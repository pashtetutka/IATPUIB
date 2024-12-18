# Исследование данных с использованием пакета dplyr

## Цель работы

1. Овладеть навыками работы с данными в R с использованием пакета **dplyr**.
2. Закрепить работу с базовыми функциями: `select()`, `filter()`, `mutate()`, `arrange()`, `group_by()`, `summarise()`.
3. Освоить способы анализа и преобразования табличных данных на примере встроенного датасета `starwars`.

---

## Исследование структуры датасета

### 1. Общая информация о данных

Для начала изучим структуру датасета **starwars**:

```{r}
str(starwars)
```

### 2. Просмотр первых строк

Посмотрим на первые 6 строк таблицы:

```{r}
head(starwars)
```

---

## Основные задачи

### 1. Вывод столбца с именами персонажей

Используем функцию `pull()` для извлечения столбца с именами:

```{r}
starwars %>% pull(name)
```

### 2. Размер датасета

Определим количество строк и столбцов в датасете:

```{r}
data_size <- list(
  rows = nrow(starwars),
  columns = ncol(starwars)
)
print(data_size)
```

### 3. Уникальные виды (species)

Найдем все уникальные виды персонажей:

```{r}
unique_species <- starwars %>% distinct(species)
print(unique_species)
```

---

## Работа с данными

### 4. Самый тяжёлый персонаж

Определим персонажа с наибольшей массой тела:

```{r}
heaviest <- starwars %>% 
  filter(!is.na(mass)) %>%
  arrange(desc(mass)) %>% 
  slice(1)

print(heaviest %>% select(name, mass))
```

### 5. Персонажи с ростом менее 160 см

Отфильтруем всех персонажей ниже 160 см и выведем их данные:

```{r}
small_characters <- starwars %>%
  filter(height < 160) %>%
  select(name, height)

print(small_characters)
```

### 6. Расчёт ИМТ (индекс массы тела)

Добавим новый столбец с вычисленным ИМТ и отобразим данные:

```{r}
starwars_bmi <- starwars %>%
  mutate(BMI = mass / (height / 100) ^ 2) %>%
  select(name, mass, height, BMI)

print(starwars_bmi)
```

---

## Дополнительные расчёты

### 7. 10 персонажей с наибольшей плотностью массы

Вычислим плотность массы (масса/рост) и найдём топ-10 персонажей:

```{r}
top_density <- starwars %>%
  mutate(density = mass / height) %>%
  filter(!is.na(density)) %>%
  arrange(desc(density)) %>%
  head(10)

print(top_density %>% select(name, mass, height, density))
```

### 8. Средний возраст персонажей по видам

Добавим возраст (на основе `birth_year`) и рассчитаем средний возраст для каждого вида:

```{r}
starwars_age <- starwars %>%
  mutate(age = 2024 - birth_year) %>%
  group_by(species) %>%
  summarise(avg_age = mean(age, na.rm = TRUE)) %>%
  arrange(desc(avg_age))

print(starwars_age)
```

### 9. Самый распространённый цвет кожи

Определим наиболее часто встречающийся цвет кожи:

```{r}
popular_skin <- starwars %>%
  group_by(skin_color) %>%
  summarise(count = n()) %>%
  arrange(desc(count)) %>%
  slice(1)

print(popular_skin)
```

### 10. Средняя длина имени для каждого вида

Добавим длину имени и посчитаем её среднее значение по видам:

```{r}
avg_name_length <- starwars %>%
  mutate(name_length = nchar(name)) %>%
  group_by(species) %>%
  summarise(avg_length = mean(name_length, na.rm = TRUE)) %>%
  arrange(desc(avg_length))

print(avg_name_length)
```

---

## Вывод

В процессе выполнения работы были выполнены следующие задачи:

1. Изучена структура датасета `starwars`.
2. Произведен анализ данных: отфильтрованы строки, рассчитаны новые показатели и выполнены группировки.
3. Закреплены навыки работы с базовыми функциями пакета `dplyr`.
---
title: "Обработка данных с пакетом dplyr"
author: "pashtet.2003@yandex.ru"
format: 
  md:
    output-file: README.md
---

# Исследование данных с использованием пакета dplyr

## Цель работы

1. Овладеть навыками работы с данными в R с использованием пакета **dplyr**.
2. Закрепить работу с базовыми функциями: `select()`, `filter()`, `mutate()`, `arrange()`, `group_by()`, `summarise()`.
3. Освоить способы анализа и преобразования табличных данных на примере встроенного датасета `starwars`.

---

## Исследование структуры датасета

### 1. Общая информация о данных

Для начала изучим структуру датасета **starwars**:

```{r}
str(starwars)
```

### 2. Просмотр первых строк

Посмотрим на первые 6 строк таблицы:

```{r}
head(starwars)
```

---

## Основные задачи

### 1. Вывод столбца с именами персонажей

Используем функцию `pull()` для извлечения столбца с именами:

```{r}
starwars %>% pull(name)
```

### 2. Размер датасета

Определим количество строк и столбцов в датасете:

```{r}
data_size <- list(
  rows = nrow(starwars),
  columns = ncol(starwars)
)
print(data_size)
```

### 3. Уникальные виды (species)

Найдем все уникальные виды персонажей:

```{r}
unique_species <- starwars %>% distinct(species)
print(unique_species)
```

---

## Работа с данными

### 4. Самый тяжёлый персонаж

Определим персонажа с наибольшей массой тела:

```{r}
heaviest <- starwars %>% 
  filter(!is.na(mass)) %>%
  arrange(desc(mass)) %>% 
  slice(1)

print(heaviest %>% select(name, mass))
```

### 5. Персонажи с ростом менее 160 см

Отфильтруем всех персонажей ниже 160 см и выведем их данные:

```{r}
small_characters <- starwars %>%
  filter(height < 160) %>%
  select(name, height)

print(small_characters)
```

### 6. Расчёт ИМТ (индекс массы тела)

Добавим новый столбец с вычисленным ИМТ и отобразим данные:

```{r}
starwars_bmi <- starwars %>%
  mutate(BMI = mass / (height / 100) ^ 2) %>%
  select(name, mass, height, BMI)

print(starwars_bmi)
```

---

## Дополнительные расчёты

### 7. 10 персонажей с наибольшей плотностью массы

Вычислим плотность массы (масса/рост) и найдём топ-10 персонажей:

```{r}
top_density <- starwars %>%
  mutate(density = mass / height) %>%
  filter(!is.na(density)) %>%
  arrange(desc(density)) %>%
  head(10)

print(top_density %>% select(name, mass, height, density))
```

### 8. Средний возраст персонажей по видам

Добавим возраст (на основе `birth_year`) и рассчитаем средний возраст для каждого вида:

```{r}
starwars_age <- starwars %>%
  mutate(age = 2024 - birth_year) %>%
  group_by(species) %>%
  summarise(avg_age = mean(age, na.rm = TRUE)) %>%
  arrange(desc(avg_age))

print(starwars_age)
```

### 9. Самый распространённый цвет кожи

Определим наиболее часто встречающийся цвет кожи:

```{r}
popular_skin <- starwars %>%
  group_by(skin_color) %>%
  summarise(count = n()) %>%
  arrange(desc(count)) %>%
  slice(1)

print(popular_skin)
```

### 10. Средняя длина имени для каждого вида

Добавим длину имени и посчитаем её среднее значение по видам:

```{r}
avg_name_length <- starwars %>%
  mutate(name_length = nchar(name)) %>%
  group_by(species) %>%
  summarise(avg_length = mean(name_length, na.rm = TRUE)) %>%
  arrange(desc(avg_length))

print(avg_name_length)
```

---

## Вывод

В процессе выполнения работы были выполнены следующие задачи:

1. Изучена структура датасета `starwars`.
2. Произведен анализ данных: отфильтрованы строки, рассчитаны новые показатели и выполнены группировки.
3. Закреплены навыки работы с базовыми функциями пакета `dplyr`.
