
**Question**:

Explain the usage of the `static_cast` keyword in C++ with code examples.

<br />

**Answer**:

`static_cast` is a type casting operator in C++ that provides compile-time type checking and conversion. It is typically used for conversions between basic data types, upcasts (or implicit conversions) in class hierarchies, conversions from `void*` to other pointer types, and constness conversions that `const_cast` cannot handle.

Here are some common uses of `static_cast`:

1. **Conversions between basic data types**:


```cpp
int a = 10;
double b = static_cast<double>(a); // Convert int to double
```
2. **Upcasts (also known as implicit conversions) in class hierarchies**:

If `Derived` is derived from `Base`, then a conversion from `Derived*` to `Base*` is safe and can be done using `static_cast`. However, such conversions are usually implicit, so you might not use `static_cast` explicitly.


```cpp
class Base {};
class Derived : public Base {};

Derived d;
Base* b = static_cast<Base*>(&d); // Convert from Derived* to Base*
```
3. **Downcasts (use with caution)**:

Downcasts (from base to derived) are unsafe at runtime because they can lead to undefined behavior if the pointer or reference does not actually point to an object of that derived type. However, if you are sure the conversion is safe, you can use `static_cast`. However, a better practice is to use `dynamic_cast`, which checks the validity of the conversion at runtime.


```cpp
Derived* d2 = static_cast<Derived*>(b); // Convert from Base* to Derived* (use with caution)
```
Note: This conversion is dangerous unless you are sure that `b` actually points to a `Derived` object.
4. **Conversions from `void*` to other pointer types**:


```cpp
void* ptr = &a;
int* ptr2 = static_cast<int*>(ptr); // Convert void* to int*
```
5. **Constness conversions**:

`static_cast` can be used to add or remove the constness of pointers or references, but only if such a conversion is logically safe. However, for non-pointer types, `const_cast` is typically used to perform constness conversions.


```cpp
const int c = 10;
int* ptr3 = const_cast<int*>(&c); // Error: Should use const_cast, not static_cast
int& ref = const_cast<int&>(c);  // This is also error-prone

// But for pointers, static_cast can be used to add constness
const int* ptr4 = static_cast<const int*>(&a);
```
However, please note that the use of `const_cast` should be done with extreme caution as it can violate constness and lead to undefined behavior.