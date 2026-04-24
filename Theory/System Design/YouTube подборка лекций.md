# YouTube — подборка лекций по конспектам System Design

## Главный вывод

System Design — это комбинация **теории distributed systems** (лучше покрыта англоязычными университетскими курсами и конференциями), **прикладного интервью-формата** (сильнее в специализированных каналах типа ByteByteGo / Gaurav Sen / Jordan Has No Life) и **архитектурных практик** (DDD, CQRS, Event Sourcing — сильнее всего в DDD Europe / Virtual DDD / talks от Greg Young, Vaughn Vernon).

- **RU** сильнее: интервью-формат на русский рынок (Karpov, Balun, OTUS), HighLoad-доклады, отдельные эпизоды DDD на Подлодке / Мы обречены
- **EN** сильнее: глубина теории, case studies, первоисточники DDD (Eric Evans, Vaughn Vernon, Greg Young)

---

## Рекомендованные YouTube-каналы (meta-список)

### Русскоязычные

| Канал / школа | Профиль | Кластеры | Авторы |
|---------------|---------|----------|--------|
| Karpov.Courses | Курс по System Design, case studies | 1, 8 | Анатолий Карпов и приглашённые |
| Balun.Courses | System Design интервью (Ex-Yandex/Ozon) | 1, 8 | Владимир Балун |
| OTUS открытые уроки | Вебинары по System Design и DDD | 1–11 | приглашённые спикеры |
| HighLoad++ / Онтико | Доклады по архитектуре высоких нагрузок | 2–7 | сообщество |
| Yandex Scale | Архитектура крупных продуктов | 2–7 | инженеры Яндекса |
| Подлодка (подкаст) | Эпизоды по архитектуре, DDD, микросервисам | 2, 9, 10, 11 | Стас Цыганов и гости |
| Мы обречены | Архитектурный подкаст | 2, 9, 10, 11 | Миша Правилов, Денис Цыплаков |
| Systems.Education | Вебинары по DDD, EDA, брокерам | 5, 9, 10 | Влад Хононов и др. |
| Александр Бындю (блог+доклады) | DDD, практика внедрения | 9, 10, 11 | Александр Бындю |

### Англоязычные

| Канал | Профиль | Кластеры | Авторы |
|-------|---------|----------|--------|
| ByteByteGo | System design, интервью, visual explanations | 1–8 | Alex Xu |
| Hussein Nasser | Backend engineering, БД, сети, кэш | 3, 4, 6 | Hussein Nasser |
| Gaurav Sen / InterviewReady | System design interview playlist | 1, 8 | Gaurav Sen |
| Jordan Has No Life | Deep dive система дизайн + distributed | 1, 4, 5, 6, 8 | Jordan Epstein |
| Tech Dummies Narendra L | Case studies, разбор архитектуры | 8 | Narendra L |
| SystemDesignFightClub | System design интервью-формат | 1, 8 | сообщество |
| Hello Interview | System design interview prep | 1, 8 | — |
| MIT 6.5840 (6.824) | Distributed Systems | 4, 5, 6 | Robert Morris |
| Martin Kleppmann | Cambridge Concurrent & Distributed Systems | 4, 5, 6 | Martin Kleppmann |
| NDC Conferences | Microservices, EDA, distributed talks | 2, 5, 10, 11 | — |
| DDD Europe | Флагманская конференция по DDD | 9, 10, 11 | Eric Evans, Vaughn Vernon и др. |
| Virtual DDD | Community meetups, интервью с авторами | 9, 10, 11 | Kenny Baas-Schwegler и др. |
| Explore DDD | Конференция, talks | 9, 10, 11 | — |
| CodeOpinion | CQRS, Event Sourcing, DDD практика | 10, 11 | Derek Comartin |
| Event Driven Architecture (сообщество) | EDA talks | 5, 11 | — |

---

## Кластер 1 — Подход к System Design интервью

**Темы из конспектов:**
- [[01. Подход к System Design интервью]]

### RU
**Основные источники:**
- **Karpov.Courses** — курс «System Design / проектирование систем» с разбором классических задач (photo app, такси, URL shortener и др.)
- **Balun.Courses** (Владимир Балун) — бесплатные уроки по SD-интервью
- **OTUS** — открытые уроки по System Design
- Книга **Алекса Сюя** «System Design. Подготовка к сложному интервью» (Т.1 и Т.2) — с видео-разборами в виде отдельных докладов

**Поисковые запросы:**
- `System Design интервью разбор задачи`
- `Karpov Courses System Design`
- `Владимир Балун System Design`
- `OTUS System Design открытый урок`
- `System Design подход требования оценки`
- `back-of-the-envelope расчёты System Design`

### EN
**Основные источники:**
- **ByteByteGo** (Alex Xu) — автор самой популярной книги по SD, visual-формат
- **Gaurav Sen / InterviewReady** — классические интервью-кейсы
- **Jordan Has No Life** — глубокие разборы distributed-систем в контексте интервью
- **SystemDesignFightClub**, **Hello Interview** — тренировочные интервью

**Поисковые запросы:**
- `ByteByteGo system design interview`
- `Alex Xu system design volume 2`
- `Gaurav Sen system design playlist`
- `Jordan Has No Life system design`
- `Hello Interview system design mock`
- `system design framework requirements capacity estimation`
- `back-of-the-envelope calculation interview`

---

## Кластер 2 — Архитектурные стили

**Темы из конспектов:**
- [[02. Архитектурные стили]]

### RU
**Основные источники:**
- **HighLoad++** (канал Онтико) — разборы monolith → microservices, service mesh
- **Подлодка** и **Мы обречены** — подкаст-эпизоды по выбору архитектуры
- **OTUS** — открытые уроки по микросервисам и EDA

**Поисковые запросы:**
- `монолит vs микросервисы доклад`
- `service mesh Istio разбор лекция`
- `serverless архитектура лекция`
- `event-driven архитектура доклад HighLoad`
- `Подлодка микросервисы архитектура`
- `Мы обречены монолит микросервисы`
- `BFF backend for frontend разбор`
- `API Gateway паттерн лекция`

### EN
**Основные источники:**
- **NDC Conferences** — архитектурные доклады по microservices, EDA, serverless
- **ByteByteGo** — краткие объяснения стилей с визуализацией
- **GOTO Conferences** — классические доклады Sam Newman (микросервисы)
- **InfoQ** — архитектурные доклады

**Поисковые запросы:**
- `monolith vs microservices Sam Newman`
- `NDC microservices architecture talk`
- `service-oriented architecture vs microservices`
- `serverless architecture patterns talk`
- `event-driven architecture patterns talk`
- `ByteByteGo architectural styles`
- `API Gateway BFF pattern explained`
- `Sam Newman building microservices talk`

---

## Кластер 3 — Модели данных, хранилища, кэширование

**Темы из конспектов:**
- [[03. Модели данных и хранилища]]
- [[04. Кэширование]]

### RU
**Основные источники:**
- **Postgres Professional** — реляционные хранилища
- **HighLoad++** — доклады по выбору хранилища (RDBMS vs NoSQL vs Search)
- Отдельные вебинары **OTUS**, **Слёрм**, **Yandex Scale**

**Поисковые запросы:**
- `выбор хранилища SQL NoSQL доклад`
- `колоночные базы данных ClickHouse лекция`
- `document store MongoDB разбор`
- `search engine Elasticsearch лекция`
- `кэширование стратегии cache-aside write-through лекция`
- `Redis как кэш паттерны`
- `CDN cache TTL eviction лекция`

### EN
**Основные источники:**
- **CMU 15-445 Intro to Database Systems** (Andy Pavlo) — фундамент по моделям данных
- **Hussein Nasser** — backend engineering, типы хранилищ, кэш
- **ByteByteGo** — популярные объяснения cache patterns (cache-aside, write-through, write-behind)
- **Redis University** — углубление по кэш-паттернам

**Поисковые запросы:**
- `CMU 15-445 data models storage`
- `Hussein Nasser database types`
- `Hussein Nasser caching`
- `ByteByteGo caching strategies`
- `cache-aside write-through write-behind explained`
- `CDN caching explained`
- `LRU LFU eviction policies explained`
- `columnar storage vs row storage talk`

---

## Кластер 4 — Масштабирование, репликация, партиционирование

**Темы из конспектов:**
- [[05. Масштабирование]]
- [[06. Репликация и партиционирование]]

### RU
**Основные источники:**
- **HighLoad++** — разборы шардирования, репликации (master-slave, multi-master, Raft)
- **Yandex Scale** — опыт шардирования больших систем
- Курсы **Слёрм** и **OTUS** по высоким нагрузкам

**Поисковые запросы:**
- `вертикальное горизонтальное масштабирование лекция`
- `шардирование базы данных стратегии лекция`
- `репликация master-slave multi-master лекция`
- `leader election Raft Paxos разбор`
- `consistent hashing разбор лекция`
- `partitioning стратегии лекция`
- `HighLoad шардинг репликация`

### EN
**Основные источники:**
- **MIT 6.824/6.5840** — Raft, replication, consistency (must-watch)
- **Martin Kleppmann Cambridge** — replication & quorum protocols
- **ByteByteGo** — visual-разборы sharding/replication
- **Jordan Has No Life** — deep dives в distributed-темы
- **Hussein Nasser** — партиционирование и репликация

**Поисковые запросы:**
- `MIT 6.824 replication Raft`
- `Martin Kleppmann replication quorum`
- `ByteByteGo database sharding`
- `ByteByteGo replication`
- `consistent hashing explained`
- `Jordan Has No Life sharding`
- `Hussein Nasser database partitioning`
- `leader follower replication explained`
- `horizontal scaling explained`

---

## Кластер 5 — Очереди сообщений

**Темы из конспектов:**
- [[07. Очереди сообщений]]

> Пересекается с кластером «Брокеры сообщений» в [[../Backend/YouTube подборка лекций|YouTube подборка лекций (Backend)]]. Здесь — фокус на **дизайн-ракурс**: когда выбирать очередь, её место в архитектуре.

### RU
**Основные источники:**
- **Systems.Education** воркшоп по Kafka + RabbitMQ
- **HighLoad++** — доклады о брокерах в архитектурах
- **Слёрм** — курсы по Kafka

**Поисковые запросы:**
- `выбор брокера сообщений Kafka RabbitMQ доклад`
- `очередь сообщений в микросервисах лекция`
- `pub-sub vs point-to-point разбор`
- `DLQ retry идемпотентность лекция`
- `event streaming vs messaging доклад`
- `backpressure в очередях лекция`

### EN
**Основные источники:**
- **Confluent Developer** (Tim Berglund Kafka 101)
- **NDC** — event-driven микросервисы, exactly-once
- **ByteByteGo** — Kafka vs RabbitMQ vs SQS сравнения

**Поисковые запросы:**
- `Confluent Kafka 101 Tim Berglund`
- `NDC event-driven microservices`
- `ByteByteGo Kafka vs RabbitMQ`
- `message queue vs event bus explained`
- `exactly once semantics Kafka explained`
- `RabbitMQ in depth talk`
- `outbox inbox pattern talk`

---

## Кластер 6 — Транзакции и консистентность

**Темы из конспектов:**
- [[08. Транзакции и консистентность]]

### RU
**Основные источники:**
- Серия «MVCC в PostgreSQL» Егора Рогова (для локальных транзакций)
- **HighLoad++** — доклады по распределённым транзакциям, saga/2PC
- **Подлодка** и **Мы обречены** — эпизоды по согласованности

**Поисковые запросы:**
- `CAP теорема PACELC разбор лекция`
- `уровни изоляции аномалии транзакций лекция`
- `SAGA распределённые транзакции лекция`
- `2PC двухфазный коммит разбор`
- `eventual consistency strong consistency лекция`
- `Подлодка консистентность CAP`
- `linearizability serializability разбор`

### EN
**Основные источники:**
- **Jepsen (Kyle Kingsbury)** — формальная проверка гарантий (must-watch)
- **Martin Kleppmann** — linearizability, consistency models
- **MIT 6.824** — consensus, ZooKeeper
- **Hussein Nasser** — ACID, isolation levels
- **ByteByteGo** — CAP, PACELC в визуальном формате

**Поисковые запросы:**
- `Jepsen consistency talk Kyle Kingsbury`
- `Martin Kleppmann linearizability`
- `CAP theorem explained ByteByteGo`
- `PACELC theorem explained`
- `saga pattern Microservices.io`
- `two-phase commit 2PC explained`
- `Hussein Nasser isolation levels`
- `eventual consistency explained`
- `distributed transactions talk`

---

## Кластер 7 — Наблюдаемость и надёжность

**Темы из конспектов:**
- [[10. Наблюдаемость и надёжность]]

### RU
**Основные источники:**
- **HighLoad++** — доклады по observability, SRE, incident handling
- **Слёрм** — курс по SRE
- **Yandex Scale** — доклады о production-опыте

**Поисковые запросы:**
- `observability три столпа метрики логи трейсы лекция`
- `SLO SLI SLA error budget лекция`
- `circuit breaker bulkhead retry timeout лекция`
- `chaos engineering лекция`
- `incident management postmortem лекция`
- `Prometheus Grafana разбор`
- `OpenTelemetry трейсинг лекция`

### EN
**Основные источники:**
- **CNCF / KubeCon** — доклады по OpenTelemetry, Prometheus
- **Google SRE** book talks / SREcon talks
- **Hussein Nasser** — надёжность и networking
- **ByteByteGo** — visual разборы SLA/SLO, fault tolerance patterns

**Поисковые запросы:**
- `Google SRE book talk`
- `SREcon observability talk`
- `OpenTelemetry tracing tutorial`
- `Prometheus metrics tutorial`
- `circuit breaker pattern explained`
- `chaos engineering Netflix talk`
- `SLO SLI error budget explained`
- `ByteByteGo reliability patterns`

---

## Кластер 8 — Case studies (KV store, URL shortener, Rate limiter, Notifications, Chat)

**Темы из конспектов:**
- [[09. Key-Value Store]]
- [[11. URL Shortener]]
- [[12. Rate Limiter]]
- [[13. Notification System]]
- [[14. Chat System]]

### RU
**Основные источники:**
- **Karpov.Courses** — case studies внутри курса SD
- **Balun.Courses** — разборы кейсов
- **HighLoad++** — доклады по реальным кейсам (chat at scale, notifications, rate limiting)
- **OTUS** открытые уроки — отдельные разборы задач

**Поисковые запросы:**
- `URL shortener system design русский`
- `Rate Limiter алгоритмы token bucket leaky bucket лекция`
- `Chat system design WhatsApp разбор`
- `Notification system push email SMS дизайн лекция`
- `Key-Value store design Dynamo разбор`
- `Karpov Courses case study System Design`
- `Balun case study SD`

### EN
**Основные источники:**
- **ByteByteGo** (Alex Xu) — визуальные разборы большинства классических кейсов (Chat, URL, Rate Limiter, KV, Notification System)
- **Gaurav Sen** — видео-серия case studies
- **Jordan Has No Life** — углублённые разборы с упором на distributed-аспекты
- **Tech Dummies Narendra L** — разборы с кодом и диаграммами
- **SystemDesignFightClub**, **Hello Interview** — mock interviews

**Поисковые запросы:**
- `ByteByteGo URL shortener design`
- `ByteByteGo rate limiter design`
- `ByteByteGo chat system design`
- `ByteByteGo notification system design`
- `Gaurav Sen URL shortener`
- `Gaurav Sen distributed key value store`
- `Jordan Has No Life rate limiter`
- `Tech Dummies Narendra chat system`
- `DynamoDB paper explained`
- `Cassandra design explained`
- `WhatsApp system design explained`
- `Twitter system design interview`

---

## Кластер 9 — DDD: стратегический дизайн

**Темы из конспектов (папка [[DDD]]):**
- [[01. Введение в DDD]]
- [[02. Ubiquitous Language и Bounded Context]]
- [[03. Стратегический дизайн (Context Map, Core Domain)]]

### RU
**Основные источники:**
- **Systems.Education** — вебинар с **Владом Хононовым** (автор «Learning Domain-Driven Design»)
- **Александр Бындю** — блог и доклады по практике DDD
- **Подлодка** и **Мы обречены** — эпизоды по DDD
- **HighLoad++ / TeamLead Conf** — доклады по стратегическому дизайну

**Поисковые запросы:**
- `Влад Хононов DDD вебинар`
- `Domain-Driven Design простыми словами лекция`
- `Ubiquitous Language единый язык лекция`
- `Bounded Context разбор лекция`
- `стратегический дизайн Context Map лекция`
- `Core Domain Supporting Generic лекция`
- `Александр Бындю DDD`
- `Подлодка DDD эпизод`

### EN
**Основные источники:**
- **DDD Europe** — conference talks (самый авторитетный источник)
- **Eric Evans** — выступления «DDD at 10/20 years», «What I've Learned About DDD»
- **Vaughn Vernon** — «Domain-Driven Design Distilled», talks по strategic design
- **Virtual DDD** — community meetups, интервью с авторами
- **Explore DDD** — конференция

**Поисковые запросы:**
- `Eric Evans DDD talk`
- `Eric Evans DDD at 10 years`
- `Vaughn Vernon DDD Distilled`
- `DDD Europe Bounded Context`
- `DDD Europe strategic design`
- `Virtual DDD context map`
- `Ubiquitous Language explained`
- `Core Domain Generic Subdomain talk`
- `Context Mapping Alberto Brandolini`

---

## Кластер 10 — DDD: тактический дизайн

**Темы из конспектов:**
- [[04. Entities и Value Objects]]
- [[05. Aggregates]]
- [[06. Repositories, Factories, Services]]
- [[07. Domain Events]]
- [[08. Application Layer]]
- [[09. Модули и слои (Modules, Layered Architecture)]]

### RU
**Основные источники:**
- Доклады **Александра Бындю**, **Николая Горлова** и других практиков DDD в рунете
- **OTUS** открытые уроки по тактическому DDD
- **Systems.Education** вебинары

**Поисковые запросы:**
- `Entity Value Object разница DDD лекция`
- `Aggregate DDD правила проектирования лекция`
- `Aggregate Root инварианты разбор`
- `Repository Factory Domain Service DDD`
- `Domain Events разбор лекция`
- `Application Service vs Domain Service разбор`
- `layered architecture DDD разбор`
- `Александр Бындю агрегаты`

### EN
**Основные источники:**
- **Vaughn Vernon** — talks по Aggregate Design (серия «Effective Aggregate Design» — обязательно)
- **DDD Europe** — множество talks по тактическому дизайну
- **CodeOpinion (Derek Comartin)** — практические разборы Entity/VO/Aggregate
- **Jimmy Bogard** — doклады по MediatR, application layer, DDD
- **Udi Dahan** — talks по сервисам и boundaries

**Поисковые запросы:**
- `Vaughn Vernon Effective Aggregate Design`
- `DDD Europe aggregate design`
- `Entity vs Value Object explained`
- `Aggregate Root invariants talk`
- `Repository pattern DDD explained`
- `Domain Service vs Application Service`
- `Domain Events explained`
- `CodeOpinion DDD aggregate`
- `Jimmy Bogard DDD application layer`
- `Udi Dahan service boundaries`

---

## Кластер 11 — DDD: архитектурные паттерны и антипаттерны

**Темы из конспектов:**
- [[10. Архитектура (Hexagonal, CQRS, Event Sourcing)]]
- [[11. Антипаттерны DDD]]

### RU
**Основные источники:**
- Доклады **Александра Бындю**, **Николая Горлова** о Hexagonal / Clean Architecture
- **OTUS** открытые уроки по CQRS и Event Sourcing
- **Подлодка** и **Мы обречены** — эпизоды по CQRS/ES/антипаттернам DDD

**Поисковые запросы:**
- `Hexagonal Architecture гексагональная лекция`
- `Clean Architecture Onion разбор`
- `Ports and Adapters лекция`
- `CQRS разбор паттерн лекция`
- `Event Sourcing разбор лекция`
- `антипаттерны DDD anemic domain model лекция`
- `Подлодка CQRS Event Sourcing`

### EN
**Основные источники:**
- **Greg Young** — первоисточник CQRS + Event Sourcing:
  - «CQRS and Event Sourcing» (Code on the Beach 2014) — must-watch
  - «A Decade of DDD, CQRS, Event Sourcing»
- **Vaughn Vernon** — talks по Hexagonal + event-driven
- **Alistair Cockburn** — автор Hexagonal Architecture, есть talks
- **Robert C. Martin (Uncle Bob)** — Clean Architecture talks
- **CodeOpinion (Derek Comartin)** — CQRS/ES в .NET-контексте
- **Udi Dahan** — анти-паттерны DDD, service boundaries

**Поисковые запросы:**
- `Greg Young CQRS Event Sourcing talk`
- `Greg Young decade of DDD CQRS Event Sourcing`
- `Vaughn Vernon hexagonal architecture`
- `Alistair Cockburn hexagonal architecture talk`
- `Uncle Bob Clean Architecture talk`
- `CQRS explained CodeOpinion`
- `Event Sourcing explained`
- `Udi Dahan anti-patterns DDD`
- `anemic domain model antipattern`
- `big ball of mud talk`
- `Alberto Brandolini EventStorming`

---

## Стратегия просмотра

### Сбалансированный трек (RU + EN)

1. **Fundamentals теории:**
   - **MIT 6.5840** или **Martin Kleppmann Cambridge** (EN) — distributed systems базис
   - **CMU 15-445** (EN) — хранилища, транзакции
2. **Интервью-формат:**
   - **ByteByteGo** (EN) + **Karpov.Courses** / **Balun.Courses** (RU) — для подготовки к SD-интервью
3. **Case studies:**
   - **Gaurav Sen** + **Jordan Has No Life** + **ByteByteGo** (EN)
   - Разборы **Karpov.Courses** (RU)
4. **Архитектурные паттерны:**
   - **NDC**, **GOTO**, **InfoQ** (EN) — для microservices/EDA/serverless
5. **DDD:**
   - **DDD Europe** + **Virtual DDD** + серии **Greg Young** / **Vaughn Vernon** (EN)
   - Эпизоды **Подлодки** / **Мы обречены** + **Александр Бындю** + вебинары **Systems.Education** (RU)

### Быстрый путь к SD-интервью
ByteByteGo (книги 1+2) → Gaurav Sen playlist → Jordan Has No Life → Karpov/Balun разборы → собственная практика.

### Быстрый путь к DDD
Eric Evans «DDD at 10 years» → Vaughn Vernon «DDD Distilled» → Greg Young «CQRS and ES» (Code on the Beach 2014) → Vlad Khononov вебинар (RU) → Александр Бындю.

---

## Оговорка

Прямые ссылки на конкретные плейлисты/видео намеренно не указаны — URL может быть удалён, переименован или перенесён. Поиск по названию канала + теме на самом YouTube надёжнее сохранённой ссылки.

---

## Источники

### Русскоязычные платформы и курсы
- Karpov.Courses System Design — [karpov.courses/systemdesign](https://karpov.courses/systemdesign)
- Balun.Courses System Design — [balun.courses/courses/system_design](https://balun.courses/courses/system_design/interview_tutorial)
- OTUS System Design — [otus.ru/lessons/system-design](https://otus.ru/lessons/system-design/)
- GitHub system-design-roadmap (RU) — [github.com/vladimir-maslov/system-design-roadmap](https://github.com/vladimir-maslov/system-design-roadmap/blob/main/ru/README.md)
- GitHub learn-system-design — [github.com/beagreatengineer/learn-system-design](https://github.com/beagreatengineer/learn-system-design)
- HighLoad++ — [highload.ru](https://highload.ru)
- Systems.Education вебинар с Владом Хононовым (DDD) — [systems.education/interview-domain-driven-design](https://systems.education/interview-domain-driven-design)

### Англоязычные курсы и каналы
- ByteByteGo — [bytebytego.com](https://bytebytego.com)
- InterviewReady (Gaurav Sen) — [interviewready.io](https://interviewready.io/)
- MIT 6.5840 Distributed Systems — [pdos.csail.mit.edu/6.824](https://pdos.csail.mit.edu/6.824/)
- Martin Kleppmann Distributed Systems (Cambridge) — [martin.kleppmann.com](https://martin.kleppmann.com/2020/11/18/distributed-systems-and-elliptic-curves.html)
- CMU 15-445 / 15-721 — [15721.courses.cs.cmu.edu](https://15721.courses.cs.cmu.edu/spring2024/)
- DDD Europe — [dddeurope.com](https://dddeurope.com/)
- Virtual DDD — [virtualddd.com](https://virtualddd.com/)
- NDC Conferences — [ndcconferences.com](https://ndcconferences.com/)

### Ключевые материалы
- Greg Young «A Decade of DDD, CQRS, Event Sourcing» — [virtualddd.com/videos/greg-young](https://virtualddd.com/videos/greg-young-a-decade-of-ddd-cqrs-event-sourcing/)
- Greg Young «CQRS and Event Sourcing» (Code on the Beach 2014) — [kurrent.io](https://www.kurrent.io/blog/transcript-of-greg-youngs-talk-at-code-on-the-beach-2014-cqrs-and-event-sourcing)
- Алекс Сюй «System Design. Подготовка к сложному интервью» — [piter.com](https://www.piter.com/product/system-design-podgotovka-k-slozhnomu-intervyu)
- Ubiquitous Language и Bounded Context (Хабр) — [habr.com/ru/post/232881](https://habr.com/ru/post/232881)
- Jordan Has No Life (about) — [linkedin.com/hello-interview](https://www.linkedin.com/posts/hello-interview_how-to-learn-system-design-with-jordan-has-activity-7306069630102446081-7XQp)
- Top 8 YouTube Channels for System Design Interview — [medium.com/javarevisited](https://medium.com/javarevisited/top-8-youtube-channels-for-system-design-interview-preparation-970d103ea18d)

---

## Теги

#подборка #youtube #system-design #ddd #distributed-systems #cqrs #event-sourcing #architecture #microservices #interview
