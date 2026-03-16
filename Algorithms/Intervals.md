# Intervals

## Суть и контекст

Intervals (интервалы) — класс задач, связанных с работой с отрезками на числовой прямой. Типичные задачи: обнаружение пересечений, слияние перекрывающихся интервалов, планирование мероприятий с одним ресурсом, поиск точки максимального перекрытия. Ключевые инструменты: сортировка по времени начала/окончания, дерево интервалов, метод сканирующей прямой.

## Ключевые идеи

- Закрытый интервал `[t1, t2]` — пара вещественных чисел t1 ≤ t2
- Два интервала либо пересекаются, либо один левее другого, либо один правее — свойство трихотомии
- Сортировка по времени окончания → жадный алгоритм для задачи выбора занятий
- Дерево интервалов (на основе Red-Black) — O(log n) для вставки, удаления, поиска пересечения
- Метод сканирующей прямой (+1/-1) для нахождения максимального перекрытия

## Определения и термины

**Closed Interval (закрытый интервал)** — упорядоченная пара [t1, t2], где t1 ≤ t2. Множество точек {t ∈ R : t1 ≤ t ≤ t2}.

**low** — нижняя (левая) граница интервала.

**high** — верхняя (правая) граница интервала.

**Overlap (пересечение)** — два интервала i и i' пересекаются если `i.low ≤ i'.high` И `i'.low ≤ i.high`.

**Trichotomy Property (свойство трихотомии)** — для любых двух интервалов выполняется ровно одно из трёх: пересекаются; i полностью левее i'; i полностью правее i'.

**Interval Tree (дерево интервалов)** — дерево поиска (на основе Red-Black), расширенное атрибутом `max` для быстрого поиска пересечений.

**Activity Selection Problem** — задача выбора максимального подмножества взаимно совместимых занятий (не перекрывающихся по времени), претендующих на один ресурс.

**Sweep Line (сканирующая прямая)** — воображаемая прямая, «движущаяся» через геометрические объекты для выявления перекрытий.

**Fuzzy Sorting** — сортировка перекрывающихся интервалов с учётом левых границ для улучшения среднего времени.

## Как устроено

### Обнаружение пересечения (Overlap Detection)

Два интервала i и i' пересекаются если:
```
i.low ≤ i'.high  AND  i'.low ≤ i.high
```

Они **не** пересекаются если:
```
i.high < i'.low  (i полностью левее i')
  OR
i'.high < i.low  (i полностью правее i')
```

### Свойство трихотомии

Для любых двух интервалов всегда истинно ровно одно из трёх:
1. Пересекаются
2. i.high < i'.low (i левее i')
3. i'.high < i.low (i правее i')

### Дерево интервалов (Interval Tree)

Основано на Red-Black дереве:
- Ключ сортировки: `x.int.low` (левая граница)
- Дополнительный атрибут `x.max` = максимальное значение правой границы (`high`) среди всех интервалов в поддереве x
- `x.max` обновляется автоматически при вставке/удалении и балансировке

**INTERVAL-INSERT**: вставка + пересчёт `max` при проходе по дереву. O(log n).

**INTERVAL-DELETE**: удаление + пересчёт. O(log n).

**INTERVAL-SEARCH**: поиск интервала, пересекающегося с заданным:
1. Начать с корня
2. Если пересекается — вернуть
3. Если левое поддерево существует и `left.max ≥ query.low` → искать в левом
4. Иначе → искать в правом

### Задача выбора занятий (Activity Selection / Meeting Rooms)

**Цель**: из n занятий, претендующих на один ресурс (лекционный зал), выбрать максимальное подмножество взаимно совместимых (не перекрывающихся).

**Жадный алгоритм** (сортировка по времени завершения):
1. Отсортировать все интервалы по `finish time` в возрастающем порядке: f1 ≤ f2 ≤ ... ≤ fn
2. Выбрать первое занятие
3. На каждом шаге выбирать следующее совместимое занятие, заканчивающееся раньше всего

Жадный выбор оптимален.

**Сложность**: O(n log n) для сортировки + O(n) для итерации.

### Нахождение максимального перекрытия (Sweep Line)

Для нахождения точки наибольшего перекрытия интервалов:
1. Каждую левую границу → добавить в структуру со значением **+1**
2. Каждую правую границу → добавить со значением **−1**
3. Итерировать по отсортированным точкам, поддерживая текущую глубину перекрытия
4. Максимум глубины — точка наибольшего перекрытия

Эффективно реализуется через Red-Black дерево.

### Сортировка по времени начала

Применяется в задачах слияния интервалов и fuzzy sorting перекрывающихся интервалов. Fuzzy sorting в среднем O(n log n), при массовых перекрытиях может работать за O(n).

## Детали и нюансы

**Merge Intervals (явного алгоритма в источниках нет)**: типичный алгоритм — сортировка по start, итерация с поддержкой текущего merged интервала. Если `curr.start ≤ merged.end` → расширить `merged.end = max(merged.end, curr.end)`, иначе → добавить merged в результат и начать новый.

**Insert Interval**: вставить новый интервал и слить пересекающиеся. Сначала добавить все не пересекающиеся слева, слить пересекающиеся, добавить оставшиеся.

**Метод сканирующей прямой (Sweep Line)**: упоминается в источниках в контексте вычислительной геометрии и VLSI (базы данных схем). O(n log n) для поиска пересечений в наборе из n элементов.

## Сравнения и trade-offs

| Задача | Подход | Сложность |
|--------|--------|-----------|
| Поиск пересечения | Interval Tree | O(log n) |
| Merge Intervals | Сортировка + sweep | O(n log n) |
| Activity Selection | Жадный (sort by finish) | O(n log n) |
| Макс. перекрытие | Sweep Line (+1/-1) | O(n log n) |
| Вставка интервала | Interval Tree Insert | O(log n) |

## Примеры

```go
type Interval struct {
    Start, End int
}

// Merge Intervals — слияние перекрывающихся
func mergeIntervals(intervals []Interval) []Interval {
    if len(intervals) == 0 {
        return nil
    }
    // Сортировка по start
    sort.Slice(intervals, func(i, j int) bool {
        return intervals[i].Start < intervals[j].Start
    })
    merged := []Interval{intervals[0]}
    for _, curr := range intervals[1:] {
        last := &merged[len(merged)-1]
        if curr.Start <= last.End {
            // Пересекаются — расширить
            if curr.End > last.End {
                last.End = curr.End
            }
        } else {
            merged = append(merged, curr)
        }
    }
    return merged
}

// Обнаружение пересечения двух интервалов
func overlaps(a, b Interval) bool {
    return a.Start <= b.End && b.Start <= a.End
}

// Insert Interval — вставить и слить
func insertInterval(intervals []Interval, newInterval Interval) []Interval {
    var result []Interval
    i := 0
    n := len(intervals)
    // Добавляем все не пересекающиеся слева
    for i < n && intervals[i].End < newInterval.Start {
        result = append(result, intervals[i])
        i++
    }
    // Сливаем пересекающиеся
    for i < n && intervals[i].Start <= newInterval.End {
        if intervals[i].Start < newInterval.Start {
            newInterval.Start = intervals[i].Start
        }
        if intervals[i].End > newInterval.End {
            newInterval.End = intervals[i].End
        }
        i++
    }
    result = append(result, newInterval)
    // Добавляем оставшиеся
    result = append(result, intervals[i:]...)
    return result
}

// Activity Selection — максимальное число совместимых занятий
func activitySelection(intervals []Interval) []Interval {
    // Сортировка по времени завершения
    sort.Slice(intervals, func(i, j int) bool {
        return intervals[i].End < intervals[j].End
    })
    var selected []Interval
    lastEnd := -1 << 62
    for _, interval := range intervals {
        if interval.Start >= lastEnd {
            selected = append(selected, interval)
            lastEnd = interval.End
        }
    }
    return selected
}

// Минимальное число комнат для всех встреч (Meeting Rooms II)
// Метод сканирующей прямой: +1 при start, -1 при end
func minMeetingRooms(intervals []Interval) int {
    starts := make([]int, len(intervals))
    ends := make([]int, len(intervals))
    for i, iv := range intervals {
        starts[i] = iv.Start
        ends[i] = iv.End
    }
    sort.Ints(starts)
    sort.Ints(ends)

    rooms, endPtr := 0, 0
    for i := 0; i < len(starts); i++ {
        if starts[i] < ends[endPtr] {
            rooms++
        } else {
            endPtr++
        }
    }
    return rooms
}

// Проверка — есть ли перекрывающиеся встречи (Meeting Rooms I)
func canAttendMeetings(intervals []Interval) bool {
    sort.Slice(intervals, func(i, j int) bool {
        return intervals[i].Start < intervals[j].Start
    })
    for i := 1; i < len(intervals); i++ {
        if intervals[i].Start < intervals[i-1].End {
            return false
        }
    }
    return true
}
```

## Связи с другими темами

- [[Heap]] — min-heap используется для задачи Meeting Rooms II (отслеживание окончания встреч)
- [[Sliding Window]] — концептуально похожи: отслеживание активного диапазона
- [[Big-O Notation]] — сортировка как основа большинства interval алгоритмов O(n log n)
- [[Graphs]] — дерево интервалов строится на Red-Black Tree

## Важно / подводные камни / best practices

- Для Activity Selection сортируй по **finish time** (не start time) — жадный выбор оптимален
- Для Merge Intervals сортируй по **start time**
- При проверке пересечения: интервалы НЕ пересекаются если `a.End < b.Start` или `b.End < a.Start` — инвертируй для проверки пересечения
- Meeting Rooms II — классически через min-heap или сортировку starts+ends отдельно

---

## Карточки для повторения

#flashcards/algorithms/intervals

Условие пересечения двух интервалов?::i.low ≤ i'.high AND i'.low ≤ i.high. НЕ пересекаются: i.high < i'.low (i левее) OR i'.high < i.low (i правее).

Свойство трихотомии интервалов?::Для любых двух интервалов истинно ровно одно: 1) пересекаются; 2) i полностью левее i'; 3) i полностью правее i'.

Алгоритм Activity Selection (Meeting Rooms)?::Сортировать по finish time. Жадно выбирать следующее совместимое занятие, заканчивающееся раньше всего. O(n log n).

Алгоритм Merge Intervals?::Сортировать по start. Итерировать: если curr.start ≤ last.end → расширить last.end = max(last.end, curr.end). Иначе → добавить новый.

Как найти точку максимального перекрытия (Sweep Line)?::Левая граница → +1, правая → -1. Сортировать точки, накапливать сумму. Максимум суммы = максимальное перекрытие.

Что такое Interval Tree?::Red-Black Tree, где ключ = левая граница. Доп. атрибут `max` = максимальная правая граница в поддереве. Поиск пересечения за O(log n).

Сложность Interval Tree операций?::Insert, Delete, Search — все O(log n).

---

## Источники

**Книга:**
- Название: Grokking Algorithms
- Автор: Aditya Bhargava (Адитья Бхаргава)
- Год: 2016 (оригинал, Manning Publications Co.) / 2017 (русское издание, «Питер»)
- ISBN: 978-1-617292231 (EN) / 978-5-496-02541-6 (RU)
- Переводчик (RU): Е. Матвеев

**Книга:**
- Название: Cracking the Coding Interview (6th Edition)
- Автор: Gayle Laakmann McDowell
- Год: 2015 (copyright), 2016 (компиляция)
- Издатель: CareerCup, LLC (Palo Alto, CA)
- ISBN: 978-0-9847828-5-7

**Книга:**
- Название: Introduction to Algorithms (3rd Edition) — CLRS
- Авторы: Thomas H. Cormen, Charles E. Leiserson, Ronald L. Rivest, Clifford Stein
- Год: 2009
- Издатель: The MIT Press (Cambridge, Massachusetts / London, England)
- ISBN: 978-0-262-03384-8 (hardcover) / 978-0-262-53305-8 (paperback)

---

## Теги

#конспект #algorithms #intervals #sweep-line #interval-tree #greedy #interview-prep
