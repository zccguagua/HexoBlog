---
title: Kotlin扩展函数
date: 2022-07-07 23:47:15
tags:
    - Kotlin
---
![](https://www.runoob.com/wp-content/uploads/2017/05/kotlin_250x250.png "kotlin")

[kotlin扩展函数参考](http://kotlin.liying-cn.net/docs/reference_zh/collection-transformations.html)
[映射(Mapping)](#function_01)
[合并(Zipping)](#function_02)
[关联(Association)](#function_03)
[扁平化(Flattening)](#function_04)
[字符串表达(String representation)](#function_05)
[过滤(Filtering)集合](#function_06)
[加法(Plus) 和 减法(Minus) 操作符](#function_07)
[分组(Grouping)](#function_08)


Kotlin 标准库提供了一组扩展函数用于集合的 变换(Transformation). 这些函数会使用指定的变换规则从原集合创建新的集合. 本节中, 我们概要介绍集合的这些变换函数.
### <span id="function_01">映射(Mapping)</span>
映射(Mapping) 变换, 会将集合的每个元素传递给一个函数, 然后用函数结果创建一个新的集合. 最基本的映射函数是 <font color=#FFA500>map()</font>. 它将每个元素传递给指定的 lambda 函数, 然后用 lambda 函数返回的结果创建一个 list. 结果的顺序与原集合中的元素顺序相同. 如果变换时还需要元素下标参数, 请使用 <font color=#FFA500>mapIndexed()</font> 函数.
```
val numbers = setOf(1, 2, 3)
println(numbers.map { it * 3 })
println(numbers.mapIndexed { idx, value -> value * idx })

[3, 6, 9]
[0, 2, 6]
```
如果对某些元素变换的结果是 null, 你可以将这些 null 值从结果集合中过滤掉, 方法是使用 <font color=#FFA500>mapNotNull()</font> 函数代替 map() 函数, 或者相应的使用 <font color=#FFA500>mapIndexedNotNull()</font> 函数代替 mapIndexed() 函数.
```
val numbers = setOf(1, 2, 3)
println(numbers.mapNotNull { if ( it == 2) null else it * 3 })
println(numbers.mapIndexedNotNull { idx, value -> if (idx == 0) null else value * idx })

[3, 9]
[2, 6]
```
其他 mapKeys()要对键(key)进行指定的变换，mapValues()要变换值(value)

### <span id="function_02">合并(Zipping)</span>
合并(Zipping) 变换, 将两个集合中相同位置的元素合并为 <font color=#FFA500>pair</font>. Kotlin 标准库中, 这个操作使用 zip() 扩展函数实现. 
可以对一个集合或数组调用 zip() 函数, 参数是另一个集合(或数组), 返回值是 Pair 对象构成的 List. 接受者对象集合中的元素, 将成为这些 pair 中的第一个元素. 
如果两个集合的大小不同, zip() 返回的结果只包含较小的那个集合大小; 较大的集合中的末尾元素不会出现在结果中. 
zip() 也可以使用中缀形式调用, 也就是 a zip b.
```
val colors = listOf("red", "brown", "grey")
val animals = listOf("fox", "bear", "wolf")
println(colors zip animals)

val twoAnimals = listOf("fox", "bear")
println(colors.zip(twoAnimals))

[(red, fox), (brown, bear), (grey, wolf)]
[(red, fox), (brown, bear)]
```
如果已有 Pair 构成的 List, 你可以做相反的变换 – 分离(unzipping) – 它会通过这些 pair 创建两个 list:

第一个 list 包含原 List 的每个 Pair 中的第一个元素.
第二个 list 包含 Pair 中的第二个元素.
要分离 pair 构成的 list, 请使用 <font color=#FFA500>unzip()</font> 函数.
```
val numberPairs = listOf("one" to 1, "two" to 2, "three" to 3, "four" to 4)
println(numberPairs.unzip())

([one, two, three, four], [1, 2, 3, 4])
```
### <span id="function_03">关联(Association)</span>
关联(Association) 变换, 可以使用指定集合的元素以及与各元素对应的值创建 map. 在不同的关联类型中, 原集合的元素可以是结果 map 中的键(key), 也可以是值(value).

基本的关联函数 <font color=#FFA500>associateWith()</font> 会创建一个 <font color=#FFA500>Map</font>, 原集合的元素成为它的键(key), 值(value)由一个变换函数通过这些元素计算得到. 如果两个元素相等, 那么<font color=#006e5f>只有后一个会保留在 map 中</font>.
```
val numbers = listOf("one", "two", "three", "four")
println(numbers.associateWith { it.length })

{one=3, two=3, three=5, four=4}
```
如果要把集合元素变换为 map 中的值(value), 请使用 associateBy() 函数. 它的参数是一个函数, 这个函数根据元素值返回一个键(key). 如果两个元素的 Key 相等, 那么<font color=#006e5f>只有后一个会保留在 map 中</font>.

调用 associateBy() 时, 也可以指定一个值变换函数.
```
val numbers = listOf("one", "two", "three", "four")

println(numbers.associateBy { it.first().uppercaseChar() })
println(numbers.associateBy(keySelector = { it.first().uppercaseChar() }, valueTransform = { it.length }))

{O=one, T=three, F=four}
{O=3, T=5, F=4}
```
构建 map 的另一种方法是 <font color=#FFA500>associate()</font> 函数, 它根据集合元素通过某种方法同时产生键(key)和值(value). 这个函数的参数是一个 lambda 函数, lambda 函数返回一个 Pair: 其中包含对应的 map 条目(entry) 的键(key)和值(value).

<font color=#c5283d>注意</font>, associate() 产生的是临时存在(short-living)的 Pair 对象, 可能会影响性能. 因此, 只有性能问题不是很关键, 或者它比其他方式更合理的情况下, 才应该使用 associate() 函数.

后一种情况的例子是, 如果需要从集合元素同时产生键(key)和对应的值(value), 那么就应该使用 associate() 函数了.

```
val names = listOf("Alice Adams", "Brian Brown", "Clara Campbell")
println(names.associate { name -> parseFullName(name).let { it.lastName to it.firstName } })  

{Adams=Alice, Brown=Brian, Campbell=Clara}
```
### <span id="function_04">扁平化(Flattening)</span>
标准库提供了对嵌套集合(nested collection)的元素进行扁平化访问(flat access)的函数, 对于嵌套集合(nested collection)的操作非常便利.

第一个函数是 <font color=#FFA500>flatten()</font>. 可以对一个集合的集合调用这个函数, 比如, Set 构成的 List. 这个函数返回单个 List, 其中包含嵌套集合中的所有元素.

```
val numberSets = listOf(setOf(1, 2, 3), setOf(4, 5, 6), setOf(1, 2))
println(numberSets.flatten())

[1, 2, 3, 4, 5, 6, 1, 2]
```
另一个函数 – <font color=#FFA500>flatMap()</font> 提供了一种灵活的方式来处理嵌套集合. 它的参数是一个函数, 负责将集合中的一个元素变换为另一个集合. flatMap() 的结果返回单个 list, 其中包括对原集合各个元素调用变换函数后返回的集合中的所有元素. 因此, flatMap() 的行为等于调用 map() (映射(Mapping)的结果是一个集合) 之后再调用 flatten().

```
val containers = listOf(
    StringContainer(listOf("one", "two", "three")),
    StringContainer(listOf("four", "five", "six")),
    StringContainer(listOf("seven", "eight"))
)
println(containers.flatMap { it.values })

[one, two, three, four, five, six, seven, eight]
```
### <span id="function_05">字符串表达(String representation)</span>
如果你需要将集合内容表达为人类可读的格式, 请使用将集合转换为字符串的函数: <font color=#FFA500>joinToString()</font> 和 <font color=#FFA500>joinTo()</font>.

joinToString() 根据指定的参数, 从集合元素创建单个 String. joinTo() 执行同样的功能, 但把结果添加到指定的 Appendable 对象中.

如果使用默认参数调用这些函数, 返回的结果与对集合调用 toString() 函数类似: 由各个元素的字符串表达组成的 String, 元素之间以逗号加空格分隔.

```
val numbers = listOf("one", "two", "three", "four")

println(numbers)         
println(numbers.joinToString())

val listString = StringBuffer("The list of numbers: ")
numbers.joinTo(listString)
println(listString)

[one, two, three, four]
one, two, three, four
The list of numbers: one, two, three, four
```
如果要创建自定义的字符串表达, 可以指定函数参数 <font color=#f5a31f>separator</font>, <font color=#f5a31f>prefix</font>, 以及 <font color=#f5a31f>postfix</font>. 结果字符串以 prefix 开始, 以 postfix 结尾. separator 会出现在每个元素之后, 最后一个元素除外.

```
val numbers = listOf("one", "two", "three", "four")    
println(numbers.joinToString(separator = " | ", prefix = "start: ", postfix = ": end"))

start: one | two | three | four: end
```
对比较大的集合, 你可能需要指定 limit – 结果中包含的最大元素个数. 如果集合大小超过 limit 值, 所有超过的元素会被替换为 truncated 参数指定的值.

```
对比较大的集合, 你可能需要指定 limit – 结果中包含的最大元素个数. 如果集合大小超过 limit 值, 所有超过的元素会被替换为 truncated 参数指定的值.
```
最后, 如果要控制集合元素本身的字符串表达, 可以指定一个 transform 函数.
```
val numbers = listOf("one", "two", "three", "four")
println(numbers.joinToString { "Element: ${it.uppercase()}"})

Element: ONE, Element: TWO, Element: THREE, Element: FOUR
```
### <span id="function_06">过滤(Filtering)集合</span>

<font color=#FFA500>filter()</font>
<font color=#FFA500> filterNot()</font>
```
val numbers = listOf("one", "two", "three", "four")  
val longerThan3 = numbers.filter { it.length > 3 }
println(longerThan3)

val numbersMap = mapOf("key1" to 1, "key2" to 2, "key3" to 3, "key11" to 11)
val filteredMap = numbersMap.filter { (key, value) -> key.endsWith("1") && value > 10}
println(filteredMap)

[three, four]
{key11=11}
```
```
val numbers = listOf("one", "two", "three", "four")

val filteredIdx = numbers.filterIndexed { index, s -> (index != 0) && (s.length < 5)  }
val filteredNot = numbers.filterNot { it.length <= 3 }

println(filteredIdx)
println(filteredNot)

[two, four]
[three, four]
```

### <span id="function_07">加法(Plus) 和 减法(Minus) 操作符</span>
在 Kotlin 中, 也为集合定义了 加法(Plus) (+) 和 减法(Minus) (-) 操作符. 这些操作符使用一个集合作为第一个操作数; 第二个操作数可以是单个元素, 也可以是另一个集合. 返回值是一个新的只读集合:

加法(Plus) 的返回值包含原来集合中的元素 和 第二个操作数的元素.
减法(Minus) 的返回值包含原来集合中的元素, 但要 除去 第二个操作数的元素. 如果第二个操作数是单个元素, 减法(Minus) 只删除原来的集合中 第一次 出现的这个元素; 如果第二个操作数是一个集合, 那么原来的集合中 所有 出现的这些元素都会被删除.

```
val numbers = listOf("one", "two", "three", "four")

val plusList = numbers + "five"
val minusList = numbers - listOf("three", "four")
println(plusList)
println(minusList)


[one, two, three, four, five]
[one, two]
```
### <span id="function_08">分组(Grouping)</span>
Kotlin 标准库提供了扩展函数, 用于对集合中的元素进行分组操作. 最基本的函数是 <font color=#FFA500>groupBy()</font>, 它接受一个 lambda 函数为参数, 返回结果是一个 Map. <font color=#006e5f>在这个 map 中, 每个键(key)是 lambda 函数的一个返回结果, 与键(key)对应的值(value) 是一个 List, 其中包含返回这个结果的所有元素</font>. 这个函数的用途, 举例来说, <font color=#006e5f>我们可以对一个 String 组成的 list, 按字符串的首字母进行分组</font>.

调用 groupBy() 函数时, 也可以使用另一个 lambda 函数作为第二个参数 – 这个函数负责对值进行变换. 象这样使用两个 lambda 函数调用 groupBy()时, 结果 map 中, 第一个参数(keySelector lambda 函数)负责生成键(key), 它对应的值(value) 则是由第二个参数(值转换 lambda 函数)产生的结果组成的 list, 而不是集合中原来元素组成的 list.

```
val numbers = listOf("one", "two", "three", "four", "five")

println(numbers.groupBy { it.first().uppercase() })
println(numbers.groupBy(keySelector = { it.first() }, valueTransform = { it.uppercase() }))

{O=[one], T=[two, three], F=[four, five]}
{o=[ONE], t=[TWO, THREE], f=[FOUR, FIVE]}
```

```
```