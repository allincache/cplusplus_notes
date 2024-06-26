`std::enable_shared_from_this` is a template class in the C++11 standard library that enables an object to create a `std::shared_ptr` smart pointer to itself. This is particularly useful when you need to return a `shared_ptr` to the current object from one of its member functions.

To use `std::enable_shared_from_this`, your class needs to publicly inherit from it. Then, you can use the member function `shared_from_this()` to obtain a `shared_ptr` pointing to the current object.

Here's a simple example:

```cpp
#include <iostream>
#include <memory>

class MyClass : public std::enable_shared_from_this<MyClass> {
public:
    std::shared_ptr<MyClass> getSharedPtr() {
        return shared_from_this();
    }

    void show() {
        std::cout << "MyClass instance" << std::endl;
    }
};

int main() {
    std::shared_ptr<MyClass> obj = std::make_shared<MyClass>();
    std::shared_ptr<MyClass> obj2 = obj->getSharedPtr();

    obj2->show();  // Outputs "MyClass instance"

    return 0;
}
```

In this example, `MyClass` inherits from `std::enable_shared_from_this<MyClass>`. The `getSharedPtr` member function uses `shared_from_this()` to obtain and return a `shared_ptr` pointing to the current object.

A few important points to note:

1. **Object Must Be Managed by `std::shared_ptr`**: If you attempt to call `shared_from_this()` on an object that is not managed by a `std::shared_ptr`, the program will throw a `std::bad_weak_ptr` exception.
2. **Do Not Call `shared_from_this()` in Constructors**: During constructor execution, the `std::shared_ptr` has not fully taken ownership of the object, so calling `shared_from_this()` leads to undefined behavior.
3. **Avoid Calling in Destructors**: While calling `shared_from_this()` in a destructor won't throw an exception, it's generally meaningless to do so since the object is about to be destroyed.

`std::enable_shared_from_this` is particularly useful when implementing certain design patterns (such as the Observer pattern) or managing object lifetimes in multithreaded environments.

**Example of the "Subscriber-Publisher" Pattern**

**Using enable_shared_from_this**

```cpp
#include <iostream>  
#include <memory>  
#include <vector>  
  
class EventListener;  
  
class EventSource {  
public:  
    void registerListener(const std::shared_ptr<EventListener>& listener) {  
        listeners_.push_back(listener);  
    }  
  
    void notifyListeners() {  
        for (const auto& listener : listeners_) {  
            listener->onEvent();  
        }  
    }  
  
private:  
    std::vector<std::shared_ptr<EventListener>> listeners_;  
};  
  
class EventListener : public std::enable_shared_from_this<EventListener> {  
public:  
    void registerWithSource(EventSource& source) {  
        source.registerListener(shared_from_this());  
    }  
  
    void onEvent() {  
        std::cout << "Event received in listener!" << std::endl;  
    }  
};  
  
int main() {  
    auto listener = std::make_shared<EventListener>();  
    EventSource source;  
    listener->registerWithSource(source);  
    source.notifyListeners(); // Calls onEvent() in the listener  
    return 0;  
}
```

**Without using enable_shared_from_this**

```cpp
#include <iostream>    
#include <memory>    
#include <vector>    
    
class EventListener;    
    
class EventSource {    
public:    
    void registerListener(const std::shared_ptr<EventListener>& listener) {    
        listeners_.push_back(listener);    
    }    
    
    void notifyListeners() {    
        for (const auto& listener : listeners_) {    
            listener->onEvent();    
        }    
    }    
    
private:    
    std::vector<std::shared_ptr<EventListener>> listeners_;    
};    
    
class EventListener {    
public:    
    // Now requires an external shared_ptr  
    void registerWithSource(EventSource& source, const std::shared_ptr<EventListener>& this_ptr) {    
        source.registerListener(this_ptr);    
    }    
    
    void onEvent() {    
        std::cout << "Event received in listener!" << std::endl;    
    }    
};    
    
int main() {    
    auto listener = std::make_shared<EventListener>();    
    EventSource source;    
      
    // During registration, explicitly pass the listener's shared_ptr  
    listener->registerWithSource(source, listener);    
    source.notifyListeners(); // Calls onEvent() in the listener  
    return 0;    
}
```


</br>

An illustrative example of a simplified implementation of `std::enable_shared_from_this`:

```cpp
#include <memory>
#include <stdexcept>

template<typename T>
class enable_shared_from_this {
protected:
    // Constructors and destructor are set to protected since this class is designed for inheritance.
    enable_shared_from_this() = default;
    ~enable_shared_from_this() = default;

    // Returns a shared_ptr pointing to the current object.
    std::shared_ptr<T> shared_from_this() {
        if (!weak_this_) {
            throw std::bad_weak_ptr(); // Indicates the object is not managed by a shared_ptr.
        }
        return weak_this_.lock(); // Attempts to lock the weak_ptr, returning an empty shared_ptr if expired.
    }

    // Allows derived classes to initialize weak_this_.
    void init_weak_this(const std::shared_ptr<T>& ptr) {
        weak_this_ = ptr;
    }

private:
    std::weak_ptr<T> weak_this_; // Holds a non-owning reference to the object.
};

// Example usage
class MyClass : public enable_shared_from_this<MyClass> {
public:
    MyClass() {
        // Do not call shared_from_this() in the constructor, as the object might not be fully constructed.
    }

    void SomeMethod() {
        auto self = shared_from_this(); // Now it's safe to obtain a shared_ptr.
        // ... Perform operations using self ...
    }
};

// Factory function to create a shared_ptr and initialize weak_this_.
template<typename T, typename... Args>
std::shared_ptr<T> make_shared_enable_shared_from_this(Args&&... args) {
    auto ptr = std::make_shared<T>(std::forward<Args>(args)...);
    ptr->init_weak_this(ptr); // Initializes the weak_ptr to point to the shared_ptr.
    return ptr;
}

int main() {
    auto obj = make_shared_enable_shared_from_this<MyClass>();
    obj->SomeMethod(); // In this method, shared_from_this() can be safely called.
    return 0;
}
```

Please note that this implementation is simplified for educational purposes and does not fully adhere to the implementation in the standard library. In the actual standard library, `std::enable_shared_from_this` interacts with `std::shared_ptr` in more complex ways, including handling race conditions in multithreaded environments and ensuring proper object lifetime management.