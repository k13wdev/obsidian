## Суть и контекст

**IndexedDB** — встроенная в браузер низкоуровневая объектная база данных для хранения больших объёмов структурированных данных: сложных объектов, файлов, BLOB. В отличие от localStorage (синхронный, только строки, ~5 МБ), IndexedDB **асинхронный**, хранит любые типы данных и поддерживает индексы для быстрого поиска. Используется как альтернатива для тяжёлых клиентских приложений (PWA, offline-first).

## Ключевые идеи

- IndexedDB — **объектно-ориентированная** БД (не SQL-таблицы)
- Все операции **асинхронные** через `IDBRequest` (onsuccess/onerror) — не блокирует main thread
- **Любые данные**: объекты, массивы, файлы, BLOB (в отличие от localStorage — только строки)
- Все операции происходят в **транзакции** — нет транзакции → нет доступа к данным
- Схема БД (object stores, индексы) создаётся в `onupgradeneeded`
- **Версионирование**: при изменении схемы нужно увеличить номер версии
- **Индексы** — быстрый поиск по любому свойству объекта, не только по ключу
- **Курсор** — поэлементный обход без загрузки всего набора в память

## Определения и термины

**IndexedDB** — низкоуровневая браузерная объектная БД для хранения больших объёмов структурированных данных.

**IDBDatabase** — объект базы данных. Основной контейнер, определяющий схему и версию.

**Object Store** — аналог «таблицы» в SQL. Хранит объекты, автоматически сортированные по ключу. В одной БД может быть несколько хранилищ.

**Index (Индекс)** — дополнительная структура для поиска записей по свойствам, отличным от первичного ключа.

**Transaction (Транзакция)** — обёртка для группы операций. Любое чтение или запись данных происходит строго в контексте транзакции.

**IDBRequest** — объект, возвращаемый каждой операцией IndexedDB. Имеет состояние `pending`/`done`. Результат доступен через `onsuccess` в `request.result`.

**Cursor (Курсор)** — механизм (IDBCursor) для асинхронного поэлементного обхода записей в хранилище или индексе.

**`onupgradeneeded`** — событие, срабатывающее при первом открытии БД или при увеличении версии. Единственное место, где можно создавать/удалять object stores и индексы.

**`readonly`** — режим транзакции только для чтения. Несколько таких транзакций работают параллельно.

**`readwrite`** — режим транзакции для чтения и записи. Блокирует другие `readwrite`-транзакции для того же хранилища.

**`versionchange`** — режим транзакции в `onupgradeneeded`. Позволяет изменять схему.

**`autoIncrement`** — опция object store: ключ генерируется автоматически (аналог AUTO_INCREMENT в SQL).

**keyPath** — путь к свойству объекта, используемому как первичный ключ.

## Как устроено

### Асинхронная природа: IDBRequest

Каждая операция возвращает `IDBRequest` — результат доступен через обработчики событий:

```javascript
const request = indexedDB.open("myDB", 1);

request.onsuccess = function(event) {
  const db = event.target.result; // = request.result
  console.log("БД открыта успешно");
};

request.onerror = function(event) {
  console.error("Ошибка:", request.error);
};
```

### Открытие / создание базы данных

```javascript
// indexedDB.open(name, version)
const openRequest = indexedDB.open("notes_db", 1);

// Срабатывает при: первом открытии или увеличении версии
openRequest.onupgradeneeded = function(event) {
  const db = event.target.result;

  // Создаём object store с автоинкрементным ключом
  const objectStore = db.createObjectStore("notes_os", {
    autoIncrement: true  // ключ генерируется автоматически
    // или: keyPath: "id" — поле объекта используется как ключ
  });

  // Создаём индексы для поиска по свойствам
  objectStore.createIndex("title", "title", { unique: false });
  objectStore.createIndex("body",  "body",  { unique: false });
};

openRequest.onsuccess = function(event) {
  const db = event.target.result;
  // Работаем с db
};

openRequest.onerror = function(event) {
  console.error("Ошибка открытия БД");
};
```

### Транзакции

Все операции с данными — только в контексте транзакции:

```javascript
// db.transaction(storeNames, mode)
const transaction = db.transaction(["notes_os"], "readwrite");

// Получаем object store из транзакции
const objectStore = transaction.objectStore("notes_os");

// Обработка завершения транзакции
transaction.oncomplete = function() {
  console.log("Транзакция завершена");
};
transaction.onerror = function() {
  console.error("Ошибка транзакции");
};
```

**Режимы транзакций:**

| Режим | Параллельность | Использование |
|-------|---------------|---------------|
| `readonly` | Несколько параллельно | Чтение данных |
| `readwrite` | Последовательно (блокирует) | Запись/удаление |
| `versionchange` | Только в onupgradeneeded | Изменение схемы |

**Важно**: Транзакция автоматически завершается (commit), если в текущей задаче Event Loop больше нет отложенных запросов. Нельзя выйти из функции и вернуться к транзакции асинхронно — она уже закрыта.

### CRUD операции

```javascript
const transaction = db.transaction(["notes_os"], "readwrite");
const store = transaction.objectStore("notes_os");

// ADD — только для новых записей (если ключ уже есть → ошибка)
const newItem = { title: "Заметка 1", body: "Текст заметки" };
const addRequest = store.add(newItem);
addRequest.onsuccess = function() {
  console.log("Добавлено, ключ:", addRequest.result);
};

// PUT — обновить если есть, создать если нет
const putRequest = store.put({ title: "Обновлённая заметка", body: "Новый текст" }, 1);
// 1 — ключ (если keyPath не задан в схеме)

// GET — получить по ключу
const getRequest = store.get(1);
getRequest.onsuccess = function(event) {
  const item = event.target.result;
  console.log("Найдено:", item);
};

// DELETE — удалить по ключу
const deleteRequest = store.delete(1);

// GET ALL — получить все записи (или по диапазону)
const getAllRequest = store.getAll();
getAllRequest.onsuccess = function(event) {
  const allItems = event.target.result; // массив объектов
  console.log("Все записи:", allItems);
};
```

### Индексы для поиска

Поиск не по первичному ключу, а по другому полю объекта:

```javascript
const transaction = db.transaction(["customers"], "readonly");
const store = transaction.objectStore("customers");

// Получаем индекс по имени поля
const index = store.index("name");

// Поиск по значению индекса
const request = index.get("Donna");
request.onsuccess = function(event) {
  console.log("Найдена:", event.target.result);
};

// getAll по индексу
const allRequest = index.getAll("Donna"); // все записи с name = "Donna"
```

### Курсор для поэлементного обхода

`getAll()` загружает все записи в память. Курсор позволяет обходить их по одной:

```javascript
const transaction = db.transaction(["notes_os"], "readonly");
const store = transaction.objectStore("notes_os");

store.openCursor().onsuccess = function(event) {
  const cursor = event.target.result;

  if (cursor) {
    // cursor.key — ключ текущей записи
    // cursor.value — объект текущей записи
    console.log("Ключ:", cursor.key, "Значение:", cursor.value);

    cursor.continue(); // переход к следующей записи
  } else {
    console.log("Больше записей нет");
  }
};

// Курсор по индексу
const index = store.index("title");
index.openCursor().onsuccess = function(event) {
  const cursor = event.target.result;
  if (cursor) {
    console.log(cursor.value);
    cursor.continue();
  }
};
```

## Детали и нюансы

### Версионирование схемы

```javascript
// Версия 1 — первоначальная схема
const request = indexedDB.open("myDB", 1);
request.onupgradeneeded = function(e) {
  const db = e.target.result;
  db.createObjectStore("users", { keyPath: "id" });
};

// Версия 2 — добавляем новый индекс
const request2 = indexedDB.open("myDB", 2);
request2.onupgradeneeded = function(e) {
  const db = e.target.result;
  // e.oldVersion — старая версия; e.newVersion — новая
  if (e.oldVersion < 2) {
    const store = e.target.transaction.objectStore("users");
    store.createIndex("email", "email", { unique: true });
  }
};
```

### Нюанс с транзакцией и async/await

```javascript
// ПЛОХО: await разрывает task → транзакция закрывается
async function badExample(db) {
  const transaction = db.transaction(["store"], "readwrite");
  const store = transaction.objectStore("store");
  store.add({ id: 1 });

  await someAsyncOperation(); // ← транзакция уже закрыта!

  store.add({ id: 2 }); // Ошибка: транзакция неактивна
}

// ХОРОШО: все операции в рамках одного синхронного потока
function goodExample(db) {
  const transaction = db.transaction(["store"], "readwrite");
  const store = transaction.objectStore("store");
  store.add({ id: 1 });
  store.add({ id: 2 }); // оба запроса до await
}
```

### keyPath vs autoIncrement

```javascript
// keyPath: "id" — ключ берётся из свойства объекта
db.createObjectStore("users", { keyPath: "id" });
store.add({ id: 1, name: "Вася" }); // ключ = 1

// autoIncrement — ключ генерируется автоматически
db.createObjectStore("notes", { autoIncrement: true });
store.add({ title: "Заметка" }); // ключ = 1 (автоматически)
// store.add вернёт ключ через request.result

// Оба сразу — и keyPath, и autoIncrement
db.createObjectStore("items", { keyPath: "id", autoIncrement: true });
store.add({ name: "item" }); // id будет присвоен автоматически
```

### Structured Clone Algorithm

IndexedDB поддерживает хранение любых объектов, поддерживаемых алгоритмом структурированного клонирования (structured clone algorithm), включая сложные типы: аудио, видео и Blob.

### Всплытие событий (Event Bubbling)

События `error` и `abort` всплывают по иерархии: `IDBRequest` → `IDBTransaction` → `IDBDatabase`. Глобальную обработку ошибок можно повесить напрямую на `db.onerror`:

```javascript
db.onerror = function(event) {
  console.error("Database error:", event.target.error);
};
// Перехватит ошибки от всех запросов и транзакций
```

### Активность транзакции и Event Loop

Транзакция переключается между **активным** и **неактивным** состояниями в зависимости от event loop task. Транзакция активна:
- В момент создания
- Во время обработки коллбэков `onsuccess`/`onerror` от связанных запросов

Вне этих задач транзакция **неактивна** — добавление новых запросов бросит ошибку.

### onblocked — конфликт версий между вкладками

```javascript
const request = indexedDB.open("myDB", 2);

request.onblocked = function() {
  // Старая вкладка (версия 1) не закрыла соединение
  console.log("Обновление заблокировано. Закройте другие вкладки с этим сайтом.");
};
```

Вкладке со старой версией приходит событие `onversionchange` — она должна вызвать `db.close()`, чтобы новая вкладка могла выполнить `onupgradeneeded`.

### Дробные номера версий

Версии являются **целыми числами**. Дробные значения (`2.1` или `2.4`) округляются (оба до `2`), из-за чего `onupgradeneeded` может не сработать.

### Потеря данных при крахе (Firefox 40+)

Событие `oncomplete` вызывается, как только ОС отдана команда на запись (flush не гарантирован на физический диск). При сбое питания возможна потеря транзакции. Для критичных данных — экспериментальный режим `readwriteflush`.

### Закрытие браузера

Все незавершённые транзакции обрываются с ошибкой `AbortError` (эквивалентно вызову `IDBTransaction.abort()`). База данных генерирует событие `close`.

### Пустой массив storeNames

```javascript
// Бросит InvalidAccessError
db.transaction([]); // пустой массив
```

## Сравнения и trade-offs

| | IndexedDB | localStorage | sessionStorage | Cookies |
|---|---|---|---|---|
| Тип данных | Любые (объекты, файлы, BLOB) | Только строки | Только строки | Только строки |
| Синхронность | Асинхронный | Синхронный | Синхронный | Синхронный |
| Размер | Сотни МБ (зависит от браузера) | ~5–10 МБ | ~5–10 МБ | 4 КБ |
| Индексы / поиск | Да | Нет | Нет | Нет |
| Транзакции | Да | Нет | Нет | Нет |
| Сложность API | Высокая | Низкая | Низкая | Средняя |
| Отправка на сервер | Нет | Нет | Нет | Да |
| Service Worker | Да | Нет | Нет | Нет |

## Примеры

### Полный пример: открытие → запись → чтение

```javascript
const openRequest = indexedDB.open("library", 1);

openRequest.onupgradeneeded = function(e) {
  const db = e.target.result;
  const store = db.createObjectStore("books", { keyPath: "isbn" });
  store.createIndex("title", "title", { unique: false });
  store.createIndex("author", "author", { unique: false });
};

openRequest.onsuccess = function(e) {
  const db = e.target.result;

  // Запись
  const writeTx = db.transaction(["books"], "readwrite");
  const writeStore = writeTx.objectStore("books");
  writeStore.add({
    isbn: "978-3-16-148410-0",
    title: "JavaScript: The Good Parts",
    author: "Douglas Crockford"
  });

  writeTx.oncomplete = function() {
    // Чтение по ключу
    const readTx = db.transaction(["books"], "readonly");
    const readStore = readTx.objectStore("books");
    const getRequest = readStore.get("978-3-16-148410-0");

    getRequest.onsuccess = function(e) {
      console.log("Книга:", e.target.result.title);
    };
  };
};
```

### Поиск по индексу

```javascript
const tx = db.transaction(["books"], "readonly");
const index = tx.objectStore("books").index("author");

// Найти все книги автора
index.getAll("Douglas Crockford").onsuccess = function(e) {
  console.log("Книги:", e.target.result);
};
```

## Связи с другими темами

- [[localStorage и sessionStorage]] — более простая альтернатива для небольших строковых данных
- [[Cookies]] — для данных, которые нужно передавать на сервер
- [[Event Loop]] — асинхронность IndexedDB строится поверх Event Loop; транзакции закрываются по завершении задачи

## Важно / подводные камни / best practices

- **Транзакция автозакрывается** — все запросы одной транзакции должны быть в одном синхронном блоке; `await` разрывает транзакцию
- **`onupgradeneeded`** — единственное место для изменения схемы; при изменении структуры увеличивай версию
- **`add` vs `put`** — `add` падает при дублировании ключа; `put` — обновляет или создаёт
- **`getAll` осторожно** — загружает все записи в память; для больших наборов используй курсор
- **Обрабатывай `onerror`** на транзакции и на запросах — иначе ошибки будут молча проглочены
- **Используй библиотеки** для сложных случаев: `idb` (обёртка с Promise API), `Dexie.js`
- **Service Worker + IndexedDB** — мощная комбинация для offline-first приложений
- **Группируй связанные операции в одну транзакцию** — если выполнять clear + write в разных транзакциях, при крахе между ними база останется пустой
- **Указывай в `transaction()` только нужные хранилища** — браузер может запускать `readonly` транзакции конкурентно, если их scope не пересекается
- **Используй `db.onerror` для глобальной обработки ошибок** — вместо назначения `onerror` на каждый `IDBRequest`, ошибки всплывают вверх по иерархии
- **`getAll()` эффективнее курсора для массивов данных** — создаёт объекты сразу, без цены на создание `cursor.value` на каждом шаге

---

## Карточки для повторения

#flashcards/browser/indexdb

Что такое IndexedDB?::Встроенная в браузер низкоуровневая объектная БД для хранения больших объёмов структурированных данных (объекты, файлы, BLOB). Асинхронная, поддерживает индексы и транзакции.

Чем IndexedDB отличается от localStorage?::IndexedDB: асинхронный, хранит любые типы, сотни МБ, индексы, транзакции. localStorage: синхронный, только строки, ~5–10 МБ, нет индексов.

Что такое Object Store?::Аналог «таблицы» в SQL. Хранит объекты, автоматически сортированные по ключу. В одной БД может быть несколько хранилищ.

Что такое `onupgradeneeded` и когда срабатывает?::Событие, срабатывающее при первом открытии БД или при увеличении номера версии. Единственное место, где можно создавать/удалять object stores и индексы.

Что такое IDBRequest?::Объект, возвращаемый каждой операцией IndexedDB. Имеет состояние `pending`/`done`. Результат доступен через `event.target.result` в обработчике `onsuccess`.

Чем отличается транзакция `readonly` от `readwrite`?::`readonly` — только чтение, несколько параллельно. `readwrite` — чтение и запись, блокирует другие `readwrite`-транзакции для того же хранилища, выполняются последовательно.

Когда транзакция автоматически закрывается?::Когда в текущей задаче Event Loop больше нет отложенных запросов. После `await` транзакция уже закрыта.

Чем `add` отличается от `put`?::`add` — только для новых записей; если ключ уже существует — ошибка. `put` — обновит если ключ есть, создаст новый если нет.

Что такое Index (индекс) в IndexedDB?::Дополнительная структура для поиска записей по свойствам объекта, отличным от первичного ключа. Создаётся через `objectStore.createIndex()` в `onupgradeneeded`.

Что такое Cursor (курсор) и зачем нужен?::Механизм для асинхронного поэлементного обхода записей в хранилище или индексе без загрузки всего набора в память. Используй `openCursor().onsuccess` + `cursor.continue()`.

Чем `getAll` опасен для больших наборов?::Загружает все записи в память сразу. При большом количестве записей лучше использовать курсор для поэлементного обхода.

Что такое `keyPath` и `autoIncrement`?::`keyPath: "id"` — указывает свойство объекта, используемое как первичный ключ. `autoIncrement: true` — ключ генерируется автоматически (аналог AUTO_INCREMENT в SQL).

Как события ошибок всплывают в IndexedDB?::IDBRequest → IDBTransaction → IDBDatabase. Можно назначить один `db.onerror` для глобальной обработки всех ошибок.

Что такое событие `onblocked` в IndexedDB?::Срабатывает, когда открытие БД с новой версией заблокировано, потому что другая вкладка держит соединение со старой версией. Старая вкладка должна получить `onversionchange` и вызвать `db.close()`.

Что произойдёт при передаче дробного номера версии в `indexedDB.open()`?::Дробные значения округляются (2.1 и 2.4 → 2), из-за чего `onupgradeneeded` может не сработать. Версии должны быть целыми числами.

Что поддерживает IndexedDB через structured clone algorithm?::Любые объекты: обычные объекты, массивы, файлы, Blob, аудио, видео. В отличие от localStorage, который хранит только строки.

Когда транзакция становится неактивной?::Транзакция активна в момент создания и во время обработки коллбэков onsuccess/onerror. Вне этих event loop tasks она неактивна — добавление запросов бросит ошибку.

---

## Источники

**Сайты / Документация:**
- MDN Web Docs: IndexedDB API — IDBDatabase, IDBObjectStore, IDBTransaction, IDBRequest, IDBIndex, IDBCursor
- MDN Web Docs: Using IndexedDB — открытие, схема (onupgradeneeded), CRUD, индексы, курсоры, версионирование
- MDN Web Docs: Client-side storage — сравнение localStorage, sessionStorage, cookies, IndexedDB

---

## Теги

#конспект #javascript #браузер #indexeddb #client-side-storage #idbdatabase #transactions #cursor #object-store #offline-first
