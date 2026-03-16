# Cookies

## Суть и контекст

**HTTP Cookie** — небольшой фрагмент данных, который сервер отправляет браузеру через заголовок `Set-Cookie`. Браузер сохраняет его и автоматически прикрепляет к каждому последующему запросу на тот же домен через заголовок `Cookie`. Основной механизм сохранения состояния в stateless HTTP-протоколе.

## Ключевые идеи

- Cookie создаётся сервером через заголовок `Set-Cookie`; браузер отправляет его через заголовок `Cookie`
- **Session cookie** — нет `Expires`/`Max-Age`, удаляется при закрытии браузера
- **Persistent cookie** — есть `Expires` или `Max-Age`, живёт до указанной даты
- `HttpOnly` — недоступен из JS; защита от XSS
- `Secure` — передаётся только по HTTPS
- `SameSite` — защита от CSRF: `Strict`, `Lax` (по умолчанию), `None`
- Размер ≤ 4 КБ; количество на домен — ограничено (~30–50)
- `document.cookie` — чтение возвращает **все** куки строкой; запись — добавляет/обновляет **одну**
- Third-party cookies используются для трекинга, современные браузеры блокируют их по умолчанию

## Определения и термины

**Cookie** — пара «имя=значение» с набором атрибутов, хранящаяся в браузере и отправляемая на сервер с каждым HTTP-запросом.

**Session cookie** — cookie без атрибутов `Expires` и `Max-Age`. Удаляется при закрытии браузера.

**Persistent cookie** — cookie с `Expires` или `Max-Age`. Живёт до указанной даты/времени.

**Set-Cookie** — HTTP-заголовок ответа сервера, создающий cookie в браузере.

**Cookie** — HTTP-заголовок запроса браузера, передающий все подходящие cookies серверу.

**`Expires`** — атрибут: дата истечения срока действия (формат GMT/UTC).

**`Max-Age`** — атрибут: срок жизни в секундах. Приоритетнее `Expires` при конфликте.

**`Domain`** — атрибут: домен, на который отправляется cookie. Если указан — включает все поддомены. Если не указан — только точный хост без поддоменов.

**`Path`** — атрибут: URL-путь, при котором cookie отправляется.

**`Secure`** — атрибут: cookie передаётся только по HTTPS (кроме localhost).

**`HttpOnly`** — атрибут: запрещает доступ к cookie из JavaScript (`document.cookie`). Защита от XSS.

**`SameSite`** — атрибут: управляет отправкой cookie при кросс-доменных запросах. Защита от CSRF.

**Third-party cookie** — cookie, установленная доменом, отличным от посещаемого сайта (обычно рекламные сети).

**CSRF (Cross-Site Request Forgery)** — атака: вредоносный сайт заставляет браузер авторизованного пользователя отправить нежелательный запрос на целевой сайт.

**`__Secure-` prefix** — cookie принимается только если установлена с `Secure` по HTTPS.

**`__Host-` prefix** — cookie должна иметь `Secure`, не иметь `Domain`, иметь `Path=/`. Жёсткая привязка к хосту.

## Как устроено

### Создание и отправка cookie

**Сервер создаёт** через `Set-Cookie` в ответе:
```http
HTTP/2.0 200 OK
Content-Type: text/html
Set-Cookie: yummy_cookie=choco
Set-Cookie: tasty_cookie=strawberry; Expires=Wed, 09 Jun 2030 10:18:14 GMT; Secure; HttpOnly
```

**Браузер отправляет** при каждом запросе через `Cookie`:
```http
GET /page HTTP/2.0
Host: www.example.org
Cookie: yummy_cookie=choco; tasty_cookie=strawberry
```

### Атрибуты заголовка Set-Cookie

```http
Set-Cookie: name=value; Expires=date; Max-Age=seconds; Domain=domain; Path=path; Secure; HttpOnly; SameSite=Strict|Lax|None
```

#### Expires и Max-Age

```http
Set-Cookie: session_id=abc123; Expires=Wed, 09 Jun 2030 10:18:14 GMT
Set-Cookie: session_id=abc123; Max-Age=3600
```

- **Session cookie** (нет ни `Expires`, ни `Max-Age`) — удаляется при закрытии браузера
- Если оба указаны — **`Max-Age` имеет приоритет**

#### Domain

```http
Set-Cookie: name=value; Domain=site.com
```

- Указан `Domain=site.com` → cookie доступна на `site.com` и всех поддоменах (`forum.site.com`, `api.site.com`)
- Не указан → только точный хост (`site.com`), **без поддоменов**

#### Path

```http
Set-Cookie: name=value; Path=/docs
```

- Cookie отправляется для `/docs`, `/docs/Web/`, `/docs/anything` и т.д.
- Не отправляется для `/` или `/other`

#### Secure

```http
Set-Cookie: name=value; Secure
```

- Cookie передаётся только по **HTTPS** (кроме `localhost`)

#### HttpOnly

```http
Set-Cookie: session_id=secret; HttpOnly
```

- Недоступна через `document.cookie` в JavaScript
- Защита от **XSS**: даже при внедрении скрипта — cookie не украсть

#### SameSite

```http
Set-Cookie: session_id=abc; SameSite=Strict
Set-Cookie: session_id=abc; SameSite=Lax
Set-Cookie: session_id=abc; SameSite=None; Secure
```

| Значение | Поведение |
|----------|-----------|
| `Strict` | Только same-site запросы. При переходе по ссылке с другого сайта — cookie не отправляется |
| `Lax` | Same-site + навигация верхнего уровня (клик по ссылке). Блокирует кросс-доменные POST, img, iframe. **Значение по умолчанию** в современных браузерах |
| `None` | Все запросы (same-site и cross-site). Обязателен атрибут `Secure` |

### Cookie prefixes

```http
Set-Cookie: __Secure-id=abc; Secure; HttpOnly
Set-Cookie: __Host-id=abc; Secure; HttpOnly; Path=/
```

- `__Secure-` — принимается только с `Secure` по HTTPS
- `__Host-` — принимается только с `Secure` + без `Domain` + `Path=/`

### CSRF и защита SameSite

**Схема CSRF-атаки:**

```
1. Пользователь авторизован на bank.com (есть cookie сессии)
2. Пользователь заходит на evilhacker.com
3. evilhacker.com отправляет скрытый POST-запрос на bank.com/transfer
4. Браузер автоматически прикрепляет cookie bank.com к запросу
5. bank.com видит валидную сессию → выполняет перевод
```

**Защита:**
- `SameSite=Lax` / `Strict` — браузер не отправит cookie в кросс-доменном POST
- **CSRF-токен** — сервер добавляет уникальный секрет в форму; при отправке проверяет

### Third-party cookies

```
Пользователь на site.com
  → страница загружает img с ads.com
  → ads.com ставит свою cookie
  → при посещении blog.com (тоже с img от ads.com)
  → ads.com видит ту же cookie
  → строит профиль пользователя
```

Современные браузеры (Safari ITP, Firefox ETP) по умолчанию блокируют third-party cookies.

### Работа с `document.cookie` в JS

**Чтение** — возвращает **все** куки строкой:
```javascript
document.cookie; // 'name=Vasya; age=25; theme=dark'
// Нет прямого способа получить одну куку — нужно парсить строку
```

**Функция получения куки:**
```javascript
function getCookie(name) {
  var matches = document.cookie.match(new RegExp(
    "(?:^|; )" + name.replace(/([\.$?*|{}\(\)\[\]\\\/\+^])/g, '\\$1') + "=([^;]*)"
  ));
  return matches ? decodeURIComponent(matches[1]) : undefined;
}

getCookie('name'); // 'Vasya'
```

**Запись** — добавляет/обновляет **одну** куку (не перезаписывает все!):
```javascript
document.cookie = "userName=Vasya"; // добавляет одну куку
```

**Функция установки куки со всеми параметрами:**
```javascript
function setCookie(name, value, options) {
  options = options || {};
  var expires = options.expires;

  if (typeof expires == "number" && expires) {
    var d = new Date();
    d.setTime(d.getTime() + expires * 1000);
    expires = options.expires = d;
  }
  if (expires && expires.toUTCString) {
    options.expires = expires.toUTCString();
  }

  value = encodeURIComponent(value);
  var updatedCookie = name + "=" + value;

  for (var propName in options) {
    updatedCookie += "; " + propName;
    var propValue = options[propName];
    if (propValue !== true) {
      updatedCookie += "=" + propValue;
    }
  }

  document.cookie = updatedCookie;
}

// Использование:
setCookie("name", "Vasya", { expires: 3600, path: "/", secure: true });
```

**Удаление куки** — установить `Expires` в прошлое:
```javascript
function deleteCookie(name) {
  setCookie(name, "", { expires: -1 });
}
// При удалении: значение не важно, важны совпадение имени, пути и домена
```

## Детали и нюансы

### Ограничения

| Параметр | Ограничение |
|----------|------------|
| Размер (имя + значение) | ≤ 4 КБ |
| Количество на домен | ~30–50 (зависит от браузера) |
| Символы | Спецсимволы нужно кодировать через `encodeURIComponent` |

### document.cookie vs HttpOnly cookies

- `HttpOnly` cookies **невидимы** через `document.cookie`
- Браузер всё равно отправляет их с запросами
- Именно поэтому session cookies делают HttpOnly — JS не может их прочитать

### Нюанс чтения document.cookie

```javascript
// Чтение возвращает ВСЕ cookies одной строкой
document.cookie; // 'a=1; b=2; c=3'

// Нет метода getItem — только строковый парсинг
// Нет метода для получения Expires/Path/Domain существующей куки
```

## Сравнения и trade-offs

| | Cookies | localStorage | sessionStorage |
|---|---|---|---|
| Создаётся | Сервер (Set-Cookie) или JS | JS | JS |
| Отправляется на сервер | Да (автоматически) | Нет | Нет |
| Размер | 4 КБ | ~5–10 МБ | ~5–10 МБ |
| Lifetime | Настраиваемо | Бессрочно | До закрытия вкладки |
| Доступ из JS | Да (без HttpOnly) | Да | Да |
| Защита от XSS | HttpOnly | Нет | Нет |
| Кросс-вкладочный scope | Да | Да | Нет |

## Примеры

### Типичная session cookie (авторизация)

```http
HTTP/2.0 200 OK
Set-Cookie: session_id=abc123; HttpOnly; Secure; SameSite=Lax; Path=/
```

- `HttpOnly` — JS не украдёт при XSS
- `Secure` — только HTTPS
- `SameSite=Lax` — защита от CSRF
- Нет `Expires`/`Max-Age` → session cookie, удалится при закрытии браузера

### Постоянная cookie (remember me)

```http
Set-Cookie: remember_token=xyz789; Max-Age=2592000; HttpOnly; Secure; SameSite=Lax; Path=/
```

- `Max-Age=2592000` — 30 дней (30 × 24 × 60 × 60)

### CORS cookie (third-party запрос)

```http
Set-Cookie: tracking=id123; SameSite=None; Secure
```

- `SameSite=None` — отправляется в кросс-доменных запросах
- **Обязателен** `Secure` при `SameSite=None`

## Связи с другими темами

- [[localStorage и sessionStorage]] — альтернатива без отправки на сервер; большие лимиты
- [[IndexedDB]] — для больших структурированных данных
- HTTP заголовки — `Set-Cookie`, `Cookie`, `SameSite`, CORS

## Важно / подводные камни / best practices

- **Всегда `HttpOnly`** для session cookies — защита от XSS-кражи
- **Всегда `Secure`** для production — только HTTPS
- **`SameSite=Lax`** минимум — защита от CSRF (является дефолтом в Chrome)
- **`SameSite=None` требует `Secure`** — иначе браузер отклонит
- **Не храни чувствительное в значении** — cookie видна пользователю; храни только токен/идентификатор
- **`encodeURIComponent`** при записи через `document.cookie` — спецсимволы ломают формат
- **Удаление** = установка `Expires` в прошлое; совпадение `Domain` и `Path` обязательно
- **Нет метода getItem** — парсинг `document.cookie` вручную или через библиотеку (js-cookie)
- **Third-party cookies умирают** — не строй архитектуру на них

---

## Карточки для повторения

#flashcards

Как сервер создаёт cookie?::Через заголовок `Set-Cookie` в HTTP-ответе. В одном ответе может быть несколько таких заголовков.

Как браузер отправляет cookies?::Через заголовок `Cookie` при каждом HTTP-запросе к домену, которому принадлежат cookies.

Чем Session cookie отличается от Persistent cookie?::Session cookie — нет `Expires`/`Max-Age`, удаляется при закрытии браузера. Persistent cookie — есть `Expires` или `Max-Age`, живёт до указанного срока.

Что делает атрибут `HttpOnly`?::Запрещает доступ к cookie из JavaScript (через `document.cookie`). Защита от XSS — скрипт не может украсть куку.

Что делает атрибут `Secure`?::Cookie передаётся только по зашифрованному протоколу HTTPS (кроме localhost).

Что делает `SameSite=Strict`?::Cookie отправляется только при same-site запросах. При переходе по ссылке с другого сайта — не отправляется.

Что делает `SameSite=Lax`?::Cookie отправляется при same-site запросах + при навигации верхнего уровня (клик по ссылке). Блокирует кросс-доменные POST, img, iframe. Значение по умолчанию в современных браузерах.

Что делает `SameSite=None` и что обязательно при его использовании?::Cookie отправляется при любых запросах (same-site и cross-site). Обязателен атрибут `Secure`.

Что такое CSRF-атака?::Вредоносный сайт заставляет браузер авторизованного пользователя отправить нежелательный запрос на целевой сайт. Браузер автоматически прикрепляет cookies, и сервер воспринимает запрос как легитимный.

Если указаны и `Expires`, и `Max-Age` — что имеет приоритет?::`Max-Age` имеет приоритет над `Expires`.

Что возвращает `document.cookie`?::Строку со всеми доступными (не HttpOnly) cookies вида `name1=value1; name2=value2`. Нет встроенного метода для получения одной куки — нужно парсить вручную.

Как записать cookie через `document.cookie`?::Присвоением строки: `document.cookie = "name=value; path=/"`. Это **не** перезаписывает все cookies — только добавляет/обновляет одну.

Как удалить cookie через JS?::Установить ей `Expires` в прошлое: `document.cookie = "name=; expires=Thu, 01 Jan 1970 00:00:00 GMT"`. Имя, Domain и Path должны совпадать.

Каков максимальный размер cookie?::4 КБ (имя + значение).

Что такое `__Host-` prefix?::Cookie с этим префиксом принимается только если: установлена с `Secure`, не имеет атрибута `Domain`, имеет `Path=/`. Гарантирует жёсткую привязку к хосту.

Что такое Third-party cookies?::Cookies, установленные доменом, отличным от посещаемого сайта (например, рекламные сети). Используются для кросс-сайтового трекинга. Блокируются современными браузерами.

---

## Источники

**Сайты / Документация:**
- MDN Web Docs: HTTP cookies — создание, атрибуты (Expires, Max-Age, Domain, Path, Secure, HttpOnly, SameSite), session vs persistent, CSRF, third-party cookies
- MDN Web Docs: Заголовки Set-Cookie и Cookie (HTTP-справочник)
- javascript.info: Куки, document.cookie — функции getCookie/setCookie/deleteCookie, SameSite, cookie prefixes

---

## Теги

#конспект #javascript #браузер #cookies #http #set-cookie #samesite #httponly #secure #csrf #third-party-cookies #client-side-storage
