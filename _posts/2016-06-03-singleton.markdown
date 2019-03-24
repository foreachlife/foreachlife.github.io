---
layout:     	post
title:     		设计模式之单例模式
author:     	YY
tag:           设计模式
subtitle:    	单例模式的几种写法
excerpt_separator: ""
dataset:    /projectors/data.json
---


<h2>单例是最常用的设计模式之一,为什么要使用单例模式？</h2><br>
<p>在一些特定的场景下,我们只需要一个实例对象,这时就使用单例模式。让这个类自己保存它的唯一实例,而且只提供一个访问的方法。
但是写好一个单例模式并不简单哦,而且有很多种写法,让我们来看一下有哪几种写法。</p>

<h3>一 1懒汉模式(非线程安全)</h3><br>
多线程环境下会产生不同的实例,所以不是线程安全的
{% highlight ruby %}
public class Singleton {

    private static Singleton instance;

    public static Singleton getHungrySingleTon() {
        if(null==instance){
            instance=new Singleton();
        }
        return instance;
    }

}
{% endhighlight %}

<h3>一 2懒汉模式(线程安全)</h3><br>
在方法上加锁,确保每次都只有一个线程可以调用,所以是线程安全的,但是这种锁粒度太大,每次获取实例都会获取锁释放锁，所以这种效率比较低。
{% highlight ruby %}
public class Singleton {  
    private static Singleton instance;      
    public static synchronized Singleton getInstance() {  
    if (instance == null) {  
        instance = new Singleton();  
    }  
    return instance;  
    }  
}  
{% endhighlight %}


<h3>一 3懒汉模式(线程安全)</h3><br>
采用双检锁模式，这里最关键的是volatile关键字确保了线程安全，volatile是如何确保线程安全的呢？<br>
这就涉及到jvm内存模型指令重排序的知识，volatile可以确保在它修饰的代码之后的代码不在它前面执行<br>
假如不加volatile关键字，当线程A在创建单例对象时，在构造方法被调用之前，就为 该对象分配了内存空间并将对象设置为默认值。<br>
此时线程A就可以将分配的内存地址赋值给instance字段了，然而该对象可能还没有完成初始化操作。线程B来调用newInstance()方法，<br>
得到的 就是未初始化完全的单例对象，这就会导致系统出现异常行为。

{% highlight ruby %}
public class Singleton {
    private volatile static Singleton instance;
    
    public static  Singleton getInstance()throws Exception {
    
        if (instance == null) {
            synchronized (Singleton.class){
                if(instance==null){
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
{% endhighlight %}

<h3>二 1饿汉模式(线程安全)</h3><br>

{% highlight ruby %}
public class Singleton {

    private static Singleton instance = new Singleton();

    public static synchronized Singleton getInstance() {

        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }

} 
{% endhighlight %}
<h3>二 2饿汉模式(静态块实现,线程安全)</h3>
{% highlight ruby %}
public class Singleton {

    private static Singleton instance = null;

    static {
        instance = new Singleton();
    }

    public static Singleton getInstance() {
        return instance;
    }
}  
{% endhighlight %}

<h3>三 静态内部类(线程安全)</h3><br>
{% highlight ruby %}
public class Singleton {  
    private static class SingletonHolder {  
    private static final Singleton INSTANCE = new Singleton();  
    }  
    private Singleton (){}  
    public static final Singleton getInstance() {  
    return SingletonHolder.INSTANCE;  
    }  
}  
{% endhighlight %}
<h3>四 枚举(线程安全)</h3><br>
{% highlight ruby %}
public enum Singleton {  
    INSTANCE;  
    
}  
{% endhighlight %}






















