## Суть и контекст

**Web Storage API** — браузерный механизм хранения пар «ключ/значение» на стороне клиента. Более интуитивная альтернатива HTTP-cookies для хранения небольших данных без отправки на сервер. Включает два хранилища: **localStorage** (постоянное) и **sessionStorage** (на время вкладки).

## Ключевые идеи

- Оба хранилища предоставляют **одинаковый API** (`Storage` интерфейс)
- Ключи и значения — **только строки** (UTF-16); объекты нужно сериализовать через `JSON.stringify`
- API **синхронное** — блокирует main thread; при больших данных снижает производительность
- `localStorage` — данные живут **бессрочно**, общие для всех вкладок одного origin
- `sessionStorage` — данные живут **до закрытия вкладки**, изолированы между вкладками
- Событие `storage` срабатывает в **других** вкладках при изменении `localStorage`
- Не подходит для больших/структурированных данных → используй [[IndexedDB]]
- Доступ из сторонних iframe может быть заблокирован если пользователь запретил сторонние cookies

## Определения и термины

**Web Storage API** — браузерный API для хранения пар «ключ/значение» на стороне клиента.

**localStorage** — постоянное хранилище, привязанное к origin. Данные сохраняются после закрытия браузера и перезагрузки компьютера.

**sessionStorage** — хранилище, привязанное к origin **и** конкретной вкладке (сессии). Данные удаляются при закрытии вкладки.

**Origin** — уникальная комбинация протокола + домена + порта (например, `https://example.com:443`).

**Storage** — интерфейс объекта, возвращаемого `window.localStorage` и `window.sessionStorage`. Предоставляет все методы работы с хранилищем.

**StorageEvent** — событие, генерируемое на `window` при изменении `localStorage`. Срабатывает в **других** документах с тем же origin, не на странице-источнике.

**QuotaExceededError** — ошибка, выбрасываемая при превышении квоты хранилища или при отключённом хранилище в настройках браузера.

## Как устроено

### Отличия localStorage vs sessionStorage

| | `localStorage` | `sessionStorage` |
|---|---|---|
| Lifetime | Бессрочно | До закрытия вкладки |
| Scope | Origin (протокол + домен + порт) | Origin + конкретная вкладка |
| Общие вкладки | Да — все вкладки одного origin | Нет — каждая вкладка изолирована |
| Перезагрузка страницы | Данные сохраняются | Данные сохраняются |
| Закрытие браузера | Данные сохраняются | Данные удаляются |
| Режим инкогнито | Данные удаляются при закрытии последней приватной вкладки | Данные удаляются при закрытии вкладки |

### API объекта Storage

```javascript
// Запись
localStorage.setItem('key', 'value');
sessionStorage.setItem('key', 'value');

// Чтение (null если ключа нет)
localStorage.getItem('key');      // 'value'
localStorage.getItem('nokey');    // null

// Удаление одного ключа
localStorage.removeItem('key');

// Очистка всего хранилища
localStorage.clear();

// Получить имя ключа по индексу
localStorage.key(0);              // имя первого ключа

// Количество элементов
localStorage.length;              // число
```

### Только строки — сериализация объектов

```javascript
// ПЛОХО: объект превратится в '[object Object]'
localStorage.setItem('user', { name: 'Вася' });
localStorage.getItem('user'); // '[object Object]'

// ХОРОШО: сериализация через JSON
const user = { name: 'Вася', age: 30 };
localStorage.setItem('user', JSON.stringify(user));

const loaded = JSON.parse(localStorage.getItem('user'));
loaded.name; // 'Вася'

// Числа тоже конвертируются в строки
localStorage.setItem('count', 42);
localStorage.getItem('count'); // '42' (строка!)
typeof localStorage.getItem('count'); // 'string'
```

### Событие `storage`

Генерируется на `window` в **других** вкладках/документах при изменении `localStorage`:

```javascript
window.addEventListener('storage', function(e) {
  console.log('Изменён ключ: ' + e.key);
  console.log('Было: ' + e.oldValue);
  console.log('Стало: ' + e.newValue);
  console.log('URL страницы-источника: ' + e.url);
  console.log('Хранилище: ', e.storageArea); // localStorage или sessionStorage
});
```

- Для `localStorage` — синхронизирует все открытые вкладки одного сайта
- Для `sessionStorage` — срабатывает только для iframe внутри текущей вкладки
- **Не срабатывает** на странице, которая сделала изменение

## Детали и нюансы

### Синхронность — главное ограничение

Все операции выполняются **синхронно**, блокируя main thread:

```javascript
// Это заблокирует main thread на время записи
for (let i = 0; i < 10000; i++) {
  localStorage.setItem('key_' + i, 'value_' + i);
}
// Для больших объёмов → используй IndexedDB (асинхронный)
```

### Ограничения

- **Только строки** — ключи и значения в UTF-16
- **Размер** — значительно больше cookies (4 КБ), но зависит от браузера
- **QuotaExceededError** — при превышении квоты:

```javascript
try {
  localStorage.setItem('bigData', hugeString);
} catch (e) {
  if (e.name === 'QuotaExceededError') {
    console.log('Превышена квота хранилища');
  }
}
```

### Итерация по хранилищу

```javascript
// Перебор всех ключей
for (let i = 0; i < localStorage.length; i++) {
  const key = localStorage.key(i);
  const value = localStorage.getItem(key);
  console.log(key, ':', value);
}

// Через Object.entries (не стандартно, но работает в большинстве браузеров)
for (const [key, value] of Object.entries(localStorage)) {
  console.log(key, ':', value);
}
```

### SecurityError

При доступе к `localStorage` или `sessionStorage` может быть выброшен `SecurityError`:
- Если origin не является допустимым кортежем scheme/host/port (например, схемы `file:` или `data:`)
- Если пользователь заблокировал cookies в настройках браузера

### HTTP vs HTTPS — разные хранилища

Документ, загруженный по HTTP (`http://example.com`), возвращает объект `localStorage`, **отличный** от объекта для того же сайта по HTTPS (`https://example.com`). Это разные origin.

### sessionStorage и новые вкладки

```javascript
// В первой вкладке:
sessionStorage.setItem('step', '3');

// Открываем ту же страницу в новой вкладке:
// → sessionStorage пустой! Это новая независимая сессия
sessionStorage.getItem('step'); // null
```

Перезагрузка той же вкладки → данные сохраняются.

### sessionStorage и opener

Если страница имеет opener (открыта через `window.open()`), её `sessionStorage` изначально инициализируется как **копия** sessionStorage объекта opener. Но после этого они остаются **независимыми** — изменения в одном не влияют на другой.

### Безопасная проверка доступности Storage (Feature Detection)

```javascript
function storageAvailable(type) {
  let storage;
  try {
    storage = window[type];
    const x = "__storage_test__";
    storage.setItem(x, x);
    storage.removeItem(x);
    return true;
  } catch (e) {
    return (
      e instanceof DOMException &&
      (e.code === 22 ||
        e.code === 1014 ||
        e.name === "QuotaExceededError" ||
        e.name === "NS_ERROR_DOM_QUOTA_REACHED") &&
      storage &&
      storage.length !== 0
    );
  }
}

// Использование:
if (storageAvailable('localStorage')) {
  // Безопасно использовать localStorage
}
```

### Deep copy при извлечении объектов

При извлечении через `JSON.parse(localStorage.getItem(...))` возвращается **глубокая копия** исходного объекта. Мутации извлечённого объекта не влияют на исходный объект в памяти.

### Использование API методов вместо свойств объекта

Рекомендуется использовать методы API (`setItem`, `getItem`, `removeItem`) вместо прямого доступа к свойствам (`localStorage.key = value`), чтобы избежать конфликтов с именами встроенных методов объекта Storage.

## Сравнения и trade-offs

| | `localStorage` | `sessionStorage` | Cookies | IndexedDB |
|---|---|---|---|---|
| Lifetime | Бессрочно | Вкладка | Настраиваемо | Бессрочно |
| Размер | ~5–10 МБ | ~5–10 МБ | 4 КБ | Сотни МБ |
| Тип данных | Строки | Строки | Строки | Любые |
| Синхронность | Синхронный | Синхронный | Синхронный | Асинхронный |
| Отправка на сервер | Нет | Нет | Да (автоматически) | Нет |
| Поиск/индексы | Нет | Нет | Нет | Да |
| Доступ из JS | Да | Да | Да (без HttpOnly) | Да |

## Примеры

### Сохранение настроек пользователя

```javascript
// Сохранение темы
function setTheme(theme) {
  localStorage.setItem('theme', theme);
  document.body.className = theme;
}

// Восстановление при загрузке
const savedTheme = localStorage.getItem('theme') || 'light';
document.body.className = savedTheme;
```

### Сохранение состояния формы

```javascript
// sessionStorage для черновика формы (только в текущей вкладке)
const input = document.getElementById('search');

input.addEventListener('input', () => {
  sessionStorage.setItem('searchDraft', input.value);
});

// Восстановить при перезагрузке
input.value = sessionStorage.getItem('searchDraft') || '';
```

### Синхронизация между вкладками через storage

```javascript
// Вкладка 1: изменяет данные
localStorage.setItem('logout', Date.now().toString());

// Вкладка 2: слушает изменения
window.addEventListener('storage', (e) => {
  if (e.key === 'logout') {
    // Пользователь вышел в другой вкладке — выходим здесь тоже
    window.location.href = '/login';
  }
});
```

## Связи с другими темами

- [[Cookies]] — альтернатива; cookies отправляются на сервер, Web Storage — нет
- [[IndexedDB]] — для больших/структурированных данных; асинхронный
- [[Event Loop]] — Web Storage синхронный; блокирует main thread

## Важно / подводные камни / best practices

- **Только строки** — всегда `JSON.stringify`/`JSON.parse` для объектов
- **Синхронный API** — не используй для больших объёмов данных; предпочти IndexedDB
- **sessionStorage изолирован между вкладками** — не используй для кросс-вкладочного состояния
- **localStorage для синхронизации вкладок** — используй событие `storage`
- **Не храни чувствительные данные** (токены авторизации, пароли) — доступны любому JS на странице (XSS)
- **Оборачивай в try/catch** — QuotaExceededError при переполнении
- **Сторонние iframe** — доступ к Web Storage может быть заблокирован браузером
- **Не полагайся на localStorage в режиме инкогнито** — данные удаляются при закрытии
- **Используй `storageAvailable()` для feature detection** — простая проверка `window.localStorage` может не выявить SecurityError или нулевую квоту в приватном режиме
- **Используй методы API вместо прямого доступа к свойствам** — `setItem`/`getItem`/`removeItem` вместо `localStorage.key = value`, чтобы избежать конфликтов с именами встроенных методов

---

## Карточки для повторения

#flashcards/browser/storage

Чем localStorage отличается от sessionStorage по lifetime?::localStorage — бессрочно (не удаляется при закрытии браузера). sessionStorage — удаляется при закрытии вкладки, переживает только перезагрузку страницы.

Чем localStorage отличается от sessionStorage по scope?::localStorage — привязан к origin (протокол + домен + порт), общий для всех вкладок. sessionStorage — привязан к origin И конкретной вкладке, изолирован между вкладками.

Какие типы данных можно хранить в Web Storage?::Только строки (UTF-16). Числа конвертируются в строки автоматически. Объекты и массивы нужно сериализовать через `JSON.stringify` и десериализовать через `JSON.parse`.

Перечисли все методы API объекта Storage::setItem(key, value), getItem(key), removeItem(key), clear(), key(n), length (свойство).

Что возвращает getItem для несуществующего ключа?::null.

Что такое событие storage и где оно срабатывает?::Событие на window при изменении localStorage. Срабатывает в **других** документах с тем же origin — не на странице, которая сделала изменение.

Почему Web Storage синхронный — это проблема?::Все операции блокируют main thread. При больших объёмах данных интерфейс «замерзает». Решение — IndexedDB (асинхронный).

Что такое QuotaExceededError?::Ошибка, выбрасываемая при превышении квоты хранилища или при отключённом хранилище в настройках приватности браузера.

Что произойдёт с sessionStorage при открытии нового окна/вкладки с тем же сайтом?::Создастся новая пустая сессия. sessionStorage нового окна изолирован от предыдущего.

Отправляются ли данные localStorage на сервер автоматически?::Нет. В отличие от cookies, Web Storage не отправляется с HTTP-запросами.

Что такое SecurityError при работе с Web Storage?::Исключение при: 1) origin не является допустимым (схемы file:/data:), 2) пользователь заблокировал cookies. Препятствует доступу к localStorage/sessionStorage.

Разделяют ли HTTP и HTTPS версии сайта один localStorage?::Нет. `http://example.com` и `https://example.com` — разные origin, у каждого свой localStorage.

Что происходит с sessionStorage при window.open()?::sessionStorage нового окна инициализируется как копия sessionStorage opener-а. Но после этого они независимы — изменения не синхронизируются.

Как безопасно проверить доступность localStorage?::Функция storageAvailable() пытает записать и удалить тестовый ключ. Отлавливает QuotaExceededError и SecurityError. Простая проверка `window.localStorage` недостаточна.

---

## Источники

**Сайты / Документация:**
- MDN Web Docs: Web Storage API — localStorage, sessionStorage, Storage интерфейс, StorageEvent; жизненный цикл, scope, методы API, событие storage, ограничения
- MDN Web Docs: Using the Web Storage API — практические примеры и паттерны

---

## Теги

#конспект #javascript #браузер #localstorage #sessionstorage #web-storage #storage-event #client-side-storage
