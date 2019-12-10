## Primitive Types

* Integral Types:
  * byte/8, default: 0
  * char/16 default:  '\u0000'
  * short/16, default: 0
  * int/32, default: 0
  * long/64 default: 0L
* Floating-type Types:
  * float/32 default: 0.0f
  * double/64 default: 0.0d
* Other:
  * boolean/(boolean: The boolean data type... This data type represents one bit of information, but its "size" isn't something that's precisely defined) default: false

## Type Cast

### boolean

基本数据类型中，布尔类型boolean占有一个字节，由于其本身所代码的特殊含义，**boolean类型与其他基本类型不能进行类型的转换（既不能进行自动类型的提升，也不能强制类型转换）， 否则，将编译出错**。

### Widening

* byte -> short, int, long, float or double
* short -> int, long, float or double
* char -> int, long, float or double
* int -> long, float double
* long -> float or double
* float -> double

A widening primitive conversion from `float` to `double` that is not `strictfp` may lose information about the overall magnitude of the converted value.

A widening primitive conversion from `int` to `float`, or from `long` to `float`, or from `long` to `double`, may result in *loss of precision* - that is, the result may lose some of the least significant bits of the value. In this case, the resulting floating-point value will be a correctly rounded version of the integer value, using IEEE 754 round-to-nearest mode

A widening conversion of a signed integer value to an integral type T simply sign-extends the two's-complement representation of the integer value to fill the wider format.

A widening conversion of a `char` to an integral type T zero-extends the representation of the `char` value to fill the wider format.

Despite the fact that loss of precision may occur, a widening primitive conversion never results in a run-time exception.

### Narrowing

* Need explicit cast.
* double to float uses standard IEEE 754 rounding.

- integer values have their most significant bits stripped to the available width of the target type. This may result in a sign bit appearing, e.g. `(byte)0xfff == (byte)-1`;
- If the source type is floating point and the target type is `long`, the value is converted by rounding towards zero.
- If the source type is floating point and the target type is integral but not `long`, the value is first converted to `int` by rounding towards zero. Then the resulting `int` is converted to the target type using integer conversion.
- [Casting rules for primitive types in java](https://stackoverflow.com/questions/30776256/casting-rules-for-primitive-types-in-java)

### Promotion

所谓类型提升就是指在多种不同数据类型的表达式中，类型会自动向范围表示大的值的数据类型提升。

[Why don't Java's +=, -=, *=, /= compound assignment operators require casting?](https://stackoverflow.com/questions/8710619/why-dont-javas-compound-assignment-operators-require-casting)

## Autoboxing and Unboxing

```java
Integer x = 2;     // 装箱 调用了 Integer.valueOf(2)
int y = x;         // 拆箱 调用了 X.intValue()
```

[Autoboxing and Unboxing](https://docs.oracle.com/javase/tutorial/java/data/autoboxing.html)

## 缓存池

new Integer(123) 与 Integer.valueOf(123) 的区别在于：

- new Integer(123) 每次都会新建一个对象；
- Integer.valueOf(123) 会使用缓存池中的对象，多次调用会取得同一个对象的引用。

```java
Integer x = new Integer(123);
Integer y = new Integer(123);
System.out.println(x == y);    // false
Integer z = Integer.valueOf(123);
Integer k = Integer.valueOf(123);
System.out.println(z == k);   // true
```

valueOf() 方法的实现比较简单，就是先判断值是否在缓存池中，如果在的话就直接返回缓存池的内容。

```java
public static Integer valueOf(int i) {
    if (i >= IntegerCache.low && i <= IntegerCache.high)
        return IntegerCache.cache[i + (-IntegerCache.low)];
    return new Integer(i);
}
```

在 Java 8 中，Integer 缓存池的大小默认为 -128~127。

编译器会在自动装箱过程调用 valueOf() 方法，因此多个值相同且值在缓存池范围内的 Integer 实例使用自动装箱来创建，那么就会引用相同的对象。

```java
Integer m = 123;
Integer n = 123;
System.out.println(m == n); // true
```

