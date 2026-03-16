# Stack

## Суть и контекст

Stack (стек) — динамическая структура данных, работающая строго по принципу LIFO (Last-In, First-Out): последний добавленный элемент удаляется первым. Аналог — стопка тарелок в столовой или стопка стикеров: можно класть и брать только сверху. Стек не предоставляет константного доступа к произвольным элементам по индексу, как массив, но его ограниченный доступ делает базовые операции исключительно эффективными — O(1).

## Ключевые идеи

- LIFO: последний вошёл — первый вышел
- Все базовые операции — O(1)
- Реализуется через массив или связный список
- Внутренний стек вызовов ОС работает по этому же принципу
- Любой рекурсивный алгоритм можно сделать итеративным через явный стек

## Определения и термины

**LIFO (Last-In, First-Out)** — принцип работы стека: элемент, добавленный последним, извлекается первым.

**Top** — указатель/индекс на верхний элемент стека.

**Stack overflow** — ошибка при попытке добавить элемент в полный стек (при реализации через массив фиксированного размера).

**Stack underflow** — ошибка при попытке извлечь элемент из пустого стека.

**Call stack (стек вызовов)** — системный стек, управляющий функциями и переменными во время выполнения программы, особенно при рекурсии.

## Как устроено

### Операции

| Операция | Сложность | Описание |
|----------|-----------|----------|
| `push(item)` | O(1) | Добавляет элемент на вершину стека. В array-реализации: incrementing `top`, запись по индексу |
| `pop()` | O(1) | Удаляет и возвращает верхний элемент. Обрабатывает элементы в обратном порядке вставки |
| `peek()` | O(1) | Возвращает значение верхнего элемента без удаления |
| `isEmpty()` | O(1) | Возвращает true, если стек пуст. Используется перед pop() для предотвращения underflow |

### Реализации

**Через массив**: `top` — индекс верхнего элемента. При `push` инкрементируется, при `pop` декрементируется. При фиксированном размере — нужна проверка на overflow.

**Через связный список**: добавление и удаление с головы списка. Не требует заранее заданного размера.

### Применение

**Call Stack**: управление функциями при рекурсии — каждый вызов остаётся на стеке до завершения.

**Итеративная рекурсия**: стек явно заменяет системный стек вызовов, позволяя реализовать рекурсивный алгоритм итеративно.

**Разворот последовательности**: LIFO-свойство естественно разворачивает порядок: push всех элементов, затем pop — элементы выходят в обратном порядке.

## Детали и нюансы

**Нет доступа по индексу**: в отличие от массива, стек не позволяет обратиться к произвольному элементу — только к верхнему.

**Итеративный DFS**: стек используется как вспомогательная структура для итеративной реализации обхода деревьев/графов в глубину (DFS).

**Монотонный стек**: специализированный паттерн — в стеке поддерживается строго возрастающий или убывающий порядок элементов. Используется в задачах типа «следующий больший элемент».

**Вычисление арифметических выражений**: два стека — `numberStack` для чисел и `operatorStack` для операторов. При встрече оператора с ≤ приоритетом применяем накопленные операторы.

**Топологическая сортировка через стек**: DFS-based топологическая сортировка: при завершении вершины — вставлять её в начало результата (или push на стек, затем вытащить в обратном порядке). Фактически накапливает вершины в порядке убывания finish time.

**Set of Stacks (стек стеков)**: если стек превысил порог (threshold), создаётся новый «внутренний» стек. Push/Pop прозрачно работают с последним. Дополнительно: `popAt(index)` — pop из произвольного внутреннего стека.

**Башни Ханоя**: три стека моделируют три стержня. Рекурсивное решение: переместить n-1 дисков на вспомогательный, переместить наибольший на целевой, перенести n-1 дисков. O(2ⁿ) ходов.

**Сортировка стека одним вспомогательным стеком**: итеративно извлекаем элементы из исходного, вставляем в отсортированном порядке во вспомогательный. O(n²). Алгоритм: взять top из src, пока top tmp > взятого — переложить tmp → src, вставить взятый в tmp.

## Сравнения и trade-offs

| Реализация | Плюсы | Минусы |
|------------|-------|--------|
| Через массив | Быстрый доступ, cache-friendly | Фиксированный размер (или дорогой resize) |
| Через linked list | Динамический размер | Доп. память на указатели |

## Примеры

```go
// Реализация стека через срез
type Stack[T any] struct {
    items []T
}

func (s *Stack[T]) Push(item T) {
    s.items = append(s.items, item)
}

func (s *Stack[T]) Pop() (T, bool) {
    var zero T
    if len(s.items) == 0 {
        return zero, false
    }
    top := s.items[len(s.items)-1]
    s.items = s.items[:len(s.items)-1]
    return top, true
}

func (s *Stack[T]) Peek() (T, bool) {
    var zero T
    if len(s.items) == 0 {
        return zero, false
    }
    return s.items[len(s.items)-1], true
}

func (s *Stack[T]) IsEmpty() bool {
    return len(s.items) == 0
}

// Разворот строки через стек
func reverseString(s string) string {
    stack := &Stack[byte]{}
    for i := 0; i < len(s); i++ {
        stack.Push(s[i])
    }
    result := make([]byte, len(s))
    for i := range result {
        result[i], _ = stack.Pop()
    }
    return string(result)
}

// Валидация скобок (классическое применение стека)
func isValid(s string) bool {
    stack := &Stack[rune]{}
    pairs := map[rune]rune{')': '(', ']': '[', '}': '{'}
    for _, c := range s {
        switch c {
        case '(', '[', '{':
            stack.Push(c)
        case ')', ']', '}':
            top, ok := stack.Pop()
            if !ok || top != pairs[c] {
                return false
            }
        }
    }
    return stack.IsEmpty()
}

// Итеративный DFS на графе с явным стеком
func dfsIterative(graph map[int][]int, start int) []int {
    visited := make(map[int]bool)
    stack := &Stack[int]{}
    var result []int

    stack.Push(start)
    for !stack.IsEmpty() {
        node, _ := stack.Pop()
        if visited[node] {
            continue
        }
        visited[node] = true
        result = append(result, node)
        for _, neighbor := range graph[node] {
            if !visited[neighbor] {
                stack.Push(neighbor)
            }
        }
    }
    return result
}

// Вычисление арифметического выражения (без скобок, только +, -, *, /)
func evalExpression(tokens []string) int {
    numStack := &Stack[int]{}
    opStack := &Stack[string]{}

    priority := map[string]int{"+": 1, "-": 1, "*": 2, "/": 2}

    applyOp := func() {
        op, _ := opStack.Pop()
        b, _ := numStack.Pop()
        a, _ := numStack.Pop()
        switch op {
        case "+":
            numStack.Push(a + b)
        case "-":
            numStack.Push(a - b)
        case "*":
            numStack.Push(a * b)
        case "/":
            numStack.Push(a / b)
        }
    }

    for _, tok := range tokens {
        if n, err := strconv.Atoi(tok); err == nil {
            numStack.Push(n)
        } else {
            for !opStack.IsEmpty() {
                top, _ := opStack.Peek()
                if priority[top] >= priority[tok] {
                    applyOp()
                } else {
                    break
                }
            }
            opStack.Push(tok)
        }
    }
    for !opStack.IsEmpty() {
        applyOp()
    }
    result, _ := numStack.Pop()
    return result
}

// Сортировка стека с одним вспомогательным стеком — O(n²)
func sortStack(src *Stack[int]) *Stack[int] {
    tmp := &Stack[int]{}
    for !src.IsEmpty() {
        curr, _ := src.Pop()
        // переместить обратно всё, что больше curr
        for !tmp.IsEmpty() {
            top, _ := tmp.Peek()
            if top > curr {
                tmp.Pop()
                src.Push(top)
            } else {
                break
            }
        }
        tmp.Push(curr)
    }
    return tmp // наименьший на вершине
}

// Set of Stacks — стек стеков с порогом вместимости
type SetOfStacks struct {
    stacks    [][]int
    threshold int
}

func NewSetOfStacks(threshold int) *SetOfStacks {
    return &SetOfStacks{threshold: threshold}
}

func (s *SetOfStacks) Push(val int) {
    if len(s.stacks) == 0 || len(s.stacks[len(s.stacks)-1]) == s.threshold {
        s.stacks = append(s.stacks, []int{})
    }
    last := len(s.stacks) - 1
    s.stacks[last] = append(s.stacks[last], val)
}

func (s *SetOfStacks) Pop() (int, bool) {
    if len(s.stacks) == 0 {
        return 0, false
    }
    last := len(s.stacks) - 1
    stack := s.stacks[last]
    val := stack[len(stack)-1]
    s.stacks[last] = stack[:len(stack)-1]
    if len(s.stacks[last]) == 0 {
        s.stacks = s.stacks[:last]
    }
    return val, true
}

// Монотонный стек — следующий больший элемент
func nextGreaterElement(nums []int) []int {
    result := make([]int, len(nums))
    stack := &Stack[int]{}
    for i := len(nums) - 1; i >= 0; i-- {
        for !stack.IsEmpty() {
            top, _ := stack.Peek()
            if top <= nums[i] {
                stack.Pop()
            } else {
                break
            }
        }
        if stack.IsEmpty() {
            result[i] = -1
        } else {
            result[i], _ = stack.Peek()
        }
        stack.Push(nums[i])
    }
    return result
}
```

## Связи с другими темами

- [[Queue]] — противоположный принцип: FIFO вместо LIFO
- [[Binary Tree DFS]] — итеративный DFS использует явный стек
- [[Graphs]] — итеративный обход графа в глубину через стек
- [[Backtracking]] — рекурсия = неявный стек вызовов

## Важно / подводные камни / best practices

- Всегда проверяй `isEmpty()` перед `pop()` и `peek()` во избежание underflow
- В Go стандартного типа Stack нет — используй срез (`[]T`) или `container/list`
- Итеративный DFS через стек обходит узлы в другом порядке, чем рекурсивный (порядок добавления в стек влияет на порядок обхода)
- Монотонный стек решает задачи «следующий/предыдущий больший/меньший элемент» за O(n)

---

## Карточки для повторения

#flashcards/algorithms/stack

Что такое Stack и принцип LIFO?::LIFO — Last-In, First-Out. Последний добавленный элемент извлекается первым. Аналог — стопка тарелок: класть и брать можно только сверху.

Четыре операции стека и их сложность?::push(item) — O(1) добавить наверх; pop() — O(1) удалить и вернуть верхний; peek() — O(1) посмотреть верхний без удаления; isEmpty() — O(1) проверить пустоту.

Stack overflow vs Stack underflow?::Overflow — попытка добавить в полный стек (array-реализация). Underflow — попытка извлечь из пустого стека.

Три классических применения стека?::1) Call stack — управление рекурсивными вызовами; 2) Итеративная рекурсия — явный стек заменяет системный; 3) Разворот порядка — LIFO разворачивает последовательность.

Как реализовать итеративный DFS через стек?::Вместо рекурсии — явный стек. Push стартового узла. Цикл: pop узел, если не посещён — обработай и push всех соседей.

Что такое монотонный стек и когда применять?::Стек, где поддерживается строго возрастающий/убывающий порядок элементов. Задачи: «следующий больший элемент», «предыдущий меньший элемент». O(n).

Как вычислять арифметические выражения через стек?::Два стека: numberStack + operatorStack. При встрече оператора с ≤ приоритетом применяем все накопленные операторы того же/большего приоритета. Алгоритм shunting-yard.

Что такое Set of Stacks?::Стек стеков: когда внутренний стек достигает порога threshold, создаётся новый. Поддерживает popAt(index) — извлечение из конкретного внутреннего стека.

Как отсортировать стек с одним дополнительным стеком?::Итерация: берём top из src, в tmp перекладываем обратно в src всё, что > curr, затем push curr в tmp. O(n²). Результат в tmp — наименьший на вершине.

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

#конспект #algorithms #stack #data-structures #lifo #interview-prep
