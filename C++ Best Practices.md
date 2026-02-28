### 1. Do not use `using namespace`.
Reason: If there are multiple symbols with the same name but in different namespaces, not specifying which namespace the symbol you want to use is in can cause name conflicts.  

Example:
```cpp
#include <iostream>

namespace a {
    int n = 10;
}
namespace b {
    int n = 5;
}

using namespace a; // Brings a::n inside the global scope.
using namespace b; // Brings b::n inside the global scope.

int main() {
    std::cout << n;
    // Compiler sees 2 'n's and will say: "Which one are you referring??" -> Ambiguous (Name conflict)
}
```

---

### 2. Use `\n` instead of `endl`.
Reason: The `endl` object flushes the console after creating a new line, forcing the output, which is often unnecessary and slower than `\n`, which just creates a new line.

---

### 3. Use `switch` instead of `else-if` chains when you have many cases with discrete values.
Reason: A `switch` is sometimes faster than a chain of `else-if` statements because the compiler can perform jumps between cases, making the time to reach the first or last case roughly the same. In `else-if`, each condition is evaluated sequentially. It also improves readability.

---

### 4. Use the `int` type whenever possible.
Reason: The size of int is usually best suited to the architecture of most processors, so operations are normally faster. It’s often a good general choice.

---

### 5. Use pre-increment instead of post-increment with objects.
Reason: Post-increment has an extra step because a temporary copy of the object is created.

> *Why does post-increment perform a temporary copy?*
```cpp
int main() {
    int n = 5;
    std::cout << n++; // Output is 5
    // From now on n is 6
    std::cout << n; // Output is 6
}
```
This is what post-increment has conceptually done (under the hood):
```cpp
int tmp = n;
n = n + 1;
return tmp;
```
Now imagine we used this operation for a whole object, whose size is large and contains various fields, we would've had to entirely copy it for nothing.

---

### 6. Place comments above lines if they are very long.
Reason: Comments next to the code can sometimes become verbose and hard to read.

---

### 7. Declare the size of a static collection as a constant.
Reason: The size of a static collection does not change once instantiated, so it is semantically correct to define it as a constant.

---

### 8. Use `emplace_back` instead of `push_back` when you are constructing an object in-place or when the value is a temporary, especially for complex objects.
Reason: `emplace_back` avoids creating a temporary copy when you are directly creating the object in the container.

---

### 9. Use `size_t` for container indices and sizes.
Reason: `size_t` is a large unsigned type designed for this purpose (just be careful when comparing with negative values).

---

### 10. Mark ANY symbol that never changes as `const` (Const-correctness)
Reasons:
  - Makes the code safer since it stops you from mutating symbols that are not supposed to be modified.
  - The compiler can sometimes make certain optimizations if it knows a symbol won’t change.

---

### 11. Store constant references when iterating in a collection of complex objects through a range-based for loop.
Reason: range-based for loops create a copy of every element. Using a `const` reference prevents modification of the elements in the loop and avoids unnecessary copies.
```cpp
std::vector<std::string> v{};

for (const auto& s : v) {
    // ...
}
```

---

### 12. Use `constexpr` for compile-time constants instead of macros.
Reason: macros are untyped and use text substitution, while `constexpr` provides type safety, scope, and real compile-time evaluation.

---

### 13. Prefer range-based for loops for all iterable containers when you don't need the index.
Reason: clearer syntax, less error-prone.
```cpp
for (auto n : numbers) {
    std::cout << n << '\n';
}
```

---

### 14. Use `nullptr` instead of `NULL`.
Reason: `NULL` is a C macro typically defined as `0`. Since it lacks **type-safety** it can cause unexpected overload resolution.

Example:
```cpp
void func(int n) { /* ... */ }
void func(int* n) { /* ... */ }

func(NULL); // Intended: func(int*)
```

After macro substitution, the compiler effectively sees:
```cpp
func(0);
```

Since `0` is an integral data type, overload resolution selects:
```cpp
func(int);
```

which may surprise the programmer.

`nullptr` has its own type (`std::nullptr_t`) and is implicitly convertible only to pointer types:
```cpp
func(nullptr); // Correctly calls func(int*)
```

---

### 15. Pass large objects by constant references.
Reason: Avoids copying the entire object.

```cpp
void func(const std::string& s); // ✅
void func(std::string s); // ❌
```

---

### 16. Prefer `std::array` or `std::vector` over raw arrays.
Avoid:
```cpp
int arr[10];
```
Better:
```cpp
std::array<int, 10> arr;
std::vector<int>arr[10];
```
Reason(s):
- Size is known
- STL compatible

---

### 17. Use **Uniform Initialization** over assignments.
Reason(s):
- Prevents uninitialized variables.
- Avoids narrowing conversions:
```cpp
int x{3.5}; // Error
int x = 3.5; // Silently becomes 3
```

---

### 18. Use `auto` when the data type is verbose.
```cpp
std::vector<std::string> v(5);

auto it = v.begin(); // Better
std::vector<std::string>::iterator it = v.begin(); // Unnecessarily complex
```
