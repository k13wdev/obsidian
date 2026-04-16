# Настройка Hysteria2

Продолжение после [vps-setup.md](vps-setup.md) и [docker-services-setup.md](docker-services-setup.md): сервер обновлён, пользователь **super**, SSH на порту **28472**, подключение через `ssh vps`, Docker и базовая структура `~/docker` уже подготовлены.

Основано на официальной документации:
- Installation: https://v2.hysteria.network/docs/getting-started/Installation/
- Server: https://v2.hysteria.network/docs/getting-started/Server/
- Full Server Config: https://v2.hysteria.network/docs/advanced/Full-Server-Config/
- ACL: https://v2.hysteria.network/docs/advanced/ACL/

---

## Каталог сервиса

**Где:** на сервере под super

```bash
mkdir -p ~/docker/hysteria2
cd ~/docker/hysteria2
```

---

## docker-compose.yml (с монтированием сертификатов)

**Где:** на сервере под super, файл `~/docker/hysteria2/docker-compose.yml`

```yaml
services:
  hysteria:
    image: tobyxdd/hysteria:v2         
    container_name: hysteria2
    restart: always
    network_mode: "host"
    volumes:
      - ./hysteria.yaml:/etc/hysteria.yaml
      - ./certs:/etc/hysteria/certs:ro
    command: ["server", "-c", "/etc/hysteria.yaml"]
```

> Ключ `version:` в современных Docker Compose устарел и игнорируется — поэтому убран.

---

## Выпуск собственного сертификата (self-signed)

**Где:** на сервере под super, каталог `~/docker/hysteria2`

```bash
mkdir -p certs
openssl req -x509 -newkey rsa:4096 -sha256 -days 365 -nodes \
  -keyout certs/server.key \
  -out certs/server.crt \
  -subj "/CN=195.62.49.129" \
  -addext "subjectAltName = IP:195.62.49.129"
chmod 600 certs/server.key
chmod 644 certs/server.crt
```

Если OpenSSL ругнётся на `-addext`, выпусти сертификат без этой опции.

---

## Конфиг сервера hysteria.yaml (с собственным сертификатом)

**Где:** на сервере под super, файл `~/docker/hysteria2/hysteria.yaml`

```yaml
# По умолчанию Hysteria слушает 443 порт
listen: :443

tls:
  cert: /etc/hysteria/certs/server.crt
  key: /etc/hysteria/certs/server.key

auth:
  type: password
  password: CHANGE_THIS_STRONG_PASSWORD

# ACL — главное лекарство от QUIC-in-QUIC.
# Без этой строки YouTube/NotebookLM/Drive и другие Google-сервисы
# идут через QUIC поверх UDP/443, который заворачивается в QUIC Hysteria.
# Получается двойной congestion control и проблемы с PMTU → видео виснет.
# reject(all, udp/443) форсит откат на TCP/443 — лечится мгновенно.
acl:
  inline:
    - reject(all, udp/443)

# Sniff нужен, чтобы ACL мог матчить по доменам и протоколам, а не только IP.
sniff:
  enable: true
  timeout: 2s
  rewriteDomain: false
  tcpPorts: 80,443,8000-9000
  udpPorts: all

# Bandwidth включает алгоритм Brutal вместо BBR (стабильнее на стримах).
# Значения = ~80% от speedtest на VPS (875 ↓ / 890 ↑ Mbps на гигабитном канале).
bandwidth:
  up: 700 mbps
  down: 700 mbps

# Явно фиксируем QUIC-параметры (это значения по умолчанию из доки).
quic:
  initStreamReceiveWindow: 8388608
  maxStreamReceiveWindow: 8388608
  initConnReceiveWindow: 20971520
  maxConnReceiveWindow: 20971520
  maxIdleTimeout: 30s
  maxIncomingStreams: 1024
  disablePathMTUDiscovery: false

udpIdleTimeout: 60s

masquerade:
  type: proxy
  proxy:
    url: https://news.ycombinator.com/
    rewriteHost: true
```

Заменить:
- `CHANGE_THIS_STRONG_PASSWORD` на сильный пароль.
- `700 mbps` в `bandwidth` — на ~80% реальной пропускной способности твоего VPS (проверить через `speedtest` на сервере).

---

## Запуск контейнера

**Где:** на сервере под super

```bash
cd ~/docker/hysteria2
docker compose up -d
docker compose ps
```

Логи:

```bash
docker compose logs -f hysteria
```

---

## Firewall (UFW)

**Где:** на сервере под super

```bash
sudo ufw allow 443/udp
sudo ufw status
```

Если на сервере уже используется VLESS на `443/tcp`, конфликта нет: `TCP` и `UDP` — разные протоколы.

---

## Обновление Hysteria2 (Docker)

**Где:** на сервере под super

```bash
cd ~/docker/hysteria2
docker compose pull
docker compose up -d
```

---

## Проверка

1. Проверить, что контейнер в статусе `Up`: `docker compose ps`.
2. Проверить логи: `docker compose logs -f hysteria` (без ошибок запуска TLS).
3. На клиенте доверить `server.crt` (или включить режим клиента без проверки сертификата, если он поддерживается).
4. Импортировать `hy2://` ссылку или клиентский конфиг в поддерживаемый клиент и подключиться.

---

## Настройки клиента (Hiddify и аналоги)

1. **Insecure / Allow insecure** — обязательно включить, так как сертификат self-signed на IP.
2. **SNI** — любой валидный домен (например `bing.com`); при `insecure=1` совпадение с CN не проверяется.
3. **Bandwidth** — обязательно указать `up` / `down` (или `upmbps` / `downmbps` в hy2://-ссылке) равными ~80% реальной полосы клиента. Без этого Brutal на сервере не активируется и стримы будут лагать.
4. **Block QUIC** — включить как страховку. ACL на сервере уже режет UDP/443, эта опция дублирует защиту на клиенте.

Пример hy2://-ссылки:

```
hy2://<PASSWORD>@<SERVER_IP>:443/?insecure=1&sni=bing.com&upmbps=<UP * 0.8>&downmbps=<DOWN * 0.8>#<NAME>                                   
```

---

Источник: [Hysteria2 Official Docs](https://v2.hysteria.network/docs/getting-started/Installation/)
