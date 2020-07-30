# Best practices

<div class="multicolumn" style="height: 400px; position: relative;">
<div class="col">
    <div style="position: absolute; bottom: 0">
        <img height=200 data-src="img/logo.png" src="img/logo.png" alt="Coders School" class="plain" style="margin-bottom: 0">
    </div>
</div>

<div class="col">
</div>

___

## Best practices

* <!-- .element: class="fragment fade-in" --> Rule of 0, Rule of 5
* <!-- .element: class="fragment fade-in" --> Avoid explicit <code>new</code>
* <!-- .element: class="fragment fade-in" --> Use <code>std::make_shared()</code> / <code>std::make_unique()</code>
* <!-- .element: class="fragment fade-in" --> Copying <code>std::shared_ptr<></code>
* <!-- .element: class="fragment fade-in" --> Use references instead of pointers

___

## Rule of 0, Rule of 5

<p style="margin: 5">Rule of 5:</p><!-- .element: class="fragment fade-in" -->

<ul style="margin-left: 50px">
    <li>If you need to implement one of those functions:</li><!-- .element: class="fragment fade-in" -->
    <ul>
        <li>destructor</li><!-- .element: class="fragment fade-in" -->
        <li>copy constructor</li><!-- .element: class="fragment fade-in" -->
        <li>copy assignment operator</li><!-- .element: class="fragment fade-in" -->
        <li>move constructor</li><!-- .element: class="fragment fade-in" -->
        <li>move assignment operator</li><!-- .element: class="fragment fade-in" -->
    </ul>
    <li>It probably means that you should implement them all, because you have manual resources management.</li><!-- .element: class="fragment fade-in" -->
</ul>

<p style="margin: 5">Rule of 0:</p><!-- .element: class="fragment fade-in" -->

<ul style="margin-left: 50px">
    <li>If you use RAII wrappers on resources, you don’t need to implement any of Rule of 5 functions.</li><!-- .element: class="fragment fade-in" -->
</ul>

___

## Avoid explicit `new`

* <!-- .element: class="fragment fade-in" --> Smart pointers eliminate the need to use <code>delete</code> explicitly
* <!-- .element: class="fragment fade-in" --> To be symmetrical, do not use <code>new</code> as well
* <!-- .element: class="fragment fade-in" --> Allocate using:
  * <!-- .element: class="fragment fade-in" --> <code>std::make_unique()</code>
  * <!-- .element: class="fragment fade-in" --> <code>std::make_shared()</code>

___

### Use std::make_shared() / std::make_unique()

* <!-- .element: class="fragment fade-in" --> What is a problem here?

```cpp
struct MyData { int value; };
using Ptr = std::shared_ptr<MyData>;
void sink(Ptr oldData, Ptr newData);

void use(void) {
    sink(Ptr{new MyData{41}}, Ptr{new MyData{42}});
}
```
<!-- .element: class="fragment fade-in" style="font-size: 50%" -->

* <!-- .element: class="fragment fade-in" --> Hint: this version is not problematic

```cpp
struct MyData { int value; };
using Ptr = std::shared_ptr<MyData>;
void sink(Ptr oldData, Ptr newData);

void use(void) {
    Ptr oldData{new MyData{41}};
    Ptr newData{new MyData{42}};
    sink(std::move(oldData), std::move(newData));
}
```
<!-- .element: class="fragment fade-in" style="font-size: 50%" -->

___

### Use std::make_shared() / std::make_unique()

`auto p = new MyData(10);` means:

* <!-- .element: class="fragment fade-in" --> allocate <code>sizeof(MyData)</code> bytes
* <!-- .element: class="fragment fade-in" --> run <code>MyData</code> constructor
* <!-- .element: class="fragment fade-in" --> assign address of allocated memory to <code>p</code>

Order of evaluation of the operands of almost all C++ operators (including the order of
evaluation of function arguments in a function-call expression and the order of evaluation of
the subexpressions within any expression) is **unspecified**.
<!-- .element: class="fragment fade-in box" style="font-size: 80%; background-color: #660000"-->

<!-- I divided this slide into two because there was too much content -->

___

### Use std::make_shared() / std::make_unique()

* <!-- .element: class="fragment fade-in" --> How about two such operations?

<div class="fragment fade-in" style="font-size: 70%; margin: 25px">

<table>
    <tr>
        <td>(1) allocate <code>sizeof(MyData)</code> bytes</td>
        <td>(1) allocate <code>sizeof(MyData)</code> bytes</td>
    </tr>
    <tr>
        <td>(2) run <code>MyData</code> constructor</td>
        <td>(2) run <code>MyData</code> constructor</td>
    </tr>
    <tr>
        <td>(3) assign address of allocated memory to <code>p</code></td>
        <td>(3) assign address of allocated memory to <code>p</code></td>
    </tr>
</table>

</div>

* <!-- .element: class="fragment fade-in" --> Unspecified order of evaluation means that order can be for example 1,2,1,2,3,3.
* <!-- .element: class="fragment fade-in" --> What if second 2 throws an exception?

___

### Use std::make_shared() / std::make_unique()

* <!-- .element: class="fragment fade-in" --> <code>std::make_shared()</code> / <code>std::make_unique()</code> resolves this problem

```cpp
struct MyData{ int value; };
using Ptr = std::shared_ptr<MyData>;
void sink(Ptr oldData, Ptr newData);

void use() {
    sink(std::make_shared<MyData>(41), std::make_shared<MyData>(42));
}
```
<!-- .element: class="fragment fade-in" --> 

* <!-- .element: class="fragment fade-in" --> Fixes previous bug
* <!-- .element: class="fragment fade-in" --> Does not repeat a constructed type
* <!-- .element: class="fragment fade-in" --> Does not use explicit <code>new</code>
* <!-- .element: class="fragment fade-in" --> Optimizes memory usage (only for <code>std::make_shared()</code>)

___

## Copying std::shared_ptr<>

```cpp
void foo(std::shared_ptr<MyData> p);

void bar(std::shared_ptr<MyData> p) {
    foo(p);
}
```

* <!-- .element: class="fragment fade-in" --> requires counters incrementing / decrementing
* <!-- .element: class="fragment fade-in" --> atomics / locks are not free
* <!-- .element: class="fragment fade-in" --> will call destructors

##### Can be better?
<!-- .element: class="fragment fade-in" -->

___

## Copying std::shared_ptr<>

```cpp
void foo(const std::shared_ptr<MyData> & p);

void bar(const std::shared_ptr<MyData> & p) {
    foo(p);
}
```

* <!-- .element: class="fragment fade-in" --> as fast as pointer passing
* <!-- .element: class="fragment fade-in" --> no extra operations
* <!-- .element: class="fragment fade-in" --> not safe in multithreaded applications

___

### Use references instead of pointers

* <!-- .element: class="fragment fade-in" style="margin: 20px" --> What is the difference between a pointer and a reference?
  * <!-- .element: class="fragment fade-in" --> reference cannot be empty
  * <!-- .element: class="fragment fade-in" --> once assigned cannot point to anything else
* <!-- .element: class="fragment fade-in" style="margin: 20px" --> Priorities of usage (if possible):
  * <!-- .element: class="fragment fade-in" --> <code>(const) T&</code>
  * <!-- .element: class="fragment fade-in" --> <code>std::unique_ptr&ltT&gt</code>
  * <!-- .element: class="fragment fade-in" --> <code>std::shared_ptr&ltT&gt</code>
  * <!-- .element: class="fragment fade-in" --> <code>T*</code>

___

## Exercise: List

Take a look at `List.cpp` file, where simple (and buggy) single-linked list is implemented.
`void add(Node* node)` method adds a new `Node` at the end of the list.
`Node* get(const int value)` method iterates over the list and returns the first Node with matching `value` or `nullptr`

1. <!-- .element: class="fragment fade-in" --> Compile and run List application
2. <!-- .element: class="fragment fade-in" --> Fix memory leaks without introducing smart pointers
3. <!-- .element: class="fragment fade-in" --> Fix memory leaks with smart pointers. What kind of pointers needs to be applied and why?
4. <!-- .element: class="fragment fade-in" --> What happens when the same Node is added twice? Fix this problem.
5. <!-- .element: class="fragment fade-in" --> (Optional) Create <code>EmptyListError</code> exception (deriving from <code>std::runtime_error</code>). Add throwing and catching it in a proper places.
