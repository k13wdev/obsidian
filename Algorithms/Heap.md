# Heap

## Суть и контекст

Heap (куча) — бинарное дерево с heap property: для каждого узла ключ родителя ≤ (min-heap) или ≥ (max-heap) ключей его детей. Хранится компактно в массиве без указателей. Является основой Priority Queue (очереди с приоритетом) и Heap Sort. Особенность: O(1) доступ к минимуму/максимуму, O(log n) вставка и извлечение.

## Ключевые идеи

- Heap — почти полное бинарное дерево: заполнено на всех уровнях кроме последнего, который заполняется слева направо
- Min-heap: минимум всегда в корне. Max-heap: максимум всегда в корне
- Элегантное хранение в массиве через арифметику индексов (без указателей)
- Build Heap из неупорядоченного массива — O(n), несмотря на наивную оценку O(n log n)
- Heap Sort — O(n log n) in-place, не требует дополнительного массива
- Heap — основа для Top-K задач, медианы стрима, планировщиков задач

## Определения и термины

**Min-Heap** — ключ каждого узла ≤ ключей его детей. Минимальный элемент всегда в корне.

**Max-Heap** — ключ каждого узла ≥ ключей его детей. Максимальный элемент всегда в корне.

**Heap Property** — структурный инвариант: родитель ≤ (min) или ≥ (max) каждого ребёнка.

**Nearly complete binary tree** — полное бинарное дерево на всех уровнях кроме, возможно, последнего, который заполняется слева направо.

**Heapify (просеивание вниз)** — подпрограмма восстановления heap property сверху вниз: сравнить узел с детьми, поменять с наименьшим/наибольшим, рекурсивно продолжить.

**Bubble Up (просеивание вверх)** — восстановление heap property снизу вверх: сравнивать узел с родителем, менять если нарушено.

**Priority Queue** — абстрактная структура данных, реализуемая через heap. Предоставляет быстрый доступ к элементу с наивысшим приоритетом.

**Fibonacci Heap** — продвинутая реализация heap с O(1) амортизированным DECREASE-KEY (против O(log n) у binary heap).

## Как устроено

### Хранение в массиве

Благодаря структуре почти полного дерева, heap хранится в массиве без указателей. При корне в индексе 1:

| Отношение | Формула |
|-----------|---------|
| Родитель узла i | ⌊i/2⌋ |
| Левый ребёнок узла i | 2i |
| Правый ребёнок узла i | 2i + 1 |

При корне в индексе 0: parent = (i-1)/2, left = 2i+1, right = 2i+2.

### Операция Insert — O(log n)

1. Добавить новый элемент в конец массива (крайняя правая позиция на нижнем уровне)
2. **Bubble Up**: сравнить с родителем, менять местами, пока heap property восстановлено

### Операция Extract-Min/Max — O(log n)

1. Корень (минимум/максимум) — результат
2. Заменить корень последним элементом массива
3. **Heapify вниз**: сравнить с наименьшим ребёнком, поменять, рекурсивно продолжить

### Build Heap — O(n)

Конвертировать неупорядоченный массив в heap:
1. Начать с последнего не-листа (индекс ⌊n/2⌋)
2. Применить Heapify на каждом узле, двигаясь к корню
3. Итого: O(n) — большинство узлов в нижних уровнях имеют высоту 0-1, работа минимальна

**Почему O(n), а не O(n log n)**: Heapify занимает O(высота узла). Большинство узлов находятся у основания с высотой ~0. Точный анализ даёт ∑ работ = O(n).

### Heap Sort

1. BUILD-MAX-HEAP → максимум в корне
2. Swap корня с последним элементом → максимум на финальной позиции
3. Уменьшить логический размер heap на 1
4. MAX-HEAPIFY на новом корне
5. Повторять до heap size == 1

**Преимущество над Merge Sort**: сортировка in-place, не требует дополнительного массива. Гарантированный worst-case O(n log n).

## Детали и нюансы

**Планировщик задач**: max-priority queue для отслеживания и выбора задач по приоритету. `EXTRACT-MAX` → следующая задача.

**Event-Driven Simulation**: min-priority queue с будущими событиями, ключ — время. `EXTRACT-MIN` → следующее событие.

**Continuous Median (медиана стрима)**:
- Max-heap для нижней половины чисел (корень = наибольший из нижних)
- Min-heap для верхней половины (корень = наименьший из верхних)
- Медиана — корень(и) heap-ов; поддерживается баланс между ними

**Fibonacci Heap**: DECREASE-KEY амортизированно O(1) вместо O(log n) → теоретическое улучшение Dijkstra до O(V log V + E).

## Сравнения и trade-offs

| Операция | Binary Heap | Fibonacci Heap |
|----------|-------------|----------------|
| Get Min/Max | O(1) | O(1) |
| Insert | O(log n) | O(1) амортизировано |
| Extract Min/Max | O(log n) | O(log n) амортизировано |
| Decrease Key | O(log n) | O(1) амортизировано |
| Build Heap | O(n) | O(n) |

| | Heap Sort | Merge Sort | Quick Sort |
|--|-----------|------------|------------|
| Worst case | O(n log n) | O(n log n) | O(n²) |
| In-place | Да | Нет | Да |
| Cache friendly | Нет | Нет | Да |

## Применения

**Top-K наименьших элементов**: поддерживать max-heap размером K. При обходе массива: если элемент < корня → insert + extract-max. В конце heap содержит K наименьших. O(n log K).

**Top-K наибольших элементов**: аналогично с min-heap размером K.

**K-я наибольшая в стриме**: max-heap размером K. Добавляем элементы, при переполнении — extract-min. Корень — K-й наибольший.

**Непрерывная медиана**: два heap-а (max для нижней половины, min для верхней).

## Примеры

```go
import "container/heap"

// Min-Heap через container/heap
type MinHeap []int

func (h MinHeap) Len() int           { return len(h) }
func (h MinHeap) Less(i, j int) bool { return h[i] < h[j] }
func (h MinHeap) Swap(i, j int)      { h[i], h[j] = h[j], h[i] }

func (h *MinHeap) Push(x any) {
    *h = append(*h, x.(int))
}

func (h *MinHeap) Pop() any {
    old := *h
    n := len(old)
    x := old[n-1]
    *h = old[:n-1]
    return x
}

// Использование
func heapExample() {
    h := &MinHeap{3, 1, 4, 1, 5}
    heap.Init(h)
    heap.Push(h, 2)
    min := heap.Pop(h).(int) // 1
    _ = min
}

// Top-K наименьших элементов
func topKSmallest(nums []int, k int) []int {
    h := &MinHeap{}
    heap.Init(h)
    for _, n := range nums {
        heap.Push(h, n)
    }
    result := make([]int, k)
    for i := 0; i < k; i++ {
        result[i] = heap.Pop(h).(int)
    }
    return result
}

// K-я наибольшая в стриме через min-heap размером K
type KthLargest struct {
    h *MinHeap
    k int
}

func NewKthLargest(k int, nums []int) *KthLargest {
    h := &MinHeap{}
    heap.Init(h)
    kl := &KthLargest{h: h, k: k}
    for _, n := range nums {
        kl.Add(n)
    }
    return kl
}

func (kl *KthLargest) Add(val int) int {
    heap.Push(kl.h, val)
    for kl.h.Len() > kl.k {
        heap.Pop(kl.h)
    }
    return (*kl.h)[0]
}

// Merge K отсортированных списков через min-heap
type Item struct {
    val, listIdx, elemIdx int
}
type ItemHeap []Item

func (h ItemHeap) Len() int           { return len(h) }
func (h ItemHeap) Less(i, j int) bool { return h[i].val < h[j].val }
func (h ItemHeap) Swap(i, j int)      { h[i], h[j] = h[j], h[i] }
func (h *ItemHeap) Push(x any)        { *h = append(*h, x.(Item)) }
func (h *ItemHeap) Pop() any {
    old := *h
    n := len(old)
    x := old[n-1]
    *h = old[:n-1]
    return x
}

func mergeKLists(lists [][]int) []int {
    h := &ItemHeap{}
    heap.Init(h)
    for i, list := range lists {
        if len(list) > 0 {
            heap.Push(h, Item{list[0], i, 0})
        }
    }
    var result []int
    for h.Len() > 0 {
        item := heap.Pop(h).(Item)
        result = append(result, item.val)
        if item.elemIdx+1 < len(lists[item.listIdx]) {
            heap.Push(h, Item{
                lists[item.listIdx][item.elemIdx+1],
                item.listIdx,
                item.elemIdx + 1,
            })
        }
    }
    return result
}

// Медиана стрима: max-heap нижняя + min-heap верхняя
type MedianFinder struct {
    lo *MaxHeap // нижняя половина
    hi *MinHeap // верхняя половина
}

type MaxHeap []int

func (h MaxHeap) Len() int           { return len(h) }
func (h MaxHeap) Less(i, j int) bool { return h[i] > h[j] } // обратный порядок
func (h MaxHeap) Swap(i, j int)      { h[i], h[j] = h[j], h[i] }
func (h *MaxHeap) Push(x any)        { *h = append(*h, x.(int)) }
func (h *MaxHeap) Pop() any {
    old := *h
    n := len(old)
    x := old[n-1]
    *h = old[:n-1]
    return x
}

func NewMedianFinder() *MedianFinder {
    lo, hi := &MaxHeap{}, &MinHeap{}
    heap.Init(lo)
    heap.Init(hi)
    return &MedianFinder{lo: lo, hi: hi}
}

func (mf *MedianFinder) AddNum(num int) {
    heap.Push(mf.lo, num)
    heap.Push(mf.hi, heap.Pop(mf.lo).(int))
    if mf.hi.Len() > mf.lo.Len() {
        heap.Push(mf.lo, heap.Pop(mf.hi).(int))
    }
}

func (mf *MedianFinder) FindMedian() float64 {
    if mf.lo.Len() > mf.hi.Len() {
        return float64((*mf.lo)[0])
    }
    return float64((*mf.lo)[0]+(*mf.hi)[0]) / 2.0
}
```

## Связи с другими темами

- [[Intervals]] — min-heap используется в задаче Meeting Rooms II (отслеживание активных интервалов)
- [[Dijkstra]] — использует min-heap для выбора ближайшей вершины
- [[Graphs]] — Heap в алгоритмах Prim и Dijkstra для минимального остовного дерева
- [[Big-O Notation]] — Build Heap O(n) — классический пример нетривиального анализа

## Важно / подводные камни / best practices

- В Go стандартный `container/heap` — min-heap. Для max-heap инвертируй `Less`
- Build Heap O(n) — используй `heap.Init()` вместо n раз `heap.Push()` (это O(n log n))
- Top-K задачи: для K наименьших используй **max-heap** размером K (отсекаешь крупные)
- Медиана: удерживай `|lo| == |hi|` или `|lo| == |hi| + 1` для корректного вычисления
- Heap Sort нестабилен — порядок одинаковых элементов не гарантирован

---

## Карточки для повторения

#flashcards/algorithms/heap

Что такое Heap и два его вида?::Почти полное бинарное дерево с heap property. Min-heap: родитель ≤ детей, минимум в корне. Max-heap: родитель ≥ детей, максимум в корне.

Хранение heap в массиве — формулы (корень в индексе 1)?::Parent(i) = ⌊i/2⌋. Left(i) = 2i. Right(i) = 2i+1.

Операция Insert в heap?::Добавить в конец массива → Bubble Up: сравнивать с родителем, менять местами пока heap property не восстановлено. O(log n).

Операция Extract-Min в heap?::Запомнить корень → заменить корень последним элементом → Heapify вниз: менять с наименьшим ребёнком пока не восстановлено. O(log n).

Почему Build Heap O(n), а не O(n log n)?::Heapify занимает O(высота). Большинство узлов — в нижних уровнях с высотой ≈ 0. Точный анализ: сумма работ = O(n).

Алгоритм Heap Sort?::1) Build-Max-Heap O(n); 2) Swap корень с последним; 3) Уменьшить heap size; 4) Heapify; 5) Повторять. Итого O(n log n), in-place.

Как найти Top-K наименьших через heap?::Max-heap размером K. Для каждого элемента: если элемент < корня → push + pop-max. В конце heap = K наименьших. O(n log K).

Как найти медиану стрима через два heap-а?::Max-heap для нижней половины, min-heap для верхней. Медиана = корень(ы). Баланс: |lo| == |hi| или |lo| == |hi| + 1.

Сложность операций Heap?::Get min/max O(1). Insert O(log n). Extract-min/max O(log n). Build Heap O(n).

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

#конспект #algorithms #heap #priority-queue #heap-sort #data-structures #interview-prep
