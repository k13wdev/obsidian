# YouTube — подборка лекций по конспектам Backend

## Главный вывод

Одного курса/плейлиста, который покрывает **все** темы папки [[Backend]], не существует. Для полного покрытия лучше комбинировать русскоязычные практические курсы (Postgres Professional, Слёрм) с англоязычными академическими (CMU, MIT, Cambridge) и индустриальными (Confluent, Redis University, CNCF).

- **RU** сильнее в практике: Postgres, Kubernetes, CI/CD, тюнинг
- **EN** сильнее в теории и глубине: internals, distributed systems, query optimization, formal correctness

---

## Рекомендованные YouTube-каналы (meta-список)

### Русскоязычные

| Канал                   | Профиль                                             | Кластеры   | Авторы                           |
| ----------------------- | --------------------------------------------------- | ---------- | -------------------------------- |
| Postgres Professional   | Внутренности PostgreSQL, MVCC, индексы, оптимизация | 2, 3       | Егор Рогов, Павел Лузанов        |
| Computer Science Center | Фундаментальные CS-курсы                            | 1, 10      | Илья Тетерин и др.               |
| Слёрм (Slurm)           | Kubernetes, DevOps, Docker, CI/CD                   | 6, 7, 8, 9 | Павел Селиванов, Сергей Бондарев |
| HighLoad++ / Онтико     | Доклады по БД, брокерам, distributed                | 2, 3, 5, 6 | Бартунов, Коротков и др.         |
| ADV-IT                  | DevOps-практика: Terraform, Ansible, K8s, GitLab CI | 7, 8, 9    | Денис Астахов                    |
| Дмитрий Кетов           | Linux, DNS, контейнеризация, автоматизация          | 7, 9       | Дмитрий Кетов                    |
| devHands                | Архитектура и тюнинг PostgreSQL                     | 2          | Николай Ихалайнен                |
| Николай Тузов — Golang  | Go-инструменты, в т.ч. тестирование                 | 10         | Николай Тузов                    |

### Англоязычные

| Канал | Профиль | Кластеры | Авторы |
|-------|---------|----------|--------|
| CMU Database Group | Курсы 15-445 (Intro) и 15-721 (Advanced DB Systems) | 1, 2, 3 | Andy Pavlo |
| MIT 6.5840 (OCW) | Distributed Systems | 5 | Robert Morris, Frans Kaashoek |
| Martin Kleppmann | Cambridge Concurrent & Distributed Systems | 5 | Martin Kleppmann |
| Hussein Nasser | Backend engineering, Databases, PostgreSQL, Redis | 1, 2, 3, 4 | Hussein Nasser |
| ByteByteGo | System design, architecture, caching | 5, 6 | Alex Xu |
| Confluent Developer | Kafka 101, Kafka Summit | 6 | Tim Berglund и др. |
| Redis University / Redis | Redis-курсы, RedisDays | 4 | — |
| TechWorld with Nana | Docker, Kubernetes, DevOps | 7, 8, 9 | Nana Janashia |
| KodeKloud | Docker, Kubernetes, CI/CD, cert-prep | 7, 8, 9 | Mumshad Mannambeth |
| That DevOps Guy | Kubernetes, GitOps, DevOps practice | 8, 9 | Marcel Dempers |
| DevOps Toolkit | Kubernetes, GitOps, Argo, Crossplane | 8, 9 | Viktor Farcic |
| Bret Fisher Docker and DevOps | Docker mastery, Compose, Swarm | 7 | Bret Fisher |
| CNCF / KubeCon | CNCF-проекты, Kubernetes talks | 8 | — |
| Gopher Academy (GopherCon) | Go talks, включая testing и fuzzing | 10 | сообщество |
| Ardan Labs | Go-обучение, testing, performance | 10 | Bill Kennedy |
| NDC Conferences | Distributed systems, microservices talks | 5, 6 | — |
| Jepsen | Formal correctness testing of distributed DBs | 3, 5 | Kyle Kingsbury |

---

## Кластер 1 — SQL и реляционная теория

**Темы из конспектов:**
- [[01. Реляционная модель, нормализация и SQL]]
- [[02. Декомпозиция отношений]]
- [[16. SQL-запросы — JOIN, GROUP BY, CTE и оконные функции]]

### RU
**Основной источник:** курс «Базы данных» **Computer Science Center** (Илья Тетерин и более поздние редакции). Покрывает реляционную алгебру, нормальные формы, декомпозицию без потерь.

**Поисковые запросы:**
- `Computer Science Center базы данных лекция`
- `Илья Тетерин базы данных реляционная модель`
- `нормальные формы 1НФ 2НФ 3НФ БКНФ лекция`
- `оконные функции SQL лекция`
- `CTE рекурсивные SQL разбор`

### EN
**Основной источник:** курс **CMU 15-445 Intro to Database Systems** (Andy Pavlo) — первые лекции по реляционной модели и SQL. Дополняет **Hussein Nasser** с практическими видео по JOIN/индексам.

**Поисковые запросы:**
- `CMU 15-445 relational model SQL`
- `Andy Pavlo database systems lecture`
- `Hussein Nasser SQL JOIN`
- `database normalization 3NF BCNF lecture`
- `window functions SQL tutorial lecture`
- `CTE recursive SQL explained`

---

## Кластер 2 — Индексы, хранение, оптимизация (PostgreSQL / InnoDB)

**Темы из конспектов:**
- [[03. Индексы и хранение данных]]
- [[04. B-Tree]]
- [[05. Hash таблица]]
- [[06. Sequential Scan]]
- [[07. Оптимизация запросов]]
- [[08. PostgreSQL]]
- [[09. Внутреннее устройство PostgreSQL]]
- [[10. InnoDB]]

### RU
**Основные источники:**
- **Postgres Professional** — курсы **DBA1**, **DBA2**, **QPT** (Query Performance Tuning), **Hacking PostgreSQL** (Егор Рогов, Павел Лузанов)
- Серия Егора Рогова «Индексы в PostgreSQL» (B-tree, hash, GiST, GIN, SP-GiST, BRIN)
- **devHands** (Николай Ихалайнен) — фрагменты и превью на YouTube
- **HighLoad++** — доклады Олега Бартунова, Александра Короткова

**Поисковые запросы:**
- `Егор Рогов индексы PostgreSQL`
- `Postgres Professional DBA1 DBA2`
- `B-Tree структура данных лекция`
- `hash index PostgreSQL объяснение`
- `Sequential Scan PostgreSQL разбор`
- `Егор Рогов QPT оптимизация запросов`
- `EXPLAIN ANALYZE PostgreSQL разбор`
- `Hacking PostgreSQL`
- `InnoDB устройство MySQL лекция`
- `Николай Ихалайнен PostgreSQL архитектура`

### EN
**Основные источники:**
- **CMU 15-445** — лекции по storage, buffer pool, B+Tree, hash index, sequential scan, query processing
- **CMU 15-721 Advanced Database Systems** — для более глубоких тем (vectorized execution, query optimization)
- **Hussein Nasser** — прикладные разборы индексов и тюнинга PostgreSQL
- **PGCon / PostgresConf** — доклады по internals (включая Bruce Momjian)
- **Percona Live** — InnoDB-специфика

**Поисковые запросы:**
- `CMU 15-445 storage B+Tree indexing`
- `CMU 15-445 query processing optimization`
- `CMU 15-721 Andy Pavlo advanced database`
- `Hussein Nasser database indexes`
- `Hussein Nasser PostgreSQL architecture`
- `Bruce Momjian PostgreSQL internals`
- `InnoDB internals MySQL talk`
- `PGCon internals lecture`
- `EXPLAIN ANALYZE PostgreSQL tutorial`

---

## Кластер 3 — Транзакции, ACID, MVCC, триггеры, миграции

**Темы из конспектов:**
- [[11. Транзакции, ACID, изоляция и MVCC]]
- [[12. Триггеры]]
- [[17. Безопасные миграции и обслуживание]]

### RU
**Основные источники:**
- Серия «MVCC в PostgreSQL» **Егора Рогова** (7 частей)
- Курс **DBA2** Postgres Professional — уровни изоляции, аномалии, VACUUM
- **HighLoad++ / Yandex Scale** — доклады по миграциям без downtime

**Поисковые запросы:**
- `Егор Рогов MVCC PostgreSQL`
- `уровни изоляции транзакций аномалии лекция`
- `ACID объяснение лекция`
- `триггеры PostgreSQL AFTER BEFORE разбор`
- `zero downtime миграции postgres лекция`
- `HighLoad миграции БД без downtime`
- `VACUUM autovacuum PostgreSQL разбор`

### EN
**Основные источники:**
- **CMU 15-445** — Concurrency Control, Recovery, Two-Phase Locking, MVCC, Serializable Snapshot Isolation
- **Jepsen (Kyle Kingsbury)** — проверка корректности изоляции на реальных БД (must-watch для тех, кто хочет понять аномалии)
- **Hussein Nasser** — ACID, isolation levels, MVCC в практическом ключе
- **PGCon / PostgresConf** — доклады по MVCC, VACUUM

**Поисковые запросы:**
- `CMU 15-445 concurrency control MVCC`
- `CMU 15-445 recovery ARIES`
- `Jepsen consistency isolation talk`
- `Kyle Kingsbury Jepsen`
- `Hussein Nasser ACID isolation levels`
- `Hussein Nasser MVCC`
- `PostgreSQL MVCC internals lecture`
- `zero downtime database migration talk`
- `database triggers explained`

---

## Кластер 4 — Redis

**Темы из конспектов:**
- [[01. Основы Redis, типы данных и команды]]
- [[02. Кэширование, cache patterns, TTL и eviction]]
- [[03. Конкурентность, транзакции и Lua scripting]]
- [[04. Pub-Sub в Redis]]
- [[05. Distributed locks, семафоры и очереди]]
- [[06. Персистентность Redis (RDB и AOF)]]
- [[07. Репликация, Sentinel, Cluster и шардинг]]
- [[08. Отказоустойчивость, Consistency и Redis vs альтернативы]]
- [[09. Производительность, Pipeline и управление памятью]]
- [[10. Архитектурные паттерны и антипаттерны Redis]]

### RU
**Основные источники:** единого большого курса нет — собираем из докладов HighLoad++, вебинаров OTUS и Слёрм.

**Поисковые запросы:**
- `Redis основы типы данных лекция`
- `Redis RDB AOF персистентность разбор`
- `Redis Sentinel Cluster репликация лекция`
- `Redis распределённые блокировки Redlock`
- `кэширование паттерны cache-aside write-through лекция`
- `Redis pub/sub streams лекция`
- `Redis cluster шардинг hash slot`
- `HighLoad Redis производительность`

### EN
**Основные источники:**
- **Redis University** + официальный **Redis** YouTube — структурированные курсы по типам данных, persistence, scaling
- **Hussein Nasser** — отдельная серия по Redis internals
- **RedisConf / RedisDays** — доклады с конференций
- **ByteByteGo** — Redis в контексте system design и кэширования

**Поисковые запросы:**
- `Redis University fundamentals`
- `Redis data types strings lists hashes streams`
- `Hussein Nasser Redis internals`
- `Redis persistence RDB AOF explained`
- `Redis Sentinel Cluster tutorial`
- `Redlock distributed lock explained`
- `cache-aside write-through write-behind patterns`
- `ByteByteGo Redis use cases`

---

## Кластер 5 — NoSQL, CAP, распределённые системы

**Темы из конспектов:**
- [[13. NoSQL базы данных и Redis]]
- [[14. Масштабирование, репликация и CAP]]
- [[15. Паттерны распределённых систем]]

### RU
**Основные источники:**
- **HighLoad++ / Saint HighLoad++** (канал «Онтико») — подборка докладов
- **TechLeadConf**, **Yandex Scale** — архитектурные доклады
- Курсы **Слёрм** и **OTUS** по распределённым системам

**Поисковые запросы:**
- `CAP теорема лекция PACELC разбор`
- `шардирование репликация базы данных лекция`
- `SAGA pattern распределённые транзакции лекция`
- `Outbox pattern микросервисы лекция`
- `двухфазный коммит 2PC разбор`
- `Raft Paxos консенсус лекция русский`
- `event sourcing CQRS лекция`

### EN
**Основные источники:**
- **MIT 6.5840 (6.824) Distributed Systems** (Robert Morris) — золотой стандарт: Raft, Zookeeper, Spanner
- **Martin Kleppmann Cambridge lectures** — 8 лекций, 7 часов: клоки, broadcast, consensus, replication, linearizability
- **ByteByteGo** — system design-ракурс: CAP, sharding, replication, patterns
- **NDC Conferences** — доклады по event-driven микросервисам
- **Jepsen** — формальная проверка гарантий в распределённых БД

**Поисковые запросы:**
- `MIT 6.824 distributed systems Robert Morris`
- `MIT 6.5840 Raft consensus`
- `Martin Kleppmann distributed systems lectures`
- `Martin Kleppmann linearizability consensus`
- `ByteByteGo CAP theorem`
- `ByteByteGo database replication sharding`
- `saga pattern explained`
- `outbox pattern microservices`
- `two-phase commit 2PC explained`
- `Raft consensus algorithm lecture`
- `event sourcing CQRS talk`
- `NDC microservices event-driven talk`

---

## Кластер 6 — Брокеры сообщений (Kafka, RabbitMQ, messaging)

**Темы из конспектов:**
- [[01. Kafka — основы, архитектура и partitioning]]
- [[02. Kafka — надёжность, хранение и delivery semantics]]
- [[03. Kafka — обработка ошибок, производительность и ecosystem]]
- [[01. RabbitMQ — основы, архитектура и routing]]
- [[02. RabbitMQ — надёжность доставки и обработка ошибок]]
- [[03. RabbitMQ — масштабирование, отказоустойчивость и эксплуатация]]
- [[01. Общие паттерны messaging — delivery semantics, идемпотентность, retry и DLQ]]
- [[02. Event-driven архитектура, backpressure и сравнение RabbitMQ vs Kafka]]

### RU
**Основные источники:**
- **HighLoad++** (канал Онтико) — регулярные разборы Kafka и RabbitMQ
- **Systems.Education** — воркшоп по Kafka и RabbitMQ
- **Слёрм** — курсы по Kafka
- **OTUS** — открытые уроки по брокерам

**Поисковые запросы:**
- `Kafka архитектура брокер партиции лекция`
- `Kafka exactly once delivery semantics лекция`
- `RabbitMQ exchange queue routing лекция`
- `RabbitMQ vs Kafka сравнение`
- `idempotency идемпотентный consumer лекция`
- `DLQ dead letter queue retry лекция`
- `event-driven архитектура доклад`
- `backpressure потоки сообщений лекция`
- `transactional outbox inbox pattern лекция`

### EN
**Основные источники:**
- **Confluent Developer** (Tim Berglund, Kafka 101, Kafka Summit) — самый авторитетный ресурс по Kafka
- **NDC Conferences** — event-driven микросервисы, exactly-once
- **ByteByteGo** — сравнения Kafka/RabbitMQ/NATS в system design-ракурсе
- **CloudAMQP** / RabbitMQ YouTube — деплой и эксплуатация

**Поисковые запросы:**
- `Confluent Kafka 101 Tim Berglund`
- `Kafka Summit talk`
- `Kafka exactly once semantics explained`
- `Kafka Streams ksqlDB tutorial`
- `RabbitMQ in depth talk`
- `RabbitMQ clustering federation`
- `NDC event-driven microservices`
- `outbox pattern Kafka explained`
- `ByteByteGo Kafka RabbitMQ comparison`
- `backpressure stream processing talk`

---

## Кластер 7 — Docker

**Темы из конспектов:**
- [[01. Docker vs VM, Архитектура, Container Runtime]]
- [[02. Images, Layers, Dockerfile, Multistage Builds]]
- [[03. Жизненный цикл контейнера, Ресурсы]]
- [[04. Volumes, Bind Mounts, tmpfs]]
- [[05. Конфигурация, Секреты, Stateless vs Stateful]]
- [[06. Healthchecks, Логирование, Debugging, Сигналы]]
- [[07. Теги образов, Immutable Images, Безопасность контейнеров]]
- [[08. Docker Networking]]
- [[09. Docker Compose]]

### RU
**Основные источники:**
- **Богдан Стащук** — «Полный курс Docker для начинающих» (~3 часа, бесплатно)
- **Слёрм / Southbridge** — курс Docker (часть DevOps Upgrade)
- **ADV-IT** — практика Docker в связке с AWS/GitLab CI
- **Дмитрий Кетов** — контейнеризация

**Поисковые запросы:**
- `Богдан Стащук Docker полный курс`
- `Docker с нуля лекция контейнеры`
- `Dockerfile multistage build разбор`
- `Docker volumes bind mount разбор`
- `Docker networking bridge overlay лекция`
- `Docker Compose полный разбор`
- `Docker безопасность immutable images лекция`

### EN
**Основные источники:**
- **TechWorld with Nana** — Docker Tutorial for Beginners и углубляющие видео
- **Bret Fisher Docker and DevOps** — Docker Captain, глубокие темы по Compose/Swarm/Desktop
- **KodeKloud** — Docker Deep Dive с практическими лабами
- **NetworkChuck** — базовый вход в контейнеры

**Поисковые запросы:**
- `TechWorld with Nana Docker tutorial`
- `Bret Fisher Docker mastery`
- `Nigel Poulton Docker deep dive`
- `Docker multistage build tutorial`
- `Docker networking bridge overlay tutorial`
- `Docker Compose explained`
- `container security best practices talk`
- `OCI runtime containerd explained`

---

## Кластер 8 — Kubernetes

**Темы из конспектов:**
- [[01. Архитектура Kubernetes, Control Plane, etcd]]
- [[02. Pod, Labels, Namespaces]]
- [[03. Workloads: Deployment, ReplicaSet, StatefulSet, DaemonSet, Job]]
- [[04. Networking: Service, Ingress, DNS, CNI]]
- [[05. ConfigMap, Secret, Конфигурация]]
- [[06. Resource Management: Requests, Limits, QoS]]
- [[07. Probes, Rolling Update, Rollback]]
- [[08. HPA, Autoscaling]]
- [[09. Pod Lifecycle, Debugging, Graceful Shutdown]]
- [[10. Persistent Volumes, PVC, Storage Classes]]
- [[11. RBAC, Network Policies, Pod Security]]

### RU
**Основные источники:**
- **Слёрм** — «Kubernetes База» (Павел Селиванов, Сергей Бондарев) — флагман
- **Слёрм** — «Kubernetes для разработчиков»
- **ADV-IT** — практические видео по K8s
- **Southbridge** — доклады и вебинары

**Поисковые запросы:**
- `Слёрм Kubernetes База Павел Селиванов`
- `Kubernetes архитектура control plane etcd лекция`
- `Pod ReplicaSet Deployment разбор`
- `Kubernetes Service Ingress DNS разбор`
- `ConfigMap Secret Kubernetes лекция`
- `Kubernetes requests limits QoS разбор`
- `HPA autoscaling Kubernetes лекция`
- `Persistent Volume PVC StorageClass разбор`
- `Kubernetes RBAC Network Policy Pod Security лекция`
- `ADV-IT Kubernetes`

### EN
**Основные источники:**
- **TechWorld with Nana** — Kubernetes Tutorial for Beginners (~4 часа), CKA-курс
- **KodeKloud** — Kubernetes for Absolute Beginners + CKA/CKAD prep
- **That DevOps Guy (Marcel Dempers)** — продвинутые темы, GitOps
- **DevOps Toolkit (Viktor Farcic)** — K8s + Argo/Crossplane/platform engineering
- **CNCF / KubeCon** — официальные доклады с конференций
- Классические доклады **Kelsey Hightower**

**Поисковые запросы:**
- `TechWorld with Nana Kubernetes tutorial`
- `KodeKloud Kubernetes for beginners`
- `Kubernetes architecture control plane etcd`
- `Pod Deployment StatefulSet explained`
- `Kubernetes Service Ingress CNI explained`
- `Kubernetes RBAC Network Policy tutorial`
- `HPA VPA autoscaling Kubernetes`
- `Kelsey Hightower Kubernetes talk`
- `KubeCon deep dive talk`
- `That DevOps Guy Kubernetes`
- `Viktor Farcic Kubernetes`

---

## Кластер 9 — CI/CD и DevOps

**Темы из конспектов:**
- [[01. CI, CD, Pipeline, Инструменты]]
- [[02. Стратегии ветвления и деплоя]]
- [[03. Тестирование в CICD]]
- [[04. Метрики CICD]]
- [[05. Security, Secrets, DevSecOps]]
- [[06. Артефакты, Реестры, Версионирование]]
- [[07. Docker и Kubernetes в CICD, GitOps]]
- [[08. Environments, IaC, DB Migrations]]
- [[09. Observability, Платформы, Антипаттерны]]

### RU
**Основные источники:**
- **Слёрм** — «CI/CD на примере GitLab CI», «GitLab CI/CD»
- **ADV-IT** — DevOps, GitLab CI, Jenkins, ArgoCD, Terraform
- **Дмитрий Кетов** — Linux, автоматизация
- **OTUS** — открытые уроки по CI/CD

**Поисковые запросы:**
- `Слёрм GitLab CI CD курс`
- `ADV-IT GitLab CI Jenkins ArgoCD`
- `GitOps ArgoCD FluxCD лекция`
- `стратегии деплоя blue green canary лекция`
- `DevSecOps secrets vault лекция`
- `Terraform Ansible IaC лекция`
- `Jenkins pipeline declarative разбор`
- `trunk based development GitFlow разбор`
- `observability метрики SLO SLI лекция`

### EN
**Основные источники:**
- **TechWorld with Nana** — CI/CD, GitLab, GitHub Actions, Jenkins, Ansible, Prometheus
- **DevOps Toolkit (Viktor Farcic)** — GitOps, Argo, Crossplane, platform engineering (сильный GitOps-фокус)
- **That DevOps Guy (Marcel Dempers)** — GitOps, IaC, Kubernetes в CI/CD
- **KodeKloud** — CI/CD learning paths
- **CI/CD conf-talks:** GitHub Universe, GitLab Connect

**Поисковые запросы:**
- `TechWorld with Nana CI CD`
- `TechWorld with Nana GitLab GitHub Actions Jenkins`
- `Viktor Farcic GitOps Argo`
- `That DevOps Guy GitOps`
- `KodeKloud CI CD pipeline`
- `trunk based development talk`
- `blue green canary deployment explained`
- `DevSecOps secrets management talk`
- `Terraform tutorial IaC`
- `observability SLO SLI SLA tutorial`
- `platform engineering backstage talk`

---

## Кластер 10 — Тестирование в Go

**Темы из конспектов:**
- [[01. Основы тестирования в Go]]
- [[02. Testify]]
- [[03. Benchmarks]]
- [[04. Fuzzing]]
- [[05. Интеграционное тестирование]]
- [[06. TDD и BDD]]
- [[07. Coverage]]

### RU
**Основные источники:**
- **GolangConf / GoCloud / HighLoad++** — доклады по тестированию
- **Николай Тузов — Golang** — разборы Go-инструментов
- Выступление **Сергея Петрова (Selectel)** о fuzzing после Go 1.18
- Доклады **OTUS** и **Слёрм** по Go

**Поисковые запросы:**
- `Николай Тузов тестирование Go`
- `Go table driven tests лекция`
- `testify mock suite разбор`
- `Go benchmark профилирование лекция`
- `Go fuzzing 1.18 лекция`
- `Сергей Петров fuzzing Go Selectel`
- `Go integration testing testcontainers`
- `TDD BDD Go лекция`
- `Go coverage профилирование тестов лекция`

### EN
**Основные источники:**
- **Gopher Academy (GopherCon)** — основной канал с талками, включая **Katie Hockman** «Fuzz Testing Made Easy» (GopherCon 2022) и **Dmitry Vyukov** «Go Dynamic Tools»
- **Ardan Labs (Bill Kennedy)** — курсы и видео по testing/profiling
- **JustForFunc (Francesc Campoy)** — архивные, но золотые видео по Go
- **Jon Calhoun** / **TutorialEdge** — прикладные туториалы

**Поисковые запросы:**
- `GopherCon testing talk`
- `Katie Hockman fuzz testing Go`
- `Dmitry Vyukov Go dynamic tools`
- `Bill Kennedy Go testing`
- `Ardan Labs Go testing benchmarking`
- `JustForFunc Go testing`
- `Go table driven tests tutorial`
- `Go testify mock suite tutorial`
- `Go benchmark pprof tutorial`
- `Go integration testing testcontainers`
- `TDD BDD Go tutorial`

---

## Стратегия просмотра

### Сбалансированный трек (RU + EN)

1. **Фундамент БД (теория):** **CMU 15-445** (EN, Andy Pavlo) — первые 10–12 лекций дают полноценный фундамент.
2. **Фундамент БД (практика):** курсы **Postgres Professional** (DBA1 → DBA2 → QPT) — применение на PostgreSQL.
3. **Распределённые системы:** **MIT 6.824/6.5840** (EN) или **Martin Kleppmann Cambridge** (EN, короче). Для RU — доклады HighLoad++.
4. **Брокеры:** **Confluent Developer Kafka 101** (EN, Tim Berglund) + воркшоп **Systems.Education** (RU).
5. **Контейнеры и K8s:** **TechWorld with Nana** (EN) + **Слёрм Kubernetes База** (RU). Комбинация даёт и теорию, и практику.
6. **CI/CD:** **Слёрм GitLab CI** (RU) + **DevOps Toolkit (Viktor Farcic)** (EN).
7. **Redis:** **Redis University** (EN, структурировано) + точечные доклады RedisConf.
8. **Testing Go:** **GopherCon talks** (EN) + **Николай Тузов** (RU).

### Только RU-трек
CSC → Postgres Professional → HighLoad++ → Слёрм (Docker/K8s/CI-CD) → Николай Тузов.

### Только EN-трек
CMU 15-445 → CMU 15-721 → MIT 6.824 / Kleppmann → Hussein Nasser → Confluent → Redis University → TechWorld with Nana → KodeKloud / Viktor Farcic → GopherCon.

---

## Оговорка

Прямые ссылки на конкретные плейлисты/видео намеренно не указаны — URL может быть удалён, переименован или перенесён. Поиск по названию канала + теме на самом YouTube надёжнее сохранённой ссылки.

---

## Источники

### Русскоязычные платформы
- Postgres Professional — [postgrespro.ru/education](https://postgrespro.ru/education)
- Computer Science Center — [compscicenter.ru/videos](https://compscicenter.ru/videos/)
- Слёрм (Slurm) — [slurm.io](https://slurm.io)
- devHands — [devhands.ru/postgresql](http://devhands.ru/postgresql)
- Southbridge — [southbridge.io](https://southbridge.io)
- HighLoad++ — [highload.ru](https://highload.ru/spring/2021)
- Systems.Education воркшоп Kafka + RabbitMQ — [systems.education/kafka-workshop](https://systems.education/kafka-workshop)

### Англоязычные курсы и каналы
- CMU 15-445 Intro to Database Systems — [15445.courses.cs.cmu.edu](https://15721.courses.cs.cmu.edu/spring2024/)
- CMU 15-721 Advanced Database Systems — [15721.courses.cs.cmu.edu/spring2024](https://15721.courses.cs.cmu.edu/spring2024/)
- MIT 6.5840 Distributed Systems — [pdos.csail.mit.edu/6.824](https://pdos.csail.mit.edu/6.824/)
- Martin Kleppmann Distributed Systems (Cambridge) — [martin.kleppmann.com](https://martin.kleppmann.com/2020/11/18/distributed-systems-and-elliptic-curves.html)
- Hussein Nasser — [husseinnasser.com](https://www.husseinnasser.com/)
- TechWorld with Nana — [techworld-with-nana.com](https://www.techworld-with-nana.com)
- KodeKloud — [kodekloud.com](https://kodekloud.com/)
- Confluent Developer (Kafka 101) — [developer.confluent.io](https://developer.confluent.io/courses/apache-kafka/events/)
- Redis University — [university.redis.io](https://university.redis.io/)
- NDC Conferences — [ndcconferences.com](https://ndcconferences.com/)
- Class Central подборка GopherCon — [classcentral.com/course/youtube-gophercon](https://www.classcentral.com/course/youtube-gophercon-2022-katie-hockman-fuzz-testing-made-easy-235451)

### Ключевые серии статей / видео
- «Indexes in PostgreSQL» (Егор Рогов) — [habr.com/companies/postgrespro](https://habr.com/en/companies/postgrespro/articles/443284/)
- «MVCC в PostgreSQL» — [postgrespro.ru/blog/pgsql/17758](https://postgrespro.ru/blog/pgsql/17758)
- «Hacking PostgreSQL» — [postgrespro.ru/education/courses/hacking](https://postgrespro.ru/education/courses/hacking)
- Распределённые транзакции (SAGA vs 2PC) — [habr.com/ru/articles/906484](https://habr.com/ru/articles/906484/)
- Fuzzing-тесты в Go (Сергей Петров, Selectel) — [habr.com/companies/oleg-bunin](https://habr.com/ru/companies/oleg-bunin/articles/709248/)

### Обзоры каналов
- Топ-5 русскоязычных YouTube-каналов по DevOps — [nikproject.com](https://www.nikproject.com/top-5/top-5-russian-devops-youtube-channels)
- 8 DevOps YouTube Channels Worth Following — [dev.to/microtica](https://dev.to/microtica/8-devops-youtube-channels-worth-following-3m9k)

---

## Теги

#подборка #youtube #backend #postgresql #kubernetes #docker #cicd #redis #kafka #rabbitmq #golang/testing #distributed-systems
