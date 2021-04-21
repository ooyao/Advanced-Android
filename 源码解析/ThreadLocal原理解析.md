 `ThreadLocal` 是用于创建线程局部变量的。多数 Android 开发者是从 `Handler` 机制源码中了解的。



通常情况下，我们创建一个变量是可以被任何一个线程访问并修改，而通过 `ThreadLocal` 创建的变量则可以保证只能被当前线程访问，其他线程无法访问和修改此变量。



# ThreadLocal 用法

`ThreadLocal`是一个泛型类，其泛型就是我们需要保存值的类型。

```kotlin
// 定义一个保存 String 类型的 ThreadLocal
val threadLocal = ThreadLocal<String>()
// 调用 set 方法保存值
threadLocal.set("ooyao")
// 调用 get 方法获取值
val value = threadLocal.get()
```



`ThreadLocal`还提供了`initialValue`方法用于设置`get`方法的默认值。



```kotlin
// 定义一个保存 String 类型的 ThreadLocal，并设置默认值
val threadLocal = object : ThreadLocal<String>() {
  override fun initialValue(): String {
    return "ooyao"
  }
}
```



如果有一个变量只能在一个线程中修改，不想被其他线程访问和修改就可以使用`ThreadLocal`来完成。



```kotlin
val threadLocal = object :ThreadLocal<String>() {
  override fun initialValue(): String {
    return "ooyao"
  }
}

Thread {
  threadLocal.set("天才威")
  println("${Thread.currentThread().name} value is ${threadLocal.get()}") // Thread-0 value is 天才威
}.start()

Thread {
  println("${Thread.currentThread().name} value is ${threadLocal.get()}") // Thread-1 value is ooyao
  threadLocal.set("光头强")
  println("${Thread.currentThread().name} value is ${threadLocal.get()}") // Thread-1 value is 光头强
}.start()
```



从打印结果可以看出`ThreadLocal`可以保证在当前线程`set`的值只会在当前线程起作用，在其他线程是不能获取到的。第二个线程中的第一个输出语句打印的是`ooyao`，因为我们在这个线程中没有调用`set`方法，所以`get`的值是默认值。如果没有设置默认值则会返回`null`。



# ThreadLocal 原理

`ThreadLocal`会在每个线程中保存一份副本，其`set`、`get`方法都是操作当前线程中的副本，所以不会互相影响。



## ThreadLocal#set(T value)

```java
public void set(T value) {
  // 获取当前线程
  Thread t = Thread.currentThread();
  // 获取当前线程的 ThreadLocalMap 对象
  ThreadLocalMap map = getMap(t);
  if (map != null)
    // 如果ThreadLocalMap不为空则将 ThreadLocal 和 value 保存其中
    map.set(this, value);
  else
    // 如果 ThreadLocalMap 为空，则创建并将ThreadLocal 和 value 保存其中
    createMap(t, value);
}
```



`ThreadLocalMap`是 `ThreadLocal`的静态内部类，其内部使用`key-value`的方式保存每个线程中的值。`key`就是 `ThreadLocal`对象，`value`是我们要保存的值。为了避免内存泄漏，这里的`key`使用了`WeakReference`进行保存。



```java
static class Entry extends WeakReference<ThreadLocal<?>> {
  /** The value associated with this ThreadLocal. */
  Object value;

  Entry(ThreadLocal<?> k, Object v) {
    super(k);
    value = v;
  }
}
```



每个线程中都会持有一个`ThreadLocal`对象。



```java
class Thread implements Runnable {
  ThreadLocal.ThreadLocalMap threadLocals = null;
}
```



在`ThreadLocal`中通过`getMap()`方法获取。



```java
ThreadLocalMap getMap(Thread t) {
  // 返回线程中的 threadLocalMap 对象
  return t.threadLocals;
}
```



如果线程中的`ThreadLocalMap`对象不为空，则将`ThreadLocal`对象和`value`封装成`Entry`保存到当前线程的`ThreadLocalMap`中。



如果`ThreadLocalMap`对象为空则会调用`createMap()`方法为当前线程创建`ThreadLocalMap`对象，并将`ThreadLocal`对象和`value`封装成`Entry`保存到当前线程的`ThreadLocalMap`中。



```java
void createMap(Thread t, T firstValue) {
  // 创建 ThreadLocalMap 对象，并将值保存其中
  t.threadLocals = new ThreadLocalMap(this, firstValue);
}
```



因为`ThreadLocalMap`对象是线程持有的，并且在`set`时都会先获取当前线程，所以`value`会在每个线程中保存一份，并且在每个线程中通过`ThreadLocal`对象即可获取对应的值。



## ThreadLocal#get()

明白了`set`方法的原理之后再看`get`方法就很简单了。



```java
public T get() {
  // 获取当前线程
  Thread t = Thread.currentThread();
  // 获取当前线程的 ThreadLocalMap 对象
  ThreadLocalMap map = getMap(t);
  if (map != null) {
    // 通过 ThreadLocal 对象获取对应的 Entry 对象（Entry 中保存着之前 set 的 value。）
    ThreadLocalMap.Entry e = map.getEntry(this);
    if (e != null) {
      @SuppressWarnings("unchecked")
      T result = (T)e.value;
      // 返回ThreadLocal 对应的 value
      return result;
    }
  }
  // 如果 ThreadLocalMap 中获取不到value 则返回默认值。
  return setInitialValue();
}
```



从代码注释即可看出`get`的原理了。



回顾一下 `ThreadLocal`之所以能保证变量可以在多线程环境下保持独立，是因为 `Thread`中有`threadLocals`变量的支持。









































