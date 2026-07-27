---
title: "Dart语言教程"
---

### 代码规范

几乎和`C/C++`一模一样.

- 扩展名： dart语言文件后缀以`.dart`结尾
- 入口：dart文件的入口方法为main方法
- 分号：dart文件中绝大部分语句都需要加分号结尾，像 `{ }` 后通常不加分号

### Dart的变量

该节与`C/C++`高度相似.

#### 字符串类型-String

与`C/C++`中的`std::string`基本类似.

基础用法:

- 关键字：String
- 语法：`String 属性名 = ‘文本内容’;`
- 特点：引号支持双引号或者单引号，支持拼接及模板字符串

进阶用法:

- 语法：`String 属性名 = ‘文本内容$变量名’;` 或 `String 变量名 = ‘文本内容${变量名}’;`
- 注意：当存在模板中的内容是一个表达式的时候需要使用`${}`, 不管是表达式还是变量,都更推荐使用`${}`,尽量不要用`$变量名`了.

```dart
void main()
{
  String name = 'Alice';
  print(name); // Output: Alice

  name = 'Bob';
  print(name); // Output: Bob

  String greeting = 'Hello, ${name}?$name!';
  print(greeting); // Output: Hello, Bob?Bob!

  String when = 'it is ${DateTime.now()},now';
  print(when); // Output: it is 2026-02-16 14:46:17.818262,now
}
```

#### 数字类型-int/double/num

与`C/C++`中的`int`,`double`基本类似.

基础语法

- 场景：当我们需要描述一个数字类型的时候，需要使用int/num/double
- 区别：int-整型数字，double-小数 , num-可整型可小数
- 语法：`int/num/double 属性名 = 数值;`

```dart
void main()
{
  int age = 23;
  print(age); // Output: 23

  age = 24;
  print(age); // Output: 24

  double height = 1.78;
  print(height); // Output: 1.78

  height = 1.80;
  print(height); // Output: 1.8

  num weight = 90.5;
  print(weight); // Output: 90.5

  weight = 76;
  print(weight); // Output: 76
}
```

**三者本质区别:**

```bash
num
 ├── int
 └── double
```

- 👉 num 是父类
- 👉 int 和 double 是子类

**直接赋值规则总表:**
| 赋值方向            | 是否可以直接赋值 | 说明         |
| --------------- | -------- | ---------- |
| int → num       | ✅ 可以     | 子类给父类      |
| double → num    | ✅ 可以     | 子类给父类      |
| num → int       | ❌ 不行     | 可能是 double |
| num → double    | ❌ 不行     | 可能是 int    |
| int → double    | ❌ 不行     | Dart 不自动转  |
| double → int    | ❌ 不行     | 会丢失小数      |
| int → int       | ✅        |            |
| double → double | ✅        |            |
| num → num       | ✅        |            |

虽然无法直接赋值,但是可以转化后再赋值.可以利用`toInt()`与`toDouble()`等方法来实现,如:

```dart
void main()
{
  int a = 10;
  print(a); // Output: 10

  double b = 3.14;
  print(b); // Output: 3.14
  
  // double赋值给double
  double c = b;
  print(c); // Output: 3.14

  // int赋值给double
  b = a.toDouble();
  print(b); // Output: 10.0

  num d = 5.6;
  print(d); // Output: 5.6

  // num赋值给int
  a = d.toInt();
  print(a); // Output: 5

}
```

#### 布尔类型-bool

与`C/C++`中的`bool`基本类似.

- 场景：当我们需要一个属性来表示当前为真(true)或假(false)的时候，需要使用bool关键字声明
- 需求：声明当前自己是否已经完成作业
- 语法：`bool 属性名 = true/false;`

```dart
void main()
{
  bool isFinishWork = false;
  print('我的工作完成状态为：$isFinishWork'); //Output: 我的工作完成状态为：false
  
  isFinishWork = true;
  print('我的工作完成状态为：$isFinishWork'); //Output: 我的工作完成状态为：true
}
```

#### 列表类型-List

##### List

与`C/C++`中的`STL`向量`std::vector`基本类似.(注意,和`std::list`并不像)
而`std::vector`我们经常把他用作`不定长数组`,所以你不理解`std::vector`的时候把他当作一个高级的`数组`也可以.

本节肯定讲不完`List`的所有用法,但是大多数常用的用法都会讲到,你忘记了可以随时去查文档就行,不用每种用法都硬背下来,用的多的用法自然你就记住了,现在你只需要学完一遍,并知道有那么个东西即可.

基础用法:

- 场景：当一个变量需要存储多个值的时候，可以使用列表类型List
- 需求：一个班级的学生用List存储，支持对学生的查找、新增、删除、循环
- 语法：List 属性名 = [‘学生1’, ‘学生2’];

常用用法1:

- 在尾部添加-`add(内容)`
- 在尾部添加一个列表-`addAll(列表)`
- 删除满足内容的第一个-`remove(内容)`
- 删除最后一个-`removeLast()`
- 删除索引范围内数据-`removeRange(start,end)` 注意：end不包含在删除范围内

常用用法2:

下面会列举几个方法与属性,学过面向对象你肯定知道是啥,如果没学过,我就先简单说一下:

```bash
类-class的声明与定义
对象-class类型的实例,(不好理解也可以理解为class类型的变量)
方法-class里的函数
属性-class里的变量
```

- 循环(方法)-`forEach((item) {});`
- 是否都满足条件(方法)-`every((item) { return 布尔值 })；`
- 筛选出满足条件的数据(方法)-`where((item) { return 布尔值 })；`
- 列表的长度(属性)-`length`
- 最后一个元素(属性)-`last`
- 第一个元素(属性)-`first`
- 是否为空(属性)-`isEmpty`

```dart
void main(List<String> args) 
{
  List students = ["张三", "李四", "王五"];
  print(students); // Output: [张三, 李四, 王五]

  students.add("新同学"); // 在尾部进行添加
  print(students); // Output: [张三, 李四, 王五, 新同学]s

  students.addAll(["新来的同学1", "新来的同学2"]); // 在尾部添加一个列表
  print(students); // Output: [张三, 李四, 王五, 新同学, 新来的同学1, 新来的同学2]

  students.add("新同学"); // 在尾部进行添加
  print(students); // Output: [张三, 李四, 王五, 新同学, 新来的同学1, 新来的同学2, 新同学]

  students.remove("新同学"); // 删除满足内容的第一个
  print(students); // Output: [张三, 李四, 王五, 新来的同学1, 新来的同学2, 新同学]

  // 删除最后一个同学
  students.removeLast(); // 删除最后一个
  print(students); // Output: [张三, 李四, 王五, 新来的同学1, 新来的同学2]

  // 删除前两个同学
  // start开始的索引 end结束的索引-不包含在删除范围内
  students.removeRange(0, 2);
  print(students); // Output: [王五, 新来的同学1, 新来的同学2]

  // forEach针对每个列表每个数据进行操作
  students.forEach((item) 
  {
    // 书写逻辑
    print(item); // Output: 王五
                 //         新来的同学1
                 //         新来的同学2
  });

  // 是不是所有的同学都以新为开头
  bool isAllStartWithNew = students.every((item) 
  {
    return item.toString().startsWith("新"); // 返回一个条件
  });
  print(isAllStartWithNew); // Output: false (因为王五不以新开头,需要满足所有的同学都以新开头才会返回true)

  // 筛选出所有的以新开头的同学呢
  List newStudents = students.where((item) 
  {
    return item.toString().startsWith("新");
  }).toList();
  print(newStudents); // Output: [新来的同学1, 新来的同学2]


  // List常用的一些属性 方法() .属性
  // 列表的长度
  print(students.length); // Output: 3

  // 列表的第一个
  print(students.first); // Output: 王五

  // 列表的最后一个
  print(students.last); // Output: 新来的同学2

  // 列表是否是空的
  print(students.isEmpty); // Output: false (因为列表中有数据)
}
```

##### main函数入口参数

除了上面的代码外可以看到`void main(List<String> args)`里有参数了,这个其实类似`C/C++`里的`int main(int argc, char* argv[])`

| 对比点     | C/C++           | Dart           |
| ------- | -------------- | -------------- |
| 参数个数    | `argc`         | `args.length`  |
| 参数数组    | `argv[]`       | `List<String>` |
| 是否包含程序名 | ✅ argv[0] 是程序名 | ❌ 不包含          |
| 类型安全    | ❌ char*        | ✅ String       |
| 内存管理    | 手动             | 自动             |

例如
```bash
dart run app.dart hello world
```
此时
```bash
args[0] = "hello"
args[1] = "world"
```

#### 字典类型-Map

类似C++的`std::map`.

基础用法:

- 一个key对应一个value
- 语法1：Map 属性名 = { key: value };
- 语法2： `字典[key] 可以取值和赋值`


常用用法:
- 循环-`forEach`
- 在添加一个字典-`addAll`
- 是否包含某个key-`containsKey`
- 删除某个key-`remove`
- 清空-`clear`

```dart
void main(List<String> args) 
{
  Map transMap = {"lunch": '午饭', "morning": "早上", "hello": '你好'};
  print(transMap);
  // 通过英文找到对应中文的描述
  print(transMap["morning"]);
  transMap["hello"] = "你非常好";
  print(transMap["hello"]);
  // 字典里面有很多对应关系
  transMap.forEach((key, value) {
    print("$key,$value");
  });
  // addAll 给当前字典添加一个字典
  transMap.addAll({"fine": "非常好"});
  print(transMap);
  // containesKey判断字典中是否包含某个key
  print(transMap.containsKey("fine"));

  transMap.remove("fine");
  print(transMap);

  // 清空字典
  transMap.clear();
  print(transMap);
}

```

#### 动态类型-dynamic

类似C++的`std::any`

- 定义：Dart语言中，dynamic用来声明动态类型
- 特点：允许变量运行时自由改变类型, 同时绕过编译时的静态检查
- 语法1：`dynamic 属性名 = 值;`

```dart
void main(List<String> args) 
{
  // dynamic 可以动态的改变类型
  dynamic name = "张三";
  print(name);
  name = 123;
  print(name);
}
```

只有你自己100%认为你写的是对的,再用dynamic,否则别用dynamic,不然可能会出现很多错误.

#### 自动推导类型-var

类似C/C++的`auto`.

这个关键字可以自动推导变量的类型.

- 关键字：var
- 语法：`var 变量名 = 值/表达式;`
- 注意：使用var声明的变量，其类型在第一次赋值之后确定，不能再赋值其他类型的值


```dart
void main(List<String> args) 
{
  var name = '张三';
  print(name); // Output: 张三

  name = '李四';
  print(name); // Output: 李四

  var age = 30;
  print(age); // Output: 30

  var isStudent = true;
  print(isStudent); // Output: true

  var height = 1.75;
  print(height); // Output: 1.75
}
```

**Dart中的动态类型-dynamic和var的区别:**
- dynamic： 运行时可自由改变类型，无编译检查，方法和属性直接调用
- var: 根据初始值进行推断类型，确定类型后类型确定，有编译检查，仅限推断的属性和方法

#### 常量声明-const/final

##### const

类似C++的`constexpr`.

- 关键字：const
- 语法：const 属性名 = 值/表达式;
- 特点：const是代码编译前被确定，不允许表达式中有变量存在，必须为常量或者固定值

```dart
void main(List<String> args) 
{
  const int a = 10;
  const double b = 3.14;
  const String c = "Hello, Dart!";

  print("Value of a: ${a}"); // Output: Value of a: 10
  print("Value of b: ${b}"); // Output: Value of b: 3.14
  print("Value of c: ${c}"); // Output: Value of c: Hello, Dart!
}
```

##### final

类似C++的`const`.

- 关键字：final
- 语法：final 属性名 = 值/表达式;
- 特点：final变量在运行时被初始化，其值设置后不可更改

```dart
void main(List<String> args) 
{
  final time = DateTime.now();
  print('Current time: $time'); // Current time: 2026-02-16 20:53:53.045010
}
```

- 变量：当需要存储一个变化的数据需要使用var来声明变量
- 编译时常量：当需要存储一个不变的数据，且在编译时就确定，需要使用const声明常量
- 运行时常量：当需要存储一个不变的数据，但是在运行时才确定，需要使用final声明常量

| 概念    | Dart    | C++         | 是否编译期常量 | 是否可运行期确定 | 是否对象不可变   |
| ----- | ------- | ----------- | ------- | -------- | --------- |
| 只赋值一次 | `final` | `const`     | ❌       | ✅        | ❌         |
| 编译期常量 | `const` | `constexpr` | ✅       | ❌        | ✅（Dart更强） |
| 只读变量  | 无完全等价   | `const`     | ❌       | ✅        | ❌         |

### 空安全机制

- 定义：在Dart语言中，通过编译静态检查将运行时空指针提前暴露
- 特点：将空指针异常从运行时提前至编译时，减少线上崩溃


**常用空安全操作符**

| 操作符  | 符号   | 作用                     | 示例                                          |
| :--- | :--- | :--------------------- | :------------------------------------------ |
| 可空类型 | `?`  | 声明可空变量                 | `String?` → 允许 String 或 null                |
| 安全访问 | `?.` | 对象为 null 时跳过操作，返回 null | `user?.name` → 若 user 为 null 则返回 null       |
| 非空断言 | `!.`  | 开发者保证变量非空（否则运行时崩溃）     | `name!.length` → 断言 name 非空                 |
| 空合并  | `??` | 左侧为 null 时返回右侧默认值      | `name ?? "Guest"` → name 为 null 时返回 "Guest" |
| 空合并赋值 | `??=` | 变量为 null 时才赋值 | `name ??= "Guest"` |
