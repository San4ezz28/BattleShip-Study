# BattleShip-Study

Учебный проект: игра «Морской бой» на C++. Цель — не только написать игру, но и по ходу изучить язык:
основы, классы, контейнеры STL, алгоритмы, ООП, тесты и графику (SFML).

- План этапов и чек-лист: [docs/PLAN.md](docs/PLAN.md)
- Карточки заданий по этапам: [docs/stages/](docs/stages/)

## Установка окружения (Windows)

Ставится один раз. Делай по порядку.

1. **Git** — <https://git-scm.com/download/win>. Все настройки установщика можно оставить по умолчанию.
   После установки один раз представься гиту (в терминале):
   ```
   git config --global user.name "Твоё Имя"
   git config --global user.email "твоя@почта"
   ```
2. **Компилятор MSVC** — скачай «Build Tools for Visual Studio 2022» с
   <https://visualstudio.microsoft.com/ru/downloads/> (раздел «Инструменты для Visual Studio»).
   В установщике отметь галочку **«Разработка классических приложений на C++»** и нажми «Установить».
   Это сам компилятор C++, без него ничего собираться не будет.
3. **CMake** — <https://cmake.org/download/> (Windows x64 Installer).
   В установщике выбери **«Add CMake to the system PATH»**.
4. **VS Code** — <https://code.visualstudio.com>.
5. Перезагрузи компьютер (чтобы все программы увидели новый PATH).

Проверка: открой новый терминал (`Win + R` → `cmd`) и выполни
```
git --version
cmake --version
```
Обе команды должны напечатать версию, а не «не является внутренней или внешней командой».

## Получение проекта

```
cd /d "D:\My projects"
git clone https://github.com/San4ezz28/BattleShip-Study.git BattleShip_Study
```

Затем в VS Code: **File → Open Folder** → `D:\My projects\BattleShip_Study`.

VS Code предложит установить рекомендованные расширения (**C/C++** и **CMake Tools**) — соглашайся.
Если не предложил: вкладка Extensions (`Ctrl+Shift+X`), найди и установи их вручную.

## Сборка и запуск в VS Code

1. При первом открытии CMake Tools спросит про **Kit** (набор компилятора). Выбери
   `Visual Studio Build Tools 2022 Release - amd64`. Если не спросил: `Ctrl+Shift+P` → `CMake: Select a Kit`.
2. Сборка: `F7` (или `Ctrl+Shift+P` → `CMake: Build`).
3. Запуск: `Shift+F5` (`CMake: Run Without Debugging`).
4. Запуск под отладчиком: `Ctrl+F5` (`CMake: Debug`). Поставь точку останова кликом слева от номера строки.

Должно напечататься `Hello, Battleship!`.

Все результаты сборки лежат в папке `build/` — она в `.gitignore`, в git её не коммитим.

## Сборка из терминала (если нужно)

```
cmake -S . -B build
cmake --build build
build\Debug\battleship_console.exe
```

## Структура

```
CMakeLists.txt      описание сборки
src/console/        консольная версия игры
src/core/           (появится на этапе 3) логика игры без ввода/вывода
src/gui/            (этап 8) графическая версия на SFML
tests/              (этап 7) тесты
docs/               план и карточки заданий
```
