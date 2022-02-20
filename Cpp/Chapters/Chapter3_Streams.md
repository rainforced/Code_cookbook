# Chapter 3 Streams 

## std::cin
`cin >> ..` - поток ввода. Из буфера в переменую сохраняется только соответсвующая переменной часть (т.е. для int - число до пробела, для char - один символ и тд). Остальная часть пользовательского ввода останется во входном буфере, который использует cin и будет доступна для использования последующим вызовам cin (`cin << a << b << c;` a, b, c - три раза вызов cin).

### Обработка некорректного ввода
При некорректном вводе (переполнение или неправельный тип принимающией переменной), надо очистить буфер:
```cpp
// при некорректном вводе, отчистит буфер
if (std::cin.fail()) 		 // если предыдущее извлечение не выполнилось или произошло переполнение,
{
    std::cin.clear(); 		 // то возвращаем cin в 'обычный' режим работы
    std::cin.ignore(32767,'\n'); // и удаляем значения предыдущего ввода из входного буфера
}
else 
    return a;			 // В случаи, что проблем нет возращаем введенные данные
```
Можно сделать `while(true){}`, где принимать `std::cin >> a;`, а затем проводить тест используя кусок кода выше. Тогда, если все ОК то данные пройдут, если не ОК, то буфер отчистится и пользователь введет данные еще раз.    

----

Если `std::cin` ожидает переменную определенного формата, но из потока приходит другая, то поток переходит в состояние fail и `std::cin.fail()` возвращает True.
```cpp
#include<iostream>
#include<limits>
using namespace std;

int main()
{
int a;

cout<<"Enter an integer number\n";
cin>>a;
while(1)
{
	if(cin.fail())
	{
		cin.clear();
		cin.ignore(numeric_limits<streamsize>::max(),'\n');
		cout<<"You have entered wrong input"<<endl;
		cin>>a;
	}
	if(!cin.fail())
		break;
}

cout<<"the number is: "<<a<<endl;
return 0;
}
```
`cin.fail()` - This function returns true when an input failure occurs. In this case it would be an input that is not an integer. If the cin fails then the input buffer is kept in an error state. `cin.clear()` - This is used to clear the error state of the buffer so that further processing of input can take place. This ensures that the input does not lead to an infinite loop of error message display. `cin.ignore()` - This function is used to ignore the rest of the line after the first instance of error that has occurred and it skips to or moves to the next line.     

Тут однако может возникнуть проблема в том, что если пользователь введет "1234ads23", то в переменную запишется "1234", потом поток зафейлится, но число останется записанной в переменную. Соответсенно `cout<<"the number is: "<<a<<endl;` выведет "1234". Простого решиния тут нет. Чтобы избежать такого косяка, нужно принимать все как строки и использовать regular expressions, которые по дефолту не поддерживаются в C++ (только с помощью сторонних библиотек). 

### get(char)
Метод `cin.get()` принимает значения по-символьно.  `char c; cin.get(c);` - сохраняет полученный символ в переменную с. Если ввод закончен (и соотв. сin.get ничего не получает), то она возвращает `false`(поэтому ее удобно использовать в while и if). Также
`cin.get()` (без указания символьной переменной) - принимает любое значение и никуда его не сохраянет (т.к. мы не передали ей переменную для этого), обычно так ее ставят в конце программы, чтобы терминал не закрылся сразу после завершения основного цикла программы. 
```cpp
// снипет посимвольного ввода 
char c = '\0';  // инициализируем "пустым" символом (можно и ничем не инициализировать
while (cin.get(c))
{ // на каждой итерации считываем один символ в переменную c
    /* здесь можно пользоваться значением прочитанным в переменную c */
    if (c != 'a')
        cout << c; // выводим символ, если он не равен 'a'
}
```

### ignore(num, char)
Метод `cin.ignor(1000, '\n')` будет игнорировать содержимое буфера следующии 1000 (int) символов или пока не встретит сивол '\n' (char). Нужен, чтобы отчистить буфер от ненужны символов. Например, мы ждем что пользователь введт int, а он ввел "420 blaze!" и нажал enter (те в потоке теперь лежит "420 blaze!\n"),
```cpp
int num;
std::cout << "Enter your num" << std::endl;
std::cin << num;    //  "420 blaze!" -> num = 420; остальное дальше висит в буфере
std::ignore(1000, '\n');    //отчистит буфер от " blaze!"
```

Вместо magic number для лимита, можно использовать максимально возможное число, используя библиотеку <limits>
```cpp
#include <limits>
std::cin.ignore(std::numeric_limits<std::streamsize>::max(), '\n');
```

## getline(input_stream, str)
The getline() function extracts characters from the input stream and appends it to the string object until the delimiting character is encountered. While doing so the previously stored value in the string object str will be replaced by the input string if any.        

На самом деле stream может быть любой input stream объект, например `cin`, file (`ifsteam`), `stringstream` и тд:      
- Пример с `cin`:
    ```cpp
    string name; 
    cout << "Please enter your name: \n"; 
    getline(cin, name); 
    ```
- Пример с `ifstream`
    ```cpp
    std::fstream in( filename );
    for( std::string line; std::getline( in,line ); )
    {
        if( line == "[Tile Size]" )
        {
            in >> tileSize;			
        }
    }
    ```

## std::cout
`cout << endl;` - после вывода переведет на новую строку  

### library iomanip
- **Precision**:  `std::setprecision(number);` 	
    ```cpp
    #include <iostream>
    #include <iomanip>					 // для std::setprecision()
    std::cout << std::setprecision(16); 			 // задаём точность в 16 цифр
    float f = 3.33333333333333333333333333333333333333f;     // выдаст 3.333333253860474
    std::cout << f << std::endl;
    double d = 3.3333333333333333333333333333333333333;	// выдаст 3.333333333333333
    std::cout << d << std::endl;
    ```
    Точность по типам данных: 
    -float: от 6 до 9 цифр (в основном 7);
    -double: от 15 до 18 цифр (в основном 16);
    -long double: 15, 18 или 33 цифры;      
    (Точность типа данных - *лимитирующий фактор*: округляется все по типу данных, а не точности вывода. Так, если float имеет 7 цифр, то cout до 9 цифр выведет первые 7 правильно, а потом фигню)
- **Set width**: `std::setw(int width)` - выдает в поток буфер рамера width. Например,
    ```cpp
    std::cout << std::setw(7) << 12 << std::setw(7) << 3 << std::setw(7) << 200 << std::endl;
    std::cout << std::setw(7) << 1 << std::setw(7) << 2223 << std::setw(7) << 2 << std::endl;
    std::cout << std::setw(7) << 1222 << std::setw(7) << 23 << std::setw(7) << 2000 << std::endl;
    ```
    Выведет, (пробелы заданы буфером) 
    ```
    12      3       200
    1       2223    2
    1222    23      200
    ```

**bool**
`std::cout << std::boolalpha; // выводит логические значения как "true" или "false"`

**std::endl**
Конец и перевод строки. Прежде чем закончить строку проверяет выведены ли все данные из буфера вывода, в отличии от `\n`, который просто переводит строку (не проверяе ничего).

### std::cout перобразование
При передаче указателя *не* типа `char`, в результате выводится просто содержимое этого указателя (адрес памяти). Однако, если вы передадите объект типа `char*` или `const char*`, то `std::cout` предположит, что вы намереваетесь вывести строку. Следовательно, вместо вывода значения указателя — выведется строка, на которую тот указывает:
```cpp
int nArray[5] = { 9, 7, 5, 3, 1 };
char cArray[] = "Hello!";
const char *name = "John";
 
std::cout << nArray << '\n'; 	// nArray распадается в указатель типа int -> 0046FAE8 (адрес)
std::cout << cArray << '\n'; 	/* cArray распадается в указатель типа char -> Hello! (распадается, а потом
автоматически преобразует указатель назад в строку */
std::cout << name << '\n'; 	// name уже и так является указателем типа char -> John
```
В связи с этим сложно вывести адрес переменой типа char:
```cpp
char a = 'R';
std::cout << &a;	// a распадется в указатель, а потом все равно автоматически преобразуется назад.
/* выведет: R╠╠╠╠╜╡4;¿■A (т.е. литерал R, а потом мусор следующий за нем в памяти до ближайшего 0 */
```

### How std::cout evaluates arguments
Если в поток std::cout отправить несколько последовательных выражений, то компилятор обязуется отправить их в поток в правильном порядке. Однако он не обязуется выполнять эти выражения в правильной последовательсти. Что имется ввиду? Например:
```cpp
std::vector<int> v = {1,2,3,4,5,6};
auto it = v.begin(); //итератор указывающий на 1й элемент
std::cout << *it << ", " << *(++it);
/* мы ожидаем: 1, 2 
а получим: 2, 2 */
```
Что тут произошло? 1. Компилятор не обещает, что выполнит операции слево направо, как они написаны (те сначала `*(it)`, а затем `*(++it)`). Он обещает, что после их выполениея (в неизвестном порядке) он выведет в указанном порядке их результаты. 2. Поэтому, в нашем случаии компилятор сначала выполнил `*(++it)`, а затем `*(it)`. Поэтому не стоит отправлять в поток зависимые данные таким образом. Эта проблема не возникает, если мы разделим вызовы std::cout:
```cpp
std::vector<int> v = {1,2,3,4,5,6};
auto it = v.begin(); //итератор указывающий на 1й элемент
std::cout << *it << ", ";
std::cout << *(++it);
/* мы ожидаем: 1, 2 
а получим: 1, 2 */
```

## files
In C++, files are mainly dealt by using three classes **fstream**, **ifstream**, **ofstream** available in `<fstream >`.
- `ofstream`: Stream class to write on files
- `ifstream`: Stream class to read from files
- `fstream`: Stream class to both read and write from/to files.

Есть два способа работы с этими классами. За их реализации отвечают разные ф-ции. 1) Как с потоками те используя операторы `<<` и `>>`. Как и в других потоках, объекты ввода/вывода разделены пробелом (те каждое слово). 2) Можно считывать по-символьно, те файл чиается/записывается по одному char раз.    

Путь до файла: по дефолту (когды мы явно не указываем путь) файлы открываются и сохраняются в папку с .exe программы (или в папку VS проекта), поэтому при запуске программы как .exe нужно убедиться, что она сможет их найти.      

Следует проверять открылся ли файл корректро: 
```cpp
ofstream file("test.txt");
if ( ! file.is_open() ) 
{
   cerr << "open error\n";
}
```

### 1. Работа с файлами, как с потоком (Formatted input)
```cpp
# include <string >
# include <fstream >
using namespace std;
int main() 
{
    string name;
    ifstream input("input.txt");
    input >> name;
    ofstream output("output.txt");
    output << "Hi , " << name << endl;
    return 0;
}
```
Создается объект input (экземпляр класса `std::ifstream`) и иницализируется файлом "input.txt". Далее input ведет себя аналогично потоку std::cin. Те строка `input >> name;` просто записывает содержимое файла в строку name (как это сделал бы std::cin). Далее создается объект output (экземпляр класса `std::ofstream`) и иницализируется файлом "output.txt". Теперь тут все аналогично действиями оператора std::cout, то теперь все записывает в файл, а не в терминал. Файл закроется, когда закороется ф-ция (и дулится объект output).

### 2. Работа с файлом, как последовательностью символов char (Unformatted input)
```cpp
#include <iostream> //для cout
#include <fstream >
using namespace std;

int main() 
{
    string name;
    ifstream input("input.txt");
    for(char c = input.get(); in.good(); c = input.get())
    {
        cout << c; //будет выводить побуквенно (по char), пока не дойдет до конца файла
    }
   
    ofstream out("out.txt");
    char c = 'd';
    out.put(c) //запишет символ в файл. Действие симметрично .get()
    
    return 0;
}
```
При таком подходе в файле есть **индекс считывания** (просто обычный int), который указывает в каком месте файла мы находимся. `input.get()` - это метод класса `ifstream`, который считывает и возвращает 1 char за раз, а затем сдвигает идекс считывания на 1. input.get() вернет -1, если мы вышли за границы файла. `input.good()` - это state function(см ниже) метод проверяющий не возникло ли ошибки при работе с файлом (Например, когда индекс считывания вышел за границы файла, то input.good() вернет false. Какие еще могут быть ошибки см ниже). В подобном случаии удобно использовать for (а не while). `out.put(c)` - записывает 1 char в output файл. Нет необходимости закрывать output файл (`out.close()`) тк при выходе из блока деструктор класса ofstream автоматически закроет его.

#### Методы
##### Possition
- `std::ios_base::cur` - индекс считывания указывает на current позицию
- `std::ios_base::beg` - индекс считывания указывает на начало файла
- `std::ios_base::end` - индекс считывания указывает на конец файла

##### Input functions
- `get()` - прочитает и вернет 1 char (1 байт), а потом сдвинет индекс считывания в файле.
- `unget()` - сдвигает индекс исчитывания назад на 1 байт (те делать последний символ доступным после get()).
- `getline(char_type* s, int c_size)` or `getline(char_type* s, int c_size, char delim)` - считывает всю строку до переноса или символа указаного в параметре char delim и сохраняет в строковый объект s (c++ or c-style строку). c_size - это размер элемента строкового объекта s.
- `read( char_type* s, int n)` - считывает n байт и сохраняет их в строковый объект s (c++ or c-style строку). Если в файле число, то его можно сохранить так (пусть мы создали int num, куда будем сохранять это число): `in.read(reinterpret_cast<char*>(&num), sizeof(int))` - те будут считаны первые n = sizeof(int) байт, реинтерпритированы в int и сохранены в num. Если файлы читаются подобным образом (не посимвольно), то их надо открывать в binary mode: `std::ifstream in("my_file.dat", std::ios::binary)`.
- `seekg(int n)` and `seekg(int n, std::ios_base::pos)` (от Seek get) - *Устанавливает* индекс считывания на n байт. Возвщает указатель  Две версии ф-ции: 1)на n байт от начала. 2) на n байт от позиции (std::ios_base::pos, где pos - cur, beg или end), при этом может быть -n (то есть отсчет байтов пойдет справа на лево). Например, `seekg(0, std::ios_base::end)` - вернет размер файла в байтах (те индекс считывания указывающий на конец файла = кол-во символов, каждый символ - 1 байт).
- `tellg()`- возвращет int значение индекса считывания в данный момент.

##### Output functions
- `put(char c)` -  - запишет 1 char (1 байт), а потом сдвинет индекс записи в файле.
- `write( char_type* s, int n)` - зписывает первые n байт строкового объекта s (c++ or c-style строку). Можно использовать например, для записи int (пусть есть int num): `out.write(reinterpret_cast<char*>(&num), sizeof(int))` - тк int - это 4 байта, то тут n =  sizeof(int) = 4. Если файлы записываются подобным образом (не посимвольно), то их надо открывать в binary mode: `std::ofstream out("my_file.dat", std::ios::binary)`. 
- `seekp(int n)` and `seekp(int n, std::ios_base::pos)` (от Seek Put) - действует аналогично seekg, только для output файлов.
- `tellp()`- возвращет int значение индекса записи в данный момент.
- `close()` - закрывает output файл.

### Типичный loop работы с файлом
```cpp
std::fstream in( filename );
for( std::string line; std::getline( in,line ); )
{
	if( line == "[Tile Size]" )
	{
		in >> tileSize;			
	}
	else if( line == "[Board Dimensions]" )
	{
		in >> boardWidth >> boardHeight;
	}
	else if( line == "[Poison Amount]" )
	{
		in >> nPoison;
	}
}
```
- Цикл `for( std::string line; std::getline( in,line ); )` последовательно считывает строку за строкой и сохраняет в строку line. Тк файл (те объект `fstream`) - это поток, то каждый вызов getline() выводит 1 строке из потока.  
- Внутри цикла идет проверка содержимого строки line. Если там лежит то, что нам надо, то мы сохраняем данные идещие слелом в потоке: `in >> tileSize`. Тк fstream - это поток, то оба оператора getline() и `in >>` считывают с потока (1й - целую строку, 2й - строку до пробела).

### State functions
`ifstream` объекты имееют оператор перегрузки в bool (true если файл считался, false если нет). Поэтому можно напрямую провести проверку считался ли файл или нет:
```cpp
ifstream input("input.txt");
if(input)           //input был преобразован в bool. Если файл не открылся то вернется false. 
{//do something}
```
**Методы:**
- `.good()` - Проверяет наличие ошибок, сюда входят все ниже перечисленные ошибки (те это общая проверка). I/O operations are available
- `.eof()` - end-of-file. Проверяет, достиг ли мы конца файла
- `.fail()` - an error has occurred on the associated stream. Например, мы ошиблись с названием файла или пакой и программа не может его найти.  
- `.bad()` - non-recoverable error has occurred on the associated stream

### binary mode
Файлы можно открывать в binary mode: `std::ifstream in("my_file.txt", std::ios::binary)` or `std::ofstream out("my_file.txt", std::ios::binary)`. Это нужно делать, когда работа с файлом идет не по-символьно (например, с числами). По факту, разница между стандартным и binary открытием в следующем. Файл .txt - это просто набор ASCII цифр. Windows рассматривает перенос строки `\n` (ASCII символ 13) как Carriage return, а потом Line feed(ASCII символ 10). Те, чтобы Windows корректно перенес строку в файле надо записать 2 символа ASCII: 13, затем 10. Пусть у нас есть строка `abc\n\n123`. Если создать output файл в обычном режиме и записать туда эту строку, то при открытии в обычном текстовом редакторе мы увидем:
```
abc            
                

123
```
Однако если мы откроем это в hex text editor(который показывает вместо символов ASCII номера), то увидем: `97(a) 98(b) 99(c) 13(Carriage return) 10(Line feed) 13(Carriage return) 10(Line feed) 49(1) 50(2) 51(3)`. Те в output были добавлены сиволы  10(Line feed) после каждого 13(Carriage return), чтобы все читалось корректно в Windows.    
Если же открыть файл в binary mode, то  10(Line feed) не будет добавляться. Это важно, тк если бы мы писали в файл бинарный код или обычные int напрямую, то эти дополнительные 10, вписанные без нашего ведома, сдивинули бы все. Так если бы мы открыли файл в binary mode и записали в него `abc\n\n123`, то hex text editor прочитал бы его так: `97(a) 98(b) 99(c) 13(Carriage return) 13(Carriage return) 49(1) 50(2) 51(3)`, а обычный text editor:  
```
abc123 (без переносов строки)
```

## stringstream
Объект, который позволяет работать со строками будто это поток.
```cpp
#include <sstream> // для std::stringstream

int countWords(string str) 
{ //ф-ция разбивает строку на отдельные слова и считает их кол-во
    stringstream s(str);    // Used for breaking words 
    string word;            // to store individual words 
    
    int count = 0; 
    while (s >> word)       //считываем из stringstream как из потока (те отдельные элементы разделены пробелом)
        count++;           
    return count;           //вренм сколько было слов в строке
} 
```
В начале создается объект типа `std::stringstream`, который инициализуется строкой (`stringstream s(str)`). Теперь его можно использовать аналогично потоку.   
### Методы
- operator `<<` — add a string to the stringstream object.
- operator `>>` — read something from the stringstream object.
- `clear()` — to clear the stream.
- `str()` — to get and set string object whose content is present in stream.
