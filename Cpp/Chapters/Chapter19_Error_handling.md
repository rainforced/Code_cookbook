# Chapter19: Error handling
## 1. Motivation

There are two types of errors:
1. **Syntax errors** - wrong C++ syntax. Compiler will find it.      
    Например:
    ```
    f 5 > 6 then write "not equal";
    ```
    Вместо:
    ```cpp
	if (5 > 6)
	    std::cout << "not equal";
    ```
2. **Semantic errors** - code that is correct from syntax perspective (compiler won't pick it up), but which produces wrong/unexpected behaviour. There are few types:
    1. **Logic errors** - errors occur in logic statements
        ```cpp
        	if (x >= 5)
	            std::cout << "x is greater than 5";
        ```
    2. **Violated assumption** - programmer assumes some behavior from user/code/compiler which can be violated:
        ```cpp
        	char strHello[] = "Hello, world!";
        	std::cout << "Enter an index: ";
        	 
        	int nIndex;
        	std::cin >> nIndex; //user can give any input, not nessesarely `unsigned int`
        	 
        	std::cout << "Letter #" << nIndex << " is " << strHello[nIndex] << std::endl;
        ```

**Defensive programming** - programmer predicts where error could appear. You can:
1. Terminate a programm due to the error
2. Handle the error 


## 2. std::assert(), std::exit() and std::cerr()
### std::assert() and std::static_assert()
`std::assert(logic_condition)` is a preprocessor macro that evaluates a conditional expression (logic) at runtime. If the conditional expression is true, the assert statement does nothing. If the conditional expression evaluates to false, an error message is displayed and the program is terminated. 
```cpp
double calculateTimeUntilObjectHitsGround(double initialHeight, double gravity)
{
  assert(gravity > 0.0); // The object won't reach the ground unless there is positive gravity.
 
  if (initialHeight <= 0.0)
  {
    return 0.0;		// The object is already on the ground. Or buried.
  }
  return std::sqrt((2.0 * initialHeight) / gravity);
}
```
To make it more discriptive you may write assert with `&&` "error messsage". Since not empty string is always True, than `condition && message` will be the same as the condition. 
```cpp
assert(found && "Car could not be found in database");
```

`std::static_assert<logic_condition, message>` is the same thing but it is checked by compiler at compile time. 
```cpp
static_assert(sizeof(long) == 8, "long must be 8 bytes");
static_assert(sizeof(int) == 4, "int must be 4 bytes");
 
int main()
{
	return 0;
} 
```

### std::cerr
It is a stream (just like `std::cout`), so it stops the program and returns the stream to terminal/file: 
```cpp
void printString(const char *cstring)
{
    // Only print if cstring is non-null
    if (cstring)
        std::cout << cstring;
    else
        std::cerr << "function printString() received a null parameter";
}
```

### std::exit()
This command just terminates the program and return the indicated code the OS (1 is normal termination, any other number is an error):
```cpp
#include <cstdlib> // for std::exit()
#include <array>
 
int getArrayValue(const std::array<int, 10> &array, int index)
{
    // use if statement to detect violated assumption
    if (index < 0 || index >= static_cast<int>(array.size()))
       std::exit(2); // terminate program and return error number 2 to OS
 
    return array[index];
}
```
Termination means that the program is stopped and all the data is lost (unless it was handled before `exit` call).

## 3. Exceptions
### Keywords
- **throw** used to throw an exception. It could be any type:
	```cpp
	throw -1; // throw a literal integer value
	throw ENUM_INVALID_INDEX; // throw an enum value
	throw "Can not take square root of negative number"; // throw a literal C-style (const char*) string
	throw dX; // throw a double variable that was previously defined
	throw MyException("Fatal Error"); // Throw an object of class MyException
	```
- **try** - *observer block*. The block where the program looks for exceptions.
- **catch(type)** - *handler block*. The block where the problem is handled before program proceeds. It is specific to the type. So, 
	- `catch(int x)` - will catch only `throw` with int value. `
	- `catch(const std::string& str)` - will catch only `throw` with string. Чтобы передача в блок не происходила значению (те копированием), то сложные типа надо проверять/передавать по const reference.
	- `catch(const Dude& my_dude)` - только для `throw` передающего объект класса Dude.    

### rules of excaption handling 
1. When an exception is raised (using throw), execution of the program immediately jumps to the nearest enclosing try block (propagating up the stack if necessary to find an enclosing try block). If any of the catch handlers attached to the try block handle that type of exception, that handler is executed and the exception is considered handled. When the exception is handled the program continues normally. If an exception is routed to a catch block, it is considered “handled” even if the catch block is empty.     

2. If no appropriate catch handlers exist, execution of the program propagates to the next enclosing try block (up the stack). If no appropriate catch handlers can be found before the end of the program, the program will fail with an exception error.

3. Note that the compiler *will not perform implicit conversions or promotions when matching exceptions with catch blocks!* For example, a char exception will not match with an int catch block. An int exception will not match a float catch block. However, casts from a derived class to one of its parent classes will be performed.

Пример простой (но нереалистичной) программы:
```cpp
#include <iostream>
#include <string>
 
int main()
{
    try	
    {	// Statements that may throw exceptions you want to handle go here
        throw -1; // here's a trivial example
	std::cout << "This never prints\n"; // once the exception is thrown, program leaves the current try block
    }
    catch (int x)
    {   // Any exceptions of type int thrown within the above try block get sent here
        std::cerr << "We caught an int exception with value: " << x << '\n';
    }
    catch (double) // no variable name since we don't use the exception itself in the catch block below
    {
        // Any exceptions of type double thrown within the above try block get sent here
        std::cerr << "We caught an exception of type double" << '\n';
    }
    catch (const std::string &str) // catch classes by const reference
    {
        // Any exceptions of type std::string thrown within the above try block get sent here
        std::cerr << "We caught an exception of type std::string" << '\n';
    }
 
    std::cout << "Continuing on our merry way\n";
 
    return 0;
}
```

### Stack unwinding
В программе выше exception вызвано прямо в try блоке, который тут же все обработал. На самом деле, так не делают. Обычно кто-то пишет свою ф-цию/объект, а у него при определенном повидении вызывается исключение. Обработка же пишется вне этой ф-ции/объекта. 
