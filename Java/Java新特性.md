### 函数式编程

作为面向对象的编程语言，Java 中使用接口来表示函数。直到 Java 8，Java 才提供了内置标准 API 来表示函数，也就是 java.util.function 包。Function<T, R> 表示接受一个参数的函数，输入类型为 T，输出类型为 R。Function 接口只包含一个抽象方法 R apply(T t)，也就是在类型为 T 的输入 t 上应用该函数，得到类型为 R 的输出。除了接受一个参数的 Function 之外，还有接受两个参数的接口 BiFunction<T, U, R>，T 和 U 分别是两个参数的类型，R 是输出类型。BiFunction 接口的抽象方法为 R apply(T t, U u)。超过 2 个参数的函数在 Java 标准库中并没有定义。如果函数需要 3 个或更多的参数，可以使用第三方库，如 Vavr 中的 Function0 到 Function8。

- Function<T,R>: 接收一个输入,一个输出,抽象方法为 apply(T t)
- BiFunction<T,U,R> 接收两个输入,一个输出,抽象方法为apply(T t,U u);
- Consumer< T>：接受一个输入，没有输出。抽象方法为 void accept(T t).
- Supplier< T>：没有输入，一个输出。抽象方法为 T get().
- Predicate< T>：接受一个输入，输出为 boolean 类型。抽象方法为 boolean test(T t).
- UnaryOperator< T>：接受一个输入，输出的类型与输入相同，相当于 Function<T, T>.
- BinaryOperator< T>：接受两个类型相同的输入，输出的类型与输入相同，相当于 BiFunction<T,T,T>.
- BiPredicate<T,U>：接受两个输入，输出为 boolean 类型。抽象方法为 boolean test(T t, U u).

| **特征**                                  | **函数式方法名**                                             | **示例**                                                     |
| ----------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 无参数； 无返回值                         | **Runnable** (java.lang) `run()`                             | **Runnable**                                                 |
| 无参数； 返回类型任意                     | **Supplier** `get()` `getAs类型()`                           | **Supplier`<T>` BooleanSupplier IntSupplier LongSupplier DoubleSupplier** |
| 无参数； 返回类型任意                     | **Callable** (java.util.concurrent) `call()`                 | **Callable`<V>`**                                            |
| 一个参数； 无返回值                       | **Consumer**`accept()`                                       | **`Consumer<T>` IntConsumer LongConsumer DoubleConsumer**    |
| 两个参数 **Consumer**                     | **BiConsumer** `accept()`                                    | **`BiConsumer<T,U>`**                                        |
| 2 参数 **Consumer**； 1 引用； 1 基本类型 | **Obj类型Consumer** `accept()`                               | **`ObjIntConsumer<T>` `ObjLongConsumer<T>` `ObjDoubleConsumer<T>`** |
| 1 参数； 返回类型不同                     | **Function** `apply()` **To类型** 和 **类型To类型** `applyAs类型()` | **Function`<T,R>` IntFunction`<R>` `LongFunction<R>` DoubleFunction`<R>` ToIntFunction`<T>` `ToLongFunction<T>` `ToDoubleFunction<T>` IntToLongFunction IntToDoubleFunction LongToIntFunction LongToDoubleFunction DoubleToIntFunction DoubleToLongFunction** |
| 1 参数； 返回类型相同                     | **UnaryOperator** `apply()`                                  | **`UnaryOperator<T>` IntUnaryOperator LongUnaryOperator DoubleUnaryOperator** |
| 2 参数类型相同； 返回类型相同             | **BinaryOperator** `apply()`                                 | **`BinaryOperator<T>` IntBinaryOperator LongBinaryOperator DoubleBinaryOperator** |
| 2 参数类型相同; 返回整型                  | Comparator (java.util) `compare()`                           | **`Comparator<T>`**                                          |
| 2 参数； 返回布尔型                       | **Predicate** `test()`                                       | **`Predicate<T>` `BiPredicate<T,U>` IntPredicate LongPredicate DoublePredicate** |
| 参数基本类型； 返回基本类型               | **类型To类型Function** `applyAs类型()`                       | **IntToLongFunction IntToDoubleFunction LongToIntFunction LongToDoubleFunction DoubleToIntFunction DoubleToLongFunction** |
| 2 参数类型不同                            | **Bi操作** (不同方法名)                                      | **`BiFunction<T,U,R>` `BiConsumer<T,U>` `BiPredicate<T,U>` `ToIntBiFunction<T,U>` `ToLongBiFunction<T,U>` `ToDoubleBiFunction<T>`** |



#### 函数组合

|                     组合方法                     |                           支持接口                           |
| :----------------------------------------------: | :----------------------------------------------------------: |
|     `andThen(argument)` 根据参数执行原始操作     | **Function BiFunction Consumer BiConsumer IntConsumer LongConsumer DoubleConsumer UnaryOperator IntUnaryOperator LongUnaryOperator DoubleUnaryOperator BinaryOperator** |
|     `compose(argument)` 根据参数执行原始操作     | **Function UnaryOperator IntUnaryOperator LongUnaryOperator DoubleUnaryOperator** |
| `and(argument)` 短路**逻辑与**原始谓词和参数谓词 | **Predicate BiPredicate IntPredicate LongPredicate DoublePredicate** |
| `or(argument)` 短路**逻辑或**原始谓词和参数谓词  | **Predicate BiPredicate IntPredicate LongPredicate DoublePredicate** |
|        `negate()` 该谓词的**逻辑否**谓词         | **Predicate BiPredicate IntPredicate LongPredicate DoublePredicate** |



#### 柯里化

将一个多参数函数,改为一系列单参数函数



### 流式编程

流操作类型分为三种,

1. 创建流
   - String.of(T..t)
2. 修改流(中间操作)
   - **skip(int)** 根据参数丢弃指定数量的流元素
   - **peek()**  无修改的查看流元素
   - **distinct()** 消除重复元素
   - **filter(Predicate)**  过滤操作会保留与传递进去的过滤器函数运算结果为true的元素
   - **map(Function)**  将函数操作应用在输入流的元素中,并将返回值传递到输出流中
   - **mapToInt(Function)**  操作同上,返回IntStream
   - **mapToLong(Function)** 操作同上  返回LongStream
   - **mapToDouble(Function)**  操作同上 返回DoubleStream
   - **flatMap(Function)** 当function产生流时使用,做了两件事,将产生流的函数作用在每个元素上,然后将每个流都扁平化为元素
   - **flatMapToInt(Function)** 产生IntStream时使用
   - **flatMapToLong** 产生LongStream时使用
   - **flatMapToDouble** 产生DoubleStream时使用
3. 消费流(终端操作)
   - 数组
     - **toArray()** 将流转为适当类型的数组
     - **toArray(generator)** 在特殊情况下,转为自定义数组
     
   - 循环
     - **foreach(Consumer)** 循环操作
     - **forEachOrder(consumer)** 保证foreach按照原始流顺序操作
     
   - 集合
     - **collect(Collector)** 使用collector将元素收集到集合中
     - **collect(Suppiler,BiConsumer,BiConsumer)** 同上,<font color="red" style="font-weight:bold;">第一个参数创建一个新的结果集合,第二个参数BiConsumer将下一个元素包含到结果中,第三个参数BiConsumer用于将两个值组合起来</font>
     
   - 组合
     - **reduce(BinaryOperator)** 使用BinaryOperator来组合所有流中的元素.因为流可能为空,返回值是Operational
     - **reduce(identity,BinaryOperator)** 功能同上,但是使用identity作为其组合的初始值.<font color="red" style="font-weight:bold;">因此如果流为空,identity就是返回的结果</font>
     - **reduce(identity, BiFunction, BinaryOperator)** 更复杂的使用形式
     
   - 匹配
     - **allMatch(Predicate)**  流中每个元素根据predicate都返回true时,结果返回true.第一个false时停止运算
     - **anyMatch(Predicate)**  流中任意一个元素根据predicate返回true时,结果返回true.第一个false时停止运算
     - **noneMatch(Predicate)** 流中每个元素根据predicate函数返回false时,结果返回true.第一个true停止运算
     
   - 查找
     - **findFirst()** 返回第一个元素,流为空返回Optional.empty()
     - **findAny()** 返回含有任意流元素的Optional 为空返回empty()
     
   - 信息
     - count() 流中元素个数
     - max(Comparator)   根据所传Comparator决定的最大元素
     - min(Comparator)  根据所传Comparator决定的最小元素
     
   - 数字流信息

     - **average()** 元素平均值
     - **max()和min()** 无需Comparator
     - **sum()** 元素和
     - **summaryStatistics()** 流的数据统计数据

int流实现循环

```java
class test{
    public static void main(String[]args){
        IntStream.range(0,10).foreach(d->System.out.print(d));
    }
}
```

##### Optional类
即使流元素为空也不会报异常,返回Optional.empty()
- findFirest()  获取第一个值
- findAny() 
- max()
- **ifPresent(Consumer)**  当值存在时调用consumer,否则什么也不做
- **orElse(otherObject)**  当值存在时直接返回,否则生成otherObject
- **orElseGet(Suppiler)** 当值存在直接返回,否则调用suppiler函数生成一个可替代对象
- **orElseThrow(Suppiler)** 当值存在直接返回,否则使用suppiler函数抛出异常

###### 创建
- **empty()** 创建空optional
- **of(value)** 非空值包装到optional中
- **ofNullable(value)** 针对可能为空的值生成optional对象,为空生成空optional

###### 对象操作

- **filter(predicate)** 将predicate应用于optional中的内容并返回结果.<font color="red" style="font-weight:bold;">流的filter操作会直接移除元素,optional的filter操作不会删除,会转为空值</font>
- **map(function)** 如果optional不为空,应用function与optional的内容.否则返回optional.empty()
- **flatMap(function)** 同map,但是提供的映射函数将结果包装在optional对象中,因此flatmap不会在最后进行任何包装

