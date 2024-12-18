# Обработка данных в R с использованием пакета dplyr
pashtet.2003@yandex.ru

## Цель работы

1.  Освоить практические навыки работы с данными в R.
2.  Углубить знания базовых функций пакета dplyr.
3.  Использовать пакет nycflights13 для анализа данных авиаперелётов.

## Исходные данные

1.  Программное обеспечение: Windows 10.
2.  RStudio Desktop и библиотека dplyr.
3.  Интерпретатор языка R версии 4.1.
4.  Пакеты: dplyr, nycflights13.

## Ход работы

### 1. Установка пакета nycflights13

    install.packages("nycflights13")

### 2. Подключение библиотек

``` r
library(nycflights13)
library(dplyr)
```


    Присоединяю пакет: 'dplyr'

    Следующие объекты скрыты от 'package:stats':

        filter, lag

    Следующие объекты скрыты от 'package:base':

        intersect, setdiff, setequal, union

### 3. Сколько наборов данных входит в пакет nycflights13?

``` r
length(data(package = "nycflights13")$results[, "Item"])
```

    [1] 5

### 4. Сколько строк и столбцов в каждом наборе данных?

``` r
lapply(list(airlines, airports, flights, planes, weather), function(df) c(nrow(df), ncol(df)))
```

    [[1]]
    [1] 16  2

    [[2]]
    [1] 1458    8

    [[3]]
    [1] 336776     19

    [[4]]
    [1] 3322    9

    [[5]]
    [1] 26115    15

### 5. Вывести структуру набора данных flights

``` r
str(flights)
```

    tibble [336,776 × 19] (S3: tbl_df/tbl/data.frame)
     $ year          : int [1:336776] 2013 2013 2013 2013 2013 2013 2013 2013 2013 2013 ...
     $ month         : int [1:336776] 1 1 1 1 1 1 1 1 1 1 ...
     $ day           : int [1:336776] 1 1 1 1 1 1 1 1 1 1 ...
     $ dep_time      : int [1:336776] 517 533 542 544 554 554 555 557 557 558 ...
     $ sched_dep_time: int [1:336776] 515 529 540 545 600 558 600 600 600 600 ...
     $ dep_delay     : num [1:336776] 2 4 2 -1 -6 -4 -5 -3 -3 -2 ...
     $ arr_time      : int [1:336776] 830 850 923 1004 812 740 913 709 838 753 ...
     $ sched_arr_time: int [1:336776] 819 830 850 1022 837 728 854 723 846 745 ...
     $ arr_delay     : num [1:336776] 11 20 33 -18 -25 12 19 -14 -8 8 ...
     $ carrier       : chr [1:336776] "UA" "UA" "AA" "B6" ...
     $ flight        : int [1:336776] 1545 1714 1141 725 461 1696 507 5708 79 301 ...
     $ tailnum       : chr [1:336776] "N14228" "N24211" "N619AA" "N804JB" ...
     $ origin        : chr [1:336776] "EWR" "LGA" "JFK" "JFK" ...
     $ dest          : chr [1:336776] "IAH" "IAH" "MIA" "BQN" ...
     $ air_time      : num [1:336776] 227 227 160 183 116 150 158 53 140 138 ...
     $ distance      : num [1:336776] 1400 1416 1089 1576 762 ...
     $ hour          : num [1:336776] 5 5 5 5 6 5 6 6 6 6 ...
     $ minute        : num [1:336776] 15 29 40 45 0 58 0 0 0 0 ...
     $ time_hour     : POSIXct[1:336776], format: "2013-01-01 05:00:00" "2013-01-01 05:00:00" ...

### 6. Найти количество уникальных мест назначения (dest) в данных flights

``` r
flights %>% filter(!is.na(dest)) %>% distinct(dest) %>% nrow()
```

    [1] 105

### 7. Сколько рейсов вылетело из аэропорта EWR в августе?

``` r
flights %>% filter(origin == "EWR", month == 8) %>% count()
```

    # A tibble: 1 × 1
          n
      <int>
    1 10359

### 8. Найти аэропорт с минимальной широтой (самый южный)

``` r
airports %>% arrange(lat) %>% slice(1)
```

    # A tibble: 1 × 8
      faa   name        lat   lon   alt    tz dst   tzone           
      <chr> <chr>     <dbl> <dbl> <dbl> <dbl> <chr> <chr>           
    1 ITO   Hilo Intl  19.7 -155.    38   -10 N     Pacific/Honolulu

### 9. Определить аэропорт с наибольшим количеством рейсов в 2013 году

``` r
flights %>% group_by(origin) %>% count() %>% arrange(desc(n)) %>% slice(1)
```

    # A tibble: 3 × 2
    # Groups:   origin [3]
      origin      n
      <chr>   <int>
    1 EWR    120835
    2 JFK    111279
    3 LGA    104662

### 10. Найти 5 самых новых самолётов

``` r
planes %>% arrange(desc(year)) %>% slice(1:5)
```

    # A tibble: 5 × 9
      tailnum  year type               manufacturer model engines seats speed engine
      <chr>   <int> <chr>              <chr>        <chr>   <int> <int> <int> <chr> 
    1 N150UW   2013 Fixed wing multi … AIRBUS       A321…       2   199    NA Turbo…
    2 N151UW   2013 Fixed wing multi … AIRBUS       A321…       2   199    NA Turbo…
    3 N152UW   2013 Fixed wing multi … AIRBUS       A321…       2   199    NA Turbo…
    4 N153UW   2013 Fixed wing multi … AIRBUS       A321…       2   199    NA Turbo…
    5 N154UW   2013 Fixed wing multi … AIRBUS       A321…       2   199    NA Turbo…

### 11. Среднее значение скорости (air_time) для рейсов авиакомпании AA в декабре

``` r
flights %>% filter(carrier == "AA", month == 12, !is.na(air_time)) %>% summarise(mean_speed = mean(distance / air_time * 60))
```

    # A tibble: 1 × 1
      mean_speed
           <dbl>
    1       398.

### 12. Найти день с наибольшей суммарной задержкой вылета (dep_delay)

``` r
flights %>% group_by(year, month, day) %>% summarise(total_delay = sum(dep_delay, na.rm = TRUE)) %>% arrange(desc(total_delay)) %>% slice(1)
```

    `summarise()` has grouped output by 'year', 'month'. You can override using the
    `.groups` argument.

    # A tibble: 12 × 4
    # Groups:   year, month [12]
        year month   day total_delay
       <int> <int> <int>       <dbl>
     1  2013     1    31       24159
     2  2013     2    27       34138
     3  2013     3     8       66746
     4  2013     4    19       40777
     5  2013     5    23       39228
     6  2013     6    28       42529
     7  2013     7     1       49542
     8  2013     8     8       40879
     9  2013     9     2       44863
    10  2013    10     7       36289
    11  2013    11    17       19160
    12  2013    12     5       42438

### 13. Определить аэропорт с наибольшей средней температурой в июле

``` r
weather %>% filter(month == 7) %>% group_by(origin) %>% summarise(mean_temp = mean(temp, na.rm = TRUE)) %>% arrange(desc(mean_temp)) %>% slice(1)
```

    # A tibble: 1 × 2
      origin mean_temp
      <chr>      <dbl>
    1 LGA         80.8

### 14. Найти авиакомпанию с минимальным количеством рейсов в апреле

``` r
flights %>% filter(month == 4) %>% count(carrier) %>% arrange(n) %>% slice(1)
```

    # A tibble: 1 × 2
      carrier     n
      <chr>   <int>
    1 HA         30

## Оценка результата

Были изучены основные функции обработки данных пакета dplyr на примере
реальных данных о рейсах. Получены результаты анализа по различным
аспектам, включая количество рейсов, уникальные направления,
температурные данные и задержки вылетов.

## Вывод

В ходе выполнения работы освоены практические подходы к обработке
больших таблиц с использованием пакета dplyr. На примере данных о рейсах
выполнен анализ, включающий группировку, фильтрацию, вычисления и
сортировку. Полученные навыки позволяют эффективно анализировать данные
и извлекать из них полезную информацию.
