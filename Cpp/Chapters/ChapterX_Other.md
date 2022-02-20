# Chapter 10 Other
## Использование слова static
1. Persistence: it remains in memory until the end of the program.
2. **(Scope)** File scope: it can be seen only withing a file where it's defined. Visibility: if it is defined within a function/block, it's scope is limited to the function/block. It cannot be accessed outside of the function/block.
3. **(Class)** Static members exist as members of the class rather than as an instance in each object of the class. So, this keyword is not available in a static member function. Such functions may access only static data members. **There is only a single instance of each static data member for the entire class**. *A static data member : class variable. A non-static data member : instance variable*. Static member function: it can only access static member data, or other static member functions while non-static member functions can access all data members of the class: static and non-static.
