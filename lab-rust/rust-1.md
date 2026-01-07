## **Лабораторная работа №1: Базовый синтаксис Rust**

### **Цель работы:** 
Освоить базовые конструкции языка Rust: переменные, функции, структуры, перечисления и основы управления памятью.

#### **Задача 1: Система учёта сотрудников**
Создайте программу для управления списком сотрудников. Реализуйте:

* Структуру `Employee` с полями: `id: u32`, `name: String`, `department: String`, `salary: f64`
* Функцию для добавления сотрудника в вектор
* Функцию для вывода всех сотрудников указанного отдела
* Подсчёт средней зарплаты по отделу

**Пример кода:**

```rust
#[derive(Debug)]
struct Employee {
    id: u32,
    name: String,
    department: String,
    salary: f64,
}

impl Employee {
    fn new(id: u32, name: &str, department: &str, salary: f64) -> Self {
        Employee {
            id,
            name: /* заполните */,
            department: department.to_string(),
            salary,
        }
    }
}

fn main() {
    let mut employees = Vec::new();
    employees.push(Employee::new(1, "Иванов", "IT", 85000.0));
    
    // Вывод сотрудников IT отдела
    for emp in &employees {
        if emp.department == "IT" {
            println!("{:?}", emp);
        }
    }
}
```
#### **Задача 2: Конвертер температур**
Создайте программу для конвертации температур между шкалами Цельсия и Фаренгейта:

* Определите перечисление `TemperatureScale` с вариантами `Celsius` и `Fahrenheit`
* Реализуйте функцию `convert(temperature: f64, from: TemperatureScale, to: TemperatureScale) -> f64`
* Добавьте обработку пользовательского ввода через консоль

**Пример работы:**

```
Выберите направление конвертации:
1. Цельсий → Фаренгейт
2. Фаренгейт → Цельсий
> 1
Введите температуру в °C: 25
25.0°C = 77.0°F
```

