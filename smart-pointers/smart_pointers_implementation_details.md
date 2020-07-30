# Implementation details

<div class="multicolumn" style="height: 400px; position: relative;">
<div class="col">
    <div style="position: absolute; bottom: 0">
        <img height=200 data-src="img/logo.png" src="img/logo.png" alt="Coders School" class="plain" style="margin-bottom: 0">
    </div>
</div>

<div class="col">
</div>

___

### Implementation details – `std::unique_ptr<>`

* <!-- .element: class="fragment fade-in" --> Just a holding wrapper
* <!-- .element: class="fragment fade-in" --> Holds an object pointer
* <!-- .element: class="fragment fade-in" --> Constructor copies a pointer
* <!-- .element: class="fragment fade-in" --> Call proper delete in destructor
* <!-- .element: class="fragment fade-in" --> No copying
* <!-- .element: class="fragment fade-in" --> Moving means:
  * <!-- .element: class="fragment fade-in" --> Copying original pointer to a new object
  * <!-- .element: class="fragment fade-in" --> Setting source pointer to nullptr
* <!-- .element: class="fragment fade-in" --> All methods are inline

___

### Implementation details – `std::shared_ptr<>`

<div class="multicolumn">
<div class="col" style="margin-top: 20px">
    <ul>
        <li>Holds an object pointer</li><!-- .element: class="fragment fade-in" --> 
        <li>Holds 2 reference counters:</li><!-- .element: class="fragment fade-in" --> 
        <ul>
            <li>shared pointers count</li><!-- .element: class="fragment fade-in" --> 
            <li>weak pointers count</li><!-- .element: class="fragment fade-in" --> 
        </ul>
    </ul>
</div>
<div class="col">
    <img data-src="img/sharedptr2.png" src="img/sharedptr2.png" alt="sharedptr2" class="plain">
</div>
</div>

* <!-- .element: class="fragment fade-in" --> Destructor:
  * <!-- .element: class="fragment fade-in" --> decrements <code>shared-refs</code>
  * <!-- .element: class="fragment fade-in" --> deletes user data when <code>shared-refs == 0</code>
  * <!-- .element: class="fragment fade-in" --> deletes reference counters when <code>shared-refs == 0</code> and <code>weak-refs == 0</code>
* <!-- .element: class="fragment fade-in" --> Extra space for a deleter
* <!-- .element: class="fragment fade-in" --> All methods are inline

___

### Implementation details – `std::shared_ptr<>`

<div class="multicolumn" style="position: relative">
<div class="col" style="font-size: 100%; width: 40%; flex: none">

* <!-- .element: class="fragment fade-in" style="margin-top: 20px" --> Copying means:
  * <!-- .element: class="fragment fade-in" --> Copying pointers to the target
  * <!-- .element: class="fragment fade-in" --> Incrementing <code>shared-refs</code>

* <!-- .element: class="fragment fade-in" style="margin-top: 60px" --> Moving means:
  * <!-- .element: class="fragment fade-in" --> Copying pointers to the target
  * <!-- .element: class="fragment fade-in" --> Setting source pointers to <code>nullptr</code>

</div>

<div class="col" style="width: 60%">

<img data-src="img/sharedptr3.png" src="img/sharedptr3.png" alt="sharedptr3" class="plain">
<img data-src="img/sharedptr4.png" src="img/sharedptr4.png" alt="sharedptr4" class="plain">

</div>
</div>

___

### Implementation details – `std::weak_ptr<>`

<div class="multicolumn">
<div class="col" style="margin-top: 20px">

* <!-- .element: class="fragment fade-in" --> Holds an object pointer
* <!-- .element: class="fragment fade-in" --> Holds 2 reference counters:
  * <!-- .element: class="fragment fade-in" --> shared pointers count
  * <!-- .element: class="fragment fade-in" --> weak pointers count

</div>
<div class="col">
    <img data-src="img/sharedptr5.png" src="img/sharedptr5.png" alt="sharedptr5" class="plain" height=90%>
</div>
</div>

</br>

* <!-- .element: class="fragment fade-in" --> Destructor:
  * <!-- .element: class="fragment fade-in" --> decrements <code>weak-refs</code>
  * <!-- .element: class="fragment fade-in" --> deletes reference counters when <code>shared-refs == 0</code> and <code>weak-refs == 0</code>

___

### Implementation details – `std::weak_ptr<>`

<div class="multicolumn" style="position: relative">
<div class="col" style="font-size: 100%; width: 40%; flex: none">

* <!-- .element: class="fragment fade-in" style="margin-top: 20px" --> Copying means:
  * <!-- .element: class="fragment fade-in" --> Copying pointers to the target
  * <!-- .element: class="fragment fade-in" --> Incrementing <code>weak-refs</code>

* <!-- .element: class="fragment fade-in" style="margin-top: 60px" --> Moving means:
  * <!-- .element: class="fragment fade-in" --> Copying pointers to the target
  * <!-- .element: class="fragment fade-in" --> Setting source pointers to <code>nullptr</code>

</div>

<div class="col" style="width: 60%">

<img data-src="img/sharedptr6.png" src="img/sharedptr6.png" alt="sharedptr6" class="plain" height=35%>
<img data-src="img/sharedptr7.png" src="img/sharedptr7.png" alt="sharedptr7" class="plain" height=35%>

</div>
</div>

___

### `std::weak_ptr<>` + `std::shared_ptr<>`

* <!-- .element: class="fragment fade-in" --> Having a shared pointer and a weak pointer

<div>
<img data-src="img/sharedptr8.png" src="img/sharedptr8.png" alt="sharedptr8" class="plain">
</div><!-- .element: class="fragment fade-in" -->

* <!-- .element: class="fragment fade-in" --> After removing the shared pointer

<div>
<img data-src="img/sharedptr9.png" src="img/sharedptr9.png" alt="sharedptr9" class="plain">
</div><!-- .element: class="fragment fade-in" -->

___

## Making a `std::shared_ptr<>`

* <!-- .element: class="fragment fade-in" --> <code>std::shared_ptr&ltData&gt p{new Data};</code>

<div>
<img data-src="img/sharedptr10.png" src="img/sharedptr10.png" alt="sharedptr10" class="plain">
</div><!-- .element: class="fragment fade-in" -->

* <!-- .element: class="fragment fade-in" --> <code>auto p = std::make_shared&ltData&gt();</code>
  * <!-- .element: class="fragment fade-in" --> Less memory (most likely)
  * <!-- .element: class="fragment fade-in" --> Only one allocation
  * <!-- .element: class="fragment fade-in" --> Cache-friendly

<div>
<img data-src="img/sharedptr11.png" src="img/sharedptr11.png" alt="sharedptr11" class="plain">
</div><!-- .element: class="fragment fade-in" -->