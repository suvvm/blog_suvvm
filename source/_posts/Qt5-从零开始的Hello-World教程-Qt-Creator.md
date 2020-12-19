---
title: Qt5--从零开始的Hello World教程(Qt Creator)
date: 2019-04-02 17:10:07
categories: 
- 简单教程
tags:
- Qt5
---
<meta name="referrer" content="no-referrer"/>
### 一、打开Qt Creator

　　本次的目的是用Qt Creator建立一个Hello World项目，在安装Qt之后，首先要打开Qt Creator。

![img](https://img2018.cnblogs.com/blog/1447131/201904/1447131-20190402144344775-843344643.png)

　　就是它啦，打开后会显示如下页面。

![img](https://img2018.cnblogs.com/blog/1447131/201904/1447131-20190402144516906-500975365.png)

### 二、建立新项目

　　点击文件——新建文件或项目 或者使用快捷键Ctrl+N便可以打开新建项目菜单。

![img](https://img2018.cnblogs.com/blog/1447131/201904/1447131-20190402144825806-315022429.png)

　　这里选择其他项目——Empty qmake Project创建一个空的qt项目 HelloWorld_Qt。

![img](https://img2018.cnblogs.com/blog/1447131/201904/1447131-20190402145220721-54460256.png)

　　一路下一步到完成之后就可以看到已经成功建立的项目HelloWorld_Qt。

![img](https://img2018.cnblogs.com/blog/1447131/201904/1447131-20190402145420368-1252528396.png)

　　这个生成的新项目包含两个文件。

 ![img](https://img2018.cnblogs.com/blog/1447131/201904/1447131-20190402145521816-1197688933.png)

　　HelloWorld_Qt.pro是qt的工程文件，HelloWorld_Qt.pro.user是用于记录该次编译所用的打开工程的路径，编译器、构建的工具链、生成目录、打开工程的qt-creator的版本等的文件，更换编译环境是需删除。

　　这样建立Qt项目便完成了。

　　由于这里建立的是一个空项目，我们需要添加文件来编写代码，右击项目HelloWorld_Qt点击Add New，选择C++中的C++ Source File添加一个c++源代码文件，起名叫main一路下一步到完成。

![img](https://img2018.cnblogs.com/blog/1447131/201904/1447131-20190402150231560-2062946358.png)

　　这样就有了一个可以用来编写代码的main.cpp

### 三、编写代码

　　打开main.cpp在其中编写代码我们需要建立一个Qt应用程序，需要引入Qt应用程序类QApplication所以我们引用它

```
#include<QApplication>
```

 

![img](https://img2018.cnblogs.com/blog/1447131/201904/1447131-20190402150738245-1316022751.png)

　　但是这里报错了，编译器告诉我们‘QApplication’类文件没有找到，这是因为没有连接Qt Widgets库，打开HelloWorld_Qt.pro引入Qt Widgets库，添加一句QT += widgets按下Ctrl+S保存，之后回到main.cpp

![img](https://img2018.cnblogs.com/blog/1447131/201904/1447131-20190402151334917-157143616.png)

![img](https://img2018.cnblogs.com/blog/1447131/201904/1447131-20190402151409236-1511120867.png)

　　果然，错误消失了。

　　继续编写代码，我们还需要有一个窗体，需要引入QWidget类

　　

```
#include<QWidget>
```

　　之后便可以开始设计主函数了。

```c++
#include<QApplication>
#include<QWidget>
int main(int argc, char* argv[]){
    //在程序编译完成后会生成一个.exe后缀的可执行文件
    //argc是命令行参数个数
    //argv[0]记录程序名
    //argv[1]~argv[argc-1]记录的输入参数
    //argc argv是标识符，可以修改名称
    //main()函数有argc和argv或没有，即int main() 或void main，都是合法的。
    QApplication app(argc, argv);
    //应用程序抽象类
    //使用在argv中的argc个命令行参数构造一个名为app的QApplication对象。
    //QApplication 类管理图形用户界面应用程序的控制流和主要设置
    //任何一个使用Qt的图形用户界面应用程序，都存在一个QApplication对象

    QWidget w;
    //QWidget类是所有用户界面对象的基类
    //在这里可以把它看作一个窗口，构造了一个窗口对象
    w.setWindowTitle("Hello World!");
    //将窗口的标题设为Hello World!
    w.show();
    //显示窗体
    return app.exec();
    //exec()函数有一个消息循环，在调用之后，程序就被锁定。等待窗口的关闭
}
```

　　执行一下

![img](https://img2018.cnblogs.com/blog/1447131/201904/1447131-20190402152145588-140921127.png)

![img](https://img2018.cnblogs.com/blog/1447131/201904/1447131-20190402152215884-149429949.png)

　　成功显示HelloWorld窗口。 

　　窗口建好之后我们便可以向窗口中添加控件了，现在需要在窗体中显示Hello World！，我们可以通过QLabel标签实现，要向窗体中添加QLabel标签则同样需要引入QLabel类。

```
#include <QLabel>
```

　　之后构造一个标签对象

```c++
textLabel.resize(100,15);
//设定标签宽40高15
textLabel.move(150, 170);
//将标签移动到距离左上角水平100px垂直130px的位置
textLabel.setText("Hello World!");
//标签的初始显示字符串NULL
textLabel.setParent(&w);
```

　　标签QLabel也是一个窗体，它继承自QFrame，而QFrame又继承自QWidget，若想让他显示在我们构造的窗口w中就需要将QLabel对象textLabel的父对象设为w。

　　在这个程序中w是主窗口，主窗口是指没有父对象的窗口。

　　若在此处不使用textLable.setParent(w),text标签将单独弹出一个窗口。

　　这就说明了，所有最终继承至QFrame的控件如果不把控件的父对象设置为当前主窗口，控件将单独作为一另个主窗口弹出。

　　![img](https://img2018.cnblogs.com/blog/1447131/201904/1447131-20190402190800843-918816892.png)

　　注释textLable.setParent(w),text后的效果

　　![img](https://img2018.cnblogs.com/blog/1447131/201904/1447131-20190402190907055-724741945.png)

　　正确效果

　　源代码：

```c++
#include <QApplication>
#include <QWidget>
#include <QLabel>

int main(int argc, char* argv[]){
    //在程序编译完成后会生成一个.exe后缀的可执行文件
    //argc是命令行参数个数
    //argv[0]记录程序名
    //argv[1]~argv[argc-1]记录的输入参数
    //argc argv是标识符，可以修改名称
    //main()函数有argc和argv或没有，即int main() 或void main，都是合法的。
    QApplication app(argc, argv);
    //应用程序抽象类
    //使用在argv中的argc个命令行参数构造一个名为app的QApplication对象。
    //QApplication 类管理图形用户界面应用程序的控制流和主要设置
    //任何一个使用Qt的图形用户界面应用程序，都存在一个QApplication对象
    QWidget w;
    w.resize(400, 400);
    //QWidget类是所有用户界面对象的基类
    //在这里可以把它看作一个窗口，构造了一个窗口对象
    w.setWindowTitle("Hello World!");
    //将窗口的标题设为Hello World!
    QLabel textLabel;
    //构造一个标签
    textLabel.resize(100,15);
    //设定标签宽40高15
    textLabel.move(150, 170);
    //将标签移动到距离左上角水平100px垂直130px的位置
    textLabel.setText("Hello World!");
    //标签的初始显示字符串NULL
    textLabel.setParent(&w);
    textLabel.show();
    w.show();
    //显示窗体
    return app.exec();
    //exec()函数有一个消息循环，在调用之后，程序就被锁定。等待窗口的关闭
}
```

### 四、Hello New World!

　　在成功完成上述项目后我们已经不能满足于单单在窗口中显示Hello World！了，这次我们希望用一个按钮来将Hello World！改为 Hello New World！。

　　新建一个空的Qt项目，名为Hello New World。

![img](https://img2018.cnblogs.com/blog/1447131/201904/1447131-20190407144430673-1999955819.png)

![img](https://img2018.cnblogs.com/blog/1447131/201904/1447131-20190407144527554-1706277458.png)

　　一路下一步到完成，打开HelloNewWorld.pro引入Qt Widgets库，添加一句QT += widgets。

![img](https://img2018.cnblogs.com/blog/1447131/201904/1447131-20190407145104965-23413638.png)

　　这次我们不像上一个项目一样一个main函数到底，我们添加一个C++类作为主窗口框架。

 　右击HelloNewWorld选择Add New添加一个C++ Class

![img](https://img2018.cnblogs.com/blog/1447131/201904/1447131-20190407145222933-287602353.png)

　　我们想让这个类作为我们主窗口的对象类型，我们将其命名为MainWidget，既然MainWidget是一个窗口，我们就让MainWidget作为QWidget的一个子类。

![img](https://img2018.cnblogs.com/blog/1447131/201904/1447131-20190407145638466-777605577.png)

　　将MainWidget的基类设为QWidget。

　　一路下一步到结束。

　　可以发现项目中多了两个文件，mainwidget.h 与 mainwidget.cpp。

**![img](https://img2018.cnblogs.com/blog/1447131/201904/1447131-20190407145926646-486457409.png)**

　　我们的MainWidget类就声明在mainwidget.h中

mainwidget.h:

```c++
#ifndef MAINWIDGET_H
#define MAINWIDGET_H

#include <QWidget>

class MainWidget : public QWidget   //MainWidget的声明
{
    Q_OBJECT
public:
    explicit MainWidget(QWidget *parent = nullptr);
    //MainWidget类的构造函数 因为使用了explicit所以为显式构造函数
    //c++ 类构造函数默认情况下即声明为implicit(隐式)
    //关于显示与隐式的区别这里就不介绍了，诸位若不知道可以搜索
signals:
    //这里可以用来声明信号函数
public slots:
    //这里可以用来声明槽函数
};
#endif // MAINWIDGET_H
```

 mainwidget.cpp: 

```c++
#include "mainwidget.h"

MainWidget::MainWidget(QWidget *parent) : QWidget(parent)
{
    //显示调用MainWidget构造函数
    //调用顺序是先调用父类QWidget的构造函数，再调用派生类的构造函数
    //具体可以看https://www.cnblogs.com/findumars/p/9231742.html这篇博客
}
```

　　之后便可以设计一下我们的主窗口了，根据本次的要求，我们希望主窗口中有一个标签来显示我们的Hello World与Hello New World，有一个按钮来实现点击按钮转换文字，这样就可以确定我们的MainWidget中会有两个成员控件，一个QLabel一个QPushButton。

　　在MainWidget的声明中添加它们。

```c++
#ifndef MAINWIDGET_H
#define MAINWIDGET_H

#include <QWidget>
//添加成员变量QPushButton与QLabel标签需要引入QPushButton与QLabel类。
#include <QPushButton>
#include <QLabel>

class MainWidget : public QWidget
{
    Q_OBJECT
public:
    explicit MainWidget(QWidget *parent = nullptr);
    //MainWidget类的构造函数 因为使用了explicit所以为显式构造函数
    //c++ 类构造函数默认情况下即声明为implicit(隐式)
    //关于显示与隐式的区别这里就不介绍了，诸位若不知道可以搜索

    QPushButton* button;    //成员控件按钮
    QLabel* label;  //成员控件标签

signals:
    //这里可以用来声明信号函数
public slots:
    //这里可以用来声明槽函数
};

#endif // MAINWIDGET_H
```

 　　之后便可以在mainwidget.cpp中编写代码了，我们希望将主窗口设计为一个400*400，文字和按钮显示在中间，文字在按钮上方，在mainwidget.cpp中，MainWidget构造函数处做如下更改 。 

```c++
MainWidget::MainWidget(QWidget *parent) : QWidget(parent)
{
    //显示调用MainWidget构造函数
    //调用顺序是先调用父类QWidget的构造函数，再调用派生类的构造函数
    //具体可以看https://www.cnblogs.com/findumars/p/9231742.html这篇博客
    resize(400, 400);
    //设置窗口大小为400px * 400px
    label = new QLabel("Hello World!", this);
    //初始化控件lable文字显示为Hello World!， 父对象为当前MainWidget窗口对象
    label->setGeometry(150, 100, 130, 50);
    //lable的位置在x = 150px y = 100px 宽为100px高为50px
    button = new QPushButton("change", this);
    //初始化控件button文字显示为change， 父对象为当前MainWidget窗口对象
    button->setGeometry(150, 175, 130, 50);
    //button的位置在x = 150px y = 175px 宽为100px高为50px
}
```

 　　之后设计主函数，在主函数中构建一个MainWidget窗口w，将它显示出来。 

```c++
int main(int argc, char* argv[]){
    QApplication app(argc, argv);

    MainWidget w;   //构建一个MainWidget窗口w
    w.show();   //显示w

    return app.exec();
}
```

　　不要忘记引入QApplication。

### 　　以下为当前mainwidget.cpp的全部代码

```c++
#include "mainwidget.h"
#include<QApplication>

MainWidget::MainWidget(QWidget *parent) : QWidget(parent)
{
    //显示调用MainWidget构造函数
    //调用顺序是先调用父类QWidget的构造函数，再调用派生类的构造函数
    //具体可以看https://www.cnblogs.com/findumars/p/9231742.html这篇博客
    resize(400, 400);
    //设置窗口大小为400px * 400px
    label = new QLabel("Hello World!", this);
    //初始化控件lable文字显示为Hello World!， 父对象为当前MainWidget窗口对象
    label->setGeometry(150, 100, 130, 50);
    //lable的位置在x = 150px y = 100px 宽为100px高为50px
    button = new QPushButton("change", this);
    //初始化控件button文字显示为change， 父对象为当前MainWidget窗口对象
    button->setGeometry(150, 175, 130, 50);
    //button的位置在x = 150px y = 175px 宽为100px高为50px
}
int main(int argc, char* argv[]){
    QApplication app(argc, argv);

    MainWidget w;   //构建一个MainWidget窗口w
    w.show();   //显示w

    return app.exec();
}
```

　　运行一下

　　![img](https://img2018.cnblogs.com/blog/1447131/201904/1447131-20190407152141926-1324001370.png)

　　一个符合我们设计要求的窗口已经完成了。

　　之后就要开始完成点击按钮更换文字的功能了，这里就用到了信号和槽的知识。

　　在QObject类中有一个connect函数其中要求传入四个参数，第一个参数为被用户操作的对象（发出信号的对象当前为我们的change按钮），第二个参数是一个被操作信号（当前为按钮被点击clicked()），第三个参数是我们要进行操作的对象（接受信号的对象当前为我们的主窗体w），第四个参数为对应的槽函数（这就需要我们自己设计一个槽函数了，我们将这个槽函数命名为changeText）。

　　

```
 QObject::connect(w.button, SIGNAL(clicked()), &w, SLOT(changeText()));
```

　　当然，Qt中QObject是绝大多数类的基类，包括QWidget，而我们的MainWidget又是QWidget的子类，所以如下写法依然正确。

```
MainWidget::connect(w.button, SIGNAL(clicked()), &w, SLOT(changeText()));
```

　　之后我们就要在MainWidget的声明中去声明和定义槽函数了（可以只声明，在mainwidget.cpp中定义）。

```
public slots:
    //这里可以用来声明槽函数
    void changeText(){
        label->setText("Hello New World!");
    }
};
```

　　运行一下

![img](https://img2018.cnblogs.com/blog/1447131/201904/1447131-20190407154617832-1219284277.png)

　　点击change

![img](https://img2018.cnblogs.com/blog/1447131/201904/1447131-20190407154646019-1773784269.png)

　　完整代码：

　　HelloNewWorld.pro:

```c++\
QT += widgets

HEADERS += \
    mainwidget.h

SOURCES += \
    mainwidget.cpp
```

 　　mainwidget.h: 

```c++
#ifndef MAINWIDGET_H
#define MAINWIDGET_H

#include <QWidget>
//添加成员变量QPushButton与QLabel标签需要引入QPushButton与QLabel类。
#include <QPushButton>
#include <QLabel>

class MainWidget : public QWidget   //MainWidget的声明
{
    Q_OBJECT
public:
    explicit MainWidget(QWidget *parent = nullptr);
    //MainWidget类的构造函数 因为使用了explicit所以为显式构造函数
    //c++ 类构造函数默认情况下即声明为implicit(隐式)
    //关于显示与隐式的区别这里就不介绍了，诸位若不知道可以搜索

    QPushButton* button;    //成员控件按钮
    QLabel* label;  //成员控件标签

signals:
    //这里可以用来声明信号函数
public slots:
    //这里可以用来声明槽函数
    void changeText(){
        label->setText("Hello New World!");
    }
};

#endif // MAINWIDGET_H
```

 　　mainwidget.cpp: 

```c++
#include "mainwidget.h"
#include<QApplication>

MainWidget::MainWidget(QWidget *parent) : QWidget(parent)
{
    //显示调用MainWidget构造函数
    //调用顺序是先调用父类QWidget的构造函数，再调用派生类的构造函数
    //具体可以看https://www.cnblogs.com/findumars/p/9231742.html这篇博客
    resize(400, 400);
    //设置窗口大小为400px * 400px
    label = new QLabel("Hello World!", this);
    //初始化控件lable文字显示为Hello World!， 父对象为当前MainWidget窗口对象
    label->setGeometry(150, 100, 130, 50);
    //lable的位置在x = 150px y = 100px 宽为100px高为50px
    button = new QPushButton("change", this);
    //初始化控件button文字显示为change， 父对象为当前MainWidget窗口对象
    button->setGeometry(150, 175, 130, 50);
    //button的位置在x = 150px y = 175px 宽为100px高为50px
}
int main(int argc, char* argv[]){
    QApplication app(argc, argv);

    MainWidget w;   //构建一个MainWidget窗口w
    w.show();   //显示w

    QObject::connect(w.button, SIGNAL(clicked()), &w, SLOT(changeText()));

    return app.exec();
}
```

