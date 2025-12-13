---
layout: post
title: Trying to understand SOLID Principles with C#
date: 2025-12-13 15:09:00
description: SOLID Principles in C#
tags: C#, SOLID
categories: commands
featured: true
---

![SolidPrinciples](https://raw.githubusercontent.com/prasadrgavande/prasadgavande.github.io/refs/heads/master/assets/img/solid.png)

The **SOLID Principles** are five fundamental principles of object-oriented class design introduced by **Robert C. Martin** (also known as "Uncle Bob"). The acronym "SOLID" was later coined by **Michael Feathers**.

These principles provide guidelines and best practices for designing class structures that are:
- **Understandable** - Easy to read and comprehend
- **Readable** - Clear and well-organized code
- **Testable** - Can be easily tested

### The Five Principles

| Principle | Full Name |
|-----------|-----------|
| S | Single Responsibility Principle |
| O | Open-Closed Principle |
| L | Liskov Substitution Principle |
| I | Interface Segregation Principle |
| D | Dependency Inversion Principle |

---

## Why SOLID Principles Matter

When working on large projects in a team environment, simply writing code that executes correctly is not enough. Your code must be:

1. **Maintainable** - Other developers on your team should understand your code
2. **Testable** - Code should be easy to test
3. **Scalable** - Code should support team collaboration on the same codebase

Following SOLID principles ensures that your codebase remains flexible, extensible, and maintainable as the project grows.

---

## Single Responsibility Principle

### Definition
A class should have only **one responsibility** and should have **only one reason to change**.

### Problem Example

```csharp
public class Circle
{
    public double Radius { get; set; }

    public double CalculateArea()
    {
        return Math.PI * Radius * Radius;
    }
}

public class Program
{
    static void Main()
    {
        Circle circle = new Circle { Radius = 5 };
        
        // Multiple responsibilities:
        // 1. Creating and managing the circle
        double area = circle.CalculateArea();  // Calculation responsibility
        
        Console.WriteLine($"Area: {area}");    // Printing responsibility
    }
}
```

**Issues:** The `Program` class has three responsibilities:
1. Core application logic
2. Area calculation
3. Printing output

### Solution

**Step 1:** Move area calculation to the `Circle` class

```csharp
public class Circle
{
    public double Radius { get; set; }

    public double GetArea()
    {
        return Math.PI * Radius * Radius;
    }
}
```

**Step 2:** Create a separate `Printer` class for output responsibility

```csharp
public class Printer
{
    public void PrintArea(Circle circle)
    {
        Console.WriteLine($"Area: {circle.GetArea()}");
    }
}

public class Program
{
    static void Main()
    {
        Circle circle = new Circle { Radius = 5 };
        Printer printer = new Printer();
        printer.PrintArea(circle);
    }
}
```

**Result:** Each class now has a single, well-defined responsibility:
- `Circle` - Manages circle data and calculations
- `Printer` - Handles output
- `Program` - Orchestrates the application flow

---

## Open-Closed Principle

### Definition
A class should be **open for extension** but **closed for modification**.

This means adding new functionality should not require changing existing code.

### Problem Example

```csharp
public class Circle
{
    public double Radius { get; set; }
    public double GetArea()
    {
        return Math.PI * Radius * Radius;
    }
}

public class Rectangle
{
    public double Length { get; set; }
    public double Width { get; set; }
    public double GetArea()
    {
        return Length * Width;
    }
}

public class Program
{
    static void Main()
    {
        Circle circle = new Circle { Radius = 5 };
        Rectangle rect = new Rectangle { Length = 4, Width = 6 };
        
        // Problem: Need separate methods for each shape
        CalculateArea(circle);
        CalculateArea(rect);  // This method doesn't exist - need to create new ones
    }
}
```

**Issue:** Every time you add a new shape, you must modify the `Program` and `Printer` classes to handle it.

### Solution: Use Abstraction through Interface

**Step 1:** Create an interface for all shapes

```csharp
public interface IShape
{
    double GetArea();
}
```

**Step 2:** Implement the interface in shape classes

```csharp
public class Circle : IShape
{
    public double Radius { get; set; }
    
    public double GetArea()
    {
        return Math.PI * Radius * Radius;
    }
}

public class Rectangle : IShape
{
    public double Length { get; set; }
    public double Width { get; set; }
    
    public double GetArea()
    {
        return Length * Width;
    }
}
```

**Step 3:** Update the Printer class to work with the interface

```csharp
public class Printer
{
    public void PrintArea(IShape shape)
    {
        Console.WriteLine($"Area: {shape.GetArea()}");
    }
}

public class Program
{
    static void Main()
    {
        Circle circle = new Circle { Radius = 5 };
        Rectangle rect = new Rectangle { Length = 4, Width = 6 };
        
        Printer printer = new Printer();
        printer.PrintArea(circle);   // Works!
        printer.PrintArea(rect);     // Works!
    }
}
```

**Step 4:** Add new shapes without modifying existing code

```csharp
public class Triangle : IShape
{
    public double Base { get; set; }
    public double Height { get; set; }
    
    public double GetArea()
    {
        return 0.5 * Base * Height;
    }
}

// No changes needed to Program or Printer classes!
public class Program
{
    static void Main()
    {
        Triangle triangle = new Triangle { Base = 5, Height = 10 };
        Printer printer = new Printer();
        printer.PrintArea(triangle);  // Works!
    }
}
```

**Result:** The code is now open for extension (add new shapes) without modification (no changes to existing classes).

---

## Liskov Substitution Principle

### Definition
Child classes (derived classes) must be able to substitute their parent classes without breaking the application.

In other words, derived classes should properly implement the contract of their base class.

### Problem Example

```csharp
public class Rectangle
{
    public double Length { get; set; }
    public double Width { get; set; }
    
    public double GetArea()
    {
        return Length * Width;
    }
}

public class Square : Rectangle
{
    // A square must have equal length and width
    // But Rectangle allows independent length and width
}
```

**Issue:** A `Square` inheriting from `Rectangle` creates a logical problem:
- User expects to set `Length` and `Width` independently on a Rectangle
- A Square must keep `Length` and `Width` equal
- When user sets `Length = 5` and `Width = 3`, it violates the square property
- If we override `Width` to always equal `Length`, we violate Rectangle's contract

This violates the Liskov Substitution Principle because you cannot substitute a `Square` for a `Rectangle` without breaking expected behavior.

### Solution

**Step 1:** Create a proper interface hierarchy

```csharp
public interface IShape
{
    double GetArea();
}

public interface ITwoDShape : IShape
{
    // For 2D shapes
}

public interface IThreeDShape : IShape
{
    // For 3D shapes
}
```

**Step 2:** Implement shapes correctly based on their hierarchy

```csharp
public class Rectangle : ITwoDShape
{
    public double Length { get; set; }
    public double Width { get; set; }
    
    public double GetArea()
    {
        return Length * Width;
    }
}

public class Square : ITwoDShape
{
    public double Side { get; set; }
    
    public double GetArea()
    {
        return Side * Side;
    }
}
```

**Result:** Now `Square` and `Rectangle` are independent implementations of `IShape`, each correctly implementing their own contract. Neither is a substitution for the other—they're separate shape types.

---

## Interface Segregation Principle

### Definition
Clients should not be forced to implement methods they don't use.

It's better to have many specific interfaces than one general-purpose interface.

### Problem Example

```csharp
public interface IShape
{
    double GetArea();
    double GetVolume();  // Not all shapes have volume!
}

public class Circle : IShape
{
    public double Radius { get; set; }
    
    public double GetArea()
    {
        return Math.PI * Radius * Radius;
    }
    
    public double GetVolume()
    {
        // Circle has no volume - forced to implement dummy method!
        throw new NotImplementedException("Circle is 2D and has no volume");
    }
}

public class Cube : IShape
{
    public double Side { get; set; }
    
    public double GetArea()
    {
        // Misleading - cube has surface area, not a single "area"
        return 6 * Side * Side;
    }
    
    public double GetVolume()
    {
        return Side * Side * Side;
    }
}
```

**Issues:**
- Circle is forced to implement `GetVolume()` which is not applicable
- Cube's `GetArea()` is ambiguous (surface area vs. 2D area concept)
- Classes implement methods they don't logically need

### Solution: Segregate Interfaces

**Step 1:** Create separate interfaces for different shape types

```csharp
public interface ITwoDShape
{
    double GetArea();
}

public interface IThreeDShape
{
    double GetVolume();
    double GetSurfaceArea();
}

public interface IShape
{
    // Common interface with no methods or only essential ones
}
```

**Step 2:** Implement appropriately

```csharp
public class Circle : ITwoDShape
{
    public double Radius { get; set; }
    
    public double GetArea()
    {
        return Math.PI * Radius * Radius;
    }
}

public class Rectangle : ITwoDShape
{
    public double Length { get; set; }
    public double Width { get; set; }
    
    public double GetArea()
    {
        return Length * Width;
    }
}

public class Cube : IThreeDShape
{
    public double Side { get; set; }
    
    public double GetVolume()
    {
        return Side * Side * Side;
    }
    
    public double GetSurfaceArea()
    {
        return 6 * Side * Side;
    }
}
```

**Step 3:** Use appropriate interfaces in methods

```csharp
public class Printer
{
    public void PrintTwoDShape(ITwoDShape shape)
    {
        Console.WriteLine($"2D Area: {shape.GetArea()}");
    }
    
    public void PrintThreeDShape(IThreeDShape shape)
    {
        Console.WriteLine($"3D Volume: {shape.GetVolume()}");
        Console.WriteLine($"Surface Area: {shape.GetSurfaceArea()}");
    }
}
```

**Result:** Each class only implements the methods it needs. No forced implementations of irrelevant methods.

---

## Dependency Inversion Principle

### Definition
High-level classes should not depend directly on low-level classes. Both should depend on **abstractions** (interfaces).

### Problem Example

```csharp
public class Printer
{
    public void Print(IShape shape)
    {
        Console.WriteLine($"Area: {shape.GetArea()}");
    }
}

public class Program
{
    static void Main()
    {
        Printer printer = new Printer();
        Circle circle = new Circle { Radius = 5 };
        printer.Print(circle);
    }
}
```

**Issue:** If later you want to use a different printer (e.g., `FilePrinter`, `CSVPrinter`, `ConsolePrinter`), you must modify the `Program` class and update all dependencies. The method signature and class structure might differ across implementations.

### Solution: Depend on Abstraction

**Step 1:** Create an interface for printers

```csharp
public interface IPrinter
{
    void Print(ITwoDShape shape);
}
```

**Step 2:** Implement different printer types

```csharp
public class ConsolePrinter : IPrinter
{
    public void Print(ITwoDShape shape)
    {
        Console.WriteLine($"Console Area: {shape.GetArea()}");
    }
}

public class FilePrinter : IPrinter
{
    public void Print(ITwoDShape shape)
    {
        File.WriteAllText("output.txt", $"File Area: {shape.GetArea()}");
    }
}
```

**Step 3:** Depend on the abstraction

```csharp
public class Program
{
    static void Main()
    {
        IPrinter printer = new ConsolePrinter();  // Can easily swap implementations
        Circle circle = new Circle { Radius = 5 };
        printer.Print(circle);
    }
}
```

**Result:** Now you can:
- Switch between different printer implementations easily
- Add new printer types without modifying the `Program` class
- Change implementation by modifying just one line
- The method signature remains consistent across all implementations

### Advanced: Dependency Injection

For even better decoupling, use dependency injection:

```csharp
public class Program
{
    private readonly IPrinter _printer;
    
    public Program(IPrinter printer)
    {
        _printer = printer;
    }
    
    public void Run()
    {
        Circle circle = new Circle { Radius = 5 };
        _printer.Print(circle);
    }
    
    static void Main()
    {
        // Inject the dependency - Program doesn't create it
        IPrinter printer = new ConsolePrinter();
        Program program = new Program(printer);
        program.Run();
    }
}
```

The `Program` class doesn't even need to know which concrete printer is being used. It only knows about the `IPrinter` interface.

---

## Summary

| Principle | Key Concept | Benefit |
|-----------|------------|---------|
| **SRP** | One responsibility per class | Easy to understand and maintain |
| **OCP** | Open for extension, closed for modification | Add features without breaking existing code |
| **LSP** | Derived classes should substitute base classes | Proper inheritance hierarchies |
| **ISP** | Segregate interfaces by client needs | Classes implement only needed methods |
| **DIP** | Depend on abstractions, not concrete classes | Flexible and loosely coupled code |

### Best Practices

1. **Apply gradually** - Don't try to implement all principles at once
2. **Use interfaces** - They enable abstraction and flexibility
3. **Keep it simple** - Over-engineering with too many abstractions can make code complex
4. **Review existing code** - Identify which classes violate which principles
5. **Refactor incrementally** - Improve your codebase step by step
6. **Consider Factory Pattern** - Combine with SOLID for maximum decoupling
7. **Use Dependency Injection** - Frameworks like ASP.NET Core provide built-in DI containers
