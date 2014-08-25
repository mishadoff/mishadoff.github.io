---
layout: post
title: "Java Magic. Part 4: sun.misc.Unsafe"
date: 2013-02-26 02:37
comments: true
categories: [programming, java]
published: true
---

Java is a safe programming language and prevents programmer
from doing a lot of stupid mistakes, most of which based on memory management.
But, there is a way to do such *mistakes* intentionally, using `Unsafe` class.

This article is a quick overview of `sun.misc.Unsafe` *public* API and few
interesting cases of its usage.

<!-- more -->

### Unsafe instantiation

Before usage, we need to create instance of `Unsafe` object.
There is no simple way to do it like `Unsafe unsafe = new Unsafe()`,
because `Unsafe` class has private constructor. It also has static
`getUnsafe()` method, but if you naively try to call `Unsafe.getUnsafe()` you, probably,
get `SecurityException`. Using this method available only from trusted code.

``` java
public static Unsafe getUnsafe() {
    Class cc = sun.reflect.Reflection.getCallerClass(2);
    if (cc.getClassLoader() != null)
        throw new SecurityException("Unsafe");
    return theUnsafe;
}
```

This is how java validates if code is trusted.
It is just checking that our code was loaded with
primary classloader.

We can make our code "trusted". Use option `bootclasspath` when running
your program and specify
path to system classes plus your one that will use `Unsafe`.

``` java
java -Xbootclasspath:/usr/jdk1.7.0/jre/lib/rt.jar:. com.mishadoff.magic.UnsafeClient
```

But it's too hard.

`Unsafe` class contains its instance called `theUnsafe`, which marked as `private`.
We can steal that variable via java reflection.

``` java
Field f = Unsafe.class.getDeclaredField("theUnsafe");
f.setAccessible(true);
Unsafe unsafe = (Unsafe) f.get(null);
```

*Note:* Ignore your IDE. For example, eclipse show error "Access restriction..."
but if you run code, all works just fine. If the error is annoying, ignore errors on
`Unsafe` usage in:

    Preferences -> Java -> Compiler -> Errors/Warnings ->
    Deprecated and restricted API -> Forbidden reference -> Warning

### Unsafe API

Class [sun.misc.Unsafe](http://www.docjar.com/docs/api/sun/misc/Unsafe.html)
consists of `105` methods. There are, actually,
few groups of important methods for manipulating with various entities.
Here is some of them:

* **Info**. Just returns some low-level memory information.
  - `addressSize`
  - `pageSize`

* **Objects**. Provides methods for object and its fields manipulation.
  - `allocateInstance`
  - `objectFieldOffset`

* **Classes**. Provides methods for classes and static fields manipulation.
  - `staticFieldOffset`
  - `defineClass`
  - `defineAnonymousClass`
  - `ensureClassInitialized`

* **Arrays**. Arrays manipulation.
  - `arrayBaseOffset`
  - `arrayIndexScale`

* **Synchronization**. Low level primitives for synchronization.
  - `monitorEnter`
  - `tryMonitorEnter`
  - `monitorExit`
  - `compareAndSwapInt`
  - `putOrderedInt`

* **Memory**. Direct memory access methods.
  - `allocateMemory`
  - `copyMemory`
  - `freeMemory`
  - `getAddress`
  - `getInt`
  - `putInt`

### Interesting use cases

#### Avoid initialization

`allocateInstance` method can be *useful* when you need to skip object initialization phase
or bypass security checks in constructor or you want instance of that class
but don't have any public constructor. Consider following class:

``` java
class A {
    private long a; // not initialized value

    public A() {
        this.a = 1; // initialization
    }

    public long a() { return this.a; }
}
```

Instantiating it using constructor, reflection and unsafe gives
different results.

``` java
A o1 = new A(); // constructor
o1.a(); // prints 1

A o2 = A.class.newInstance(); // reflection
o2.a(); // prints 1

A o3 = (A) unsafe.allocateInstance(A.class); // unsafe
o3.a(); // prints 0
```

Just think what happens to all your [Singletons](http://en.wikipedia.org/wiki/Singleton_pattern).

#### Memory corruption

This one is usual for every C programmer.
By the way, its common technique for security bypass.

Consider some simple class that check access rules:

``` java
class Guard {
    private int ACCESS_ALLOWED = 1;

    public boolean giveAccess() {
        return 42 == ACCESS_ALLOWED;
    }
}
```

The client code is *very secure* and calls
`giveAccess()` to check access rules. Unfortunately, for clients,
it always returns `false`. Only privileged users *somehow* can change
value of `ACCESS_ALLOWED` constant and get access.

In fact, it's not true. Here is the code demostrates it:

``` java
Guard guard = new Guard();
guard.giveAccess();   // false, no access

// bypass
Unsafe unsafe = getUnsafe();
Field f = guard.getClass().getDeclaredField("ACCESS_ALLOWED");
unsafe.putInt(guard, unsafe.objectFieldOffset(f), 42); // memory corruption

guard.giveAccess(); // true, access granted
```

Now all clients will get unlimited access.

Actually, the same functionality can be achieved by reflection.
But interesting, that we can modify any object, even ones
that we do not have references to.

For example, there is another `Guard` object in memory
located next to current `guard` object. We can modify its `ACCESS_ALLOWED` field with the following code

``` java
unsafe.putInt(guard, 16 + unsafe.objectFieldOffset(f), 42); // memory corruption
```

Note, we didn't use any reference to this object.
`16` is size of `Guard` object in 32 bit architecture.
We can calculate it manually or use `sizeOf` method, that defined... right now.

#### **sizeOf**

Using `objectFieldOffset` method we can implement C-style `sizeof` function.
This implementation returns *shallow* size of object:

``` java
public static long sizeOf(Object o) {
    Unsafe u = getUnsafe();
    HashSet<Field> fields = new HashSet<Field>();
    Class c = o.getClass();
    while (c != Object.class) {
        for (Field f : c.getDeclaredFields()) {
            if ((f.getModifiers() & Modifier.STATIC) == 0) {
                fields.add(f);
            }
        }
        c = c.getSuperclass();
    }

    // get offset
    long maxSize = 0;
    for (Field f : fields) {
        long offset = u.objectFieldOffset(f);
        if (offset > maxSize) {
            maxSize = offset;
        }
    }

    return ((maxSize/8) + 1) * 8;   // padding
}
```

Algorithm is the following: go through all *non-static* fields including all
superclases, get offset for each field, find maximum and add padding.
Probably, I missed something, but idea is clear.

Much simpler `sizeOf` can be achieved if we just read `size` value from
the class struct for this object, which located with offset 12 in `JVM 1.7 32 bit`.

``` java
public static long sizeOf(Object object){
    return getUnsafe().getAddress(
        normalize(getUnsafe().getInt(object, 4L)) + 12L);
}
```

`normalize` is a method for casting signed int to unsigned long, for
correct address usage.

``` java
private static long normalize(int value) {
    if(value >= 0) return value;
    return (~0L >>> 32) & value;
}
```

Awesome, this method returns the same result as our previous `sizeof`
function.

In fact, for good, safe and accurate `sizeof` function better to use
[java.lang.instrument](http://docs.oracle.com/javase/7/docs/api/java/lang/instrument/package-summary.html) package,
but it requires specifyng `agent` option in your JVM.

#### **Shallow copy**

Having implementation of calculating shallow object size, we can simply
add function that copy objects. Standard solution need modify your code with `Cloneable`,
or you can implement custom copy function in your object, but it won't be multipurpose function.

Shallow copy:

``` java
static Object shallowCopy(Object obj) {
    long size = sizeOf(obj);
    long start = toAddress(obj);
    long address = getUnsafe().allocateMemory(size);
    getUnsafe().copyMemory(start, address, size);
    return fromAddress(address);
}
```

`toAddress` and `fromAddress` convert object to its address in memory and vice versa.

``` java
static long toAddress(Object obj) {
    Object[] array = new Object[] {obj};
    long baseOffset = getUnsafe().arrayBaseOffset(Object[].class);
    return normalize(getUnsafe().getInt(array, baseOffset));
}

static Object fromAddress(long address) {
    Object[] array = new Object[] {null};
    long baseOffset = getUnsafe().arrayBaseOffset(Object[].class);
    getUnsafe().putLong(array, baseOffset, address);
    return array[0];
}
```

This copy function can be used to copy object of any type, its size will be calculated
dynamically. Note that after copying you need to cast object to specific type.

#### Hide Password

One more interesting usage of direct memory access in `Unsafe` is removing
unwanted objects from memory.

Most of the APIs for retrieving user's password, have signature
as `byte[]` or `char[]`. Why arrays?

It is completely for security reason, because we can nullify array elements after we don't need them.
If we retrieve password as `String` it can be saved like an object in memory and nullifying that
string just perform dereference operation. This object still in memory by the time GC decide to perform cleanup.

This trick creates fake `String` object with the same size and replace original one in memory:

``` java
String password = new String("l00k@myHor$e");
String fake = new String(password.replaceAll(".", "?"));
System.out.println(password); // l00k@myHor$e
System.out.println(fake); // ????????????

getUnsafe().copyMemory(
          fake, 0L, null, toAddress(password), sizeOf(password));

System.out.println(password); // ????????????
System.out.println(fake); // ????????????
```

Feel safe.

**UPDATE:** That way is not really safe. For real safety we need to nullify
backed char array via reflection:

``` java
Field stringValue = String.class.getDeclaredField("value");
stringValue.setAccessible(true);
char[] mem = (char[]) stringValue.get(password);
for (int i=0; i < mem.length; i++) {
  mem[i] = '?';
}
```

Thanks to *Peter Verhas* for pointing out that.

#### **Multiple Inheritance**

There is no multiple inheritance in java.

Correct, except we can cast every type to every another one, if we want.

``` java
long intClassAddress = normalize(getUnsafe().getInt(new Integer(0), 4L));
long strClassAddress = normalize(getUnsafe().getInt("", 4L));
getUnsafe().putAddress(intClassAddress + 36, strClassAddress);
```

This snippet adds `String` class to `Integer` superclasses, so we can cast
without runtime exception.

``` java
(String) (Object) (new Integer(666))
```

One problem that we must do it with pre-casting to object. To cheat compiler.

#### Dynamic classes

We can create classes in runtime, for example from
compiled `.class` file. To perform that read class contents
to byte array and pass it properly to `defineClass` method.

``` java
byte[] classContents = getClassContent();
Class c = getUnsafe().defineClass(
              null, classContents, 0, classContents.length);
    c.getMethod("a").invoke(c.newInstance(), null); // 1
```

And reading from file defined as:

``` java
private static byte[] getClassContent() throws Exception {
    File f = new File("/home/mishadoff/tmp/A.class");
    FileInputStream input = new FileInputStream(f);
    byte[] content = new byte[(int)f.length()];
    input.read(content);
    input.close();
    return content;
}
```

This can be useful, when you must create classes dynamically, some proxies
or aspects for existing code.

#### Throw an Exception

Don't like checked exceptions? Not a problem.

``` java
getUnsafe().throwException(new IOException());
```

This method throws checked exception, but your code not forced to catch or rethrow it.
Just like runtime exception.

#### Fast Serialization

This one is more practical.

Everyone knows that standard java `Serializable` capability
to perform serialization is very slow. It also require class
to have public non-argument constructor.

`Externalizable` is better, but it needs to define schema for
class to be serialized.

Popular high-performance libraries, like [kryo](http://code.google.com/p/kryo/)
have dependencies, which can be unacceptable with low-memory requirements.

But full serialization cycle can be easily achieved with unsafe class.

Serialization:

* Build schema for object using reflection. It can be done once for class.
* Use `Unsafe` methods `getLong`, `getInt`, `getObject`, etc. to retrieve actual field values.
* Add `class` identifier to have capability restore this object.
* Write them to the file or any output.

You can also add compression to save space.

Deserialization:

* Create instance of serialized class. `allocateInstance` helps, because does not require any constructor.
* Build schema. The same as 1 step in serialization.
* Read all fields from file or any input.
* Use `Unsafe` methods `putLong`, `putInt`, `putObject`, etc. to fill the object.

Actually, there are much more details in correct inplementation, but intuition is clear.

This serialization will be really fast.

By the way, there are some attempts in `kryo` to use `Unsafe` [http://code.google.com/p/kryo/issues/detail?id=75](http://code.google.com/p/kryo/issues/detail?id=75)

#### **Big Arrays**

As you know `Integer.MAX_VALUE` constant is a max size of java array.
Using direct memory allocation we can create arrays with size limited by only heap size.

Here is `SuperArray` implementation:

``` java
class SuperArray {
    private final static int BYTE = 1;

    private long size;
    private long address;

    public SuperArray(long size) {
        this.size = size;
        address = getUnsafe().allocateMemory(size * BYTE);
    }

    public void set(long i, byte value) {
        getUnsafe().putByte(address + i * BYTE, value);
    }

    public int get(long idx) {
        return getUnsafe().getByte(address + idx * BYTE);
    }

    public long size() {
        return size;
    }
}
```

And sample usage:

``` java
long SUPER_SIZE = (long)Integer.MAX_VALUE * 2;
SuperArray array = new SuperArray(SUPER_SIZE);
System.out.println("Array size:" + array.size()); // 4294967294
for (int i = 0; i < 100; i++) {
    array.set((long)Integer.MAX_VALUE + i, (byte)3);
    sum += array.get((long)Integer.MAX_VALUE + i);
}
System.out.println("Sum of 100 elements:" + sum);  // 300
```

In fact, this technique uses `off-heap memory` and partially
available in `java.nio` package.

Memory allocated this way not located in the heap and not under GC management, so take care of it
using `Unsafe.freeMemory()`. It also does not perform any boundary checks, so any
illegal access may cause JVM crash.

It can be useful for math computations, where code can operate with large arrays of data.
Also, it can be interesting for realtime programmers, where GC delays on large arrays can
break the limits.

#### Concurrency

And few words about concurrency with `Unsafe`.
`compareAndSwap` methods are atomic and can be used to implement
high-performance lock-free data structures.

For example, consider the problem to increment value in the shared object
using lot of threads.

First we define simple interface `Counter`:

``` java
interface Counter {
    void increment();
    long getCounter();
}
```

Then we define worker thread `CounterClient`, that uses `Counter`:

``` java
class CounterClient implements Runnable {
    private Counter c;
    private int num;

    public CounterClient(Counter c, int num) {
        this.c = c;
        this.num = num;
    }

    @Override
    public void run() {
        for (int i = 0; i < num; i++) {
            c.increment();
        }
    }
}
```

And this is testing code:

``` java
int NUM_OF_THREADS = 1000;
int NUM_OF_INCREMENTS = 100000;
ExecutorService service = Executors.newFixedThreadPool(NUM_OF_THREADS);
Counter counter = ... // creating instance of specific counter
long before = System.currentTimeMillis();
for (int i = 0; i < NUM_OF_THREADS; i++) {
    service.submit(new CounterClient(counter, NUM_OF_INCREMENTS));
}
service.shutdown();
service.awaitTermination(1, TimeUnit.MINUTES);
long after = System.currentTimeMillis();
System.out.println("Counter result: " + c.getCounter());
System.out.println("Time passed in ms:" + (after - before));
```

First implementation is not-synchronized counter:

``` java
class StupidCounter implements Counter {
    private long counter = 0;

    @Override
    public void increment() {
        counter++;
    }

    @Override
    public long getCounter() {
        return counter;
    }
}
```

Output:

```
Counter result: 99542945
Time passed in ms: 679
```

Working fast, but no threads management at all, so result is inaccurate.
Second attempt, add easiest java-way synchronization:

``` java
class SyncCounter implements Counter {
    private long counter = 0;

    @Override
    public synchronized void increment() {
        counter++;
    }

    @Override
    public long getCounter() {
        return counter;
    }
}
```

Output:

```
Counter result: 100000000
Time passed in ms: 10136
```

Radical synchronization always work. But timings is awful.
Let's try `ReentrantReadWriteLock`:

``` java
class LockCounter implements Counter {
    private long counter = 0;
    private WriteLock lock = new ReentrantReadWriteLock().writeLock();

    @Override
    public void increment() {
        lock.lock();
        counter++;
        lock.unlock();
    }

    @Override
    public long getCounter() {
        return counter;
    }
}
```

Output:

```
Counter result: 100000000
Time passed in ms: 8065
```

Still correct, and timings are better. What about atomics?

``` java
class AtomicCounter implements Counter {
    AtomicLong counter = new AtomicLong(0);

    @Override
    public void increment() {
        counter.incrementAndGet();
    }

    @Override
    public long getCounter() {
        return counter.get();
    }
}
```

Output:

```
Counter result: 100000000
Time passed in ms: 6552
```

`AtomicCounter` is even better. Finally, try `Unsafe`
primitive `compareAndSwapLong` to see if it is really privilegy to use it.

``` java
class CASCounter implements Counter {
    private volatile long counter = 0;
    private Unsafe unsafe;
    private long offset;

    public CASCounter() throws Exception {
        unsafe = getUnsafe();
        offset = unsafe.objectFieldOffset(CASCounter.class.getDeclaredField("counter"));
    }

    @Override
    public void increment() {
        long before = counter;
        while (!unsafe.compareAndSwapLong(this, offset, before, before + 1)) {
            before = counter;
        }
    }

    @Override
    public long getCounter() {
        return counter;
    }
```

Output:

```
Counter result: 100000000
Time passed in ms: 6454
```

Hmm, seems equal to atomics. Maybe atomics use `Unsafe`? (*YES*)

In fact this example is easy enough, but it shows some power of `Unsafe`.

As I said, `CAS` primitive can be used to implement lock-free data structures.
The intuition behind this is simple:

* Have some state
* Create a copy of it
* Modify it
* Perform `CAS`
* Repeat if it fails

Actually, in real it is more hard than you can imagine. There are a lot of problems like
[ABA Problem](http://en.wikipedia.org/wiki/ABA_problem), instructions reordering, etc.

If you really interested, you can refer to the awesome presentation about [lock-free HashMap](http://www.azulsystems.com/about_us/presentations/lock-free-hash)

**UPDATE:** Added `volatile` keyword to `counter` variable to avoid risk of infinite loop.

Kudos to *Nitsan Wakart*

### Bonus

Documentation for `park` method from `Unsafe` class contains
longest English sentence I've ever seen:

> Block current thread, returning when a balancing
> unpark occurs, or a balancing unpark has
> already occurred, or the thread is interrupted, or, if not
> absolute and time is not zero, the given time nanoseconds have
> elapsed, or if absolute, the given deadline in milliseconds
> since Epoch has passed, or spuriously (i.e., returning for no
>"reason"). Note: This operation is in the Unsafe class only
> because unpark is, so it would be strange to place it
> elsewhere.

### Conclusion

Although, `Unsafe` has a bunch of useful applications, never use it.
