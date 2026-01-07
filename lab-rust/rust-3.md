## **Лабораторная работа №3: Системы управления данными**

## 1. Задание
Разработать два консольных приложения на Rust, которые демонстрируют:

* Модульную организацию кода
* Обработку ошибок через `Result<T, E>` и собственные типы ошибок
* Сериализацию/десериализацию данных в JSON
* Работу с файлами для сохранения состояния

## 2. Теоретические сведения

**Модульная система Rust** позволяет разбивать код на логические единицы. Ключевое слово `mod` объявляет модуль, `pub` делает элементы публичными, `use` импортирует элементы в текущую область видимости.

**Обработка ошибок** осуществляется через типы `Result<T, E>` для операций, которые могут завершиться ошибкой, и `Option<T>` для значений, которые могут отсутствовать. Можно создавать собственные типы ошибок через перечисления.

**Сериализация** — процесс преобразования структур данных в формат, пригодный для хранения или передачи. Крейт `serde` с фичей `derive` позволяет автоматически генерировать код для сериализации/десериализации.

**Cargo** — система сборки и менеджер пакетов Rust. Файл `Cargo.toml` описывает проект и его зависимости.

## 3. Порядок выполнения работы

### Задача 1: Система управления инвентарём

#### Шаг 1: Создание проекта

```bash
cargo new inventory_system
cd inventory_system
```

#### Шаг 2: Добавление зависимостей

Откройте `Cargo.toml` и добавьте:

```toml
[package]
name = "inventory_system"
version = "0.1.0"
edition = "2021"

[dependencies]
serde = { version = "1.0", features = ["derive"] }
serde_json = "1.0"
```

#### Шаг 3: Создание модуля моделей

Создайте файл `src/models.rs`:

```rust
use serde::{Serialize, Deserialize};

/// Структура, представляющая товар в инвентаре
#[derive(Serialize, Deserialize, Debug, Clone)]
pub struct Item {
    pub id: u32,
    pub name: String,
    pub category: String,
    pub quantity: u32,
    pub price: f64,
}

/// Основная структура инвентаря
#[derive(Serialize, Deserialize)]
pub struct Inventory {
    items: Vec<Item>,
    next_id: u32,
}
```

#### Шаг 4: Создание модуля ошибок

Создайте файл `src/errors.rs`:

```rust
use std::fmt;

/// Пользовательские ошибки для системы инвентаря
#[derive(Debug)]
pub enum InventoryError {
    ItemNotFound,
    InsufficientQuantity,
    InvalidPrice,
    InvalidQuantity,
    DuplicateItem,
}

impl fmt::Display for InventoryError {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        match self {
            InventoryError::ItemNotFound => write!(f, "Товар не найден"),
            InventoryError::InsufficientQuantity => write!(f, "Недостаточно товара на складе"),
            /* заполните остальные варианты */
        }
    }
}
```

#### Шаг 5: Создание основной логики

Создайте файл `src/inventory.rs`:

```rust
use crate::models::{Item, Inventory};
use crate::errors::InventoryError;
use std::fs::File;
use std::io::{Read, Write};

impl Inventory {
    /// Создает новый пустой инвентарь
    pub fn new() -> Self {
        Inventory {
            items: Vec::new(),
            next_id: 1,
        }
    }
    
    /// Добавляет новый товар в инвентарь
    pub fn add_item(&mut self, name: String, category: String, quantity: u32, price: f64) -> u32 {
        let new_item = Item {
            id: self.next_id,
            name: /* заполните */,
            category,
            quantity,
            price,
        };
        self.items.push(new_item);
        self.next_id += 1;
        self.next_id - 1
    }
    
    /// Продает указанное количество товара
    pub fn sell_item(&mut self, item_id: u32, quantity: u32) -> Result<f64, InventoryError> {
        // Найти товар по ID
        let item = self.items
            .iter_mut()
            .find(|item| item.id == item_id)
            .ok_or(/* заполните какую ошибку? */)?;
        
        // Проверить достаточное количество
        if item.quantity < quantity {
            return Err(InventoryError::InsufficientQuantity);
        }
        
        // Обновить количество
        item.quantity -= quantity;
        
        // Вернуть стоимость продажи
        Ok(item.price * quantity as f64)
    }
    
    /// Сохраняет инвентарь в JSON-файл
    pub fn save_to_file(&self, filename: &str) -> Result<(), std::io::Error> {
        let json = serde_json::to_string_pretty(self)?;
        let mut file = File::create(filename)?;
        file.write_all(json.as_bytes())?;
        Ok(())
    }
    
    /// Загружает инвентарь из JSON-файла
    pub fn load_from_file(filename: &str) -> Result<Self, std::io::Error> {
        let mut file = File::open(filename)?;
        let mut contents = String::new();
        file.read_to_string(&mut contents)?;
        let inventory: Inventory = serde_json::from_str(&contents)?;
        Ok(inventory)
    }
}
```

#### Шаг 6: Главный файл с меню

Замените содержимое `src/main.rs`:

```rust
mod models;
mod errors;
mod inventory;

use crate::inventory::Inventory;
use std::io::{self, Write};

const DATA_FILE: &str = "inventory.json";

fn main() -> Result<(), Box<dyn std::error::Error>> {
    // Загрузка или создание нового инвентаря
    let mut inventory = match Inventory::load_from_file(DATA_FILE) {
        Ok(inv) => {
            println!("Данные загружены из файла");
            inv
        }
        Err(_) => {
            println!("Создан новый инвентарь");
            Inventory::new()
        }
    };
    
    loop {
        print_menu();
        let choice = read_input()?;
        
        match choice.trim() {
            "1" => add_item_ui(&mut inventory),
            "2" => sell_item_ui(&mut inventory),
            "3" => list_items_ui(&inventory),
            "4" => {
                inventory.save_to_file(DATA_FILE)?;
                println!("Данные сохранены");
            }
            "5" => {
                println!("Выход...");
                break;
            }
            _ => println!("Неверный выбор"),
        }
    }
    
    Ok(())
}

fn print_menu() {
    println!("\n=== Система управления инвентарём ===");
    println!("1. Добавить товар");
    println!("2. Продать товар");
    println!("3. Показать все товары");
    println!("4. Сохранить данные");
    println!("5. Выход");
    print!("Выберите действие: ");
    io::stdout().flush().unwrap();
}

fn read_input() -> Result<String, std::io::Error> {
    let mut input = String::new();
    io::stdin().read_line(&mut input)?;
    Ok(input)
}

fn add_item_ui(inventory: &mut Inventory) {
    println!("Введите название товара: ");
    let name = read_input().unwrap().trim().to_string();
    
    println!("Введите категорию: ");
    let category = read_input().unwrap().trim().to_string();
    
    println!("Введите количество: ");
    let quantity: u32 = read_input().unwrap().trim().parse().unwrap_or(0);
    
    println!("Введите цену: ");
    let price: f64 = read_input().unwrap().trim().parse().unwrap_or(0.0);
    
    let id = inventory.add_item(name, category, quantity, price);
    println!("Товар добавлен с ID: {}", id);
}
```

### Задача 2: Система бронирования билетов
#### Шаг 1: Создание проекта

```bash
cargo new booking_system
cd booking_system
```

#### Шаг 2: Структура проекта

Создайте следующую структуру файлов:

```
src/
├── main.rs
├── models.rs
├── booking.rs
└── errors.rs
```

#### Шаг 3: Модели данных (`src/models.rs`)

```rust
use serde::{Serialize, Deserialize};
use chrono::{DateTime, Utc};

#[derive(Serialize, Deserialize, Debug, Clone)]
pub struct Event {
    pub id: u32,
    pub name: String,
    pub venue: String,
    pub date: DateTime<Utc>,
    pub total_seats: u32,
    pub booked_seats: u32,
}

#[derive(Serialize, Deserialize, Debug, Clone)]
pub struct User {
    pub id: u32,
    pub username: String,
    pub email: String,
}

#[derive(Serialize, Deserialize, Debug)]
pub struct Booking {
    pub id: u32,
    pub user_id: u32,
    pub event_id: u32,
    pub seats: u32,
    pub booking_date: DateTime<Utc>,
}
```

#### Шаг 4: Обработка ошибок (`src/errors.rs`)

```rust
use std::fmt;

#[derive(Debug)]
pub enum BookingError {
    EventNotFound,
    UserNotFound,
    NotEnoughSeats,
    InvalidSeatCount,
    AlreadyBooked,
}

impl fmt::Display for BookingError {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        match self {
            BookingError::EventNotFound => write!(f, "Мероприятие не найдено"),
            BookingError::NotEnoughSeats => write!(f, "Недостаточно свободных мест"),
            /* заполните остальные варианты */
        }
    }
}

impl std::error::Error for BookingError {}
```

#### Шаг 5: Логика бронирования (`src/booking.rs`)

```rust
use crate::models::{Event, User, Booking};
use crate::errors::BookingError;
use chrono::Utc;
use std::collections::HashMap;

pub struct BookingSystem {
    events: HashMap<u32, Event>,
    users: HashMap<u32, User>,
    bookings: Vec<Booking>,
    next_event_id: u32,
    next_user_id: u32,
    next_booking_id: u32,
}

impl BookingSystem {
    pub fn new() -> Self {
        BookingSystem {
            events: HashMap::new(),
            users: HashMap::new(),
            bookings: Vec::new(),
            next_event_id: 1,
            next_user_id: 1,
            next_booking_id: 1,
        }
    }
    
    pub fn create_event(&mut self, name: String, venue: String, date: chrono::DateTime<Utc>, total_seats: u32) -> u32 {
        let event = Event {
            id: self.next_event_id,
            name,
            venue,
            date,
            total_seats,
            booked_seats: 0,
        };
        
        self.events.insert(self.next_event_id, event);
        self.next_event_id += 1;
        self.next_event_id - 1
    }
    
    pub fn book_tickets(&mut self, user_id: u32, event_id: u32, seats: u32) -> Result<u32, BookingError> {
        // Проверить существование пользователя
        if !self.users.contains_key(&user_id) {
            return Err(BookingError::UserNotFound);
        }
        
        // Найти мероприятие
        let event = self.events.get_mut(&event_id)
            .ok_or(BookingError::EventNotFound)?;
        
        // Проверить доступность мест
        if event.booked_seats + seats > event.total_seats {
            return Err(BookingError::NotEnoughSeats);
        }
        
        // Создать бронирование
        let booking = Booking {
            id: self.next_booking_id,
            user_id,
            event_id,
            seats,
            booking_date: Utc::now(),
        };
        
        // Обновить количество забронированных мест
        event.booked_seats += seats;
        
        // Сохранить бронирование
        self.bookings.push(booking);
        self.next_booking_id += 1;
        
        Ok(self.next_booking_id - 1)
    }
    
    pub fn get_available_seats(&self, event_id: u32) -> Result<u32, BookingError> {
        let event = self.events.get(&event_id)
            .ok_or(BookingError::EventNotFound)?;
        
        Ok(event.total_seats - event.booked_seats)
    }
}
```

#### Шаг 6: Главный файл (`src/main.rs`)

```rust
mod models;
mod errors;
mod booking;

use crate::booking::BookingSystem;
use chrono::{Utc, TimeZone};
use std::io::{self, Write};

fn main() -> Result<(), Box<dyn std::error::Error>> {
    let mut system = BookingSystem::new();
    
    // Пример создания тестовых данных
    let event_date = Utc.with_ymd_and_hms(2024, 12, 25, 19, 0, 0).unwrap();
    let event_id = system.create_event(
        "Новогодний концерт".to_string(),
        "Большой зал".to_string(),
        event_date,
        100
    );
    
    println!("Создано мероприятие с ID: {}", event_id);
    
    loop {
        println!("\n=== Система бронирования билетов ===");
        println!("1. Создать мероприятие");
        println!("2. Забронировать билеты");
        println!("3. Проверить доступные места");
        println!("4. Выход");
        print!("Выберите действие: ");
        io::stdout().flush()?;
        
        let mut choice = String::new();
        io::stdin().read_line(&mut choice)?;
        
        match choice.trim() {
            "1" => create_event_ui(&mut system),
            "2" => book_tickets_ui(&mut system),
            "3" => check_seats_ui(&system),
            "4" => break,
            _ => println!("Неверный выбор"),
        }
    }
    
    Ok(())
}

fn create_event_ui(system: &mut BookingSystem) {
    println!("Введите название мероприятия: ");
    let name = read_line();
    
    println!("Введите место проведения: ");
    let venue = read_line();
    
    println!("Введите общее количество мест: ");
    let seats: u32 = read_line().parse().unwrap_or(0);
    
    // Упрощенная дата (можно расширить для ввода даты от пользователя)
    let date = Utc::now();
    
    let id = system.create_event(name, venue, date, seats);
    println!("Мероприятие создано с ID: {}", id);
}

fn read_line() -> String {
    let mut input = String::new();
    io::stdin().read_line(&mut input).unwrap();
    input.trim().to_string()
}
```

## 4. Тестирование приложений

### Тест для системы инвентаря:

```bash
cd inventory_system
cargo run

# Тестовые действия:
# 1. Добавить товар: "Ноутбук", "Электроника", 10, 50000
# 2. Продать 3 ноутбука
# 3. Проверить, что количество уменьшилось до 7
# 4. Сохранить данные
# 5. Выйти и запустить снова - данные должны загрузиться
```

### Тест для системы бронирования:

```bash
cd booking_system
cargo run

# Тестовые действия:
# 1. Создать мероприятие на 50 мест
# 2. Забронировать 30 мест
# 3. Проверить, что осталось 20 свободных мест
# 4. Попробовать забронировать ещё 25 мест - должна быть ошибка
```

## 5. Контрольные вопросы

1. Почему в структуре `Inventory` поле `items` не объявлено как `pub`?
2. Какие преимущества даёт использование собственного типа ошибок `InventoryError` вместо `String`?
3. Как работает оператор `?` в методах `save_to_file` и `load_from_file`?
4. Почему в `BookingSystem` для событий используется `HashMap`, а для бронирований `Vec`?
5. Что делает аннотация `#[derive(Serialize, Deserialize)]` и зачем она нужна?
6. Как `chrono::DateTime<Utc>` помогает в работе с датами?
7. В чем разница между `Result<T, E>` и `Option<T>`? Когда что использовать?
8. Почему метод `book_tickets` возвращает `Result<u32, BookingError>`, а не просто `u32`?
9. Как организована модульная структура проекта? Какие файлы что содержат?
10. Что произойдёт, если при запуске файл `inventory.json` будет поврежден?

## 6. Задания для самостоятельной работы

### Задание 1 (базовый уровень)

Добавьте в систему инвентаря поле `description: String` для товаров и обновите все соответствующие методы.

### Задание 2 (базовый уровень)

Реализуйте функцию отмены бронирования в системе бронирования билетов.

### Задание 3 (средний уровень)

Добавьте возможность фильтрации товаров по категории и цене в системе инвентаря.

### Задание 4 (средний уровень)

Реализуйте систему скидок в бронировании: если пользователь бронирует более 10 мест, дайте скидку 10%.

### Задание 5 (продвинутый уровень)

Добавьте модульные тесты для обеих систем, проверяющие основные функции (добавление, продажа/бронирование, обработка ошибок).
