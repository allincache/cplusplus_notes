
**Question**:

Explain the usage of the `virtual` keyword in C++ with code examples.

<br />

**Answer**:

In C++, the `virtual` keyword is primarily used to enable polymorphism, especially in the context of base classes and derived classes. The `virtual` keyword can modify member functions (including destructors) and inherited base class functions. Here are the main uses of the `virtual` keyword:

1. **Virtual Functions**:
   When a member function is declared `virtual` in a base class, it can be overridden in a derived class. This means that when a base class pointer or reference is used to point to a derived class object and the function is called, the version in the derived class will be called, not the one in the base class. This is key to achieving runtime polymorphism.

   ```cpp
   class Base {
   public:
       virtual void foo() {
           std::cout << "Base::foo()" << std::endl;
       }
   };

   class Derived : public Base {
   public:
       void foo() override { // Note: 'override' is introduced in C++11 to explicitly specify an override
           std::cout << "Derived::foo()" << std::endl;
       }
   };

   int main() {
       Base* ptr = new Derived();
       ptr->foo(); // Outputs "Derived::foo()"
       delete ptr;
       return 0;
   }
   ```

2. **Pure Virtual Functions**:
   If a function is declared `virtual` in a base class and is not defined (only declared), it is a pure virtual function. A class that contains a pure virtual function is called an abstract class and cannot be instantiated. Derived classes must provide a definition for the pure virtual function, unless the derived class itself is also abstract.

   ```cpp
   class AbstractBase {
   public:
       virtual void bar() = 0; // Pure virtual function
   };

   class Derived : public AbstractBase {
   public:
       void bar() override {
           std::cout << "Derived::bar()" << std::endl;
       }
   };

   // AbstractBase* ptr = new AbstractBase(); // Error: Cannot instantiate an abstract class
   ```

3. **Virtual Destructors**:
   When a base class has one or more virtual functions, it is generally recommended to also declare the base class destructor as `virtual`. This is because when a base class pointer is used to delete a derived class object, if the destructor is not virtual, only the base class destructor will be called, not the derived class destructor, which can lead to resource leaks or other issues.

   ```cpp
   class Base {
   public:
       virtual ~Base() {
           // Implementation of the base class destructor
       }
   };

   class Derived : public Base {
   public:
       ~Derived() {
           // Implementation of the derived class destructor
       }
   };

   int main() {
       Base* ptr = new Derived();
       delete ptr; // Calls the destructor of Derived, then Base
       return 0;
   }
   ```

Note: Although the `virtual` keyword can modify member functions, it cannot modify data members or static member functions. Additionally, using the `virtual` keyword typically adds some runtime overhead due to maintaining a pointer to the virtual function table (vptr) and additional memory for the virtual function table (vtable). Therefore, avoid unnecessary use of the `virtual` keyword when polymorphism is not required.