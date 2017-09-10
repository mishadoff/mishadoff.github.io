---
layout: post
title: "Data Structures"
date: 2017-08-29 17:24
comments: true
categories: [java, data, programming, algorithms]
sharing: true
published: true
mathjax: true
---

In this post we discuss the evolution of Data Structures and try to develop everything from scratch.

<!-- more -->

### Index
	
- [Intro](#intro)
- [0. Cell](#cell)
- [1. Array](#array)
- [2. String](#string)
- [3. Record](#record)
- [4. Node](#node)
- [5. Linked List](#linked-list)
- [6. Doubly Linked List](#doubly-linked-list)
- [7. Dynamic Array](#dynamic-array)
- [8. Map](#map)
- [9. Set](#set)
- [10. MultiSet](#multiset)
- [11. MultiMap](#multimap)
- [12. BiMap](#bimap)
- [13. LinkedMap](#linkedmap)
- [14. Queue](#queue)
- [15. Stack](#stack)
- [16. Deque](#deque)
- [17. Circular Buffer](#circular-buffer)
- [18. Binary Tree](#binary-tree)
- [19. Binary Tree Array](#binary-tree-array)


- [20. Binary Search Tree](#binary-search-tree)


- [Matrix]()
- [Bip Buffer]()
- [Priority Queue]()
- [Tree](#tree)
- [Prefix Tree](#prefix-tree)
- [Graph](#graph)
- [Other Trees](#trees)
- [Partially Ordered Set aka Lattice](#lattice)
- [Persistent Data Structures](#psd)
- [Functional Data Structures](#fds)
- [Lock Free DS](#psds)
- [Wait Free DS](#wfds)

### Intro

> Algorithms + Data Structures = Programs  
> -- Niklaus Wirth

Algorithms are important in our programs.

Some algorithms are better than others.  
For example, take [Bubble Sort](https://en.wikipedia.org/wiki/Bubble_sort), it's goal to sort the array.
[Insertion Sort](https://en.wikipedia.org/wiki/Insertion_sort) can sort the same array, but faster.
We would say that insertion sort is better than bubble sort, or _more efficient_.

Some algorithms are better than others in different circumstances.  
For example, if you have a tree with high branching factor and short depth and you need to find specific element in the tree, [Depth First Search](https://en.wikipedia.org/wiki/Depth-first_search) is preffered over [Breadth First Search](https://en.wikipedia.org/wiki/Breadth-first_search). Both algorithms are interchangable, but one is _more efficient_ than other by given additional information.

Some algorithms are applicable only if additional requirements for input holds.  
For example, [Binary Search](https://en.wikipedia.org/wiki/Binary_search_algorithm) can find element in the array much faster than [Linear Search](https://en.wikipedia.org/wiki/Linear_search) if input array is sorted.

And so on.

If algorithm is a _sequence of actions_, data structure is a _way of organizing data_. Access to such data often controlled by algorithms. Data structures use algorithms and algorithms use data structures, they always live together.

Some data structures are more efficient than others.  
For example, Array is better than Linked List for random access and iteration.
Or if you have data, pairs of keys and values, you can use associative arrays but not binary heaps. Or if you need LIFO access you can use Stack. Or...

Wait, let's go back to the beginning and see where it started.

### 0. Cell

If you need a structure for data, you need data, right?  
What is data?  

It has a lot of definitions, including some philosophical, but as we starting from scratch, we stick to the simplest one. Data is a set of `1` or `0`.

Really, if you take a snapshot of your RAM it has only `1` and `0`.  
Lot of them.  
Like this.

```
011000010110110001101100011110010110111101110101
011100100110010001100001011101000110000101100001
011100100110010101100010011001010110110001101111
011011100110011101110100011011110111010101110011
```

This is a good starting point, because at the end data will look the same.

Each `1` or `0` is called **bit**.  
So, `1` is a bit, `0` is a bit, `10` are two bits.

People are smart, so they took group of eight bits and called them a **byte**. 

![](/images/ds/hobbit.jpg)

```
01100001 01101100 01101100 01111001 01101111 01110101
01110010 01100100 01100001 01110100 01100001 01100001
01110010 01100101 01100010 01100101 01101100 01101111
01101110 01100111 01110100 01101111 01110101 01110011
```

This is how first **data type** was born.

We can create a variable of `byte` type and assign some value to it.

``` java
byte a = 97; // 01100001
```

Under the hood, machine transforms your value `97` into `01100001` (because of binary system) and store only zeros and ones. Now, when we have a byte, we can forget about bits. No more zeros and ones.

More data types could be produced by combining more bytes. For example, Java has a data type `int` which takes 4 bytes and `double` which takes 8 bytes.

``` java
int b = 20457823; // 00000001 00111000 00101001 01011111
double c = 3.14;  // 01000000 00001001 00011110 10111000 01010001 11101011 10000101 00011111
```

Data type which represented in terms of bytes is called a **primitive type**. The cell in memory, which holds one value of primitive type is our basic data structure, which we call just **Cell**.

Many may argue that cell in the memory is not data structure, but wait, it holds the data (1) and provide two methods to this data: `read` and `write` (2).

``` java
int x = 10;    // write value 10 to cell x
int y = 20;    // write value 20 to cell y
int z = x + y; // read value from cell x, read value from cell y, add them and write to cell z
```

To refer the cell we could take our memory and say, take the value of the cell, located at adress `00187424`. But we don't care too much about physical address of this cell, we just need a reference to it. So we introduced **variables**.

When you define a variable, like `int x`, compiler finds free region of memory which can contain 4 bytes (to hold the int type), return the address of this memory region and assign alias to it called `x`. Later, when you write to `x`, you actually write to specific adress in memory.

### 1. Array

All of us familliar with the arrays.  
Really, in previous paragraph we discussed RAM, data structure which holds all your data. RAM is actually a huge array of bytes and the size of this array limited to your RAM size.

But we can create our own arrays.

``` java
int[] array = new int[3];
```

We created an array of size `3`, which holds `int` values and assign it to variable `array`, so we can refer to it later.

Few important notes here.

`3` is an input parameter for array.
You can create array, only if you can provide its size.
We will see later that most datastructures require some input parameters for initialization

Such array is always static and it always holds three elements, not less, not more. Some of them may be unitialized or zeros or undefined, but always three. If you need 4 elements, create new array with input size 4.

Array provides access to its cells by **index**. It's like address for RAM.
To refer to the first element you write `array[0]`, second `array[1]` and so on. (indexing starts from zero in Java and most of the languages)

If you refer to element, which is not available in array, like `array[643]`, you will get an error.

To simulate the previous example with cell and three variables, we can use arrays. 

``` java
array[0] = 10; // refer to the cell 0 in array, and write 10 to it
array[1] = 20; // refer to the cell 1 in array, and write 20 to it
array[2] = array[0] + array[1]; // read cell 0, read cell 1, add them and write to cell 2
```

As you can see, all methods available for cells, are available for array cells as well (`read`/`write`) if you dereferred them by index.

Also, array has one additional method `array.length` which returns size of array.

Having this length, we can iterate over array and print every element.

``` java
for (int i = 0; i < array.length; i++) {
    System.out.println(array[i]);
}
```

### 2. String

String is a partial case of array, where all elements are characters.  
The type of array is always `char`.

``` java
char[] chars = new char[3];
chars[0] = 'h';
chars[1] = 'e';
chars[2] = 'y';
```

When you need to print such string, you print every character in a loop

``` java
for (int i = 0; i < chars.length; i++) {
  System.out.print(chars[i]);
}
```

But string is so popular in programming, so instead of working with it as with array, it has it's own data type, called `String`.

``` java
class MyString {
    private char[] chars;

    public MyString(char[] chars) {
        this.chars = Arrays.copyOf(chars, chars.length);
    }

    public int length() {
        return chars.length;
    }
}
```

Every time you create a string, you pass array of characters to constructor as an initialization `public String(char[] chars)`. This array copied to another internal array which represent the string, but it contents hidden from outside world, only type `String` can access it.

There are no methods which expose single characters of this string, so you can not read them or write to them. Such data structures are called **immutable**, once you created them you can't change them.

Let's add method `print` to be able see contents of string.

``` java
public void print() {
    for (int i = 0; i < chars.length; i++) {
        System.out.print(chars[i]);
    }
    System.out.println();
}
```

And see what happens if we change char array content after we create a string from it.

``` java
MyString s1 = new MyString(chars); // create a string from char array 'h', 'e', 'y'
s1.print();                        // string is "hey"
chars[2] = 'x';                    // change last char in array to 'x'
MyString s2 = new MyString(chars); // create new string from char array
s2.print();                        // new string is "hex"
s1.print();                        // old string is still "hey"
```

The key point here is _string like char array, but immutable_. Immutability do not prevents
exposing contents for reading so we do. Add such method to expose single char.

``` java
public char charAt(int i) {
    return chars[i];
}
```

Keep in mind, it returns you a value of the single char in string, not a reference to it.
Again, write is prohibited.

Real `String` implementations are much complex than this, but you got the idea.

When we have strings, life is much easier!

### 3. Record

So far we have seen two basic data structures: Cell, which contains single value of some type and array, which contains _multiple_ values of the _same type_.

And here is **record**. Some languages refer to record as object or tuple, but basically all they are the same things.

Record is a _container_ for multiple values of different types.

``` java
class Person {
    int age;
    String name;
}
```

In Java `class` keyword defines a new class of objects (or records) as a data type.
In example above we defined new class of records, called a `Person`, and it is a record which contains two fields: person name, and person age.

``` java
Person p1 = new Person();                    // create empty record Person called p1
p1.name = "John";                            // set p1 name to "John"
p1.age = 21;                                 // set p1 age to 21
System.out.println(p1.name + " " + p1.age);  // read both name and age from p1
```

Record is a simple data structure but important building block in every programming language. If record will contain only single value, it has similar properties to cell. If records has multiple elements of the same type it has similar properties to array. All future data structures will be based on records.

### 4. Node

**Node** is another building block for bunch of data structures.

In a nutshell, node is a record, which contains two fields.
First field is so called _data_ field, it can contain a cell, string or any other record.
Second field is a _reference_ field to record of the same type `Node`.

How that's even possible? Record contain itself?

``` java
class Node {
    String data;     // effective information
    Node reference;  // reference to another node
}
```

Such trick called **recursive data type**,
the definition of the `Node` based on `Node` itself.

You can use it the same way as records

``` java
Node n1 = new Node();      // create empty record n1
n1.data = "Suspect #1";    // set n1.data to "Suspect #1"
Node n2 = new Node();      // create empty record n2
n2.data = "Suspect #2";    // set n2.data to "Suspect #2"
n1.reference = n2;         // Important!  n1 refers to n2
```

Assume you are detective and want to solve a crime scene.
You have contacts only with suspect #1 (`Node n1`).
If you have enough detective skills you can talk to him and he will expose to you
another suspect by `n1.reference`. As it equals to `n2` you move forward to talk to `n2`.

When suspect has no information about additional suspects, his reference field is equal to `null`, which is special marker to denote _absence of any value_. So if you see `null`, you see nothing.

Our `Node` class can contain only strings as a data.
To make it more usable in the future, let's make it _generic_.

Java has a feature called Generics, so you can make your records typed.

``` java
public class Node<T> {
    public T data;
    public Node<T> next;

    public Node(T data) {
        this.data = data;
    }
}
```

If you want int array you define it as `int[]`,
if you want char array you write `char[]`, the same applies for typed nodes.

```
Node<String> sNode = new Node<>("Suspect #1");
Node<Integer> iNode = new Node<>(2500);
sNode.reference = iNode  // error here!
```

If you defined `String` node, you can add string to it, but not integer.
If you defined `Integer` node, you can add int to it, but not string.

And lastly you can't refer from String node to Integer or vice versa. This could be both good and bad design choice depending on your needs, but this is how generics work.

### 5. Linked List

Node is so powerful data structure that allows you to build **Linked List**, container for multiple values and the most classic example of data structures.
It will be our first **dynamic** data structure.

Remember arrays? They also contain multiple values, but they are _static_. If you created array of size `3`, you can not put `4` elements into it. Linked List can _dynamically_ grow or shrink in size, depending on how much elements it holds.

Remember arrays? You can not delete element from it. You can set it to `null` but not delete. If you need real deletion, you create new array with size of initial minus one, and copy all elements except one being deleted.

Linked List solve this problems, let's implement it:

``` java
class LinkedList<T> {
    Node<T> head;
}
```

That's all?  
Almost.

Linked list represented as a set of nodes (which we defined before). Each node has a reference to next node, until the last node refers to `null`

```
node1 -> node2 -> node3 -> ... -> node58 -> null
```

Since we can get information about the next node from the `reference`, for linked list we need only one node, first one, which always called `head`.

To create a linked list, you don't need to know size, it grows dynamically.

``` java
LinkedList<String> list = new LinkedList<>();
```

At this point, linked list is empty, because `head` is `null`

#### INSERT

When you need to add element to the list:

- create new node holding new element
- link head to this node
- make new node as head

``` java
public void add(T element) {
   Node<T> node = new Node<>(element);
   node.next = head;
   head = node;
}
```

Three steps, three lines of code.
This process could be shown in the following pic.

<img src="/images/ds/linked_list_add_1.png" alt="img" style="width: 100%;"/>

Take little time to go over steps and to understand what's going on.  
To clarify, repeat this action one more time

<img src="/images/ds/linked_list_add_2.png" alt="img" style="width: 100%;"/>

Notice, it will add element to the beginning of the list, because it is
more efficient add operation for linked list.
You already have access to the head node and just swaps few variables.
**Adding element to the beginning of linked list in terms of algorithmic complexity is** `O(1)`

_Warning: If you are not familiar with algorithmic complexity, I highly recommend
[this post](http://discrete.gr/complexity/)_

Adding element to the beginning of the list may sound counterintuitively, because we used to add elements to the end of the list in most cases. But we should understand this is most effective operation for linked list. 

If you have a node reference (in our case, `head`) you could add element _after it_ in `O(1)`.
Some improvements and future algorithms will be based on this fact.

To add element to the middle (or to the end of the list) we should make extra step to find the correct node reference, after which we can add new element.

Assume we have a list of size `3`, and we want to insert new element "5" after the 2nd position. So it become 3rd in resulting list. We call such method as `insertAfter(1, "5")` (1 refers to the 2nd element, starting from zero).

To ilustrate the process, look at the following pic:

<img src="/images/ds/linked_list_insert_after.png" alt="img" style="width: 100%;"/>

Formally, it's three steps again

- save current head to some node `temp`
- Move `temp` by few positions to the node you need to insert after
- switch references accordingly

Translating this to java:

``` java
public void insertAfter(int index, T element) {
    Node<T> temp = head;

    // skip index elements
    for (int i = 0; i < index; i++) {
        if (temp == null) break;
        temp = temp.next;
    }	

    if (temp == null) {
        throw new IllegalArgumentException("No such node");
    }

    // create new node
    Node<T> newNode = new Node<>(element);
    newNode.next = temp.next;
    temp.next = newNode;
}
```

You could easily implement other insertion methods like `insertBefore`, `addLast`, etc. The common trick for all operations is _to find correct node_ and swap few variables.

#### DELETE

Deletion from the list is a symmetrical operation to insert.
The most classic operation for the list, is deletion of the first element, which commonly called `tail` operation (analogy with head & tail, head refers to the first element, tail is everything but first element)

``` java
public void tail() {
    if (head == null) return;
    head = head.next;
}
```

The algorithm for deletion from beginning is following:

- If list is empty, do nothing
- Otherwise, move head to next element

Where is deletion you ask?

The first element is still in memory, but we don't have access to it, as we moved reference. In languages with automatic memory management (aka garbage collectors, like java) the element automatically deleted if no references point to it. In other languages you need manually deallocate memory occupied by first element.

The deletion from beginning complexity is also `O(1)`.

If you want to introduce methods like deletion from specific position, you need to do what we already did.

- Find the node you want to delete by moving temp reference
- Switch references

Pic will help to get what's going on

<img src="/images/ds/linked_list_delete.png" alt="img" style="width: 100%;"/>

Note, that we move by one step less than element we trying to delete to find the previous node. That's important.
Also, we haven't removed reference from node 3 to node 4. It's safe since no one is pointing to this node anymore and it
will be automatically deleted. If you add edge cases to detect errors you come up with something like this.

``` java
public void delete(int index) {
    if (index == 0) {
        tail();
    } else {
        Node<T> temp = head;
        // we moving by one position less to find the prev node
        for (int i = 1; i < index; i++) {
            if (temp == null) {
                break;
            }
            temp = temp.next;
        }
        if (temp == null || temp.next == null) {
            throw new IllegalArgumentException("No such element");
        }
        // skip next node (one we want to delete)
        // by referencing to next.next
        temp.next = temp.next.next;
    }
}
```	

Ok, we discussed two important type of operations for dynamic data structures: INSERT and DELETE.
You can not do this with static arrays. Insertion to and deletion from the beginning of the list is `O(1)` operation.
Insertion to and deletion from any other place is `O(n)`, because you need to find correct node in a loop with `n` elements.

What you can do effectively with arrays is random access and iteration.

#### RANDOM ACCESS

Remember array methods read by index, and write to index (`a[i]`).
This is `O(1)` operation, you can instantly get a cell, which you want to update.
The property to access any element in the datastructure called random access.

You can't get random access with linked list so fast as array, because you have only `head` node.
What you can do is to find correct node by moving temp pointer and change the `data` fields under this node.
Obviously moving inside list involves loop and it's a `O(n)` operation.

We have seen so many times lines of code for moving through list,
so it's better to introduce private method for doing this.

``` java
private Node<T> move(int steps) {
    Node<T> temp = head;
    for (int i = 0; i < steps; i++) {
        if (temp == null) {
            break;
        }
        temp = temp.next;
    }
    if (temp == null) {
        throw new IllegalArgumentException("No such element");
    }
    return temp;
}
```

We return node to which we moved so far, so future actions could be taken.
Now our random access methods will look very simple.

``` java
public T read(int index) {
    return move(index).data;
}
	
public void write(int index, T value) {
    move(index).data = value;
}
```

#### SIZE

Size is simple. Since array is static data structure it always knows what its size.
Size operation for array is always `O(1)`.

Linked list is dynamic and number of elements can vary, so in order to detect list size
we should write a function for that.

``` java
public int size() {
    int size = 0;
    Node<T> node = head;
    while (node != null) {
        size++;
        node = node.next;
    }
    return size;
}
```

Just move to the end of the list and calculate how much nodes have you seen.
Obviously this is `O(n)` operation.

The trick is to modify `insert` and `delete` methods, so they can count how many elements in the list so far.
Empty list inited with the counter `0`. Insert called, increment the counter. Delete is called decrement counter.
This is common practice for most of data structures to calculate some values on-the-fly, therefore speedup some
features, like `size()` call in this example.

#### ITERATION

One more thing we want to discuss with data structures is the iteration.
For example, go over the list and print every node value. Or go over list and add `1` to every value value.

Or more practical use case. Find node in the list which has specific value.

``` java
public Node<T> find(T value) {
    Node<T> temp = head;
    while (temp != null) {
        if (temp.data.equals(value)) {
            break;
        }
        temp = temp.next;
    }
    return temp;
}
```

_As we see later, methods like this will be extremely useful for building other data structures._

Iterating over arrays is simple, because we have indexed access, and always can write a for loop for that.
Linked list use slightly different approach for _generic_ iteration use case.

Java provides an interface called `Iterable` which you can implement in your data structure to add iteration capabilities to it.
The idea is to provide implementation for `iterator` method which return iterator object.
Iterator has two methods: `hasNext` (_do we have next value available?_) and `next` (_return next value_).
It also has other methods like `remove`,  but consider it as bad design and do not implement them.

Applying this approach we can make linked list _iterator aware_

``` java
class LinkedList<T> implements Iterable<T> {

    // ... other linked list stuff
    
    @Override
    public Iterator<T> iterator() {
        return new Iterator<T>() {
            private Node<T> currentNode = head;

            @Override
            public boolean hasNext() {
                return currentNode != null;
            }

            @Override
            public T next() {
                T element = currentNode.data;
                currentNode = currentNode.next;
                return element;
            }
        };
    }
}
```

We save head reference to the `currentNode` reference inside iterator. If `currentNode` equals to null, then we don't
have more elements in the list and return false. Otherwise it returns true. And `hasNext` has two responsibilities,
return current value of the node and move `currentNode` pointer to the next node.

The common pattern for iterating over data structure with `iterator` object is following

``` java
Iterator<String> iter = list.iterator();
while (iter.hasNext()) {
    System.out.println(iter.next());
}
```


_Warning: the iterator object is one-time object for iterating over data structure.
Once you created it and iterated you can not use it anymore. Also, the iterator based
on the current version of datastructure at the time of iterator creation. If you created iterator
and then added/removed elements to datastructure the result is unpredictable._

Java has a nice syntactic sugar for iterator pattern, example above could be rewritten as

``` java
for (String elem : list) {
    System.out.println(elem);
}
```

This way you don't even use `iterator` varaible.

Remember, the iteration is always `O(n)` complexity, despite the fact is it array or linked list.

But if you remember, arrays are contiguos blocks in the memory, where list nodes could be scattered over the RAM.
Kinda like this.

<img src="/images/ds/array_in_memory.png" alt="img" style="width: 100%;"/>

In terms of algorithmic complexity iteration over array and linked list is equal, but common sense,
hardware architecture details and performance tests says that arrays are faster for iteration.

And the last thing. Previous image is a bit lie, because linked list node holds value and a reference to the next node.
You will be surprised but one reference could take 8 or even 16 bytes. So arrays are much more memory efficient than linked lists.

### 6. Doubly Linked List

Common way to design new data structure, is to take exisiting data structure and improve
things that behave poorly.

Linked list is a good data structure which introduced actions that we
couldn't do with arrays, insert and delete elements at arbitrary place.

Insert to the beginning is fast, but most of the time developers need insert to the end
of the list. Insert to the end still slow `O(n)`. The same with deletion.

Doubly linked list introduced the simple idea, each node holds references for both
**next** and **previous** node.

``` java
public class DoublyNode<T> {
    public T data;
    public DoublyNode<T> prev;
    public DoublyNode<T> next;

    public DoublyNode(T data) {
        this.data = data;
    }
}
```

Also, doubly linked list holds two references, one for `head` and one for the `last` node.

``` java
public class DoublyLinkedList<T> {
    DoublyNode<T> head;
    DoublyNode<T> last;
}	
```

Visually, the doubly linked list with nodes `1`, `2`, `3` looks like this:

![](/images/ds/doubly_linked_list.png)

This way we can move from beginning to the end, and from the end to the beginning.
Operations INSERT to the end and DELETE from the end become a `O(1)` complexity, exactly what we needed.

However, this have few tradeoffs. Algorithms for insertion and deletion became a bit harder and memory footprint
for such data structure adds two references per element. Imagine array of integer values (4 bytes per value).
With 16 bytes per reference, doubly linked list with the same values has 800% overhead.

If you define all `O(1)` insert and delete operations in the doubly linked list, you will get something like this:

``` java
public void addFirst(T element) {
    DoublyNode<T> node = new DoublyNode<>(element);
    node.next = head;
    if (head != null) head.prev = node;
    head = node;
    if (last == null) last = node;
}

public void addLast(T element) {
    DoublyNode<T> node = new DoublyNode<>(element);
    node.prev = last;
    if (last != null) last.next = node;
    last = node;
    if (head == null) head = node;
}

public void deleteFirst() {
    if (head == null) return;
    if (head.next == null) {
        head = null;
        last = null;
    } else {
        head.next.prev = null;
        head = head.next;
    }
}

public void deleteLast() {
    if (last == null) return;
    if (last.prev == null) {
        head = null;
        last = null;
    } else {
        last.prev.next = null;
        last = last.prev;
    }
}
```

These methods may look a bit overloaded with null checks, but at the end it's all about changing
the references between different variables, no rocket science.

If you want to define random access methods, or insert/delete value at the specific position,
it's pretty similar to what we have done with linked list. Few checks and few more swaps.

The one trick you could explore, is to check whether your random access position closer to the `head`
or to the `last` node. This could be achieved only if you cache the size of the list, so it has `O(1)` complexity.
Compare the position to the `size/2` and if it's less than that, start from `head`, otherwise start from `last`.

``` java
public void write(int position, T value) {
    DoublyNode<T> node;
    if (position <= size / 2) {
        // move from head by x positions
        node = head;
        for (int i = 0; i < position; i++) {
            node = node.next;
        }
    } else {
        node = last;
        // move from last by x positions
        for (int i = 0; i < size - position - 1; i++) {
            node = node.prev;
        }
    }
    node.data = value;
}
```

The iteration size in the worst case reduced to `n/2`, still `O(n)` complexity, but twice faster on average.

### 7. Dynamic Array

Although the doubly-linked list makes adding elements to the end of the list fast, it still has two major drawbacks:
big memory footprint and poor performance for random access.
Dynamic Array (so called `ArrayList` in Java) solve these problems.

Dynamic Array use the idea to store elements in internal static array.
This provides a random access and low memory footprints.
When you add elements more than static array can hold, create new array,
bigger in size, and copy all elements from the old one.

This may sound like a terribly slow operation, but it is not.
Copying array is moving one block of memory in the RAM to another block.
Theoretically, it's still has `O(n)` complexity, but practically it is very fast operation
on modern hardware.

Let's start.

``` java
public class DynamicArray<T> {
    private T[] elements;
    private int effectiveSize;
    
    public DynamicArray() {
        this.elements = (T[]) new Object[10];
        this.effectiveSize = 0;
    }
    
    public void add(T elem) {
        elements[effectiveSize] = elem;
        effectiveSize++;
    }
    
    public T get(int index) {
        if (index < 0 || index > effectiveSize - 1) throw new IllegalArgumentException();
        return elements[index];
    }

    public void set(int index, T value) {
        if (index < 0 || index > effectiveSize - 1) throw new IllegalArgumentException();
        elements[index] = value;
    }

    public int size() {
        return effectiveSize;
    }

    @Override
    public String toString() {
        return Arrays.toString(Arrays.copyOf(elements, effectiveSize))
    }
}
```


We make `elements` array private, we don't want anyone from outside world will corrupt our array.

During the initialization, in constructor we create the array, with some default size, say `10`.
Type cast to `T[]` is required, because java doesn't allow you to create an array of generic type `T`.

When we create empty dynamic array, backed by static array of size `10`, its real size `0`.
That's why we introduce variable `effectiveSize` to track the _effective_ number of elements in array.
Method `size()` could be called to get this value.

And lastly, we give three methods: `add` which adds element to the end of dynamic array,
and `get(i)`/`set(i, value)` which provide random access. 

<img src="/images/ds/dynamic_array_general.png" alt="img" style="width: 100%;"/>

So far so good.

We can even add `10` elements to the array. But when we add `11`th element, `effectiveSize` will point to
non-existing cell in internal static array and we will get `ArrayIndexOutOfBoundsException`.

We missed core functionality of dynamic array.
When new element is added, we need to check do we have
free cell in internal array for it. If no, extend the array.

Algorithm for extending array is following:

- Create new array, double the size from existing
- Copy all elements from old to new

``` java
private void extend() {
    this.elements = Arrays.copyOf(elements, elements.length * 2);
}
```

`Arrays.copyOf` does two things for us, it creates an array with desired size `length * 2`
and copies all elements from old array to new array. Right half of the new array will be empty yet.

Then modify `add` method to add condition "grow if no free cells left"

``` java
public void add(T elem) {
    if (effectiveSize >= elements.length) extend();    // <-- this line added
    elements[effectiveSize] = elem;
    effectiveSize++;
}
```

<img src="/images/ds/dynamic_array_extend.png" alt="img" style="width: 100%;"/>

Current implementation of dynamic array can accept as many elements as you want.
Limited only by RAM size.

Add more than `10` elements, internal array grows to size `20`  
Add more than `20` elements, internal array grows to size `40`  
Add more than `5120` elements, internal array grows to size `10240`  

But what happened when we remove elements from the end of array?

Since we have not discussed yet method for removing elements from array, here you go:

``` java
public void removeLast() {
    if (effectiveSize == 0) throw new IllegalArgumentException();
    elements[effectiveSize - 1] = null;
    effectiveSize--;
}
```

To remove element unset its reference from array (set to `null`), so garbage collector
able to pickup it during next clenaup cycle. And obviously, if element is removed
decrement the `effectiveSize`.

Now assume you added `100000` elements to dynamic array.
The internal array grows to the size `163840`.
Then, you removed `100000` elements, `effectiveSize` became zero, array is empty again.
But internal array still have the size `163840`.
Although references from the cell of this array is nullified, and objects are already deleted.
Array with `163840` cells still occupies a lot of space (assume each cell is a reference 8/16 bytes)

Symmetry. If we extend the array if more elements arrive,
we should **shrink** it when more elements removed.

Obvious choice is to shrink to half of the size of array.
When element is removed and there is possible to halve the size of internal array to hold all elements, then `shrink`.
Algorithm is opposite to `extend`

- Create new array, half the size of the current.
- Copy all _effective_ elements from the current to the new array.

``` java
private void shrink() {
    this.elements = Arrays.copyOf(elements, elements.length / 2);
}
```

And one condition in `removeLast`

``` java
public void removeLast() {
    if (effectiveSize == 0) throw new IllegalArgumentException();
    if (elements.length > 10 && effectiveSize - 1 <= elements.length / 2)   // <-- these two lines
        shrink();                                                           // <-- added
    elements[effectiveSize - 1] = null;
    effectiveSize--;
}
```

The part of `elements.length > 10` is not required, but in our case it forces the internal array to
have minimum size of `10` and next sequence of `extend`/`shrink` calls always will maintain sizes `10, 20, 40, etc.`

The implementation above has one major drawback. Imagine the situation:

- Empty array is created, internal array size is `10`.
- Then `10` elements is added, internal array is full.
- Adding `11`th element cause array to `grow`, so its size become `20`.
- Remove `11`th element, so effective size become `10` again.
- Internal array of size `20` is too big to hold `10` elements and can be shrinked.
- `Shrink` it, so internal array become of size `10` and we have `10` effective elements.
- Add `11th` element again.

You got the idea, on the boundaries where array is doubling `10, 20, 40, etc` adding and removing elements
cause set of `extend`/`shrink` operations, which for large arrays will be performance bottleneck.

The solution to this problem is surprisingly simple.
Shrink array by half, only if it has **less than `1/3` of the current internal array size**.

To make that clear, consider example above again

- Empty array is created, internal array size is `10`.
- Then `10` elements is added, internal array is full.
- Adding `11`th element cause array to `grow`, so its size become `20`.
- Remove `11`th element, so effective size become `10` again.
- `10` is greater than one third of array size (`20/3 = ~6`) so we don't shrink.
- Remove one, `9` elements, don't shrink
- Remove one, `8` elements, don't shrink
- Remove one, `7` elements, don't shrink
- Remove one, `6` elements. And now we can apply `shrink` operation, so internal array size become `10` again.
- Immediate adding and removing elements don't cause array `extend` or `shrink`

All you need to fix is to change condition inside `removeLast`

``` java
effectiveSize - 1 <= elements.length / 2
```

into

``` java
effectiveSize <= element.length / 3
```

To ilustrate this process, look into following pic

<img src="/images/ds/dynamic_array_shrink.png" alt="img" style="width: 100%;"/>

That's all. The basic functionality of dynamic array.  
Random access has complexity `O(1)`,
`add` to the end of array has **amortized** complexity of `O(1)`.

Amortized complexity means _most of the time_.
As we discussed, adding to array has `O(1)` complexity, but when it needs to extend, complexity becomes `O(n)`
(since we need to copy n elements). Theoretically, worst case is still `O(n)`, but practically _most of the time_ you
will see `O(1)`, so even rare cases of `O(n)` don't cause much troubles.

We have not discussed method like `insert` in the middle or `delete` from the middle of the dynamic array.
This was done intentionally, because such operations are not very performant on array.
Anyway, for rare cases they still might be useful.

To remove element from array, shift all elements _after_ it by one position left. The same with insertion.  
To insert element to array, shift all element _after_ it by one position right.

``` java
public void insert(int position, T elem) {
    if (position < 0 || position > effectiveSize) throw new IllegalArgumentException();
    if (effectiveSize >= elements.length) extend();
    // shift all elements from position by 1 to the right
    System.arraycopy(elements, position, elements, position + 1, effectiveSize - position);
    elements[position] = elem;
    effectiveSize++;
}
	
public void remove(int position) {
    if (position < 0 || position > effectiveSize - 1) throw new IllegalArgumentException();
    if (effectiveSize == 0) throw new IllegalArgumentException();
    if (elements.length > 10 && effectiveSize <= elements.length / 3)
        shrink();
    // shift all elements from position by 1 to the left
    System.arraycopy(elements, position + 1, elements, position, effectiveSize - position);
    elements[effectiveSize - 1] = null;
    effectiveSize--;
}
```

The key here is to call native method `System.arraycopy` with correct arguments.  

And lastly add iterator for the dynamic array

``` java
@Override
public Iterator<T> iterator() {
    return new Iterator<T>() {
        int index = 0;
	
        @Override
        public boolean hasNext() {
            return index < effectiveSize;
        }
			
        @Override
        public T next() {
            if (index >= effectiveSize) throw new IllegalArgumentException("No such element");
            T element = elements[index];
            index++;
            return element;
        }
    };
}
```

At this point you should have a completely working implementation of dynamic array.
You can adopt few more tricks to make dynamic arrays more useful.

- Init internal array with size `0` instead of `10` to reduce memory footprint.  
- Provide `trim` method to make internal array size equal to the effective size, reducing memory overhead.  
- Use different approch for extending array. Instead of doubling the size use arithmetic progression.

I doubt this list will have an end.


### 8. Map

Dynamic arrays and maps are probably the two most used data structures in the programming.
We have got understanding how dynamic arrays are working, so it's time to move to maps.

**Map** (or dictionary, or symbol table) is an **associative array**. This data structure composed
from the list of pairs (**key** and **value**).

Arrays (both static and dynamic) are particular case of associative array,
you can make association between `index` and `element`. For example, when you
set element in array `a[2] = 7`, you make association between `key=2` and `value=7`.

The limitation of arrays though, they can use only integer type of keys.
That means you can't associate person name and person age, like `key="John", value=25`

_Hold on, boy. I know records, so I can do this to represent the pair._

``` java
public class Pair<K, V> {
    public K key;
    public V value;

    public Pair(K key, V value) {
        this.key = key;
        this.value = value;
    }
}
```

_I know dynamic arrays, so I can put all pairs into it._

``` java
DynamicArray<Pair<String, Integer>> list = new DynamicArray<>();

list.add(new Pair<>("John", 25));
list.add(new Pair<>("Brad", 37));
list.add(new Pair<>("Chris", 52));
list.add(new Pair<>("Dan", 16));
```

_I have all the data, and when I want to find out what is John's age, I just do it in a loop_

``` java
// what is John's age?
Integer age = null;
for (int i = 0; i < list.size(); i++) {
    Pair<String, Integer> pair = list.get(i);
    if (pair.key.equals("John")) {
        age = pair.value;
        break;
    }
}
```


Ok, my fault. You can associate whatever you want using records and dynamic arrays.  
The problem, however, is finding John's age involves linear search algorithm, which complexity is `O(n)`.
Kinda slow, right?

Map provides search value by key, with `O(1)` complexity, what means instantly.

Before we dig into the idea behind the effective map implementation,
we should define the minimal set of operations for map:

- `add(key, value)`
- `get(key)`
- `remove(key)`

All operations should have `O(1)` complexity.
Also we assume the `key` is _unique_, so you can not put two different values with the same key into map.
If you add key/value pair and the key is present in the map, the new value will overwrite the old one.

_Imagine map as centralized knowledge base in post-nuclear world.
Most of the animal species disappeared and only one dog is survived.
Lonely stalker discovered this dog, goes to the knowledge base and says
"the dog is grey", so everyone knows what is dog's color. Years passed,
things changed and another stalker discovered the same dog. He added to knowledge
base fact "the dog is white". Radiation affects dogs as well. The latest true fact that the
dog is white, and story about grey dogs gone._

So, how do we implement a map with all required operations having the `O(1)` complexity.

Array is good starting point, it provides random access, but can use only integers as a keys.
Somehow we need to transform key object into integer representation.
The problem is number of possible objects is infinite, but the number of integers is limited.
We can't do this one-to-one.

Let's define a function called **hash function** which transform `String` object into integer number,
using its length.

``` java
int hash(String s) {
    return s.length();
}
```

<img src="/images/ds/map_hash.png" alt="img" style="width: 50%;"/>

Excellent, we have a way to transform every possible string into integer.
Now we can build a map, backed by static array.
The keys for such map always strings, values are generic types.

``` java
public class Map<V> {
    private V[] buckets;

    public Map() {
        this.buckets = (V[]) new Object[10];
    }

    public void add(String key, V value) {
        int index = hash(key);
        buckets[index] = value;
    }

    public void remove(String key) {
        int index = hash(key);
        buckets[index] = null;
    }

    public V get(String key) {
        int index = hash(key);
        return buckets[index];
    }
}		
```

The idea is to use integer value produced by hash function as an index for array. 

If you try it, it even looks like it's working.

``` java
Map<Integer> ages = new Map<>();
ages.add("Angelina", 27);
ages.add("Brad", 52);
ages.add("Casey", 31);
ages.add("Donald", 80);
ages.add("Elijah", 45);

System.out.println(ages.get("Brad")); // <-- prints 52, correct!
```

The problem with this map is clear if you visualise contents of internal array.

<img src="/images/ds/map_collision.png" alt="img" style="width: 100%;"/>

This problem known as **hash collision**. When two different objects have the same hash value,
the hash collision happens and things got messy.

Maybe use the `hash` function which gives better distribution?
Probably, but still there is a chance for collisions
since we transform infinite set of objects into finite integer numbers.

The solution is to back _every cell_ in the map internal array by linked list.
If collision happens, add new value to the list.

Declaration of `buckets` array changes to

``` java
LinkedList<Pair<String, V>>[] buckets;

public Map() {
    this.buckets = (LinkedList<Pair<String, V>>[]) new LinkedList[10]
    // initialize each linked list
    for (int i = 0; i < buckets.length; i++) {
        buckets[i] = new LinkedList<>();
    }
}
```

This may look a bit strange, but nothing special here. We have seen `Pair` object before.
It's a record which holds both `key` and `value`. Then we use linked list of such pairs.
And lastly array, where each cell is separate linked list. Make sure every linked list
is initialized during constructor call.

<img src="/images/ds/map_linked_list.png" alt="img" style="width: 100%;"/>

Recall function `find(T value)` on linked list.
Its goal to find node, where `data` is equal to `value` and return this node.

We upgrade this function a bit, so instead of giving it a value we want to find,
we give it a predicate which says if it's value we search for or not.

``` java
public Node<T> findByPredicate(Predicate<T> predicate) {
    Node<T> temp = head;
    while (temp != null) {
        if (predicate.test(temp.data)) {    // <-- this line
            break;
        }
        temp = temp.next;
    }
    return temp;
}
```

`Predicate<T>` is an object which represent function, which accepts object value and return either true or false.
This is extremely helpful as you can search for the nodes using arbitrary conditions.

``` java
list.findByPredicate(i -> i > 3);      // find first value which is greater than 3
list.findByPredicate(i -> i % 2 == 0); // find first even value
```

Now back to our map implementation. If we use linked list of pairs for each array cell, we should
change map methods `add`/`get`/`remove` accordingly.

``` java
public void add(String key, V value) {
    int index = hash(key);
    LinkedList<Pair<K, V>> list = buckets[index];
    Node<Pair<K, V>> node = list.findByPredicate(p -> p.key.equals(key));
    if (node == null) {
        list.add(new Pair<>(key, value));
    } else {
        node.data.value = value;
    }
}
```

- Calculate hash for the key.
- Use hash value as an index for array, to get appropriate list
- Find the node **by predicate**, where pair key is **equal** to one which passed to the function.
- If found, update the pair `data` to the new value.
- If not found, create new pair from the key and value provided and add it to the list

When we have list of values instead of one cell value, we can prevent collisions.
Let's see how adding new pairs change the map.

<img src="/images/ds/map_add.png" alt="img" style="width: 100%;"/>

`Get` method is similar, just returns the existing value, if it's found.

``` java
public V get(String key) {
    int index = hash(key);
    LinkedList<Pair<String, V>> list = buckets[index];
    Node<Pair<String, V>> node = list.findByPredicate(p -> p.key.equals(key));
    if (node == null) return null;
    return node.data.value;
}
```

`Remove` method is also similar, but we need to tweak linked list implementation a bit. The same way as we defined
`findByPredicate` we could define `deleteByPredicate` as follows

``` java
public boolean deleteByPredicate(Predicate<T> predicate) {
    if (head == null) return false;
     // if first element just switch head
    if (predicate.test(head.data)) {
        head = head.next;
        return true;
    } else { // otherwise we need to keep track previous element
        Node<T> prev = head;
        Node<T> node = head.next;
        while (node != null) {
            if (predicate.test(node.data)) {
                prev.next = node.next;
                return true;
            }
            prev = node;
            node = node.next;
        }
        return false;
    }
}
```

This method deletes the first node which match the predicate.

The point here is to track two nodes in a loop, one which we test for predicate and previous to it.
If test for node succeed, we update reference for previous node.

For the sake of completeness, this call returns `true` if node was deleted, or `false` if not found.

Then, `remove` from the map has straightforward implementation, based on `deleteByPredicate`.

``` java
public void remove(String key) {
    int index = hash(key);
    LinkedList<Pair<String, V>> list = buckets[index];
    list.deleteByPredicate(p -> p.key.equals(key));
}
```

So far so good. But wait, what if large string key comes to a map?

``` java
map.add("Count Dracula", 500);
```

The `hash` for "Count Dracula" is equal to `13`, but array has only `10` cells.
Clearly, you get `ArrayIndexOutOfBoundsException`. One way to solve it to use bigger array.
But again, if you need a map for holding _just one_ large key and value, using extremely large array for that is not
effective at all.

The trick is to do `mod` operation between hash and array size.
In simple words, it will be remainder after division of hash value by array size.

When you divide `13` by `10`, you get `1` and `3` as a remainder. We use `3` as an index for array.
Math says, the remainder is always guaranteed to be less than divisor, in our case it will be between `0` and `10`,
exactly set of possible indices of array.

``` java
int mod = 13 % 10; // 3
```

Then, every time when we calculate `hash` for the key,
index is defined as a remainder from division this hash by array size.

``` java
int index = hash(key) % buckets.length;
```

Applying this change to all lines where we detect array index by hash, gives us
working map implementation for `String` keys.

Why only `String`? Because we defined `hash` function only for `String` type. If you
define `hash` for any other record type, you can use it as a key for the map.

That's why every object in Java has a method called `hashCode`, which you should define if you
want this object use as a key in the map. It has only one requirement

**hash codes for equal objects should be equal**

That does not mean though if hashcodes are equal, objects are equal.
As we seen before two different objects "Donald" and "Elijah" have the same hashcode `6`,
it's called collision and we have a way to solve it.
But if you calculate hashcode for "Elijah" and then for "Elijah" again, both should be equal to `6`.

Although our choice of hash function is pretty valid use case it doesn't behave well in certain situations.

<img src="/images/ds/map_bad_hash.png" alt="img" style="width: 100%;"/>

Even we have a set of different objects, they all produce the same hash code and put
in the same bucket. This is no differs from regular linked list with poor `get` and `remove`
performance. To avoid this, recommended to use hash functions which give better distribution of different
objects across the buckets, and this is pretty complex topic. Most of the IDE can generate good enough
hash functions for your objects, so you don't need to care.

Equals is another story. How you define two objects are equal?

Pretty clear that `3=3` and `Elijah=Elijah`, but for complex objects it's not so obvious.

_Is 2+2=4?_  
_Is objects Add(2,2) and Constant(4) are equal?_  
_Is "John Doe" the same as "Doe John"?_  
_A lot of questions._

That's why Java also allows you to define what equality means for specific type of objects.
Just override `equals` method. It has few requirements though which may sound obvious.

- `a` should be equal to `a`
- if `a` equal to `b`, then `b` equal to `a`
- if `a` equal to `b` and `b` equal to `c`, then `a` should be equal to `c`

Remember we solved collision by putting objects with the same hash code in the list.
Then we did a search inside list for specific value by calling `equals` on each node until we find correct value.

That's why Map requires for object you use as a key to have correct implementations for both `equals` and `hashCode`.

`Equals` could be generated in IDE as well. I prefer to use [lombok](https://projectlombok.org/features/EqualsAndHashCode) annotation `@EqualsAndHashCode`, which generates these methods on the fly.

Assuming some object has correct implementation of `equals` and `hashCode` we can promote map to work with generic keys as well.
Replace `hash(key)` with `key.hashCode()`. Since `hashCode` returns an integer, which could be negative number as well, we take absolute value of hashcode, to make sure our array indices are always positive.

``` java
int index = Math.abs(key.hashCode()) % buckets.length;
```

#### ITERATOR

Even map data structure has no ordering guarantees we would like to get every key value pair as a list.

We already know, what `Iterator` interface does and just need provide a correct implementation

``` java
@Override
public Iterator<Pair<K, V>> iterator() {
    return new Iterator<Pair<K, V>>() {
        private int currentIndex = 0;
        private Iterator<Pair<K, V>> iter = buckets[currentIndex].iterator();
		
        @Override
        public boolean hasNext() {
            if (iter.hasNext()) {
                return true;
            } else {
                currentIndex++;
                if (currentIndex >= buckets.length) return false;
                iter = buckets[currentIndex].iterator();
                return hasNext();
            }
        }

        @Override
        public Pair<K, V> next() {
            if (iter.hasNext()) {
                return iter.next();
            } else {
                currentIndex++;
                if (currentIndex >= buckets.length)
                    throw new IllegalArgumentException("No such element");
                iter = buckets[currentIndex].iterator();
                return next();
            }
        }
    };
}
```

Although it may look a bit clumsy, the idea is simple.
Use list iterator for the first bucket and delegate all calls `hasNext` and `next` to it.
If iterator has no element left, move to then next bucket and get iterator for the next list until you run out of buckets.

#### REHASHING

The last problem we need to tackle for effective map implementation is **rehashing**.

So far we were working with map backed by static array of size `10`.
When one million elements added to such map, the backed array is still have size `10`
and every linked list holding around `100000` elements.
`Get` call require to process such lists sequentially, and considering their length, that's will be slow.

Our goal to keep each list as short as possible.
The obvious choice is to make backed array larger, so each lists contains less elements.

How much larger?

Let's introduce a function `fillPercentage` which calculates ratio between number of elements in the map
and size of the backed array.

``` java
private double fillPercentage() {
    return 1.0 * size / buckets.length;
}
```

_Size for map defined the same way as effectiveSize for dynamic array, when add element increment size, when remove decrement it_

For example, if we have `4` elements in the map, and backed array size is `10`, the fill percentage will be `4/10 = 0.4`.
When fill percentage is _high enough_ we need to extend a backed array, the same way as we extended the dynamic array.
If it's _small enough_ we need to shrink it back.

Values _high enough_ and _small enough_ could be defined, but we need provide meaningful defaults.
Let's assume we extend array when `fillPercentage` is greater than `0.8`, and shrink when fill percentage is less than `0.2`.

Why it's called _rehashing_ then?

Because it's not enough to change the internal array size. You need recalculate buckets for **every** element.

Recall previous example "Count Dracula". Hash is `13`, array length is `10`, `13 % 10 = 3` so it falls under `3`rd index in backed array.
When backed array is extended, say to the size `20`. Hash still `13`, but `13 % 20 = 13` so it needs to be moved to the `13`th bucket. 

We need recalculate bucket for every element. The easiest way to do it, create extended array,
iterate over all current elements in the map add them to new array of lists using the same way as in `add` function.

``` java
private void rehash(int newSize) {
    System.out.println("Rehashing to size " + newSize);
    LinkedList<Pair<K, V>>[] newArray = (LinkedList<Pair<K, V>>[]) new LinkedList[newSize];
    // initialize each linked list
    for (int i = 0; i < newArray.length; i++) {
        newArray[i] = new LinkedList<>();
    }
    for (Pair<K, V> pair : this) { // <-- iterate over all map elements
        int index = Math.abs(pair.key.hashCode()) % newArray.length;
        newArray[index].add(pair);
    }
    this.buckets = newArray;
}
```

This picture demonstrates what happens when you rehash to bigger array.

<img src="/images/ds/map_rehashing.png" alt="img" style="width: 100%;"/>

Pay attention how few elements moved to the new buckets of larger array. Some lists become smaller.
Exactly what rehashing is supposed to do.

To apply rules of rehashing to the map add this line to the end of `add` method

``` java
if (fillPercentage() >= 0.8) rehash(buckets.length * 2);
```

Tweak `remove` as well

``` java
if (buckets.length > 10 && fillPercentage() <= 0.2) rehash(buckets.length / 2);
```

Although real world implementations of the map apply more complex rules and tricks, our map
still very robust for the most practical use cases.

### 9. Set

Map is very important data structure by itself and as a building block for other data structures.
For example, Set, data structure borrowed from the Math, has number of practical use cases
and extremely simple implementation if backed by map.

Formally, **Set** is a collection which contain only unique values. No ordering, no association, just unique values.

That means the set `{1, 2, 3}` is the same set as `{1, 2, 2, 3}` and the same as `{2, 1, 3}`

The operations defined on sets are `add` value to the set, `remove` value from the set, and check if it `contains` specific value.

If you put `1` to the set `{1, 2, 3}`, nothing changes, set already contains value `1`.  
If you remove `5` from the set `{1, 2, 3}`, nothing changes as well since nothing to remove.  

_Collector Donald Duck gathers coins.
He has a lot of unique coins, including some from egyptian and roman ages.
But he still not done and want to collect every unique coin in the world.
His process however a bit routine, he finds a coin and starts comparing it to every coin in his
collection. If match found, he discards coin as known, otherwise he add it to the collection._

_Another coin collector Ronald Ruck thinks differently. Every coin he found, he puts into latest
invention Set Coin Dispenser. This machine is responsible for checking uniqueness for a coins and do it very effectively.
As a plus, repeatable coins not discarded and could be exchanged with other collectors._

Set, latest invention, could be implemented in following way

``` java
public class Set<T> {
    private Map<T, T> map = new Map<>();

    public void add(T object) {
        map.add(object, object);
    }

    public void remove(T object) {
        map.remove(object);
    }

    public boolean contains(T object) {
        return map.get(object) != null;
    }
}
```

So simple, feel the power of Map!

When you `add` value to set, add this value as both key and value into the map.  
When you `remove` value from the set, remove this key from the map.  
When you check if the value `contains` in the set, get value from the map, it should not be null.

The iterator for the set could be derived from iterator for the backed map.

``` java
@Override
public Iterator<T> iterator() {
    return new Iterator<T>() {
        Iterator<Pair<T, T>> mapIterator = map.iterator();

        @Override
        public boolean hasNext() {
            return mapIterator.hasNext();
        }

        @Override
        public T next() {
            return mapIterator.next().key;
        }
    };
}
```

Since the set data structure derived from the set theory in Mathematics, we could also define some additional
operations on sets, like `union`, `intersection` and `difference`.

The best way to illustrate them using the Venn diagram.

<img src="/images/ds/set_venn.png" alt="img" style="width: 80%;"/>

These operations could be easily defined using set iterator.

``` java
public Set<T> intersection(Set<T> aSet) {
    Set<T> newSet = new Set<T>();
    for (T e : this) {
        if (aSet.contains(e)) {
            newSet.add(e);
        }
    }
    for (T e : aSet) {
        if (this.contains(e)) {
            newSet.add(e);
        }
    }
    return newSet;
}
```

To get `intersection` between two sets,
go through every element in set 1 and check if it contains in set 2. If so add it to resulting set.
Repeat the same operation by iterating over set 2.

`Union` is simpler, because you need to go through each set and add element to the resulting set.

``` java
public Set<T> union(Set<T> aSet) {
    Set<T> newSet = new Set<T>();
    for (T e : this) newSet.add(e);
    for (T e : aSet) newSet.add(e);
    return newSet;
}
```

`Difference` is not symmetric operation, therefore you need to iterate over first set and add to the resulting
set elements which are not in the set 2.

``` java
public Set<T> difference(Set<T> aSet) {
    Set<T> newSet = new Set<T>();
    for (T e : this) {
        if (!aSet.contains(e)) {
            newSet.add(e);
        }
    }
    return newSet;
}
```

Operation on sets are very useful in certain use cases.

If you have cluster of nodes and set of unique IP adresses which accessed the specific node,
you can do `union` between all sets to get full set of IPs accessed whole cluster.

### 10. MultiSet

**MultiSet** (or `Bag`) is an upgrade to the regular set, where element can occur multiple times.

For example, multiset `{a, b, b}` is the same as `{b, a, b}` (remember, ordering is not matter).
But mulitset `{a, b, b}` and multiset `{a, b}` are not equal, since first has two copies of element `b`.

Implementation, as you probably already imagined, is a piece of cake.
Back it by the map as well, use element as a key, and number of occurences as a value.

``` java
public class MultiSet<T> {
    private Map<T, Integer> map = new Map<>();

    public void add(T object) {
        Integer count = map.get(object);
        if (count == null) { // element is not seen yet
            map.add(object, 1);
        } else {
            map.add(object, count + 1); // increment the count
        }
    }

    public void remove(T object) {
        Integer count = map.get(object);
        if (count == null) return; // element not exists, do nothing
        if (count.equals(1)) {
            map.remove(object); // last element, remove entry from the map
        } else {
            map.add(object, count - 1); // decrement the number of elements
        }
    }

    public int multiplicity(T object) {
        Integer count = map.get(object);
        return count == null ? 0 : count;
    }

    public int size() {
        int size = 0;
        for (Pair<T, Integer> pair : map) {
            if (pair.value != null) {
                size += pair.value;
            }
        }
        return size;
    }
}
```

To `add` element to the multiset, increment counter for this element in the map by one,
or if it's the first occurence, create new map entry with the count `1`.  
`Remove` is the opposite. If that's the last occurence, remove entry from the map, otherwise decrement the counter.

The number of occurences for the element in multiset called `multiplicity`, so we defined such method as well.

Contains could be defined in terms of multiplicity as

``` java
public boolean contains(T object) {
    return multiplicity(object) > 0;
}
```

The total sum of all multiplicities define a size of the multiset.

The iterator for such multiset, however, is not trivial.
Since number of items in multiset could be larger than number of items in the map, we should do the trick.
Cache next map pair and return its key so many times as the multiplicty of this key. Then move to the next pair in map iterator.

``` java
@Override
public Iterator<T> iterator() {
    return new Iterator<T>() {
        Iterator<Pair<T, Integer>> mapIterator = map.iterator();
        Pair<T, Integer> nextPair;
        int timesReturned = 0;

        @Override
        public boolean hasNext() {
            if (nextPair != null && timesReturned < multiplicity(nextPair.key)) {
                return true;
            } else {
                return mapIterator.hasNext();
            }
        }

        @Override
        public T next() {
            if (nextPair != null && timesReturned < multiplicity(nextPair.key)) {
                timesReturned++;
                return nextPair.key;
            } else {
                nextPair = mapIterator.next();
                timesReturned = 1;
                return nextPair.key;
            }
        }
    };
}
```

Standard set operations like `union`/`intersection` not as straightforward in multiset.

For example, take `union`

`{1, 1, 2}` union with `{1, 2, 3}` gives `{1, 1, 2, 3}`  
`{1, 2, 3}` union with `{1, 2, 3}` gives `{1, 2, 3}`  

See how the number of occurences for the elements in _both_ multisets affect the resulting multiset.

The idea is to get every unique value from both multisets, and assign it to _maximum_ multiplicity.

``` java
public MultiSet<T> union(MultiSet<T> mSet) {
    Set<T> regularSet = new Set<T>();
    for (T e : mSet) regularSet.add(e);
    for (T e : this) regularSet.add(e);

    MultiSet<T> newMSet = new MultiSet<T>();
    for (T e : regularSet) {
        // total multiplicity defines how many times the element will occur in resulting multiset                	
        int totalMultiplicity = Math.max(this.multiplicity(e), mSet.multiplicity(e));
        for (int i = 0; i < maxMult; i++) {
            newMSet.add(e);
        }
    }
    return newMSet;
}
```

Pay the attention how we used previous data structure `Set` to implement new method in the new data structure.
This is common approach for all advanced data structures. If you understand basics you will understand complex data structures as well.

As you probably guessed, if you use `min` instead of `max`, you will get an `intersection` function.

``` java
int totalMultiplicity = Math.min(this.multiplicity(e), mSet.multiplicity(e));
```

For `difference` function, calculate number difference between two multiplicities.
If the difference is negative, truncate it to zero.

``` java
int totalMultiplicity = Math.max(0, this.multiplicity(e) - mSet.multiplicity(e));
```

What are practical applications of MultiSet?

Well, it has a lot of usages. Classic "big data" example of word counts is based on multiset.
You have a large text, and you need to find how many times specific word occured in the text.

Split text into the words, put every word into multiset and then get multiplicity of the word you looking.
You could do it in a loop, but here is the main point of using data structure. It is reusable.
Next time you ask for number of occurences for another word, we still can query the same multiset, and it still will be `O(1)`.

### 11. MultiMap

If there is `Set` and `MultiSet`, should not we have `Map` and `MultiMap`?

Right, there is such thing like **MultiMap** and it's an upgrade to regular Map, where each key can have multiple values.

For example, if you want to associate book with multiple authors you can use multimap for that.

You may ask how that differs from regular map where value is `Set`?
Well, it doesn't differ at all, that's exactly the way how multimap is implemented.

MultiMap implementation should define following operations:

- `add(key, value)`, add association key to value to the multimap
- `remove(key, value)`, remove association between key and value
- `getValues(key)`, return all values for the specific key
- `removeAll(key)`, remove all associations for the specific key

``` java
public class MultiMap<K, V> {
    private Map<K, Set<V>> map = new Map<>();

    public void add(K key, V value) {
        Set<V> set = map.get(key);
        if (set == null) {
            set = new Set<V>();
            map.add(key, set);
        }
        set.add(value);
    }

    public Set<V> getValues(K key) {
        return map.get(key);
    }

    public void remove(K key, V value) {
        Set<V> set = map.get(key);
        if (set != null) set.remove(value);
    }

    public void removeAll(K key) {
        map.remove(key);
    }
}
```

Nothing special here. Each method implementation is playing with internal map.  
That was just one more example how powerful is map data structure.

### 12. BiMap

Map data structure can effectively answer the question _"what is the value for the specific key?"_

If you want to find out _"what is the key for specific value?"_ you need to get a map iterator and do a search
over all elements. That's not very effective, so we introduce another data structure called **BiMap**.

BiMap is like regular map, but it ensure uniqueness for both keys and values.
So every key has only one value, and every value has only one key (this is called [bijection](https://en.wikipedia.org/wiki/Bijection))

BiMap is applicable in number of usecases, for example you want associate IP adress with domain name.
Or color name with it's code, so that user can see readable color, but your css styles will use actual rgb value.

Because of uniqueness we could provide effective implementation for getting key by value.

``` java
public class BiMap<K, V> {
    private Map<K, V> directMap = new Map<>();
    private Map<V, K> inverseMap = new Map<>();

    public void add(K key, V value) {
        directMap.add(key, value);
        inverseMap.add(value, key);
    }

    public V getByKey(K key) {
        return directMap.get(key);
    }

    public K getByValue(V value) {
        return inverseMap.get(value);
    }

    public void removeByKey(K key) {
        directMap.remove(key);
    }

    public void removeByValue(V value) {
        inverseMap.remove(value);
    }
}
```

The trick is simple, back BiMap by two maps: one from key to value, another from value to key.
Then route the calls either to map one or to map two.
This way both `getByKey` and `getByValue` has `O(1)` complexity. 

However, this implementation has one major flow. Can you spot it?

Try harder.  
Maybe, usage example will help you.

``` java
BiMap<String, String> bimap = new BiMap<>();
bimap.add("white", "#FFFFFF");
bimap.add("black", "#000000");
bimap.add("red", "#FF0000");
bimap.add("green", "#00FF00");
bimap.add("blue", "#0000FF");
System.out.println(bimap.getByKey("white")); // #FFFFFF
System.out.println(bimap.getByValue("#FFFFFF")); // white
bimap.removeByKey("white");
System.out.println(bimap.getByKey("white")); // null
System.out.println(bimap.getByValue("#FFFFFF")); // white
```

The association is not removed if you access by value. Let's discuss what happened.

We used _data duplication_.
Our BiMap is based on two maps, each map contains the same data, because when we add value we add to both. 
This data duplication allows us to speedup access to the bimap, providing instant `getByKey` and `getByValue`.
However when we removed key from one map, it's still present in another map. This effect is called **data inconsistency**.

When you design new data structure, first try to avoid data duplication.
If you don't have extra variables you don't need to keep them in sync and care about consistency. 

If you need duplication for the sake of performance, always make sure you don't have data inconsistency.
Because is wrong data worse than no data. 

Some recommendations to avoid data incosistency.
																  
- **Write tests**. This is always the simplest thing we all can do.
- **Always implement toString for your data structures**. Having string representation of data structure simplifies debugging and allows to make snapshots of current data inside while it evolves.
- **Make invariants before and after method calls**. For example, if you have method `add` it should increase the size for the data structure by one, and `remove` on non-empty collection should decrease size by one.
- **Track the size of duplicated object**. For the BiMap usecase we can easily detect inconsistency if we track size for both maps after each operation. They always should be equal.

However, the fix for out BiMap is extrememly simple.
When you `removeByKey`, get value for this key, and remove the association from both maps.

``` java
public void removeByKey(K key) {
    V value = directMap.get(key);
    directMap.remove(key);
    inverseMap.remove(value);
}
```

Similar technique could be applied to `removeByValue`.

### 13. LinkedMap

`Map`, `Set` and their `Multi-` extensions are examples of unordered data structures.
The iteration for those data structures always ensures that _all_ elements will be returned, but their order is unknown.
Moreover, in the current implementations there is no way to maintatin insertion order.

If you put 3 values in the set `1`, `2` and `3` and then iterate over the set you can get `1`,`2`,`3` or `3`,`1`,`2`.
Always check if data structure is ordered when your logic is based on this order.

Hovewer, for several use cases it's important to know the insertion order.

_Consider the active user list for some web application.
When user log in, we put in the set his ID. 
When user log out we remove it's entry from the map.
For active users which are currently in the set we want to show them
on the screen in order they appeared in the system.
As a solution, you can add ID and timestamp to the map, and then apply sorting algorithm by timestamp, but insertion
order already contains sorted data, so it's much simpler to use LinkedSet instead._

Since most of our data structures are based on Map, we provide an extension to the map which
maintains insertion order, datastructure called `LinkedMap`. If you build `Set` based on `LinkedMap` instead of `Map` you will get `LinkedSet` and so on. The goal here to understand how we add support of insertion order to some data structure.

`LinkedMap` behaviour should be the same as a regular `Map`, but iterator should go over elements in order of insertion.

We have already seen data structures which maintain specific order: dynamic array, linked list and doubly-linked list.
Probably we should incorporate some of this data structures into the map.

Let's chop them one by one.

`Dynamic Array` doesn't support fast removal from the middle, which is important for map.  
`LinkedList` supports fast removal from the middle, but only if you reference to previous node. We don't have such reference.  
`DoublyLinkedList` supports instant removal node from the middle, if you have a reference to this node.

Looks like doubly-linked list is exactly what we need.
But if you recall the structure of the map, each bucket has a linked list which
have reference to next node inside the bucket. So it's total of 3 references per node!

The picture will help to understand why three references required per node.

<img src="/images/ds/linked_map_flow.png" alt="img" style="width: 100%;"/>

The order list should be a doubly linked list with references `before`/`after`.
Bucket list still could be a linked list, with reference `next`.

But two these data structures should share the same nodes, how that's even possible?

As we already said, each node in linked map should have three references.

- `next` is a reference to next node in bucket list
- `after` is a next reference in insertion order list
- `before` is a prev reference in insertion order list

So we start by creating a node object to fit these requirements

``` java
class LinkedMapNode<T> {
    T data;

    LinkedMapNode<T> next;
    LinkedMapNode<T> before;
    LinkedMapNode<T> after;
	
    public LinkedMapNode(T data) {
        this.data = data;
    }
}
```

Since it will be kinda mixed data structure, doubly-linked list for ordering and singly-linked list for buckets
we define node references inside `LinkedMap` implementation.

``` java
public class LinkedMap<K, V> {
    private LinkedMapNode<Pair<K, V>>[] buckets;
    private LinkedMapNode<Pair<K, V>> orderListHead;
    private LinkedMapNode<Pair<K, V>> orderListTail;
```

As in regular map, buckets array serve the same purpose.
Element stored in bucket define a head for linked list for this bucket.
Using `LinkedMapNode.next` you could move inside the bucket.

New things here `orderListHead` and `orderListTail`, those are references for ordering.
`orderListHead` needed to track the head of the doubly-linked list, if you use iteration for example.
`orderListTail` required to allow insertion to the end of doubly-linked list.

For **singly-linked list part of the map** (moving inside the bucket) we should define two functions

Add node to the bucket

``` java
private void addToBucket(LinkedMapNode<Pair<K, V>> node, int bucketIndex) {
    node.next = buckets[bucketIndex];
    buckets[bucketIndex] = node;
}
```

The implementation looks exactly like adding element to the beginning of linked list

Get node from the bucket, by predicate

``` java
private LinkedMapNode<Pair<K, V>> findByPredicate(LinkedMapNode<Pair<K, V>> startNode,
                                                  Predicate<Pair<K, V>> predicate) {
    LinkedMapNode<Pair<K, V>> temp = startNode;
    while (temp != null) {
        if (predicate.test(temp.data)) break;
        temp = temp.next;
    }
    return temp;
}
```

And lastly, delete node from the bucket by predicate, we have already seen methods like these in regular linked list implementation.

``` java
private LinkedMapNode<Pair<K, V>> deleteByPredicateFromBucket(int bucketIndex,
                                                              Predicate<Pair<K, V>> predicate) {
    LinkedMapNode<Pair<K, V>> startNode = buckets[bucketIndex];
    if (startNode == null) return null;
    // if first element in bucket
    if (predicate.test(startNode.data)) {
        buckets[bucketIndex] = startNode.next;
        return startNode;
    } else {
        LinkedMapNode<Pair<K, V>> prev = startNode;
        LinkedMapNode<Pair<K, V>> node = startNode.next;
        while (node != null) {
            if (predicate.test(node.data)) {
                prev.next = node.next;
                return node;
            }
            prev = node;
            node = node.next;
        }
        return null;
    }
}
```

The trick here is to return deleted node from the linked list, so it can be removed from doubly linked list as well later.

For **doubly-linked list part of the map** we also need functions for adding and removal.

``` java
private void addToOrderList(LinkedMapNode<Pair<K, V>> node) {
    if (node == null) return;
    node.before = orderListTail;
    if (orderListTail != null) orderListTail.after = node;
    orderListTail = node;
    if (orderListHead == null) orderListHead = node;
}
```

`Add` is adopted from the `addLast` method of the doubly linked list.

Delete by node reference we haven't discussed yet, but it's also really simple

<img src="/images/ds/doubly_linked_list_delete_ref.png" alt="img" style="width: 100%;"/>

If you add all checks, it will look like this

``` java
private void deleteFromOrderList(LinkedMapNode<Pair<K, V>> node) {
    if (node == null) return;
    // if last and only node
    if (node.before == null && node.after == null) {
        orderListHead = null;
        orderListTail = null;
    } else if (node.before == null) { // if first node
        node.after.before = null;
        orderListHead = node.after;
    } else if (node.after == null) { // if last node
        node.before.after = null;
        orderListTail = node.before;
    } else {
        node.before.after = node.after;
        node.after.before = node.before;
    }
}
```

We added everything what we need for managing bucket lists and order list, so let's actually implement a map.

Add element to the `LinkedMap`

``` java
public void add(K key, V value) {
    int index = Math.abs(key.hashCode()) % buckets.length;
    LinkedMapNode<Pair<K, V>> list = buckets[index];
    LinkedMapNode<Pair<K, V>> node = findByPredicate(list, p -> p.key.equals(key));
    if (node != null) {
        node.data.value = value;
    } else {
        LinkedMapNode<Pair<K, V>> newNode = new LinkedMapNode<>(new Pair<K, V>(key, value));
        addToBucket(newNode, index);
        addToOrderList(newNode);
        size++;
    }
    if (fillPercentage() >= 0.8) rehash(buckets.length * 2);
}
```

See, how similar to standard map implementation it looks. We add node to specific bucket and then the same reference we add to order list.

`Get` has no difference, except the changed node type. 

``` java
public V get(K key) {
    int index = Math.abs(key.hashCode()) % buckets.length;
    LinkedMapNode<Pair<K, V>> list = buckets[index];
    LinkedMapNode<Pair<K, V>> node = findByPredicate(list, p -> p.key.equals(key));
    if (node == null) return null;
    return node.data.value;
}
```

`Remove` pretty straightforward as well

``` java
public void remove(K key) {
    int index = Math.abs(key.hashCode()) % buckets.length;
    LinkedMapNode<Pair<K, V>> list = buckets[index];
    LinkedMapNode<Pair<K, V>> deletedNode = deleteByPredicateFromBucket(index, p -> p.key.equals(key));
    deleteFromOrderList(deletedNode);
    if (deletedNode != null) size--;
    if (buckets.length > 10 && fillPercentage() <= 0.2) rehash(buckets.length / 2);
}
```

Delete from the bucket, and than use the reference to delete from order list.

All map operations provided, and, finally we can make insertion order aware iterator

``` java
@Override
public Iterator<Pair<K, V>> iterator() {
    return new Iterator<Pair<K, V>>() {
        LinkedMapNode<Pair<K, V>> nextNode = orderListHead;

        @Override
        public boolean hasNext() {
            return nextNode != null;
        }

        @Override
        public Pair<K, V> next() {
            Pair<K, V> pair = nextNode.data;
            nextNode = nextNode.after;
            return pair;
        }
    };
}
```

Remeber, that we use `after/before` reference to move over insertion order list.

That's all. Our map works, it maintains the insertion order!

``` java
LinkedMap<String, Integer> lmap = new LinkedMap<>();
lmap.add("Angelina", 27);
lmap.add("Brad", 52);
lmap.add("Casey", 31);
lmap.add("Donald", 80);
lmap.add("Elijah", 45);
lmap.add("Fez", 4);

lmap.remove("Brad");
lmap.remove("Donald");
lmap.remove("Fez");

System.out.println(lmap); // {Angelina -> 27, Casey -> 31, Elijah -> 45}
```

What about rehashing?

Rehashing is simple, because you don't need to change insertion order references.
Just move through each node and put it into the newly calculated bucket.
Like `addToTheBucket` but to the new array.

Alhough our implementation for the `LinkedMap` may look a bit clumsy, it should give you one point for understanding data structures.
Especially, the useful skill for combining partial features of some datastructures. 

### 14. Queue

**Queue** is another classical example of ordered data structure.
The order it defines called **FIFO** (_First In, First Out_)

You can put as many elements into the queue as you want, but have access only to the first one.
If you processed it and removed, you will get access to the next one and so on.

_Imagine people standing in line in the supermarket.
Cashier could serve only one person at a time, and start from those who arrived earlier.
When new person arrived to the line, it becomes
the last person an will be served only if all people before him are served._

Pretty simple, huh?

<img src="/images/ds/queue.png" alt="img" style="width: 100%;"/>

The methods defined for the queue are

- `offer` add element to the end of the queue
- `poll` get first element from the queue and _remove it_
- `peek` get first element from the queue and do not remove it

Since we need add element to the end of the queue and return from the beginning, we could use doubly-linked list
and its `addLast` and `deleteFirst` functions. But memory overhead from doubly linked list is not what we want.
Single linked list is not a good fit, because you can't add to the end of single linked list effectively.

The trick is to use single linked list and track one more reference for the last node.

``` java
public class Queue<T> {
    private Node<T> head;
    private Node<T> tail;

    public void offer(T elem) {
        Node<T> node = new Node<T>(elem);
        if (head == null && tail == null) { // queue is empty yet
            head = node;
            tail = node;
        } else {
            tail.next = node;
            tail = node;
        }
    }

    public T poll() {
        if (head == null) throw new IllegalArgumentException("Queue is empty");
        T elem = head.data;
        head = head.next;
        if (head == null) tail = null;
        return elem;
    }

    public T peek() {
        if (head == null) throw new IllegalArgumentException("Queue is empty");
        return head.data;
    }
}
```

Nothing new from the functional perspective, `offer` looks like adding element after specific node in linked list
and `poll` looks like removing first element from the linked list. Both works in `O(1)` time complexity.

Despite its simplicity queue data structure very effective in practical application.
For example its a basis for breadth-first search algorithm which we discuss later.
Some parsing and graphics rendering techniques involves queues.
Also queue is a natural choice as a processing buffer in web frameworks.
Requests which come first should be served first.

### 15. Stack

**Stack** is an opposite data structure to the queue. It defines an order **LIFO** (_Last In, First Out_)

That means new elements will be added to the end of the stack, but retrieval starts from the end as well.

_Eddie is lazy interviewer.
Everytime, Christine from HR department comes to Eddie, she brings new CV for the candidate
and put it in a pile of other CVs.
Eddie is smart, so he waits until a pile become large enough.
Then he starts processing CVs and obviously he take first CV from the pile, discards it, then second and so on.
Eddie has not finished with this pile yet and, suddenly, Christine interrupts him and brings another bunch of CVs.
Eddie put new CVs at the top of unprocessed pile and go home. New work should be done in new day.
The story is finished, but not for the candidate Johnnie Jarco.
He submitted his resume to the company month ago, but still get no response.
Pile of CVs operates like a stack data structure, not queue, so if you send your CV first... sorry._

Altough we empathize Johnnie, the Stack is not guilty. 

<img src="/images/ds/stack.png" alt="img" style="width: 100%;"/>

Like queue, stack also has three operations, but they called differently.

- `push` adds element to end of the stack
- `pop` get and remove last element from the stack
- `peek` get last element from the stack

Since we add and remove elements from one end (we don't care if its beginning or end),
the standard `LinkedList` could be used.

``` java
public class Stack<T> {
    LinkedList<T> list = new LinkedList<T>();

    public void push(T elem) {
        list.add(elem);
    }

    public T pop() {
        if (list.head == null) throw new IllegalArgumentException("Stack is empty");
        T value = list.head.data;
        list.tail();
        return value;
    }

    public T peek() {
        if (list.head == null) throw new IllegalArgumentException("Stack is empty");
        return list.head.data;
    }
}		
```

That's all.

Although, stack is even simpler than the queue, it has enormous amount of applications.
Depth-first search, parsers and arithmetic evaluators, history functionality (aka _undo_ button).
Need to say, any recursive algorithm could be transformed in non-recursive by using stack.

### 16. Deque

If you marry `Queue` and `Stack` data structures, you will get a thing called **Deque**.

Deque (or dequeue) is a _double-ended queue_.
That means you can add elements to both ends as well as read them from both ends.

<img src="/images/ds/deque.png" alt="img" style="width: 100%;"/>

As you might guess, we can inplement such datastructure by using doubly-linked list, all required methods are already available.

``` java
public class Deque<T> {
    DoublyLinkedList<T> list = new DoublyLinkedList<T>();

    public void pushFirst(T elem) {
        list.addFirst(elem);
    }

    public T popFirst() {
        if (list.head == null) throw new IllegalArgumentException("No such element");
        T e = list.head.data;
        list.deleteFirst();
        return e;
    }

    public T peekFirst() {
        if (list.head == null) throw new IllegalArgumentException("No such element");
        return list.head.data;
    }

    public void pushLast(T elem) {
        list.addLast(elem);
    }

    public T popLast() {
        if (list.last == null) throw new IllegalArgumentException("No such element");
        T e = list.last.data;
        list.deleteLast();
        return e;
    }

    public T peekLast() {
        if (list.last == null) throw new IllegalArgumentException("No such element");
        return list.last.data;
    }

    @Override
    public String toString() {
        return list.toString();
    }
}
```

The iterator thing for the Deque is unclear. Even if you have a formal distinction between `head` and `last` elements,
they are equally interchangeble. For example instead of pair `pushFirst/popFirst` for stack you can use pair `pushLast/popLast` and nothing
changes.

For such case where we have two ways of iterating over data structure (from head to tail and vice versa)
we coud define two different iterators, or iterables as you wish.

``` java
public Iterable<T> iterateFromHead() {
    return new Iterable<T>() {
        @Override
        public Iterator<T> iterator() {
            return new Iterator<T>() {
                private DoublyNode<T> current = list.head;
                @Override
                public boolean hasNext() {
                    return current != null;
                }
                @Override
                public T next() {
                    T elem = current.data;
                    current = current.next;
                    return elem;
                }
            };
        }
    };
}
```

If it looks clumsy for you, use non-anonymous classes instead. Anyway the usage for such iterator is simple.

``` java
for (Integer i : deque.iterateFromHead()) {
    System.out.println(i);
}
```

The `reverseIterator` could be defined the same way, use `tail` as a starting node and move by `prev` references.

The deque could be used to simulate the waiting line, similar to queue.
The problem with the queue, however, that elements added to the end can not be processed until all elements before it processed.

Imagine the line where you standing last and there are hundred people before you, it's better for you to leave and find another line.
The deque allows us to remove elements from the back.

### 17. Circular Buffer

All three data structures dicussed above `Queue`, `Stack` and `Deque` are examples of unbounded data structures.
You can put as many elements as you want. For some cases you want to make them bounded to specific size,
to say it could not contain more than `100` elements.

One simple way to do it, is to track the current size and reject new elements if size reached limit.

**Circular Buffer** (or ring buffer) data structure use another approach.
Instead of rejecting new values, it discard the oldest one, so there is place for new one.
New value put at the end of the buffer, so it's basically, a bounded queue implemented on arrays.

For example when you have full circular buffer of size `5`, `[1, 2, 3, 4, 5]`.
Adding next element `6` produces such array `[2, 3, 4, 5, 6]`.

Why arrays? You could add extra logic to the old-good queues based on linked list.
But since we know it should be bounded, we allocate one array and for the rest of the application life
we can use this array, no more allocation required.

<img src="/images/ds/circular_buffer.png" alt="img" style="width: 100%;"/>

Although it's called circular, no extra references required. Also no array shifts required.
You can move index over circular buffer by incrementing it and then take modulo by array length.

Consider, the array of size `5`.
First index is `0`.
If you increment you get next element, index `1`.
Then `2`, `3`, `4`, `5`. As soon as you get `5` the mod by array length will be `5 % 5 = 0`, so after reaching the last array index
you go back to the `0` index again. That's why it's called circular and on this simple trick based whole data structure.

Let's implement it. Start from the internals.

``` java
public class CircularBuffer<T> {
    private T[] array;
    private int headIndex = 0;
    private int tailIndex = 0;
    private int size;

    public CircularBuffer(int maxSize) {
        array = (T[]) new Object[maxSize];
    }
}
```

Array for data, two indices for head/tail and size.

Because it's stack data structure we need to implement `offer` and `poll`

Adding element is basically writing data to current `tailIndex`, move it no next cell and incrementing the size.

``` java
array[tailIndex] = elem;
tailIndex = (tailIndex + 1) % array.length; // circular increment
size++;
```

Polling from such queue is read the value under the `headIndex`, move it to next cell and decrement the size.

``` java
T value = array[headIndex];
array[headIndex] = null;
headIndex = (headIndex + 1) % array.length;
size--;
```

It works until the buffer is full.
Then when `tailIndex` overwrites old values, we should move head as well. The size for the queue doesn't change in that case.

The final set of methods look like this

``` java
public void offer(T elem) {
    if (tailIndex == headIndex && size > 0) { // if we approach the headIndex, move it
        headIndex = (headIndex + 1) % array.length;
    } else {
        size++;
    }
    array[tailIndex] = elem;
    tailIndex = (tailIndex + 1) % array.length;
}


public T poll() {
    if (size == 0) throw new IllegalArgumentException("No such element");
    T value = array[headIndex];
    array[headIndex] = null;
    headIndex = (headIndex + 1) % array.length;
    size--;
    return value;
}
```

Circular buffers often used for the hardware operations, drivers and for the problems like
"keep the last N items". _elaborate more_

### 18. Binary Tree

**Binary tree** is like linked list, but every node have links for two other nodes `left` and `right`.
Not confuse this with doubly linked nodes, they have references to the `next` and `prev` so you can move in two directions. Structure for doubly linked list is still linear.
For binary tree nodes you have no references back, you can move in one direction,
but on every node you could choose, move left or move right.

The best way to ilustrate this with the following picture

<img src="/images/ds/binary_tree.png" alt="img" style="width: 100%;"/>

Instead of head node, you have `root` node, _one_ entry point to your tree.
From this node you have links to next level `left` or `right` node.
These nodes are children for the `root` node.
Each of the left and right can have their left and right nodes on next level and so on.

The important requirement for the binary tree is each node could have only one parent, so multiple links to the same node are not allowed.

<img src="/images/ds/binary_tree_two_links.png" alt="img" style="width: 100%;"/>

If to write numbers for each tree level, for first level you have `1` node, then `2`, `4`, `8` and so on. Each level
contains $$2^n$$ nodes. If node has no left or right children it refers to null.

The distance between first and last level of the tree defines it's **height**.

<img src="/images/ds/binary_tree_height.png" alt="img" style="width: 100%;"/>

The tree with height `2` can contain $$1 + 2 + 4 = 7$$ elements. `7` looks like $$2^3-1$$.
There is generic formula for the max number of elements in the binary tree

$$N_{elements} = 2^{height + 1} - 1$$

or if you reverse the formula to get height by number of elements you will get

$$Height_{tree} = \log_2 ({N + 1}) - 1$$

Or approximately $$log(N)$$, later we will see why this logarithm is important.

The tree could be _incomplete_, means that some nodes contain more children than others.
The nodes which has no children (both left and right points to null) called **leaf** nodes.

<img src="/images/ds/binary_tree_leaf_nodes.png" alt="img" style="width: 100%;"/>

Ok, enough theory, let's implement the binary tree.

``` java
public class BinaryTreeNode<T> {
    public T data;
    public BinaryTreeNode<T> left;
    public BinaryTreeNode<T> right;

    public BinaryTreeNode(T data) {
        this.data = data;
    }
}
```

Having these simple class you can create a tree, by referencing root node

``` java
BinaryTreeNode<Integer> root = new BinaryTreeNode<>(1);
root.left = new BinaryTreeNode<>(2);
root.right = new BinaryTreeNode<>(3);

root.left.left = new BinaryTreeNode<>(4);
root.left.right = new BinaryTreeNode<>(5);
root.right.left = new BinaryTreeNode<>(6);
root.right.right = new BinaryTreeNode<>(7);
```

Not very fancy way of operating on data structure.
Let's define few methods on node that we discussed before.

Leaf node have both references as null

``` java
public boolean isLeaf() {
    return left == null && right == null;
}
```

Height of the node could be defined recursively. If node is leaf it's height is 0.
Otherwise node height equal to `1 +`, either left node height or right node height.
For height we take the greater value so here is `Math.max`

``` java
public int height() {
    if (isLeaf()) return 0;
    int leftHeight = left == null ? 0 : left.height();
    int rightHeight = right == null ? 0 : right.height();
    return Math.max(leftHeight, rightHeight) + 1;
}
```

The height for binary tree is a height of its root node.

Somehow we need to get all elements from the tree. Since it's not a linear data structure not clear
in what order should we iterate on the tree. There are three basic traversal strategies which are done recursively.

- Preorder (print current node value, preorder traverse left tree, preorder traverse right tree)
- Inorder (inorder traverse left tree, print node value, inorder traverse right tree)
- Postorder (postorder traverse left, postorder traverse right, print node value)

For example if you want to do preorder traversal, you will have method like this

``` java
public void preorder() {
   System.out.println(data);
   if (left != null) left.preorder();
   if (right != null) right.preorder();
}
```

For the tree we defined above it prints `1 2 4 5 3 6 7`.
You could play with `inorder` and `postorder` traversal and even define them like iterators.

To search over the tree, two common strategies exist, breadth-first search and depth-first search.

The difference between them could be shown visually.

<img src="/images/ds/binary_tree_search.png" alt="img" style="width: 100%;"/>

Always starting from the root node, the BFS do search on layers, layer 0, then layer 1, then layer 2 and so on
until it found the specific node. On the other side, DFS is focused on depth, it checks the first path to the leftmost leaf node,
then another path, etc.

Both algorithms are interchangeable and both have their strength and weaknesses in certain situations.

For example BFS could be implement using Queue data structure.

``` java
public BinaryTreeNode<T> bfs(Predicate<T> predicate) {
    Queue<BinaryTreeNode<T>> queue = new Queue<>();
    queue.offer(this);
    while (!queue.isEmpty()) {
        BinaryTreeNode<T> node = queue.poll();
        if (predicate.test(node.data)) return node; // <-- FOUND
        if (node.left != null) queue.offer(node.left);
        if (node.right != null) queue.offer(node.right);
    }
    return null;
}
```

The idea is to init the queue with one root node, our starting point.
Then poll node from the queue, if it matches the predicate we done.
If it doesn't match the predicate, put left and right subtrees in the queue and repeat.

DFS could be implemented similar way if you choose Stack instead of Queue.

BFS - Queue.  
DFS - Stack.  

Mnemonic rule is in alphabet, _abcde...pqrst.._  
B comes before D, the same way as Q comes before S (even more the same distance between them!)

Now when we have methods to search specific node, we can discuss insertion and deletion.

Recall linked list. For linked list we have _two_ ways to insert a new node, to the beginning and _after_ any other node.
Both in `O(1)`.

For binary trees situation is a bit complex, because we have 6 different ways to insert node.

- **Root Left**, new node becomes the root node and its `left` ref points to the old root, `right` points to null
- **Root Right**, new node becomes the root node and its `right` ref points to the old root, `left` points to null
- **Insert After Left Left**, new node inserted after some node `A` as its `left` ref. The old `left` ref for `A` becomes `left` ref for new node.
- **Insert After Left Right**, new node inserted after some node `A` as its `left` ref. The old `left` ref for `A` becomes `right` ref for new node.
- **Insert After Right Left**, new node inserted after some node `A` as its `right` ref. The old `right` ref for `A` becomes `left` ref for new node.
- **Insert After Right Right**, new node inserted after some node `A` as its `right` ref. The old `right` ref for `A` becomes `right` ref for new node.

These may sound a bit complicated so check the picture

<img src="/images/ds/binary_tree_insertion.png" alt="img" style="width: 100%;"/>

Defining all these 6 methods could be a bit routine, but nothing complex so far.
For example insert after left left could be implemented like this.

``` java
public BinaryTreeNode<T> insertAfterLeftLeft(T value) {
    BinaryTreeNode<T> newNode = new BinaryTreeNode<T>(value);
    newNode.left = this.left;
    this.left = newNode;
    return newNode;
}
```

Keep in mind we always return reference to the new inserted node, to allow future calls on it.
All insert operations on the tree if you have reference for node happening in `O(1)`

Deletion is simpler, because there are not so many cases.
First of all deletion of the root is not allowed. If you delete the root, who will become the new root?

The same way, deletion of the any other node which has two children is not allowed, who become a new parent?

If node is leaf (has no children) it could be safely deleted by setting its parent ref `left` or `right` to null.  
If node has one child, its parent could delete it and inherit its child.  

<img src="/images/ds/binary_tree_deletion.png" alt="img" style="width: 100%;"/>

``` java
public void deleteLeft() {
    BinaryTreeNode<T> node = this.left;
    // two children, fail
    if (node.left != null && node.right != null)
        throw new IllegalArgumentException("Can not delete node with two children");
    // zero children, node removed
    if (node.left == null && node.right == null) this.left = null;
    // one child, node removes, parent inherit the child
    if (node.left != null) this.left = node.left;
    if (node.right != null) this.left = node.right;
}
```

Binary Tree data is not very useful by itself.
You can construct the tree, traverse it, search for specific element and that's all.

Later we will see how advanced data structures reuse binary tree functionality to provide powerful features.

### 19. Binary Tree Array

The nice trick with binary tree is it can be represented using static arrays.
Right, one linear array which represents hierarchical data structure.

The trick is based on observation, if you enumarate all tree elements using BFS traversal (level by level)
they can be packed into array, and still maintain parent-child relationship.

<img src="/images/ds/binary_tree_array.png" alt="img" style="width: 100%;"/>

The element `a[i]` has two children `a[2*i+1]` and `a[2*i+2]`.
If you try to get a child for leaf node, you will get `ArrayIndexOutOfBounds` so make sure it's handled properly.

If some node doesn't have a child, just set child cell to null.

Although dynamic methods like `insert`/`delete` could not be applied easiely on static arrays, you still
can use tree traversal and search algorithms for such array representation.

BFS is obviosuly simple, just iterate through array.

``` java
public int bfs(Predicate<T> predicate) {
    for (int i = 0; i < nodes.length; i++) {
        if (nodes[i] != null && predicate.test(nodes[i])) return i;
    }
    return -1; // not found
}
```

DFS is a bit harder, but basically it's all about choosing correct index

``` java
TODO
```

### 20. Binary Search Tree

### Next

- Return all elements from container
