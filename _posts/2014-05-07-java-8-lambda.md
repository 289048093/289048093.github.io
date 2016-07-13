---
id: 346
title: Java 8 lambda表达式
date: 2014-05-07T14:08:36+00:00
author: 李朝
layout: post
guid: http://www.lizhaoblog.com/?p=346
permalink: /archives/346
seo_follow:
  - 'false'
seo_noindex:
  - 'false'
categories:
  - java
  - java8
tags:
  - java
  - java8
  - lambda
---
jdk8最重要的特性-lambda表达式，意味着java可以函数式编程那样直接定义一个函数了，因此java8先从学习ambda开始。先看熟悉的例子，新建一个线程：

<pre class="brush: java; title: ; notranslate" title="">new Thread(new Runnable() {
@Override
public void run() {
System.out.println("this is thread run method");
}
});
</pre>

用lambda表达式的代码：

<pre class="brush: java; title: ; notranslate" title="">new Thread(() -&gt; System.out.println("this is thread run method")).start()

</pre>

上面“ () -> System.out.println(&#8220;this is thread run method&#8221;) ”这部分就是lambda表达式了。
  
这一行代码运行了一个新建的线程，并用lambda表达式传递了一个 System.out.println(&#8220;this is thread run method&#8221;)的run方法体，相比以前代码简洁了不少。

Java8加入lambda表达式后，意味着方法可以进行传递了，Java也能像python的filter,map,reduce这样进行玩耍了。

在介绍lambda表达式之前，我们先得知道java8的函数接口：

[JSR335](http://download.oracle.com/otndocs/jcp/lambda-0_9_3-fr-spec/index.html)中对函数式接口的描述是：

A _functional interface_ is an interface that has just one abstract method (aside from the methods of `Object`), and thus represents a single function contract. (In some cases, this &#8220;single&#8221; method may take the form of multiple abstract methods with override-equivalent signatures inherited from superinterfaces; in this case, the inherited methods logically represent a single method.)

函数接口是声明有且只有一个抽象方法的接口，这个抽象方法也可以是继承其他接口的，函数接口也可以有java8新增的default方法。上面这段代码用lambda表达式替换了继承Runnable接口的匿名类，这里Runnable就是所谓的函数接口了。在jdk8新增了一个函数接口的注解“ @FunctionalInterface” ，加上这个注解后，该接口就必须符合函数接口的规范，否则无法编译。

申明了函数接口后，我们就可以通过lambda表达式来实现这个接口的方法及这个接口的调用了。

lambda的申明有两部分组成，分别为参数和方法体，由“->&#8221;隔开，
  
参数部分：当没有参数时，我们用&#8221;()&#8221;表示，有参数时，我们在括号内申明参数，如&#8221;(int arg1,String arg2)&#8221;；编译器可以对参数类型自动推导，因此我们还可以写成&#8221;(arg1,arg2)&#8221;,如果只有一个参数，甚至可以去掉&#8221;()&#8221;,直接写成&#8221;arg1&#8243;;

方法体部分：我们可以用&#8221;{}&#8221;来表示，当该函数接口有返回值的时候我们可以这么表示{return value;} ,当然我们也可以直接写成“value”，如果有函数接口的方法有返回值则会自动返回value。

例：

函数接口：

<pre class="brush: java; title: ; notranslate" title="">@FunctionalInterface
interface Addable{
 int add(int i);
}

</pre>

调用接口方法：

<pre class="brush: java; title: ; notranslate" title="">public void testAdd(Addable fn,Integer num){
System.out.println(fn.add(num));
 }

</pre>

因此我们可以这么调用testAdd方法：

<pre class="brush: java; title: ; notranslate" title="">testAdd((int i)-&gt;{return i+100;},88);
</pre>

我们可以把类型定义去掉，方法体只有一句代码我们也可以把“{}&#8221;去掉，同时把return去掉，变成了下面的代码：

<pre class="brush: java; title: ; notranslate" title="">testAdd((i) -&gt; i + 100,88);
</pre>

由于参数只有一个，我们可以把参数部分的&#8221;()”都去掉，最简洁的代码是：

<pre class="brush: java; title: ; notranslate" title="">testAdd(i -&gt; i + 100,88);
</pre>

以上的运行结果当然都是188。

以后也可以这样引用一个lambda，代码：

<pre class="brush: java; title: ; notranslate" title="">Comparator&lt;String&gt; compareLength = (a,b)-&gt;a.length()-b.length();
</pre>

lambda表达式我们可以理解为就是实现了一个函数接口的匿名类的实例。
  
但是实际上java8并不是仅仅只在编译器上做个编译转换处理完事了，我们把上面的代码编译的class类的字节码扒出来看一下就知道了。
  
java代码：

<pre class="brush: java; title: ; notranslate" title="">public class LambdaTest {
 public static void main(String[] args) {
 new Thread(() -&gt; System.out.println("hello lambda")).start();
 }
}
</pre>

在命令行切换到class目录，运行命令：

<pre class="brush: bash; title: ; notranslate" title="">javap -verbose LambdaTest.class</pre>

找到如下的打印的信息:

<pre class="brush: plain; title: ; notranslate" title="">public static void main(java.lang.String[]);
 descriptor: ([Ljava/lang/String;)V
 flags: ACC_PUBLIC, ACC_STATIC
 Code:
 stack=3, locals=1, args_size=1
 0: new #2 // class java/lang/Thread
 3: dup
 4: invokedynamic #3, 0 // InvokeDynamic #0:run:()Ljava/lang/Runnable;
 9: invokespecial #4 // Method java/lang/Thread."&lt;init&gt;":(Ljava/lang/Runnable;)V
 12: invokevirtual #5 // Method java/lang/Thread.start:()V
 15: return
</pre>

可以看到“4: invokedynamic #3, 0 // InvokeDynamic #0:run:()Ljava/lang/Runnable;”，这里有一个invokedynamic的指令。而且没有生成“Method com/sed/LambdaTest$1.”这种带$符号的匿名类。所以这里只是定义了一个方法调用点，只有在执行的时候才能知道具体调用的是哪个方法，这就是所谓的延迟执行。

了解了lamdba表达式，我们可以像Python一样使用filter,map,reduce类似的方法了。

在集合中，java8在Collection接口新增了一个stream()的default方法，可通过返回的Stream对象进行filter和forEach操作，代码：

<pre class="brush: java; title: ; notranslate" title="">List&lt;String&gt; list = Arrays.asList("aaa","bba","ccc");
 list.stream().filter(s-&gt;s.contains("a"))
              .forEach(s-&gt; System.out.println(s+"_"));

</pre>

运行结果如下：

<pre class="brush: plain; title: ; notranslate" title="">aaa_

bba_

</pre>

怎么样，java8的lambda是不是很酷！！！