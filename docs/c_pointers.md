# Pointers in C

## What a pointer really is
A pointer is just a variable that stores a memory address.
```c
int x = 42;
int *p = &x;
```
- `x`: an `int` value (`42`)
- `&x`: the **address** where `x` lives
- `p`: a variable that stores that address

So:
```
x:   42
p:   0x7ffeefbff59c   (example address)
```

## The two magic operators
**`&`: address-of**

“*Where does this live?*”
```c
&p   // address of the pointer itself
&x   // address of x
```
**`*`: dereference**

“*Go to the address and give me what’s there*”
```c
*p   // value at the address p points to
```
Example:
```c
printf("%d\n", *p);  // prints 42
```

## Pointer types matter (a LOT)
```c
int    *pi;
double *pd;
char   *pc;
```
All of these:
- store addresses
- but mean different things

**Why?**

Because C needs to know:
1. How many bytes to read/write
2. How pointer arithmetic works

## Pointer arithmetic (the sneaky part)
```c
int arr[4] = {10, 20, 30, 40};
int *p = arr;
```
Memory (simplified):
```c
arr[0] arr[1] arr[2] arr[3]
 10     20     30     40
 ^p
```
Now:
```c
p + 1   // moves 4 bytes (sizeof int)
p + 2   // moves 8 bytes
```
So:
```c
*(p + 2) == arr[2] == 30
```

## Arrays and pointers (they’re related, not identical)
**Key rule:** In most expressions, an array **decays** into a pointer to its first element.
```c
int arr[3];
int *p = arr;   // same as &arr[0]
```
But:
```c
sizeof(arr)  // size of entire array
sizeof(p)    // size of pointer
```
**Different things.**

## Pointers and functions (why they exist)
C passes arguments **by value**.

So this doesn’t work:
```c
void swap(int a, int b) {
    int tmp = a;
    a = b;
    b = tmp;
}
```
But this does:
```c
void swap(int *a, int *b) {
    int tmp = *a;
    *a = *b;
    *b = tmp;
}
```
Call it like this:
```c
swap(&x, &y);
```
Pointers let functions **modify the caller’s data**.

## NULL pointers (important safety concept)
```c
int *p = NULL;
```
Means: “*This pointer points to nothing.*”

Dereferencing it:
```c
*p   // undefined behavior
```
Always check before using:
```c
if (p != NULL) {
    ...
}
```

## Pointers and dynamic memory
```c
int *p = malloc(sizeof *p);
```
- `p` gets an address on the heap
- You own that memory
- You must free it
```c
free(p);
p = NULL;
```
Classic rule:

**malloc**-> **free exactly once**

## Common beginner mistakes
Using uninitialised pointers
```c
int *p;
*p = 10;  // bad
```
Confusing `*p` and `p`
```c
p = 10;   // wrong
*p = 10;  // correct
```
Printing pointers incorrectly
```c
printf("%d\n", p);  // wrong
printf("%p\n", (void *)p);  // correct
```

## How to read pointer declarations (life-saving trick)
Read right-to-left:
```c
int *p;        // p is a pointer to int
int **pp;      // pp is a pointer to pointer to int
int (*fp)();   // fp is a pointer to function
```

## Double pointers (`int **`)
An `int **` is: a pointer that points to another pointer that points to an `int`
```c
int x = 10;
int *p = &x;
int **pp = &p;
```
Memory picture
```
x   = 10
p   = &x
pp  = &p
```
Visually:
```css
pp ──▶ p ──▶ x
          10
```
How dereferencing works (layer by layer)
```c
pp        // address of p
*pp       // p (address of x)
**pp      // x (10)
```
Each `*` removes one level of indirection.

## Why double pointers exist (the real reason)

### Modify a pointer itself inside a function
Remember: C passes arguments **by value**.

This fails:
```c
void alloc(int *p) {
    p = malloc(sizeof *p);
}

int *x = NULL;
alloc(x);   // x is still NULL
```
Because you modified a *copy* of the pointer.

Correct version: use `int **`
```c
void alloc(int **p) {
    *p = malloc(sizeof **p);
}

int *x = NULL;
alloc(&x);  // now points to allocated memory
```
Why it works:
```c
&x  →  int **
*p  →  int *
**p →  int
```

### Dynamic 2D arrays
```c
int **matrix;
```
Often used like:
```sql
matrix
 ├─▶ row 0 ─▶ ints
 ├─▶ row 1 ─▶ ints
 └─▶ row 2 ─▶ ints
```
Each row can be allocated independently.

## Function pointers (the “what the hell?” pointer)

**What is a function pointer?**

A function pointer stores **the address of a function**.
```c
int add(int a, int b) {
    return a + b;
}
```
Pointer to it:
```c
int (*fp)(int, int) = add;
```
Parentheses matter!

Without them:
```c
int *fp(int, int); // function returning int*
```

**Calling through a function pointer**
```c
int result = fp(2, 3);   // 5
```
You can also write:
```c
(*fp)(2, 3);
```
Both are equivalent.

**Why function pointers exist (very important)**
### Callbacks
```c
void apply(int *arr, int n, int (*fn)(int)) {
    for (int i = 0; i < n; i++)
        arr[i] = fn(arr[i]);
}
```
Usage:
```c
int square(int x) { return x * x; }

apply(arr, 5, square);
```
This is how:
- `qsort`
- signal handlers
- event systems
- plugins
work in C.

### Replacing if / switch with behavior
```c
int add(int a, int b) { return a + b; }
int sub(int a, int b) { return a - b; }

int (*ops[])(int, int) = { add, sub };

printf("%d\n", ops[0](5, 3)); // 8
printf("%d\n", ops[1](5, 3)); // 2
```
Boom - jump table.

### Standard library usage
```c
qsort(array, n, sizeof *array, compare);
```
Where `compare` is a function pointer.

## Function pointers vs data pointers
Important rule:
- `void *` works for **data pointers**
- `void *` does **NOT** work for function pointers

This is **illegal** / **undefined**:
```c
void *p = add;
```
Function pointers are a separate category in C.
