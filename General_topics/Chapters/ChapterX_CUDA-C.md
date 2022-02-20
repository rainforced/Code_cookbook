# ChapterX: CUDA-C/C++
## Основы
В целом GPU нужны, чтобы быстро параллельно обрабатывать простые вычисления. Такие вычисления встречаются, например, при рендеринге. Мы рендерим фрейм сотни раз в секундку (60 frame это хорошо для игр, например). Каждый раз компьютер должен просчитать как и куда рансформируются точки (vertices) из которых состоят объекты в графике. Это просте вычисления, но их очень много. Так GPU делает простые вычисления параллельно. Сравнивая CPU и GPU получим у меня на ноуте (intel core i7 и GeForce960M) получим, что у CPU 4 гигогерцовых ядра, а у GPU 640 мегагерцовых ядер (для ноута mobile версия, для обычной GeForce960 - 1024 ядер). Отсюда, **GPU хорош для параллельных алгоритмов, а CPU для последовательных**.     

CUDA - это специальное API для видеокарт NVIDIA. Хотя синтаксис аналогичен C/C++ с небольшими добавлениями, под капотом он реализован на PTX языке (Parallel Thread Execution (PTX, or NVPTX) - это псевдо-ассемблер разработанный NVIDIA), который потом переводтся в в бинарный код для GPU. Надо понимать, что это CUDA - это не библиотека для С/С++ - это считай что отдельный язык, который называется CUDA-C/C++. Для его реализации нужен специальный компилятор NVCC (не входит в стандартные пакеты IDE), а файлы с кодом имеют расширение `.CU` (в VS можно выставить опцию, что .CU файлы автоматически ассоциировались с NVCC компилятором). Вообще NVCC компилирует CUDA часть кода, а потом остальное передает на обычный C/C++ компилятор.     

**Embarrassingly (or pleasingly) parallel algorithm** - алгоритм, в котором threads не взаимодействуют друг с другом, соответственно могут действовать абсолютно параллельно. Это идеальный случай для вычслений на GPU, где он может развернуться во всей красе.

### Термины
**Host** - это CPU. Host is in control of the system RAM, disks and other devises.       
**Device** - это GPU. It has its own RAM, and it can control only this RAM. It doesn't have access to system RAM, disks and other deivses.       
**Kernel** - так называют ф-ции написаные на CUDA языке, которая выпоняется на GPU. Каждый kernel может выполняться параллельно на 100- 1000 threads. Пусть есть `SomeKernel<<<n, m>>> (...аргументы...)`. Числа в `<<<n, m>>>`(**launch configuration**): n-number of thread blocks, m-number of threads in one block. На kernel накладываются следующие ограничения:     
  1. No recursive kernels
  2. kernels always return void
  3. They can't use system memory (?)
  4. Kernel can't have variable number of arguments
  
**Function qualifiers** - это приставки, которые пишутся перед ф-цией, чтобы компилятор понял, что это kernel. There are 3 function qualifiers (используется два черточки `__`, а не одна `_`):    
  1. `__global__` - called by host, runs on device (те вызывается, например, из main()).
  2. `__device__` - called by device, runs by device (вспомогательные ф-ции, которые вызываются из других kernel).
  3. `__host__` - normal host function (те device ее вообще не трогает. Все делается на CPU). На самом деле NVCC компилятор передает такие ф-ции напрямую обычному компилятору C, чтобы тот сам разбирался со своими CPU ф-циями.      
  
### System (RAM) and graphic card memory  
CPU имеет доступ к RAM (на ней он и считает, стэкает и хипает), но не имеет доступа к памяти видеократы. У GPU есть доступ к память видеокарты (напримре, у GeForce 960m - это 2GB), но не имеет доступа к RAM. Поэтому возникает проблема: основная часть программы (Host) распоряжается RAM, а kernel распоряжаются только памятью видеократы. Во время программы нужно передавать данные из RAM на память видеократы (и обратно). Следующие ф-ции нужны для этого:
- `cudaMalloc(int **devPrt, sizeof(int))` - выделяет память на device (те на видеокарте). Первый параметр - указатель на указатель на нужного типа (тут int), второй параметр - это размер выделяемой памяти (тут на 1 int). Эта ф-ция возвращает тип `cudaSuccess`, если память успешно выделена. Таким образом, можно зафиксировать ошибку при выделении памяти:
  ```cpp
  if(cudaMalloc(&d_a, sizeof(int)*count) != cudaSuccess)
    {
      std::cout << "Smth went wrong!";
    }
  ```
- `cudaFree(int* devPrt)` - высвобождает память на device соответвующую данному указателю.
- ` cudaMemcpy(void* dest, void* src, sizeof(int), enum direction)` - ф-ция копирующая содержимое указателя src в указатель dest. Нужна для передачи данных с Host на device (и обратно). Копирует объект целиком, включая массивы (ориентируется по размеру). При этом надо указать направление передачи(enum direction): `cudaMemcpyHostToDevice` и `cudaMemcpyDeviceToHost`. На самом деле есть еще `cudaMemcpyHostToHost` и `cudaMemcpyDeviceToDevice`, но не оч понятно зачем. Аналогично, это ф-ция возвращает тип `cudaSuccess`, если все успешно скоприровано. 
```cpp
  if(cudaMemcpy(d_a, h_a, sizeof(int)*count, cudaMemcpyHostToDevice) != cudaSuccess)
    {
      std::cout << "Smth went wrong!";
      cudaFree(d_a);
    }
  ```
  
### Threads, thread blocks and grid   
<img src = "https://github.com/PlohoyParen/Cpp_doc/blob/master/Documents/images/Software-Perspective_for_thread_block.jpg" alt = "CUDA_grid" width = 700 >     

- **Thread** - single execution unit that runs kernel on the GPU (те задействует 1 core). 
- **Thread block** - collection of threads. All threads in the same block can communicate. Задействует 1 SMM (streaming multiprocessor) unit. Maximum threads per block is 512 (Compure capability before 1.3) and 1024 (... above 1.3);
- **Grid** - a collection of thread blocks. So, we launch a kernel on a grid (in <<<n, m>>> we configure the grid). Maximum blocks per grid (per launch) is 2^32-1. Подробнее про спецификации для конкретных видеократ [тут](https://en.wikipedia.org/wiki/CUDA#Version_features_and_specifications).

Таким образом, запуская следуюший kernel `somekernel<<<50, 1024>>>(..)`, мы зайдествуем 51200 threads (50 blocks with 1024 in each). Ограничения на кол-во threads per block сделано для того, чтобы один и тот же код работан на видеокартах разных поколений (см [тут](https://en.wikipedia.org/wiki/CUDA#Version_features_and_specifications), при разных compute capability спецификации остаются тема же). Так например, старая видиокарта вытянет только 5 blocks параллельно, а новая 20 блоков. При этом созадется очередь из блоков на выполнение. В итоге новая карта просчитает все быстрее, но не при этом не надо для нее менять код.

#### Ограничения на размеры
**Hardware Constraints**    
Следующие ограничения на размер и состав grid введены NVIDIA:     
1. Each block cannot have more than 512/1024 threads in total (Compute Capability 1.x or 2.x and later respectively)
2. The maximum dimensions of each block are limited to [512,512,64]/[1024,1024,64] (Compute 1.x/2.x or later)
3. Each block cannot consume more than 8k/16k/32k/64k/32k/64k/32k/64k/32k/64k registers total (Compute 1.0,1.1/1.2,1.3/2.x-/3.0/3.2/3.5-5.2/5.3/6-6.1/6.2/7.0)
4. Each block cannot consume more than 16kb/48kb/96kb of shared memory (Compute 1.x/2.x-6.2/7.0)

**Performance Tuning**    
Вопрос оптимального выбора размера и состава grid - сложный и люди защищают PhD на эту тему.     
Например, чтобы точно уместить все нужные вычисления можно задать размер блока следующим образом. Пусть на нужно сделать прогон из N шагов (например, for loop на N шагов). Пусть кол-во threads = 512. Тогда нужный размер блока будет = `N/512 + 1` (+ 1 для округления). Тогда вызов нашего kernel будет: `SomeKernel<<<N/512 + 1, 512>>>(..)`. 


#### Thread handling 
- `dim3` - data type состоящий из 3х int значений: x, y и z (они по дефолту равны 1). `dim3 data1(256); //x=256, y=1, z=1`, `dim3 data2(25, 60, 22); //x=25, y=60, z=22`. Обычно используется для создания и работы с grid. Например, все следующие структуры основаны на dim3: `threadIdx`, `blockIdx`, `blockDim`, `gridDim`.

Each thread is an individual object, that has the following atributes:
- `threadIdx` - Thread index within the block.
- `blockIdx` - Block index within the grid.
- `blockDim` - Block dimensions in threads.
- `gridDim` - Grid dimentions in blocs.
Эти атрибуты задают уникальность каждного thread. Часто вычисляют id для отдельных thread: 
```cpp
int id = blockIdx.x*blockDim.x + threadIdx.x;
```
Это id будет уникальным для каждого thread зпущенного программой, поэтому к нему удобно привязывать рассчеты. Так, например, 5th thread in 4th block (путь будет 25 blocks in that kernel) будет иметь id = 4*25 + 5 = 100. 
                                    

### Пример и общий workflow
Ниже программа, которая складывает почленно 2 матрицы на GPU (что-то типо "Hello, world" для CUDA).  
```cpp
#include "cuda_runtime.h"
#include "device_launch_parameters.h"

#include <cuda.h>

#include <iostream>
#include <cstdlib>
#include <ctime>
#include <time.h>

__global__ void AddTwoMtx(int* a, int* b, int size)
{
    int id = blockIdx.x * blockDim.x + threadIdx.x;
    a[id] += b[id];
}

int main()
{
/* 1. На Host (на RAM) создаются нужные нам объекты  */
    int size = 100000;
    int* mtx_a = new int[size];
    int* mtx_b = new int[size];
    //заполнили их случайными числами
    srand(time(NULL));
    for (int i = 0; i < size; i++)
    {
        mtx_a[i] = rand() % 100;
        mtx_b[i] = rand() % 100;
    }

/* 2. На Device выделятеся память достаточная для этих объектов */
    int* dev_a, * dev_b;
    cudaMalloc(&dev_a, sizeof(int) * size);
    cudaMalloc(&dev_b, sizeof(int) * size);

/* 3. Эти объекты копируются с Host на выделенный блок памяти на Device */    
    cudaMemcpy(dev_a, mtx_a, sizeof(int) * size, cudaMemcpyHostToDevice);
    cudaMemcpy(dev_b, mtx_b, sizeof(int) * size, cudaMemcpyHostToDevice);

/* 4. Вызывается kernel */
    AddTwoMtx <<<size / 512 + 1, 512 >>> (dev_a, dev_b, size);

/* 5. Копируем измененные объекты с Device обратно на Host */
    cudaMemcpy(mtx_a, dev_a, sizeof(int) * size, cudaMemcpyDeviceToHost);
    cudaMemcpy(mtx_b, dev_b, sizeof(int) * size, cudaMemcpyDeviceToHost);

/* 6. Высвобождаем память на Device */
    cudaFree(dev_a);
    cudaFree(dev_b);

    return 0;
}
```
Общий workflow следующий:
1. На Host создаются нужные нам объекты.
2. На Device выделятеся память достаточная для этих объектов.
3. Эти объекты копируются с Host на выделенный блок памяти на Device.
4. Вызывается kernel (тк он ничего не возвращает, то все действия производятся in-place, те мы передаем все что хотим поменять через ссылки и указатели).
5. Копируем измененные объекты с Device обратно на Host.
6. Высвобождаем память на Device.


- Библиотеки
  ```cpp
  //#include "cuda_runtime.h"
  //#include "device_launch_parameters.h"
  
  #include <cuda.h>
  ```
  Нужно добавить либо первые 2, либо последнюю (cuda.h). Последняя старая бибилиотка (и она же была в туториалах). Если же добавить первые 2, то появиться подсветка индекса в VS и он перестанет ругаться но CUDA спецефический код.
- Kernel. Вызов kernel имеет следующий ситаксис: `AddTwoMtx <<<size / 512 + 1, 512 >>> (dev_a, dev_b, size);`. Числа в `<<<n, m>>>`: n-number of thread blocks, m-number of threads in one block. 


## GPU memory
<img src="https://github.com/PlohoyParen/Cpp_doc/blob/master/Documents/images/CUDA-memory-model.gif" atl = "GPU_memory" width = 500 >
Память на видеокарте можно отнести к двум типам (по их расположению): 1) основную (**DRAM**) и 2) собственную для каждого мультипроцессора (**SM**-streaming multiprocessor).

### Global memory (DRAM)
CPU и все threads имеют к ней доступ и могут на нее записывать. Каждый байт имеет свой уникальный адрес, поэтому действия над объектами сохраненными в ней происходят с использованием указателей. Адресе сохранненого на ней объекта остаются постоянными, в общем все как на обычном RAM. Ф-ции работы с глобальной памятью: `cudaMalloc`, `cudaFree`, `cudaMemcpy` и `cudaMemset`.
- Большой размер (это она указана в спецификации видеокарты - GDDR). Например, у меня 2GB.
- Самый медленный доступ к памяти (не считая доступа к RAM). То есть, если thread обращается к глобальной памяти, то это самое медленное, что может случиться.
- Ну нее есть свой cache (L2) так, что доступ может быть чуть быстрее (размер ~2MB).

### Local memory (DRAM)
Что-то вроде back up памяти. Память с медленным доступом (как у global), небольшого размера. Расположена на DRAM. Используется при переполнении регистров. Например, мы на одном thread создали большой объект и привысили размеры доступных регистров (которые очень быстрые). Тогда коспилятор автоматически будет создавать локальные переменные (локальные для данного thread) на локальной памяти.     
У локальной памяти также есть адреса, а у регистрво - нет. Поэтому массивы с некостанными индексами сохраняются на локальной памяти (видимо, чтобы можно было выделить достаточно места на run-time и использовать адресную арифметику).
- Scope for local memory is per thread (локальный доступ).
- Имеет 2 уровня cache: L1 (быстрее), а затем L2 (медленнее)

### Constant memory (DRAM)
Специальный кусок главной памяти, где хранятся константные объекты. CPU имеет доступ и может записывать туда данные. Все threads имееют read-only доступ. Тк доступ к этой памяти очень быстрый и его имеют все threads, то сюда обычно записываются данные нужные для частых вычислений, например, матрицы проекций или трасформаций и тп, что рендеринг на theeads проходил быстрее.
- Очень быстрый доступ к этой памяти (на укровне скорости доступа к регистрам - оч быстро)
- Аналогично константной памяти в С/С++ - все объекты записываются сюда единоразово, и любые обращения к ним делаются по тому же адресу.
- Все threads имеют к ней доступ.
- Имеет свои отдельный cache: L1 (быстрее), а затем L2 (медленнее). Это делает ее еще быстре.
 
### Texture memory (DRAM)
Специальный кусок главной памяти. У нее есть свои трюки для работы с текстурами, например, экстрополяция можду точкими. У CPU полынй доступ к ней (чтение и записть), у GPU - read-only доступ. 
- Имеет свой L1 cache.

### Shared memory (SM. each block)
У каждого мулитипроцессор своя. Память, к которой имеет доступ только threads из этого блока.
- Очень быстрый доступ к этой памяти (на укровне скорости доступа к регистрам - оч быстро). В x100 и более раз быстрее, чем доступ к глобальной памяти. Таким образом часто оптимизация CUDA кода - это перенос объектов из global в shared memory.
- Это память малого размера (десятки KB).
На самом деле физически это часть L1 cache глобальной памяти. Над содержимым cache и его распределением программист не имеет контроля. Однако над частью выделенной для shared памяти, программист несет полный контроль, работая с ней как с обычно памятью. 

#### Мануальное распределение памяти
Для того, чтобы мануально выделить часть памяти с L1 cache для shared memory, используется ф-ция `cudaFuncSetCacheConfig(kernelName, enum cudaFuncCache)`, которая вызывается из Host (поэтому и явно указывается имея kernel). `cudaFuncCache` принимает следующие значения:
- `cudaFuncCachePreferNone` 0 - соответвует default распределению L1 и shared памяти (вроде это 16k to 48k, соответвенно). 
- `cudaFuncCachePreferShared` 1 - соответвует распределению L1 и shared памяти: 16k to 48k, соответвенно*.
- `cudaFuncCachePreferL1` 2 - соответвует распределению L1 и shared памяти: 48k to 16k, соответвенно*.
- `cudaFuncCachePreferEqual` 3 - соответвует распределению L1 и shared памяти: 32k to 32k, соответвенно*.
* Возмозно числа другие, но предпочтение остается.      
 
Если программисты отдал предпочтение L1 cache, но программа при этом требует больше shared memory, чем ей предоставленно, то компилятор сам праспределит L1 и shared память, чтобы предоставить достаточно shared memory (игнорируя запрос программиста).     
  
#### Объявление переменной
Объеявление shared переменно происходит на kernel. Можно выделить статически и динамически.
##### Static 
Память выделяется статически. Это значит, что все должно быть выделено at compile-time. Те мы можем выделить простые переменные, или например массив известного (at compile-time) размера.
```cpp
__global__ void SomeKernel()
{
 __shared__ int i;
 __shared__ float f_arrayp[100];
}
```
##### Dynamic
Динамическое выделение означает, что мы можем выделить память at tun-time. Нужно сделать 2 вещи.    
1. Объявление. Создаем массив с пустыми скобками (те не указываем ничего про размер внутри тела kernel. Размер выделяемой памяти  указывается при вызове kernel.
  ```cpp
  __global__ void SomeKernel()
  {
  extern __shared__ char sharebuffer[]; //обрати внимание на пустые скобки
  }
  ```
 2. Вызов kernel. Размер выделяемой памяти (в байтах) указывается 3м параметром при вызове. Ниже мы выделили 20байт и они будет автоматически выделены по указателю на sharebuffer (те мы выделили 20 байтовый массив, в нашем случаии тк это char, то это 20 элементный массив типа char).
  ```cpp
  SomeKernel<<<10, 512, 20>>>();
  ```
  Если мы хотим выделить, например, 2 массива, то тут - беда: CUDA позваляет выделить только один. Можно выделить один массив размера на 2 (или сколько нам надо), а потом руками его разделить,
   ```cpp
  __global__ void SomeKernel()
  {
  extern __shared__ char bothBuffers[]; //обрати внимание на пустые скобки
  char* firstArray = &bothBuffers[0]; //элеметны с 1го байта по 12й байты - первый массив
  float* secondArray = (float*)&bothBuffers[12]; //элеметны с 12го байта - второй массив
  }
  ```

#### Race condtion
Может случиться ситуация, когда threads должны действовать скоординированно (особенно, когда действие одной thread влечет изменения в kernel). Для этого нужна синхронизурующая ф-ция `__syncthreads()`. Дойдя до нее thread остановится и будет ждать все другие параллальные threads этого блока. Когда они все дойдут до нее, их дейсвтвие продолжится.

### Registers (SM, each thread)
Непольшой участок неиндексируемой памяти. Resister scope is per thread. Сюда kernel сохраняет локальные переменные и тд. Если для данного thread не хватит registers, то переменные будут сохораняться в меделнную local memory. Не имеют адресов, поэтому не подходят для динамически индексированных массивов. 
- Самый быстрый доступ среди всех GPU паметей.
- Register scope is per thread.
- В GPU тысячи регистров, но они очень маленькие.
- Если kernel не использует много памяти, то вручную ограничив кол-во регистров per thread может значительно ускорить программу, тк многи регисты будут простаивать. При этом NVCC определяет какое кол-во блоков зайдествовать параллельно, основываясь на доступных регистрах. Кол-во доступных регистров главыный лимит кол-ва параллельных блоков.     

### Caches (both DRAM and SM)
<img  src = "https://github.com/PlohoyParen/Cpp_doc/blob/master/Documents/images/GPU_cache.jpg" alt = "GPU_cache" width = 700>
Часто используемы данные кэшируются сначала на L1. Если они какое-то время не используются, то GPU сам переносит их в L2 кэш. Если же данные не используются и там, то они удаляются из кэша. Все это делает GPU, у программиста нет над этим контроля. 


## Random number generator on GPU
https://nidclip.wordpress.com/2014/04/02/cuda-random-number-generation/


-----

### Error: "Display Driver has Stopped Working and has Recovered"
Проблема связана с тем, что widows автоматически перезапускает драйвер GPU, если видеокарта не отвечает более 2сек. Те если мы выполняем программу на GPU дольше 2сек, то widows будет думать, что что-то посшло не так и перезагрузит драйвер. Нужно поменять найстройки регистров. Подробнее [тут](https://www.youtube.com/watch?v=8NtHDkUoN98&list=PLKK11Ligqititws0ZOoGk3SW-TZCar4dK&index=3). Обычно программа просто дропает выполнение kernel, а CPU продолжает работу доводя программу до завершения, так что проблема может быть не очевидна.
