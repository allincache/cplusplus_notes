
**Question**:

Explain the usage of the `friend` keyword in C++ with code examples.<br />

**Answer**:

In C++, the `friend` keyword is used to declare a class or function as a friend of another class. Friends can access the private and protected members of a class, thus breaking the encapsulation of the class. While `friend` can provide some convenience, overusing it can lead to breaking encapsulation and making the code harder to understand and maintain.

Here are a few examples of how the `friend` keyword is used:

### 1. Friend Class

One class can declare another class as its friend. This allows the friend class to access all the private and protected members of the original class.


```cpp
class MyClass {
private:
    int secret;

public:
    MyClass(int value) : secret(value) {}

    // Declare AnotherClass as a friend class
    friend class AnotherClass;
};

class AnotherClass {
public:
    void printSecret(const MyClass& obj) {
        // Since AnotherClass is a friend of MyClass, it can access secret
        std::cout << "Secret value: " << obj.secret << std::endl;
    }
};

int main() {
    MyClass obj(42);
    AnotherClass ac;
    ac.printSecret(obj);  // Output: "Secret value: 42"
    return 0;
}
```
### 2. Friend Function

A class can declare one or more non-member functions as its friends. These functions can access the private and protected members of the class.


```cpp
class MyClass {
private:
    int secret;

public:
    MyClass(int value) : secret(value) {}

    // Declare printSecret as a friend function
    friend void printSecret(const MyClass& obj);
};

// printSecret is a friend function of MyClass and can access secret
void printSecret(const MyClass& obj) {
    std::cout << "Secret value: " << obj.secret << std::endl;
}

int main() {
    MyClass obj(42);
    printSecret(obj);  // Output: "Secret value: 42"
    return 0;
}
```
### 3. Friend Function as a Member of Another Class

Although uncommon, a friend function can also be a member function of another class. In this case, the function can only access the private and protected members of the original class when called as a member function of that other class.


```cpp
class MyClass {
private:
    int secret;

public:
    MyClass(int value) : secret(value) {}

    // Declare AnotherClass's member function printSecret as a friend
    friend void AnotherClass::printSecret(const MyClass& obj);
};

class AnotherClass {
public:
    void printSecret(const MyClass& obj) {
        // Since printSecret is a friend of MyClass, it can access secret
        std::cout << "Secret value: " << obj.secret << std::endl;
    }
};

int main() {
    MyClass obj(42);
    AnotherClass ac;
    ac.printSecret(obj);  // Output: "Secret value: 42"
    return 0;
}
```
Note: While the `friend` keyword provides the ability to break encapsulation, it should be used carefully to avoid breaking the encapsulation of objects and adding complexity to the code.