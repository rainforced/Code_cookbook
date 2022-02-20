# Chapter13 Exception handling 
## exit() и cerr
`exit()` - закрывает программу. При этом не сохраняются файлы, датабазы и тп.  
`cerr` — это объект вывода (как и cout), который находится в заголовочном файле iostream и выводит сообщения об ошибках в консоль (как и cout), но только эти сообщения можно ещё и перенаправить в отдельный файл об ошибках. 
```cpp
void printString(const char *cstring)
{
    if (cstring)    // Выводим cstring при условии, что он не нулевой
        std::cout << cstring;
    else
        std::cerr << "function printString() received a null parameter";
}
```

## assert и static_assert
Требует библиотек `#include <cassert>`.    
`assert` - (или ещё «оператор проверочного утверждения») в C++ — это макрос препроцессора, который обрабатывает условное выражение во время выполнения. Если условное выражение истинно, то оператор assert ничего не делает. Если же оно ложное, то выводится сообщение об ошибке, и программа завершается. Это сообщение об ошибке содержит ложное условное выражение, а также имя файла с кодом и номером строки с assert. Например:
```cpp
int getArrayValue(const std::array<int, 10> &array, int index)
{
    // Предполагается, что значение index-а находится между 0 и 8
    assert(index >= 0 && index <= 8); // это строка 6 в Program.cpp
 
    return array[index];
}
//Если assert сработает, то будет выведено:
//Assertion failed: index >= 0 && index <=8, file C:\\VCProjects\\Program.cpp, line 6
```
Вывод assert не очень информативный, для человека, который сам не писал этот код (или писал его давно). Для легкости понимания, в логическое условие обычно добавляют C-style строку с описанием ошибки. В логических стейтментах C-style строки возвращают True, поэтому, если их вставить через `&&` (логическое И), то она не будет влиять на верность условия по assert:
```cpp
assert(found && "Animal could not be found in database");
//Если assert сработает, то будет выведено:
//Assertion failed: found && "Animal could not be found in database", file C:\\VCProjects\\Program.cpp, line 42
```

### static_assert
Обычный assert рабоет в run-time. Есть специальная версия для complie-time: `static_assert(условие, "сообщение")`. Если ее условие не истинно, то копилятор пожалуется. Это значит, что само условие должно быть тоже complie-time. Поскольку static_assert не обрабатывается во время выполнения, то стейтменты static_assert могут быть размещены в любом месте кода (даже в глобальном пространстве).
```cpp
static_assert(sizeof(long) == 8, "long must be 8 bytes");
```

### Debuger vs release (NDEBUG)
`#define NDEBUG`- Обычно assert используется в версиях, которые на отладке. Когда готовится release версия программы, то assert быть не должно. Для этого прописывается директива: `#define NDEBUG`. Она отключает все assert. Во многих IDE, когда программа реализуется в  release версии, IDE сама ставит `#define NDEBUG`.
