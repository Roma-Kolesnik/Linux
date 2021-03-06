# UNIXWAY: Everything is a file, streams, pipes, sockets. Commandline vs GUI, automation
asdsa

Как было уже рассмотрено на прошлом занятии, в **UNIX**-системах большое внимание уделено гибкости файлового дерева, которое
может включать в себя множество разных файловых систем для разных целей - как дисковых, так и виртуальных. Ко всему прочему
CLI содержит в себе достаточно гибкий инструментарий для оперирования данными в файлах. Файлы же, в свою очередь, могут
быть отображением *любой информации*, включая практичеки любые настройки операционной системы.
Все это дает гибкий и мощный инструментарий для автоматизации и управления операционной системой.

## Понятие потока/сокета. Применение в **POSIX**.

Потоки, сокеты и пайпы (каналы) - схожие понятия, суть которых в абстракции **потока данных**. Поток предоставляет
данные порциями для обработки, и, обычно, не позволяет доступ в случайном порядке. Потоки ввода-вывода - однонаправленные,
сокет, обычно, состоит из двух потоков. Сокеты обычно используются для сетевых соединений.

С точки зрения **POSIX** (стандарт **UNIX**-систем) все эти сущности, как в сети, так и в случае потоков ввода-вывода, работают
схоже (или одинаково), что облегчает и унифицирует программирование потоков, и позволяет гибко перенаправлять локальные потоки
в сеть или в любому другому получателю, который готов воспринимать потоковые данные.

## Потоки ввода-вывода.

У каждой программы, работающей с CLI (а это по умолчанию все в **UNIX**), есть три потока:
- **Поток ввода**. По умолчанию связан с вводом пользователя с клавиатуры. Однако его можно *перенаправить*.
- **Поток вывода**. То, что выводит программа на терминал. Все основные консольные команды типа `cat` и `ls` выводят полезную информацию в поток вывода.
- **Поток ошибок**. Это, по большому счету, тоже поток вывода, который по умолчанию отправляется туда же, куда и основной поток вывода - на экран терминала. Однако ошибки могут быть *перенаправлены* *отдельно* в другом направлении, так как это отдельный полноправный поток.

## Перенаправление ввода-вывода.

 - `>` - перенаправление вывода программы в файл
 - `>>` - дописывает в файл.
 - `<` - перенаправления файла на поток ввода
 - `|` - перенаправление потока вывода одной программы на другой.

## Коды ошибок и `&&`, `||`.

Ко всему прочему, каждая программа в рамках **CLI** может вести себя как **функция** - имеет код результата, который обычно передается из программы с помощью функции `exit`. Код числовой, 0 означает что все хорошо,
иные значение - ту или иную ошибку. Это можно использовать в условиях и циклах оболочки. Значение ошибки доступно в переменной `$?` интерпретатора.

### `&&` и `||`

В отличие от **PHP**, во многих языках логические **И** и **ИЛИ** работают не только в логическом, но и в бытовом смысле: **И** работает до первой неудачи, **ИЛИ** работает до первой удачи.
Под 'удачей' или 'неудачей' понимается код ошибки, возвращенный из программы:

```sh
git commit -am 'text' && git push # сделать коммит, И, если он удачный (код ошибки === 0), сделать git push
```

```sh
reboot || shutdown -h now # перезагрузить ИЛИ выключить. Если reboot не срабатывает - запускается shutdown -h now
```

### `;`
  
Объединяет несколько команд в одну строку не обращая внимания на коды ошибок.


## Фильтры (`sort`, `uniq`, `grep`, `sed`, `cut`, `wc`, `tail`, `head`, `find` ...)


Короткий перечень что делают эти программы(команды)


- __sort__ -- Название говорит само за себя. Без параметров проводиться обычная сортировка по алфавиту.
*Например*


__Изначальный файл__

```
A
d
F
q
w
e
t
a
J
{
`
2
G
j
y
u
Z
0
```

__С применением программы sort__

```
`
{
0
2
a
A
d
e
F
G
j
J
q
t
u
w
y
Z
```



- ```sort -r``` __reverse__ сортирует по алфавиту задом наперед(реверсирует)
    

    *Например*
    ```
Z
y
w
u
t
q
J
j
G
F
e
d
A
a
2
0
{

    ```


- ```sort -n``` __numeric sort__ сортирует с учетом чисел. 

*Например*

```
`
{
0
a
A
d
e
F
G
j
J
q
t
u
w
y
Z
2

```


- __uniq__ -- Программа для вывода или удаления из вывода повторяющихся строк

> Очень полезная программа которая поможет вам облегчить жизнь при чтении стен текста(например лог файлов сервера или приложения)

__изначальный файл__

```
I will use Google before asking dumb questions.
I will use Google before asking dumb questions.
I will use Google before asking dumb questions.
I will use Google before asking dumb questions.
I will use Google before asking dumb questions.
I will use Google before asking dumb questions.
I will use Google before asking dumb questions.
I will use Google before asking dumb questions.
I will use Google before asking dumb questions.

Success is one percent inspiration, ninety-nine percent perspiration.
Success is one percent inspiration, ninety-nine percent perspiration.
Success is one percent inspiration, ninety-nine percent perspiration.
Success is one percent inspiration, ninety-nine percent perspiration.
Success is one percent inspiration, ninety-nine percent perspiration.
Success is one percent inspiration, ninety-nine percent perspiration.

The mind is everything. What you think you become
The mind is everything. What you think you become
The mind is everything. What you think you become
The mind is everything. What you think you become
I am not coder. I am a programmer!

```

__вывод файла и применением программы без параметров__

```
user@hostname ~ $ >> uniq example.file

I will use Google before asking dumb questions.

Success is one percent inspiration, ninety-nine percent perspiration.

The mind is everything. What you think you become
I am not coder. I am a programmer!
```

- ```uniq -c example.file``` - вывод информации о количестве дубликатов каждой из строк

```
9 I will use Google before asking dumb questions.
1 
6 Success is one percent inspiration, ninety-nine percent perspiration.
1 
4 The mind is everything. What you think you become
1 I am not coder. I am a programmer!
1
```

> Прошу заметить что считаются также пробелы т.к это тоже символы

- ```uniq -u example.file``` - вывод лишь не повторяющихся строк

```
I am not coder. I am the programmer!
```

- __grep__ - расшифровывается как *Global Regular Expression Printer*. Иными словами grep выдрезает нужные вам строки из текстовых файлов которые содержат указаный пользователем текст.

__изначальный файл__

```
I love the smell of napalm in the morning(Apocalypse Now)

May the force be with you(Star Wars)

You Shall Not Pass!(The Lord of the Ring)

I'll be back(Terminator)

Houston, we had a problem(Apollo 13)

Everything begins with choice(The Matrix)

I'm going to make him an offer he can't refuse(The God Father)

Elementary, my dear Watson(Sherlock Holmes)

```

В отличии от вышеприведенных инструментов, grep - принимает первым параметром шаблон который следует искать, а потом файл к которому он применит этот шаблон и найдет в соответствии с ним

*Например*

```
user@hostname ~ $ >> grep Matrix example.file
Everything begins with choice(The Matrix)
```

- ```grep -i ```  -- параметр который заставляет нашу программу искать совпадения игнорируя регистр.

*Например*

```
user@hostname ~ $ >> grep term example.file
```

> упс! Ничего не вывело т.к именно такого совпадения не обнаружено
> Применяем параметр

```
user@hostname ~ $ >> grep -i term example.file
I'll be back(Terminator)
```

- ```grep -v``` --  Вместо того чтобы вывести строки где искомое было найдено
выводит только те строки где искомой подстроки нет.

> То есть в таком случае вы исключаете  совпадения с шаблоном

*Например*

__изначальный файл__

```
John: Hello Linda! 12:13 
 
Linda: hello 14:11 
 
John: How are you? 14:11 
 
Linda: i am ok 15:54 
 
John: What are you doing? 15:54 
 
Linda: I am working 18:01 
 
John: Why? The working day is over 18:03 
 
Linda: john... sorry... but.. you don`t know php... good bye 00:30 
```

- __с параметром__

> Выведем сообщения только те которые написал Джохн

`grep -v Linda example.file`

```
John: How are you? 14:11 
 
 
John: What are you doing? 15:54 
 
 
John: Why? The working day is over 18:03 
```

> Теперь выведем сообщения Линды

`grep -v John example.file`

```
 
Linda: hello 14:11 
 
Linda: i am ok 15:54 
 
Linda: I am working 18:01 
 
Linda: john... sorry... but.. you don`t know javaScript... good bye 00:30 
```

- __sed__ - потоковый ТЕКСТОВЫЙ РЕДАКТОР(__S__tream __E____D__itor)

>Утилиту sed называют потоковым текстовым редактором. В интерактивных текстовых редакторах, наподобие nano, с текстами работают, используя клавиатуру, редактируя файлы, добавляя, удаляя или изменяя тексты. Sed позволяет редактировать потоки данных, основываясь на заданных разработчиком наборах правил. Вот как выглядит схема вызова этой команды

[Для тех кому интересно =)](https://habr.com/company/ruvds/blog/327530/)

```
sed -e 's/oldstuff/newstuff/g' inputFileName > outputFileName
```

Здесь __e__ - выражение;  __s__ — замена; __g__ — глобально, что означает «все вхождения искомого значения». После первого прямого слеша расположено регулярное выражение для поиска, после второго — выражение для замены.

>очень похожее можно увидеть в vim с поиском и заменой :)

[Полезные скрипты на sed](http://ant0.ru/sed1line.html)

- __Полезные операции sed__

    - 8d  Удалить 8-ю строку.
    - /^$/d Удалить все пустые строки
    - s/Windows/Linux/  В каждой строке, заменить первое встретившееся слово "Windows" на слово "Linux"
    - /GUI/d    Удалить все строки, содержащие "GUI".

- __cut__ -- Команда cut используется для выборки колонок из таблицы или полей из каждой строки файла;

__исходный файл__

```
Python

Hypertext

Parallax

objective-C
react

Java Core

Array

Vertex

async

Symphony

C++

R

ISO

Php

Turple

???

```

*Например*

- __cut -cNumber__ - выкусывает колонку, Number - номер колонки. Да, мы сейчас воспринимаем текст не строчками а столпцами то бишь as columns


```
user@hostname ~ $ >> cut -c1 example.file
P

H

P

o
r

J

A

V

a

S

C

R

I

P

T

?


```

-  __wc__ - unix‐утилита, выводящая число переводов строк, слов и байт для каждого указанного файла и итоговую строку, если было задано несколько файлов. Если входной файл не задан, или равен ‘-‘, то данные считываются со стандартного ввода.

Используеться она так:

- wc -l filename - вывести количество строк

- wc -c filename - вывести количество байт

- wc -m filename - вывести количество символов

- wc -L filename - вывести длину самой длинной строки

- wc -w filename - вывести количество слов



- __tail__ - показывает хвостовую часть файла

В выдаче мы получим 10 последних строк syslog, после чего команда завершит свою работу.

__первые строчки исходного файла__
```
Hello I am the teapot : 06/19/18:02:42:59
Hello I am the teapot : 06/19/18:02:43:07
Hello I am the teapot : 06/19/18:02:43:09
Hello I am the teapot : 06/19/18:02:43:11
Hello I am the teapot : 06/19/18:02:43:13
Hello I am the teapot : 06/19/18:02:43:15
Hello I am the teapot : 06/19/18:02:43:17
Hello I am the teapot : 06/19/18:02:43:19
Hello I am the teapot : 06/19/18:02:43:21
Hello I am the teapot : 06/19/18:02:43:23
...
```

__вместе с tail__

```
Hello I am the teapot : 06/19/18:08:03:04
Hello I am the teapot : 06/19/18:08:03:06
Hello I am the teapot : 06/19/18:08:03:08
Hello I am the teapot : 06/19/18:08:03:10
Hello I am the teapot : 06/19/18:08:03:12
Hello I am the teapot : 06/19/18:08:03:14
Hello I am the teapot : 06/19/18:08:03:16
Hello I am the teapot : 06/19/18:08:03:18
Hello I am the teapot : 06/19/18:08:03:20
Hello I am the teapot : 06/19/18:08:03:22
```

__```tail -n```__ -- параметр через который можно регулировать количество выдачи строк

*Например*
`tail -n 20 /var/log/syslog`

`tail -n 30 /var/log/nginx/access.log`

Отличительной чертой команды tail является ее возможность не завершаться по достижении конца вывода, а ожидать появления новых данных. Для этого добавим ключ —f
То есть tail может работать в реальном времени, очень полезная тулза.


__head__ -- выводит первые N количество строк файла, по умолчанию это 10

__```head -n```__ -- выводит заданое количество строк собсна как тейл но только начало 


__Find__ - утилита поиска файлов по имени и другим свойствам, используемая в UNIX‐подобных операционных системах. Может производить поиск в одном или нескольких каталогах с использованием критериев, заданных пользователем.

Самый простой пример нахождения файла по имени с помощью команды find:

`find . -name test`


тот же поиск по имени, но без учета регистра в названии файлов

`find . -iname test`


Но, дело в том, что в примере выше мы не указали, что ищем именно файл, по тому, если в текущем каталоге или его подкаталоге существует папка из названием test, то она тоже попадет в результаты поиска. Для указания того, что мы ищем именно файл, и что названия папок в результаты поиска включать не надо, используем ключ «-type

`find . -type f -name test`

__type f__ — мы указали, что ищем именно файл (f) Для того, чтобы в результат включить одни папки с названием test, а файлы не искать, используем __-type d__

`find . -type d -name test`



**TODO**

## Всё есть файл и что это даёт.

В данном случае, “всё” действительно означает всё. Жесткий диск, разделы на жестком диске, параллельные порты, подключение к web-сайтам, Ethernet карточка, все они являются файлами. Даже каталоги - это файлы. В GNU/Linux существует много типов файлов в дополнение к стандартным файлам и каталогам. Обратите внимание, здесь под типом файла, мы не подразумеваем содержимое файла: в GNU/Linux и любой другой UNIX системах, файл, будь это текст, или PNG картинка, или двоичный файл, или что-то подобное, является только потоком байтов. Разделение файлов согласно их содержанию оставлено приложениям.



## Сокеты в файловой системе и сетевые.

https://en.wikipedia.org/wiki/Unix_domain_socket

Сокет — один из интерфейсов __межпроцессного__ взаимодействия, позволяющий разрабатывать клиент-серверные системы для локального или сетевого использования.

Socket с английского буквально это разьем. Под сокетом можно подразумевать гнездо для кабеля интернета в вашем компьютере, USB кабель и т.д
То бишь сокет направлен на то что есть некий сервер который предоставляет возможность подключения к себе для общения данными с тем кто подключаеться к нему. В случае с USB кабелем это телефон который являеться клиентом.

>Прошу обратить внимание на то что сервер никогда не инициирует с клиентом соединение. Буквально компьютер не воткнет в ваш телефон USB кабель и не начнет закачивать все :)

Такое взаимодействие сплошь и рядом. В разрезе web разработки сокетом являеться некий абстрактный разьем к которому можно подключиться некоторыми способами и начать общение. Например когда мы в браузере вводим адресс 
`http:gitlab.a-level.com.ua` то браузер является клиентом который подключаеться по 80 порту(сокету) к серверу по протоколу HTTP и получает от сервера определенные данные, в нашем случае это html, css, js, картинки и т.д

Кроме того что бы подключиться к серверу нам не обязательно нужно это делать по интернету.

*nix системы так же могут общаться по модели клиент-сервер и без интернета при условии что клиент находиться физически на сервере.

Для этого есть те самые Unix-Sockets.

В чем разница между Unix-Socket и TCP-Sockets???

Разницы с точки зрения клиента почти никакой.

Серверное приложение создает сокет, который как файловый дескриптор представляет собой ресурс, присваиваемый единственному серверному процессу.

То есть inode(файловый дескриптор) выступает в роли URI.

Далее сервер присваивает сокету имя. Локальные сокеты с заданными именами файлов в файловой системе Linux часто размещаются в каталоге /tmp или /usr/tmp, /var/run/

То есть простыми словами взаимодействие клиент-сервер происходит внутри сервера.

__Какие плюсы Unix-Sockets???__

- Во-первых безопасность, безопасность, безопасность
К примеру ваш сайт общается с базой данных по интернету и очевидно что эти данные может кто либо перехватить, подслушать или просто узнать какая база данных используеться.

Если у вас общение с БД происходит только на локальной машине то из любого другого места никто не сможет этого сделать так как все изолировано от сети что уже достаточно безопасно.

- Скорость
Да, использование Unix-Sockets являеться куда более быстрым т.к не тратиться время на обработку данных на сетевой карте, после не тратиться время на обработку процессором данных с сетевой карты.

Ваш сервер с клиентом общается напрямую. То есть сетевая карта вообще не учавствует в этой цепочке. 

- Стабильность

Общаясь по юниксовым сокетам вы вообще не паритесь по поводу того что соединения может пропадать, например в силу некомпетентности вашего интернет провайдера и т.д

Например хорошей практикой считается подключатся к БД (при условии что приложения и БД находяться на одном сервере)

именно по юниксовым сокетам. 

*Например*

```$link = mysql_connect('localhost:/var/run/mysqld/mysqld.sock', 'mysql_user', 'mysql_password');
```

### Practice

