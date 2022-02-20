# std:: типы данных
## Строка
```cpp
#include <string>
string n = "Hello,";
string m = " world!";
string a = m + n;	// поддерживают конкатенацию
std::cout << a << a[2]; 	//и прямой вывод строки; Также поддерживает [] (индексирование)
//выведет "Hello, world!";
if (n > m)		//поддерживает прямое сравнение 
	//TODO
```
Строки можно напрямую сравнивать. Наприпример,
```cpp
std::string s= "Vasya";
if(s == "Vasya")	//будет true
	//do something
if(s == "Kolya")	//будет false
	//do something
```
Обычно это полезно при чтении файлов:
```cpp
std::fstream in( filename );				//открыли файл
for( std::string line; std::getline( in,line ); )	//считываем строку за строкой
{					
	if( line == "[Tile Size]" )			//строка целикос состоит из "[Tile Size]"
	{
		in >> tileSize;				//следующая за "[Tile Size]" строка, тк getline(in, line)
	}						//и in>>, оба оператора считывают с потока: getline() - всю строку, 
	else if( line == "[Board Dimensions]" )		// in>> - часть текста до пробела
	{
		in >> boardWidth >> boardHeight;
	}
	else
	{
		std::cout << "Man, the is something else";
	}
```
### Методы
- `my_str.find(other_str)` - смотрит, есть ли в my_str подстрока other_str. Возвращает индекс (int), указывающий на первое совпадение. Если other_str = "" (те пустая строка), то возвращет 0 (тк "" содержится в любой строке, причем в начале). Если other_str не найдена, то возвращает `std::string::npos`.
	```cpp
	std::ifstream input(path_in);
	for (std::string line; std::getline(input, line);)
	{	
		if (line.find("[Skip]") == std::string::npos	//will skip is the line contains "[Skip]"
		{
			//do somethig 
		}
	}
	```
- `my_str.length()` - возвращает длинну строки
- `my_str.to_string(42);` - преобразует число в строку

### Не методы, но полезные ф-ции
- `std::stoi(str)` - преобразует строку в число. Например, `int num = std::stoi("69")`. `std::stof("69.69f")` - тоже самое, но для float.
- `std::getline(std::cin, my_str)` - cin принимает строку целиком не прирываясь на пробелах (если без getline(), то cin принимает строку до пробела). Параметры: (std::cin, строка). `std::cin.ignore(32767, '\n')` -  игнорирует до 32767 символов до тех пор, пока \n не будет удалён из потока. Это нужно когда за cout идет getline():
	```cpp
	std::cout << "Pick 1 or 2: ";
	int choice;
	std::cin >> choice; 		/* (пусть мы выбрали 2) когда мы ввели 2, cin фактически получил '2\n', 
	тогда поэтому следующий стейтмент std::getline(std::cin, myName) получит '\n' 
	std::cin.ignore(32767, '\n');  - этот стейтмент все исправит */
	std::cout << "Now enter your name: ";
	std::string myName;
	std::getline(std::cin, myName);
	```

## Array
```cpp
#include <array>
// при объявлении или инициализации
std::array<int, 4> myarray0;
std::array<int, 4> myarray1 = { 8, 6, 4, 1 }; // список инициализаторов
std::array<int, 4> myarray2 { 8, 6, 4, 1 }; // uniform инициализация

// Доступ к значениям массива через оператор индекса осуществляется как обычно
myarray[2] = 7;
```
В отличии от обычных массивов, `std::array` передаются в ф-ции не распадаясь в указатель, поэтому с ними удобно работать в ф-циях.
```cpp
void printLength(const std::array<double, 4> myarray)
{
	for (auto a: myarray)		// будет работать! В отлиции от обычных массивов
		std::cout << "length: " << a;
}

int main()
{
	std::array<double, 4> myarray{ 8.0, 6.4, 4.3, 1.9 };
	printLength(myarray);
}
```

### Методы
- `myarray.at(8) = 7;` - проверяет, существует ли элемент 8 (достаточно ли велик массив), если да, то возвращает ссылку на этот элемент, которой может быть присвоено значение. Если нет, то вернет исключение `std::out_of_range`. Все аналогичего `myarray[8] = 7`, только тут проверяются границы массива. 
- `myarray.size()` - возвращает длинну массива.


## Vector (одномерный массив любого типа)
Это тот же динамический массив, но который может сам управлять выделенной себе памятью. Это означает, что вы можете создавать массивы, длина которых задаётся во время выполнения, без использования операторов new и delete (явного указания выделения и освобождения памяти). Когда переменная-вектор выходит из области видимости, то она *автоматически освобождает память*, которую контролировала (занимала). Это не только удобно (так как вам не нужно это делать вручную), но также помогает предотвратить утечки памяти.      

Тк внутри вектор использует стандартное выделение памяти на куче (`new`), то выделяется целый кусок памяти так, что последовательный элементы выктора идет последовательно в памяти.     

```cpp
#include <vector>
 // Нет необходимости указывать длину при инициализации
std::vector<int> array0; // векстор рамера 0
std::vector<int> array1(n); // векстор рамера n, где n - run-time переменная 
std::vector<int> array2 = { 10, 8, 6, 4, 2, 1 }; // используется список инициализаторов для инициализации массива
std::vector<int> array3 { 10, 8, 6, 4, 2, 1 }; // используется uniform инициализация для инициализации массива (начиная с C++11)

std::vector<bool> array4(28, false); // создает вектор из 28 элементов и инициализирует все его элементы false
 ```
Подобно std::array, доступ к элементам массива может выполняться как через оператор [] (который не выполняет проверку диапазона), так и через функцию at() (которая выполняет проверку диапазона):
```cpp
array[7] = 3; // без проверки диапазона 
array.at(8) = 4; // с проверкой диапазона
```
Вектор будет самостоятельно изменять свою длину, чтобы соответствовать количеству предоставленных элементов:
```cpp
array = { 0, 2, 4, 5, 7 }; // хорошо, длина array теперь 5
array = { 11, 9, 5 }; // хорошо, длина array теперь 3
```

### Конструктор на итераторе
У вектора есть удобный конструктор, позволяющий создавать новый вектор на основе среза другого (оформленного через итераторы):
```cpp
std::vector<int> v1 = {0,1,2,3,4,5,6,7};
std::vector<int> v2(v1.begin() + 1, v1.end() - 2); // v2 = {1,2,3,4,6}
```

### Length vs Capacity
У обычного массива, как и std::array нет такого понятия как емкость (вернее это то же самое, что и длинна). Тк std::vector сам следить за кол-во памяти, которая выделяется для его переменных, то ему необходим способ следить за заполненностью. Так **capacity** - это кол-во ячеек динамической памяти выделенной вектору, **length** - это кол-во ячеек заполнено пользователем (те хранят не мусор).
```cpp
std::vector<int> array;
array = { 0, 1, 2, 3, 4, 5 }; // ок, длина array - 6
std::cout << "length: " << array.size() << "  capacity: " << array.capacity() << '\n';	
/* length: 6 capacity: 6 */
array = { 8, 7, 6, 5 }; // ок, длина array теперь 4!
std::cout << "length: " << array.size() << "  capacity: " << array.capacity() << '\n';
/* length: 4 capacity: 6 */
```
- Чтобы изменить свой capacity (те выделить еще или вернуть чать памяти) старый вектор должен неявно себя целиком скопировать в новый вектор. Это трудоемкая оперция, поэтому емкость векторя обычно больше длинны (например, выделяя `std::vector<int> arr = {1,2,3}`, длинна будет 3, а емкость будет 5. Это на случай, если я добавлю еще элементов используя `arr.push_back()`). Кол-во дополнительной памяти, выделенной "прозапас", зависит от компилятора.
- Диапазон для оператора индеса `([])` и функции `at()` основан на длине вектора, а не на его ёмкости.  Например, длина вектора равна 4, а ёмкость — 6. Что произойдёт, если мы попытаемся получить доступ к элементу массива под индексом 5? Ничего, поскольку индекс 5 находится за пределами длины массива. `at()` вренет ошибку, а `([])` - выйдет за пределы вектора.

### Методы
- `array.push_back(object)` - добавляет object в конец. Если нужно, автоматически увеличивает capacity. Важно, что если object не простой тип данных (наприрмер, класса), то действия будут следующими: 1)создается анонимный object использую конструктор, 2)вызывается копирующий конструктор, который копирует этот анонимный объект в ячейку памяти вектора, 3) вызывается деструктор анонимного объекта. В связи с этим, `push_back()` рекомедуется использовать только для простых данных. В случаи классов и тп следует использовать `emplace_back()`.
- `array.emplace_back(object)` - добавляет object в конец. То же самое, что и push_back(), но тут object создается прямо в ячейке вектора, те без создания анонимных объектов.
- `pop_back()` - удаляет последний object из вектора (ничего не возвращает - void).
----
Вектор это контейнер с умной и удобной оберткой. Если есть необходимость получить доступ к самому массиву данных (указатель на него), то нужно воспользоваться методом     
- `array.data()` - возвращает указатель на начало массива данного вектора. Это может понадобиться, например, в этом случаи,
	```cpp
	void SomeFunction(int* array); //какая-то ф-ция, принимающая простой массив
	std::vector<int> vec{1,2,3};
	
	SomeFunction(vec); //ошибка, тк vector не может быть автоматиски преобразован в обычный массив
	SomeFucntion(vec.data()) //ОК! Тк .data() возвращает указатель на массив этого вектора.
----
- `array.at(8) = 4;`  - налогично вызову для `std::array`. Это присвоение с проверкой диапозона. **Лучше использовать []** тк этот опратор быстрее. Более того, `.at()` - использует exaptations, хотя такие выщи как выход за диапозон лушче обрабатывать с помощью assertion.
- `array.size()` - возвращает длинну вектора.
- `array.capacity()` - возвращает емкость вектора.
- `array.reserve(num)` - расширяет вектор до capacity равной num (делает глубое копирование).
- `array.resize()` - изменяет длинну(size) вектора. Однако не меняет capacity, если resize < capacity. Те если resize уменьшает size, то лишние данные будут просто удалены, но capacity останется прежней. Если же происходит расширение, то произойдет глубокое копирование в вектор с больши capacity. Пример использования, 
	```cpp
	std::vector<int> array { 0, 1, 2 };
   	array.resize(7); /* изменяем длину array на 7. Теперь это {0 1 2 0 0 0 0} - автоматически дополнилось нулями */
	
	 std::vector<int> array { 0, 1, 4, 7, 9, 11 };
    	array.resize(4); /* изменяем длину array на 4. Теперь это {0 1 4 7} */
	```

- `array.shrink_to_fit(num)` - изменяет size до num, при этом обрезая capacity до num. Те то же самое, что и resize, только так же подрезает capacity. (экономит место, если точно знаешь размер ветора, но смысла в этом мало). 
- `array.empty()` - проверяет не пуст ли вектор (bool)
- `array.clear()` - очистить вектор
- `vector.assign(n, val)` - assigns new contents to the vector, replacing its current contents (*val*), and modifying its size (*n*) accordingly
- `vector.resize(n) /*or*/ vector.resize(n, val)` - Resizes the container so that it contains *n* elements. If *n* is smaller than the current container size, the content is reduced to its first *n* elements, removing those beyond (and destroying them). If *n* is greater than the current container size, the content is expanded by inserting at the end as many elements as needed to reach a size of n (if *val* is specified, the new elements are initialized as copies of *val*, otherwise, they are value-initialized). If *n* is also greater than the current container capacity, an automatic reallocation of the allocated storage space takes place.

### Методы на основе итераторов
- `v.erase(iter 1, iter 2)` - стирает элементы [iter 1, iter2), где iter - это итераторы, указывающие на элементы. Если итератор 1, то стирается только один элемент. Если их 2, то стираются все элементы между ними (не включая iter 2). При стирании, смещает последующие элементы на место только что стертых. Метод возвращает итератор, указывающий на певный из только что смещенных элементов.
- `v1.emplace(iterV1 place, iterV2 begin, iterV2 end)` - Метод вставляющий срез одного вектора в другой в указанном итератором месте. При вставке, создается анонимный объект, который вставляется через конструктор копирования. Те действует аналоничено `push_back()`.
	```cpp
	std::vector<int> v1 = {0,1,2,3,4,5,6,7};
	std::vector<int> v2 = {69,96};
	v1.insert(v1.begin() + 3, v2.begin(), v2.end()); // теперь v1 = {1,2,3,69,96,45,6,7}
	```
- `v1.emplace(iterV1 place, iterV2 begin, iterV2 end)` - метод аналогичный методу insert, однако тут объекты конструируются in-place. Те действует аналоничено `emplace_back()`

### Методы делающие stack из std::vector
Если использовать только эти три метода, то из вектора получится стэк (структура данных): 
- `array.back()` - возвращает последний элемент вектора
- `array.push_back(n)` - присоединит n в конец вектора (append)
- `array.pop_back(n)` - удаляет последний элемент вектора

### Обрезка вектора
Также смотри про обрезку векторов [тут](https://github.com/PlohoyParen/Cpp_doc/blob/master/Documents/Chapter15_Inheritance.md#%D0%BE%D0%B1%D1%80%D0%B5%D0%B7%D0%BA%D0%B0-%D0%BE%D0%B1%D1%8A%D0%B5%D0%BA%D1%82%D0%BE%D0%B2-object-slicing). Вектор ограничен объектами одного класса, поэтому сложно реализовывать полиморфизм через вектор. Например, мы хотим сделать вектор на основе базового класса и вписывать туда наследников. Хотя это сработате для обычного array, но с вектором так не выйдет. В вектор будет записывать обрез дочернего класса (те копия родительской части), поэтому нельзя будет вызвать виртуальные ф-ции на объекты в этом векторе. Например:
```cpp
int main()
{
	std::vector<Parent> v;
	v.push_back(Parent(7)); // добавляем объект класса Parent в наш вектор
	v.push_back(Child(8)); // добавляем объект класса Child в наш вектор
 
        // Выводим все элементы нашего вектора
	for (int count = 0; count < v.size(); ++count)
		std::cout << "I am a " << v[count].getName() << " with value " << v[count].getValue() << "\n";
 
	return 0;
}

/*** Выведет 
I am a Parent with value 7
I am a Parent with value 8 // нельзя вызвать виртуальную ф-цию 
***/
```
#### Решение 1
Можно решить эту проблему записывая в вектор указатели на классы, и реализовывать всю работу через указатели (хотя это и неудобно):
```cpp
int main()
{
	std::vector<Parent*> v;
	v.push_back(new Parent(7)); // добавляем объект класса Parent в наш вектор
	v.push_back(new Child(8)); // добавляем объект класса Child в наш вектор
 
        // Выводим все элементы нашего вектора
	for (int count = 0; count < v.size(); ++count)
		std::cout << "I am a " << v[count]->getName() << " with value " << v[count]->getValue() << "\n";
 
	for (int count = 0; count < v.size(); ++count)
		delete v[count];
 
	return 0;
}
```
#### Решение 2
Использовать специальный класс-обертку ` std::reference_wrapper` (`#include <functional>`). Этот класс работает как ссылка, но позволяет выполнять операции присваивания. 
```cpp
#include <functional> // для std::reference_wrapper
 
int main()
{
	std::vector<std::reference_wrapper<Parent> > v;
	Parent p(7); // p и ch не могут быть анонимными объектами
	Child ch(8);
	v.push_back(p); // добавляем объект класса Parent в наш вектор
	v.push_back(ch); // добавляем объект класса Child в наш вектор
 
	// Выводим все элементы нашего вектора
	for (int count = 0; count < v.size(); ++count)
		std::cout << "I am a " << v[count].get().getName() << " with value " << v[count].get().getValue() << "\n"; // используем .get() для получения элементов из std::reference_wrapper
 
 
	return 0;
}
```


 ## map и unordered_map
 ```cpp
 #include <map>
 map<string, int> name_to_value; //<key_type: value_type>
 name_to_value["one"] = 1;
 name_to_value["two"] = 2;   
 cout << "two is " << name_to_value["two"];
 
 // используется список инициализаторов для инициализации массива
 map<string, int> name_to_value2 = {{"one",1}, {"two",2}, {"Three",3}};
```
Вызвов ключей(`.first`) и значений(`.second`):
```cpp
map<string, int> abc = {{'a':1}, {'b':2}, {'c':3}}
for (auto i: abc) {                         // тут i - это пара key: values
    cout << i.first;                        // вывод key (т.е. 'a')
    cout << i.second;}                      // вывов values (т.е. 1)
```
Если вызвать словарь с несуществующим ключом, то автоматически будет создана пара {key:default_value} (напр, для int default_value = 0):
```cpp
map<char, int> dict;	// пустой словать 
dict['p'];		// ключа 'p'нет, поэтому словать создаст пару {'p':0} (т.к. по дефолту int это 0)
			// теперь словать имеет длинну 1 (одну пару)
```

### unordered_map
То же самое, что и `map`, но в нем данные хранятся неотсортированными (и другая внутренняя имплементация). На самом деле тесты показывают, что `unordered_map` значительно быстрее практически во всем: insert, look-up/fetch, remove (подробнее [тут](https://stackoverflow.com/questions/2196995/is-there-any-advantage-of-using-map-over-unordered-map-in-case-of-trivial-keys).     
Пример кэша для рекурсивного поиска чисел Фибоначи на основе unordered_map:
```cpp
 static long long fibonachi(int n) {
        assert(n>=0);			
        if (n==0 || n==1) {				 // 0,1 первые числа последовательности
            return n;
        }
        static std::unordered_map<int, long long> cache; // объявлне единожды, тк static	
        if (cache.find(n)==cache.end()) {		 // итератор дошел до конца и ничего не нашел
            cache[n] = fibonachi(n-2)+fibonachi(n-1);	 // тогда добавим элемент в кэш
        }
        return cache.at(n);				 // вернет ссылку на n, с проверкой наличия n
```


### Методы (для map и unordered_map)
- `map.at(n);` - проверяет, существует ли элемент n, если да, то возвращает ссылку на этот элемент, которой может быть присвоено значение или ее может возвращать ф-ция. Если нет, то вернет исключение `std::out_of_range`.
- `map.size()` - размер словаря (те кло-во пар ключ:значение)
- `map.erase(key)` - удалит пару, соотвутсвующую key
- `map.count(key)` - возвращает сколько раз key встречается в словаре. Тк все key в нем уникальны, то возвращет 1 (если есть) и 0 (если нет)
- `it = map.find(name);` - returns an iterator to `it` if found, otherwise it returns an iterator to `map::end` (см продробнее про итераторы).
- `map.begin()`, `map.end()` - возвращает итераторы к началу и концу map.
    
#### В С++ 17 
Появилась возможность удобнее итерировать по словарю использую нотацию `[key, value]`:
```cpp
 map<string, int> name_to_value = {{"one",1}, {"two",2}, {"Three",3}};
 for (const auto& [key, value] : name_to_value)
 {/* прямо использовать key и value (вместо name_to_value.first, name_to_value.second) */ }
```

### Снипет для подсчета повторений
Тк какждый вызов вида `map[key]` возвращает `value` (если такой ключ есть) или создает и возращает 0 (если такого ключа нет), то можно создать кэш на этой основе. 
```cpp
std::map<char, int> letter_count;
for (auto& letter : str)
{
	++letter_count[letter]; /* если букву встретили 1й раз, то создается пара key:value (автоматически value = 0).
	Если буква уже была, то делаем letter_count[letter] = letter_count[letter] + 1. Таким образом получим словать,
	где key - уникальные быквы в слове (string), а value - кол-во совпадений (отсчет идет от 0, те value =0 -> одно
	совпадение, value = 2 -> 3 совпадения и тд. */
}
return letter_count;
``` 

## Set
Множество (set) - контейнер, который ведет себе так, как ты и ожидал бы от множества. Он содержит:
- Одного типа 
- Без повторов
- Отсортированными

```cpp
#include <set>	
set<string> example_set;			// объявление
set<string> month_names =
{"January", "March", "February", "March"}; 	// инициализация
```

### Методы
- `example_set.insert("Vasya")` - добавляет элемент
- `example_set.erase("Vasya")` - удаляет элемент 
- `example_set.size()` - размер множества
- `example_set.count("Vasya")` - проверяет наличие элемента, возвращает 0 или 1
- `example_set.empty()` - проверяет не пусто ли множество
- `example_set.clear()` – removes all the elements from the set.

- `example_set.begin()` - returns an iterator to the first element in the set.
- `example_set.end()` - returns an iterator to the theoretical element that follows last element in the set.
[more methods](https://www.geeksforgeeks.org/set-count-function-in-c-stl/) 

### Создание vector по set (и наоборот)
Чтобы создать множество по вектору, не обязательно писать цикл. Реализовать это можно следующим образом:
```cpp
vector<string> v = {"a", "b", "a"};
set<string> s(begin(v), end(v));
```
Аналогично можно создать set по vector.

### Оперции над множествами
#### Сравнение 
Оператор сравнения прописан для set, поэтому сравнение происходит напрамую. Сравнение возвращает булевый 0 или 1:
```cpp
set<string> month_names = {"January", "March", "February", "March"};
set<string> other_month_names = {"March", "January", "February"};
cout << (month_names == other_month_names) << endl; 	// вернет 1 (true)
```
#### Разница
Прямой перегрузки разницы у set нет. Однако, ее можно найти используя ф-цию `std::set_difference` из библиотеку <algorithms>. Эта ф-ция может быть использована и для других контейнеров (см подробнее в главе про библиотеку <algorithms>).
```cpp
#include <algorithm>
#include <set>
#include <iterator>
// ...
std::set<int> s1, s2;
// Fill in s1 and s2 with values
std::set<int> result;
std::set_difference(s1.begin(), s1.end(), s2.begin(), s2.end(),
    std::inserter(result, result.end()));	
/* In the end, the set result will contain the s1-s2. */
```

## std::initializer_list
Это специальный тип данных, который нужен, чтобы переопределить инициализацию списком для своего класса. 
```cpp
int one_array[7] { 7, 6, 5, 4, 3, 2, 1 }; 	//uniform инициализация через список инициализации
int two_array[3] = { 1, 2, 3 };			//копирующая инициализация через список инициализации
```
Пусть у нас есть свой класс для int массива.     
```cpp
class ArrayInt
{
private:
	int m_length;
	int *m_data;
 
public:
	ArrayInt() :
		m_length(0), m_data(nullptr)
	{
	}
 
	ArrayInt(int length) :
		m_length(length)
	{
		m_data = new int[length];
	}
 }
```
Для использования `std::initializer_list` требуется подключить библиотеку `<initializer_list>`.    
1. Мы создаем специальный конструктор, который будет обрабатывать данные переданные через список инициализации:
	```cpp
	ArrayInt(const std::initializer_list<int> &list): // позволяем инициализацию ArrayInt через список инициализации
			ArrayInt(list.size()) 	/* используем концепцию делегирования конструкторов для создания 
						начального массива, в который будет выполняться копирование элементов */
		{
			// Инициализация нашего начального массива значениями из списка инициализации
			int count = 0;
			for (auto &element : list)
			{
				m_data[count] = element;
				++count;
			}
		}
	```
	- `list.size()` - метод, который указывает на количество элементов списка.
	- Хотя мы знаем размер нашего списка, по какой-то технической причине его реализации, он не поддерживает оператор [], этому нельзя использовать обычный цикл for и вызывать члены списка по-членно (например, list[i]). Поэтому мы используем range based for и счетчик counter.

2. Обязательно надо переопределить оператор присваивания = или запретить его. Также при переопределении = надо позаботиться о глубоком копировании. Например мы хотим использовать копирующую инициализацию:
```cpp
int ArrayInt[3] = { 1, 2, 3 };
```
В этом случаи анонимный объект будет создан, используя наш новый конструктор использующий std::initializer_list. Он у нас нет переопределения =, поэтому компилятор почленно скопирует все и потеряет указатель и память, и все будет плохо.
