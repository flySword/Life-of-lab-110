
# 基于抽象编程与面向对象
---

***

### 示例 -- 数据文件读取

读取三维模型数据文件中的点信息，先假定用于可视化

```
test.vtk
# vtk DataFile Version 3.0
Right rectangular prisms grid
ASCII
DATASET UNSTRUCTURED_GRID

POINTS  8   double
0   0   0
50  0   0
0   50  0
50  50  0
0   0   50
50  0   50
0   50  50
50  50  50

CELLS 1 9
8   0   1   2   3   4   5   6   7
```

***

# 基于函数的抽象

***

### 定义与使用

java版c++，未测试..

```c++
// 从vtk文件中读取点数据
Point* getPointsFromVtkFile(string filename){ /*...*/}
```

```c++
// 使用上面的函数
Point *points = getPointsFromVtkFile("filename");
```

***

### 问题

```c++
// 使用上面的函数
// string filename
Point *points = getPointsFromVtkFile(filename);
```

当此处需要读取osg文件以及其它文件格式时，需要改viewer源码

*** 

### 一个解决方案

```c++
Points* getPointsFromDataFile(const string &filename){
  if(/*文件后缀为osg*/)
    return getPointsFromVtkFile(filename);
  else if(/*文件后缀为osg*/)
    return getPointsFromOsgFile(filename);
  else /*....*/
}
```

小缺陷：每次添加新的文件类型时需要修改此函数，调用此函数的代码都需要重新编译（影响打包和测试等问题）。

***

### 基于函数的抽象(c语言 + 函数式编程）

数据文件读取函数的特点：输入参数与返回值相同。

指向函数的指针： 

```c++
Point* (*getPointsFromDataFile)(const string &filename);
```

用指针表达一类函数，函数的参数为filename，返回值为Points*

```c++
getPointsFromDataFile = getPointsFromVtkFile;
getPointsFromDataFile("test.vtk");
```

***

### 使用

```c++
// 函数指针作为参数之一
void viewer(const string &filename,
           Points* (*getPointsFromDataFile)(const string &filename)){
  Point *points = getPointsFromDataFile(filename);
  // ...
}
```

上面的函数没有使用具体的函数，只使用抽象的指针，在被调用时才指向具体的函数。

```c++
// 将具体的函数通过参数传入
viewer("test.vtk", getPointsFromVtkFile);
viewer("test.osg", getPointsFromOsgFile);
```

***

### 抽象的好处

- 避免对具体函数的依赖，方便扩展
- 方便代码编写和测试
- 逻辑清晰

```c++
// 写一个得到测试点的函数代替从文件中获取点
// 让viewer函数能够被单独测试不受其它函数的影响
Point * getPointsTest(string filename){
  Point *points = new Points(10);
  points[0].setCoordinate(0,0,0);
  // ....
  return points;
}
viewer("test.vtk", getPointsTest);
```

***

### 总结

通过指向函数的指针代表某一类函数（抽象函数）

尽可能使用抽象函数使程序通用

通过指针和宏定义可以实现基于c语言的面向对象，典型项目：Gtk——linux上的主流桌面实现工具

等同于c++的多态

***

# 基于类的抽象（c++）

接口（interface）：抽象类  （含有没实现的纯虚函数）

相当于对上面函数指针的封装

***

### 抽象基类

定义一种类（对象上面的函数指针表示一种函数）

```c++
class FileReaderBase{
  private string filename;
  FileReaderBase(string filename) { this->filename = filename;}
  // 读取文件中的点
  virtual Point *getPoints() = 0;
  // 读取文件中的三角网
  virtual Triangle *getTriangles() = 0;
}
```

***

### 语法： 抽象基类（有纯虚函数的基类）

```c++
FileReaderBase base("test.vtk"); // 无法创建（因为没有具体的函数实现）
FileReaderBase *p_base;  // 可以创建指针

class VtkFileReader: public FileReaderBase {...} // 假设子类实现了虚函数

p_base = new VtkFileReader("test.vtk"); //可以创建子类
p_base-> getPoints(); // 可以调用子类的函数
```

***

### 针对接口编程（使用抽象基类）

使用抽象基类的指针，可以通用于所有子类。

```c++
void viewer(FileReaderBase *fileReader){
  Point* points = fileReader->getPoints();
  Triangle* triangles = fileReader->getTriangles();
  // 可视化......
}
```

***

### 读取vtk文件的具体实现

```c++
class VtkFileReader : public FileReaderBase{
  private string filename;
  VtkFileReader(string filename) { this->filename = filename;}
  Point *getPoints(); // cpp 文件中实现
  Triangle *getTriangles(); 
}

// 调用上面的函数
viewer(new VtkFileReader("test.vtk"))
```

***

### 总结

先定义一套抽象基类（定义每个函数的作用而非不实现）

使用时只使用抽象基类的函数（通过指针），使代码通用于所有子类的代码。

直到涉及具体逻辑时才赋值子类
（使用具体子类会失去了通用性，设计模式中的创建性模式用于合理创建子类的问题，如工厂模式）。

优点：代码服用、逻辑清晰...

***

# 另外一套思路

---

***

# Object-Oriented

首先，要有对象

没有就new一个 `myFriend = new Friend();`

记得释放 delete myFriend;

***

### 类与对象

```c++
class A{ };  // 类的声明

A a1;  // 创建一个对象
A *a2 = new A();  // 创建一个对象
```

***

### 面向对象三大特征

- 封装 （类代码放一起）
- 继承 （子类可以使用基类的函数与变量）
- 多态 （基类可以指向不同的子类  划重点）

代码复用、尽可能少的修改、逻辑清晰等

***

### 封装

a.cpp 与 b.cpp 中同时定义了相同的函数时，会因符号相同报错。

```c++
void fun(){}
```

定义同名的类和变量时也会报错

常用的解决方案：

1. 放入不同的namespace
2. 放入不同的类

***

### 继承

```c++
class A {
public:
  void fun1(){}
};
class A1 : public A {};
```

继承(子类可以使用基类的成员）

```c++
A1 *a1 = new A1();
a1->fun1();
```

***

### 多态

多态(由于子类肯定有基类的函数）

基类指针可以指向子类

```c++
A *a = new A1();
```

***

### 示例

对于性取向不明（某师兄）的情况，对象需要区分男女两种类型。

讨论dating（约会）和marry（结婚）两个函数。

```c++
class Friend{
  void dating(){cout<<"dating"<<endl;}// 对于子类行为相同
  virtual void marry() = 0; // 对于子类行为不同，抽象为虚函数由子类实现
  //....
};
class GirlFriend:public Friend{
  void marry(){cout<<"marry"<<endl;}
};
class BoyFriend:public Friend{
  void marry(){cout<<"go abroad first"<<endl;}
};
```

***

### 没有多态

就只能对每个具体类都写一个函数。

添加新的类时（泰国某手术)，还需要添加新的函数。

```c++
void datingAndMarry(GirlFriend *friend){
  friend->dating(); 
  friend->marry();
}
void datingAndMarry(BoyFriend *friend){
  friend->dating(); 
  friend->marry();
}
```

***

### 通过多态提高代码的通用性

函数定义时只依赖抽象的Friend类（marry函数为纯虚函数，行为由子类定义）

对于男朋友、女朋友通用（还可以根据需要传入其它）

可以把基类当做是能指向所有子类的万能类型

```c++
void datingAndMarry(Friend *friend){
  friend->dating(); 
  friend->marry();
}
```
***

### 两个设计原则

**里氏替换原则**(面向对象n大设计原则之一）

在设计时，使用基类的地方都能够使用子类。

（能够使用基类的地方尽可能使用基类）

**针对抽象编程**

基类定义抽象接口（纯虚函数），由子类实现具体的逻辑，通过多态调用。

（添加新的子类不影响通过基类调用的逻辑）

***

# 实例：Logger 日志记录器

用于程序运行日志的记录，需求：

- 日志会根据配置文件写入标准输出流（cout）、日志文件
- 未来可能会加入新的日志记录方式(写入数据库等)

```c++
if(result ！= rightResult) // 如果结果存在错误写入日志
  writeLog("result error ...");
```

***

### 直接实现

```c++
writeLog(string log){
  if(loggerType == "cout")
    cout << log << endl;
  else if(loggerType == "file"){
    ofstream fout("loggerFile.txt");
    fout << info << endl;
    fout.close();
    // 一般fout变量需要复用
  }
  else{
    // 如果是写到数据库，此处会有大规模连接和初始化代码
  }
}
```

***

### 封装

每个类封装各自的逻辑代码

```c++
class CoutLogger{
public:
  void writeLog(string log){
    cout << log << endl;
  }
};

class FileLogger{
public:
  ofstream fout;
  FileLogger(){
    fout.open("logger.txt");
  }
  void writeLog(string log){
    fout << log <<endl;
  }
};
```

***

### 类的使用

具体细节由类的内部封装，直接创建对象调用类函数。

```c++
FileLogger logger;
// 或者 CoutLogger logger;
logger.writeLog("test");

```

***

### 依赖了具体的类型

```c++
// 定义对象，只需要一次
FileLogger fileLogger;
CoutLogger coutLogger;

// 每个使用logger的地方都需要具体的对象
if(loggerType == "cout")
  coutLogger.writeLog("...");
else if(loggerType == "file"){
  fileLogger.writeLog("...");
}
```
 
***

### 多态形式

添加一个基类

```c++
class Logger {
public:
  virtual void writeLog(string log) = 0;
};

class CoutLogger : public Logger { /*.....*/};
class FileLogger : public Logger { /*.....*/};
```

***

### 使用时

```c++
// new对象  只调用一次
Logger *logger;
if(loggerType == "cout")
  logger = new CoutLogger();
else if(loggerType == "file"){
  logger = new FileLogger();
}

// 后面需要写Logger的地方直接
logger->writeLog("...");
```

***

### 总结

针对未来可能被修改或者扩展的函数：

1. 首先设计基类包含抽象函数（功能确定但具体实现可能变化）
2. 使用时尽可能使用基类指针（不涉及子类）

优点在于添加新的子类或者修改子类对调用处的代码没有影响。

***

# 设计模式

设计模式。GOF（四人帮，全拼 Gang of Four）提出，提供面向对象的代码设计参考。

创建型模式,当使用代码中出现new具体子类对象会影响代码的通用性，通过包装new的逻辑提高代码的通用性。

后面以单例模式与工厂模式两种创建型模式为例。

***

## 单例模式 (singleton pattern)

如某些情况下的资源独占（GPU只有一个），希望只有一个类对象单独管理资源。

主要目标：对象只创建一次，每次都获得先前第一次创建的对象而不创建新的对象。

***

### 构造函数私有

```c++
class A{
private: // 可省略但不建议省略
  A(){}
};

A *a = new A();
```
上面的代码会因为构造函数私有无法创建对象。

c++会创建默认的公有构造函数，当希望类只能以指定形式创建时，可以使构造函数私有，然后用其它函数创建类。

***

### 一般单例模式

如果对象指针为空则创建新对象，否则得到原有的对象。

```c++
class Singleton{
private:
  static Singleton *m_singleton;// = new Singleton();
  Singleton(){/*...*/}
public:
  static Singleton *getInstance(){
    if (m_singleton == NULL)
      m_singleton = new Singleton();
    return m_singleton;
  }
};

Singleton *Singleton::m_singleton = NULL;
```

多线程下还需要考虑加锁

***

# 工厂模式 (后面的资料度娘比较多）

最常用设计模式之一

工厂模式主要分为： 

- 简单工厂模式（Simple Factory Pattern）
- 工厂方法模式（Factory Method Pattern 经常简称为工厂模式）
- 抽象工厂模式（Abstract Factory Pattern） （略麻烦，后面忽略）

主要目的为:

- 将产品的创建逻辑(如读取本地文件、连接数据库）放入工厂类，简化使用逻辑。
- 隐藏具体创建的对象，提高代码的通用性

***

### 简单工厂模式

前面的例子中，Logger的初始化过程较为复杂，而且会被用于其它地方，因此被封装到一个类。

```c++
class SimpleFactory {
public:
  static Logger *produceLogger() {
    string loggerType; // 从配置文件中读取
    Logger *logger; // 忽略初始化
    if (loggerType == "cout") {
      logger = new CoutLogger();
    } else if (loggerType == "file") {
      logger = new FileLogger();
    } else { ...}
    return logger;
  }
};

// 后面需要写Logger的地方直接
Logger *logger = SimpleFactory::produceLogger();
logger1->writeLog("...");
```

通过简单工厂模式，在添加和修改Logger子类时，只需要修改工厂类而不必要更改所有使用Logger子类的位置。

一般代码中xxxFactory，很有可能是用来得到具体对象。


***

### 反射机制

反射机制，能够通过类名直接生成对象，避免添加新类能够避免修改源码。

c++实现略复杂，下面用的java

 ```java
// simple factoryPatten
class SimpleFactory {
    public static Logger produceLogger(String loggerName) {
         Logger logger = null;
         Class c = Class.forName(loggerName); // 通过类名创建类
         logger = (Logger)c.newInstance();  // 根据类名创建具体的对象
         // 省略 try catch  ......
         return logger;
    }
}
```

通过类的名字得到具体的Logger，当类的名字从配置文件中读取时，修改配置文件即可实现Logger类型的切换，不需要修改源码。

上面的代码无法处理每个类的不同初始化过程，因此后面的工厂模式通过工厂类实现不同的初始化。

***

### 工厂方法模式


对每个Logger的子类都绑定一个Factory。

```c++
class BaseFactory{
public:
  virtual Logger *produceLogger() = 0;
};

class CoutLoggerFactory : public BaseFactory{
public:
  Logger *produceLogger(){
    Logger *logger = new CoutLogger();
    // 初始化省略
    return logger;
  }
};

//然后通过上面简单工厂的方式读取配置文件得到工厂类，然后创建对象。
```

添加新的类只需要写好的Logger子类和Factory子类包放入指定目录，然后修改配置文件，不需要改动当前的源码。

***

# 设计模式总结

要点：不滥用。

https://www.zhihu.com/question/23757906/answer/25610042
