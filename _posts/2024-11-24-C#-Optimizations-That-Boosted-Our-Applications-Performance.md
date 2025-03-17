---
layout: post
title:  "C# Optimizations That Boosted Our Application's Performance"
tags: csharp dotnet
category: csharp dotnet
image: /assets/img/Csharp_optimizations.png
---

In the world of software development, performance is often the difference between success and failure. As an experienced developer, I faced a significant challenge when our C# application began to struggle under heavy load. After debugging and optimization, my team and I managed to give a good boost to the application's performance. 
In this article, I'll share some easy key optimizations that made this possible, along with some practical examples and explanations.

## Choosing the Right Data Structures
One of the most significant performance bottlenecks in any application is the choice of data structures. In C#, the right data structure can make all the difference. In some high-performance critical scenarios we can for example use Span<T>

#### Example: Using Span<T> Instead of Arrays

Before
```csharp
public void ProcessData(byte[] data)
{
    for (int i = 0; i < data.Length; i++)
    {
        // Process each byte
    }
}
```

After
```csharp
public void ProcessData(Span<byte> data)
{
    foreach (byte b in data)
    {
        // Process each byte
    }
}
```

#### Explanation:
Span<T> is a stack-allocated struct that provides a memory-safe way to work with contiguous blocks of data (Span<T> is not supposed to replace arrays).
By avoiding the overhead of array bounds checking and unnecessary memory allocations, Span<T> can significantly improve performance, especially in tight loops.

## Minimizing Garbage Collection Pressure

Garbage collection (GC) is a powerful feature of .NET, but it can introduce significant overhead if not managed properly. In some scenarios when the class is expensive to create or destroy, we can create an object pool by using a ConcurrentBag.
Example: Object Pooling

Before:
```csharp
public void ProcessMessages()
{
    while (true)
    {
        var message = new Message();
        // Process message
    }
}
```

After:
```csharp
public void ProcessMessages()
{
    var pool = new ObjectPool<Message>(() => new Message());
    while (true)
    {
        var message = pool.Get();
        // Process message
        pool.Return(message);
    }
}
```

#### Explanation:
In C# Object Pooling reuses objects instead of creating new ones, reducing the frequency of garbage collection.
We avoid the overhead of constant allocations and deallocations by reusing Message objects.

## Algorithmic Optimizations
Sometimes, the biggest performance gains come from rethinking your algorithms. 

#### Example: Replacing Linear Search with Binary Search

Before:
```csharp
public bool Contains(List<int> list, int value)
{
    foreach (var item in list)
    {
        if (item == value)
            return true;
    }
    return false;
}
```

After:
```csharp
public bool Contains(List<int> list, int value)
{
    list.Sort();
    return list.BinarySearch(value) >= 0;
}
```

#### Explanation:
List<T>.BinarySearch(T) Method uses a binary search algorithm to locate a specific element in the sorted List<T> or a portion of it.
Binary Search has a time complexity of O(log n), whereas linear search is O(n).
By sorting the list once and using binary search, we significantly reduce the time complexity for multiple searches.

## Leveraging Parallel Processing
Modern CPUs have multiple cores, and C# provides powerful tools to take advantage of them.

#### Example: Parallel LINQ (PLINQ)

Before:
```csharp
var results = data.Select(x => Process(x)).ToList();
```

After:
```csharp
var results = data.AsParallel().Select(x => Process(x)).ToList();
```

#### Explanation:
PLINQ automatically parallelizes LINQ queries, distributing the workload across multiple cores.
This can lead to significant performance improvements for CPU-bound operations.

## Caching and Avoiding Redundant Computations
Caching is a powerful technique to avoid redundant computations.

#### Example: Memoization with Dictionary Cache

Before:
```csharp
public int Fibonacci(int n)
{
    if (n <= 1)
        return n;
    return Fibonacci(n - 1) + Fibonacci(n - 2);
}
```

After:
```csharp
private Dictionary<int, int> cache = new Dictionary<int, int>();
public int Fibonacci(int n)
{
    if (n <= 1)
        return n;
    if (cache.ContainsKey(n))
        return cache[n];
    var result = Fibonacci(n - 1) + Fibonacci(n - 2);
    cache[n] = result;
    return result;
}
```

#### Explanation:
Memoization stores the results of expensive function calls and returns the cached result when the same inputs occur again.
By caching Fibonacci numbers, we reduce the time complexity from exponential to linear.

## Avoiding Unnecessary I/O Operations
I/O operations, such as file access or network calls, are often the biggest bottlenecks in an application.

#### Example: Minimizing Database Calls

Before:
```csharp
for (int i = 0; i < ids.Length; i++)
{
    var user = GetUserFromDatabase(ids[i]);
    // Process user
}
```

After:
```csharp
var users = GetUsersFromDatabase(ids);
foreach (var user in users)
{
    // Process user
}
```

#### Explanation:
Batching database calls reduces the number of round trips to the database, significantly improving performance.
By fetching all users in a single call, we minimize the I/O overhead.

## Using Structs Instead of Classes for Small Data Types
Value types (structs) are stored on the stack, which can be more efficient than reference types (classes) stored on the heap.

#### Example: Using Structs for Small Data

Before:
```csharp
public class Point
{
    public int X { get; set; }
    public int Y { get; set; }
}
```

After:
```csharp
public struct Point
{
    public int X;
    public int Y;
}
```

#### Explanation:
Structs are value types and are allocated on the stack, which is faster and uses less memory for small data types.
By converting Point to a struct, we reduce the overhead of object creation and garbage collection.

## Profiling and Benchmarking
Before making any optimizations, it's crucial to identify the actual bottlenecks in your application.

#### Example: Using Benchmark.NET

```csharp
[MemoryDiagnoser]
public class MyBenchmarks
{
    [Benchmark]
    public void MyMethod()
    {
        // Code to be benchmarked
    }
}
```

#### Explanation:
Benchmark.NET is a powerful tool for measuring the performance of your code.
By diagnosing memory usage and execution time, you can pinpoint the areas that need optimization.



## Conclusion 
As a developer, you can boost the performance of your C# application by applying the right strategies. In this article, I explained how it's important to always try to choose the right data structure, to use wisely parallel processing, and to avoid doing redundant computations. Optimization is not just about making code run faster, it's about making intelligent decisions that balance performance, readability, and maintainability.
