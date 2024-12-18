# Исследование информации о состоянии беспроводных сетей
pashtet.2003@yandex.ru

## Цель работы

1.  Получить знания о методах исследования радиоэлектронной обстановки.
2.  Составить представление о механизмах работы Wi-Fi сетей на канальном
    и сетевом уровне модели OSI.
3.  Закрепить практические навыки использования языка программирования R
    для обработки данных.
4.  Закрепить знания основных функций обработки данных экосистемы
    tidyverse языка R.

## Исходные данные

1.  Программное обеспечение Windows 11.
2.  Rstudio Desktop и пакет dplyr.
3.  Интерпретатор языка R 4.1.
4.  Тестовые данные `P2_wifi_data.csv`.

## План

1.  Импортировать данные.
2.  Привести датасеты в вид “аккуратных данных”, преобразовать типы
    столбцов в соответствии с типом данных.
3.  Просмотреть общую структуру данных с помощью функции `glimpse()` и
    провести анализ.

## Ход выполнения работы

### Импорт данных

``` r
library(tidyverse)
```

    ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ✔ dplyr     1.1.4     ✔ readr     2.1.5
    ✔ forcats   1.0.0     ✔ stringr   1.5.1
    ✔ ggplot2   3.5.1     ✔ tibble    3.2.1
    ✔ lubridate 1.9.4     ✔ tidyr     1.3.1
    ✔ purrr     1.0.2     
    ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ✖ dplyr::filter() masks stats::filter()
    ✖ dplyr::lag()    masks stats::lag()
    ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

``` r
library(dplyr)
library(readr)

# Импорт данных с разными частями
ds1 <- read_csv("P2_wifi_data.csv", n_max = 167)
```

    Rows: 167 Columns: 15
    ── Column specification ────────────────────────────────────────────────────────
    Delimiter: ","
    chr  (6): BSSID, Privacy, Cipher, Authentication, LAN.IP, ESSID
    dbl  (6): channel, Speed, Power, beacons, # IV, ID-length
    lgl  (1): Key
    dttm (2): First.time.seen, Last.time.seen

    ℹ Use `spec()` to retrieve the full column specification for this data.
    ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
ds2 <- read_csv("P2_wifi_data.csv", skip = 169)
```

    Warning: One or more parsing issues, call `problems()` on your data frame for details,
    e.g.:
      dat <- vroom(...)
      problems(dat)

    Rows: 12081 Columns: 7
    ── Column specification ────────────────────────────────────────────────────────
    Delimiter: ","
    chr  (3): Station.MAC, BSSID, Probed.ESSIDs
    dbl  (2): Power, # packets
    dttm (2): First.time.seen, Last.time.seen

    ℹ Use `spec()` to retrieve the full column specification for this data.
    ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

### Формирование “аккуратных данных”

``` r
# Очистка данных и преобразование
# Для ds1
ds1 <- ds1 %>%
  mutate(across(c(BSSID, Privacy, Cipher, Authentication, LAN.IP, ESSID), ~trimws(.))) %>%
  mutate(across(c(BSSID, Privacy, Cipher, Authentication, LAN.IP, ESSID), ~na_if(., "")))

ds1 <- ds1 %>%
  mutate(
    First.time.seen = as.POSIXct(First.time.seen, format = "%Y-%m-%d %H:%M:%S"),
    Last.time.seen = as.POSIXct(Last.time.seen, format = "%Y-%m-%d %H:%M:%S")
  )

# Для ds2
ds2 <- ds2 %>%
  mutate(across(c(Station.MAC, BSSID, Probed.ESSIDs), ~trimws(.))) %>%
  mutate(across(c(Station.MAC, BSSID, Probed.ESSIDs), ~na_if(., "")))

ds2 <- ds2 %>%
  mutate(
    First.time.seen = as.POSIXct(First.time.seen, format = "%Y-%m-%d %H:%M:%S"),
    Last.time.seen = as.POSIXct(Last.time.seen, format = "%Y-%m-%d %H:%M:%S")
  )
```

### Просмотр общей структуры данных

``` r
glimpse(ds1)
```

    Rows: 167
    Columns: 15
    $ BSSID           <chr> "BE:F1:71:D5:17:8B", "6E:C7:EC:16:DA:1A", "9A:75:A8:B9…
    $ First.time.seen <dttm> 2023-07-28 09:13:03, 2023-07-28 09:13:03, 2023-07-28 …
    $ Last.time.seen  <dttm> 2023-07-28 11:50:50, 2023-07-28 11:55:12, 2023-07-28 …
    $ channel         <dbl> 1, 1, 1, 7, 6, 6, 11, 11, 11, 1, 6, 14, 11, 11, 6, 6, …
    $ Speed           <dbl> 195, 130, 360, 360, 130, 130, 195, 130, 130, 195, 180,…
    $ Privacy         <chr> "WPA2", "WPA2", "WPA2", "WPA2", "WPA2", "OPN", "WPA2",…
    $ Cipher          <chr> "CCMP", "CCMP", "CCMP", "CCMP", "CCMP", NA, "CCMP", "C…
    $ Authentication  <chr> "PSK", "PSK", "PSK", "PSK", "PSK", NA, "PSK", "PSK", "…
    $ Power           <dbl> -30, -30, -68, -37, -57, -63, -27, -38, -38, -66, -42,…
    $ beacons         <dbl> 846, 750, 694, 510, 647, 251, 1647, 1251, 704, 617, 13…
    $ `# IV`          <dbl> 504, 116, 26, 21, 6, 3430, 80, 11, 0, 0, 86, 0, 0, 0, …
    $ LAN.IP          <chr> "0.  0.  0.  0", "0.  0.  0.  0", "0.  0.  0.  0", "0.…
    $ `ID-length`     <dbl> 12, 4, 2, 14, 25, 13, 12, 13, 24, 12, 10, 0, 24, 24, 1…
    $ ESSID           <chr> "C322U13 3965", "Cnet", "KC", "POCO X5 Pro 5G", NA, "M…
    $ Key             <lgl> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA…

``` r
glimpse(ds2)
```

    Rows: 12,081
    Columns: 7
    $ Station.MAC     <chr> "CA:66:3B:8F:56:DD", "96:35:2D:3D:85:E6", "5C:3A:45:9E…
    $ First.time.seen <dttm> 2023-07-28 09:13:03, 2023-07-28 09:13:03, 2023-07-28 …
    $ Last.time.seen  <dttm> 2023-07-28 10:59:44, 2023-07-28 09:13:03, 2023-07-28 …
    $ Power           <dbl> -33, -65, -39, -61, -53, -43, -31, -71, -74, -65, -45,…
    $ `# packets`     <dbl> 858, 4, 432, 958, 1, 344, 163, 3, 115, 437, 265, 77, 7…
    $ BSSID           <chr> "BE:F1:71:D5:17:8B", "(not associated)", "BE:F1:71:D6:…
    $ Probed.ESSIDs   <chr> "C322U13 3965", "IT2 Wireless", "C322U21 0566", "C322U…

## Анализ. Точки доступа.

### 1. Определить небезопасные точки доступа (без шифрования – OPN)

``` r
unsafe_points <- ds1 %>% 
  filter(Privacy == 'OPN') %>% 
  distinct(BSSID)
unsafe_points
```

    # A tibble: 42 × 1
       BSSID            
       <chr>            
     1 E8:28:C1:DC:B2:52
     2 E8:28:C1:DC:B2:50
     3 E8:28:C1:DC:B2:51
     4 E8:28:C1:DC:FF:F2
     5 00:25:00:FF:94:73
     6 E8:28:C1:DD:04:52
     7 E8:28:C1:DE:74:31
     8 E8:28:C1:DE:74:32
     9 E8:28:C1:DC:C8:32
    10 E8:28:C1:DD:04:50
    # ℹ 32 more rows

### 2. Определить производителя для каждого обнаруженного устройства

``` r
# Извлечение OUI и определение производителей
unsafe_points$Vendor <- substr(unsafe_points$BSSID, 1, 8)
print(unique(unsafe_points$Vendor))
```

     [1] "E8:28:C1" "00:25:00" "E0:D9:E3" "00:26:99" "02:BC:15" "00:AB:0A"
     [7] "00:03:7A" "00:03:7F" "00:3E:1A" "02:67:F1" "02:CF:8B" "00:53:7A"

### 3. Выявить устройства, использующие протокол шифрования WPA3

``` r
ds1 %>% 
  filter(str_detect(Privacy, "WPA3")) %>% 
  select(BSSID, ESSID, Privacy)
```

    # A tibble: 8 × 3
      BSSID             ESSID                                          Privacy  
      <chr>             <chr>                                          <chr>    
    1 26:20:53:0C:98:E8  <NA>                                          WPA3 WPA2
    2 A2:FE:FF:B8:9B:C9 "Christie’s"                                   WPA3 WPA2
    3 96:FF:FC:91:EF:64  <NA>                                          WPA3 WPA2
    4 CE:48:E7:86:4E:33 "iPhone (Анастасия)"                           WPA3 WPA2
    5 8E:1F:94:96:DA:FD "iPhone (Анастасия)"                           WPA3 WPA2
    6 BE:FD:EF:18:92:44 "Димасик"                                      WPA3 WPA2
    7 3A:DA:00:F9:0C:02 "iPhone XS Max \U0001f98a\U0001f431\U0001f98a" WPA3 WPA2
    8 76:C5:A0:70:08:96  <NA>                                          WPA3 WPA2

### 4. Отсортировать точки доступа по времени активности на связи

``` r
ds1 %>% 
  mutate(duration = as.numeric(difftime(Last.time.seen, First.time.seen, units = "mins"))) %>% 
  arrange(desc(duration)) %>% 
  select(BSSID, duration) %>% 
  head(10)
```

    # A tibble: 10 × 2
       BSSID             duration
       <chr>                <dbl>
     1 00:25:00:FF:94:73     163.
     2 E8:28:C1:DD:04:52     163.
     3 E8:28:C1:DC:B2:52     163.
     4 08:3A:2F:56:35:FE     162.
     5 6E:C7:EC:16:DA:1A     162.
     6 E8:28:C1:DC:B2:50     162.
     7 E8:28:C1:DC:B2:51     162.
     8 48:5B:39:F9:7A:48     162.
     9 E8:28:C1:DC:FF:F2     162.
    10 8E:55:4A:85:5B:01     162.

### 5. Обнаружить топ-10 самых быстрых точек доступа

``` r
ds1 %>% 
  arrange(desc(Speed)) %>% 
  select(BSSID, Speed) %>% 
  head(10)
```

    # A tibble: 10 × 2
       BSSID             Speed
       <chr>             <dbl>
     1 26:20:53:0C:98:E8   866
     2 96:FF:FC:91:EF:64   866
     3 CE:48:E7:86:4E:33   866
     4 8E:1F:94:96:DA:FD   866
     5 9A:75:A8:B9:04:1E   360
     6 4A:EC:1E:DB:BF:95   360
     7 56:C5:2B:9F:84:90   360
     8 E8:28:C1:DC:B2:41   360
     9 E8:28:C1:DC:B2:40   360
    10 E8:28:C1:DC:B2:42   360

### 6. Частота отправки запросов (beacons)

``` r
ds1 <- ds1 %>% 
  mutate(beacon_rate = beacons / as.numeric(difftime(Last.time.seen, First.time.seen, units = "secs")))

beacon_top10 <- ds1 %>% 
  arrange(desc(beacon_rate)) %>% 
  select(BSSID, beacon_rate) %>% 
  head(10)
beacon_top10
```

    # A tibble: 10 × 2
       BSSID             beacon_rate
       <chr>                   <dbl>
     1 76:E4:ED:B0:5C:9A         Inf
     2 C2:B5:D7:7F:07:A8         Inf
     3 E8:28:C1:DE:47:D1         Inf
     4 A2:FE:FF:B8:9B:C9         Inf
     5 BA:2A:7A:DD:38:3E         Inf
     6 76:5E:F3:F9:A5:1C         Inf
     7 00:03:7F:12:34:56         Inf
     8 E0:D9:E3:49:00:B1         Inf
     9 E8:28:C1:DC:BD:52         Inf
    10 00:26:CB:AA:62:72         Inf

## Данные клиентов

### 1. Определить производителя для каждого обнаруженного устройства

``` r
ds2 <- ds2 %>% 
  filter(BSSID != '(not associated)') %>% 
  mutate(Vendor = substr(BSSID, 1, 8))
unique(ds2$Vendor)
```

     [1] "BE:F1:71" "1E:93:E3" "E8:28:C1" "00:25:00" "00:26:99" "0C:80:63"
     [7] "0A:C5:E1" "9A:75:A8" "8A:A3:03" "4A:EC:1E" "08:3A:2F" "6E:C7:EC"
    [13] "2A:E8:A2" "56:C5:2B" "9A:9F:06" "12:48:F9" "AA:F4:3F" "3A:70:96"
    [19] "8E:55:4A" "5E:C7:C0" "E2:37:BF" "96:FF:FC" "CE:B3:FF" "76:70:AF"
    [25] "00:AB:0A" "8E:1F:94" "EA:7B:9B" "BE:FD:EF" "7E:3A:10" "00:23:EB"
    [31] "E0:D9:E3" "3A:DA:00" "92:F5:7B" "DC:09:4C" "22:C9:7F" "92:12:38"
    [37] "B2:1B:0C" "1E:C2:8E" "A2:64:E8" "A6:02:B9" "AE:3E:7F" "B6:C4:55"
    [43] "86:DF:BF" "02:67:F1" "36:46:53" "82:CD:7D" "00:03:7F" "00:0D:97"

### 2. Обнаружить устройства, которые НЕ рандомизируют MAC-адрес

``` r
norandom <- ds2 %>% 
  filter(!str_detect(BSSID, "^02|^06|^0A|^0E")) %>% 
  filter(BSSID != '(not associated)')
norandom %>% select(BSSID) %>% head(10)
```

    # A tibble: 10 × 1
       BSSID            
       <chr>            
     1 BE:F1:71:D5:17:8B
     2 BE:F1:71:D6:10:D7
     3 BE:F1:71:D5:17:8B
     4 1E:93:E3:1B:3C:F4
     5 E8:28:C1:DC:FF:F2
     6 00:25:00:FF:94:73
     7 00:26:99:F2:7A:E2
     8 0C:80:63:A9:6E:EE
     9 E8:28:C1:DD:04:52
    10 1E:93:E3:1B:3C:F4

### 3. Кластеризация запросов и определение времени появления устройств

``` r
ds2 %>% 
  group_by(Probed.ESSIDs) %>% 
  summarise(
    first_seen = min(First.time.seen),
    last_seen = max(Last.time.seen)
  ) %>% 
  arrange(first_seen)
```

    # A tibble: 38 × 3
       Probed.ESSIDs                 first_seen          last_seen          
       <chr>                         <dttm>              <dttm>             
     1 C322U13 3965                  2023-07-28 09:13:03 2023-07-28 11:53:16
     2 C322U21 0566                  2023-07-28 09:13:03 2023-07-28 11:51:54
     3 C322U06 5179,Galaxy A71       2023-07-28 09:13:06 2023-07-28 11:50:50
     4 GIVC                          2023-07-28 09:13:06 2023-07-28 11:55:54
     5 <NA>                          2023-07-28 09:13:06 2023-07-28 11:56:21
     6 KOTIKI_XXX                    2023-07-28 09:13:08 2023-07-28 11:53:36
     7 AndroidAP177B                 2023-07-28 09:13:09 2023-07-28 11:34:42
     8 Galaxy A71                    2023-07-28 09:13:13 2023-07-28 11:50:22
     9 KC                            2023-07-28 09:13:14 2023-07-28 11:51:50
    10 MIREA_HOTSPOT,Galaxy A30s5208 2023-07-28 09:13:17 2023-07-28 11:56:16
    # ℹ 28 more rows

### 4. Стабильность уровня сигнала в кластерах

``` r
stable_clusters <- ds2 %>% 
  filter(!is.na(Probed.ESSIDs)) %>% 
  group_by(Probed.ESSIDs) %>% 
  summarise(
    mean_time = mean(as.numeric(difftime(Last.time.seen, First.time.seen, units = "secs"))),
    sd_time = sd(as.numeric(difftime(Last.time.seen, First.time.seen, units = "secs")))
  ) %>% 
  filter(sd_time != 0) %>% 
  arrange(sd_time)

stable_clusters %>% head(1)
```

    # A tibble: 1 × 3
      Probed.ESSIDs mean_time sd_time
      <chr>             <dbl>   <dbl>
    1 IKB                 790    9.90

## Оценка результата

Был проведен анализ датасета с использованием библиотек `dplyr`,
`tidyverse` и `readr`. Выполнены задания, включая анализ небезопасных
точек доступа, определение производителей устройств, исследование
сигналов и их стабильности.

## Вывод

В ходе работы были получены знания о методах исследования
радиоэлектронной обстановки и о механизмах работы Wi-Fi сетей на
канальном и сетевом уровнях модели OSI. Закреплены практические навыки
использования языка R и его экосистемы для обработки и анализа данных
сетевой активности. Полученные результаты демонстрируют эффективность
использования R для анализа беспроводных сетей и выявления уязвимостей в
их настройке.
