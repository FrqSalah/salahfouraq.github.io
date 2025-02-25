---
layout: post
title:  "Error Handling in .NET: Exceptions or Results?"
tags: csharp dotnet
category: csharp dotnet
cover-img: /assets/img/results_vs_exceptions_cover.jpg
---

After years of experience working on diverse software projects, I've observed a concerning trend: developers often overuse exceptions for error handling. While exceptions have their place in certain situations, I believe that well-designed code should be capable of managing most scenarios without resorting to exceptions. In my view, truly clean and robust code should handle various cases and potential issues gracefully, with exceptions reserved for exceptional circumstances.

## Exceptions

Exceptions are most effectively employed for handling unexpected errors that the program cannot gracefully manage through normal control flow. They should be reserved for serious issues that demand immediate attention and typically necessitate halting the program's execution. However, their use should be judicious and purposeful.

### Pros
- Clear indication of critical failures.
- Simplifies control flow by separating error handling from regular logic.

### Cons
- Can be overused, leading to cluttered code.
- My cause performance overhead due to stack unwinding.

### Example with Exceptions

```csharp
public void ProcessOrder(Order order)
{
    try
    {
        ValidateOrder(order);
        SaveOrder(order);
    }
    catch (ValidationException ex)
    {
        Console.WriteLine($"Validation failed: {ex.Message}");
    }
    catch (Exception ex)
    {
        Console.WriteLine($"An unexpected error occurred: {ex.Message}");
    }
}

private void ValidateOrder(Order order)
{
    if (order == null)
        throw new ValidationException("Order cannot be null.");
    // Additional validation logic...
}
```

It's crucial to distinguish between exceptional conditions and expected error states. For instance, a file not found when opening a configuration file might be an exception, but a file not found when searching for user-uploaded content is likely an expected possibility that should be handled without throwing an exception.
By adhering to these principles, developers can create more robust, maintainable, and performant code that clearly separates normal operations from truly exceptional circumstances.

## Result : returning values instead of exception

A superior alternative to using exceptions is implementing the Result pattern. This approach leads to more robust and maintainable code for several compelling reasons:
- Explicit error handling: Results force developers to consider and handle potential failure cases explicitly.
- Better performance: Creating and throwing exceptions can be costly in terms of performance.
- Improved readability: The code's intent becomes clearer when success and failure paths are explicitly defined.
- Type safety: Results can leverage the type system to ensure errors are handled appropriately.

### Implementation example : 
Let's write the previous code using result patter : 

Result is a generic class that encapsulates the success or failure of an operation, along with either the result value or an error message.
```csharp
public class Result<T>
{
    public bool IsSuccess { get; }
    public T Value { get; }
    public string Error { get; }

    private Result(bool isSuccess, T value, string error)
    {
        IsSuccess = isSuccess;
        Value = value;
        Error = error;
    }

    public static Result<T> Success(T value) => new Result<T>(true, value, null);
    public static Result<T> Failure(string error) => new Result<T>(false, default, error);
}
```

ProcessOrder method: Now returns a Result<Order> instead of using exceptions. It explicitly handles failures from both validation and saving steps.
ValidateOrder and SaveOrder methods are now returning Result<bool> instead of throwing exceptions. This makes the possibility of failure explicit in their signatures :

```csharp
public class OrderProcessor
{
    public Result<Order> ProcessOrder(Order order)
    {
        var validationResult = ValidateOrder(order);
        if (!validationResult.IsSuccess)
        {
            Console.WriteLine($"Validation failed: {validationResult.Error}");
            return Result<Order>.Failure(validationResult.Error);
        }

        var saveResult = SaveOrder(order);
        if (!saveResult.IsSuccess)
        {
            Console.WriteLine($"An unexpected error occurred: {saveResult.Error}");
            return Result<Order>.Failure(saveResult.Error);
        }

        return Result<Order>.Success(order);
    }

    private Result<bool> ValidateOrder(Order order)
    {
        if (order == null)
            return Result<bool>.Failure("Order cannot be null.");

        // Additional validation logic...
        return Result<bool>.Success(true);
    }

    private Result<bool> SaveOrder(Order order)
    {
        try
        {
            // Simulating saving the order to a database
            // In a real scenario, this might involve a database call
            // that could fail for various reasons
            return Result<bool>.Success(true);
        }
        catch (Exception ex)
        {
            return Result<bool>.Failure($"Failed to save order: {ex.Message}");
        }
    }
}

```

Instead of using try-catch blocks, we now check the IsSuccess property of the returned Results. This makes the error handling more explicit and easier to follow.

```csharp
// Usage example
public void ProcessOrderExample()
{
    var processor = new OrderProcessor();
    var order = new Order(); // Assume this is properly initialized

    var result = processor.ProcessOrder(order);
    if (result.IsSuccess)
    {
        Console.WriteLine("Order processed successfully.");
    }
    else
    {
        Console.WriteLine($"Order processing failed: {result.Error}");
    }
}
```

This approach provides several benefits:

- It's more explicit about what can go wrong and where.
- It avoids the performance overhead of throwing and catching exceptions.
- It makes the code easier to reason about, as the flow is more linear and predictable.
- It encourages handling of all possible outcomes, as the compiler will warn you if you don't check the Result.

## Summary 
Effective error handling is crucial for developing robust and maintainable applications. As developers, we should anticipate potential failures and address them at the earliest possible stage in our code. This proactive approach will not only improves code quality but also enhances overall application reliability.
