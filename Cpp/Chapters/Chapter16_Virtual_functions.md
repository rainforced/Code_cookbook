# Chapter 16: Virtual functions
## Перегрузка (overload) vs переопределение (override)
- При **перегрузке** сигнатура ф-ции должна отличаться от изначальное (те другие аргументы и тд). 
- При **переопределении** сигнатура ф-ции должна остаться в точности той же самой. Используется для *виртуальных ф-ций*.

**Виртуальная функция** — это особый тип функции, которая, при её вызове, вызывает «наиболее» дочерний метод, который существует между родительским и дочерними классами. Для этого перед переопердяемой ф-цией ставится ключевое слово `virtual`.
## Полиморфизм (и постановка задачи)
**Полиморфизм** -  возможность объектов с одинаковой спецификацией иметь различную реализацию: "Один интерфейс, множество реализаций". Виртуальные ф-ции - это ключевой аспект реализации полиморфных объектов.    
Например, у нас есть класс Animal, который мы хотим использовать как основу для более специфических классов, например, Cat, Dog etc., 
```cpp
class Animal
{
protected:
    std::string m_name;

    /* Мы делаем этот конструктор protected так как не хотим,  чтобы пользователи создавали 
    объекты класса Animal напрямую, но хотим, чтобы у дочерних классов доступ был открыт */
    Animal(std::string name)
        : m_name(name)
    {
    }
public:
    std::string getName() { return m_name; }
    const char* speak() { return "???"; }
};
```
Теперь создадим дочерние классы на его основе, 
```cpp
class Cat: public Animal
{
public:
    Cat(std::string name)
        : Animal(name)
    {
    }
 
    const char* speak() { return "Meow"; } //переопределили ф-цию speak для класса Cat
};
 
class Dog: public Animal
{
public:
    Dog(std::string name)
        : Animal(name)
    {
    }
 
    const char* speak() { return "Woof"; }  //переопределили ф-цию speak для класса Dog
};
```
### Пример 1
Теперь, если мы захотим создать универсальный интерфейс работы с классами Cat и Dog, то у нас возникнет проблема. Например, если я хочу создать независимую ф-цию, вызвающую `speak()` для обоих классов, то мне придется сделать 2 перегрузки принимаюшие `Cat& cat` и `Dog& dog`. Есть решение по-лучше: я могу взять их общую точку отсчета - родительский класс Animal :`Animal& animal`,
```cpp
/*** Вместо этого ***/
void report(Cat &cat)
{
    std::cout << cat.getName() << " says " << cat.speak() << '\n';
}
 
void report(Dog &dog)
{
    std::cout << dog.getName() << " says " << dog.speak() << '\n';
}

/*** Сделать это ***/
void report(Animal &rAnimal)
{
    std::cout << rAnimal.getName() << " says " << rAnimal.speak() << '\n';
}
```
Проблема в том, что при вызове `report(cat)` будет сделан "срез" родительской части (тк ссылка идеть только на родительскую часть), а значит перегрузка ф-ции `speak()` не будет учтена и бедт вызвана ее родительская форма.     
Тут на помощь приходят виртуальные ф-ции. В этом случаии, если мы сделаем `virtual const char* speak() { return "???"; }` в родительском классе, то компилятор, увидя, что ф-ция виртуальная, будет искать наиболее дочернюю перегрузку этой ф-ции (те ф-ции дочерних классов Cat и Dog).     
### Пример 2 
Хотим создать массив, куда соберем разных животных. Тк Cat и Dog - это разные массивы, то с этим будет проблема. Можно аналогичено создать массив класса Animal, а методы, которые мы хотим использовать для их элементов сделать вирутальными,
```cpp
Cat matros("Matros"), ivan("Ivan"), martun("Martun");
Dog barsik("Barsik"), tolik("Tolik"), tyzik("Tyzik");
 
// Создаём массив указателей на наши объекты Cat и Dog
Animal *animals[] = { &matros, &barsik, &ivan, &tolik, &martun, &tyzik};
for (int iii=0; iii < 6; ++iii)
    std::cout << animals[iii]->getName() << " says " << animals[iii]->speak() << '\n'
```

## Особенности использования виртуальных ф-ций
### Пример цепочки наследования 
```cpp
class A
{
public:
    virtual const char* getName() { return "A"; }
};
 
class B: public A
{
public:
    virtual const char* getName() { return "B"; }
};
 
class C: public B
{
public:
    virtual const char* getName() { return "C"; }
};
 
class D: public C
{
public:
    virtual const char* getName() { return "D"; }
};
 
int main()
{
    C c;
    B &rParent = c;
    std::cout << "rParent is a " << rParent.getName() << '\n';
 
    return 0;
}

/***** Будет выведенно *****
rParent is a B
***************************/
```
В данном примере демонстрируется как работает "срез" и вирутуальная ф-ция. Мы сделали срез по классу B из его дочернего класса C. Далее мы вызываем ф-цию getName(). Компилятор обнаруживает, что это virtual ф-ция и начинает идти вверх по дочерним классам, пока не найдет наиболее дочернюю для *данного случая* перегрузку. В данном случаи это перегрузка для дочернего класса C. Он не пойдет далее в класса D, тк он лежит за рамками данного случая. 

### Корректная перегрузка виртульных ф-ций
Перегруженные вресии виртуальных ф-ций должны полностью совпадать по: 
1. Сигнатуре (имя, типы параметров и является ли метод константным),
2. Типу возвращаемого значения.     
Считай быть идентичными по сигнатуре.

#### Ковариантный тип возврата
Исключением являются ф-ции с **Ковариантным типом возврата** - это когда метод некоторого класса возвращает указатель на самого себя. В этом случаии позволительно иметь различные типы возвращаемого значения. Например,
```cpp
class Parent{
public:
    virtual Parent* getThis() { std::cout << "called Parent::getThis()\n"; return this; }
}
class Child: public Parent
{
public:
    virtual Child* getThis() { std::cout << "called Child::getThis()\n";  return this; }
}
```
### Порядок указания virtual
Не обязательно указывать `virtual` рядом скаждой перегрузкой ф-ции. Достаточно указать это у родителя (у класса, по которому пойдет срез). Например, в данном случаи все следующие 3 варанта будут работать:
1. Случаий 1, это когда `virtual` указывается для всех ф-ций, задуманных как virtual. Это самый лучший способ, тк улучшает читаемость кода. 
2. ```cpp
    // 2. Случай virtual написано у самого "глубокого" родятеля
    class A
    {
    public:
        virtual const char* getName() { return "A"; }
    };
    class B: public A
    {
    public:
        const char* getName() { return "B"; }
    };
    class C: public B
    {
    public:
        const char* getName() { return "C"; }
    };

     int main()
     {
        C c;
        B &rParent = c;
        std::cout << "rParent is a " << rParent.getName() << '\n';

        return 0;
     }
     ```
3. ```cpp
    // 3. Случай virtual написано у родятеля по которому идет срез
    class A
    {
    public:
        const char* getName() { return "A"; }
    };
    class B: public A
    {
    public:
        virtual const char* getName() { return "B"; }
    };
    class C: public B
    {
    public:
        const char* getName() { return "C"; }
    };

     int main()
     {
        C c;
        B &rParent = c;
        std::cout << "rParent is a " << rParent.getName() << '\n';

        return 0;
     }
     ```
     Тут нужно отметить, что в данном случаии виртуальные ф-ции начинаются с класса B. Поэтому, если бы срез был `A &rParent = c;` то перегрузка бы не срабоата, тк для класса A ф-ция getName() не является виртуальной.     

### Виртуальные ф-ции в конструкторах и деструкторах
В обоих случаях они ведут себя как обычне ф-ции:
- В конструкторах: конструктор дочернего класса будет последоваетльно вызывать конструкторы классов от базового до самого дочернего (base->next_child->next_child->...->final_child). При этом, в базовом классе находится *__vptr (указатель на виртуальную таблицу). Он будет последовательно переписываться в каждом из этих конструкторов, инициализоваться таблицой соответствующей данному дочернему классу. Таким образом, если вызвать виртуальный метод в конструкторе, то будет вызван самый дочерний метод для *этого* конструктора.
- В дестректорах: уничтожение идет от самого дочернего к самому базовому. При этом, в C++ реализована перезапись *__vptr при очередном вызове деструктора. Таким образом, в данном деструкторе *__vptr будет соответвовать данному классу. Так в деструкторе будет доступено переопределение соответвующее виртуальной таблице данного класса.

### Когда НЕ использовать virtual
1. Виртуальные ф-ции работают медленнее, чем обычные (см ниже). Более того, для каждого экземпляра класса с виртуальной ф-цией компилятор создает дополнительный указатель для своей внутренней работы. Таким образом, следуют использовать virtual только в случаии явной реализации полиморфизма кода. 
2. Не следует использвать virtual для ф-ций вызываемых в конструкторах и деструкторах. В конструкторах самым дочерний класс создается в последнюю очередь, в деструкторе самый дочерний класс будет уничтожен первым. **В конструкторах и деструкторах виртуальные ф-ции ведут себя как обычные**.
 
## Final and override
### Override
Это модификатор (те слово значение, которого зависит от контекста. Хотя у override есть только 1 корректный контекст), говорящий компилятору, что данная ф-ция задумывалась как виртуальная. Поэтому, если по какой-то причине не происходит ее перегрузка (например, неправильно написана сигнатура), то компилятор выдаст ошибку. Записывать ее следуюет так,
```cpp
class A
{
public:
	virtual const char* getName1(int x) { return "A"; }
	virtual const char* getName2(int x) { return "A"; }
	virtual const char* getName3(int x) { return "A"; }
};
 
class B : public A
{
public:
	virtual const char* getName1(short int x) override { return "B"; } // ошибка компиляции, метод не является переопределением
	virtual const char* getName2(int x) const override { return "B"; } // ошибка компиляции, метод не является переопределением
	virtual const char* getName3(int x) override { return "B"; } // всё хорошо, метод является переопределением A::getName3(int)
 
};
```
Это полезная штука, которую следует всегда использовать как проверку выполнения виртуальности ф-ции.
### Final 
#### Для виртуальной ф-ции
Это модификатор запрещающий дальнейшее наследования (для классов) и переопределение (для виртуальных ф-ций). Например,
```cpp
class A
{
public:
	virtual const char* getName() { return "A"; }
};
 
class B : public A
{
public:
	// Заметили final в конце? Это означает, что метод переопределить уже нельзя
	virtual const char* getName() override final { return "B"; } // всё хорошо, переопределение A::getName()
};
```
Это значит, что, если некоторый класса C, являющейся наследником для B, попробует переопределить ф-цию getName(), то компилятор выдаст ошибку. Дальнейшее переопределение запрещено.
#### Для класса 
```cpp
class A
{
public:
	virtual const char* getName() { return "A"; }
};
 
class B final : public A // обратите внимание на модификатор final здесь
{
public:
	virtual const char* getName() override { return "B"; }
};
 
class C : public B // ошибка компиляции: нельзя наследовать класс final
{
public:
	virtual const char* getName() override { return "C"; }
};
```
Тут класс C не может стать наследником класса B, тк у него стоит модификатор final.

## Виртуальный деструктор 
Если программа предсматривает *полиморфное* повидение некоторого класса, то **его деструктор должен быть виртулальным**. Под полиморфным повидением подразумевается вероятность использования "срезов", те указателей Parent классов для указания на их дочерние подклассы. Суть виртуального деструктора аналогична виртуальным ф-циям: вызов самого дочернего деструктора. Пример, где это необходимо,
```cpp
class Parent
{
public:
    ~Parent() // примечание: Деструктор не виртуальный
    {
        std::cout << "Parent: я тут вообще ничего не делаю" << std::endl;
    }
};
 
class Child: public Parent
{
private:
    int* m_array;
 
public:
    Child(int length)
    {
        m_array = new int[length];
    }
 
    ~Child() // примечание: Деструктор не виртуальный
    {
        std::cout << "Child: а я чистю память" << std::endl;
        delete[] m_array;
    }
};
 
int main()
{
    Parent *parent =  new Child(7);
    delete parent; // вызывается деструтруктор класса Parent, который не высвобождает выделенную память.
 	
    return 0;
}
```
В этом случаи у нас произойдет утечка памяти, тк был вызван Parent деструктор, который не удаляет Child часть класса (он о ней даже не знает). Ситуацию спасет `virtual`, 
```cpp
virtual ~Parent() // примечание: Деструктор виртуальный
{
	std::cout << "Calling ~Parent()" << std::endl;
}
```
Теперь будет вызван самый дочерний деструктор. Можно спать спокойно!

## Виртуальные табилцы (virtual tables)
Виртуальные таблицы - это классический пример динамичского связывания (тк компилятор не знает какую из ф-ций вызовет программа, пока она не дойдет до место исполнения ф-ции). Когда програмист объявляет виртуальную ф-цию, компилятор автоматически добавляет в каждый класс следующие компоненты.
1. Виртуальная таблица (virtual method table (VMT))
2. Скрытый указатель на вирутуальную таблицу (`*__vptr`) (по 1 на каждый экземпляр)
     
### 1. Виртуальные таблицы (VMT)
Чтобы класс всегда знал какие из виртуальных ф-ций наиболее дочернии используется виртуальная таблица. Это статический массив, каждый элемент которого это указатель на наиболее дочений метод доступный данному классу. На каждый класс есть только 1 виртуальная таблица, которая хранится в statiс памяти, и к ней имеют доступ все экземпляры данного класса. Это также значит, что таблица не увеличивает размер объекта данного класса.     
Другой важный момент заключается в том, что каждый класс имеет свою таблицу. Те, если у нас есть Parent класс и два наследника, то у каждого будет своя таблица (те всего 3 таблицы). Например, у нас есть 2 класса: B (parent) и C (child):
```cpp
class B
{
public:
  virtual void bar();
  virtual void qux();
};

void B::bar() { std::cout << "This is B's implementation of bar" << std::endl;}
void B::qux() { std::cout << "This is B's implementation of qux" << std::endl;}

class C : public B
{
public:
  void bar() override;
};
void C::bar() { std::cout << "This is C's implementation of bar" << std::endl;}

int main()
{
	B* b = new C(); // сделали "срез" класса C по его родительской части (класс B)
	b->bar(); // ф-ция bar - виртуальная и имеет перегрузку в классе C
}
```
В этом случаии таблицы для классов B и C будут выглядеть следующим образом,      
<img src = "https://github.com/PlohoyParen/Cpp_doc/blob/master/Documents/images/vpointer.jpg" alt = "vpointer" width = "700" />
- тк у нас 2 виртуальные ф-ции, то у табилиц обоих классов будет 2 элемента: указатель на соответсвующую реализацию bar и qux. Так для класса B самыми дочерними являются реализации B::bar() и B::qux(). Для класса С, доступна перегрузка bar(), поэтому самой дочерней реализацией является C::bar(). *Перегрузка виртуальной ф-ции - это самая дочерняя реализация доступная данному классу*, поэтому в таблице будет указатель на ф-цию не "выше/дочерней", чем данный класс.

### 2. Скрытый указатель на виртуальную таблицу (vpointer)
До сего момента не стало ясно как же при "срезе" программа понимает какую перегрузку (а точнее какую из виртуальных таблиц использовать).   
Когда программист создает виртуальную ф-цию, компилятор также добавляет скрытый указатель в родительском классе, который указывает на виртуальную таблицу данного класса:
```cpp
/*** Что видим мы ***/
class B
{
public:
    virtual void bar() {};
    virtual void qux() {};
};

/*** Что ввидит коспилятор ***/
class B
{
public:
    FunctionPointer *__vptr; // Вот он! Скрытый указатель
    virtual void bar() {};
    virtual void qux() {};
};
```
Таким образом, класс B имеет дополнительный указатель, добавленный в класс компилятором. Этот указатель указывает на виртуальную таблицу класса B. Когда дочерний класс C наследуют от родительского B, он также наследует скрытый указатель, *в котором теперь хранится указатель на виртуальную таблицу класса C*. Таким образом, получается, что, когда мы делаем "срез" родительским классом, то мы копируем содержимое `*__vptr`. Так, делая подобное действие:
```cpp
B* b = new C(); 
```
мы копируем в `*__vptr` класса B содержимое класса C, те указатель на таблицу класса C. В итоге, данный "срез" ссылается на виртуальную таблицу класса C -> он будет использовать его наиболее дочерние методы.  

### Виртуальные ф-ции медленнее обычных
Для обычных ф-ций компилятор использует статическое связывание (напрямую вставляет инструкцию на исполнение ф-ции в месте ее вызова) - это одно действие. Для виртуальных ф-ций коспилятор совершает 3 действия: 1)смотрит содержимое `*__vptr`, 2)переходит в таблицу и ищет указатель на соответсвующую ф-цию, 3)переходит по адресу и выполняет нужную перегрузку. Это замедляет выполнение программы, поэтому ф-ции в c++ по дефолту не виртуальные, и их создание лежит на совести программиста.

## Абстрактные классы и Интерфейсы
### Абстрактные функции и абстрактные классы
В c++ если ф-ции-"затычки", которые называются **чистые виртуальные функции** (или ещё **абстрактные функции**). Они нужны как placeholder, для того чтобы программист переопределил их в новом дочернем классе. Если эта ф-ция не будет переопределена, то компилятор выдас ошибку. Так что программист *обязан* переопределить обстрактную ф-цию при наследовании. Также важно отметить, что абстрактными ф-циями могут быть только виртуальные ф-ции (логично, тк сам экземпляр этой ф-ции не функционален). Оформляется это так:
```cpp
class Parent
{
public:
    const char* sayHi() { return "Hi"; } // обычная не виртуальная функция    
    virtual const char* getName() { return "Parent"; } // обычная виртуальная функция
    virtual int getValue() = 0; // чистая виртуальная функция
};

class Child: public Parent //Ошибка! Необходимо переопределить абстрактную ф-цию.
{
public:
	 virtual const char* getName() { return "Child"; } // переопределили getName
	// а getValue переопределить забыли -> компилятор выдаст ошибку
}; 
```
Более того, если у класса есть *хотя бы одна* абстрактная ф-ция, то это **абстрактный класс**. Это значит, что нельзя создавать объекты этого класса. Он нужет только для наследования и написания полиморфного кода (см. далее про интерфейсы). Так, в примере выше класс Parent - это абстрактный класс, и, если мы попытаемся создать объект класса Parent, то компилятор выдаст ошибку.     
На самом деле абстрактную ф-цию можно определить (хотя вызвать ее нельзя все равно). При этом необходимо вынести определение за тело класса (те определить используя namespace, типо Parent::getValue). Это полезно, когда вы хотите, чтобы дочерние классы имели возможность переопределять виртуальную функцию или оставить её реализацию по умолчанию (которую предоставляет родительский класс). Например:
```cpp
class Animal // это абстрактный родительский класс
{
protected:
    std::string m_name;
 
public:
    Animal(std::string name)
        : m_name(name)
    {
    }
 
    std::string getName() { return m_name; }
    virtual const char* speak() = 0; // окончание "= 0" означает, что эта функция является чистой виртуальной функцией
};
 
const char* Animal::speak()  // несмотря на то, что вот здесь её определение
{
    return "buzz";
}

const char* Animal::speak() 
{
    return "buzz"; // реализация по умолчанию
}
 
class Dragonfly: public Animal
{
 
public:
    Dragonfly(std::string name)
        : Animal(name)
    {
    }
 
    virtual const char* speak() // этот класс уже не является абстрактным, так как мы переопределили функцию speak()
    {
        return Animal::speak(); // используется реализация по умолчанию класса Animal
    }
};
```

### Интерфейсы
Главна причина существования абстрактных классов - это необходимость использования **Интерфейсов**. **Интерфейс** -  это класс, который не имеет переменных-членов и все методы которого являются чистыми виртуальными функциями. Нужно это, чтобы создавать полиморфные программы, работающие с похожими но разными классами, используя один и тот же набор ф-ций. Например, создадим класс-интерфейс IErrorLog: 
```cpp
class IErrorLog
{
public:
    virtual bool openLog(const char *filename) = 0;
    virtual bool closeLog() = 0;
 
    virtual bool writeError(const char *errorMessage) = 0;
 
    virtual ~IErrorLog() {}; // создаём виртуальный деструктор в случае, если удалим указатель на IErrorLog, то чтобы вызывался соответствующий деструктор дочернего класса
};
```
Любой класс, который наследует IErrorLog, должен предоставить свою реализацию всех трёх методов класса IErrorLog. Вы можете создать дочерний класс с именем FileErrorLog, где openLog() открывает файл на диске, closeLog() закрывает файл, а writeError() записывает сообщение в файл. Вы можете создать ещё один дочерний класс с именем ScreenErrorLog, где openLog() и closeLog() ничего не делают, а writeError() выводит сообщение во всплывающем окне. Теперь предоположим, что вам нужно написать программу, которая использует журнал ошибок. Если вы будете писать классы FileErrorLog или ScreenErrorLog напрямую, то это не эффективно. Например, следующая функция заставляет всех объектов, вызывающих mySqrt(), использовать общий интерфейс IErrorLog, что позволит использовать эту ф-цию для любого из дочерних классов IErrorLog, например FileErrorLog (вывести сообщение в файл) или ScreenErrorLog (вывести сообщение на экран):
```cpp
double mySqrt(double value, IErrorLog &log)
{
    if (value < 0.0)
    {
        log.writeError("Tried to take square root of value less than 0");
        return 0.0;
    }
    else
        return sqrt(value);
}
```
### Чистые виртуальные функции и виртуальная таблица
Запись чистой виртуальной функции в виртуальной таблице обычно содержит либо нулевой указатель, либо указывает на общую функцию, которая выводит ошибку (иногда эта функция называется `__purecall`), если не было обнаружено переопределения.

## Перегрузка потоков ввода и вывода
Тк перегрузка потоков ввода и вывода всегда осуществляется через обычные/дружественные ф-ции (тк требует 2 параметра), то напрямую виртуальные ф-ции с ними использовать нельзя (виртуальными могут быть только методы класса, но не внешние ф-ции). Однако, можно схитрить: написать виртуальную ф-цию вывода, а оператор `<<` потока будет вызывать ее. Наприрмер, 
```cpp
class Parent
{
public:
	Parent() {}
	// Перегрузка оператора вывода <<
	friend std::ostream& operator<<(std::ostream &out, const Parent &p)
	{
		// Делегируем выполнение операции вывода методу print()
		return p.print(out);
	}
	// Делаем метод print() виртуальным
	virtual std::ostream& print(std::ostream& out) const
	{
		out << "Parent";
		return out;
	}
};
 
class Child: public Parent
{
public:
	Child() {}
	// Переопределение метода print() для работы с объектами класса Child
	virtual std::ostream& print(std::ostream& out) const override
	{
		out << "Child";
		return out;
	}
};
 
int main()
{
	Parent p; 		// случай 1
	std::cout << p << '\n';
 
	Child ch;		// случай 2
	std::cout << ch << '\n'; // обратите внимание, всё работает даже без наличия перегрузки оператора вывода в классе Child
 
	Parent &pref = ch;	// случай 3
	std::cout << pref << '\n';
 
	return 0;
}

/*** Выведет ***
Parent 
Child
Child
***************/
```
1. В этом случаии вызывается Parent версия метода print, как и ожидалось.
2. При передаче ссылки на объект класса Child в ф-цию потока (`<<`) проиходит неявное преобразование Child->Parent. Далее вызывается метод print, однако тк он является виртуальным, то вызов переходет к дочерней версии.
3. Все произойдет аналогично случаю 2, только без неявного преобразования Child->Parent

## Как "докапаться" до содержимого виртуальной таблицы
Несмотря на то, что все классы, имеющие виртуальные ф-ции, содержат указатель на виртуальную таблицу, компилятор запрещает доступ к нему. Можно исхитриться и вынуть его (работает не на всех компиляторах). Так как он идет первым полем находящимся по адресу объекта, то можно преобразовать в `*int` содержимое этого поля (те первые `sizeof(int)` лежащие по адресу объекта): `int* vptr =  *(int**)obj;`. Полный разбор что и почему [тут](https://kaisar-haque.blogspot.com/2008/07/c-accessing-virtual-table.html).
```cpp
#include <iostream>

using namespace std;

//a simple class
class X
{
public:
//fn is a simple virtual function
virtual void fn()
{
  cout << "n = " << n << endl;
}
//a member variable
int n;
};

int main()
{
//create an object (obj) of class X
X *obj = new X();
obj->n = 10;

//get the virtual table pointer of object obj
int* vptr =  *(int**)obj;

// we shall call the function fn, but first the following assembly code
//  is required to make obj as 'this' pointer as we shall call
//  function fn() directly from the virtual table
__asm
{
	mov ecx, obj
}
//function fn is the first entry of the virtual table, so it's vptr[0]	
( (void (*)()) vptr[0] )();
//the above is the same as the following
//obj->fn();
return 0;
}
```