# ConcurrencyBootcamp

1. [Single-Threaded Code](#SingleThreadingCode)
2. [Atomic Operations](#AtomicOperations)
3. [Multi-Threading](#MultiThreading)
4. [Understanding Thread Safety](#ThreadSafety)
5. [Data Race](#DataRace)



# Single-Threaded Code:
What is it?

In Swift, when we say code is "single-threaded," it means that the code runs one instruction at a time, one after the other.
Example:

Consider a program that asks the user for their given name and family name in sequence. It waits for the user to input each name before proceeding.
swift
```swift
func getFullName() {
    let givenName = getGivenName()
    let familyName = getFamilyName()
    print("\(givenName) \(familyName)")
}

func getGivenName() -> String {
    print("What's your given name?")
    return readLine() ?? "--"
}

func getFamilyName() -> String {
    print("What's your family name?")
    return readLine() ?? "--"
}

getFullName()


```
### How does it work?

The program executes line by line, function by function, and waits for user input during the readLine() calls.
### Visualizing it:

If we visualize the program's flow, it looks like a straight line, moving from one task to the next.


<img width="788" alt="Screenshot 2024-02-04 at 11 47 03 PM" src="https://github.com/abdahad1996/ConcurrencyBootcamp/assets/28492677/d2f28bcc-4b3b-462f-9458-66c5516ddd5c">

## Concurrency:

### Why is it needed?

While single-threaded code is easy to reason about, it has limitations. For example, if one task takes a long time, the entire program might freeze.

### What is concurrency?

Concurrency is the idea that even though code is single-threaded, it can work on multiple tasks at the same time, giving the illusion of parallelism.

### Visualizing Concurrency:

Imagine the CPU briefly switching between tasks, so it works on multiple things over time. It doesn't mean tasks are happening at the exact same time, but they share CPU time.

<img width="809" alt="Screenshot 2024-02-04 at 11 58 34 PM" src="https://github.com/abdahad1996/ConcurrencyBootcamp/assets/28492677/38ee60d9-b09b-45ee-a6fa-61f096402bcd">


### Atomic Operations
Operations that run in this manner where they must run from start to finish without interruption are sometimes called atomic operations.


### Why multiple threads or CPUs?

If one task (like another app or a slow operation) takes up all the CPU time, our app might freeze. Multiple threads or CPUs allow different tasks to run simultaneously, ensuring responsiveness.
In summary, single-threaded code runs one line at a time, while concurrency allows the illusion of doing multiple things at once, even though the CPU is switching between tasks over time. Multiple threads or CPUs help avoid freezing when one task takes too long.

# Multi-Threading:

#### In summary, thread safety is about making sure multiple threads can work together without causing unexpected problems. Data races are issues that occur when multiple threads try to access shared data simultaneously. Synchronization is a technique to prevent data races and maintain program stability.

A thread is like a worker that can do tasks in a computer program.
Think of your application as a team of workers (threads) doing different jobs.
For example, one thread could handle user input, another could work on UI rendering, and so on.
Even if you have only one computer processor (CPU), you can still have multiple threads. The CPU takes turns giving each thread a bit of time to work. This is called concurrency.

### Difference Between Concurrency and Parallelism:

Concurrency means working on multiple tasks at the same time, but not necessarily simultaneously. Threads take turns working.
Parallelism means doing multiple things truly at the same time. This happens when you have multiple CPU cores. Each core can handle a different thread simultaneously.

### Why Multi-Threading is Useful:

As your application grows, there might be tasks that take a long time. Multi-threading allows your program to perform these tasks without blocking the user interface or other important operations.

### Common Example: Network Calls:

Consider making a network call when a user taps a button in an app.
If done on the main thread (UI thread), the app would freeze until the network call completes.
Multi-threading allows the network call to happen on a separate thread, so the main thread can still handle UI interactions and animations.


# Understanding Thread Safety:

Imagine you're building a chat application where multiple users can send and receive messages in real-time. Each message is stored in an array for display in the chat interface.
```swift
class ChatManager {
    var messages: [String] = []

    func receiveMessage(message: String) {
        // Simulating message received from the network
        DispatchQueue.global().async {
            // Potential issue: Multiple threads trying to write to 'messages' array
            self.messages.append(message)
            self.updateUI() // Update chat interface
        }
    }

    func updateUI() {
        // Code to update the user interface with the latest messages
        // ...
    }
}

```
 If two or more threads try to add messages to the messages array at the same time, it can lead to a data race.

## Data Race

#### What is a Data Race?
#### Definition:

A data race happens when multiple threads try to access the same data (like an array) simultaneously, and at least one of them is trying to write (modify) the data.
#### Issue:

If multiple threads write to the array at the same time, it can lead to inconsistent data and even memory corruption. This is a problem in multithreading.
