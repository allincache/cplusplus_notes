**Question**:

Explain the usage of the `private` keyword in C++ with code examples.<br />


**Answer**:

In C++, the `private` keyword is an access specifier that determines the accessibility of class members, including data members and member functions. When a class member is declared as `private`, it can only be accessed from within the class itself, specifically through the member functions (including constructors and destructors) of that class. Outside of the class (including other classes), `private` members cannot be accessed directly.

### Usage of Private Members:

1. **Data Encapsulation**: `private` data members are the basis for data encapsulation in a class. By hiding these data members and only allowing access through public member functions, we can ensure the integrity and security of the data.

2. **Preventing Direct Access**: By declaring data members as `private`, we prevent users of the class from directly accessing or modifying these data members, thus avoiding potential errors and uncertainty.

3. **Providing an Interface**: Through public member functions (also known as accessor functions), we can provide controlled access to `private` data members. These functions can perform necessary validation and error checking to ensure data integrity and consistency.

### Example Code:

Here's a simple example demonstrating the use of the `private` keyword to declare private members in a class:

```cpp
#include <iostream>
#include <string>

class Person {
private:
    std::string name; // Private data member
    int age; // Private data member

public:
    // Constructor to initialize private members
    Person(const std::string& newName, int newAge) 
        : name(newName), age(newAge) {}

    // Public member function to get the value of a private member
    std::string getName() const {
        return name;
    }

    int getAge() const {
        return age;
    }

    // Public member function to set the value of a private member (optional)
    void setName(const std::string& newName) {
        name = newName;
    }

    void setAge(int newAge) {
        // Can add some validation logic, e.g., to ensure age is reasonable
        if (newAge >= 0) {
            age = newAge;
        } else {
            std::cerr << "Invalid age!" << std::endl;
        }
    }

    // Other public member functions...
};

int main() {
    Person person("Alice", 30);
    std::cout << "Name: " << person.getName() << ", Age: " << person.getAge() << std::endl;

    // The following code would cause a compilation error because name and age are private
    // std::cout << "Name (direct access): " << person.name << std::endl; // Error
    // std::cout << "Age (direct access): " << person.age << std::endl; // Error

    // But we can set and get their values through public member functions
    person.setName("Bob");
    person.setAge(35);
    std::cout << "Updated Name: " << person.getName() << ", Updated Age: " << person.getAge() << std::endl;

    return 0;
}
```
In the above example, the `Person` class has two private data members: `name` and `age`. These members can only be accessed and modified through the public member functions of the class, such as `getName()`, `getAge()`, `setName()`, and `setAge()`. Attempting to directly access private members (e.g., `person.name` or `person.age`) would result in a compilation error.
