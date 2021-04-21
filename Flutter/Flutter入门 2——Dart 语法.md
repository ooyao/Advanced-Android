# 简介

`Dart`中的重要概念：

- 在`Dart`中所有变量引用的都是对象，所有的对象都是类的实例。如数字、函数、null 都是对象。所有的对象都继承自内置的 Object 类。`D`
- 尽管 Dart 是强类型语言，但是在声明变量时指定类型是可选的，因为 Dart 可以进行类型推断。在上述代码中，变量 `number` 的类型被推断为 `int` 类型。如果想显式地声明一个不确定的类型，可以[使用特殊类型 `dynamic`](https://dart.cn/guides/language/effective-dart/design#do-annotate-with-object-instead-of-dynamic-to-indicate-any-object-is-allowed)。但是指定类型可以提高编译、运行速度。
- 

每个 Dart 程序都必须有一个 `main()` 顶级函数作为程序的入口，`main()` 函数返回值为 `void` 并且有一个 `List<String>` 类型的可选参数。

```dart
void main() {
  var a = 120;
  print(a);
}
```



# 注释

`Dart`中注释分为三种：

1. 单行注释：`// 这是单行注释` 
2. 多行注释：`/*这是多行注释*/`
3. 文档注释：文档注释分为两种。
   1. `/// 文档注释`。
   2. `/**文档注释*/`

在文档注释中可以使用`[]`来引用变量或类等信息，可方便的点击`[]`内容进行代码跳转。

```dart
// 这个是单行注释
/*这个是多行注释*/
/// 这个是文档注释
/**这个也是文档注释*/

/// Feeds your llama [Food].
/// The typical llama eats one bale of hay per week.
void feed(Food food) {
  // ...
}
```



# 变量与基本数据类型

## 1. 变量

声明变量使用`var`关键字，例如：`var age = 18;`。因为在`Dart`中所有的东西都是对象，所以`age`也是一个对象类型，如果不对其进行初始化则默认为`null`。所有的 `Dart`变量都可以直接用（`.`）点操作符进行方法调用，例如：`age.toDouble();`。

## 2. 常量和固定值

如果一个变量的值不会发生改变可以使用`final`或`const`进行声明。其区别如下：

- `final`表示其值在确定之后不会发生改变，例如：

```dart
final name = ‘洞洞幺';
name = 'ooyao'; 	// 错误
```

因为`final`修饰的变量不能重复赋值，所以上面的`name = 'ooyao';`会引发错误。

- `const`表示是一个编译时的常量，即在编译时其值已经确定并不能被修改。例如：

```dart
const pi = 3.1415926;
const area = pi * 5 * 5;
```



- `const[]`可以用来创建常量值，例如：

```dart
final stars = const[1,2,3,4];
const buttons = const[];
```

## 3. 常用数据类型

在`Dart`中常用的数据类型包括`Number`、`String`、`Boolean`、`List`、`Map`。















































































