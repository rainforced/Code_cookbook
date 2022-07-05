# Функции 
```r
foo <- function(x, y)
{   
    z = x/y # local environment
    return(z)
}
a = foo(x = 1, y = 2)
b = foo(1, 2)
```
**Важно:** Если в ф-ции нет `return()`, то она вернет последнее посчитанное значение (а не NULL/void как в других языках)