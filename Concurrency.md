

The `async` and `await` keywords are fundamental to asynchronous programming with tasks. The `async` keyword marks a method as asynchronous, indicating that it may contain asynchronous operations. The `await` keyword is used to suspend the execution of an asynchronous method until the asynchronous operation completes.

Asynchronous methods enable non-blocking I/O operations, allowing the main thread to remain responsive while asynchronous operations are in progress. This improves the overall responsiveness of the application.

Exception handling in asynchronous code is handled using `try`/`catch` blocks. Exceptions thrown within asynchronous methods are propagated to the caller, where they can be caught and handled appropriately.

`using System;`  
`using System.Threading.Tasks;`  
  
`class Program`  
`{`  
    `static async Task Main()`  
    `{`  
        `Task myTask1 = Task.Run(async () =>`  
        `{`  
            `Console.WriteLine("Chef 1 is preparing Dish 1");`  
            `await Task.Delay(10000); // Chef 1 takes 10 seconds to prepare Dish 1`  
            `Console.WriteLine("Dish 1 is ready!");`  
        `});`  
  
        `Task myTask2 = Task.Run(async () =>`  
        `{`  
            `Console.WriteLine("Chef 2 is preparing Dish 2");`  
            `await Task.Delay(1000); // Chef 2 takes 1 second to prepare Dish 2`  
            `Console.WriteLine("Dish 2 is ready!");`  
        `});`  
  
        `Task myTask3 = Task.Run(async () =>`  
        `{`  
            `Console.WriteLine("Chef 3 is preparing Dish 3");`  
            `await Task.Delay(1000); // Chef 3 takes 1 second to prepare Dish 3`  
            `Console.WriteLine("Dish 3 is ready!");`  
        `});`  
  
        `await Task.WhenAll(myTask1, myTask2, myTask3);`  
  
        `Console.WriteLine("Manager: All dishes are ready! Task completed!");`  
    `}`  
`}`

The Task Parallel Library (TPL) is a set of classes that simplifies parallel programming in .NET. It provides several features for managing tasks and coordinating their execution.

**Parallel.ForEach and Parallel.For**

`Parallel.ForEach` and `Parallel.For` are methods for executing a delegate in parallel for each element in a collection. `Parallel.ForEach` preserves the order of iteration, while `Parallel.For` does not guarantee order.


`using System;`  
`using System.Threading.Tasks;`  
  
`class Program`  
`{`  
    `static void Main()`  
    `{`  
        `Chef[] chefs = { new Chef("Alice"), new Chef("Bob"), new Chef("Charlie"), new Chef("David") };`  
  
        `// Parallel.ForEach example`  
        `Parallel.ForEach(chefs, chef =>`  
        `{`  
            `chef.Cook();`  
        `});`  
  
        `// Parallel.For example`  
        `Parallel.For(0, chefs.Length, i =>`  
        `{`  
            `chefs[i].Cook();`  
        `});`  
    `}`  
`}`  
  
`class Chef`  
`{`  
    `public string Name { get; }`  
  
    `public Chef(string name)`  
    `{`  
        `Name = name;`  
    `}`  
  
    `public void Cook()`  
    `{`  
        `Console.WriteLine($"{Name} is cooking...");`  
        `Task.Delay(1000).Wait(); // Simulating cooking time`  
        `Console.WriteLine($"{Name} finished cooking.");`  
    `}`  
`}`


## **Thread Safety**

**Importance of thread safety in concurrent programming**

In concurrent programming, when multiple threads can access and modify shared resources, ensuring thread safety is paramount to avoid data corruption. The `lock` statement is commonly used to synchronize access to shared data. In the following example, a `SharedResource` class uses a `lock` to safely increment a shared counter:

`using System;`  
`using System.Threading;`  
  
`class SharedResource`  
`{`  
    `private int counter = 0;`  
    `private object lockObject = new object();`  
  
    `public void IncrementCounter()`  
    `{`  
        `lock (lockObject)`  
        `{`  
            `counter++;`  
            `Console.WriteLine($"Counter: {counter}, Thread ID: {Thread.CurrentThread.ManagedThreadId}");`  
        `}`  
    `}`  
`}`  
  
`class Program`  
`{`  
    `static void Main()`  
    `{`  
        `SharedResource sharedResource = new SharedResource();`  
  
        `// Simulating multiple threads incrementing the counter`  
        `for (int i = 0; i < 5; i++)`  
        `{`  
            `new Thread(() => sharedResource.IncrementCounter()).Start();`  
        `}`  
  
        `Console.ReadLine();`  
    `}`  
`}`


## **Immutable types and their role**

Immutable types, whose state cannot be modified after creation, inherently provide thread safety. In this example, an `ImmutableData` class is created with an immutable property:

`using System;`  
  
`public class ImmutableData`  
`{`  
    `public int Value { get; }`  
  
    `public ImmutableData(int value)`  
    `{`  
        `Value = value;`  
    `}`  
`}`  
  
`class Program`  
`{`  
    `static void Main()`  
    `{`  
        `ImmutableData immutableData = new ImmutableData(42);`  
  
        `// The state of immutableData cannot be modified after creation`  
        `Console.WriteLine($"Immutable Data Value: {immutableData.Value}");`  
    `}`  
`}`


## `**ThreadLocal<T>` for per-thread data**

`ThreadLocal<T>` is a useful class for managing per-thread data without synchronization. In the following example, a `ThreadLocal<int>` is used to store a unique value for each thread:


`using System;`  
`using System.Threading;`  
  
`class Program`  
`{`  
    `static ThreadLocal<int> threadLocalValue = new ThreadLocal<int>(() => 0);`  
  
    `static void SetThreadLocalValue(int newValue)`  
    `{`  
        `threadLocalValue.Value = newValue;`  
    `}`  
  
    `static int GetThreadLocalValue()`  
    `{`  
        `return threadLocalValue.Value;`  
    `}`  
  
    `static void Main()`  
    `{`  
        `// Simulating multiple threads with unique per-thread values`  
        `for (int i = 1; i <= 3; i++)`  
        `{`  
            `new Thread(() =>`  
            `{`  
                `SetThreadLocalValue(i);`  
                `Console.WriteLine($"Thread ID: {Thread.CurrentThread.ManagedThreadId}, ThreadLocal Value: {GetThreadLocalValue()}");`  
            `}).Start();`  
        `}`  
  
        `Console.ReadLine();`  
    `}`  
`}`


# Promises
A Promise is an object representing the eventual completion or failure of an async operation. If a function returns a Promise, it doesn’t immediately return a value, but the Promise will once the async operation is complete.

`public void Main()`  
`{`  
  `// DownloadData is the async operation, and it returns a Promise`  
  `DownloadData("<some https url>")`  
    `.Then((data) => {`  
      `// This function gets called if DownloadData is successful`  
      `// the 'data' argument is the result of the download`  
    `})`  
    `.Catch(() => {`  
      `// This function gets called if DownloadData fails`  
      `// In that case, the above .Then will not be called`  
    `});`  
`}`


# Conclusion

To sum it up, learning C# concurrency, including threads, tasks, and the ThreadPool, is key for building responsive applications. Learning about parallel execution, best practices, and advanced considerations ensures smoother development and enhances the overall C# concurrency experience.