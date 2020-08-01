### `std::shared_ptr<>`

<div style="width: 70%; margin: 0 auto">

Traits:

* <!-- .element: class="fragment fade-in" --> one object == multiple owners
* <!-- .element: class="fragment fade-in" --> last referrer destroys the object
* <!-- .element: class="fragment fade-in" --> copying allowed
* <!-- .element: class="fragment fade-in" --> moving allowed
* <!-- .element: class="fragment fade-in" --> can use custom deleter
* <!-- .element: class="fragment fade-in" --> can use custom allocator

</div>

<img src="img/sharedptr1.png" data-src="img/sharedptr1.png" alt="shared pointers" class="plain fragment fade-in">

___

### `std::shared_ptr<>` usage

* Copying and moving is allowed

<div class="multicolumn" style="font-size: 90%">
<div class="col">

```cpp
std::shared_ptr<MyData> source();
void sink(std::shared_ptr<MyData> ptr);

void simpleUsage() {
    source();
    sink(source());
    auto ptr = source();
    sink(ptr);
    sink(std::move(ptr));
    auto p1 = source();
    auto p2 = p1;
    p2 = std::move(p1);
    p1 = p2;
    p1 = std::move(p2);
}

```

</div>

<div class="col">

```cpp
std::shared_ptr<MyData> source();
void sink(std::shared_ptr<MyData> ptr);

void collections() {
    std::vector<std::shared_ptr<MyData>> v;

    v.push_back(source());

    auto tmp = source();
    v.push_back(tmp);
    v.push_back(std::move(tmp));

    sink(v[0]);
    sink(std::move(v[0]));
}
```

</div>

___

### `std::shared_ptr<>` usage

<div style="font-size: 80%; width: 100%; margin: 0 auto">

```cpp
#include <memory>
#include <map>
#include <string>

class Gadget {};
std::map<std::string, std::shared_ptr<Gadget>> gadgets; // it wouldn't compile with C++03. Why?

void foo() {
    std::shared_ptr<Gadget> p1{new Gadget()};           // reference counter = 1
    {
        auto p2 = p1;                                   // shared_ptr copy (reference counter == 2)
        gadgets.insert(make_pair("mp3", p2));           // shared_ptr copy (reference counter == 3)
        p2->use();
    }                                                   // destruction of p2, reference counter = 2
}                                                       // destruction of p1, reference counter = 1

gadgets.clear();                                        // reference counter = 0 - gadget is removed
```

</div>

___

#### `std::shared_ptr<>` cyclic dependencies

* What happens here?

<div class="multicolumn" style="font-size: 85%">
<div class="col">

```cpp
#include <memory>

struct Node {
    std::shared_ptr<Node> child;
    std::shared_ptr<Node> parent;
};

int main () {
    auto root = std::shared_ptr<Node>(new Node);
    auto child = std::shared_ptr<Node>(new Node);

    root->child = child;
    child->parent = root;
}


```

</div>

<div class="col" style="margin-top: 30px;">
    <div style="margin: 0 auto">Memory leak!</div>
    <img data-src="img/kot.jpg" src="img/kot.jpg" alt="kot" class="plain" style="height: 70%">
    
</div>
<!-- .element: class="fragment fade-in" -->
