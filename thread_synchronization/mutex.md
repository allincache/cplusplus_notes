
In C++, `std::mutex` is a mutual exclusion lock used to synchronize access to shared resources. When multiple threads attempt to access a shared resource simultaneously, `std::mutex` ensures that only one thread can access the resource at a time, thereby preventing data races and inconsistencies.

Here's a simple example demonstrating how to use `std::mutex` to synchronize access to a shared counter:


```cpp
#include <iostream>
#include <thread>
#include <mutex>
#include <vector>

std::mutex mtx; // Global mutex
int counter = 0; // Shared counter

void increment_counter(int id) {
    for (int i = 0; i < 10000; ++i) {
        std::lock_guard<std::mutex> lock(mtx); // Automatically manage lock acquisition and release with lock_guard
        ++counter;
    }
}

int main() {
    const int num_threads = 5;
    std::vector<std::thread> threads;

    // Create multiple threads, each incrementing the counter
    for (int i = 0; i < num_threads; ++i) {
        threads.push_back(std::thread(increment_counter, i));
    }

    // Wait for all threads to complete
    for (auto& t : threads) {
        t.join();
    }

    std::cout << "Final counter value: " << counter << std::endl;

    return 0;
}
```
Thanks to the use of a mutex to synchronize access to the counter, the final counter value should be as expected (in this case, 50000, as there are 5 threads, each incrementing the counter 10000 times). Without the mutex, multiple threads could modify the counter simultaneously, leading to data races and incorrect results.


**Approach for lock/unlock**

```cpp
void increment_counter(int id, int iterations) {  
    for (int i = 0; i < iterations; ++i) {  
        mtx.lock(); // Manually lock the mutex  
        ++counter;  
        mtx.unlock(); // Manually unlock the mutex  
    }  
}
```

std::lock_guard is a class template in the C++ Standard Library, designed to manage the locking and unlocking of mutexes such as std::mutex. The primary purpose of this class is to simplify mutex management, following the RAII (Resource Acquisition Is Initialization) principle to ensure that the mutex is properly unlocked after use, even in exceptional cases.

When a std::lock_guard object is created, it attempts to lock the given mutex. When the lifecycle of the std::lock_guard object ends (typically when it leaves its scope), its destructor automatically unlocks the mutex. This automatic management mechanism significantly reduces the risk of deadlocks and other multi-threading issues caused by forgotten unlocking or failure to unlock in exceptional situations.

**lock_guard Implementation Reference**


```cpp
template<typename Mutex>
class lock_guard {
private:
    Mutex& m;

    explicit lock_guard(const lock_guard&);
    lock_guard& operator=(const lock_guard&);

public:
    explicit lock_guard(Mutex& m_) : m(m_) {
        m.lock();
    }

    lock_guard(Mutex& m_, std::adopt_lock_t) : m(m_) {}

    ~lock_guard() {
        m.unlock();
    }
};
```