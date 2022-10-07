---
# Front matter
title: "Отчет по лабораторной работе №5"
subtitle: "Дискреционное разграничение прав в Linux. Исследование влияния дополнительных атрибутов"
author: "Бурдина Ксения Павловна"
group: NFIbd-01-19
institute: RUDN University, Moscow, Russian Federation
date: 2022 Oct 5th

# Generic otions
lang: ru-RU
toc-title: "Содержание"

# Pdf output format
toc: true # Table of contents
toc_depth: 2
lof: true # List of figures
fontsize: 12pt
linestretch: 1.5
papersize: a4
documentclass: scrreprt
### Fonts
mainfont: PT Serif
romanfont: PT Serif
sansfont: PT Sans
monofont: PT Mono
mainfontoptions: Ligatures=TeX
romanfontoptions: Ligatures=TeX
sansfontoptions: Ligatures=TeX,Scale=MatchLowercase
monofontoptions: Scale=MatchLowercase,Scale=0.9
## Biblatex
biblatex: true
biblio-style: "gost-numeric"
biblatexoptions:
  - parentracker=true
  - backend=biber
  - hyperref=auto
  - language=auto
  - autolang=other*
  - citestyle=gost-numeric
## Misc options
indent: true
header-includes:
  - \linepenalty=10 # the penalty added to the badness of each line within a paragraph (no associated penalty node) Increasing the value makes tex try to have fewer lines in the paragraph.
  - \interlinepenalty=0 # value of the penalty (node) added after each line of a paragraph.
  - \hyphenpenalty=50 # the penalty for line breaking at an automatically inserted hyphen
  - \exhyphenpenalty=50 # the penalty for line breaking at an explicit hyphen
  - \binoppenalty=700 # the penalty for breaking a line at a binary operator
  - \relpenalty=500 # the penalty for breaking a line at a relation
  - \clubpenalty=150 # extra penalty for breaking after first line of a paragraph
  - \widowpenalty=150 # extra penalty for breaking before last line of a paragraph
  - \displaywidowpenalty=50 # extra penalty for breaking before last line before a display math
  - \brokenpenalty=100 # extra penalty for page breaking after a hyphenated line
  - \predisplaypenalty=10000 # penalty for breaking before a display
  - \postdisplaypenalty=0 # penalty for breaking after a display
  - \floatingpenalty = 20000 # penalty for splitting an insertion (can only be split footnote in standard LaTeX)
  - \raggedbottom # or \flushbottom
  - \usepackage{float} # keep figures where there are in the text
  - \floatplacement{figure}{H} # keep figures where there are in the text
---

# Цель работы

Целью данной работы является изучение механизмов изменения идентификаторов, применения SetUID- и Sticky-битов; получение практических навыков работы в консоли с дополнительными атрибутами; рассмотрение работы механизма смены идентификатора процессов пользователей, а также влияние бита Sticky на запись и удаление файлов.

# Ход выполнение лабораторной работы

## Создание программы

1. Войдем в систему от имени пользователя guest

2. Создадим программу simpleid.c:

![Создание программы simpleid.c](screens/1.jpg)

![Листинг программы simpleid.c](screens/2.jpg)

3. Скомпилируем программу и убедимся, что файл программы создан:

![Компиляция файла simpleid.c](screens/3.jpg)

4. Выполним программу simpleid:

![Выполнение программы simpleid](screens/4.jpg)

5. Выполним системную программу id:

![Выполнение системной программы id](screens/5.jpg)

Видим, что выводимые данные совпадают с данными из прошлого пункта.

6. Усложним программу, добавив вывод действительных идентификаторов:

![Листинг усложненной программы](screens/6.jpg)

Получившуюся пргограмму назовем simpleid2.c.

7. Скомпилируем и запустим simpleid2.c:

![Компиляция и запуск simpleid2.c](screens/7.jpg)

8. От имени суперпользователя выполним команды:

![Выполнение команд от имени суперпользователя](screens/8.jpg)

9. Для перехода в режим суперпользователя мы выполнили команду su. Если говорить про команду sudo, то ее следует использовать перед каждой командой, если работа производится от имени обычного пользователя, а команды возможны к выполнению только суперпользователем.

10. Выполним проверку правильности установки новых атрибутов и смены владельца файла simpleid2:

![Проверка установки атбирутов и смены владельца simpleid2](screens/9.jpg)

Видим, что владельцем файла указан root и у пользователя выставлен атрибут s.

11. Запустим simpleid2 и id:

![Запуск simpleid2 и id](screens/10.jpg)

Видим, что у нас при запуске программы выводятся значения e_uid, e_gid, real_uid и real_gid, которые мы прописали в коде, а при просмотре информации выводятся действительные значения uid и gid.

12. Проделаем то же самое относительно SetGID-бита [[1]](https://esystem.rudn.ru/pluginfile.php/1651889/mod_resource/content/2/005-lab_discret_sticky.pdf):

![Изменение атрибутов файла для группы пользователей](screens/11.jpg)

![Проверка изменения атрибутов](screens/12.jpg)

![Запуск программы и id](screens/13.jpg)

13. Создадим программу readfile.c:

![Создание программы readfile.c](screens/14.jpg)

![Листинг программы readfile.c](screens/15.jpg)

14. Откомпилируем программу readfile.c:

![Компиляция программы readfile.c](screens/16.jpg)

15. Сменим владельца файла readfile.c и изменим права так, чтоб только суперпользователь мог прочитать его, а guest нет:

![Изменение владельца и атрибутов файла readfile.c](screens/17.jpg)

16. Проверим, что пользователь guest не может прочитать файл readfile.c:

![Проверка невозможности чтения файла readfile.c](screens/18.jpg)

17. Сменим у программы readfile владельца и установим SetUID-бит:

![Смена владельца readfile и установка SetUID-бита](screens/19.jpg)

18. Проверим, что программа может прочитать файл readfile:

![Проверка возможности чтения файла readfile](screens/20.jpg)

19. Проверим, что программа readfile может прочитать файл /etc/shadow:

![Чтение файла /etc/shadow](screens/21.jpg)

Можем заметить, что SetUID-бит позволяет пользователю запускать исполняемый файл с правами владельца этого файла.

## Исследование Sticky-бита

1. Выясним, установлен ли атрибут Sticky на директории /tmp:

![Проверка установки атрибута Sticky](screens/22.jpg)

2. От имени пользователя guest создадим файл file01.txt в директории /tmp со словом test:

![Создание файла file01.txt](screens/23.jpg)

3. Просмотрим атрибуты у только что созданного файла и расширим чтение и запись для категории пользователей "все остальные":

![Просмотр и изменение атрибутов файла file01.txt](screens/24.jpg)

4. От пользователя guest2 (не являющегося владельцем) попробуем прочитать файл /tmp/file01.txt:

![Попытка чтения файла от имени guest2](screens/25.jpg)

5. От пользователя guest2 попробуем дозаписать в файл /tmp/file01.txt слово test2:

![Дозапись в файл слова test2](screens/26.jpg)

6. Проверим содержимое файла file01.txt:

![Проверка содержимого файла](screens/27.jpg)

7. От пользователя guest2 попробуем записать в файл /tmp/file01.txt слово test3, стерев при этом всю имеющуюся в файле информацию:

![Запись в файл слова test3](screens/28.jpg)

8. Проверим содержимое файла:

![Проверка содержимого файла](screens/29.jpg)

9. От пользователя guest2 попробуем удалить файл /tmp/file01.txt:

![Попытка удаления файла](screens/30.jpg)

Мы получили отказ в выполнении действия.

10. Повысим свои права до суперпользователя командой su - и выполним после этого команду, снимающую атрибут t (Sticky-бит) с директории /tmp:

![Снятие атрибута t с директории /tmp](screens/31.jpg)

11. Покинем режим суперпользователя:

![Покидание режима суперпользователя](screens/32.jpg)

12. От пользователя guest2 проверим, что атрибута t у директории /tmp нет:

![Проверка отсутствия атрибута t](screens/33.jpg)

13. Повторим предыдущие шаги:

![Выполнение команд со снятым атрибутом t](screens/34.jpg)

14. Видим, что теперь мы можем удалить файл file01.txt. Суть в том, что атрибут t как Sticky-бит устанавливает такие права, что файл с данным битом может удалить только тот пользователь, который является владельцем файла [[2]](https://zalinux.ru/?p=6440).

15. Повысим свои права до суперпользователя и вернем атрибут t на директорию /tmp:

![Возврат атрибута t на директорию](screens/35.jpg)


# Выводы

В ходе работы мы изучили механизмы изменения идентификаторов, применения SetUID- и Sticky-битов; получили практические навыки работы в консоли с дополнительными атрибутами; рассмотрели работу механизма смены идентификатора процессов пользователей, а также влияние бита Sticky на запись и удаление файлов.

# Список литературы

1. Методические материалы курса [[1]](https://esystem.rudn.ru/pluginfile.php/1651889/mod_resource/content/2/005-lab_discret_sticky.pdf)
2. Электронный источник "Атрибуты файлов в Linux" [[2]](https://zalinux.ru/?p=6440)
