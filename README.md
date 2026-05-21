# Лабораторная работа №7
# Анализ и преобразование кода с использованием Clang и LLVM

---

# Цель лабораторной работы

Познакомиться с инструментарием Clang и LLVM, освоить получение абстрактного синтаксического дерева (AST) и промежуточного представления (LLVM IR) для кода на C/C++, научиться применять базовые оптимизации, строить графы потока управления (CFG), а также анализировать влияние оптимизаций на различные синтаксические конструкции языка.

---

# Постановка задачи

Необходимо:

- Установить Clang и LLVM;
- Скомпилировать простой C-файл с использованием `clang` и получить:
  - AST;
  - LLVM IR;
- Использовать `opt` для применения оптимизации `-O2`;
- Построить CFG для оптимизированной программы;
- Проанализировать результат;
- Ответить на контрольные вопросы;
- Выполнить индивидуальное задание.

---

# 1. Общая часть работы

## 1.1 Установка среды

Установка Clang, LLVM, `opt` и Graphviz:

```bash
sudo apt install clang llvm
sudo apt install graphviz
```
<img width="576" height="278" alt="image" src="https://github.com/user-attachments/assets/b83d1995-d4ad-4741-acdf-76f1a6fdc997" />


<img width="628" height="188" alt="image" src="https://github.com/user-attachments/assets/a27e58da-bcbf-4569-8a0d-2ae8bf1c6cd1" />


---

## 1.2 Исходный код

Создан файл `main.c`:

```c
#include <stdio.h>

int square(int x) {
    return x * x;
}

int main() {
    int a = 5;
    int b = square(a);

    printf("%d\n", b);

    return 0;
}
```
<img width="616" height="226" alt="image" src="https://github.com/user-attachments/assets/afb218c9-2bca-4dc0-b34d-9309016fba48" />


---

## 1.3 Работа с AST

Получение AST:

```bash
clang -Xclang -ast-dump -fsyntax-only main.c
```

<img width="624" height="372" alt="image" src="https://github.com/user-attachments/assets/a6f2e044-3fa3-4166-845f-242383b4b98b" />


---

## 1.4 Генерация LLVM IR

Получение LLVM IR:

```bash
clang -S -emit-llvm main.c -o main.ll
```
<img width="550" height="343" alt="image" src="https://github.com/user-attachments/assets/1362268c-4f18-4db3-ac2b-541eb26c8919" />


---

## 1.5 Оптимизация IR

### LLVM IR без оптимизаций

```bash
clang -O0 -S -emit-llvm main.c -o main_O0.ll
```
<img width="585" height="342" alt="image" src="https://github.com/user-attachments/assets/e8baa546-1664-4ca3-9f0b-d769c361345c" />


### LLVM IR с оптимизацией `-O2`

```bash
clang -O2 -S -emit-llvm main.c -o main_O2.ll
```
<img width="622" height="389" alt="image" src="https://github.com/user-attachments/assets/48f7f032-6660-45a8-9249-31c5f6b15691" />


### Сравнение файлов

```bash
diff main_O0.ll main_O2.ll
```
<img width="619" height="392" alt="image" src="https://github.com/user-attachments/assets/ac31aaae-fbb4-46d1-9da1-a9533e05f1ee" />


---

## 1.6 Построение CFG

Генерация CFG:

```bash
opt -passes="dot-cfg" main_O2.ll -disable-output
```
<img width="146" height="25" alt="image" src="https://github.com/user-attachments/assets/8cb5ce0b-52aa-4878-9925-2f7efeaf22df" />


Поиск `.dot` файлов:

```bash
find . -name "*dot"
```
<img width="92" height="29" alt="image" src="https://github.com/user-attachments/assets/d2714770-cbdb-4081-9ec6-6b703122e477" />


### Преобразование DOT в PNG

```bash
dot -Tpng .main.dot -o cfg_main.png
dot -Tpng .square.dot -o cfg_square.png
```
<img width="248" height="27" alt="image" src="https://github.com/user-attachments/assets/9d56978f-8a65-4e44-8a3f-6da9ecfb42e1" />

### Открытие графов

```bash
xdg-open cfg_main.png
xdg-open cfg_square.png
```

<img width="500" height="99" alt="image" src="https://github.com/user-attachments/assets/b045816b-afbe-4a06-90ea-69f5525108eb" />
<img width="224" height="90" alt="image" src="https://github.com/user-attachments/assets/f41a626a-8c22-44a9-9e4f-5e50d34b5503" />



---

## 1.7 Выводы

В ходе работы было установлено:

- Clang позволяет получать AST и LLVM IR;
- LLVM предоставляет инструменты анализа и оптимизации;
- LLVM IR удобно использовать для компиляторных преобразований;
- CFG помогает анализировать выполнение программы.

---

# 2. Индивидуальное задание

## 2.1 Создание файла

Создание файла `array.cpp`:

```bash
nano array.cpp
```

<img width="298" height="169" alt="image" src="https://github.com/user-attachments/assets/c224b436-8b6e-4160-b1e5-4b6d8a08c548" />


---

## 2.2 Получение IR (`-O0` и `-O2`)

### Без оптимизации (`-O0`)

```bash
clang++ -O0 -S -emit-llvm array.cpp -o array_O0.ll
```

<img width="625" height="366" alt="image" src="https://github.com/user-attachments/assets/6cd944e1-65b2-49eb-8a08-21a07039213a" />


### С оптимизацией (`-O2`)

```bash
clang++ -O2 -S -emit-llvm array.cpp -o array_O2.ll
```

<img width="624" height="357" alt="image" src="https://github.com/user-attachments/assets/918beb9f-bce8-4666-b33d-959d421b300a" />


---

## 2.3 Исследование развёртывания цикла

Генерация LLVM IR с флагом `-funroll-loops`:

```bash
clang++ -O2 -funroll-loops -S -emit-llvm array.cpp -o array_unroll.ll
```

Сравнение файлов:

```bash
diff array_O2.ll array_unroll.ll
```

<img width="443" height="29" alt="image" src="https://github.com/user-attachments/assets/e927a235-e4d4-41c5-a745-d86b66dc8f23" />


### Вывод

Сравнение IR показало отсутствие изменений. Это означает, что цикл был полностью удалён компилятором после оптимизации, так как сумма элементов массива была вычислена заранее.

---

## 2.4 Построение CFG

### CFG без оптимизаций (`-O0`)

```bash
clang++ -O0 -S -emit-llvm array.cpp -o array_O0.ll

opt -passes="dot-cfg" array_O0.ll -disable-output

dot -Tpng .main.dot -o cfg.png

xdg-open cfg.png
```

<img width="1845" height="1007" alt="image" src="https://github.com/user-attachments/assets/e558ea1a-2b2a-4922-8be8-8e8f8d36362c" />


### CFG после оптимизации (`-O2`)

```bash
clang++ -O2 -S -emit-llvm array.cpp -o array_O2.ll

opt -passes="dot-cfg" array_O2.ll -disable-output

dot -Tpng .main.dot -o cfg_O2.png

xdg-open cfg_O2.png
```

<img width="1761" height="1017" alt="image" src="https://github.com/user-attachments/assets/d05afed8-832f-401e-b5bf-daf475c92cc4" />


---

## 2.5 Применение `-loop-rotate` и `-licm`

### Генерация IR без оптимизации

```bash
clang++ -O0 -S -emit-llvm array.cpp -o base.ll
```

### Применение оптимизаций

```bash
opt -passes="loop-rotate,licm" base.ll -S -o opt_loops.ll
```

### Просмотр результата

```bash
nano opt_loops.ll
```

<img width="622" height="365" alt="image" src="https://github.com/user-attachments/assets/e2a5e4e7-805c-4ff7-833a-a89f58d20388" />


---

## 2.6 Вывод: какие оптимизации применились к массиву и циклу

В ходе анализа было установлено, что LLVM применяет ряд оптимизаций к работе с массивами и циклами.

### Применённые оптимизации

- Выполнена свёртка констант;
- Сумма элементов массива вычислена на этапе компиляции;
- Цикл полностью устранён;
- Удалены лишние операции `alloca`, `load`, `store`;
- `LICM` выносит неизменяемые вычисления за пределы цикла;
- `loop-rotate` изменяет структуру цикла.

### Общий вывод

LLVM эффективно оптимизирует массивы и циклы, вплоть до полного удаления цикла при заранее известном результате вычислений.

---

# Контрольные вопросы

## 1. Что такое Clang и какова его роль в процессе компиляции программ?

Clang — это фронтенд компилятора для языков C/C++, который выполняет разбор исходного кода и генерацию промежуточного представления.

---

## 2. Что представляет собой LLVM и как он используется в современных компиляторах?

LLVM — это инфраструктура компилятора, предназначенная для оптимизации и генерации машинного кода.

---

## 3. Чем отличается AST от LLVM IR?

AST отражает синтаксическую структуру программы, а LLVM IR — низкоуровневое представление для анализа и оптимизации.

---

## 4. Для чего необходимо промежуточное представление (IR)?

IR используется для анализа и оптимизации программы перед генерацией машинного кода.

---

## 5. Что делает инструкция `alloca` в LLVM IR?

`alloca` выделяет память под локальные переменные в стеке функции.

---

## 6. Зачем нужна оптимизация кода?

Оптимизация повышает производительность программы, уменьшает объём кода и устраняет лишние операции.

---

## 7. Что такое SSA-форма?

SSA (Static Single Assignment) — форма представления, где каждая переменная присваивается только один раз.

---

## 8. Что такое CFG?

CFG (Control Flow Graph) — граф потока управления, отображающий переходы между блоками программы.

---

## 9. Как представлены арифметические операции в LLVM IR?

Операции представлены инструкциями трёхадресного кода:

```llvm
%res = mul i32 %a, %b
```

Каждая операция работает с типизированными значениями и соответствует SSA-форме.

---

## 10. Почему функции являются отдельными единицами анализа?

Каждая функция имеет собственную область видимости и может оптимизироваться независимо.

---

## 11. Что происходит с короткой функцией, вызываемой один раз?

Она может быть встроена (`inline`) в место вызова и затем удалена.

---

## 12. Какие преимущества дают IR и CFG?

IR и CFG позволяют:

- выполнять автоматические оптимизации;
- упростить анализ программы;
- работать независимо от языка программирования;
- повысить эффективность компиляции.

  # Оптимизация промежуточного представления (IR)

## Описание

После выполнения лексического, синтаксического и семантического анализа программа генерирует промежуточное представление (IR — Intermediate Representation).

На данном этапе выполняются локальные оптимизации, направленные на упрощение IR-кода без изменения семантики программы.

Все оптимизации:

- являются локальными;
- работают только внутри одной конструкции `Dictionary<int, string>`;
- не требуют анализа других частей программы;
- сохраняют исходную семантику;
- упрощают и канонизируют IR-код.

---

# Реализованные оптимизации

---

## 1. Удаление дубликатов IR-инструкций

### Описание

Если в промежуточном коде встречаются одинаковые инструкции, повторяющиеся строки удаляются.

Это позволяет:

- уменьшить размер IR;
- убрать избыточные операции;
- упростить дальнейшую обработку.

---

### Пример

#### До оптимизации

```llvm
t1 = 10 => "Phone"
t2 = 20 => ""
t1 = 10 => "Phone"
t3 = 30 => "Laptop"
```

#### После оптимизации

```llvm
t1 = 10 => "Phone"
t2 = 20 => ""
t3 = 30 => "Laptop"
```

---



---

# 2. Удаление пустых инструкций

## Описание

Если после генерации IR появляются пустые строки или строки, содержащие только пробелы, они удаляются.

Оптимизация улучшает:

- читаемость IR;
- компактность кода;
- качество отображения результата.

---

## Пример

### До оптимизации

```llvm
t1 = 10 => "Phone"

t2 = 20 => ""

```

### После оптимизации

```llvm
t1 = 10 => "Phone"
t2 = 20 => ""
```

---

## Блок-схема оптимизации


# 3. Канонизация элементов Dictionary

## Описание

Во время семантического анализа выполняется удаление повторяющихся ключей словаря.

Сохраняется только первое вхождение ключа.

Это обеспечивает:

- корректность структуры словаря;
- канонический вид данных;
- устранение неоднозначностей.

---

## Пример

### Исходный код

```csharp
Dictionary<int, string> products = new Dictionary<int, string>
{
    {10, "Phone"},
    {10, "DuplicatePhone"},
    {30, "Laptop"}
};
```

### После оптимизации

```llvm
t1 = 10 => "Phone"
t2 = 30 => "Laptop"
```

---


# Свертка констант

## Описание

Так как конструкция `Dictionary<int, string>` содержит только константные значения (`int` и `string`), вычисление значений выполняется на этапе компиляции.

IR уже содержит готовые константы:

```llvm
t1 = 10 => "Phone"
```

Дополнительных вычислений во время выполнения программы не требуется.

---

# Пример полной оптимизации

## Исходный код

```csharp
Dictionary<int, string> products = new Dictionary<int, string>
{
    {10, "Phone"},
    {20, ""},
    {10, "DuplicatePhone"},
    {30, "Laptop"},
    {40, ""},
    {30, "DuplicateLaptop"}
};
```
<img width="542" height="303" alt="image" src="https://github.com/user-attachments/assets/a5b62c85-d7d3-4917-9141-81ebdeafc73e" />

---


## Optimized IR

<img width="604" height="479" alt="image" src="https://github.com/user-attachments/assets/5c49e042-b423-464c-b4ba-b4b2fee458ef" />


---

# Реализация оптимизатора

## Класс `IROptimizer`

```csharp
public class IROptimizer
{
    public List<string> Optimize(List<string> instructions)
    {
        List<string> result = new();

        HashSet<string> seen = new();

        foreach (string instruction in instructions)
        {
            string normalized = instruction.Trim();

            // удаление дубликатов
            if (seen.Contains(normalized))
                continue;

            seen.Add(normalized);

            // удаление пустых строк
            if (string.IsNullOrWhiteSpace(normalized))
                continue;

            result.Add(normalized);
        }

        return result;
    }
}
```

---

# Итог

В ходе лабораторной работы был реализован локальный оптимизатор промежуточного представления для конструкции:

```csharp
Dictionary<int, string>
```

Реализованные оптимизации:

- удаление дубликатов IR;
- удаление пустых инструкций;
- канонизация элементов словаря;
- свертка констант.

Оптимизации уменьшают размер промежуточного кода, делают его более читаемым и подготавливают IR к дальнейшей компиляции.
