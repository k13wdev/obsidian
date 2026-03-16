# Binary Tree BFS

## Суть и контекст

BFS (Breadth-First Search) на бинарном дереве — обход в ширину, при котором алгоритм сначала исследует все непосредственные соседи узла, прежде чем переходить к их детям. Иначе называется level-order traversal (обход по уровням). Реализуется через очередь (FIFO). Предпочтителен, когда нужно найти кратчайший путь или ближайший к корню узел.

## Ключевые идеи

- BFS = «сначала широко, потом глубоко» — обход уровень за уровнем
- Реализуется через очередь (FIFO), не через рекурсию
- Обход по уровням: обработка всех узлов глубины k перед глубиной k+1
- Кратчайший путь: BFS находит ближайший узел эффективно
- Временная сложность O(n), пространственная O(w) где w — максимальная ширина дерева

## Определения и термины

**BFS (Breadth-First Search)** — обход в ширину: посещаем все непосредственные соседи узла до перехода к их детям.

**Level-order traversal** — синоним BFS для деревьев: обход узлов уровень за уровнем.

**Width of tree (ширина дерева)** — максимальное количество узлов на одном уровне. Определяет пространственную сложность BFS.

**Right view** — последний узел на каждом уровне — то, что видно с правой стороны дерева.

**Zigzag traversal** — level-order, но с чередованием направления: чётные уровни — слева направо, нечётные — справа налево.

## Как устроено

### Стандартный BFS

1. Добавить корень в очередь
2. Пока очередь не пуста:
   - Dequeue узел → «посетить» его
   - Enqueue непустые левый и правый дочерние узлы
3. FIFO-свойство гарантирует обработку узлов в порядке их обнаружения

### Level-by-Level (обход уровень за уровнем)

Когда нужно знать точный уровень узла:
1. В начале итерации зафиксировать `size = len(queue)` — это количество узлов текущего уровня
2. Обработать ровно `size` узлов (это весь текущий уровень)
3. Их дети, добавленные в очередь — составят следующий уровень
4. Повторить

### Временная и пространственная сложность

**Time**: O(n) — каждый из n узлов посещается ровно один раз, константная работа на узел.

**Space**: O(w) — очередь одновременно держит все узлы одного уровня. В идеально сбалансированном дереве максимальная ширина ≈ n/2 → O(n) в худшем случае. Итеративный BFS избегает O(log n) накладных расходов рекурсивного стека вызовов (как у DFS).

### Right View

Используя level-by-level BFS: последний узел, обработанный в каждой «партии» уровня — это правый вид на данной глубине.

### Zigzag Traversal

Стандартный level-by-level обход + булевый флаг, чередующий направление: на каждом чётном уровне разворачиваем собранные значения перед добавлением в результат.

## Детали и нюансы

**BFS итеративен по природе**: в отличие от DFS, BFS сложно написать рекурсивно. Очередь — естественная структура.

**Память vs DFS**: в широких деревьях BFS потребляет больше памяти, чем DFS. На очень широких уровнях очередь может содержать n/2 узлов.

**Кратчайший путь**: BFS гарантирует нахождение кратчайшего пути (по числу рёбер) — перед тем как идти глубже, исследует все близкие узлы.

## Сравнения и trade-offs

| | BFS | DFS |
|--|-----|-----|
| Структура | Очередь (FIFO) | Стек / Рекурсия |
| Память (balanced) | O(n/2) ≈ O(n) (ширина) | O(log n) (высота) |
| Кратчайший путь | Да | Нет |
| Уровневый обход | Естественно | Требует доп. логики |
| Реализация | Итеративная | Рекурсивная (проще) |
| Когда применять | Найти ближайший узел | Обойти все узлы |

## Примеры

```go
type TreeNode struct {
    Val   int
    Left  *TreeNode
    Right *TreeNode
}

// Стандартный BFS — level-order traversal
func levelOrder(root *TreeNode) [][]int {
    if root == nil {
        return nil
    }
    var result [][]int
    queue := []*TreeNode{root}

    for len(queue) > 0 {
        levelSize := len(queue) // размер текущего уровня
        level := make([]int, 0, levelSize)
        for i := 0; i < levelSize; i++ {
            node := queue[0]
            queue = queue[1:]
            level = append(level, node.Val)
            if node.Left != nil {
                queue = append(queue, node.Left)
            }
            if node.Right != nil {
                queue = append(queue, node.Right)
            }
        }
        result = append(result, level)
    }
    return result
}

// Right View — последний узел каждого уровня
func rightSideView(root *TreeNode) []int {
    if root == nil {
        return nil
    }
    var result []int
    queue := []*TreeNode{root}

    for len(queue) > 0 {
        levelSize := len(queue)
        for i := 0; i < levelSize; i++ {
            node := queue[0]
            queue = queue[1:]
            if i == levelSize-1 {
                result = append(result, node.Val)
            }
            if node.Left != nil {
                queue = append(queue, node.Left)
            }
            if node.Right != nil {
                queue = append(queue, node.Right)
            }
        }
    }
    return result
}

// Zigzag traversal — чередование направления
func zigzagLevelOrder(root *TreeNode) [][]int {
    if root == nil {
        return nil
    }
    var result [][]int
    queue := []*TreeNode{root}
    leftToRight := true

    for len(queue) > 0 {
        levelSize := len(queue)
        level := make([]int, levelSize)
        for i := 0; i < levelSize; i++ {
            node := queue[0]
            queue = queue[1:]
            if leftToRight {
                level[i] = node.Val
            } else {
                level[levelSize-1-i] = node.Val
            }
            if node.Left != nil {
                queue = append(queue, node.Left)
            }
            if node.Right != nil {
                queue = append(queue, node.Right)
            }
        }
        result = append(result, level)
        leftToRight = !leftToRight
    }
    return result
}

// Минимальная глубина дерева (BFS — первый лист)
func minDepth(root *TreeNode) int {
    if root == nil {
        return 0
    }
    queue := []*TreeNode{root}
    depth := 1

    for len(queue) > 0 {
        levelSize := len(queue)
        for i := 0; i < levelSize; i++ {
            node := queue[0]
            queue = queue[1:]
            // Первый лист — минимальная глубина
            if node.Left == nil && node.Right == nil {
                return depth
            }
            if node.Left != nil {
                queue = append(queue, node.Left)
            }
            if node.Right != nil {
                queue = append(queue, node.Right)
            }
        }
        depth++
    }
    return depth
}

// Связать узлы одного уровня (next pointer)
type NodeWithNext struct {
    Val   int
    Left  *NodeWithNext
    Right *NodeWithNext
    Next  *NodeWithNext
}

func connect(root *NodeWithNext) *NodeWithNext {
    if root == nil {
        return nil
    }
    queue := []*NodeWithNext{root}
    for len(queue) > 0 {
        levelSize := len(queue)
        for i := 0; i < levelSize; i++ {
            node := queue[0]
            queue = queue[1:]
            if i < levelSize-1 {
                node.Next = queue[0]
            }
            if node.Left != nil {
                queue = append(queue, node.Left)
            }
            if node.Right != nil {
                queue = append(queue, node.Right)
            }
        }
    }
    return root
}
```

## Связи с другими темами

- [[Queue]] — BFS основан на очереди (FIFO)
- [[Binary Tree DFS]] — альтернативный обход: в глубину, рекурсия или стек
- [[Graphs]] — BFS на графах: аналогичный алгоритм с множеством visited
- [[Binary Search Tree]] — BFS работает и на BST

## Важно / подводные камни / best practices

- Фиксируй `levelSize = len(queue)` **до** начала обработки уровня — иначе будешь обрабатывать узлы следующего уровня в текущей итерации
- BFS находит **минимальную** глубину (первый встреченный лист), DFS — нет без полного обхода
- В широких деревьях BFS потребляет O(n) памяти — учитывай это при выборе между BFS и DFS
- Для поиска кратчайшего пути в невзвешенном графе/дереве — BFS, не DFS

---

## Карточки для повторения

#flashcards/algorithms/binary-tree-bfs

Что такое BFS на дереве и как называется иначе?::Обход в ширину — level-order traversal. Посещаем все узлы уровня k перед уровнем k+1. Реализуется через очередь.

Алгоритм стандартного BFS?::1) Добавить корень в очередь; 2) Пока очередь не пуста: dequeue узел, enqueue левого и правого детей; 3) FIFO гарантирует обработку в порядке обнаружения.

Как реализовать level-by-level BFS?::Фиксировать `size = len(queue)` до итерации. Обработать ровно `size` узлов — это весь текущий уровень. Их дети составят следующий уровень.

Временная и пространственная сложность BFS?::O(n) время. O(w) память, где w — максимальная ширина дерева. В идеальном дереве w ≈ n/2 → O(n) худший случай.

Когда выбрать BFS, когда DFS?::BFS — найти кратчайший путь, ближайший узел, обход по уровням. DFS — обойти все узлы, естественная рекурсия, меньше памяти на глубоких деревьях.

Как найти Right View через BFS?::Level-by-level BFS. Последний узел в каждой партии уровня — это правый вид.

Как сделать Zigzag traversal?::Level-by-level BFS + булевый флаг. На чётных уровнях — обычный порядок, на нечётных — разворачиваем собранные значения.

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

#конспект #algorithms #binary-tree #bfs #level-order #tree-traversal #interview-prep
