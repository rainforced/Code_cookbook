# Chapter 7 Control Flow

Main flow control statements:   
- **halt** - statement that tells the program to quit running immediately. (напр., `exit()`, `break;`).
- **Jump** - causes the CPU to jump to another statement. (`goto`, `break`, and `continue` keywords, любая не main() ф-циия т.к. при вызове CPU прыгает к ее началу).
- **Conditional branches** - statement that causes the program to change the path of execution based on the value of an expression (`if-else`, `switch`).
- **Loops** - cause the program to repeatedly execute a series of statements until a given condition is false.
- **Exceptions** - a mechanism for handling errors that occur in a function. If an error occurs in a function that the function cannot handle, the function can trigger an exception. This causes the CPU to jump to the nearest block of code that handles exceptions of that type.
## Branches (Ветвление)
### if else 
```cpp
if (bool){
body}
else{
body}
```
- Может сравнивать строки (лексикографический, т.е. как в словаре): "a" < "aa" < "ab" < "aba" и т.д. (т.е. чем раньше по славорю, тем меньше строка. Строка сравнивается последовательно по каждой букве. При равных условиях, чем длинее строка, тем она больше)
- Сравнивает векторы аналогичным образвом:
```cpp
vector<int> nums1 = {1,2,3};     // nums2 > nums3 > nums1
vector<int> nums2 = {1,3,4};
vector<int> nums3 = {1,2,3,4};
if(nums3<nums2){
    cout << "more" << endl;
}
else{
    cout << "less" << endl;
}
``` 
Также есть упрощенная форма для вложенных if-else:
```cpp
// will check condition 1, then condition 2, then else
if (condition 1){
body 1}
else if(condition 2){	// аналогично elif в python
body 2}
else {
body3}
```

### Switch
Это способ ветвления по значению **integral data type** (т.е. char, short, int, long, long long, or enum). При этом переменная должны быть **compile time** (те цифра или enum):
```cpp
int x;
std::cin >> x;			// пусть будет 2
switch (x)		
{
   case 1: 			// Does not match -- skipped
       std::cout << 1 << '\n';
       break;
   case 2: 			// Match!  Execution begins at the next statement
       std::cout << 2 << '\n';  // Execution begins here
       break; 			// Break terminates the switch statement
   case 3:
   case 4:			// выполнистся код ниже при успловии x= 3 или 4
       std::cout << "3 or 4" << '\n';
       break;
   default:
       std::cout << 5 << '\n';
       break;
}
// Execution resumes here
```
Тут `x` проверсяется на равенсво стейтменту послу `case`, когда они совпали, то воспроизводится код ниже. Если не было совпадений, то воспроизводится код после `default`. При этом, если несколько `case` идут подряд (как для 3 и 4 в примере), то они работают как единый case (т.е. код выполнится при совпаденнии с любым из них).   

**Execution halt**   
В switch не используются блоки ({}) - отсюда странные условия выхода из case. Как только x совпал с case, выполнится весь код идущий за ним (включая любые ниже идущие case) (называется **fall-through**):
```cpp
switch (2)
{
   case 1: 			// Does not match
       std::cout << 1 << '\n';  // skipped
   case 2: 			// Match!
       std::cout << 2 << '\n';  // Execution begins here
   case 3:
       std::cout << 3 << '\n';  // This is also executed
   default:
       std::cout << 5 << '\n';  // This is also executed
}
```
Выполение внутри switch прирвется только после: 1) конец всего блока switch (после default), 2)`break`, 3)`return`. Поэтому под кодом case должны всегда стоять либо break, либо return.

**Scopes**   
Весь блок switch имеет единую область видимости. Поэтому, если объявить переменную в `case 1` (который не будет выполнен), а определить ее в `case 2` (который будет выполенен), то все выполнится:
```cpp
switch (2)
{
    int a; 		// okay, declaration is allowed before the case labels
    int b = 5; 		// illegal, initialization is not allowed before the case labels
 
    case 1:
        int y;		// okay, declaration is allowed within a case
        y = 4; 		// okay, this is an assignment
        break;
 
    case 2:
        y = 5; 		// okay, y was declared above, so we can use it here too
        break;
 
    case 3:
        int z = 4; 	// illegal, initialization is not allowed within a case
        break;
    case 4:
    	{
		int x = 4; 	// ok, тут отдльный scope, не принадлежащий общему scope case
        	break;		// в данном примере смысла мало, тк x будет унижтожен при выходе из этого scope
	}
}
```
Т.к. объявление - это выделение памяти для переменной при компиляции, то объявленной переменной в любом месте (под любым case и до них) может пользоваться весь блок (например, переменными `a` и `y`). Однако, определение переменной производится на этапе выполнения, и тут уже нужно зайти в какой либо case. Тогда при инициализации (напр., `int z = 4`), копилятор должен выделить память под z, но еще неизвестно зайдет ли программа в `case 3` - поэтому это действвие запрещенно. Однако, если создать отдельный scope внутри case (выделить нужный код в {}), то все будет ок (см. пример `case 4`).

## Halts and jumps
### goto 
`goto placeFlag` - оператор, который заставляет CPU выполнить переход из одного участка кода к placeFlag (осуществить прыжок).
```cpp
	double z;
tryAgain: 					// это лейбл
	std::cout << "Enter a non-negative number: "; 
	std::cin >> z;
 
	if (z < 0.0)
goto tryAgain; 					// а это оператор goto 
 	std::cout << "The sqrt of " << z << " is " << sqrt(z) << std::endl;
```
- У goto - **область видимости функции** (т.е. goto и соответствующий лейбл (placeFlag) должны находиться в одной и той же функции.    
- Нельзя перепрыгнуть вперёд через переменную, которая инициализирована в том же блоке, что и goto:
```cpp
goto skip; // прыжок вперёд недопустим
    int z = 7;
skip: // лейбл
    z += 4; // какое значение будет в этой переменной? Она даже не определена
```

### break and continue
`break` - закончивает выполение блока {}. Если это switch или loop, то на этом преращается выполнение цикла, а CPU переходит на строку следующую сразу за ним (вне цикла).    
`continue` - оператор перемещающий CPU к концу блока. В случаии switch - это просто его конец. В случаии loop - это конец данной итерации цикла, после этого начнется следующая итерация. Нужно быть внимтельным при использовании continue и while (а также for(;condition;)) где счетчик в теле цикла: если continue перепрыгнет инкримент счетчика, то получится бесконеный цикл:
```cpp
while (count < 10)
	{
		if (count == 5)
			continue; 		// переходим в конец тела цикла
		std::cout << count << " ";
		++count;			// перепрыгнули инкримент счетчика
	// Точка выполнения после оператора continue перемещается сюда. Цикл стал бесконеным
	}
```
В этом случаии лучше использовать do-while:
```cpp
do
	{
		if (count == 5)
			continue; 		// переходим в конец тела цикла 
		std::cout << count << " ";
		// Точка выполнения после оператора continue перемещается сюда
	} while (++count < 10);			// инкремент счетчика находится вне тела цикла 
```

## loops
- `break;` - выход из цикла  
### for loop
1. `for (int n=10; n>0; n--){}` - general view

**Особенности**   
- Можно определить for только с успловием (`for(; condition; )`), если счетчик объявлен вне цикла и есть его инкремент/декремент(внутри цикла): 
   ```cpp
	int count = 0;				// счетчик объявлен вне цикла
	for (; count < 10; )
	{
		std::cout << count << " ";
		++count;			// инкремент/декремент счетчика контролирует цикл
	}
   ```
- Можно делать циклы по нескольким переменным: 
   ```cpp
   for (int aaa = 0, bbb = 9; aaa < 10; ++aaa, --bbb)
		std::cout << aaa << " " << bbb << std::endl;	
   /* вывод будет 0 9 / 1 8 / 2 7 / 3 6 / etc */
   ```
- У оператора `for` область видимости ограничена самим циклом (т.е. счетчики `aaa` и `bbb` будут уничтожены при выходе из цикла (полного цикла, а не каждой итерации как у `while`). (Однако, в старых версиях счетчики не удалялись после цикла).

2. **foreach loop** (для C++ 11 и выше)
Это специальный цикл for для контейнерова(массивы, строки и т.д.): `for (data_type element: container){}`. Каждый обработанный элемент массива копируется в переменную element. Поэтому внутри тела массива можно получить значение элемента без индексирования (оно в foreach не поддерживается). Можно сделать data_type = auto (`for (auto c: container){}`) - компилятор сам выберет нужный тип.

    ```cpp
    vector<int> nums= {1,2,3};
    string hw = "Hello world!"
    for (int c: nums){cout << c << ",";}
    for (char c: nums){cout << c << ",";}
    ```
Т.к. элемент массива только копируется в переменную element надо которой происходит действия внутри тела цикла, то сами элементы не могут быть изменены. Так же, если элемент очень большой (например, массива длинных строк), то его копия затратна. Можно "перебирать" элементы по ссылке (подобно передачи ссылке в ф-цию): 
```cpp
for (auto &element: array) // символ амперсанда делает element ссылкой на текущий элемент массива, предотвращая копирование
        std::cout << element << ' ';
```
Если же при этом надо убедиться, что элемент не изменится, то нужно использовать константный указатель (`for (const auto &element: array)`).

**foreach должен знать длинну контейнера**. Поэтому через него нельзя выводит динамические массивы и массивы переданные через *расподания в указатель*:
```cpp
int sumArray(int array[]) 		// array - расподается в указатель
{
    int result = 0;
    for (const auto &number : array) 	// ошибка компиляции, размер массива неизвестен
        result += number;
 
    return sum;   
}
```

### while и while do
1. `while(bool) {body}` - сначала проверяет условие, а затем выполняет.  
2. `do {body} while (bool);` - сначала выполняет, а потом проверяет условие.

**Особенности**   
Поскольку тело цикла обычно является блоком, и поскольку этот блок выполняется по новой с каждым повтором, то любые переменные, объявленные внутри тела цикла, создаются, а затем и уничтожаются по новой. Например:
```cpp
int result = 0;		// result определенн вне блока {} -> будет жить после всех циклов
while (count <= 6) 	// итераций будет 6 
{
	int z; 		// z создаётся здесь по новой с каждой итерацией
        std::cout << "Enter integer #" << count << ':';
        std::cin >> z;
 
        result += z;
	++count;	// Увеличиваем значение счётчика цикла на единицу
} 			// z уничтожается здесь по новой с каждой итерацией
```
