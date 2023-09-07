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

PECS原则：Producer-Extends, Consumer-Super。
### 泛型接口

### 泛型方法

### 通配符

#### 协变、逆变

## 实现原理(类型擦除)

/实现泛型接口

## 自限定
```
class SelfBounded<T extends SelfBounded<T>>{

}
```
