
**Question**:

Explain the usage of the `dynamic_cast` keyword in C++ with code examples.

<br />

**Answer**:

`dynamic_cast` is a type casting operator in C++ that is primarily used for safe downcasting of pointers and references between classes in an inheritance hierarchy. It is a runtime check that performs the cast only if the object being cast is actually of the target type or a type derived from the target type.

Here are some key points about `dynamic_cast`:

1. **Usage**:
   - It is used to cast pointers and references to classes with virtual functions (i.e., classes with polymorphic behavior).
   - It is often used to downcast from a base class pointer/reference to a derived class pointer/reference.

2. **Types of Casts**:
   - `dynamic_cast<D*>(b)`: Casts a base class pointer `b` to a derived class pointer `D*`. If the cast is not valid (i.e., `b` does not point to an object of type `D` or a type derived from `D`), the result is a null pointer.
   - `dynamic_cast<D&>(b)`: Casts a base class reference `b` to a derived class reference `D&`. If the cast is not valid, a `std::bad_cast` exception is thrown.

3. **Requirements**:
   - The base class must have at least one virtual function (even if it's the destructor) to enable runtime type information (RTTI).
   - The cast must be between classes in the same inheritance hierarchy.

4. **Example**:


```cpp
#include <iostream>

class Base {
public:
    virtual ~Base() {} // Virtual destructor to enable RTTI
};

class Derived : public Base {};

int main() {
    Base* basePtr = new Base();
    Derived* derivedPtr = dynamic_cast<Derived*>(basePtr);
    if (derivedPtr == nullptr) {
        std::cout << "Cannot cast Base* to Derived*." << std::endl;
    }

    basePtr = new Derived();
    derivedPtr = dynamic_cast<Derived*>(basePtr);
    if (derivedPtr != nullptr) {
        std::cout << "Successfully cast Base* to Derived*." << std::endl;
    }

    delete basePtr; // Make sure to delete the allocated memory
    return 0;
}
```
In this example, the first `dynamic_cast` fails because `basePtr` points to an object of type `Base`, not `Derived`. The second `dynamic_cast` succeeds because `basePtr` now points to an object of type `Derived`.

5. **Performance**:
   - `dynamic_cast` is slower than `static_cast` because it involves runtime type checks. Therefore, it should be used sparingly, especially in performance-critical code.

Remember that `dynamic_cast` is a safe alternative to `static_cast` when performing downcasts in inheritance hierarchies, but it should only be used when the safety check is necessary.