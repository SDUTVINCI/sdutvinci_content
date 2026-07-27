---
title: "结构体与共用体"
---

## 结构体

### 结构体基本概念

结构体属于用户==自定义的数据类型==，允许用户存储不同的数据类型

### 结构体定义和使用

**语法：**`struct 结构体名 { 结构体成员列表 }；`

通过结构体创建变量的方式有三种：

* struct 结构体名 变量名
* struct 结构体名 变量名 = { 成员1值 ， 成员2值...}
* 定义结构体时顺便创建变量

**示例：**

```cpp
//结构体定义
struct student
{
	//成员列表
	string name;  //姓名
	int age;      //年龄
	int score;    //分数
}stu3; //结构体变量创建方式3 

int main() {

	//结构体变量创建方式1
	struct student stu1; //struct 关键字可以省略

	stu1.name = "张三";
	stu1.age = 18;
	stu1.score = 100;
	
	cout << "姓名：" << stu1.name << " 年龄：" << stu1.age  << " 分数：" << stu1.score << endl;

	//结构体变量创建方式2
	struct student stu2 = { "李四",19,60 };

	cout << "姓名：" << stu2.name << " 年龄：" << stu2.age  << " 分数：" << stu2.score << endl;

	stu3.name = "王五";
	stu3.age = 18;
	stu3.score = 80;
	

	cout << "姓名：" << stu3.name << " 年龄：" << stu3.age  << " 分数：" << stu3.score << endl;


	return 0;
}
```

**运行/观察结果：** 运行后会按输出语句打印对应内容，变量值可结合初始化、赋值和函数调用顺序推导。

> 总结1：定义结构体时的关键字是struct，不可省略

> 总结2：创建结构体变量时，关键字struct可以省略

> 总结3：结构体变量利用操作符 ''.''  访问成员

### 结构体数组

**作用：**将自定义的结构体放入到数组中方便维护

**语法：**` struct  结构体名 数组名[元素个数] = {  {} , {} , ... {} }`

**示例：**

```cpp
//结构体定义
struct student
{
	//成员列表
	string name;  //姓名
	int age;      //年龄
	int score;    //分数
}

int main() {
	
	//结构体数组
	struct student arr[3]=
	{
		{"张三",18,80 },
		{"李四",19,60 },
		{"王五",20,70 }
	};

	for (int i = 0; i < 3; i++)
	{
		cout << "姓名：" << arr[i].name << " 年龄：" << arr[i].age << " 分数：" << arr[i].score << endl;
	}


	return 0;
}
```

**运行/观察结果：** 运行后会按输出语句打印对应内容，变量值可结合初始化、赋值和函数调用顺序推导。

### 结构体指针

**作用：**通过指针访问结构体中的成员

* 利用操作符 `-> `可以通过结构体指针访问结构体属性

**示例：**

```cpp
//结构体定义
struct student
{
	//成员列表
	string name;  //姓名
	int age;      //年龄
	int score;    //分数
};

int main() {
	
	struct student stu = { "张三",18,100, };
	
	struct student * p = &stu;
	
	p->score = 80; //指针通过 -> 操作符可以访问成员

	cout << "姓名：" << p->name << " 年龄：" << p->age << " 分数：" << p->score << endl;
	

	return 0;
}
```

**运行/观察结果：** 运行后会打印示例中的变量值或地址；地址值与运行环境有关，以同类对象的相对位置和指针变化为观察重点。

> 总结：结构体指针可以通过 -> 操作符 来访问结构体中的成员

### 结构体嵌套结构体

**作用：** 结构体中的成员可以是另一个结构体

**例如：**每个老师辅导一个学员，一个老师的结构体中，记录一个学生的结构体

**示例：**

```cpp
//学生结构体定义
struct student
{
	//成员列表
	string name;  //姓名
	int age;      //年龄
	int score;    //分数
};

//教师结构体定义
struct teacher
{
    //成员列表
	int id; //职工编号
	string name;  //教师姓名
	int age;   //教师年龄
	struct student stu; //子结构体 学生
};

int main() {

	struct teacher t1;
	t1.id = 10000;
	t1.name = "老王";
	t1.age = 40;

	t1.stu.name = "张三";
	t1.stu.age = 18;
	t1.stu.score = 100;

	cout << "教师 职工编号： " << t1.id << " 姓名： " << t1.name << " 年龄： " << t1.age << endl;
	
	cout << "辅导学员 姓名： " << t1.stu.name << " 年龄：" << t1.stu.age << " 考试分数： " << t1.stu.score << endl;


	return 0;
}
```

**运行/观察结果：** 运行后会按输出语句打印对应内容，变量值可结合初始化、赋值和函数调用顺序推导。

**总结：**在结构体中可以定义另一个结构体作为成员，用来解决实际问题

### 结构体做函数参数

**作用：**将结构体作为参数向函数中传递

传递方式有两种：

* 值传递
* 地址传递

**示例：**

```cpp
//学生结构体定义
struct student
{
	//成员列表
	string name;  //姓名
	int age;      //年龄
	int score;    //分数
};

//值传递
void printStudent(student stu )
{
	stu.age = 28;
	cout << "子函数中 姓名：" << stu.name << " 年龄： " << stu.age  << " 分数：" << stu.score << endl;
}

//地址传递
void printStudent2(student *stu)
{
	stu->age = 28;
	cout << "子函数中 姓名：" << stu->name << " 年龄： " << stu->age  << " 分数：" << stu->score << endl;
}

int main() {

	student stu = { "张三",18,100};
	//值传递
	printStudent(stu);
	cout << "主函数中 姓名：" << stu.name << " 年龄： " << stu.age << " 分数：" << stu.score << endl;

	cout << endl;

	//地址传递
	printStudent2(&stu);
	cout << "主函数中 姓名：" << stu.name << " 年龄： " << stu.age  << " 分数：" << stu.score << endl;


	return 0;
}
```

**运行/观察结果：** 运行后会打印示例中的变量值或地址；地址值与运行环境有关，以同类对象的相对位置和指针变化为观察重点。

> 总结：如果不想修改主函数中的数据，用值传递，反之用地址传递

### 结构体中 const使用场景

**作用：**用const来防止误操作

**示例：**

```cpp
//学生结构体定义
struct student
{
	//成员列表
	string name;  //姓名
	int age;      //年龄
	int score;    //分数
};

//const使用场景
void printStudent(const student *stu) //加const防止函数体中的误操作
{
	//stu->age = 100; //操作失败，因为加了const修饰
	cout << "姓名：" << stu->name << " 年龄：" << stu->age << " 分数：" << stu->score << endl;

}

int main() {

	student stu = { "张三",18,100 };

	printStudent(&stu);


	return 0;
}
```

**运行/观察结果：** 运行后会打印示例中的变量值或地址；地址值与运行环境有关，以同类对象的相对位置和指针变化为观察重点。

### 结构体案例

#### 案例1

**案例描述：**

学校正在做毕设项目，每名老师带领5个学生，总共有3名老师，需求如下

设计学生和老师的结构体，其中在老师的结构体中，有老师姓名和一个存放5名学生的数组作为成员

学生的成员有姓名、考试分数，创建数组存放3名老师，通过函数给每个老师及所带的学生赋值

最终打印出老师数据以及老师所带的学生数据。

**示例：**

```cpp
struct Student
{
	string name;
	int score;
};
struct Teacher
{
	string name;
	Student sArray[5];
};

void allocateSpace(Teacher tArray[] , int len)
{
	string tName = "教师";
	string sName = "学生";
	string nameSeed = "ABCDE";
	for (int i = 0; i < len; i++)
	{
		tArray[i].name = tName + nameSeed[i];
		
		for (int j = 0; j < 5; j++)
		{
			tArray[i].sArray[j].name = sName + nameSeed[j];
			tArray[i].sArray[j].score = rand() % 61 + 40;
		}
	}
}

void printTeachers(Teacher tArray[], int len)
{
	for (int i = 0; i < len; i++)
	{
		cout << tArray[i].name << endl;
		for (int j = 0; j < 5; j++)
		{
			cout << "\t姓名：" << tArray[i].sArray[j].name << " 分数：" << tArray[i].sArray[j].score << endl;
		}
	}
}

int main() {

	srand((unsigned int)time(NULL)); //随机数种子 头文件 #include <ctime>

	Teacher tArray[3]; //老师数组

	int len = sizeof(tArray) / sizeof(Teacher);

	allocateSpace(tArray, len); //创建数据

	printTeachers(tArray, len); //打印数据
	

	return 0;
}
```

**运行/观察结果：** 运行结果包含随机数或时间相关内容，每次执行可能不同，重点观察生成和处理流程。

#### 案例2

**案例描述：**

设计一个英雄的结构体，包括成员姓名，年龄，性别;创建结构体数组，数组中存放5名英雄。

通过冒泡排序的算法，将数组中的英雄按照年龄进行升序排序，最终打印排序后的结果。

五名英雄信息如下：

```cpp
		{"刘备",23,"男"},
		{"关羽",22,"男"},
		{"张飞",20,"男"},
		{"赵云",21,"男"},
		{"貂蝉",19,"女"},
```

**运行/观察结果：** 这段是语法或接口示例，重点观察写法；放入完整程序后再运行验证。

**示例：**

```cpp
//英雄结构体
struct hero
{
	string name;
	int age;
	string sex;
};
//冒泡排序
void bubbleSort(hero arr[] , int len)
{
	for (int i = 0; i < len - 1; i++)
	{
		for (int j = 0; j < len - 1 - i; j++)
		{
			if (arr[j].age > arr[j + 1].age)
			{
				hero temp = arr[j];
				arr[j] = arr[j + 1];
				arr[j + 1] = temp;
			}
		}
	}
}
//打印数组
void printHeros(hero arr[], int len)
{
	for (int i = 0; i < len; i++)
	{
		cout << "姓名： " << arr[i].name << " 性别： " << arr[i].sex << " 年龄： " << arr[i].age << endl;
	}
}

int main() {

	struct hero arr[5] =
	{
		{"刘备",23,"男"},
		{"关羽",22,"男"},
		{"张飞",20,"男"},
		{"赵云",21,"男"},
		{"貂蝉",19,"女"},
	};

	int len = sizeof(arr) / sizeof(hero); //获取数组元素个数

	bubbleSort(arr, len); //排序

	printHeros(arr, len); //打印


	return 0;
}
```

**运行/观察结果：** 运行后会按输出语句打印对应内容，变量值可结合初始化、赋值和函数调用顺序推导。

#

## 结构体在 C 和 C++ 中的写法差异

### 定义结构体
结构体定义由关键字 **struct** 和结构体名组成，结构体名可以根据需要自行定义。

struct 语句定义了一个包含多个成员的新的数据类型，struct 语句的格式如下：

```cpp
struct type_name {
member_type1 member_name1;
member_type2 member_name2;
member_type3 member_name3;
.
.
} object_names;
```

**运行/观察结果：** 这段偏语法定义，通常需要配合调用代码一起编译，重点看定义方式和使用位置。

### 访问结构体成员
下面是声明一个结构体类型 **Books** ，变量为 **book，** 为了访问结构的成员，我们使用 **成员访问运算符（.）** 。成员访问运算符是结构变量名称和我们要访问的结构成员之间的一个句号。

```cpp
struct Books
{
   char  title[50];
   char  author[50];
   char  subject[100];
   int   book_id;
} book;

book.book_id = 1;
strcpy(book.title,"春天");
```

**运行/观察结果：** 这段偏语法定义，通常需要配合调用代码一起编译，重点看定义方式和使用位置。

```cpp
typedef struct Books //Books可忽略不写
{
   char  title[50];
   char  author[50];
   char  subject[100];
   int   book_id;
}Books_Rename;

struct Books book;   //第一种写法,不使用typedf必须加 struct 定义结构体
Books_Rename book;   //用typedef后的写法，9,11行不能共存，因为变量名相同。

book.book_id = 1;0
strcpy(book.title,"春天");
```

**运行/观察结果：** 这段偏语法定义，通常需要配合调用代码一起编译，重点看定义方式和使用位置。

### 指向结构的指针
您可以定义指向结构的指针，方式与定义指向其他类型变量的指针相似，如下所示：

```cpp
struct Books *struct_pointer;
```

**运行/观察结果：** 这段偏语法定义，通常需要配合调用代码一起编译，重点看定义方式和使用位置。

现在，您可以在上述定义的指针变量中存储结构变量的地址。为了查找结构变量的地址，请把 & 运算符放在结构名称的前面，如下所示：

```cpp
struct_pointer = &Book1;
```

**运行/观察结果：** 这段是语法或接口示例，重点观察写法；放入完整程序后再运行验证。

为了使用指向该结构的指针访问结构的成员，您必须使用 -> 运算符，如下所示：

```cpp
struct_pointer->title;
(*struct_pointer).title;
```

**运行/观察结果：** 这段是语法或接口示例，重点观察写法；放入完整程序后再运行验证。

### 结构体与函数
1.  作为函数参数

```cpp
struct Books
{
    ···
    int   book_id;
} book;

void fun1(Books book){
}
//通常使用指针传递
void fun2(Books *book){
}
```

**运行/观察结果：** 这段偏语法定义，通常需要配合调用代码一起编译，重点看定义方式和使用位置。

2.  作为函数返回类型

### C++中的结构体
1.  定义 与上述定义一致，不同的是，在 C++ 中即使不使用 typedef struct 来定义结构体，定义结构体变量时也无需在变量前加 struct

```cpp
//定义结构体
struct Books
{
   char  title[50];
   char  author[50];
   char  subject[100];
   int   book_id;
};
//定义结构体变量
book book1;
```

**运行/观察结果：** 这段偏语法定义，通常需要配合调用代码一起编译，重点看定义方式和使用位置。

2.  面向对象 `struct` 默认的成员和继承是 `public`

```cpp
struct Books
{
  public://默认
    string title;
    string author;
    string subject;
    int    book_id;
  //构造函数
   Books(string t,string author,string subject,int id){
        title = t;
        author = author;
        subject = subject;
        book_id = id;
   }
   //析构函数
   ~Books(){
    ...
   }
  //成员函数
  void fun1(){
   ...
  }
   private:
   protected:
};
//初始化结构体
Books book1("title","author","subject",1);
```

**运行/观察结果：** 这段是语法片段，重点看写法；补全上下文后再运行。

## 共用体 union

**共用体** 是一种特殊的数据类型，允许您在相同的内存位置存储不同的数据类型。您可以定义一个带有多成员的共用体，但是任何时候只能有一个成员带有值。共用体提供了一种使用相同的内存位置的有效方式。

### 定义共用体
为了定义共用体，您必须使用 **union** 语句，方式与定义结构类似。union 语句定义了一个新的数据类型，带有多个成员。union 语句的格式如下：

```cpp
union [union tag]
{
   member definition;
   member definition;
   ...
   member definition;
} [one or more union variables];
```

**运行/观察结果：** 这段是语法片段，重点看写法；补全上下文后再运行。
```cpp
//举例：
union Type_Name
{
   int i;
   float f;
   char str1[20];
   string str2;
} object_name;

//调用方式
object_name.i = 5;
object_name.f = 6.0f;
object_name.str2 = "你好！";
```

**运行/观察结果：** 这段是语法或接口示例，重点观察写法；放入完整程序后再运行验证。

注意：共用体所占内存大小，按成员变量需占内存最大的来。
