## **Лабораторная работа №2: Владение (Ownership) и трейты**

### **Цель работы:** 
Изучить систему владения Rust, работу с ссылками и реализацию трейтов.

#### **Задача 1: Умный указатель для строк**
Создайте структуру `SmartString`, которая:

* Содержит поле `data: String`
* Реализует трейт `Deref` для неявного преобразования в `&str`
* Реализует метод `append(&mut self, text: &str)` для добавления текста
* Гарантирует, что длина строки не превышает 100 символов

**Частичное решение:**

```rust
use std::ops::Deref;

struct SmartString {
    data: String,
}

impl Deref for SmartString {
    type Target = str;
    
    fn deref(&self) -> &Self::Target {
        &self.data
    }
}

impl SmartString {
    fn new() -> Self {
        SmartString {
            data: String::new(),
        }
    }
    
    fn append(&mut self, text: &str) {
        if self.data.len() + text.len() <= 100 {
            self.data.push_str(/* заполните */);
        } else {
            println!("Превышена максимальная длина!");
        }
    }
}
```

#### **Задача 2: Трейт для геометрических фигур**
Создайте трейт `Shape` с методами:

* `area(&self) -> f64` — вычисление площади
* `perimeter(&self) -> f64` — вычисление периметра
* `describe(&self)` — вывод описания фигуры

Реализуйте трейт для структур:

* `Circle { radius: f64 }`
* `Rectangle { width: f64, height: f64 }`
* `Triangle { a: f64, b: f64, c: f64 }`

**Пример использования:**

```rust
let shapes: Vec<Box<dyn Shape>> = vec![
    Box::new(Circle { radius: 5.0 }),
    Box::new(Rectangle { width: 4.0, height: 6.0 }),
];

for shape in shapes {
    println!("Площадь: {:.2}", shape.area());
}
```
