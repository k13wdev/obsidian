# Trie Autocomplete

## Суть и контекст

Trie (префиксное дерево, Prefix Tree) — специализированная структура данных, вариант n-ary дерева, где символы хранятся в узлах. Каждый путь вниз по дереву представляет слово или префикс. Используется для быстрого поиска по префиксу и автодополнения (autocomplete). Позволяет проверить, является ли строка валидным префиксом, за O(K) — столько же, сколько поиск в HashMap, но с дополнительными возможностями prefix search.

## Ключевые идеи

- Символы хранятся в узлах, пути представляют слова/префиксы
- Prefix sharing: все слова с одинаковым префиксом делят общие узлы
- Все операции — O(K), где K — длина строки
- Ключевое применение: ранняя остановка рекурсии по недостижимым путям (pruning)
- Одновременный поиск нескольких подстрок через суффиксный Trie

## Определения и термины

**Trie (Prefix Tree)** — n-ary дерево, где символы хранятся в узлах, пути представляют слова/префиксы.

**ALPHABET_SIZE** — количество возможных дочерних узлов у каждого узла (например, 26 для строчных букв английского алфавита).

**Termination indicator (terminates flag)** — маркер конца слова. Реализуется как булевое поле в узле (`isEnd bool`) или специальный дочерний узел `*` (null).

**Prefix sharing** — слова с общим префиксом делят узлы. Экономит память по сравнению с хранением отдельных строк.

**Short-circuiting (pruning)** — ранняя остановка рекурсии: если текущий префикс не найден в Trie → прекратить поиск по этому пути.

**Multi-Search (мультипоиск)** — поиск нескольких малых строк внутри большой строки через Trie.

## Как устроено

### Структура узла

Каждый узел содержит:
1. **Children**: отображение символа → дочерний TrieNode. Реализуется через `HashMap<Character, TrieNode>` (динамически, по факту ветвления) или массив размером ALPHABET_SIZE
2. **Terminates flag**: булевый флаг (или специальный `*`-узел), сигнализирующий конец полного слова

### Trie класс

Обёртка хранит ссылку на корневой узел — пустой TrieNode, представляющий начало.

### Операции

**Insert (addWord)**:
1. Начать с корня
2. Для каждого символа строки:
   - Проверить через `getChild(char)` — существует ли дочерний узел
   - Если нет → создать новый TrieNode, добавить в children
3. После последнего символа → установить `terminates = true`

**Search (contains)** и **startsWith**:
1. Начать с корня, итерировать по символам
2. На каждом шаге: если следующего символа нет в children → вернуть false
3. Для **startsWith**: успешный проход длины префикса → вернуть true
4. Для **поиска точного слова**: успешный проход всей строки + `terminates == true`

### Временная и пространственная сложность

**Time**: O(K) для insert, search, startsWith, где K — длина строки. Примечание: HashMap также требует O(K) для чтения всех символов строки при хешировании — оба подхода одинаковы по времени.

**Space**: зависит от реализации и размера алфавита. Каждый узел может иметь до ALPHABET_SIZE дочерних. HashMap-реализация — динамическое выделение по факту. За счёт prefix sharing Trie может экономить память по сравнению с хранением отдельных строк, но накладные расходы на объекты узлов и HashMap могут быть значительны.

## Применения

### Хранение словаря

Классический use case: хранение всего словаря английских слов для быстрой проверки валидности префикса.

### Автодополнение (Autocomplete)

Поиск всех слов, начинающихся с заданного префикса:
1. Пройти по Trie до конца префикса
2. DFS/BFS из этого узла → собрать все слова (узлы с `terminates = true`)

### Pruning в рекурсивных алгоритмах

Ключевое применение: если текущая строка — **не валидный префикс** в Trie → немедленно прерываем рекурсию по этому пути. Примеры:
- T9 раскладка клавиатуры: сопоставление цифр со словами — Trie позволяет остановиться, если нет слов с текущим префиксом
- Построение «словарных прямоугольников»: каждый новый символ проверяется через Trie

### Multi-Search

Поиск нескольких коротких строк в большой строке:
- Способ 1: добавить все короткие строки в Trie, итерироваться по большой строке
- Способ 2: добавить все суффиксы большой строки в Trie, искать короткие строки

## Детали и нюансы

**Trie vs HashMap для поиска слов**: оба O(K) по времени, но Trie дополнительно поддерживает prefix search. HashMap — быстрее на практике (константы), Trie — мощнее для prefix-задач.

**Реализация children**: массив `[26]byte` — быстрее (O(1) доступ по индексу), но тратит память на все 26 слотов. HashMap — только реально существующие дочерние узлы, но чуть медленнее.

**terminates как `*`-узел**: вместо булева флага добавляется специальный null-дочерний узел `*`. Унифицирует интерфейс, но добавляет одну итерацию.

## Сравнения и trade-offs

| | Trie | HashMap |
|--|------|---------|
| Поиск слова | O(K) | O(K) |
| Prefix search | O(K) | Нельзя (напрямую) |
| Autocomplete | Да | Нет |
| Память | Зависит от prefix sharing | Компактнее для несвязанных слов |
| Pruning рекурсии | Да | Нет |

## Примеры

```go
type TrieNode struct {
    children  [26]*TrieNode
    isEnd     bool
}

type Trie struct {
    root *TrieNode
}

func NewTrie() *Trie {
    return &Trie{root: &TrieNode{}}
}

func (t *Trie) Insert(word string) {
    node := t.root
    for _, ch := range word {
        idx := ch - 'a'
        if node.children[idx] == nil {
            node.children[idx] = &TrieNode{}
        }
        node = node.children[idx]
    }
    node.isEnd = true
}

func (t *Trie) Search(word string) bool {
    node := t.root
    for _, ch := range word {
        idx := ch - 'a'
        if node.children[idx] == nil {
            return false
        }
        node = node.children[idx]
    }
    return node.isEnd
}

func (t *Trie) StartsWith(prefix string) bool {
    node := t.root
    for _, ch := range prefix {
        idx := ch - 'a'
        if node.children[idx] == nil {
            return false
        }
        node = node.children[idx]
    }
    return true
}

// Autocomplete — все слова с заданным префиксом
func (t *Trie) Autocomplete(prefix string) []string {
    node := t.root
    for _, ch := range prefix {
        idx := ch - 'a'
        if node.children[idx] == nil {
            return nil
        }
        node = node.children[idx]
    }
    var results []string
    var dfs func(*TrieNode, string)
    dfs = func(n *TrieNode, current string) {
        if n.isEnd {
            results = append(results, current)
        }
        for i, child := range n.children {
            if child != nil {
                dfs(child, current+string(rune('a'+i)))
            }
        }
    }
    dfs(node, prefix)
    return results
}

// Trie через HashMap — динамическое выделение
type TrieNodeMap struct {
    children map[rune]*TrieNodeMap
    isEnd    bool
}

func newTrieNodeMap() *TrieNodeMap {
    return &TrieNodeMap{children: make(map[rune]*TrieNodeMap)}
}

type TrieMap struct {
    root *TrieNodeMap
}

func NewTrieMap() *TrieMap {
    return &TrieMap{root: newTrieNodeMap()}
}

func (t *TrieMap) Insert(word string) {
    node := t.root
    for _, ch := range word {
        if _, ok := node.children[ch]; !ok {
            node.children[ch] = newTrieNodeMap()
        }
        node = node.children[ch]
    }
    node.isEnd = true
}

func (t *TrieMap) Search(word string) bool {
    node := t.root
    for _, ch := range word {
        if _, ok := node.children[ch]; !ok {
            return false
        }
        node = node.children[ch]
    }
    return node.isEnd
}

// Pruning — поиск слов с backtracking + Trie
func findWords(board [][]byte, words []string) []string {
    trie := NewTrie()
    for _, w := range words {
        trie.Insert(w)
    }

    rows, cols := len(board), len(board[0])
    var result []string
    visited := make([][]bool, rows)
    for i := range visited {
        visited[i] = make([]bool, cols)
    }

    var dfs func(node *TrieNode, i, j int, path string)
    dfs = func(node *TrieNode, i, j int, path string) {
        if i < 0 || i >= rows || j < 0 || j >= cols || visited[i][j] {
            return
        }
        ch := board[i][j]
        next := node.children[ch-'a']
        if next == nil {
            return // pruning — нет слов с этим префиксом
        }
        path += string(ch)
        visited[i][j] = true
        if next.isEnd {
            result = append(result, path)
            next.isEnd = false // избегаем дубликатов
        }
        dfs(next, i+1, j, path)
        dfs(next, i-1, j, path)
        dfs(next, i, j+1, path)
        dfs(next, i, j-1, path)
        visited[i][j] = false
    }

    for i := 0; i < rows; i++ {
        for j := 0; j < cols; j++ {
            dfs(trie.root, i, j, "")
        }
    }
    return result
}
```

## Связи с другими темами

- [[HashMap]] — альтернатива для поиска слов; Trie добавляет prefix search поверх O(K) сложности
- [[Binary Tree DFS]] — автодополнение использует DFS для обхода Trie
- [[Backtracking]] — Trie используется для pruning в backtracking алгоритмах

## Важно / подводные камни / best practices

- `terminates` должен быть у **узла**, а не у **ребра** — это позволяет корректно различать «apple» и «app»
- При autocomplete DFS по Trie — всегда добавляй текущий символ к пути перед спуском
- При Word Search (board + words) — сбрасывай `isEnd = false` после нахождения слова, чтобы не добавлять дубликаты
- Trie из массива `[26]*TrieNode` vs HashMap: массив быстрее для ASCII алфавитов, HashMap — для больших алфавитов (Unicode)

---

## Карточки для повторения

#flashcards/algorithms/trie

Что такое Trie?::N-ary дерево, где символы хранятся в узлах. Каждый путь сверху вниз представляет слово или префикс. Узлы с одинаковым префиксом общие — prefix sharing.

Структура узла Trie?::1) children — массив [ALPHABET_SIZE] или HashMap: символ → дочерний узел; 2) terminates (isEnd) — булевый флаг окончания слова.

Алгоритм Insert в Trie?::С корня итерировать по символам. Если дочернего нет — создать. После последнего символа: isEnd = true.

Разница Search и StartsWith в Trie?::StartsWith: успешный проход длины префикса → true. Search: успешный проход всей строки + isEnd == true.

Временная сложность операций Trie?::O(K) для insert, search, startsWith — где K длина строки. Столько же, сколько HashMap (тоже читает все K символов для хеша).

Почему Trie vs HashMap для поиска?::Оба O(K), но Trie дополнительно поддерживает prefix search и autocomplete. HashMap — проще и быстрее на практике.

Как Trie используется для pruning в backtracking?::Если текущий собранный префикс не найден в Trie (StartsWith = false) → немедленно прекратить рекурсию по этому пути. Срезаем бесперспективные ветки.

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

#конспект #algorithms #trie #prefix-tree #autocomplete #backtracking #interview-prep
