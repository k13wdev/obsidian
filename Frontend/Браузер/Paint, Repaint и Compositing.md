# Paint, Repaint и Compositing

## Суть и контекст

**Paint (Отрисовка)** и **Compositing (Композитинг)** — финальные шаги конвейера рендеринга браузера. На этапе Paint геометрические блоки превращаются в пиксели. На этапе Compositing пиксели разных слоёв объединяются в итоговое изображение на экране с помощью GPU.

Это пятый этап [[Critical Rendering Path|критического пути рендеринга]], следующий после [[Layout (Reflow)]].

## Ключевые идеи

- **Paint** — преобразование геометрических узлов в фактические пиксели (текст, цвета, границы, тени)
- Порядок отрисовки определяется **stacking contexts** — нельзя рисовать элементы в произвольном порядке из-за `z-index`
- **Reflow всегда триггерит Repaint**; Repaint без Reflow — возможен (при изменении только визуальных свойств)
- Браузеры используют **слои (layers)** и **compositing** — части страницы растеризуются отдельно и объединяются GPU
- Compositing происходит **вне основного потока** — в compositor thread, что делает его самым эффективным вариантом
- Только **`transform`** и **`opacity`** анимируются без Layout и Paint (только через Compositing)
- **`will-change`** сигнализирует браузеру создать отдельный слой заранее
- **Layer Explosion** — слишком много слоёв = нехватка VRAM + падение производительности

## Определения и термины

**Paint (Repaint / Отрисовка)** — процесс преобразования геометрических данных (из Layout Tree) в пиксели: рисование текста, цветов, границ, теней, замещаемых элементов.

**Paint Record (Запись рисования)** — инструкция для отрисовки, например: «сначала нарисуй фон, затем текст, затем прямоугольник». Основной поток создаёт список таких записей при обходе дерева макета.

**Stacking Context (Контекст наложения)** — локальный стек элементов, гарантирующий правильное перекрытие по оси Z. Создаётся элементами с `z-index`, `opacity < 1`, `transform`, `position: fixed` и др.

**Compositing (Композитинг)** — техника разделения страницы на отдельные слои, которые растеризуются по отдельности и объединяются в финальное изображение в compositor thread (вне main thread).

**Compositor Thread (Поток компоновщика)** — отдельный поток браузера, выполняющий compositing. Не зависит от работы JS в main thread.

**Layer Tree (Дерево слоёв)** — структура, создаваемая main thread при обходе Layout Tree. Определяет, какие элементы выносятся на отдельные compositing layers.

**Compositing Layer (Слой компоновщика)** — отдельный слой, растеризуемый и загружаемый в GPU независимо от остальной страницы.

**Raster Thread (Растровый поток)** — поток, превращающий тайлы слоя в пиксели и сохраняющий битмапы в GPU-памяти.

**Tile (Тайл)** — небольшой фрагмент слоя. Compositor thread разбивает большие слои на тайлы и отправляет их в raster threads.

**Compositor Frame (Кадр компоновщика)** — набор quad (четырёхугольников рисования) с информацией о тайлах. Compositor thread собирает этот кадр и передаёт его в GPU для вывода на экран.

**will-change** — CSS-свойство, сигнализирующее браузеру создать для элемента отдельный compositing layer заранее.

**Layer Explosion (Взрыв слоёв)** — ситуация, когда на странице создаётся слишком много compositing layers, исчерпывающих VRAM и вызывающих деградацию производительности.

**Composite-only Properties** — CSS-свойства, изменение которых не требует Layout и Paint, а обрабатывается только на уровне Compositing: `transform`, `opacity`.

## Как устроено

### Этап Paint: создание записей рисования

1. Основной поток **обходит Layout Tree**
2. Создаёт **paint records** (инструкции рисования)
3. Порядок определяется по правилам **stacking context**

Стандартный порядок отрисовки элементов внутри stacking context (от заднего к переднему):
1. Цвет фона (background color)
2. Фоновое изображение (background image)
3. Границы (border)
4. Дочерние элементы (children)
5. Контур (outline)

Элементы с `z-index` образуют **локальный стек (stacking context)** — они гарантированно правильно перекрывают друг друга по оси Z.

### Repaint vs Reflow

**Reflow → всегда вызывает Paint**:
Изменение геометрических свойств (`width`, `height`, `left`, `top`) → браузер заново вычисляет геометрию → перерисовывает пиксели.

**Repaint без Reflow**:
Изменение чисто визуальных свойств (`background-color`, `color`, `box-shadow`) → браузер пропускает Layout и сразу запускает только Paint.

Стоимость Paint не одинакова:
- `background: red` — дёшево
- `box-shadow: 0 0 20px rgba(0,0,0,0.5)` — дорого (размытие)

Paint часто является **самым тяжёлым этапом** конвейера — его следует по возможности избегать.

### GPU Compositing: полный конвейер

```
Main Thread
    │
    ├─ Обход Layout Tree → Layer Tree
    ├─ Определение compositing layers
    │
    └─ Передача Layer Tree → Compositor Thread
                                   │
                          ┌────────┴────────┐
                    Разбивка слоёв на тайлы  │
                          │                  │
                    Raster Threads           │
                    (тайлы → битмапы → GPU)  │
                          │                  │
                    Compositor собирает      │
                    Compositor Frame         │
                    (quads с тайлами)        │
                          │                  │
                    Передача в Browser Process
                          │
                    GPU → экран
```

**Ключевое преимущество**: Compositor Thread работает **off-main-thread**. При прокрутке страницы, если слои уже растеризованы в GPU, compositor просто пересчитывает координаты — JS при этом не нужен.

### Какие элементы автоматически получают compositing layer

- `<video>`, `<canvas>`
- Элементы с CSS `opacity`
- Элементы с 3D-трансформациями (`transform: translateZ()`, `transform: rotateY()`)
- Элементы с `will-change`
- Элементы с CSS-анимациями `transform` или `opacity`

### Composite-only Properties

Самая эффективная анимация — та, которая избегает и Layout, и Paint, работая только через Compositing.

На сегодняшний день это **только два CSS-свойства**:
- **`transform`** (translate, scale, rotate)
- **`opacity`**

Если анимировать эти свойства у элемента на собственном compositing layer → вся работа перекладывается на GPU. Main thread остаётся свободным — анимация плавная, даже если JS выполняет тяжёлые вычисления.

### will-change

```css
/* Сигнал браузеру: создай отдельный слой для этого элемента */
.my-moving-element {
  will-change: transform;
}
```

Старый хак для браузеров без поддержки `will-change`:
```css
.my-moving-element {
  transform: translateZ(0); /* принудительно создаёт compositing layer */
}
```

## Детали и нюансы

### Layer Explosion (Взрыв слоёв)

Кажется соблазнительным применить `will-change: transform` ко всем элементам. Это **категорически нельзя**:

- Каждый слой требует **оперативной памяти** и накладных расходов на управление
- Текстуры каждого слоя загружаются в **VRAM** (память видеокарты)
- При избытке слоёв пропускная способность CPU↔GPU и объём VRAM исчерпываются
- На слабых **мобильных устройствах** это вызывает катастрофическое падение производительности

Правило: выносить элементы на новые слои только при реальной необходимости и всегда проверять результат через DevTools.

## Сравнения и trade-offs

| Изменение | Layout | Paint | Composite |
|-----------|--------|-------|-----------|
| `width`, `height` | ✅ | ✅ | ✅ |
| `background-color`, `color` | ❌ | ✅ | ✅ |
| `box-shadow`, `border-radius` | ❌ | ✅ | ✅ |
| `transform` (на своём слое) | ❌ | ❌ | ✅ |
| `opacity` (на своём слое) | ❌ | ❌ | ✅ |

| | Main Thread | Compositor Thread |
|---|---|---|
| Layout | ✅ | ❌ |
| Paint | ✅ | ❌ |
| Compositing | ❌ | ✅ |
| Зависит от JS | Да | Нет |
| Блокируется JS | Да | Нет |

## Примеры

### Пример: стоимость разных CSS-свойств

```css
/* Дёшево: только Composite */
.fast { transform: translateX(100px); }

/* Дороже: Repaint без Reflow */
.medium { background-color: red; }

/* Дорого: Reflow + Repaint */
.slow { width: 200px; }

/* Очень дорого в Paint: размытые тени */
.expensive-paint { box-shadow: 0 0 30px rgba(0,0,0,0.8); }
```

### Пример: правильное использование will-change

```css
/* Применять только к элементам, которые реально будут анимироваться */
.animated-button {
  will-change: transform;
  transition: transform 0.3s ease;
}

.animated-button:hover {
  transform: scale(1.05);
}
```

### Пример: порядок отрисовки в stacking context

```html
<!-- z-index создаёт новый stacking context -->
<div style="position: relative; z-index: 1">
  <!-- Порядок отрисовки: background → border → children → outline -->
</div>
<div style="position: relative; z-index: 2">
  <!-- Отрисуется поверх первого div -->
</div>
```

## Связи с другими темами

- [[Layout (Reflow)]] — предшествует Paint; Reflow всегда вызывает Repaint
- [[Critical Rendering Path]] — Paint и Compositing — финальные этапы CRP
- [[Event Loop]] — Compositor Thread работает независимо от main thread и event loop
- [[Парсинг CSS (CSSOM)]] — stacking contexts определяются CSS-свойствами

## Важно / подводные камни / best practices

- **Анимируй только `transform` и `opacity`** — они не триггерят Layout и Paint
- **`will-change` только по необходимости** — не применяй глобально, это вызывает Layer Explosion
- **Проверяй слои в DevTools**: Chrome → Rendering → Layer borders / Paint flashing
- **`visibility: hidden` vs `display: none`**: `visibility: hidden` вызывает только Repaint; `display: none` — Reflow
- **Сложные CSS-эффекты дорогие**: `box-shadow`, `filter: blur()`, `border-radius` на больших элементах — замедляют Paint
- **Скролл бесплатен** если страница на compositing layers — compositor thread пересчитывает координаты без main thread
- **Compositor Thread не блокируется JS** — анимации на GPU плавные, даже если JS выполняет тяжёлые операции

---

## Карточки для повторения

#flashcards

Что такое Paint в браузере?::Процесс преобразования геометрических данных (из Layout Tree) в фактические пиксели: рисование текста, цветов, границ, теней, замещаемых элементов.

Что такое Paint Record?::Инструкция для отрисовки, например: «нарисуй фон, затем текст, затем прямоугольник». Основной поток создаёт список таких записей при обходе Layout Tree.

Всегда ли Reflow вызывает Repaint?::Да. Изменение геометрии означает, что пиксели тоже нужно перерисовать — Reflow всегда триггерит Paint.

Может ли Repaint произойти без Reflow?::Да. Изменение чисто визуальных свойств (background-color, color, box-shadow) пропускает Layout и сразу запускает только Paint.

Что такое Compositing?::Техника разделения страницы на отдельные слои, которые растеризуются по отдельности и объединяются в финальное изображение в compositor thread с помощью GPU.

Что такое Compositor Thread?::Отдельный поток браузера, выполняющий compositing (объединение слоёв). Работает вне main thread и не блокируется JavaScript.

Какие два CSS-свойства анимируются без Layout и Paint (только Compositing)?::transform и opacity.

Почему анимация transform/opacity плавная даже при нагруженном JS?::Они обрабатываются в Compositor Thread, который работает вне main thread и не зависит от выполнения JavaScript.

Что такое will-change?::CSS-свойство, сигнализирующее браузеру создать для элемента отдельный compositing layer заранее, до начала анимации.

Что такое Layer Explosion?::Ситуация, когда создаётся слишком много compositing layers. Каждый слой занимает VRAM и требует ресурсов на управление. На мобильных устройствах приводит к катастрофическому падению производительности.

Перечисли стандартный порядок отрисовки элементов в stacking context::1. Цвет фона. 2. Фоновое изображение. 3. Границы. 4. Дочерние элементы. 5. Контур.

Что такое Compositor Frame?::Набор quad (четырёхугольников рисования) с информацией о тайлах. Compositor thread собирает этот кадр и передаёт его в GPU для вывода на экран.

Что такое Tile (тайл) в контексте рендеринга?::Небольшой фрагмент слоя. Compositor thread разбивает большие слои на тайлы и отправляет их в raster threads для растеризации.

Изменение какого CSS-свойства триггерит Layout, Paint И Composite?::Геометрические свойства: width, height, left, top, margin, padding и т.д.

Изменение какого CSS-свойства триггерит только Paint и Composite (без Layout)?::Визуальные свойства: background-color, color, box-shadow, border-radius (без изменения размеров) и т.д.

---

## Источники

**Ноутбук (NotebookLM):**
- Название: «Работа браузера»
- Платформа: NotebookLM
- Охваченные темы: paint pipeline, stacking contexts, порядок отрисовки (WebKit/Blink); compositing — layer tree, compositor thread, raster threads, GPU; composite-only properties (transform, opacity); will-change и layer explosion

---

## Теги

#конспект #frontend #браузер #paint #repaint #compositing #layers #gpu #will-change #transform #opacity #stacking-context #performance #critical-rendering-path
