# NOTE
Я остановился на:
- [Ravesli](https://ravesli.com/uroki-cpp/) на Главе 14: исключения. (альтернативно: [Eng](https://www.learncpp.com/))
- [Chili C++, intermediate](https://www.youtube.com/watch?v=kOsnq5JJvaw&list=PLqCJpWy5Fohfil0gvjzgdV4h29R9kDKtZ): Tutorial 19: dynamic_cast / RTTI typeid

# Ouline
1. [**Chapter 1: General**](./Chapters/Chapter1_General.md)     
	- Важные понятия и термины
	- General structure  
		- Вид типичного ситаксиса   
		- Общий цикл написания программы
	- Preprocessor -> compiler -> linker -> executable
		- Этапы преобразования .cpp -> .exe
		- Facts about compilation
		- Компиляция из терминала
	- Сегментация памяти в C++
		- Memory segments
			- Code (text) segment
			- Static segment
			- Stack segment
				- Последовательность заполнения стека
				- frame pointer он же base pointer он же ebp
	- Проблемы неоднозначности кода
	- Связи между объектами в C++
	- Раннее и позднее связывание ( Early binding and late binding )
		

2. [**Chapter 2: Operators**](./Chapters/Chapter2_Operators.md)     
	- Assiment and initialisation
	- Виды инициализации: прямая, uniform, копирующая
	- Оператор запятая (,)    
	- Тернарный if     
	- && (и) и || (или) - ленивые
	- Math    
		- Общее 
		- cmath
		- random number generator 
		
3. [**Chapter 3: Streams**](./Chapters/Chapter3_Streams.md)   
	- std::cin
		- обработка некорректного ввода
		- getline(cin, str)
		- get(char)
		- ignore(num, char)
	- std::cout
		- library <iomanip>
		- форматирование вывода
		- распадение в указатель при выводе
		- How std::cout evaluates arguments
	- files
		- Работа с файлами, как с потоком (Formatted input)
		- Работа с файлом, как последовательностью символов char (Unformatted input)
			- Методы
		- State functions
		- binary mode
	- stringstream

4. [**Chapter 4: Memory**](./Chapters/Chapter4_Memory.md)
	- Pointers
		- Указатель * и &
		- Размер
		- Scaling (Арифметика указателей)
		- null pointer
		- const pointers and pointers to const
		- Оператор доступа к членам класса через указатель (->)
		- Указатель типа void
		- Указатель на указатель
	- References
		- Общее: как и зачем?
		- Константные ссылки
	- Dynamic memory allocation (оператор new)
		- Статическое, автоматическое (стек) и динамическое выделение памяти (куча)
		- Когда следует выделять память в куче (динамически)
		- Placement new
		- исключение bad_alloc
		- Memory leak
		
			

5. [**Chapter 5: Data types**](./Chapters/Chapter5_Data_types.md)
	- Basic data types
		- Встроенные типы: размеры
		- char
		- Фиксированный размер int
		- float/double: ошибки округления
		- nan и inf
		- constants
		- auto
	- Анонимные переменные
	- Преобразование типов
		- Неявное преобразование данных
		- Явное преобразование данных
			- С style cast
			- C++ style cast
	- Regular arrays(фиксированные массивы)
		- объявление с enum 
		- Указатели и массивы
		- Память и индексация (array[i])
		- n-dimensional array
	- Dynamic arrays(Динамические массивы)
		- Выделение C-style
		- Выделение C++ style
		- многомерный динамический массив
	- C-style strings
		-C-style string symbolic constants
	- Enumeration
		- Обычный перечислитель
			- Scope, input/output, память
		- enum classes
		- Зачем это надо?
		- Short enum
	- Helpful functions
	
6.  [**Chapter 6: Classes**](./Chapters/Chapter6_Classes.md)
	 - Общее
	 	- Initialization
	 - Struct (public class)
	 	- Присвоение значений полям
		- Вложенные структуры
		- Размер
	 - Class
	 	- Анатомия класса
		- Объявление методов вне класса
		- Конструктор
			- Implicitly generated (or default) constructor
			- Default constructors
			- Список инициализации членов класса
			- Инициализация константы
			- Инициализация массива
			- Инициализация всего подряд
			- Инициализация по умолчанию
			- Делегирующие конструкторы
		- Порядок действий в конструкторе
		- Вызов конструктора при определении класса (.h файл)
		- Копирующий конструктор
			- Определение конструктора копирования
			- Пример копирующего конструктора
			- Элизия 
			- Перегрузка оператора присваивания =
			- Как задать конструктор копирования через оператор присваивания
			- Правило 3х и правило 0
		- Неявное преобразования при вызове конструктора (conversion constructor)
			- Как запретить компилятору неявно преобразовывать аргументы: explicit, delete
		- Вложенные enum
		- Вложенные классы
		- Деструктор
			- Идиома RAII 
			- Порядок действий в деструкторе (инь и янь)
			- Implicitly generated (or default) destructor
		- Скрытый указатель *this ->
		- Const методы
			- mutable
			- Константные ссылки и классы
		- Static
			- Static переменные класса
			- Static методы
	- Special member functions (implicitly generated functions)
	- friend (дружественные класса и ф-ции)
		- Дружественные функции и несколько классов
		- Дружественные классы
		- Does "friend" violate encapsulation?
	- void * или история про злого близница
	- Circular dependency (and forward declaration)

	
7. [**Chapter 7: Control Flow**](./Chapters/Chapter7_Flow_Control.md)
	- Branches (Ветвление)
		- is else
		- Switch
	- Halts and jumps
	 	- goto
		- break и continue
 	- loops
		- for
		- while, while do

8. [**Chapter 8: Functions**](./Chapters/Chapter8_Functions.md)
	- Термины 
	- Общее, main, void
		-Передача в main() аргументов из командной строки
			- Командная строка
	- Прототип функции
	- Аргументы
		- по значению
		- по ссылке (const и ф-ция как аргумент другой ф-ции)
		- по адресу (указателю)
		- передача массива в качестве аргумента
		- read only (const) pass
		- Страшная правда: все параметры-указатели
		- Default arguments
	- Возвращаемое значение
		- по значению 
		- по адресу (указателю)
		- по ссылке
	- inline (встроенные ф-ции)
	- Function overloading (перегрузка ф-ций)
		- Порядок сравнения параметров
	- Адрес ф-ции и указатель на нее
		- Инициализация и присвоение ф-ции указателем
		- Передача ф-ции в качестве параметра ф-ции
		- Псевдонимы для указателей на ф-ции
		- std::function
	- Эллипсис (ellipsis) аргумент

9. [**Chapter 9: Files and project magement**](./Chapters/Chapter9_Files_and_project_manegment.md)
	- Добавление файла в проект
	- header file (.h)
		- Директивы препроцессора
		- header guards
	- Рефакторинг классов
		- Область принадлежности (::)
	
10. [**Chapter 10: Scopes**](./Chapters/Chapter10_Scopes.md)
	- Scopes (Зоны видимости)
		- Локальные переменные, глобальные переменные (static, extern) 
		- сокрытие имен, глобальные ф-ции
	- namespace
		- Глобальное пространство имен 
		- :: и using
		- Псевдонимы (вложение)
11. [**Chapter 11: Algorithm lib**](./Chapters/Chapter11_Algorithm_lib.md)
	- [A few things algorithm relies on](./Chapters/Chapter11_Algorithm_lib.md#a-few-things-algorithm-relies-on)
		- 1. Comparasing functors
			- functional lib
			- Свой функтор сравнения
		- 2. Predicates
		- 3. lambda functions
	- [1. Sequence operators](./Chapters/Chapter11_Algorithm_lib.md#1-sequence-operators)
		- sort и stable_sort
		- remove and remove_if
		- copy and copy_if
		- for_each
		- swap
		- set_difference
	- [2. Mathematics](./Chapters/Chapter11_Algorithm_lib.md#2-mathematics)
		- count
		- accoumulate

12. [**Chapter 12: std data types**](./Chapters/Chapter12_std_data_types.md)
	- strings
	- std::array
	- vector
		- Length vs Capacity
		- Методы
		- Методы делающие stack из std::vector
	- map и unordered_map
		- unordered_map
		- методы
		- в С++ 17
		- Снипет для подсчета повторений
	- set
		- методы
		- vector по set (и наоборот)
		- Оперции над множествами (сравнение и разница)
	- std::initializer_list
		
13.  [**Chapter 13: Exception handling**](./Chapters/Chapter13_Exception_handling.md)    
	- exit() и cerr   
	- assert и static_assert
		- static_assert
		- Debuger vs release (NDEBUG)
		
14. [**Chapter 14: Operator overloading**](./Chapters/Chapter14_Operator_overload.md)
	- Перегразка через обычные ф-ции
		- Перегрузка через дружественные ф-ции
		- Когда лучше использовать
	- Перегрузка через методы класса
		- Перегрузка симетричных операторов через методы класса
		- Когда лучше использовать
		- Создание цепочек операторов
		- Перегрузка унарных операторов
	- Перегрузка операторов сравнения
	- Перегрузка инкремента и декремента
	- Перегрузка операторов ввода и вывода
	- Перегрузка оператора индекса []
		- Указатели на объекты и перегруженный оператор []
		- Три совета
		- Упоротрая перегрузка [][]
	- Перегрузка оператора ()
		- Функторы
		- Где использвоать
	- Перегрузка операций преобразования типа
	- Перегрузка оператора ->
	- Перегрузка оператора присваивания =
		- Присваивание по-умолчанию  
		- Проблемы при работе с памятью  
		- Самоприсваивание, возврат *this
		- delete
		
15. [**Chapter 15: Inheritance**](./Chapters/Chapter15_Inheritance.md)
	- Общее
	- Порядок построения дочерних классов
		- Инициализация членов родительского класса при наследовании
		- Цепочка наследований
	- Приведение (преобразование) классов
	- Спецификаторы наследования
		- Спецификаторы доступа
		- Типы наследований
			- public 
			- private
			- protected
			- Тип наследования по дефолту (без явного указания)
			- Пример цепочки
	- Переопределение методов родительского класса 
		- Изменение спецификатора доступа при переопределении метода (и сокрытие метода)
	- Множественное наследование
		- Diamond of doom
		- Виртуальный базовый класс
			- Особенности 
	- Обрезка объектов (object slicing)
	- dynamic_cast и static_cast
		- dynamic vs static

16. [**Chapter 16: Virtual functions**](./Chapters/Chapter16_Virtual_functions.md)
	- Перегрузка (overload) vs переопределение (override)
	- Полиморфизм (и постановка задачи)
		- Пример с ф-цией
		- Пример с массивом
	- Особенности использования виртуальных ф-ций
		- Пример цепочки наследования
		- Корректная перегрузка виртульных ф-ций
			- Ковариантный тип возврата
		- Порядок указания virtual
		- Виртуальные ф-ции в конструкторах и деструкторах
		- Когда НЕ использовать virtual
	- Final and override
		- override
		- final
			- для ф-цйи и для класса
	- Виртуальный деструктор
	- Виртуальные табилцы (virtual tables)
		- 1. Виртуальные таблицы (VMT)
		- 2. Скрытый указатель на виртуальную таблицу (vpointer)
		- Виртуальные ф-ции медленнее обычных
	- Абстрактные классы и Интерфейсы
		- Абстрактные функции и абстрактные классы
		- Интерфейсы
		- Чистые виртуальные функции и виртуальная таблица
	- Перегрузка потоков ввода и вывода
	
17. [**Chapter 17: Templates**](./Chapters/Chapter17_Templates.md)
	- [Как это решалось в C (спойлер: макросы)](./Chapters/Chapter17_Templates.md#%D0%BA%D0%B0%D0%BA-%D1%8D%D1%82%D0%BE-%D1%80%D0%B5%D1%88%D0%B0%D0%BB%D0%BE%D1%81%D1%8C-%D0%B2-c-%D1%81%D0%BF%D0%BE%D0%B9%D0%BB%D0%B5%D1%80-%D0%BC%D0%B0%D0%BA%D1%80%D0%BE%D1%81%D1%8B)
	- [Шаблоны ф-ций](./Chapters/Chapter17_Templates.md#%D1%88%D0%B0%D0%B1%D0%BB%D0%BE%D0%BD%D1%8B-%D1%84-%D1%86%D0%B8%D0%B9)
		- Как компилятор реализует шаблоны ф-ций
		- Явная специализация шаблона ф-ции
		- Вывод аргументов (deduce)
		- Перегрузка щаблонов
		- Явная специализация VS перегрузка шаблона
		- Шаблоны методов (в основном для больщой 4ки)
	- [Шаблоны классов](./Chapters/Chapter17_Templates.md#%D1%88%D0%B0%D0%B1%D0%BB%D0%BE%D0%BD%D1%8B-%D0%BA%D0%BB%D0%B0%D1%81%D1%81%D0%BE%D0%B2)
		- Заголовочные файлы
		- Явная специализация шаблона класса
			- Полная специализация
			- Частичная специализация 
				- Использование родительского класса для частичной специализации метода класса
		- typedef мета
	- [none-type параметр](./Chapters/Chapter17_Templates.md#none-type-%D0%BF%D0%B0%D1%80%D0%B0%D0%BC%D0%B5%D1%82%D1%80)   
		- Еще пример использования none-type параметра 
	- [template и функторы - союз созданный на небесах](./Chapters/Chapter17_Templates.md#template-%D0%B8-%D1%84%D1%83%D0%BD%D0%BA%D1%82%D0%BE%D1%80%D1%8B---%D1%81%D0%BE%D1%8E%D0%B7-%D1%81%D0%BE%D0%B7%D0%B4%D0%B0%D0%BD%D0%BD%D1%8B%D0%B9-%D0%BD%D0%B0-%D0%BD%D0%B5%D0%B1%D0%B5%D1%81%D0%B0%D1%85)
	- [Несколько фич связанныз с классами](./Chapters/Chapter17_Templates.md#%D0%BD%D0%B5%D1%81%D0%BA%D0%BE%D0%BB%D1%8C%D0%BA%D0%BE-%D1%84%D0%B8%D1%87-%D1%81%D0%B2%D1%8F%D0%B7%D0%B0%D0%BD%D0%BD%D1%8B%D0%B7-%D1%81-%D0%BA%D0%BB%D0%B0%D1%81%D1%81%D0%B0%D0%BC%D0%B8)
		- Использование зависимых имён
		- Использование функций для вывода параметров

18. [**Chapter 18: Iterators**](./Chapters/Chapter18_Iterators.md)
	- [Основы синтаксиса](./Chapters/Chapter18_Iterators.md#%D0%BE%D1%81%D0%BD%D0%BE%D0%B2%D1%8B-%D1%81%D0%B8%D0%BD%D1%82%D0%B0%D0%BA%D1%81%D0%B8%D1%81%D0%B0)
		- Iterator invalidation
	- [Инкриментирование в итераторах](./Chapters/Chapter18_Iterators.md#%D0%B8%D0%BD%D0%BA%D1%80%D0%B8%D0%BC%D0%B5%D0%BD%D1%82%D0%B8%D1%80%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5-%D0%B2-%D0%B8%D1%82%D0%B5%D1%80%D0%B0%D1%82%D0%BE%D1%80%D0%B0%D1%85)
	- [Итераторы бывают разные](./Chapters/Chapter18_Iterators.md#%D0%B8%D1%82%D0%B5%D1%80%D0%B0%D1%82%D0%BE%D1%80%D1%8B-%D0%B1%D1%8B%D0%B2%D0%B0%D1%8E%D1%82-%D1%80%D0%B0%D0%B7%D0%BD%D1%8B%D0%B5)
	- [Почему вся STL построена на итераторах?](./Chapters/Chapter18_Iterators.md#%D0%BF%D0%BE%D1%87%D0%B5%D0%BC%D1%83-%D0%B2%D1%81%D1%8F-stl-%D0%BF%D0%BE%D1%81%D1%82%D1%80%D0%BE%D0%B5%D0%BD%D0%B0-%D0%BD%D0%B0-%D0%B8%D1%82%D0%B5%D1%80%D0%B0%D1%82%D0%BE%D1%80%D0%B0%D1%85)
	- [Const iterator](./Chapters/Chapter18_Iterators.md#const-iterator)
	- [Reverse interator](./Chapters/Chapter18_Iterators.md#reverse-interator)
	- [Библиотека <iterator>](./Chapters/Chapter18_Iterators.md#%D0%B1%D0%B8%D0%B1%D0%BB%D0%B8%D0%BE%D1%82%D0%B5%D0%BA%D0%B0-iterator)
		- std::begin() и std::end()
		- std::advace()
		- Iterator adaptors
			- std::back_insert_iterator и std::back_inserter
		- Stream iterators
			- std::istream_iterator и std::ostream_iterator
	- [Базовый итератор для кастомного класса](./Chapters/Chapter18_Iterators.md#%D0%B1%D0%B0%D0%B7%D0%BE%D0%B2%D1%8B%D0%B9-%D0%B8%D1%82%D0%B5%D1%80%D0%B0%D1%82%D0%BE%D1%80-%D0%B4%D0%BB%D1%8F-%D0%BA%D0%B0%D1%81%D1%82%D0%BE%D0%BC%D0%BD%D0%BE%D0%B3%D0%BE-%D0%BA%D0%BB%D0%B0%D1%81%D1%81%D0%B0)
	
19. [**Chapter 19: Error handling**](./Chapters/Chapter19_Error_handling.md) 	
	
49. [**Chapter X: Useful STD Libraries**](./Chapters/Chapter_X_Useful_%20STD_Libraries.md)
	- chrono
	- random (и cstdlib)

50. [**Chapter X: Other**](./Chapters/ChapterX_Other.md)
	- Использование слова static
		- Global scope
		- Class 
