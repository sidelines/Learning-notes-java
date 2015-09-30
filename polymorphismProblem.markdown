#多态的一些问题
标签（空格分隔）： 多态

---

java多态中的一些问题分析

``` java
 public class test {

    public static void main(String args[])
    {
        A a = new B();
        System.out.println("a的地址="+a);
        System.out.println("在a中--------a="+a.a);
        System.out.print("调用a.get()方法后---------");
        a.get();
        B b = new B();
        System.out.println("b的地址="+b);
        System.out.println("在b中--------a="+b.a);

        System.out.println("-------------将a强制类型转换成b并将a的引用赋给b---------------");
        b=(B)a;
        System.out.println("a的地址="+a);
        System.out.println("b的地址="+b);
        System.out.println("在a中--------a="+a.a);
        System.out.println("在b中--------a="+b.a);

        System.out.print("调用a.get()方法后---------");
        a.get();
        System.out.print("调用b.get()方法后---------");
        b.get();
    }
}

class A{
    int a =1;
    A()
    {
        System.out.println("构造A");
    }

    public void get()
    {
        System.out.println("A中a="+this.a);
    }
}
class B extends A{
    int a =2;
    B()
    {
        System.out.println("构造B");
    }
    public void get()
    {
        System.out.println("B中a="+this.a);
    }
}
```

    程序执行结果： 
    构造A 
    构造B 
    a的地址=stringOperate.B@65b1fd9c 
    在a中——–a=1 
    调用a.get()方法后———B中a=2 
    构造A 
    构造B 
    b的地址=stringOperate.B@88140ed 
    在b中——–a=2 
    ————-将a强制类型转换成b并将a的引用赋给b————— 
    a的地址=stringOperate.B@65b1fd9c 
    b的地址=stringOperate.B@65b1fd9c 
    在a中——–a=1 
    在b中——–a=2 
    调用a.get()方法后———B中a=2 
    调用b.get()方法后———B中a=2

##问题

 1. A a = new B();是a的引用指向了b的空间，为什么会打印出a.a=1而在调用get方法后打印出的是b的结果 
 2. 将a强制类型转换成b并将a的引用赋给b后，a，b指向同一块地址空间为什么打印出的a.a=1 而b.a=2


##解释

    从代码调试角度来看，，我先从转发走起： 
    A a -> 这是申明一个A的对象a，放在栈空间内 , 
    new B(); 这是在调用B的构造方法构造一个B的实例化对象。因为B类继承自A类。所以B类中的构造函数实际应该是这样的 
    public B(){ 
    supre（）；//默认构造父类，之后再执行B中具体的构造 
    } 
    这个时候调用new B（）完了后其实是在堆空间创建了空间大小为B实例对象大小的空间，即在堆空间创建了B的实例化。 
    然而，申明的a是A类型对象，，，并不是A的实例化，，相当于一个A类型的空间的引用。。。可我们创建的空间却是B类型大小的空间 
    但是B是A的继承，这就不妨碍，将 A的引用的指向改为指向我们创建出来的堆空间这块B大小的空间
    
    那么接下来就好解释了，， 
    a的地址，，对应的是a这个申明在栈空间的地址，他的指向是堆空间中的B。 
    接下来就是a.a = 1 ;a.get() =2这个问题了。。首先，，堆空间里的B：他构造了A，构造了B，
    
    那么现在堆空间里就有两个a,一个是==1；
    
    一个==2；这点是重点，，，， 
    子类中的a=2；并不是单纯的覆盖了父类中a的1,而是重新创建空间，，
    
    这样就好解释，，，a.a==1这个了，，，，那么为什么a.get（）==2呢。。这是因为get 方法是被覆写后的方法，，，， 
    覆写后的方法现在要去堆里面拿数据a ,,那么有两个a拿 哪个呢？？？先从自己找，，自己有了就不拿父亲的。正好自己有个 
    a=2,被存在堆空间里。。。。。这样就解释了a.get() == 2；




