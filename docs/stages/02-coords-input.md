# Этап 2. Свои типы и ввод координат

## Цель

Можно «стрелять» по полю, вводя координаты вроде `B5`. На поле заранее вручную расставлены
несколько палуб. Программа отвечает «попал»/«мимо», отмечает выстрел и снова выводит поле.
Любой неправильный ввод не роняет программу, а просит ввести заново.

```
   A B C D E F G H I J
 1 X . . . . . . . . .
 2 . . . . . . . . . .
 3 . . o . . . . . . .
 ...
Shot (e.g. B5, q to quit): K1
Invalid coordinate, try again.
Shot (e.g. B5, q to quit): C3
You already shot there.
Shot (e.g. B5, q to quit): q
```

Обозначения: `.` — не стреляли, `X` — попадание, `o` — промах. Корабли противника не показываем.

## Что прочитать

На <https://ravesli.com/uroki-cpp/>:

- Структуры (`struct`).
- Перечисления: `enum` и **`enum class`** (почему второй лучше).
- `switch`.
- `std::string`: длина, доступ к символу по индексу, сравнение.
- Ссылки: передача параметров по значению, по ссылке и по `const`-ссылке. Здесь это уже нужно понимать по-настоящему.
- `std::optional` — на <https://en.cppreference.com/w/cpp/utility/optional> (на русском хорошо гуглится
  «std::optional C++17»). Главное: это «значение или ничего».
- Функции `std::toupper` и `std::isdigit` из `<cctype>`.

## Что меняется по сравнению с этапом 1

Раньше клетка была `char` (`'.'`, `'#'`). Это неудобно: символ — это про **отображение**,
а не про **смысл**. Теперь клетка будет `enum class Cell`, а превращение в символ — отдельная функция.
Это первый шаг к отделению логики игры от вывода на экран.

## Мини-шаги

1. Объяви `enum class Cell { Empty, Ship, Hit, Miss };` и поменяй `Grid` на массив `Cell`.
   Исправь `makeEmptyGrid` и `printGrid`: компилятор подскажет все места, где теперь ошибка.
2. Напиши `char toChar(Cell cell, bool showShips)` через `switch`. Если `showShips == false`,
   корабль выглядит как пустая клетка.
3. Объяви `struct Coord { int row; int col; };`. Поставь пару палуб через
   `Coord`: `Coord c{4, 1}; grid[c.row][c.col] = Cell::Ship;` — это `B5`.
4. Считай одно слово с клавиатуры в `std::string` и просто выведи его обратно. Проверь.
5. Напиши `parseCoord` (заготовка ниже). Сначала **только для простых случаев** `A1`–`J9`
   (ровно 2 символа). Проверь на `A1`, `B5`, `J9`.
6. Добавь проверки: неверная длина, буква вне `A–J`, не цифра. Добавь поддержку `10`
   (3 символа) и маленьких букв (`b5`).
7. Напиши тестовый вызов прямо в `main()`: прогони `parseCoord` на списке строк
   `"A1", "b5", "J10", "K1", "B0", "B11", "", "5B", "B5x"` и выведи результат каждого.
   Правильные: первые три, остальные должны быть отклонены.
8. Напиши `shoot(Grid& grid, Coord target)`: `Ship` → `Hit`, `Empty` → `Miss`,
   уже стреляли — сообщение и ничего не меняем.
9. Собери игровой цикл: вывести поле → спросить координату → `q` — выход →
   не разобралась — сообщение и снова → иначе `shoot`.
10. Под отладчиком поставь точку останова в `parseCoord` и пройди по шагам ввод `J10`.
    Смотри, как меняется переменная с числом.

## Заготовка

```cpp
#include <array>
#include <cctype>
#include <iostream>
#include <optional>
#include <string>

const int kBoardSize = 10;

enum class Cell { Empty, Ship, Hit, Miss };

using Grid = std::array<std::array<Cell, kBoardSize>, kBoardSize>;

struct Coord
{
    int row;  // 0..9, строка "1" на экране — это row 0
    int col;  // 0..9, столбец "A" — это col 0
};

// Символ для вывода клетки. Если showShips == false, корабль не виден.
char toChar(Cell cell, bool showShips)
{
    // TODO: switch по cell
}

void printGrid(const Grid& grid, bool showShips)
{
    // TODO: как на этапе 1, но через toChar
}

// "B5" -> Coord{4, 1}. "b5" тоже подходит. Если ввод неправильный — std::nullopt.
std::optional<Coord> parseCoord(const std::string& input)
{
    // TODO
}

// Выстрел по клетке: меняет поле и печатает результат.
void shoot(Grid& grid, Coord target)
{
    // TODO
}

int main()
{
    Grid grid{};  // {} заполняет все клетки нулевым значением, то есть Cell::Empty
    // TODO: поставь несколько палуб
    // TODO: игровой цикл
    return 0;
}
```

## Подсказки

<details>
<summary>Подсказка 1 (намёк)</summary>

- `parseCoord` удобно писать по принципу «проверил — если плохо, сразу `return std::nullopt;`».
  Тогда не будет глубокой вложенности `if`.
- Цифра-символ `'7'` — это не число 7. Но `'7' - '0'` — это 7.
- Буква тоже превращается в номер столбца вычитанием.
- В `shoot` нужно **изменить** клетку массива, поэтому `grid` передаётся по ссылке без `const`.
</details>

<details>
<summary>Подсказка 2 (направление)</summary>

`parseCoord`, по порядку:
1. Длина должна быть 2 или 3 (`input.size()`).
2. `char letter = std::toupper(input[0])` (компилятор может ругаться на тип — тогда `static_cast<char>(...)`).
   Буква от `'A'` до `'J'`, иначе `nullopt`.
3. Пройди циклом по символам с индекса 1 до конца: каждый должен быть цифрой (`std::isdigit`),
   и собирай число: `number = number * 10 + (input[i] - '0');`
4. Число от 1 до 10.
5. `return Coord{number - 1, letter - 'A'};`

Использование:
```cpp
std::optional<Coord> target = parseCoord(input);
if (!target)        // ничего не вернулось
{
    // ...
}
shoot(grid, *target);  // * достаёт значение из optional
```

Ввод: `std::cin >> input;` читает одно слово. Если `std::cin` «сломался» (например, конец ввода),
выражение `!(std::cin >> input)` будет истинным — по нему тоже стоит выйти из цикла.
</details>

<details>
<summary>Подсказка 3 (почти готовый код — только если совсем застрял)</summary>

```cpp
void shoot(Grid& grid, Coord target)
{
    Cell& cell = grid[target.row][target.col];  // ссылка: меняя cell, меняем клетку в поле
    if (cell == Cell::Ship)
    {
        cell = Cell::Hit;
        std::cout << "Hit!\n";
    }
    // TODO: Empty -> Miss, иначе "already shot"
}
```

Игровой цикл:

```cpp
std::string input;
while (true)
{
    printGrid(grid, false);
    std::cout << "Shot (e.g. B5, q to quit): ";
    if (!(std::cin >> input) || input == "q")
    {
        break;
    }
    // TODO: parseCoord, проверка, shoot
}
```
</details>

## Дополнительно (по желанию)

- Посчитай количество палуб на поле и заверши игру, когда все подбиты («You win!»).
- Считай число выстрелов и выведи в конце.
- Попробуй сделать `printGrid(grid, true)` по команде `show` — «чит» для отладки.

## Самопроверка

1. Почему `enum class` лучше обычного `enum`? Что не скомпилируется с `enum class`, но скомпилируется с `enum`?
2. Почему `parseCoord` принимает `const std::string&`, `shoot` — `Grid&`, а `Coord` передаётся по значению?
3. Что такое `std::optional` и чем он лучше, чем, например, вернуть `Coord{-1, -1}` при ошибке?
4. Что произойдёт, если в `shoot` написать `Cell cell = ...` без `&`? Проверь и объясни.
5. Что вернёт `parseCoord("A01")`? Это нормально? (Решай сам — тут нет одного правильного ответа.)
6. Почему `Grid grid{};` заполняет поле значениями `Cell::Empty`?

Когда всё работает — коммит (`git commit -m "Stage 2: coordinates input and shooting"`), отметки в [PLAN.md](../PLAN.md), `git push`.

**Следующий этап** — классы `Ship` и `Board`. Перед ним пришли код на ревью — карточку этапа 3
лучше писать с учётом того, как у тебя получились этапы 1–2.
