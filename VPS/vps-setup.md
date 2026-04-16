# Настройка VPS

Базовая настройка сервера Ubuntu 22.04: пользователь с sudo, вход только по SSH-ключу, нестандартный порт SSH, UFW. Предполагается: у тебя есть root-доступ по паролю и IP сервера (в примерах — 195.62.49.129).

---

## Подключение и обновления

**Где:** локально, затем на сервере под root

Подключаемся на сервер (yes + пароль root):

```bash
ssh root@195.62.49.129
```

Обновляем пакеты и перезагружаемся:

```bash
apt update && apt dist-upgrade -y && reboot
```

После перезагрузки снова входим:

```bash
ssh root@195.62.49.129
```

---

## Создание пользователя и настройка sudo

**Где:** на сервере под root

Создаём пользователя **super** с sudo и bash:

```bash
useradd -m super -G sudo -s /bin/bash
```

Редактируем sudo через visudo:

```bash
visudo
```

В редакторе:

1. **Группа sudo** — дать NOPASSWD:
   ```
   # Allow members of group sudo to execute any command
   %sudo   ALL=(ALL:ALL) NOPASSWD:ALL
   ```

2. **Строку с includedir удалить:**
   ```
   #includedir /etc/sudoers.d
   ```
   Эту строку удалить целиком.

3. **Группу admin закомментировать:**
   ```
   # Members of the admin group may gain root privileges
   #%admin ALL=(ALL) ALL
   ```

Сохранить и выйти.

---

## Переход под super и подготовка .ssh

**Где:** на сервере (сначала под root, затем под super)

```bash
su super
cd
```

Маска для создаваемых файлов:

```bash
echo 'umask 0077' >> .bashrc
```

Папка для ключей:

```bash
mkdir .ssh && chmod 700 .ssh
```

---

## Настройка SSH-ключей

Нужны две консоли: одна на сервере, вторая на локальном компьютере.

**Где:** локально (генерация и копирование), затем на сервере под root/super

**Локально** — генерируем ключ (не на сервере):

```bash
ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa_vps
```

Появится пара ключей в `~/.ssh/`: `id_rsa_vps` (приватный) и `id_rsa_vps.pub` (публичный). Публичный загружаем на сервер, приватный никуда не отправляем.

**Локально** — копируем публичный ключ на сервер:

```bash
scp ~/.ssh/id_rsa_vps.pub root@195.62.49.129:/home/super/.ssh/
```

**На сервере** — владелец, права и переименование в authorized_keys:

```bash
sudo chown super:super .ssh/id_rsa_vps.pub && chmod 600 .ssh/id_rsa_vps.pub && mv .ssh/id_rsa_vps.pub .ssh/authorized_keys
```

**Локально** — проверка входа по ключу под пользователем super:

```bash
ssh -i ~/.ssh/id_rsa_vps super@195.62.49.129
```

Если вход успешен — продолжаем.

---

## Отключение пользователя root

**Где:** на сервере под root (до перезапуска sshd не отключаться)

У пользователя root меняем shell на `/usr/sbin/nologin`:

```bash
sudo vi /etc/passwd
```

Строка root должна заканчиваться так:
```
/usr/sbin/nologin
```
(вместо `/bin/bash` или `/bin/sh`). Сохранить и выйти.

---

## Настройка SSH-сервера (sshd_config)

**Где:** на сервере под super (sudo)

```bash
sudo vi /etc/ssh/sshd_config
```

Изменить или добавить:

1. Нестандартный порт:
   ```
   Port 28472
   ```

2. Запретить вход под root:
   ```
   PermitRootLogin no
   ```

3. Запретить вход по паролю:
   ```
   # To disable tunneled clear text passwords, change to no here!
   PasswordAuthentication no
   PermitEmptyPasswords no
   ```

4. Вход только по ключу:
   ```
   PubkeyAuthentication yes
   ```

5. Отключить интерактивную/парольную авторизацию:
   ```
   ChallengeResponseAuthentication no
   KbdInteractiveAuthentication no
   ```

6. Баннер (в статье указан в итоговом конфиге):
   ```
   Banner /etc/ssh/banner
   ```
   Файл баннера создать отдельно:
   ```bash
   echo "Authorized use only." | sudo tee /etc/ssh/banner
   ```

Сохранить конфиг и выйти. Перезапуск sshd:

```bash
sudo systemctl restart sshd
```

Дальше подключаться только на порт **28472**. Текущую сессию не закрывать, пока не проверишь вход.

---

## Локальный SSH config

**Где:** локально

В `~/.ssh/config` добавить (vim или nano):

```
Host vps
  HostName 195.62.49.129
  User super
  IdentitiesOnly yes
  IdentityFile ~/.ssh/id_rsa_vps
  Port 28472
```

Подключение одной командой:

```bash
ssh vps
```

Проверить вход в новом терминале, затем можно закрыть старую сессию.

---

## Дополнительные программы

**Где:** на сервере под super

```bash
sudo apt install git ufw nmap net-tools curl
```

---

## Firewall (UFW)

**Где:** на сервере под super

Порт SSH сменён на 28472 — разрешаем его:

```bash
sudo ufw allow 28472/tcp
```

Политики по умолчанию:

```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
```

Включить firewall:

```bash
sudo ufw enable
```

(Если бы порт не меняли, использовали бы `sudo ufw allow ssh`.)

---

На этом базовая настройка VPS завершена. Дальше: [docker-services-setup.md](docker-services-setup.md), затем нужный сервисный гайд (VLESS, WG-Easy, Hysteria2 и т.д.).
