---
id: 417
title: Java Unsafe类实现任意实例浅克隆
date: 2014-10-31T18:16:35+00:00
author: 李朝
layout: post
guid: http://www.lizhaoblog.com/?p=417
permalink: /index.php/archives/417
seo_follow:
  - 'false'
seo_noindex:
  - 'false'
categories:
  - java
tags:
  - java
  - Unsafe
  - 浅克隆
---
了解java并发包或nio底层的都应该知道Unsafe这个类，如并发包的锁，通过Unsafe#park() 和Unsafe#unPark()来实现线程阻塞和恢复运行的，这个类没有公布源码，但是有很多比较有用的方法，它可以直接操作内存，使用的时候务必要谨慎，不小心可能会造成内存泄漏。

**实现浅克隆思路**
  
为了表述方便，用S代表要克隆的对象，D表示克隆后的对象，SD表示S的内存地址，DD表示D的内存地址，SIZE表示该对象在内存中的大小。

1，获取原对象的所在的内存地址SD
  
2，计算原对象在内存中的大小SIZE
  
3，新分配一块内存,大小为原对象大小SIZE,记录新分配内存的地址DD。
  
4，从原对象内存地址SD处复制大小为SIZE的内存，复制到DD处
  
5，DD处的SIZE大小的内存就是原对象的浅克隆对象，强制转换为源对象类型就可以了
  
<!-more->
  
**unsafe方法介绍**
  
unsafe有很多比较牛逼的方法，刚开始接触可能会感到不可思议，下面介绍几个比较实用的方法。
  
1，Object allocateInstance(Class<?> aClass)


  
这个方法是分配一个实例，它的牛逼之处在于，只要是非abstract的类它都能实例化，即使这个类没有public的构造方法，它甚至能绕过各种JVM安全检查，不运行任何构造方法,当你用这个方法初始化类后，通过getClassLoader()方法视图去获取他的classloader会发现它返回的是null。是不是很神奇
  
2，long objectFieldOffset(Field f)
  
获取对象属性偏移量
  
3，int arrayBaseOffset(Class<?> arrayClass)


  
这个方法是返回一个数组第一个元素的偏移量，这个可以用在获取对象内存地址的时候，因为unsafe没有提供直接获取对象实例内存地址的方法，只有获取通过对象属性偏移量获取属性内存地址的方法，所以我们可以通过构建一个数组对象，通过数组元素偏移量获取元素的内存地址，可以参考后面的代码。
  
4，long allocateMemory(long bytes)
  
这个方法是直接分配一个bytes大小的内存，然后返回内存的起始地址。
  
5,long getLong(Object o, long offset)
  
获取对象o的偏移量为offset位置的long值，这个long值是该位置后64位二进制的long值，同样的方法还有getInt,getByte等
  
6， putLong(Object o, long offset, long x);
  
把long x的2进制值放在对象o的offset偏移量的位置。

offset偏移量：是只相对于另一个地址便宜的byte数；

**获取Unsafe实例**
  
Unsafe这个类是在sun.misc包下，构造器是私有的，提供一个getUnsafe()的方法获取单例，仅供java类库的类使用，即只能是BootstrapClassLoader加载器加载的类使用，源码如下：

<pre class="brush: java; title: ; notranslate" title="">private Unsafe() {}

    private static final Unsafe theUnsafe = new Unsafe();

    @CallerSensitive
    public static Unsafe getUnsafe() {
        Class cc = Reflection.getCallerClass();
        if (cc.getClassLoader() != null)
            throw new SecurityException("Unsafe");
        return theUnsafe;
    }
</pre>

可以通过反射的方式获取它的实例：

<pre class="brush: java; title: ; notranslate" title="">Field theUnsafe = null;
        try {
            theUnsafe = Unsafe.class.getDeclaredField("theUnsafe");
            theUnsafe.setAccessible(true);
            unsafe = (Unsafe) theUnsafe.get(null);
        } catch (NoSuchFieldException | IllegalAccessException e) {
            e.printStackTrace();
        }
</pre>

**1,获取原对象的内存地址**

unsafe类没有提供直接获取实例对象内存地址的方法，但是可以间接获取，大概思路是：构建一个新对象N,N包含了S对象的引用，只要获取到N对S的引用地址就是S的内存地址了；
  
unsafe类获取内存地址的方法只有一个，就是getLong(Object o, long offset)；为了方便，可以构建一个包含S的Object[]数组，获取到Object[]的S引用就可以了，代码如下：

<pre class="brush: java; title: ; notranslate" title="">private static long getAddr(Object obj) {
        Object[] array = new Object[]{obj};
        long baseOffset = unsafe.arrayBaseOffset(Object[].class);
        return unsafe.getLong(array, baseOffset);
    }
</pre>

**2,计算原对象在内存中的大小**
  
我们新建一个对象实例后，jvm做的其实只是在堆中分配非static的Field的内存,其他的static属性，或者方法在加载期间就已经放到内存中去了，所以当我们计算对象大小时只要计算field的大小就行了，jvm分配内存时单个实例中的每个field内存都是连续的，所以我们只需要获得最大偏移量的Field的偏移量，然后加上这个field的大小就可以了，代码如下：

<pre class="brush: java; title: ; notranslate" title="">public static long sizeOf(Class&lt;?&gt; clazz) {
        long maximumOffset = 0;
        Class&lt;?&gt; maxiNumFieldClass = null;
        do {
            for (Field f : clazz.getDeclaredFields()) {
                if (!Modifier.isStatic(f.getModifiers())) {
                    long tmp = unsafe.objectFieldOffset(f);
                    if(tmp&gt;maximumOffset){
                        maximumOffset = unsafe.objectFieldOffset(f);
                        maxiNumFieldClass = f.getType();
                    }
                }
            }
        } while ((clazz = clazz.getSuperclass()) != null);
        long last = byte.class.equals(maxiNumFieldClass)?1:
                ( short.class.equals(maxiNumFieldClass) || char.class.equals(maxiNumFieldClass))?2:
                        (long.class.equals(maxiNumFieldClass)||double.class.equals(maxiNumFieldClass))?8:4;
        return maximumOffset + last;
    }
</pre>

**3,新分配一块大小为SIZE的内存**
  
Unsafe提供了方法 long allocateMemory(long bytes); 可直接分配一块内存，返回内存地址。

**4，从原对象内存地址SD处复制大小为SIZE的内存到DD位置**
  
见Unsafe方法：void copyMemory(long srcAddress, long destAddress, long bytes) 

**5,DD处的SIZE大小的内存赋值给目标对象**
  
Unsafe没有提供直接读内存转为java对象的方法，但是可以通过类似获取对象内存地址的方法来实现：
  
先新建一个包含S类型属性的对象，让后把DD的内存地址赋值给S类型的属性变量就可以了：

<pre class="brush: java; title: ; notranslate" title="">private static &lt;T&gt; T fromAddress(long addr, long size) {
        Object[] array = new Object[]{null};
        long baseOffset = unsafe.arrayBaseOffset(Object[].class);
        unsafe.putLong(array, baseOffset, addr);
        return (T) array[0];
    }
</pre>

**克隆方法**
  
如果是数组，这个方法就不适用了，因为数组比较特殊，数组类是jvm在运行时动态生成的，有兴趣可以去研究下jvm对数组的处理。最终的克隆方法代码如下：

<pre class="brush: java; title: ; notranslate" title="">public static &lt;T&gt; T shallowClone(T t) throws InstantiationException {
        Class clazz = t.getClass();
        if(clazz.isArray()){
            Object[] os = (Object[])t;
            return (T)Arrays.copyOf(os,os.length);
        }
        long srcAddr = getAddr(t);
        long size = sizeOf(clazz);
        long destAddr = unsafe.allocateMemory(size);
        unsafe.copyMemory(srcAddr, destAddr, size);
        return fromAddress(destAddr, size);
    }
</pre>

**测试**

<pre class="brush: java; title: ; notranslate" title="">Object s = new Foo(8,888L,"test")
        Object s2 = shallowClone(s);
        Assert.assertEquals(s, s2);
        Assert.assertTrue(s != s2);
</pre>

ps：用Unsafe分配的内存不在jvm管理的范围内，所以，jvm不会自动去回收这一块内存，你得通过Unsafe#freeMemory(long address) 去释放这块的内存。