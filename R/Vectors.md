# Вектор
## Свойства
- Это непрервыный массив (как в C). Это значит, что под него выделяется непрерывный кусок памяти. Если он превышен, то вектор переписывается в новом участке памяти. Поэтому, вектор - условно изменяеймый т.е. если у нас есть запас памяти, то мы можем добавить элемент, если нет - переписываем весь массив заново. Также, нельзя вставить или удалить элемент из середины без переписывания массива.
- В записи `x <- vector(2)` - `x` - это указатель, указывающий на участок памяти выделенный под массив. При этом, в отличии от C, укащатели тут не имеют типа, значит операция ниже валидна:
  ```r
  > v <- 1:10 # int vector
  > v
  [1]  1  2  3  4  5  6  7  8  9 10
  > v <- 'adasd' # character vector == string
  > v
  [1] "adasd"
  ```
- Содержит данные одного типа (**mode**). Типы веторов:
    1. logical
    2. int
    3. double/numeric
    4. Complex
    5. Character
    6. raw (байтовые строки)

- Если мы добовляем новый тип данных, то вектор делает автоматическое приведение типа в соответствии с иерархией (см. выше) так, чтобы уместить все данные:
    ```r
    > z <- vector(length = 2)
    > z
    [1] FALSE FALSE #bool
    > z[1] <- 2
    > z
    [1] 2 0 #int
    > z[2] = 'a'
    > z
    [1] "2" "a" #str
    ```
- проиндексирован (т.е. можно обращаться к элементу через его номер). **Индексы начинаются с 1** т.е. вектор размера n простирается с v[1] до v[n].
- скаляры в R - тоже векторы. Например, число 3 или литерал 'aa - это структура вектор. 

## Объявление/создание вектора
```r
> z <- vector(length = 2) # создается булевый вектор с FALSE
> z
[1] FALSE FALSE #bool
```
**concatenate** - `c`
```r 
> x <- c(1,2,3) # создали вектор
> x
[1] 1 2 3
> y <- c(x, 5, 6) # combine вектор x и числа (они тоже векторы)
> y
[1] 1 2 3 5 6
> z <- c(y, 'a') # combine вектор y и символ (все типы изменились)
> z
[1] "1" "2" "3" "5" "6" "a"
```
**repeat** - `rep` - повторяет указанный вектор несколько раз
```r
> x <- c(1,2,3)
> y <- rep(x, times = 2) # кол-во повторений вектора x
> y
[1] 1 2 3 1 2 3
> z <- rep(1:3, times = 2)
> z
[1] 1 2 3 1 2 3
> a <- rep(x, length = 5) # можно указать какой длинны вектор должен быть, а кол-во повторений будет вычислено автоматически
> a
[1] 1 2 3 1 2
> b <- rep(x, length.out = 5)
> b
[1] 1 2 3 1 2
```
## Атрибуты 
Все объекты в R - это классы. У каждого типа данных есть свой набор атрибутов. **Любому объекту в R можно также добавить свои собственные атрибуты**. Например:
```r
> v <- 1:5
> attributes(v)
NULL
# создадим свой атрибут 
> attr(v, "my own property") <- "I create CUSTOM properties"
# names - это дефолтный атрибут  
> names(v) <- c("one", "two")
> attributes(v) # посмотреть атрибуты
$`my own property`
[1] "I create CUSTOM properties"

$names
[1] "one" "two" NA    NA    NA   

> v
 one  two <NA> <NA> <NA> 
   1    2    3    4    5 
attr(,"my own property")
[1] "I create CUSTOM properties"
> attributes(v) <- NULL # удалим все атрибуты
> attributes(v) 
NULL
```
- `attributes(v)` - доступ к атрибутам объекта v
- `names(v) <- ... ` - names - это дефолтный атрибут. Мы можем его поменять.
- `attr(v, "my own property") <- ... ` - создать свой атрибут
- `attributes(v) <- NULL` - удаление всех атрибутов

### length
Это один из дефолтных атрибутов.   
- `length(v)` - ф-ция показывающая значение атрибута длины ветора.
- Длинну вектора можно менять через команду присваивания `<-`. См. пример ниже: 
    ```r
    > a <- 1:10
    > a
    [1]  1  2  3  4  5  6  7  8  9 10
    > length(a) # смотрим длинну вектора
    [1] 10
    > length(a) <- 5 # изменили длинну вектора 
    > a
    [1] 1 2 3 4 5
    > length(a) <- 7 # еще раз именили длинну вектора
    > a
    [1]  1  2  3  4  5 NA NA
    ```
    При этом, вектор заполнит недостающие значения `NA`.

## Индексирование 
Поменять значение элемента:
```r
v[i] <- var
var -> v[i] # то же самое 
```
### Численное
- Положительные числа от `1` до `length(x)` - возвращают соответствующий элемент 
- Отрицательные числа: возвращается вектор без соответствующего элемента. Например:
    ```r
    > v = 1:6
    > v[c(-1, -3, -length(v))]
    [1] 2 4 5 # убрали 1й, 3й и последний элементы
    ```
### Индексирование вектором
Передается вектор, элементы которого являются индексами. Общая форма `vector1[vectro2]`. Повторы в vector2 разрешены - возвращают один и тот же элемент vector1. Например:
```r
> v = 1:6
> v[c(1, 3, length(v))]
[1] 1 3 6 # включили 1й, 3й и последний элементы
> v[c(-1, -3, -length(v))]
[1] 2 4 5 # исключили 1й, 3й и последний элементы
```
### Логическое
Это индексирование логическим вектором: т.е. каждый элемент проверяется на TRUE/FALSE. Возвращается только то, что соответвует TRUE:
```r
> v = 1:10
> v[rep(c(TRUE, FALSE), length(v)/2)] 
[1] 1 3 5 7 9
# тут создается вектор (TRUE, FALSE, TRUE, FALSE, ...x 5)
# Соответственно возвращаются только нечетные элементы
```
Если логический вектор меньше длинны опорного ветора, то он расширяется по правилу переписывания:
```r
> v = 1:10
> v[c(TRUE, FALSE)]
[1] 1 3 5 7 9
> v[c(TRUE, FALSE)]
[1] 1 3 5 7 9
# тут вектор расширился до (TRUE, FALSE, TRUE, FALSE, ...x 5)
> v[v > 3 & v < 6]
[1] 4 5
# тут создается 2 вектора:
# 1й где все TRUE для поэлеметного сравнения v[i] > 3
# 2й где все TRUE для поэлеметного сравнения v[i] < 6
# их пересечение - это элементы TRUE для обоих векторов 
```
Пример логического пересечения:
```r
# Иллюстрация к примеру выше
> a = c(FALSE, FALSE, TRUE, TRUE)
> b = c(TRUE, FALSE, TRUE, FALSE)
> c = a & b
> c
[1] FALSE FALSE  TRUE FALSE
```
### Через имя (для именовынных векторов)
```r
> v <- c("one" = 1, "two" = 2)
> v
one two 
  1   2 
> v['one']
one 
  1 
```

## Именованные вектора
- Каждому элементу вектора можно присвоить собственное имя:
    ```r
    > a <- c(one = 1, two = 2, "three etc" = 3, 4)
    > a
        one       two three etc           
            1         2         3         4 
    > names(a)
    [1] "one"       "two"       "three etc" ""    
    ```
    - При этом, если слово без пробелом и особых символов, то можно писать без ковычек (в примере, *one* и *two*). Иначе, надо писать в ковычках (в примере, *"three etc"*)
    - `names(v)` - вернет имена вектора
    - Можно оставить часть элементов без имени (в примере, *4*) 
- Через ф-цию `names(v)` можно присвоить (или удалить) имена элементам: 
    ```r
    > b <- c(1, 2, 3, 4)
    > b
    [1] 1 2 3 4
    > names(b) <- c("one", "two", "three")
    > b
    one   two three  <NA> 
        1     2     3     4 
    > names(b) <- NULL # удалить имена
    > b
    [1] 1 2 3 4
    ```
## Векторизация
Арифметика в R **векторизована** - т.е. применяется поэлементно: 
    ```r
    > 4*c(1,2,3)
    [1]  4  8 12
    > 4 + c(1,2,3)
    [1] 5 6 7
    > c(1,2,3) * c(1,2,3)
    [1] 1 4 9
    ```
- Многие другие ф-ции в R векторизованы, но не все. Обычно, это соответветсвует здравому смыслу. Например:
    ```r
    > sqrt(c(1,2,3))
    [1] 1.000000 1.414214 1.732051
    > floor(c(1.4,2.1,3.7))
    [1] 1 2 3    
    ```
### Векторизация быстрее циклов
Векторизация всегда быстрее циклов. Сравним способа найти вектор квадратов какого-то большого вектора:
```r
# 1й случай: for, x размера 1
v <- 1:10^5
system.time({
  x <- 0
  for (i in v) x[i] <- sqrt(v[i])
})
# 2й случай: for, y размера 10^5 
system.time({
  y <- vector(length = 10^5)
  for (i in v) y[i] <- sqrt(v[i])
})
# 3й случай: векторизация
system.time({
  z <- sqrt(v)
})

identical(x, y, z)

# время 1го случая
   user  system elapsed 
 34.818   0.175  34.955 
# время 2го случая
   user  system elapsed 
   0.23    0.00    0.23 

# время 3го случая
   user  system elapsed 
  0.002   0.000   0.002 

# результат - одинаковые векторы
> identical(x, y, z)
[1] TRUE
```
- В случаях 1 и 2, цикл сильно затормозил вычисления. Также, в 1ом случаии, под принимающий вектор не было выделено достаточно места с самого начала, поэтому его нужно было расширять - это заняло дополнительное время. 
- 3й случай самый быстрый.

# Операции над векторами
## Правила переписывания (recycling rules)
Операции на векторами проводятся только, если оба вектрова одинаковой длинны. Если мы проводим операцию на двух веторах разной длины, то их приводят к одинаковой длине, применяя правила переписывания:
1. Длина результата равна длине наибольшего из векторов.
2. Меньший вектор дублируется/переписывается (т.е. расширяется) так, чтобы его длина совпала с длиной большего вектора.
3. Если длина большего вектора не делится на длину меньшего нацело, то выдается предупреждение.
Например:
```r
> 1:5 + 0:1
[1] 1 3 3 5 5
Warning message:
In 1:5 + 0:1 :
  longer object length is not a multiple of shorter object length
# на самом деле 2ой вектор расширяется до размера 1го:
# (1, 2, 3, 4, 5) + (0, 1, 0, 1, 0) 
```
Так как скаляр - это единичный ветор, то любая операция со скаляром подчиняется правилу переписывания:
```r
> 1:5 + 3
[1] 4 5 6 7 8
# на деле:
# (1, 2, 3, 4, 5) + (3, 3, 3, 3, 3)
```
Налогично поведение с другими типами данных:
```r
> (1:5) > 3
[1] FALSE FALSE FALSE  TRUE  TRUE
# тут вектор 3 расширяется до (3, 3, 3, 3, 3) и происходит почленное сравнение
```

## all, any, which
```r
> v <- 1:20
> v
 [1]  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18 19 20
# Проверяет верность условия для всех элементов
> all(v>0)
[1] TRUE
> all(v>1)
[1] FALSE
# Проверяет верность условия хотя бы для одного элемента
> any(v>1)
[1] TRUE
> any(v>20)
[1] FALSE
# возвращает индектсы для который выполнено условие
> which(v > 15)
[1] 16 17 18 19 20
> which.min(v > 15) # индекс min
[1] 1
> which.max(v > 15) # индекс max
[1] 16
```
Если надо найти индексы максимальных значений (а их может быть несколько) то можно воспользоваться следующей конструкцией:
```r
> which(y == max(y))
[1] 40 52 71
```