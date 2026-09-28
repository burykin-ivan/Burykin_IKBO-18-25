# Практическая работа №1. Командная строка

## Задание 1

```bash
grep -o '^[^:]*' /etc/passwd | sort
```

## Задание 2

```bash
sort -k2 -nr /etc/protocols | head -5
```

## Задание 3

```bash
#!/bin/bash

text="$1"
len=${#text}

echo -n "+"
for (( i=0; i<len+2; i++ )); do
    echo -n "-"
done
echo "+"

echo "| $text |"

echo -n "+"
for (( i=0; i<len+2; i++ )); do
    echo -n "-"
done
echo "+"
```

## Задание 4

```bash
#!/bin/bash

if [ -z "$1" ]; then
    echo "Использование: $0 <файл>"
    exit 1
fi

grep -oE '[a-zA-Z_][a-zA-Z0-9_]*' "$1" | sort -u
```

## Задание 5

```bash
#!/bin/bash

if [ -z "$1" ]; then
    echo "Использование: $0 <имя_файла>"
    exit 1
fi

file="$1"

if [ ! -f "$file" ]; then
    echo "Ошибка: файл '$file' не найден."
    exit 1
fi

chmod +x "$file"
sudo cp "$file" /usr/local/bin/

echo "Команда '$file' успешно зарегистрирована в /usr/local/bin."
```

## Задание 6

```bash
#!/bin/bash

dir="${1:-.}"

find "$dir" -type f \( -name "*.c" -o -name "*.js" -o -name "*.py" \) | while read -r file; do
    first_line=$(head -n 1 "$file")
    case "$file" in
        *.c|*.js)
            if [[ "$first_line" =~ ^[[:space:]]*(//|/\*) ]]; then
                echo "$file: комментарий есть"
            else
                echo "$file: комментария нет"
            fi
            ;;
        *.py)
            if [[ "$first_line" =~ ^[[:space:]]*# ]]; then
                echo "$file: комментарий есть"
            else
                echo "$file: комментария нет"
            fi
            ;;
    esac
done
```

## Задание 7

```bash
#!/bin/bash

if [ -z "$1" ]; then
    echo "Использование: $0 <каталог>"
    exit 1
fi

find "$1" -type f -exec md5sum {} + | sort | awk '
{
    if ($1 == prev) {
        if (!printed) { print "Дубликаты:"; print prev_line; printed=1 }
        print $0
    } else {
        printed=0
        prev=$1
        prev_line=$0
    }
}'
```

## Задание 8

```bash
#!/bin/bash

if [ $# -ne 2 ]; then
    echo "Использование: $0 <каталог> <расширение>"
    exit 1
fi

dir="$1"
ext="${2#.}"

find "$dir" -type f -name "*.$ext" -print0 | tar --null -T - -cvf "archive_$ext.tar"
```

## Задание 9

```bash
#!/bin/bash

if [ $# -ne 2 ]; then
    echo "Использование: $0 <входной_файл> <выходной_файл>"
    exit 1
fi

sed 's/    /\t/g' "$1" > "$2"
```

## Задание 10

```bash
#!/bin/bash

if [ -z "$1" ]; then
    echo "Использование: $0 <каталог>"
    exit 1
fi

find "$1" -type f -empty
```
