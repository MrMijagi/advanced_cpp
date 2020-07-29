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
