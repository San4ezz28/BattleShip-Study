cmake_minimum_required(VERSION 3.20)

project(BattleShip LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

add_executable(battleship_console
    src/console/main.cpp
)

if(MSVC)
    # /utf-8 is needed so that Russian text in sources and string literals is read correctly.
    target_compile_options(battleship_console PRIVATE /W4 /utf-8 /permissive-)
else()
    target_compile_options(battleship_console PRIVATE -Wall -Wextra -Wpedantic)
endif()
