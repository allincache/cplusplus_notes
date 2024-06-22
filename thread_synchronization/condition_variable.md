In C++, `std::condition_variable` is a synchronization primitive often used to block one or more threads until a certain condition is met or until notified. It is frequently paired with `std::mutex` to achieve synchronization between threads.

Here's a simple example demonstrating how to use `std::condition_variable` to implement a producer-consumer model:


```cpp
#include <iostream>
#include <thread>
#include <mutex>
#include <condition_variable>
#include <queue>

std::queue<int> data_queue;
std::mutex mtx;
std::condition_variable cond_var;
bool stop = false;

void producer(int id) {
    while (true) {
        std::this_thread::sleep_for(std::chrono::milliseconds(1000)); // Simulate production time
        std::lock_guard<std::mutex> lock(mtx);
        data_queue.push(rand() % 100); // Produce data and enqueue
        std::cout << "Producer " << id << " produced " << data_queue.back() << std::endl;
        cond_var.notify_one(); // Notify waiting consumer thread
        if (stop) break; // Exit loop if stop signal is received
    }
}

void consumer() {
    while (true) {
        std::unique_lock<std::mutex> lock(mtx);
        cond_var.wait(lock, [] { return !data_queue.empty() || stop; }); // Wait until queue is non-empty or stop signal is received
        if (stop && data_queue.empty()) break; // Exit loop if stop signal is received and queue is empty
        int data = data_queue.front();
        data_queue.pop();
        lock.unlock(); // Unlock to allow producers to produce data
        std::cout << "Consumer consumed " << data << std::endl;
        std::this_thread::sleep_for(std::chrono::milliseconds(1500)); // Simulate consumption time
    }
}

int main() {
    std::thread producer_thread1(producer, 1);
    std::thread producer_thread2(producer, 2);
    std::thread consumer_thread(consumer);

    std::this_thread::sleep_for(std::chrono::seconds(5)); // Let producer and consumer threads run for a while
    {
        std::lock_guard<std::mutex> lock(mtx);
        stop = true; // Set stop signal
    }
    cond_var.notify_all(); // Notify all waiting threads

    producer_thread1.join();
    producer_thread2.join();
    consumer_thread.join();
    return 0;
}
```
In this example, there are two producer threads and one consumer thread. The producer threads produce data (in this case, generate a random number) and enqueue it, then notify the waiting consumer thread. The consumer thread waits until the queue is non-empty, dequeues the data, and consumes it (here, simulating the time it takes to consume the data). When the main thread sets the stop signal, all threads will stop running. It's important to note that when using condition variables, they must always be used together with a mutex to ensure thread-safe access to shared data.