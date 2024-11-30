# QDialog

- 模态：对话框弹出后会阻塞其他窗口（如：只有先关闭对话框才能再关闭其他窗口
- 非模态：对话框弹出后不会阻塞其他窗口



# 信号与槽

- 一个界面通知另一个界面的方式
- 一个界面发送信号时，链接该信号的槽会进行响应
- 信号连接槽、信号连接信号



# Qt的对象树机制

- 只有父窗口关闭，才会回收子窗口

> 假设父窗口有一个按钮a，点击a触发槽函数，进而在堆中产生一个子窗口，我们关闭子窗口，其实子窗口是隐藏了。随着点击和关闭 重复进行，窗口会越来越多。
>
> 解决方法：
>
> 在主窗口的构造函数中 创建子窗口，析构函数中 回收子窗口，槽函数只用来控制子窗口的显示



# exec()

```qt
int main(int argc,char * argv[])
{
    QApplication a(argc,argv);
    MainWindow w;
    w.show();
    return a.exec();
}
```

exec()作用：启动一个局部事件循环，等待用户操作完成后返回结果

> main函数返回后交给系统处理，因此可以猜到exec()和系统处理有关。
>
> `QApplication a(argc,argv);`将main函数中的参数传到a中，所以控制权从main转到Qt
>
> `return a.exec()`是进入循环等待事件的状态，等待用户和系统的消息并进行处理
>
> return 0;的话，就直接退出程序了
>
> return a.exec()就是进行循环等待事件的状态



# exec()和show()的区别

作用和区别：都是用于显示对话框，区别在于 运行机制 和 返回值

- exec()：当前线程中显示对话框，阻塞当前进程，直到用户关闭对话框。也就是说会开启一个事件循环，知道对话框关闭事件被触发
- show()：当前线程中显示对话框并返回，不会阻塞当前线程，因此程序可以执行其他代码

应用场景：

- exec()：用于显示模态对话框
- show()：用于显示 模态 和 非模态对话框



# tr()

作用：国际化使用

所有传入tr函数的文本，都可以用工具提取出来翻译成其他语言。



## C++中局部变量的销毁顺序

是按照逆序销毁

- 该例中QPushButon父类是QWidget，QWidget的父类是QObject

```C++
int main()
{
    QWidget window;
    QPushButton quit("Quit", &window);
    ...
}
```

正确：先调用子类析构（QPushButton）后调用父类析构（QWidget）

```c++
int main()
{
    QPushButton quit("Quit", &window);
    QWidget window;
    
    quit.setParent(&window);
    ...
}
```

有问题：先调用父类析构，父类析构造接着调用子类析构。然后又在销毁子类局部对象时，又调用了一次子类的析构函数