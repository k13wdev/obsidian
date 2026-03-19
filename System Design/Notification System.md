## Суть и контекст

Notification System — система, предупреждающая пользователя о важной информации через push-уведомления (iOS/Android), SMS-сообщения и Email. Архитектура строится на Notification Servers, отдельных Message Queues для каждого типа уведомлений и Workers, отправляющих данные в Third-Party Services (APNs, FCM, SMS Service, Email Service).

---

## Ключевые идеи

- Три типа: push-уведомления (iOS через APNs, Android через FCM), SMS, Email
- Отдельная Message Queue на каждый тип уведомления — изоляция сбоев
- Workers извлекают события из очередей, отправляют в Third-Party Services, делают retry on error
- At-least-once доставка → необходим механизм дедупликации (dedupe)
- Notification log database — уведомления могут задерживаться, но не должны теряться
- Rate limiting — ограничение количества уведомлений для защиты пользователя от перегрузки
- Проверка opt-out настроек пользователя перед отправкой

---

## Определения и термины

**Notification System** — система, предупреждающая пользователя о важной информации: push-уведомления (iOS/Android), SMS-сообщения и Email.

**APNs (Apple Push Notification service)** — сторонний сервис, используемый для доставки push-уведомлений на iOS.

**FCM (Firebase Cloud Messaging)** — сторонний сервис, используемый для доставки push-уведомлений на Android.

**Rate Limiting** — механизм ограничения количества уведомлений, отправляемых пользователю, чтобы избежать его перегрузки (avoid overwhelming users).

---

## Как устроено

### Компоненты системы

- **Микросервисы / Cron-задачи (Service 1..N)** — источники уведомлений
- **Notification Servers** — принимают запросы, формируют события
- **Cache** — User info, Device tokens, Templates
- **DB** — хранение данных и notification log
- **Message Queues** — отдельная очередь для каждого типа (iOS PN, Android PN, SMS, Email)
- **Workers** — пул серверов, обрабатывающих события из очередей
- **Third-Party Services** — APNs, FCM, SMS Service, Email Service

### Sending Flow

1. Микросервис вызывает API Notification Server
2. Server извлекает метаданные (user info, device token, настройки) из Cache/DB
3. Проверяет opt-out настройки пользователя
4. Формируется событие уведомления, помещается в очередь, соответствующую типу (iOS PN / Android PN / SMS / Email)
5. Workers извлекают события из Message Queues
6. Workers отправляют данные в Third-Party Services (APNs, FCM)
7. При ошибке — worker возвращает сообщение обратно в очередь (retry on error)
8. При успехе — данные логируются в Notification Log
9. Third-Party Services доставляют уведомления на устройства

---

## Детали и нюансы

- **SPOF**: использование одного Notification Server — если он падает, вся система останавливается. БД и кэш не масштабируются независимо, генерация шаблонов блокирует систему в пиковые часы. Решение: вынести DB/Cache отдельно и масштабировать серверы горизонтально
- **At-least-once доставка**: распределённая природа приводит к дублированию. Exactly-once невозможно. Требуется механизм дедупликации (dedupe) на уровне бизнес-логики Workers
- **Opt-out**: в таблице БД хранятся настройки пользователя (notification settings). Сервер обязан проверить флаг перед отправкой
- **Отдельные очереди**: если FCM отвечает с задержкой или падает, это не влияет на Email/SMS — изоляция сбоев через независимые очереди

---

## Сравнения и trade-offs

| Подход | Плюсы | Минусы |
|--------|-------|--------|
| Одна общая очередь | Простота | Сбой одного канала блокирует все остальные |
| Отдельная очередь на тип | Изоляция сбоев, независимое масштабирование | Больше инфраструктуры |
| Один Notification Server | Простая архитектура | SPOF, bottleneck при пиках |
| Горизонтальное масштабирование серверов | Отказоустойчивость, производительность | Сложнее координация |

---

## Примеры

### Архитектура Notification System

```
Service 1..N → Notification Servers → Cache / DB
                                    ↓
                    ┌───────────────┼───────────────┐
                    │               │               │
               iOS PN queue   Android PN queue  SMS queue  Email queue
                    │               │               │           │
                 Workers         Workers         Workers     Workers
                    │               │               │           │
                  APNs             FCM          SMS Service  Email Service
                    │               │               │           │
                 iPhone          Android         Телефон      Почта
```

Workers при ошибке возвращают сообщение обратно в очередь (retry on error).

---

## Связи с другими темами

- [[Очереди сообщений]] — Message Queues для изоляции каналов доставки
- [[Rate Limiter]] — ограничение количества уведомлений пользователю
- [[Наблюдаемость и надёжность]] — мониторинг доставки, retry, notification log
- [[Масштабирование]] — горизонтальное масштабирование Notification Servers

---

## Важно / подводные камни / best practices

- **Отдельная Message Queue для каждого Third-Party Service** — изоляция сбоев: падение FCM не блокирует Email/SMS
- **Notification log database** — уведомления могут задерживаться, но никогда не должны теряться. Лог позволяет Workers делать retry on error
- **Дедупликация** — exactly-once невозможно, at-least-once приводит к дублям → dedupe на уровне Workers
- **Opt-out проверка** — обязательна перед каждой отправкой
- **Горизонтальное масштабирование** — вынести DB/Cache отдельно, масштабировать серверы независимо

---

## Карточки для повторения

#flashcards/system-design/notification-system

Notification System:::Система доставки push-уведомлений (iOS/Android), SMS и Email пользователю о важной информации

APNs (Apple Push Notification service):::Сторонний сервис для доставки push-уведомлений на iOS

FCM (Firebase Cloud Messaging):::Сторонний сервис для доставки push-уведомлений на Android

Sending Flow в Notification System
?
1. Микросервис → API Notification Server
2. Server → метаданные из Cache/DB + проверка opt-out
3. Событие → Message Queue (по типу: iOS/Android/SMS/Email)
4. Workers → извлекают из очереди → Third-Party Services
5. Ошибка → retry (обратно в очередь). Успех → Notification Log

Зачем отдельная очередь для каждого типа уведомлений?::Изоляция сбоев: если FCM падает или тормозит, это не влияет на отправку Email или SMS

Гарантия доставки в Notification System: ==at-least-once==. Exactly-once невозможно → нужна ==дедупликация== на уровне Workers

Почему нужен Notification Log?::Уведомления могут задерживаться, но никогда не должны теряться. Лог позволяет Workers делать retry on error

SPOF в Notification System::Один Notification Server → если падает, вся система останавливается. Решение: вынести DB/Cache отдельно, масштабировать серверы горизонтально

Что нужно проверить перед отправкой уведомления?::Opt-out настройки пользователя в БД — флаг отказа от рассылки

---

## Источники

**Книга:**
- Название: System Design Interview: An Insider's Guide
- Автор: Alex Xu
- Содержание: Chapter 10 — Design a Notification System

---

## Теги

#конспект #system-design #system-design/notification-system #system-design/push-notifications #system-design/message-queues
