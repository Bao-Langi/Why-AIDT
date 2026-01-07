# Установка и настройка среды разработки
## Компиляторы и инструменты
### Для Windows

**MSYS2 с GCC (рекомендуется)**:

1. Скачайте MSYS2 с [официального сайта](https://www.msys2.org/).
2. Запустите терминал **UCRT64**.
3. Установите компилятор и инструменты:
```
pacman -S --needed base-devel mingw-w64-ucrt-x86_64-toolchain
```

**MinGW-w64 (альтернатива)**:

* Скачайте установщик с [SourceForge](https://sourceforge.net/projects/mingw-w64/).

**WSL2 (Ubuntu) для продвинутых**:

1. Установите WSL2 через Microsoft Store.
2. В терминале WSL выполните:
```
sudo apt update
sudo apt install build-essential
```

### Для macOS

1. Установите Homebrew:
```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```
2. Установите GCC:
   ```
   brew install gcc
   ```

### Для Linux

* **Ubuntu/Debian**:
  ```
  sudo apt update
  sudo apt install build-essential
  ```

* **Arch Linux**:
  ```
  sudo pacman -S gcc
  ```

* **Fedora**:
  ```
  sudo dnf install gcc
  ```

## Проверка установки GCC

### Windows

1. Откройте командную строку (Win + R → `cmd`).
2. Проверьте версию:
   ```
   gcc --version
   ```
3. Если возникает ошибка `'gcc' is not recognized`:
   * Добавьте путь к GCC в переменную **PATH**:
     * Для MSYS2: `C:\msys64\ucrt64\bin`
     * Для MinGW: `C:\MinGW\bin`

   * Перезапустите командную строку.

### macOS

1. Откройте терминал.
2. Проверьте версию:
   ```
   gcc --version
   ```
   Если установлен через Homebrew, используйте:
   ```
   gcc-13 --version
   ```
3. При необходимости добавьте путь в `PATH`:
   ```
   export PATH="/usr/local/bin:$PATH"
   # или для Apple Silicon:
   export PATH="/opt/homebrew/bin:$PATH"
   ```

### Linux

1. Откройте терминал.
2. Проверьте версию:
   ```
   gcc --version
   ```
   Обычно не требует дополнительной настройки PATH.

## Настройка среды разработки

### Visual Studio Code / Cursor

1. Установите необходимые расширения:

   * **C/C++** (Microsoft)
   * **C/C++ Extension Pack**
   * **Code Runner**

2. Настройка пути к компилятору:\
   Создайте файл `.vscode/c_cpp_properties.json`:
   ```
   {
       "configurations": [
           {
               "name": "Win32",
               "compilerPath": "C:/msys64/ucrt64/bin/gcc.exe",
               "cStandard": "c17",
               "intelliSenseMode": "windows-gcc-x64"
           }
       ],
       "version": 4
   }
   ```

### Другие среды разработки

* **VSCodium** – Open Source версия VS Code.
* **CLion** – от JetBrains (бесплатно для студентов).
* **Code::Blocks** – включает MinGW.
* **Xcode** – для macOS.
