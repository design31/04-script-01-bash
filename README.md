### Как сдавать задания

Вы уже изучили блок «Системы управления версиями», и начиная с этого занятия все ваши работы будут приниматься ссылками на .md-файлы, размещённые в вашем публичном репозитории.

Скопируйте в свой .md-файл содержимое этого файла; исходники можно посмотреть [здесь](https://raw.githubusercontent.com/netology-code/sysadm-homeworks/devsys10/04-script-01-bash/README.md). Заполните недостающие части документа решением задач (заменяйте `???`, ОСТАЛЬНОЕ В ШАБЛОНЕ НЕ ТРОГАЙТЕ чтобы не сломать форматирование текста, подсветку синтаксиса и прочее, иначе можно отправиться на доработку) и отправляйте на проверку. Вместо логов можно вставить скриншоты по желани.

---


# Домашнее задание к занятию "4.1. Командная оболочка Bash: Практические навыки"

## Обязательная задача 1

Есть скрипт:
```bash
a=1
b=2
c=a+b
d=$a+$b
e=$(($a+$b))
```

Какие значения переменным c,d,e будут присвоены? Почему?

| Переменная  | Значение | Обоснование |
| ------------- | ------------- | ------------- |
| `a`  | a+b  | Переменная `с` будет равна `a+b` поскольку она умолчанию определена как строковая переменная |
| `b`  | 1+2  | Переменная `d` будет равна `1+2` поскольку a и b это простые числовые значения, а bash выводит выражение как последовательность переменных и знак `+`здесь выводится как текст. |
| `c`  | 3  | Переменная `e` будет равна `3` поскольку выражение заключено в двойные скобки и bash уже может посчитать формулу |

---

## Обязательная задача 2
На нашем локальном сервере упал сервис и мы написали скрипт, который постоянно проверяет его доступность, записывая дату проверок до тех пор, пока сервис не станет доступным (после чего скрипт должен завершиться). В скрипте допущена ошибка, из-за которой выполнение не может завершиться, при этом место на Жёстком Диске постоянно уменьшается. Что необходимо сделать, чтобы его исправить:
```bash
while ((1==1)
do
	curl https://localhost:4757
	if (($? != 0))
	then
		date >> curl.log
	fi
done
```
```bash
while ((1==1))
do
	curl https://localhost:4757
	if (($? != 0))
	then
		sleep 1
		date > curl.log
	else
	        echo site is available!
	fi
done
```

Во второй строке не хватало скобки, я также добавил паузу в 1сек для того чтобы ограничить кол-во ресурсов для скрипта.  
Ну и также вывода команды `date` будет достаточно одного в данном случае, чтобы понять в какой момент сайт стал доступен.  
Хотя можно было и дописывать лог с шагом в 1 сек, это не так много места займёт. И есть ещё вариант с logrotate для контроля размера логов.

---

Необходимо написать скрипт, который проверяет доступность трёх IP: `192.168.0.1`, `173.194.222.113`, `87.250.250.242` по `80` порту и записывает результат в файл `log`. Проверять доступность необходимо пять раз для каждого узла.

### Ваш скрипт:

Проверять доступность решил с помощью netstat:

Поправленный скрипт с использованием циклов:
```bash
#!/usr/bin/env bash
ip=("192.168.5.108" "192.168.5.162" "192.168.5.2")
port=80
a=5
while (($a > 0))
do
  for i in ${ip[@]}
    do
      date >> nc.log
      nc -zv $i $port &>> nc.log
    done
    let "a=a-1"
done
cat -n nc.log #выведем итог и пронумеруем строки для наглядности
```
На выходе получается что-то вроде:
```
us@ubuntu:~$ ./script.sh
     1  Вт 14 дек 2021 12:23:26 +07
     2  Connection to 192.168.5.108 80 port [tcp/http] succeeded!
     3  Вт 14 дек 2021 12:23:27 +07
     4  Connection to 192.168.5.162 80 port [tcp/http] succeeded!
     5  Вт 14 дек 2021 12:23:28 +07
     6  Connection to 192.168.5.2 80 port [tcp/http] succeeded!
     7  Вт 14 дек 2021 12:23:29 +07
     8  Connection to 192.168.5.108 80 port [tcp/http] succeeded!
     9  Вт 14 дек 2021 12:23:30 +07
    10  Connection to 192.168.5.162 80 port [tcp/http] succeeded!
    11  Вт 14 дек 2021 12:23:31 +07
    12  Connection to 192.168.5.2 80 port [tcp/http] succeeded!
    13  Вт 14 дек 2021 12:23:32 +07
    14  Connection to 192.168.5.108 80 port [tcp/http] succeeded!
    15  Вт 14 дек 2021 12:23:33 +07
    16  Connection to 192.168.5.162 80 port [tcp/http] succeeded!
    17  Вт 14 дек 2021 12:23:34 +07
    18  Connection to 192.168.5.2 80 port [tcp/http] succeeded!
    19  Вт 14 дек 2021 12:23:35 +07
    20  Connection to 192.168.5.108 80 port [tcp/http] succeeded!
    21  Вт 14 дек 2021 12:23:36 +07
    22  Connection to 192.168.5.162 80 port [tcp/http] succeeded!
    23  Вт 14 дек 2021 12:23:37 +07
    24  Connection to 192.168.5.2 80 port [tcp/http] succeeded!
    25  Вт 14 дек 2021 12:23:38 +07
    26  Connection to 192.168.5.108 80 port [tcp/http] succeeded!
    27  Вт 14 дек 2021 12:23:39 +07
    28  Connection to 192.168.5.162 80 port [tcp/http] succeeded!
    29  Вт 14 дек 2021 12:23:40 +07
    30  Connection to 192.168.5.2 80 port [tcp/http] succeeded!
```

## Обязательная задача 3
Необходимо дописать скрипт из предыдущего задания так, чтобы он выполнялся до тех пор, пока один из узлов не окажется недоступным. Если любой из узлов недоступен - IP этого узла пишется в файл error, скрипт прерывается.

### Ваш скрипт:
Поправленный скрипт с использованием циклов:
```bash
#!/usr/bin/env bash
ip=("192.168.5.108" "192.168.5.162" "192.168.5.2")
port=80

while ((1==1))
do
for i in ${ip[@]}
  do
    nc -zv $i $port
    if (($? != 0))
    then
      echo $i > error
      exit 1
    fi
  done
done
```


Проверим лог ошибок. Я остановил apache2 и гляну на error.log:
```
us@ubuntu:~$ cat error
192.168.5.162
```

--- 

## Дополнительное задание (со звездочкой*) - необязательно к выполнению

Мы хотим, чтобы у нас были красивые сообщения для коммитов в репозиторий. Для этого нужно написать локальный хук для git, который будет проверять, что сообщение в коммите содержит код текущего задания в квадратных скобках и количество символов в сообщении не превышает 30. Пример сообщения: \[04-script-01-bash\] сломал хук.

### Ваш скрипт:
```bash
???
```
