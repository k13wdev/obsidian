# Настройка VPS RU — Sing-Box relay (каскад на Hysteria2 и WireGuard)

Продолжение после [vps-setup.md](vps-setup.md): сервер обновлён, пользователь **super**, SSH на нестандартном порту, UFW настроен.

Docker для этой схемы **не нужен** — Sing-Box устанавливается как системный пакет и запускается через systemd.

> Исторически здесь использовался VLESS+Reality, но из-за бага
> [SagerNet/sing-box#4023](https://github.com/SagerNet/sing-box/issues/4023)
> (Reality ломается, когда и клиент, и сервер работают на sing-box) inbound
> переведён на Hysteria2 с self-signed сертификатом.

---

## Архитектура

```
<device-1>-hy2 (pass) ──┐
<device-2>-hy2 (pass) ──┤──→ hysteria2-exit → Hysteria2 VPS
...                     ──┘
                               VPS RU [Sing-Box hy2 :443/udp]
<device-1>-wg  (pass) ──┐    ├── geosite-ru / geoip-ru → direct
<device-2>-wg  (pass) ──┤──→ wg-exit → WireGuard VPS
...                     ──┘
```

- Один inbound (UDP/443, Hysteria2), у каждого клиента свой пароль.
- Переключение exit'а — сменой профиля в Hiddify (hy2-профиль или wg-профиль).
- Российские домены и IP идут напрямую с RU-IP для всех клиентов.
- На relay отключён UDP/443 для клиентов (rule `reject udp:443`) — лекарство
  от QUIC-in-QUIC: Google-сервисы форсятся на TCP/443 и не вкладывают QUIC в
  QUIC Hysteria.

---

## Установка Sing-Box

**Где:** на сервере под super

```bash
sudo curl -fsSL https://sing-box.app/gpg.key -o /etc/apt/keyrings/sagernet.asc
sudo chmod a+r /etc/apt/keyrings/sagernet.asc
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/sagernet.asc] \
  https://deb.sagernet.org/ * *" | sudo tee /etc/apt/sources.list.d/sagernet.list
sudo apt update && sudo apt install sing-box -y
```

Проверить версию (нужна **≥ 1.11**, так как используется rule action `reject`):

```bash
sing-box version
```

---

## Генерация self-signed сертификата для Hysteria2

**Где:** на сервере под super

Hysteria2 требует настоящий TLS — Reality тут не применяется. Сертификат
выпускаем на IP сервера, на клиентах включается `insecure=1`.

```bash
sudo mkdir -p /etc/sing-box/certs
sudo openssl req -x509 -newkey rsa:4096 -sha256 -days 365 -nodes \
  -keyout /etc/sing-box/certs/server.key \
  -out /etc/sing-box/certs/server.crt \
  -subj "/CN=<VPS_RU_IP>" \
  -addext "subjectAltName = IP:<VPS_RU_IP>"
sudo chmod 600 /etc/sing-box/certs/server.key
sudo chmod 644 /etc/sing-box/certs/server.crt
sudo chown -R root:root /etc/sing-box/certs
```

Если OpenSSL ругнётся на `-addext`, выпусти сертификат без этой опции.

---

## Генерация паролей для клиентов

**Где:** на сервере под super (или локально)

По одному паролю на каждый профиль (hy2-exit и wg-exit — отдельные профили,
поэтому паролей столько же, сколько устройств × 2):

```bash
for i in $(seq 1 8); do openssl rand -base64 18 | tr -d '=+/' | cut -c1-16; done
```

Сохрани — они пойдут и в конфиг сервера (поле `password`), и в `hy2://`-ссылки
для клиентов.

---

## Получение WireGuard-пира для wg-exit

**Где:** в wg-easy панели через SSH-туннель (см. [wg-easy-setup-steps.md](wg-easy-setup-steps.md))

1. Открыть панель wg-easy (`http://127.0.0.1:51821/`).
2. Создать нового клиента с именем `vps-ru-relay`.
3. Скачать конфиг (кнопка Download).

Из скачанного `.conf` файла взять:

```ini
[Interface]
PrivateKey = <WG_CLIENT_PRIVATE_KEY>   ← private_key в Sing-Box
Address = 10.8.0.X/32, fdcc:...:X/128  ← address[] в Sing-Box endpoint (обе строки!)
MTU = 1420                             ← mtu в Sing-Box

[Peer]
PublicKey = <WG_SERVER_PUBLIC_KEY>     ← public_key в Sing-Box peers
PresharedKey = <WG_PSK>                ← pre_shared_key в Sing-Box peers (обязательно!)
Endpoint = <WG_VPS_IP>:51820           ← address + port в Sing-Box peers
```

---

## Конфиг Sing-Box

**Где:** на сервере под super, файл `/etc/sing-box/config.json`

```bash
sudo vi /etc/sing-box/config.json
```

```json
{
  "log": { "level": "info" },

  "endpoints": [
    {
      "type": "wireguard",
      "tag": "wg-exit",
      "address": ["<WG_CLIENT_IPV4>/32", "<WG_CLIENT_IPV6>/128"],
      "private_key": "<WG_CLIENT_PRIVATE_KEY>",
      "peers": [
        {
          "address": "<WG_VPS_IP>",
          "port": 51820,
          "public_key": "<WG_SERVER_PUBLIC_KEY>",
          "pre_shared_key": "<WG_PSK>",
          "allowed_ips": ["0.0.0.0/0", "::/0"],
          "persistent_keepalive_interval": 25
        }
      ],
      "mtu": 1420
    }
  ],

  "inbounds": [
    {
      "type": "hysteria2",
      "tag": "hy2-in",
      "listen": "::",
      "listen_port": 443,
      "users": [
        { "name": "<device-1>-hy2", "password": "<PASS>" },
        { "name": "<device-2>-hy2", "password": "<PASS>" },
        { "name": "<device-1>-wg",  "password": "<PASS>" },
        { "name": "<device-2>-wg",  "password": "<PASS>" }
      ],
      "ignore_client_bandwidth": false,
      "tls": {
        "enabled": true,
        "certificate_path": "/etc/sing-box/certs/server.crt",
        "key_path": "/etc/sing-box/certs/server.key"
      },
      "masquerade": "https://news.ycombinator.com/"
    }
  ],

  "outbounds": [
    {
      "tag": "hysteria2-exit",
      "type": "hysteria2",
      "server": "<HYSTERIA2_VPS_IP>",
      "server_port": 443,
      "password": "<HYSTERIA2_PASSWORD>",
      "tls": {
        "enabled": true,
        "insecure": true,
        "server_name": "bing.com"
      },
      "up_mbps": <UP>,
      "down_mbps": <DOWN>
    },
    { "tag": "direct", "type": "direct" }
  ],

  "route": {
    "rule_set": [
      {
        "tag": "geosite-ru",
        "type": "remote",
        "format": "binary",
        "url": "https://raw.githubusercontent.com/SagerNet/sing-geosite/rule-set/geosite-category-ru.srs",
        "download_detour": "direct"
      },
      {
        "tag": "geoip-ru",
        "type": "remote",
        "format": "binary",
        "url": "https://raw.githubusercontent.com/SagerNet/sing-geoip/rule-set/geoip-ru.srs",
        "download_detour": "direct"
      }
    ],
    "rules": [
      { "action": "sniff" },
      { "rule_set": ["geosite-ru"], "outbound": "direct" },
      { "rule_set": ["geoip-ru"],   "outbound": "direct" },
      { "action": "reject", "network": "udp", "port": 443 },
      {
        "auth_user": ["<device-1>-hy2", "<device-2>-hy2"],
        "outbound": "hysteria2-exit"
      },
      {
        "auth_user": ["<device-1>-wg", "<device-2>-wg"],
        "outbound": "wg-exit"
      }
    ],
    "final": "hysteria2-exit",
    "auto_detect_interface": true
  }
}
```

Заменить:
- `<device-N>` — имена устройств (например `MacBook-Kirill`, `iPhone-Mini`)
- `<PASS>` — пароли из шага выше (уникальный на каждую строку)
- `<HYSTERIA2_VPS_IP>`, `<HYSTERIA2_PASSWORD>` — из конфига Hysteria2 VPS
- `<WG_VPS_IP>`, `<WG_CLIENT_PRIVATE_KEY>`, `<WG_SERVER_PUBLIC_KEY>`, `<WG_PSK>`, `<WG_CLIENT_IPV4>`, `<WG_CLIENT_IPV6>` — из .conf файла клиента wg-easy (см. шаг выше)
- `<UP>` / `<DOWN>` — ~80% пропускной способности VPS RU (в Mbit/s)

> `server_name: "bing.com"` — SNI для TLS-рукопожатия с Hysteria2 exit.
> Сертификат там self-signed, поэтому `insecure: true`.

---

## Geo-базы

Начиная с версии 1.12, Sing-Box скачивает rule-set файлы (`*.srs`) **автоматически при старте** через `download_detour: direct`. Вручную ничего скачивать не нужно.

---

## Проверка конфига и запуск

**Где:** на сервере под super

```bash
# Проверить синтаксис конфига
sudo sing-box check -c /etc/sing-box/config.json

# Включить автозапуск и стартовать
sudo systemctl enable sing-box --now

# Статус
sudo systemctl status sing-box
```

Логи в реальном времени:

```bash
sudo journalctl -u sing-box -f
```

---

## Firewall (UFW)

**Где:** на сервере под super

Hysteria2 работает по UDP:

```bash
sudo ufw allow 443/udp
sudo ufw status
```

Если ранее был открыт `443/tcp` под VLESS — его можно закрыть:

```bash
sudo ufw delete allow 443/tcp
```

Порт SSH уже открыт из [vps-setup.md](vps-setup.md).

---

## Настройка Hiddify (клиент)

**Где:** локально, на каждом устройстве

В Hiddify добавить два профиля вручную (Add Profile → Manual → Hysteria2).
Все параметры одинаковые, кроме пароля и названия.

Общие параметры:

| Поле | Значение |
|------|----------|
| Address | IP VPS RU |
| Port | 443 |
| Protocol | Hysteria2 |
| SNI | любой валидный домен (например `bing.com`) |
| Allow insecure | **обязательно ON** (сертификат self-signed на IP) |
| Up / Down Mbps | ~80% реальной полосы клиента |
| Block QUIC | ON (страховка — ACL на relay уже режет UDP/443) |

Пароль — уникальный для каждого профиля из конфига сервера. Профиль с
`-hy2` паролем направляет трафик через Hysteria2, профиль с `-wg` паролем —
через WireGuard.

Переключение exit'а = смена активного профиля в Hiddify.

Формат ссылки:

```
hy2://<PASSWORD>@<VPS_RU_IP>:443/?insecure=1&sni=bing.com&upmbps=<UP>&downmbps=<DOWN>#<NAME>
```

---

## Обновление rule-set

Rule-set файлы кешируются локально. Для обновления достаточно перезапуска:

```bash
sudo systemctl restart sing-box
```

Автоперезапуск по воскресеньям в 3:00 (опционально):

```bash
sudo crontab -e
# добавить:
0 3 * * 0 systemctl restart sing-box
```

---

## Обновление Sing-Box

**Где:** на сервере под super

```bash
sudo apt update && sudo apt upgrade sing-box -y
sudo systemctl restart sing-box
```

---

## Проверка

1. Конфиг без ошибок: `sudo sing-box check -c /etc/sing-box/config.json`
2. Сервис запущен: `sudo systemctl status sing-box`
3. Активировать hy2-профиль в Hiddify → открыть `https://ipinfo.io/` → IP = Hysteria2 VPS ✓
4. Активировать wg-профиль в Hiddify → открыть `https://ipinfo.io/` → IP = WireGuard VPS ✓
5. Открыть `yandex.ru` через любой профиль → IP = VPS RU (direct) ✓
6. Логи без ошибок: `sudo journalctl -u sing-box -f`

---

## Альтернативный конфиг: VLESS+Reality (для регресс-тестов)

Выходы (hy2-exit / wg-exit / direct) те же; отличается только inbound:
VLESS+Reality на TCP/443 вместо Hysteria2 на UDP/443.

> Reality в sing-box ломается по багу
> [SagerNet/sing-box#4023](https://github.com/SagerNet/sing-box/issues/4023)
> (handshake валится в "forwarded SNI", когда и клиент, и сервер на sing-box).


