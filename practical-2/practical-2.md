# Практическая работа 2 – Многофайловые проекты

ФИО: [Введите ФИО]

Курс: [Введите курс]

Группа: [Введите номер группы]

## Введение

Многофайловые проекты позволяют разделить программу на логически независимые модули, что значительно упрощает её разработку, тестирование и сопровождение. Для будущих учителей информатики особенно важно научиться организовывать исходный код так, чтобы его можно было легко адаптировать для создания качественных учебных материалов для школьников. В данной работе вам необходимо разработать три разных задания по созданию многофайловых проектов. Каждое задание должно включать подробное алгоритмическое описание, частичное решение с пропусками для самостоятельного заполнения и примеры входных/выходных данных.

## Цель работы

Научиться организовывать проекты на языке C, разбивая их на несколько исходных файлов и используя GNU Make для автоматизации сборки. Задания должны помочь будущим учителям информатики:
- Освоить принципы модульного программирования.
- Научиться составлять Makefile для сборки многофайловых проектов.
- Разработать оригинальные учебные примеры, которые можно адаптировать для школьников.

## Требования к выполнению

**Структурированность.** Итоговый документ оформляется в формате Markdown с использованием заголовков, списков и кодовых блоков. Каждая задача представляется отдельной секцией.
**Код и комментарии.** Приводимые фрагменты кода должны содержать подробные комментарии, поясняющие логику решения (соблюдая стиль Google).
**Оригинальность.** Задания должны быть разработаны самостоятельно.

## Задача 1. База данных студентов (базовая система с Makefile)

### Описание задачи

Создайте многофайловый проект на C для управления базой данных студентов. Проект должен состоять как минимум из четырёх файлов:
- `student.h` – содержит определение структуры `Student` и прототипы функций для работы с отдельными студентами.
- `student.c` – реализует функции работы со студентами.
- `database.h` – содержит прототипы функций для управления базой данных (массивом студентов).
- `database.c` – реализует функции для управления базой данных.
- `main.c` – содержит функцию `main()` с консольным меню для взаимодействия с пользователем.

Также необходимо написать `Makefile` для автоматической сборки проекта.

### Алгоритмическое решение

1.  В файле `student.h` определить структуру `Student` с полями:
    - `id` – уникальный идентификатор (int)
    - `surname` – фамилия (char[50])
    - `name` – имя (char[50])
    - `group` – группа (char[20])
    - `average_score` – средний балл (float)
    - `birth_year` – год рождения (int)

    Объявить функции:
    - `void create_student(struct Student *s, ...)` – заполняет структуру данными.
    - `void print_student(const struct Student *s)` – выводит информацию о студенте.
    - `int calculate_age(const struct Student *s, int current_year)` – вычисляет возраст.

2.  В файле `database.h` объявить функции для работы с массивом студентов:
    - `void add_student(...)` – добавляет нового студента в базу.
    - `void delete_student(...)` – удаляет студента по ID.
    - `struct Student* find_student(...)` – ищет студента по фамилии.
    - `void print_all_students(...)` – выводит всех студентов.
    - `void save_to_file(...)` / `void load_from_file(...)` – (опционально) работа с файлом.

3.  Реализовать объявленные функции в соответствующих `.c` файлах.

4.  В `main.c` реализовать консольное меню с циклическим выбором операций:
    1. Добавить студента
    2. Удалить студента
    3. Найти студента
    4. Показать всех студентов
    0. Выход

5.  Написать `Makefile` с целями `all`, `clean`.

### Частичное решение

**Файл: student.h**
```c
#ifndef STUDENT_H
#define STUDENT_H

struct Student {
    int id;
    char surname[50];
    char name[50];
    char group[20];
    float average_score;
    int birth_year;
};

void create_student(struct Student *s, int id, const char *surname, const char *name, const char *group, float score, int year);
void print_student(const struct Student *s);
int calculate_age(const struct Student *s, int current_year);

#endif
```

**Файл: student.c**
```c
#include <stdio.h>
#include <string.h>
#include "student.h"

void create_student(struct Student *s, int id, const char *surname, const char *name, const char *group, float score, int year) {
    s->id = /* заполните: присвойте ID */;
    strcpy(s->surname, /* заполните: фамилия */);
    strcpy(s->name, name);
    strcpy(s->group, group);
    s->average_score = score;
    s->birth_year = year;
}

void print_student(const struct Student *s) {
    printf("ID: %d | %s %s | Группа: %s | Балл: %.2f | Год рождения: %d\n",
           /* заполните: выведите поля структуры */);
}
```

**Файл: database.h**
```c
#ifndef DATABASE_H
#define DATABASE_H
#include "student.h"

#define MAX_STUDENTS 100

void add_student(struct Student db[], int *count, struct Student new_student);
void delete_student(struct Student db[], int *count, int id);
struct Student* find_student(struct Student db[], int count, const char *surname);
void print_all_students(struct Student db[], int count);

#endif
```

**Файл: main.c (фрагмент меню)**
```c
#include <stdio.h>
#include "database.h"

int main() {
    struct Student database[MAX_STUDENTS];
    int count = 0;
    int choice;

    do {
        printf("\n1. Добавить студента\n");
        printf("2. Удалить студента\n");
        printf("3. Найти студента\n");
        printf("4. Показать всех\n");
        printf("0. Выход\n");
        printf("Выберите: ");
        scanf("%d", &choice);

        switch(choice) {
            case 1: {
                struct Student s;
                // ... запрос данных у пользователя ...
                // create_student(&s, ...);
                add_student(database, &count, s);
                break;
            }
            // ... остальные case ...
        }
    } while(choice != 0);

    return 0;
}
```

### Пример входных/выходных данных

**Вход через меню:**

```
Выберите: 1
Введите ID: 101
Введите фамилию: Иванов
Введите имя: Алексей
Введите группу: ИВТ-101
Введите средний балл: 4.5
Введите год рождения: 2002
```

**Выход (пункт меню 4):**

```
Список всех студентов:
ID: 101 | Иванов Алексей | Группа: ИВТ-101 | Балл: 4.50 | Год рождения: 2002
```

**Makefile:**

```
CC = gcc
CFLAGS = -Wall -O2
TARGET = student_db
OBJS = main.o student.o database.o

all: $(TARGET)

$(TARGET): $(OBJS)
	$(CC) -o $@ $^

%.o: %.c
	$(CC) $(CFLAGS) -c $<

clean:
	rm -f $(OBJS) $(TARGET)
```

## Задача 2. Простая библиотека книг с модулями

### Описание задачи

Создайте многофайловый проект для учёта библиотечного фонда. Проект должен состоять как минимум из пяти файлов:

* `book.h` – определение структуры `Book` и перечисления `BookStatus`.
* `catalog.c` – управление каталогом книг (добавление, удаление, поиск).
* `reader.c` – работа с читателями.
* `lending.c` – логика выдачи и возврата книг.
* `main.c` – точка входа с пользовательским интерфейсом.

Также необходим `Makefile` для сборки.

### Алгоритмическое решение

1. В `book.h` определить:
   * `enum BookStatus { AVAILABLE, BORROWED, RESERVED, LOST }`
   * `struct Book` с полями: `isbn[20]`, `title[100]`, `author[100]`, `year`, `status`

2. В `catalog.c` реализовать функции:
   * `add_book()`, `remove_book()`, `find_book_by_isbn()`, `list_available_books()`

3. В `reader.c` определить структуру `Reader` и функции:
   * `register_reader()`, `find_reader()`

4. В `lending.c` реализовать функции:
   * `lend_book()`, `return_book()`, `show_borrowed_books()`

5. В `main.c` создать простое меню для библиотекаря.
6. Написать `Makefile`.

### Частичное решение

**Файл: book.h**

```c
#ifndef BOOK_H
#define BOOK_H

typedef enum {
    AVAILABLE,
    BORROWED,
    RESERVED,
    LOST
} BookStatus;

struct Book {
    char isbn[20];
    char title[100];
    char author[100];
    int year;
    BookStatus status;
};

#endif
```

**Файл: catalog.c (фрагмент)**

```c
#include <string.h>
#include "book.h"

struct Book catalog[100];
int book_count = 0;

void add_book(const char *isbn, const char *title, const char *author, int year) {
    if(book_count >= 100) return;
    
    strcpy(catalog[book_count].isbn, /* заполните: ISBN */);
    strcpy(catalog[book_count].title, title);
    strcpy(catalog[book_count].author, author);
    catalog[book_count].year = year;
    catalog[book_count].status = AVAILABLE;
    
    book_count++;
}
```

**Файл: main.c (фрагмент меню)**

```c
printf("1. Добавить книгу\n");
printf("2. Выдать книгу\n");
printf("3. Вернуть книгу\n");
printf("4. Список доступных книг\n");
// ... обработка выбора ...
```

### Пример входных/выходных данных

**Ввод (добавление книги):**

```
ISBN: 978-5-699-12014-7
Название: Мастер и Маргарита
Автор: Михаил Булгаков
Год: 1966
```

**Вывод (список доступных):**

```
Доступные книги:
1. ISBN: 978-5-699-12014-7 | "Мастер и Маргарита" (Булгаков, 1966)
```

## Задача 3. Консольный текстовый редактор (базовая версия)

### Описание задачи

Создайте многофайловый проект простого консольного текстового редактора. Проект должен состоять как минимум из пяти файлов:

* `editor.h` – основные определения и константы.
* `buffer.c` – работа с текстовым буфером.
* `file_ops.c` – загрузка и сохранение файлов.
* `ui_console.c` – отрисовка интерфейса.
* `main.c` – главный цикл программы.

Также необходим `Makefile`.

### Алгоритмическое решение

1. В `editor.h` определить:
   * `struct TextBuffer` с полями: `char** lines`, `int line_count`, `int capacity`
   * Константы: `MAX_LINE_LEN`, `INITIAL_CAPACITY`

2. В `buffer.c` реализовать:
   * `init_buffer()`, `insert_line()`, `delete_line()`, `append_text()`

3. В `file_ops.c` реализовать:
   * `load_file()`, `save_file()`

4. В `ui_console.c` реализовать:
   * `draw_screen()`, `handle_input()`

5. В `main.c` организовать главный цикл редактора.

### Частичное решение

**Файл: editor.h**

```c
#ifndef EDITOR_H
#define EDITOR_H

#define MAX_LINE_LEN 256
#define INITIAL_CAPACITY 10

struct TextBuffer {
    char **lines;
    int line_count;
    int capacity;
};

void init_buffer(struct TextBuffer *buf);
void insert_line(struct TextBuffer *buf, int pos, const char *text);
void save_to_file(struct TextBuffer *buf, const char *filename);

#endif
```

**Файл: buffer.c (фрагмент)**

```c
#include <stdlib.h>
#include <string.h>
#include "editor.h"

void init_buffer(struct TextBuffer *buf) {
    buf->lines = malloc(INITIAL_CAPACITY * sizeof(char*));
    buf->capacity = /* заполните: начальная ёмкость */;
    buf->line_count = 0;
}

void insert_line(struct TextBuffer *buf, int pos, const char *text) {
    if(pos < 0 || pos > buf->line_count) return;
    
    // ... проверка необходимости расширения ...
    
    // Сдвиг строк
    for(int i = buf->line_count; i > pos; i--) {
        buf->lines[i] = buf->lines[i-1];
    }
    
    buf->lines[pos] = malloc(strlen(text) + 1);
    strcpy(/* заполните: куда копировать текст? */, text);
    buf->line_count++;
}
```

### Пример работы редактора

```
[Редактор v1.0] Файл: новый.txt
1>
2>
3>
---
Команды: i <строка> - вставка, s - сохранить, q - выход
> i 1 Привет, мир!
> i 2 Это вторая строка.
> s
Введите имя файла: test.txt
Сохранено.
```
