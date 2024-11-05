# 软件设计模式
软件设计模式（Design Pattern，或称软件设计典范）是软件开发人员在软件开发过程中面临的一般问题的解决方案。这些解决方案是众多软件开发人员经过相当长的一段时间的试验和错误总结出来的，能保证代码可靠、易读以及重用。


## 设计模式分类
目前一共有23种软件设计模式，主要分成以下三类。

创建型设计模式有5种，包括单例模式、工厂模式、抽象工厂模式、建造者模式以及原型模式。此类设计模式将对象的创建与使用分离，并总结出多种适配不同场的对象创建方法，同时隐藏对象的创建逻辑。

结构型设计模式有7种，包括适配器模式、桥接模式、组合模式、装饰模式、外观模式、享元模式、代理模式。此类设计模式关注类和对象的组合，即如何将类或对象按某种布局组成更大的结构，并保持结构的灵活和高效。

行为型设计模式有11种，包括访问者模式、模版模式、策略模式、状态模式、观察者模式、备忘录模式、中介者模式、迭代器模式、解释器模式、命令模式、责任链模式。此类设计模式关注类或对象之间的交互关系，包括类或对象之间的通信、合作、职责分配等。

<br/><br/>

# 创建型设计模式
## 单例模式（Singleton）
单例模式下，一个类最多只能创建一个对象。

类的构造和析构函数是private，不允许外部调用；拷贝构造、移动拷贝构造、赋值构造、移动赋值构造均被禁用。类中声明一个private的静态指针来保证只能创建出一个对象，同时也提供一个public的成员函数用于获取静态指针。

单例模式又分为懒汉式和饿汉式，其中懒汉式在多线程环境中可能发生线程冲突，导致创建出多个对象，这需要额外的机制去保护线程安全。

### 懒汉式
懒汉式下，用户需要手动调用函数创建或释放对象，使用起来更灵活，内存占用小。但是在C++11之前，懒汉式线程不安全，一般需要锁机制来保护GetSingle函数：
```cpp
class Single {
private:
    Single() = default;
    Single(const Single &) = delete; //禁用拷贝构造
    Single(Single &&) = delete;      //禁用移动拷贝构造
    Single & operator=(const Single &) = delete; //禁用赋值运算符
    Single & operator=(Single &&) = delete; //禁用移动赋值运算符
    static Single * istance;
public:
    static Single * GetSingle();
    static void FreeSingle() {
        if (istance != nullptr) {
            delete istance;
        }
    }
};

Single * Single::GetSingle() {
    //上锁
    if (istance == nullptr) 
        istance = new Single();
    //解锁
}

Single* Single::istance = nullptr; //记得要初始化

int main() {
    Single* p = Single::GetSingle();
    ...
}
```
上述方式保证了线程安全，但由于每次通过GetSingle获取对象指针时都要上锁与解锁，比较影响性能。对此，可以使用双检查锁的方式，仅让创建对象时才执行一次上锁与解锁：
```cpp
Single * Single::GetSingle() {
    if (istance == nullptr) {
        //上锁
        if (istance == nullptr)  
            istance = new Single();
        //解锁
    }
    return istance;
}
```

### 饿汉式
在饿汉式下，全局静态指针在main函数执行前就创建了单例，线程安全得以保证。但在内存空间上略有浪费（程序运行期间，即使不使用该对象了，对象也会一直占用空间，直到程序结束）。
```cpp
Single * Single::GetSingle() {
    return istance; //无需在此处创建单例，因此直接返回指针
}

Single* Single::istance = new Single(); 
```

## 工厂模式（Factory）
在工厂模式中，产品类的声明专注于描述产品的特性，不负责产品的创建（因此不声明构造函数）。产品对象的创建完全交给专门的工厂类负责，从而将对象的创建和对象的表示分离开。

假设我要生产两种品牌的杯子A和B，对此我们定义基类Cup，以及派生类CupA和CupB。然后定义工厂类，工厂类中有专门的成员函数负责创建杯子对象：
```cpp
class Cup {};
class CupA : public Cup {};
class CupB : public Cup {};

class Factory {
public:
    Cup* createCup(char type) {
        switch (name) {
            case 'A':
                return new CupA;
            case 'B':
                return new CupB;
            default:
                cout << "无此类型的杯子" << endl;
        }
        return nullptr;
    }
};
```
## 抽象工厂模式（Abstact Factory）
工厂模式下，如果有新的品牌CupC，也需要当前的工厂类创建，则需要修改createCup函数，添加新的switch分支，这不符合开闭原则。另外，假设上述品牌拓展产品线，开始生产笔，这样一来工厂类会变得非常冗杂，对此可使用抽象工厂模式。首先声明一个抽象基类作为抽象工厂，然后按照品牌创建派生类：工厂类A（负责品牌A旗下所有类型产品的创建）、工厂类B（负责品牌B旗下所有类型产品的创建）和工厂类C（同理）……
```cpp
class Cup {};
class CupA : public Cup {};
class CupB : public Cup {};
class CupC : public Cup {};

class Pen {};
class PenA : public Pen {};
class PenB : public Pen {};

class AbstractFactory {
public:
    virtual Cup* createCup() = 0; // 专门用于创建杯子
    virtual Pen* createPen() = 0; // 专门用于创建笔
};

class FactoryA : public AbstractFactory {
public:
    virtual Cup* createCup() { return new CupA; }
    virtual Pen* createPen() { return new PenA; }
};

class FactoryB : public AbstractFactory {
public:
    virtual Cup* createCup() { return new CupB; }
    virtual Pen* createPen() { return new PenB; }
};

class FactoryC : public AbstractFactory {
public:
    virtual Cup* createCup() { return new CupC; }
};

int main() {
    AbstractFactory * factoryA = new FactoryA;
    AbstractFactory * factoryB = new FactoryB;
    Cup * newCup = factoryA->createCup(); //创建一个A品牌的杯子
    Pen * newPen = factoryB->createPen(); //创建一支B品牌的笔
}
```

## 建造者模式（Builder）
当需要构建的类比较复杂，且由多个部分组成时，可以考虑使用建造者模式来构建类的对象。

举个例子，假设现在需要生产一些椅子，椅子可以简单视为由靠背（back）、坐垫（seat）以及椅子腿（stick）三种组件构成的。我们需要生产不同类型的椅子，可能有木质椅子（三种组件都是木质的）、钢制椅子（三种组件都是钢质的）、轻量化椅子（靠背和椅子腿是碳纤维的）等等。面对不同类型的椅子，如果在产品类（也就是Chair类）中声明不同的构造函数，不仅没有令Chair对象的创建与使用分离开，当有新类型的椅子需要生产时，还要在Chair类中增加新的构造函数，违反了开闭原则。

使用建造者模式可以妥善解决上述问题。我们使用不同的建造者类来生产不同类型的椅子。木质椅子建造者专门负责生产木质椅子，钢质椅子建造者专门负责生产钢质椅子……如果有新类型的椅子要生产，则只需要新声明一个建造者即可，无需修改其他部分的代码。

所有的建造者类都有一个共同的基类——抽象建造者。抽象建造者是一个抽象类，为所有的派生类建造者规定了椅子的生产规范（每张椅子都要生产靠背、坐垫和椅子腿）。

最后，我们通过一个指挥者类的对象来指挥椅子的生产。该对象可以招募不同的建造者对象来生产出不同类型的椅子。
```cpp
class Chair {
private:
    string back, seat, stick;
public:
    void SetBack(const string & s) { back = s;}
    void SetSeat(const string & s) { seat = s; }
    void SetStick(const string & s) { stick = s; }
    void Show() {
        cout << "该椅子由" << 
            back << "、" << seat << "、" << stick << "构成。" << endl;
    }
};


class AbstractBuilder { //抽象建造者，规定椅子的生产流程：生产靠背、坐垫和椅子腿
protected:
    Chair* product;
public:
    Chair* GetProduct() { return product; }
    virtual void BuildBack() = 0;
    virtual void BuildSeat() = 0;
    virtual void BuildStick() = 0;
};


class WoodChairBuilder: public AbstractBuilder {//木质椅子建造者
public:
    WoodChairBuilder() { product = new Chair; };
    void BuildBack() { product->SetBack("木质靠背"); }
    void BuildSeat() { product->SetSeat("木质坐垫"); }
    void BuildStick() { product->SetStick("木质椅子腿"); }
};


class Director { //指挥者
private:
    AbstractBuilder* builder;
public:
    void SetBuilder(AbstractBuilder* b) { builder = b; }//招募建造者
    Chair* CreateChair() {
        builder->BuildBack();
        builder->BuildSeat();
        builder->BuildStick();
        return builder->GetProduct();
    }
};


int main() {
    AbstractBuilder* builder1 = new WoodChairBuilder;
    Director* director = new Director;
    director->SetBuilder(builder1);  //指挥者招募了一个木质椅子建造者
    Chair* chair1 = director->CreateChair();//指挥者命令建造者生产一张椅子
    chair1->Show();
}
```

## 原型模式（Prototype）
原型模式下，类的对象能通过某种方法完全复制出一个与当前对象完全一样的新对象。在C++中，这个复制过程一般通过拷贝构造函数实现。在原型模式下，会首先声明一个抽象类Prototype，该类声明了一个纯虚函数Clone，用于让对象创建一个与自己完全一样的新对象。如果有对象需要使用该功能，则令该对象的类继承自Prototype。
```cpp
class ProtoType {
public:
    virtual ProtoType* Clone() const =0;
};

class Recipient : public ProtoType {
private:     //Recipient类用于描述收件人，描述的信息包括收件人的姓名及其多个地址
    string name;
    int size;  //地址数量
    string* address; 
public:
    Recipient(string s, int n, string * p = nullptr) {
        name = s;
        size = n;
        address = nullptr;
        if (n > 0 && p) {
            address = new string [n];   // 深拷贝
            for (int i=0; i<n; ++i) address[i] = p[i]; 
        }
    }

    Recipient(const Recipient & a): 
        Recipient(a.name, a.size, a.address) {}

    ~Recipient() { 
        delete [] address; 
    }
    
    virtual ProtoType* Clone() const {
        return new Recipient(*this);
    }
};

int main() {
    string address[2] {"China", "American"};
    Recipient r("Zerg", 2, address);
    ProtoType* p = r.Clone();
}
```
原型模式为对象的复制提供了统一的接口（即Clone成员函数），让代码无需依赖待复制对象所属的具体类。的
<br/><br/>

# 结构型设计模式
## 适配器模式（Adapter）

### 类适配器

### 对象适配器
<br/><br/>


# 行为型设计模式
## 观察者模式


# 结构型设计模式

# MVC
全名Model View Controller，是模型（model）－视图（view）－控制器（controller）的缩写。

该模式将业务逻辑、数据与界面显示分离开来组织代码，将众多的业务逻辑聚集到一个部件里面，在需要改进和个性化定制界面及用户交互的同时，不需要重新编写业务逻辑，达到减少编码的时间。

以下详解三部分各自的功能。

**View** 指用户看到并与之交互的界面。比如由html元素组成的网页界面或者软件的客户端界面。一个应用程序可能有多个界面，对应不同的用户需求，在视图中其实并没有任何的数据处理，它仅仅是用于与用户交互，得到用户的输入并传给控制器或者输出信息。

**Model** 指模型，表示业务规则。在MVC的三个部件中，模型拥有最多的处理任务。被模型返回的数据是中立的，模型与数据格式无关，这样一个模型能为多个视图提供数据，由于应用于模型的代码只需写一次就可以被多个视图重用，所以减少了代码的重复性。

**Controller** 指控制器，接受用户的输入并调用模型和视图去完成任务。控制器本身不输出任何东西和做任何处理。它只是接收请求并决定调用哪个模型构件去处理请求，然后再确定用哪个视图来显示返回的数据。

下图说明了三者之间的调用关系：
![](软件设计模式：以C++为例_1.png)
例如，我们在C#下编写一个简单的用户管理程序：

Main就相当于我们的View，我们在此编写与用户的交互命令，如：
```c#
UserController uc = new UserController();
 
Console.WriteLine("---------用户信息管理系统--------");
 
while(true)
{
    Console.WriteLine("请按指示输入命令：增加-1 修改-2 删除-3 查看-4");
    string command = Console.ReadLine();
    switch (command)
    {
        case "1":
            uc.Add();
            break;
        ...
    }
}
```
当然，在Main开头，我们会实例化一个UserController对象。

在类UserController中，我们编写实现用户相应需求的方法：
```c#
class UserController
{
    List<User> userList = new List<User>();
    public void Add()
    {
        ... 
    }
    public void Remove()
    {
        ... 
    }    
    ...
}
```
然而，这些方法是指向User中的方法，在UserController这一层，正如上文所说，是根据View返回的用户指令，决定调用什么方法去实现，真正修改数据的方法，在User中：
```c#
class User
{
    private string name;
    private int age;
    public string Name { get; set;}
    public int Age { get; set;}
    public User(string name, int age)
    {
        //构造方法
        ...
    }

}
```
<br/><br/>

# 参考资料

[C++设计模式：建造者模式（详解+案例代码）](https://blog.csdn.net/jj6666djdbbd/article/details/128576583)