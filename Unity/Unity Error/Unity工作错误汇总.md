# Unity错误汇总

## 常见错误

### Unity本体相关

#### 1. 空引用


1. 脚本序列化在Inspector面板上的属性，没有拖拽目标进去，程序运行起来找不到就报空。如：Player有个Public的Weapon，没有拖拽进去，运行时，该引用Weapon的语句就会报错

2. 引用的对象没有来及实例化，如：切换场景后Player在Awake就去找相机脚本，有可能Player比相机脚本实例化的早，此时Player就会报空，但去Hierarchy面板却能看见相机   
    解决办法：结合脚本的生命周期与GameObject的生成时机修改逻辑顺序

3. 代码逻辑问题导致报空，如：   
    1.  删除过的对象依然被访问
    2.  压根就引用错了对象 
    3.  在经常出现报空的地方没有==null逻辑保护（如：Label脚本在Start时会找子节点的Image，但外部直接调用修改方法修改Image，比Start还早，就报空，可以加一个判断）     
解决办法：复盘不了代码就打断点一步步找，或者疯狂debug

#### 2. 委托内容不对

原因是Lambda触发闭包，先举个例子：    

    void Start()
    {
        Action action;
        int value = 3;
        action = () => { print(value); };
        value = 30;
        action();

        Action action1;
        string str = "第一次";
        action1 = () => { print(str); };
        str = "第二次";
        action1();

    }     
    打印：30 ，第二次

Lambda表达式会将当前表达式内部对外部变量的引用闭包，编译器会根据表达式生成一个对应的类，将用到的变量包装为该类的成员变量。  
如：代码中的value，被lambda表达式引用，编译器生成的表达式类记录value，当外部对value修改后，lanmda打印时通过包装好的成员变量找到外部的value，打印出来30。    

解决办法：在lambda表达式前声明临时变量，将Start中的value复制一份专门留给lambda调用。    

闭包的益处：有时候这种情况也是有用的，比如lambda的委托要在喝酒时给玩家加血，当然是需要基于当前血量加上去的，此时lambda的闭包特性就有了用武之地。



### Unity插件相关

## 少见错误