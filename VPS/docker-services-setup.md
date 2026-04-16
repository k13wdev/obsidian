# Docker и каталоги сервисов

Общий пререквизит для гайдов:
- [vless-setup-steps.md](vless-setup-steps.md)
- [hysteria2-setup-steps.md](hysteria2-setup-steps.md)
- [wg-easy-setup-steps.md](wg-easy-setup-steps.md)

Продолжение после [vps-setup.md](vps-setup.md): сервер обновлён, пользователь **super**, SSH на порту **28472**, подключение через `ssh vps`.

---

## Установка Docker и Docker Compose

**Где:** на сервере под super

```bash
sudo apt install docker.io docker-compose -y
sudo usermod -aG docker super
```

Выйти и зайти снова (`exit`, затем `ssh vps`), чтобы группа docker применилась.

Проверка:

```bash
docker --version
docker-compose --version
docker ps
```

---

## Базовая директория Docker-сервисов

Все контейнеры и compose-файлы храним в одном каталоге **~/docker**.

**Где:** на сервере под super

```bash
mkdir -p ~/docker
```

---

## Каталоги сервисов

Создай подкаталоги под каждый сервис, который планируешь запускать:

```bash
mkdir -p ~/docker/3x-ui
mkdir -p ~/docker/hysteria2
mkdir -p ~/docker/wg-easy
```

Рекомендуемая структура:

```text
~/docker/
  3x-ui/       # панель VLESS (3X-UI)
  hysteria2/   # Hysteria2
  wg-easy/     # WireGuard + веб-интерфейс
```

Если сервис не нужен, его подкаталог можно не создавать.
