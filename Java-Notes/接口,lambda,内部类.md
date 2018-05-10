# 一,接口

- 接口绝不能含有`实例域`,接口中的方法也不能引用实例域,**接口没有实例**,提供实例域和方法实现的任务应该由实现接口的那个类来完成
- 在实现接口时**必须**把方法声明为`public`,否则编译器将认为这个方法的访问属性是包可见性
- 接口中可以包含**常量**,接口中的域将自动被设为`public static final`
- 接口可以为方法提供一个默认实现,必须用`default`标记这样的方法,主要用于为暂时没用的方法设空方法

```java
public interface MouseListener {
    default void mouseClicked(MouseEvent event){}
}
```

## 回调

- 通过回调指出某个特定事件发生时应该采取的动作

```java
Timer的参数是一个接口,所以要先定义一个类实现这个接口,
然后获取这个类的实例对象,然后传入Timer()中

public class TimerTest {
    public static void main(String[] args) {
        //向上转型
        ActionListener listener = new TimerPrinter();

        Timer t = new Timer(1000, listener);
        t.start();

    }
}

class TimerPrinter implements ActionListener {
    @Override
    public void actionPerformed(ActionEvent e) {
        System.out.println(" " + new Date());
        Toolkit.getDefaultToolkit().beep();
    }
}
```

--------------------------------------------------------------------------------

# 二,lambda表达式

- `(参数)->{逻辑表达式}`,如果可以推导出参数类型,就可以忽略参数类型
- 需要将一个代码块传递到某个对象(一个定时器或是一个sort方法),这个代码块在将来某个时间被调用

## 函数式接口

- 对于**只有一个抽象方法的接口**,需要这种接口的对象时就可以提供一个lambda表达式,这种接口称为函数式接口

```java
public class TimerTest {
    public static void main(String[] args) {
        //向上转型
        ActionListener listener = new TimerPrinter();

        Timer t1 = new Timer(1000, listener);

        Timer t2 = new Timer(1000, event-> {
            System.out.println(new Date());
            Toolkit.getDefaultToolkit().beep();
        });

        Timer t3 = new Timer(1000, new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                System.out.println(new Date());

            }
        });

        t1.start();

    }
}

class TimerPrinter implements ActionListener {
    @Override
    public void actionPerformed(ActionEvent e) {
        System.out.println(" " + new Date());
        Toolkit.getDefaultToolkit().beep();
    }
}
```

## 方法引用

- 条件:已经有**现成的方法**可以完成你想要传递到其他代码的某个动作
- 用`::`操作符分隔方法名与对象或类名,省略参数

```java
public static void main(String[] args) {
       String strings[] = {};
       Arrays.sort(strings, String::compareToIgnoreCase);

       Arrays.sort(strings,(x,y)->x.compareToIgnoreCase(y));


       Timer t = new Timer(1000, event -> System.out.println(event));
       Timer t1 = new Timer(1000, System.out::println);
   }
```

## 变量作用域

- 在lambda表达式中只能引用**值不会改变的变量** _因为并发执行多个动作时会不安全_

  - lambda引用的变量既不能在内部改变,也不能在外部改变,比如fori

- lambda表达式的体与嵌套块有相同的作用域

```java
非法同名变量:
public static void main(String[] args) {
        Path first = Paths.get("/usr/bin");
        Comparator<String> comparator =
                (first, second) -> first.length() - second.length();
    }
```

- lambda中使用length是指**创建这个lambda表达式的 方法** 的this参数.

  - 把lambda看成代码块,相当于在方法中直接调用`this`参数

```java

public class Main {
    public void init() {
        ActionListener listener = event -> {
            //this 指Main
            System.out.println(this.toString());
        };
    }

    public static void main(String[] args) {

    }
}
```

## lambda总结

- 使用lambda表达式的重点是**延迟执行**

--------------------------------------------------------------------------------

# 三,内部类

- 内部类方法可以访问 **该类定义所在作用域** 中的数据
- 内部类可以对同一个包中的其他类隐藏起来
- 想要定义一个回调函数并且不想编写大量代码时使用匿名内部类

## 内部类特殊语法

- 内部类中声明的所有**静态域**都必须是`final`
- 内部类不能有`static`方法

```java
public class InnerClassTest {
    public static void main(String[] args) {
        TalkingClock clock = new TalkingClock(1000, true);
        clock.start();

        JOptionPane.showMessageDialog(null, "OK");
        System.exit(0);
    }
}


class TalkingClock {
    private int interval;
    private boolean beep;

    public TalkingClock(int interval, boolean beep) {
        this.interval = interval;
        this.beep = beep;
    }

    public void start() {
        ActionListener listener = new TimerPrinter();
        Timer timer = new Timer(interval, listener);
        timer.start();
    }

    public class TimerPrinter implements ActionListener {
        @Override
        public void actionPerformed(ActionEvent e) {
            System.out.println(new Date());
            if (beep) {
                Toolkit.getDefaultToolkit().beep();
            }
        }
    }
}
```

```java
等同于匿名内部类或lambda函数式表达式
class TalkingClock {
    private int interval;
    private boolean beep;

    public TalkingClock(int interval, boolean beep) {
        this.interval = interval;
        this.beep = beep;
    }

    public void start(int interval, boolean beep) {

        Timer t = new Timer(interval, event -> {
            System.out.println(new Date());
            if (beep) {
                Toolkit.getDefaultToolkit().beep();
            }
        });
        t.start();
    }
}
```

## 局部内部类

- 定义在方法中的内部类

  - 不能用public或private声明,它的作用于被限定在声明这个局部类的块中.除了本方法,没有任何方法知道这个局部内部类的存在
  - 不仅能够访问包含它们的**外部类**,还可以访问**声明为final的局部变量**

```java
class TalkingClock {
    private int interval;
    private boolean beep;

    public TalkingClock(int interval, boolean beep) {
        this.interval = interval;
        this.beep = beep;
    }

    public void start() {
        class TimerPrinter implements ActionListener {
            @Override
            public void actionPerformed(ActionEvent e) {
                System.out.println(new Date());
                if (beep) {
                    Toolkit.getDefaultToolkit().beep();
                }
            }
        }

        ActionListener listener = new TimerPrinter();
        Timer timer = new Timer(interval, listener);
        timer.start();
    }
}
```

## 静态内部类

- 有时候使用内部类**只是为了把一个类隐藏在另一个类的内部**,**并不需要内部类 引用外围类 对象**.此时可以将内部类声明为`static`,以便取消产生的引用 _比如为了避免Pair这个大众化的名字产生类名冲突,把这个类隐藏在ArrayAlg类中_
- 静态内部类可以有静态域和静态方法,常规内部类的静态域必须为final,不能有静态方法

```java
package com.company;

public class StaticInnerClassTest {
    public static void main(String[] args) {
        double[] d = new double[20];
        for (int i = 0; i < d.length; i++) {
            d[i] = 100 * Math.random();
        }
        ArrayAlg.Pair p = ArrayAlg.minmax(d);
        System.out.println("min: " + p.getFirst());
        System.out.println("max: " + p.getSecond());
    }
}


/**
 * 工具类ArrayAlg包装一个内部类Pair
 */
class ArrayAlg {
    public static class Pair {
        private double first;
        private double second;

        public Pair(double first, double second) {
            this.first = first;
            this.second = second;
        }

        public double getFirst() {
            return first;
        }

        public double getSecond() {
            return second;
        }
    }

    public static Pair minmax(double[] values) {
        double min = Double.POSITIVE_INFINITY;
        double max = Double.NEGATIVE_INFINITY;
        for (double v : values) {
            if (min > v) min = v;
            if (max < v) max = v;
        }

        return new Pair(min, max);
    }

}
```
