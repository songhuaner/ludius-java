# 泛型

## 作用

## 类型变量 参数化类型
## 实现形式

### 泛型类
```
class A<T>{

}
```
#### 继承泛型类

```
class Animal<T>{
    private T food;

    Animal(){}
    Animal(T f){
        food=f;
    }

    public T getFood() {
        return food;
    }

    public void setFood(T f) {
        this.food =f;
    }
}


class Cat extends Animal<Fish>{

    @Override
    public Fish getFood() {
        return super.getFood();
    }

    @Override
    public void setFood(Fish f) {
        super.setFood(f);
    }
}


class Person<T> extends Animal<T>{

    Person(){
        super();
    }

    Person(T e) {
        super(e);
    }
}

```





### 泛型接口




### 泛型方法



#### 桥接方法 多态
场景:
实现 泛型接口 的类，编译器会为 这个类生成 桥接方法
继承 超类的 方法，并且升级方法的返回类型（即子类 覆写 超类方法 时，返回类型 升级为 原返回类型的父类）

```


```

### 通配符

#### 型变（Variance）
Java的数组默认就支持型变：
只要A是B的子类，那么A[]就相当于B[]的子类，比如Integer是Number的子类，因此Integer[]就相当于Number[]的子类。
型变:
以下代码可以编译通过，运行时，报异常
无法确定Number[]类型的变量实际引用的数组对象
Number[]类型的变量实际引用的数组完全可以是Integer[]、也可是Float[]、也可是Double[]......，因此当程序尝试向 Number[]数组中存入元素时，总有可能导致ArrayStoreException异常
```
public class ArrayVariance
{
  public static void main(String[] args)
  {
    Integer[] intArr = new Integer[5];
    
    // 数组默认就支持型变，因此下面代码是正确的
    Number[] numArr = intArr;
    
    // numArr只要求集合元素是Number，因此下面代码也可通过编译
    numArr[0] = 3.4;  // ①
    
  }
}

```
<B>Java泛型不支持默认的型变。</B>
```
即使A是B的子类，那么List<A>也不是List<B>的子类。
比如Integer是Number的子类，而List<Integer>却并不是List<Number>的子类。


import java.util.*;
public class GenericNoVariance
{
  public static void main(String[] args)
  {
    List<Integer> intList = List.of(2, 4);
    
    // 泛型默认不支持型变，因此下面代码编译错误
    List<Number> numList = intList;  // ①

    numList.add(3.4);    // ②
  }
}
```

#### 通配符的作用
> List<T> , 这个 T 是一个形参，可以理解为一个占位符，被使用时，会在程序运行的时候替换成具体的类型，比如替换成String，Integer之类的。


> List<?>, 这个 ？ 是一个实参，这是Java定义的一种特殊类型，比Object更特殊，就像一个影子。比如List<Object>和List<String>是没有父子关系的，这是两个类型，List<Object>类型和List<String>类型；但是List<?> 是 List<String>的父类。

> 用数学集合的语言来表述，? 表示了集合【所有Java类型】这个整体；而 T 表示了集合【所有Java类型】中的一个成员。

```
public static void main(String[] args) {

        List<Integer> inList = new ArrayList<>();
        List<Number> numberList = inList; //编译异常
        
        List<String> strList = new ArrayList<>();
        inList = strList;//编译异常
        
        List<?> list = inList;
        list = strList;// 编译异常

        Object o = list.get(0);// 可以获取Object

        list.add(null); //可以添加null

        // 不能添加
        list.add("");
        list.add(1);

    }
```

#### PECS原则
##### Producer-Extends  <? extends E> 协变

> 为了让泛型支持型变，Java引入了通配符上限语法：如果A是B的子类，那么List\<A>相当于是List<? extends B>的子类，比如Integer是Number的子类，List<Integer>就相当于List<? extends Number>的子类——这种型变方式被称为协变（covariance）。

> 使用 extends 确定上界的只能是生产者，只能往外生产东西，取出的就是上界类型。不能往里塞东西。[extends get] 协变指定了上界(限定父类，允许往下的子类), 限制调用 set 方法, 允许调用get来保证类型的安全

```
public static void main(String[] args)
  {
    List<Integer> intList = new ArrayList<>();
    intList.add(2);
    intList.add(4);

    List<Double> doubleList = new ArrayList<>();
    doubleList.add(2.1);
    doubleList.add(4.3);

    // List<? extends Number>支持协变，
    // 因此只要元素是Number子类的List集合，就可以赋值给numList集合

    List<? extends Number> numList = intList;  // ①
    // 取出的元素被当成Number处理
    Number n1 = numList.get(0);   // ②
    System.out.println(n1);

    // List<? extends Number>支持协变，
    // 因此只要元素是Number子类的List集合，就可以赋值给numList集合
    List<? extends Number> numList2 = doubleList; // ①
    // 取出的元素被当成Number处理
    Number n2 = numList2.get(0);  // ②
    System.out.println(n2);
  }

```

> Java引入了通配符下限语法是为支持逆变（controvariance）：如果A是B的父类，那么List<A>反而相当于是List<? super B>的子类，比如Number是Integer的父类，List<Number>反而相当于List<? super Integer>的子类——这种型变方式被称为逆变（contravariance）。

> Consumer-Super   <? super E> 逆变
使用 super 确定下界的只能做消费者，只能往里塞东西。取出的因为无法确定类型只能转成 Object 类型
[super set] 逆变限定了下界(限定子类，允许往上的父类), 允许调用 set 方法, 并可以将objects赋值给它

```
    public static void main(String[] args)
    {
        List<Number> numList = new ArrayList<>();
        numList.add(2);
        numList.add(4.3);

        List<Object> objList = new ArrayList<>();
        objList.add("Java");
        objList.add(3.5f);
        
        // List<? super Integer>支持逆变，
        // 因此只要元素是Integer父类的List集合，就可以赋值给intList1集合
        List<? super Integer> intList1 = numList;  // ①
        // 逆变的集合添加元素完全没问题——集合元素肯定是Integer的父类（此处为Number）
        intList1.add(20);   // ②
        System.out.println(intList1);

        // List<? super Integer>支持逆变，
        // 因此只要元素是Integer父类的List集合，就可以赋值给intList2集合
        List<? super Integer> intList2 = objList; // ①
        // 逆变的集合添加元素完全没问题——集合元素肯定是Integer的父类（此处为Object）
        intList2.add(30);   // ②
        System.out.println(intList2);

        // 取出集合元素时，集合元素只能被当成Object处理
        Object ob1 = intList1.get(0);
        Object ob2 = intList2.get(0);
    }

```

#### T与？的区别

1 List<T> , 这个 T 是一个形参。List<?> ,? 是实参
``` 
定义方法时
  public static void setNullWildcard(List<?> list){
        list = null;
    }
    
    public static <T> void setNullT(List<T> list){
        list = null;
    }

```

2 T可多继承
```
public static <T extends Number & ExtendInterface> void printListTExtend(List<T> list){
        for(Number e: list){
            System.out.print(e + " ");
        }
        System.out.println("");
    }
```

3 T 可以写入
```
public static <T extends Number> void addTExtend(List<T> list, T e){
        list.add(e);
    }
```

4 T 不能限定父集
不支持 <T super E>



## 自限定
> 基类用导出类替代其参数
> 参数协变 方法参数类型会随子类而变化
> 保证类型参数必须与正在被定义的类相同
> 自限定只能强制作用于继承关系

普通泛型类-构建自限定
```
class BasicHolder<T> {
    T element;
    void set(T arg) { element = arg; }
    T get() { return element; }
    void f() {
        System.out.println(element.getClass().getSimpleName());
    }
}

class Subtype extends BasicHolder<Subtype> {}
```

标准实现
```
class SelfBounded<T extends SelfBounded<T>>{

}
```

另一种实现
```
public class Basic <T>{

    T t;

    public T get(){
        return t;
    }

    public void set(T t){
        this.t = t;
    }
}

public class Self<T extends Basic<T>> extends Basic<T>{
}


public class Self<T extends Self<T>> extends Basic<T>{
}

```

```
class Base{}
class Derived extends Base{}

class GenericSetter<T>{
    void set(T arg) {
        System.out.println("GenericSetter.set(Base)");
    }
}


class DerivedGS extends GenericSetter<Base>{
    void set(Derived derived) {
        System.out.println("DerivedGS.set(Derived)");
    }
}

Base base = new Base();
Derived derived = new Derived();           
DerivedGS dgs = new DerivedGS();

// 调用了父类方法
dgs.set(base);
dgs.set(derived);

///output：
GenericSetter.set(Base)
DerivedGS.set(Derived)

```


## 实现原理(类型擦除)

实现泛型接口