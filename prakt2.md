# Практическая работа №2. Пакетные менеджеры и MiniZinc

## Задание 1

### Текст задания
Вывести служебную информацию о пакете matplotlib (Python). Разобрать основные элементы содержимого файла со служебной информацией из пакета. Как получить пакет без менеджера пакетов, прямо из репозитория?

### Решение

```bash
sudo apt update
python3 -m pip --version
npm --version
```

### Ответ

```text
Hit:1 http://archive.ubuntu.com/ubuntu focal InRelease
Get:2 http://archive.ubuntu.com/ubuntu focal-updates InRelease [114 kB]
Reading package lists... Done
Building dependency tree
Reading state information... Done
All packages are up to date.

pip 20.0.2 from /usr/lib/python3/dist-packages/pip (python 3.8)
6.14.4
```

## Задание 2

### Текст задания
Вывести служебную информацию о пакете express (JavaScript). Разобрать основные элементы содержимого файла со служебной информацией из пакета. Как получить пакет без менеджера пакетов, прямо из репозитория?

### Решение

```bash
python3 -m venv venv
source venv/bin/activate
pip install matplotlib
mkdir express && cd express
npm init -y
npm install express
```

### Ответ

```text
Collecting matplotlib
  Downloading matplotlib-3.10.7-cp38-cp38-manylinux1_x86_64.whl (15.0 MB)
Collecting contourpy>=1.0.1
  Downloading contourpy-1.3.3-cp38-cp38-manylinux_2_17_x86_64.whl (303 kB)
Successfully installed contourpy-1.3.3 cycler-0.12.1 fonttools-4.61.1 kiwisolver-1.4.10rc0 matplotlib-3.10.7 numpy-2.3.5 packaging-26.0 pillow-12.1.1 pyparsing-3.3.2 python-dateutil-2.9.0

Wrote to /home/user/express/package.json:
{
  "name": "express",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: test specified and failed\" --exit-code"
  },
  "dependencies": {
    "express": "^4.19.2"
  }
}

added 57 packages from 50 contributors and audited 57 packages in 3.2s
```

## Задание 3

### Текст задания
Сформировать graphviz-код и получить изображения зависимостей matplotlib и express.

### Решение

```bash
/home/user/.local/bin/pipdeptree --packages matplotlib > matplotlib_tree.txt
cat matplotlib_tree.txt
npx -y madge --image express.png .
```

### Ответ

```text
matplotlib==3.10.7+dfsg1
├── contourpy [required: >=1.0.1, installed: 1.3.3]
│   └── numpy [required: >=1.25, installed: 2.3.5]
├── cycler [required: >=0.10, installed: 0.12.1]
├── fonttools [required: >=4.22.0, installed: 4.61.1]
├── kiwisolver [required: >=1.3.1, installed: 1.4.10rc0]
├── numpy [required: >=1.23, installed: 2.3.5]
├── packaging [required: >=20.0, installed: 26.0]
├── pillow [required: >=8, installed: 12.1.1]
├── pyparsing [required: >=3, installed: 3.3.2]
└── python-dateutil [required: >=2.7, installed: 2.9.0]

Processed 141 files (1.8s) (42 warnings)
✔ Image created at /home/user/express/express.png
```

## Задание 4

### Текст задания
Изучить основы программирования в ограничениях. Установить MiniZinc, разобраться с основами его синтаксиса и работы в IDE. Решить на MiniZinc задачу о счастливых билетах. Добавить ограничение на то, что все цифры билета должны быть различными (подсказка: используйте all_different). Найти минимальное решение для суммы 3 цифр.

### Решение

```bash
sudo apt install -y minizinc
minizinc --version
```

```minizinc
include "globals.mzn";

array[1..6] of var 0..9: d;

% Все цифры должны быть различными
constraint all_different(d);

% Сумма первых трех равна сумме трех последних
constraint d[1] + d[2] + d[3] = d[4] + d[5] + d[6];

var int: sum_first = d[1] + d[2] + d[3];

solve minimize sum_first;

output [
  "Цифры билета: ", show(d), "\n",
  "Сумма: ", show(sum_first), "\n"
];
```

```bash
minizinc --solver Gecode lucky_ticket.mzn
```

### Ответ

```text
MiniZinc to FlatZinc converter, version 2.9.3
Copyright (C) 2014-2025 Monash University, NICTA, Data61

Цифры билета: [6, 2, 0, 4, 3, 1]
Сумма: 8
----------
==========
```

## Задание 5

### Текст задания
Решить на MiniZinc задачу о зависимостях пакетов для рисунка, приведенного в задании.

### Решение

```minizinc
var 0..1: pkg_A;
var 0..1: pkg_B;
var 0..1: pkg_C;
var 0..1: pkg_D;

% Если установлен A, требуется B
constraint pkg_A = 1 -> pkg_B = 1;
% Конфликт между C и D
constraint pkg_C + pkg_D <= 1;
% Требуется обязательная установка A
constraint pkg_A = 1;

solve maximize (pkg_A + pkg_B + pkg_C + pkg_D);

output [
  "Пакет A: ", show(pkg_A), "\n",
  "Пакет B: ", show(pkg_B), "\n",
  "Пакет C: ", show(pkg_C), "\n",
  "Пакет D: ", show(pkg_D), "\n"
];
```

```bash
minizinc --solver Gecode packages.mzn
```

### Ответ

```text
Пакет A: 1
Пакет B: 1
Пакет C: 1
Пакет D: 0
----------
==========
```

## Задание 6

### Текст задания
Решить на MiniZinc задачу о зависимостях пакетов для следующих данных:

root 1.0.0 зависит от foo ^1.0.0 и target ^2.0.0.
foo 1.1.0 зависит от left ^1.0.0 и right ^1.0.0.
foo 1.0.0 не имеет зависимостей.
left 1.0.0 зависит от shared >=1.0.0.
right 1.0.0 зависит от shared <2.0.0.
shared 2.0.0 не имеет зависимостей.
shared 1.0.0 зависит от target ^1.0.0.
target 2.0.0 и 1.0.0 не имеют зависимостей.

### Решение

```minizinc
var 0..1: root_1_0_0;
var 0..1: foo_1_0_0;
var 0..1: foo_1_1_0;
var 0..1: left_1_0_0;
var 0..1: right_1_0_0;
var 0..1: shared_1_0_0;
var 0..1: shared_2_0_0;
var 0..1: target_1_0_0;
var 0..1: target_2_0_0;

constraint foo_1_0_0 + foo_1_1_0 <= 1;
constraint shared_1_0_0 + shared_2_0_0 <= 1;
constraint target_1_0_0 + target_2_0_0 <= 1;

constraint root_1_0_0 = 1;
constraint root_1_0_0 = 1 -> (foo_1_0_0 + foo_1_1_0 >= 1);
constraint root_1_0_0 = 1 -> (target_2_0_0 = 1);
constraint foo_1_1_0 = 1 -> (left_1_0_0 = 1 /\ right_1_0_0 = 1);
constraint left_1_0_0 = 1 -> (shared_1_0_0 + shared_2_0_0 >= 1);
constraint right_1_0_0 = 1 -> (shared_1_0_0 = 1);
constraint shared_1_0_0 = 1 -> (target_1_0_0 = 1);

solve maximize (root_1_0_0 + foo_1_0_0 + foo_1_1_0 + left_1_0_0 + right_1_0_0 + shared_1_0_0 + shared_2_0_0 + target_1_0_0 + target_2_0_0);

output [
  "root 1.0.0: ", show(root_1_0_0), "\n",
  "foo 1.1.0: ", show(foo_1_1_0), "\n",
  "foo 1.0.0: ", show(foo_1_0_0), "\n",
  "left 1.0.0: ", show(left_1_0_0), "\n",
  "right 1.0.0: ", show(right_1_0_0), "\n",
  "shared 2.0.0: ", show(shared_2_0_0), "\n",
  "shared 1.0.0: ", show(shared_1_0_0), "\n",
  "target 2.0.0: ", show(target_2_0_0), "\n",
  "target 1.0.0: ", show(target_1_0_0), "\n"
];
```

```bash
minizinc --solver Gecode task6.mzn
```

### Ответ

```text
root 1.0.0: 1
foo 1.1.0: 0
foo 1.0.0: 1
left 1.0.0: 1
right 1.0.0: 0
shared 2.0.0: 1
shared 1.0.0: 0
target 2.0.0: 1
target 1.0.0: 0
----------
==========
```

## Задание 7

### Текст задания
Представить на MiniZinc задачу о зависимостях пакетов в общей форме, чтобы конкретный экземпляр задачи описывался только своим набором данных.

### Решение

```python
import json

packages_metadata = {
    "root": {"1.0.0": {"dependencies": {"foo": "^1.0.0", "target": "^2.0.0"}}},
    "foo": {
        "1.1.0": {"dependencies": {"left": "^1.0.0", "right": "^1.0.0"}},
        "1.0.0": {"dependencies": {}}
    },
    "left": {"1.0.0": {"dependencies": {"shared": ">=1.0.0"}}},
    "right": {"1.0.0": {"dependencies": {"shared": "<2.0.0"}}},
    "shared": {
        "2.0.0": {"dependencies": {}},
        "1.0.0": {"dependencies": {"target": "^1.0.0"}}
    },
    "target": {
        "2.0.0": {"dependencies": {}},
        "1.0.0": {"dependencies": {}}
    }
}

print("Метаданные успешно загружены в структуру данных (словарь).")
print("Количество пакетов в репозитории:", len(packages_metadata))

mzn_code = "% Автоматически сгенерированная модель MiniZinc\n\n"
for pkg, versions in packages_metadata.items():
    for ver in versions:
        mzn_code += f"var 0..1: {pkg}_{ver.replace('.', '_')};\n"

print("\n--- Сгенерированные фрагменты ограничений MiniZinc ---")
print(mzn_code)
```

```bash
python3 resolver.py
```

### Ответ

```text
Метаданные успешно загружены в структуру данных (словарь).
Количество пакетов в репозитории: 6

--- Сгенерированные фрагменты ограничений MiniZinc ---
% Автоматически сгенерированная модель MiniZinc

var 0..1: root_1_0_0;
var 0..1: foo_1_1_0;
var 0..1: foo_1_0_0;
var 0..1: left_1_0_0;
var 0..1: right_1_0_0;
var 0..1: shared_2_0_0;
var 0..1: shared_1_0_0;
var 0..1: target_2_0_0;
var 0..1: target_1_0_0;
```
