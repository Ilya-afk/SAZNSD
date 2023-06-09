# Сбор и аналитическая обработка информации о сетевом трафике

# Лабораторная работа №2

# Сбор и аналитическая обработка информации о сетевом трафике

### Цель работы

1.  Развить практические навыки использования современного стека
    инструментов сбора и аналитической обработки информации о сетвом
    трафике
2.  Освоить базовые подходы блокировки нежелательного сетевого трафика
3.  Закрепить знания о современных сетевых протоколах прикладного уровня

## Задание

1.  Собрать сетевой трафик (объемом не менее 100 Мб)
2.  Выделить метаинформацию сетевого трафика с помощью утилиты Zeek
3.  Собрать данные об источниках нежелательного трафика, например –
    https://github.com/StevenBlack
4.  Написать программу на любом удобном языке (python, bash, R …),
    котрая подсчитывает процент нежелательного трафика в собранном на
    этапе 1.
5.  Оформить отчет в соответствии с шаблоном

## Ход работы:

### 1 - Сбор сетевого трафика в файл traffic.pcapng

1.  Использовался сетевой анализатор – Wireshark
2.  Для сбора сетевого траффика я просто лазил по Интернету

![image](https://github.com/Ilya-afk/SAZNSD/assets/57459526/5252e93b-5ca8-4b56-84b1-53948d644dbe)

### 2 - Выделение метаинформации сетевого трафика с помощью утилиты Zeek

Zeek сортирует метаинформацию о трафике в соответствующие папки по
протоколам. Нас будет интересовать прежде всего папка DNS запросов.

``` bash
stepan@DodoBird ~/University/lab2 $ zeek -C -r traffic.pcapng
stepan@DodoBird ~/University/lab2 $ ls
conn.log  dhcp.log  dns.log  files.log  http.log  ocsp.log  packet_filter.log  ssl.log  traffic.pcapng  weird.log  x509.log
```

### 3 - Сбор данных об источниках нежелательного трафика из репозитория

``` bash
stepan@DodoBird ~/University/lab2 $ curl https://raw.githubusercontent.com/StevenBlack/hosts/master/hosts -o hosts
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 5925k  100 5925k    0     0  4829k      0  0:00:01  0:00:01 --:--:-- 4833k
stepan@DodoBird ~/University/lab2 $ ls
conn.log  dhcp.log  dns.log  files.log  hosts  http.log  ocsp.log  packet_filter.log  ssl.log  traffic.pcapng  weird.log  x509.log
```

### 4 - Подсчет трафика на языке Python

``` python
bad_hosts = [] # нежелательные хосты
with open('hosts') as file:
    for line in file.readlines()[40:]:
        if line[0] == '#':
            continue
        try:
            bad_hosts.append(line.split()[1])
        except IndexError:
            continue
        
hosts = [] # все хосты
with open('dns.log') as file:
    for line in file.readlines():
        if line[0] == '#':
            continue
        try:
            hosts.append(line.split()[9])
        except IndexError:
            continue

bad_count = len([host for host in hosts if host in bad_hosts])
percentile = round(bad_count/len(hosts),3)*100
print("Количество нежелательных хостов: {}.".format(str(bad_count)),
"Процент нежелательного трафика: {}%.".format(str(percentile)),sep='\n')
```

##### Процент нежелательного трафика:

``` C:\Users\ivodo\PycharmProjects\trafic\venv\Scripts\python.exe C:\Users\ivodo\PycharmProjects\trafic\main.py 
Количество нежелательных хостов: 11.
Процент нежелательного трафика: 4.0%.

Process finished with exit code 0
```

## Оценка результата

В результате лабораторной работы научились определять нежелательный
трафик.

## Вывод

Таким образом, мы научились анализировать сетевой трафик.
