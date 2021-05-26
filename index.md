# namespace std

### string
* Доступ к C-строке `s.с_str()`, `s.data()`
* `to_string(a_value)`
* `format("The answer is {}", 42)`, спецификация формата см. [тут](https://en.cppreference.com/w/cpp/utility/format/formatter#Standard_format_specification)
* s.substr(pos, len) - взять подстроку
* `operator+` склеить строки
* `tolower`, `toupper`
* `isdigit`, `isalpha`, `isalnum`, ...
* split строки "из коробки" нет! Вот решение из find+substr:
```
vector<string> split(string path, char delim) {
    vector<string> result;
    int i = 0;
    int j = 0;
    while (i < path.size()) {
        j = path.find(delim, i);
        if (j != string::npos) {
            auto tmp = path.substr(i, j - i);
            result.push_back(tmp);
            i = j + 1; // j + delim size
        } else {
            auto tmp = path.substr(i, path.size() - i);
            result.push_back(tmp);
            break;
        }
    }
    return result;
}
```
* `find(s, pos) -> pos` первого символа если s найдена, или `string::npos`. Также `rfind`
* `substr(pos, count) -> string`
* Парсинг чисел в строку: `stoi`, `stol`, `stoll`; `stoul`, `stoull`; `stof`, `stod`, `stold`. Сигнатура `int stoi(const string&, size_t* chars_processed=0, int base=10)`

### pair<T1, T2>
* Создание `make_pair(t1, t2)`
* Доступ к элементам `p.first` и `p.second`

### tuple<T1, T2, ...>
* Создание (элементы копируются) `make_tuple(t1, t2, ...)`
* Создание (привязка по `тип&) `tie(t1, t2, ...)`
* Доступ к элементу: `get<0>(t)`, `get<1>(t)`, где `t` - tuple

### optional<T>
* `make_optional(some_value)`
* `value()`, `has_value()`
* `value_or(default_value)`
* `reset()`
* `nullopt` - special value for a null optional
* `operator=()`
* Use `reference_wrapper` to put a reference into an optional.

### array
* `array<int, 3> arr = {1, 2, 3}`
* `make_array(1, 3, 3)`

### vector
* push_back, push_front
* `erase(it1) -> it2` - стирает it1 и возвращает it2 (следующий после удаленного).

### list, forward_list
* Итераторы инвалидируются только когда удаляется соотв. элемент

### deque

### stack
По дефолту, под капотом deque, но можно в шаблон задать другой совместимый контейнер
* `T& top()`
* `void push(const T&)`
* `void pop()`

### priority_queue
- Компаратор - это `operator<`. По умолчанию там less<T> и это max-heap.
- Чтобы сделать min heap:
```
priority_queue<int, vector<int>, greater<int>> pq;
```
- Чтобы использовать кастомный компаратор:
```
auto cmp = [](int a, int b) { return a > b; };
priority_queue<int, vector<int>, decltype(cmp)> pq(cmp);
```
- Доступ к элементам как в `stack`: методы `top`, `push`, `pop`.

### unordered_map - это hash map

### map - это tree map
* insert(key, value) возвращает pair<iterator, bool>, где bool - это была вставка или нет.

### unordered_set - это hash set
Чтобы сделать unordered_set для кастомного типа, надо определить hash функцию. Самый простой способ:
```
struct PairHash {
    size_t operator()(const pair<int, int>& p) const {
        return intHash(p.first) ^ intHash(p.second);
    }
private:
    hash<int> intHash;
};
using HashSet = unordered_set<pair<int, int>, PairHash>;
```

### set - это tree set
* Элементы set константные (как и ключи в map)
* Задать кастомный компаратор можно при помощи кастомного "меньше": `struct { bool operator()(const T& a, const T& b) {...} }`

### span<T> - это абстракция для `T*` или `const T*` 
где `T*` или `const T*` обычно указывают на область памати с идущими подряд элементами `T` (или `const T`).
Ништяки:
* передавать в функции `span<T>` вместо `T*`, например `void fun(span<int> nums)`
* итерироваться по спану `for (auto& num : nums)`
* использовать алгоритмы `find_if(nums.begin(), nums.end(), a_predicate)`

## Алгоритмы
* `sort(s.begin(), s.end())`
* в обратном порядке (лямбда это `operator<`):
`sort(s.begin(), s.end(), [](int a, int b) { return a > b; })`
----
* `find`
* `find_if(s.begin(), s.end(), [](int a) { return a == 42; })`
----
* `all_of`
* `any_of`
* `none_of`
----
* `count`
* `count_if`

## Numeric Limits
В хедере limits.h есть шаблон numeric_limits, примеры использования:
* `numeric_limits<int>::max()`
* `numeric_limits<int>::min()`

NaN значения типов с плавающей точкой:
* `numeric_limits<T>::quiet_NaN` - это константа с NaN значением
* `isnan` - проверка на NaN (из хедера cmath.h)

Infinity значения типов с плавающей точкой:
* `numeric_limits<T>::infinity`
* `isinf` и `isfinite`
   
## Bitwise operators
Надо быть осторожным, при сдвиге влево знаковых отрицательных чисел. Это UB!
Сдвиг вправо для отрицательных чисел арифметический (т.е. это "деление на 2" как и для положительных чисел)
    
## Random
* The old C-way: 
```
srand(time(nullptr)); // Параметр - это time_t*
// int rand(); // возвращает число от [0, RAND_MAX]
rand() % 10; // даст число  [0, 10)
```

* The new C++11 (since) way:
    TODO
    
## Math
* std::gcd - greatest common divisor, gcd(12, 18) = 6 // 12=2*2*3, 18=2*3*3, 6=2*3
* std::lcm - least common multiple, lcm(12, 18) = 36 // 12=2*2*3, 18=2*3*3, 36=2*2*3*3
