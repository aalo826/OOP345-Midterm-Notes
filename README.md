# C++ Programming Notes

# Week 1 Old & New Ideas

## 1. Modular Design in Applications

### Key Concepts
```cpp
// Module Example (Parking.h)
#pragma once
class ParkingModule {
public:
    void processTicket();
};
```

### Design Principles
- **Loose Coupling**:
  - Modules communicate through interfaces
  - Minimal dependencies between modules
- **Benefits**:
  - ✅ Independent compilation
  - ✅ Easier testing
  - ✅ Team parallelization

## 2. Typedef and Aliases

### Traditional vs Modern
```cpp
// Traditional (C-style)
typedef std::vector<std::string> StringList;

// Modern (C++11)
using StringList = std::vector<std::string>;
```

### Template Aliasing
```cpp
template<typename T>
using Matrix = std::vector<std::vector<T>>;

Matrix<double> neuralWeights;
```

## 3. Variable Scope and Lifetime

### Block Scope Example
```cpp
void foo() {
    int x = 5;  // Local scope
    {
        int y = x * 2;  // Nested scope
    } // y destroyed here
} // x destroyed here
```

### Shadowing Warning
```cpp
int value = 10;
if (true) {
    int value = 20;  // DANGER: Shadowing
    std::cout << value;  // 20 (confusing)
}
```

## 4. Initialization in Conditionals (C++17)

### Syntax
```cpp
if (auto result = calculate(); result > threshold) {
    // result only exists here
}
```

### Switch Statement Usage
```cpp
switch (int code = getErrorCode(); code) {
    case 404: //...
    case 500: //...
}
```

## 5. For Loop Scoping

### Correct Implementation
```cpp
for (int i = 0; i < 10; ++i) {
    // i only visible here
}
// i is destroyed
```

### Range-based For
```cpp
for (const auto& item : container) {
    // Automatic type deduction
}
```

## 6. Global vs Module-Specific Variables

### Linkage Types
```cpp
// module1.cpp
extern int globalVar;  // Declaration

// module2.cpp
int globalVar = 42;    // Definition
```

### Static Variables
```cpp
static int moduleLocal = 0;  // File scope only
```

## 7. Anonymous Namespaces

### Implementation
```cpp
namespace {
    void helper() {  // Internal linkage
        // Implementation
    }
}
```

### Modern Alternative
```cpp
static void helper() {  // Same effect
    // Implementation
}
```

## 8. Command Line Arguments

### Complete Example
```cpp
int main(int argc, char* argv[]) {
    std::cout << "Program: " << argv[0] << "\n";
    for (int i = 1; i < argc; ++i) {
        std::cout << "Arg " << i << ": " << argv[i] << "\n";
    }
}
```

## 9. Constexpr and Compile-Time

### Variables
```cpp
constexpr double PI = 3.1415926535;
```

### Functions
```cpp
constexpr int factorial(int n) {
    return (n <= 1) ? 1 : n * factorial(n - 1);
}
```

## 10. Static Local Variables

### Persistent State
```cpp
void counter() {
    static int calls = 0;  // Persists
    std::cout << ++calls;
}
// Output: 1, 2, 3...
```

## 11. Subobjects in Inheritance

### Memory Layout
```cpp
class Base { int x; };
class Derived : public Base { int y; };
// Memory: [Base][Derived]
```

### Best Practices Summary

1. **Scope Management**:
   - Prefer narrow scope
   - Avoid shadowing

2. **Compile-Time**:
   - Use `constexpr` where possible

3. **Modularity**:
   - Favor small, focused modules
   - Minimize dependencies

4. **Modern C++**:
   - Prefer `using` over `typedef`
   - Use anonymous namespaces for internal linkage





# Week 2 Fundamental Types, Classes and Enumerations

1\. **Integer Types and Representations**
-----------------------------------------

### Signed Integer Ranges (Two's Complement Representation)

| Type | Min | Max |
| --- | --- | --- |
| `signed char` | -128 | 127 |
| `short int` | ≤ -32,768 | ≥ 32,767 |
| `int` | -2,147,483,648 | 2,147,483,647 |
| `long int` | ≤ -2,147,483,648 | ≥ 2,147,483,647 |
| `long long int` | ≤ -9.2e18 | ≥ 9.2e18 |

### Unsigned Integer Ranges (32-bit)

| Type | Min | Max |
| --- | --- | --- |
| `unsigned char` | 0 | 255 |
| `unsigned short int` | 0 | ≥ 65,535 |
| `unsigned int` | 0 | 4,294,967,295 |
| `unsigned long int` | 0 | ≥ 4,294,967,295 |
| `unsigned long long` | 0 | ≥ 1.8e19 |

### Numeric Literal Notation

-   Decimal: `91`

-   Octal: `0133` (leading `0`)

-   Hexadecimal: `0x5b`, `0X5B`

-   Type suffixes:

    -   `L`/`LL` = long/long long

    -   `U` = unsigned (e.g., `0x5bUL`, `456789ULL`)

* * * * *

2\. **Wide and Unicode Character Types**
----------------------------------------

### Types and Literal Prefixes

-   `wchar_t` → `L'['`, `L'\x5b'`

-   `char16_t` → `u'['`, `u'\133'`

-   `char32_t` → `U'['`, `U'\x5b'`

These allow representation of wide and UTF characters for internationalization and platform-independent encoding.

* * * * *

3\. **Floating-Point Types**
----------------------------

| Type | Size | Significant Digits | Min Exp | Max Exp |
| --- | --- | --- | --- | --- |
| float | 4 bytes | 6 | -37 | 38 |
| double | 8 bytes | 15 | -307 | 308 |

Refer to standards for precision-critical tasks, such as scientific computing or financial systems.

* * * * *

4\. **Array Initialization Variants**
-------------------------------------

Syntax variations (all valid):

```cpp
Type id[c] = { list };
Type id[c] { list };
Type id[] = { list };
Type id[] { list };
Type* id = new Type[n] { list };`
```
These enable fixed-size and dynamic array creation, with flexibility in initialization.

* * * * *

5\. **Struct Memory Layout and Alignment**
------------------------------------------

### Key Issues:

-   **Padding**: The compiler adds unused bytes to align data properly.

-   **Alignment Directive**: `alignas(8)` ensures data is aligned to 8-byte boundaries.

### Examples:

#### Poor Layout:
```cpp
`struct sample {
  char type;    // 1 byte
  int value;    // 4 bytes
  char field;   // 1 byte
};
// Padding: 3 bytes after `type`, 3 bytes after `field``
```
#### Improved Layout:
```cpp
`struct sample {
  int value;    // 4 bytes
  char field;   // 1 byte
  char type;    // 1 byte
};
// Padding: 2 bytes after `type``
```
Proper layout minimizes memory waste and improves performance (e.g., cache alignment).

* * * * *

6\. **Typedef and Pointer Declaration**
---------------------------------------

### Examples:

```cpp
typedef long long int* lliptr;
lliptr myptr = nullptr;  // pointer included in typedef

typedef long long int lli;
lli* myptr2 = nullptr;   // pointer explicitly declared`
```
Avoid confusion by knowing whether the pointer is part of the typedef.

* * * * *

7\. **Type Casting with Pointers**
----------------------------------

### Void Pointer Casting

```cpp
int x = 5;
void* ptr = &x;
int* myintptr = static_cast<int*>(ptr);`
```
### Chained Casts (Unsafe)

```cpp
char x = 'A';
char* c = &x;
int* i = static_cast<int*>(static_cast<void*>(c));`
```
-   **Caution**: Casting between unrelated types (e.g., `char*` to `int*`) is error-prone and undefined in strict aliasing rules.

* * * * *

8\. **Iteration Techniques**
----------------------------

### Traditional Index-Based Loop

```cpp
`for (int i = 0; i < sizeof(x)/sizeof(int); ++i) {
  cout << x[i] << endl;
}`
```
### Range-Based Loop

```cpp
for (int& item : x) { ... }
for (auto& item : x) { ... }`
```
-   `auto&` allows type inference and is flexible with container types.

-   Prefer **range-based for loops** for readability and safety.

* * * * *

9\. **Anonymous Classes**
-------------------------

### Characteristics:

-   No class name.

-   Intended for short-lived, scoped use.

-   Instance declared directly after class definition.

### Example:

```cpp
class {
  double efficiency;
public:
  void setEfficiency(double e) { efficiency = e; }
  int fuelCost(int distance) { return distance * efficiency; }
} engine;`
```
-   Use cases: encapsulate behavior locally without polluting the global namespace.

* * * * *

10\. **Strongly Typed Enumerations (`enum class`)**
---------------------------------------------------

### Example:

```cpp
`enum class Traffic_light {
  red = 1,
  yellow = 3,
  green = 6,
  orange = 12
};`
```
### Benefits:

-   Type-safe (unlike legacy `enum`)

-   Scopes the enumeration to prevent name conflicts.

Recurring Themes & Best Practices
---------------------------------

-   **Memory layout and alignment** affect performance and binary compatibility.

-   **Use modern C++ features** (`enum class`, `auto`, `range-based for`) for safer and cleaner code.

-   **Literal suffixes and type awareness** are critical when working with mixed-size integers or portability-sensitive code.

-   **Avoid unsafe casting** and prefer `static_cast` where necessary, with full understanding of consequences.

-   **Leverage typedefs wisely** to simplify pointer or complex type declarations.

# Week 3 Inheritance & Templates

1\. **Abstract Base Classes (ABCs)**
------------------------------------

### Definition & Purpose

-   **Abstract class**: A class with one or more **pure virtual functions**.

-   Cannot be instantiated.

-   Acts as an interface for derived concrete classes.

### Example:

```cpp
`class Compound {
public:
  virtual std::ostream& display(std::ostream& os) const = 0;
  virtual std::istream& write(std::istream& is) = 0;
  virtual int value() const = 0;
  virtual Compound* clone() const = 0;
  virtual bool operator==(Compound& c) const = 0;
};`
```

### Design Ideas:

-   ABCs enforce a contract for derived classes.

-   Useful for polymorphism and generic programming.

* * * * *

2\. **Concrete Implementations & Polymorphism**
-----------------------------------------------

### Inheritance Hierarchy:

```scss
`Compound (ABC)
├── Playdoh (Concrete)
└── Clay (Concrete)`
```
### Dynamic Allocation:

```cpp
`Compound* c = new Playdoh();`
```
Used to support runtime polymorphism via virtual functions.

* * * * *

3\. **Cloning and Copying via Virtual Constructors**
----------------------------------------------------

### Problem:

-   Polymorphic copying (e.g., `Compound* copy = new ???(src)`)

### Solution: Virtual `clone()`

```cpp
`virtual Compound* clone() const = 0;

Compound* Playdoh::clone() const {
  return new Playdoh(*this);
}`
```
-   Ensures correct type is copied.

-   Enables copying without knowing the concrete type.

* * * * *

4\. **Equality via Operator Overloading**
-----------------------------------------

### Pattern:

```cpp
`virtual bool operator==(Compound& c) const = 0;

bool Playdoh::operator==(Compound& c) const {
  const Playdoh* play = dynamic_cast<Playdoh*>(&c);
  return play ? play->colour == colour : false;
}`
```
### Key Points:

-   Uses `dynamic_cast` to safely downcast.

-   Supports type-safe polymorphic comparison.

* * * * *

5\. **Run-Time Type Information (RTTI)**
----------------------------------------

### Type Identification:

```cpp
`typeid(i).name();   // Outputs: int
typeid(ptr).name(); // Outputs: int*`
```
### Usage Example:

```cpp


`void DrawShape(const Shape& s) {
  if (typeid(s) == typeid(Square)) DrawSquare(static_cast<Square&>(s));
  else if (typeid(s) == typeid(Circle)) DrawCircle(static_cast<Circle&>(s));
}`
```
### Pitfall:

-   Not scalable: Adding new shapes requires modifying `DrawShape()`.

-   Better alternative: Use virtual functions or double dispatch.

* * * * *

6\. **Templates**
-----------------

### Function Templates

Generic implementation reusable across types.

```cpp
`template <typename T>
void temp_swap(T& a, T& b) {
  T c = a;
  a = b;
  b = c;
}`
```
### Specialization

```cpp
`template <>
char* maximum<char*>(char* a, char* b) {
  return std::strcmp(a, b) > 0 ? a : b;
}`
```
### Benefits:

-   Reuse logic for multiple types.

-   Type safety enforced at compile-time.

* * * * *

7\. **Class Templates**
-----------------------

### Example:

```cpp
`template <typename T, int N>
class Calculator {
  T result[N]{};
};`
```
-   `T` = type parameter

-   `N` = non-type parameter (e.g., array size)

### With Defaults:

```cpp
`template <typename T = int, int N = 3>
class Calculator { ... };

Calculator<> c1; // Uses defaults`
```
### Application:

-   Efficient for compile-time fixed-size containers or computations.

* * * * *

8\. **Template Parameter Packs (Variadic Templates)**
-----------------------------------------------------

### Function Templates with Variable Parameters:

```cpp
`template <typename T, typename... Args>
void print(const T& t, const Args&... rest) {
  std::cout << t << " | ";
  print(rest...);
}

template <typename T>
void print(const T& t) {
  std::cout << t << std::endl;
}`
```
### Usage:

```cpp
`print(1, "apple", 3.14, 'X');`
```
-   Powerful pattern for generic logging, forwarding, tuple handling, etc.

-   Recursive expansion until base case.

* * * * *

Recurring Themes & Design Principles
------------------------------------

-   **Polymorphism**: Achieved via ABCs, virtual functions, and cloning.

-   **Type Safety**: Enforced using templates, type traits, and `dynamic_cast`.

-   **Compile-Time vs Runtime**:

    -   Templates: Resolved at compile-time (efficient but less flexible).

    -   RTTI & Virtual Dispatch: Resolved at runtime (flexible but slightly costlier).

-   **Open/Closed Principle**: Violated by RTTI-heavy code like `DrawShape()`; solved via polymorphism.

-   **Generic Programming**: Templates allow type-independent implementation.

# Week 4 Relationships & Expressions

🧱 Composition vs. Aggregation in C++
-------------------------------------

### 1\. **Composition (Has-a Relationship)**

-   A **composing class** owns its **component class** objects.

-   **Subobject semantics**: component is part of the parent object's memory.

### Example

```cpp

class Playdoh {
    char* colour;
    int weight;
    ...
};

class PlaydohPack {
    Playdoh playdohs; // direct subobject
    ...
};
```

-   **Ownership**: `PlaydohPack` owns the `Playdoh` object.

-   **Lifecycle**: When `PlaydohPack` is destroyed, so is the contained `Playdoh`.

### 2\. **Composition Using Pointers**

```cpp

class PlaydohPack {
    Playdoh* playdohs; // dynamic allocation
    ...
};
```
-   Requires **manual memory management**:

    -   Custom **copy constructor**, **copy assignment operator**, and **destructor** needed (Rule of 3/5/0).

-   Used when:

    -   You need **polymorphism**, or

    -   The number of contained objects is not known at compile-time.

* * * * *

🧩 Aggregation
--------------

-   An **aggregating class** uses but **does not own** the component object.

-   Think of it as "borrowing" rather than owning.

### 1\. **Reference Aggregation**

```cpp
class PlaydohPackAgg {
    Playdoh& playdoh;
    ...
};
```

-   `PlaydohPackAgg` uses a reference to an existing `Playdoh`.

-   No memory management responsibility.

-   Safer when lifetime of referenced object is guaranteed.

### 2\. **Pointer Aggregation**

```cpp

class PlaydohPackAgg {
    const Playdoh* playdohs[size];
    ...
};
```

-   Pointers to existing objects.

-   No ownership → no deletion in destructor.

-   Useful for arrays or collections of shared resources.

* * * * *

🔁 Mutual Association Example
-----------------------------

```cpp

class Playdoh {
    char* colour;
    int weight;
    ToyBox* t;
    ...
};

class ToyBox {
    string name;
    string material;
    Playdoh* p;
    ...
};
```

-   Demonstrates **bidirectional association** using pointers.

-   Useful for models requiring navigation in both directions.

* * * * *

🔍 Value Categories & Expression Types
--------------------------------------

### Lvalue vs. Prvalue

#### Example 1

```cpp

int i = 10;
std::cout << i++ << std::endl;
(i++)++;  // ❌ ERROR: (i++) is a prvalue (can't be assigned to)`
```
-   `i++` is a **prvalue** (pure rvalue); result is temporary.

-   You can't increment a temporary.

#### Example 2

```cpp

`int i = 10;
++(++i);  // ✅ OK: (++i) is an lvalue
```
-   ++i` returns an lvalue --- can be incremented again.

-   This difference is subtle but critical in expression evaluation.

* * * * *

📦 Struct with Array and Display Function
-----------------------------------------

```cpp

struct Box {
    int weight{};
    int items[5]{1, 2, 3, 4, 5};

    void display() const {
        std::cout << "Box Weight: " << weight << std::endl;
        for (int i = 0; i < 5; ++i) {
            int temp = *this.items[i]; // ❌ ERROR
            ...
        }
    }
};
```

-   **Issue**: `*this.items[i]` is invalid syntax.

    -   Should be: `items[i]`

-   This illustrates misuse of the dereference operator in array context.

* * * * *

🔎 `decltype` and Type Deduction
--------------------------------

```cpp

`int x = 10;
double y = 5.5;
double& z = y;

decltype(x) var1 = 1;          // int
decltype(y) var2 = 2.2;        // double
decltype(x + y) var3 = 3;      // double (arithmetic promotion)
decltype(z) var4 = var2;       // double& (reference preserved)
```
### Key Takeaways

-   `decltype(expr)` gives the **exact type**, including references.

-   Useful for:

    -   Writing generic code.

    -   Preserving const/reference semantics.

-   `decltype(x + y)` evaluates expression type, not individual variable types.

* * * * *

🧠 Key Themes & Takeaways
-------------------------

### Object Design Principles

-   Understand ownership: use composition for strong ownership; aggregation for shared use.

-   Pointer and reference semantics demand careful lifecycle control.

### Memory Management

-   Manual management introduces risk of **resource leaks** or **undefined behavior**.

-   Use modern tools like `std::unique_ptr` and `std::shared_ptr` where applicable.

### Expression Categories

-   Knowing when expressions are **lvalues** or **prvalues** affects what you can do with them.

-   Misusing them results in **compilation errors**.

### Type Deduction

-   `decltype` helps maintain type correctness in templates or complex declarations.

* * * * *

⚠️ Pitfalls
-----------

-   Using pointers in compositions without managing memory leads to undefined behavior.

-   Incorrect dereferencing or misuse of operators can result in compile-time or run-time errors.

-   Bidirectional associations need careful handling to avoid **dangling pointers**.

# Week 5 Functions and Error Handling

🧮 Prime Number Check: Recursive vs. Iterative
----------------------------------------------

### 1\. **Recursive Implementation**

```cpp
bool isPrime(int num, int recur) {
    bool ret = false;
    if (num == 1 || recur == 1)
        ret = true;
    else if ((num % recur) == 0)
        ret = false;
    else
        ret = isPrime(num, recur - 1);
    return ret;
}
```

#### Concepts & Design

-   **Base case**: If either `num == 1` or `recur == 1`, return `true`.

-   **Divisibility check**: If `num` is divisible by `recur`, not prime.

-   **Recursive step**: Check divisibility down to `1`.

#### Pitfalls

-   This implementation incorrectly considers `1` as prime.

-   Recursive depth can be large, leading to **stack overflow** on big `num`.

* * * * *

### 2\. **Iterative Implementation**

```cpp
bool isPrimeIter(int num) {
    bool ret = true;
    if (num != 1) {
        int i = num - 1;
        bool done = false;
        while (i > 1 && !done) {
            if (num % i == 0) {
                done = true;
                ret = false;
            }
            i--;
        }
    }
    return ret;
}
```

#### Key Points

-   **Loop-based check** from `num-1` down to `2`.

-   Sets `ret = false` on first divisor match.

-   More efficient in avoiding stack overflows.

#### Comparison

| Aspect | Recursive | Iterative |
| --- | --- | --- |
| Readability | Often more intuitive | More control flow details |
| Efficiency | Risk of deep call stack | Better memory control |
| Correctness | Incorrectly considers 1 prime | Correct, if adjusted |

* * * * *

🧭 Function Pointers
--------------------

### Syntax & Usage

```cpp
void (*ptrToFuncA)() = funcA;

void funcA() {
    cout << "I am Function A" << endl;
}
```

### Concepts

-   `void (*ptr)()` declares a **pointer to a function** that returns `void` and takes no parameters.

-   You can invoke the function via the pointer: `ptrToFuncA();`.

#### Applications

-   Used in **callback mechanisms**, **event handlers**, and **dynamic dispatch**.

-   Enables storing functions in **arrays of function pointers** or **passing functions as parameters**.

* * * * *

🧠 Lambda Expression Snippet (Incomplete)
-----------------------------------------

```cpp
[](char c) {
    c = toupper(c);
    return c != 'A' || c != 'E' || c != 'I' || c != 'O' || c != 'U';
};
```

### Key Concepts

-   **Anonymous function (lambda)** with one parameter `char c`.

-   Converts to uppercase and returns a boolean.

#### Logical Issue

-   Condition `c != 'A' || c != 'E' || ...` is always `true`:

    -   Because any char not equal to one of them will satisfy at least one `!=`.

    -   **Correct logic should be**:

       ``` cpp
        return !(c == 'A' || c == 'E' || ...);
       ```

#### Uses

-   Lambdas are common in **STL algorithms** like `std::find_if`, `std::sort`, etc.

-   Can capture variables and support inline custom logic.

* * * * *

🛡️ Exception Handling in C++
-----------------------------

### Structure

```cpp
try {
    // Code that might throw
} catch (Type identifier) {
    // Handle specific type
} catch (...) {
    // Catch-all handler
}
```

### Principles

-   Use `try` to wrap code that may throw.

-   Use `catch` to handle **specific exception types**.

-   `catch(...)` is a **generic handler** for unexpected types.

### Best Practices

-   Catch exceptions **by reference** (e.g., `catch (const std::exception& e)`).

-   Minimize code inside `try`; isolate risky operations.

-   Avoid overuse of `catch(...)`, as it obscures error types.

* * * * *

🎯 Key Takeaways
----------------

### General Patterns

-   **Recursive vs Iterative**: Choose based on resource limits and clarity.

-   **Function pointers**: Add flexibility, especially in systems with dynamic behavior.

-   **Lambdas**: Modern and concise, ideal for inline logic, but watch logical correctness.

-   **Exception handling**: Provides robustness, but must be used thoughtfully to avoid masking errors.

### Common Pitfalls

-   Incorrect base cases in recursion (e.g., `1` is not prime).

-   Logical errors in boolean expressions (`||` vs `&&`).

-   Dereferencing uninitialized function pointers can lead to crashes.

-   Catching by value may cause object slicing or inefficient copies.

* * * * *

🔄 Recurring Themes & Relationships
-----------------------------------

-   **Abstraction & control**: Function pointers and lambdas both decouple **what** is done from **when/how** it's called.

-   **Safety vs. performance**: Iterative versions and explicit error handling aim for **robustness**, while recursion and lambdas offer **conciseness**.

-   **Error containment**: Exception handling ensures failures don't propagate unchecked.
