## 设计模式学习笔记

### 1、基本原则
* 开闭原则
	* 面向对象设计过程中应该保证对继承开发，对修改关闭
* 里式替换原则
	* 主要是在面向对象的设计过程中对于继承的原则，要确保继承过程中，超类所拥有的性质在子类中仍然存在
	* 子类应该拓展父类的功能，而不是修改父类的功能
* 依赖倒置原则
	* 高层模块不应该依赖底层模块，两者都应该依赖其抽象
	* 抽象不应该依赖细节，细节应该依赖抽象
	* 要面向接口编程而不是面向实现编程
* 单一职责原则
	* 一个类应该有且仅有一个引起他变化的原因，即一个对象应该承担单一职责
	* 方法也同样，一个方法应该尽量只做好一件事情
* 接口隔离原则
	* 尽量将大接口拆分成小的，用户更关心的接口，让接口中只包含用户感兴趣的方法
* 迪米特法则
	* 如果两个软件实体无需直接通信，那就不应该发生直接的互相调用，可以通过第三方转发这个调用
	* 只与你直接朋友交谈，不要和陌生人说话
* 合成复用原则
	* 在软件使用过程中尽量使用组合或者聚合的方式而不是继承的方式去实现

### 2、创建型模式----单例模式
* 单例模式：某些类只能生成一个实例对象。
	* 类只有一个实例对象（单一对象）
	* 实例对象必须由实例类单独创建（对象必须由类创建）
	* 单例类对外提供一个全局的单例访问点（必须提供全局访问点）

（1）懒汉模式，类加载的时候并没有生成实例，只有在第一次获得的时候才创建

```java
public class LazySingleton{
	private static volatile LazySingleton instance;
	private LazySingleton(){
	}
	public static getInstance(){
		if(instance == null){
			instance = new LazySingleton();
		}
		return instance;
	}
}
```

***该版本不是线程安全的版本，多线程访问的情况下可能会出现问题***

线程安全版本：

```java
public class ThreadSafeSingleton{
	private static volatile ThreadSafeSingleton instance;
	private ThreadSafeSingleton(){
	}
	public synchronized static ThreadSafeSingleton getInstance(){
		if(instance==null){
			instance=new ThreadSafeSingleton();
		}
		return instance;
	}
}
```

双检锁版本：

```java
public class ThreadSafeSingleton{
	private static volatile ThreadSafeSingleton instance;
	private ThreadSafeSingleton(){
	}
	public static ThreadSafeSingleton getInstance(){
		if(instance==null){
			synchronized(ThreadSafeSingleton.class){
				if(instance==null){
					instance=new ThreadSafeSingleton();
				}
			}
		}
		return instance;
	}
}
```

（2）饿汉模式，类加载的时候就生成实例了

```java
public class HungrySingleton{
	private static HungrySingleton instance = new HungrySingleton();
	private HungrySingleton(){
	}
	public static HungrySingleton getInstance(){
		return instance;
	}
}
```

***类加载的时候就创建好了，所以这种是线程安全的***

（3）枚举类版本

```java
public enum Singleton{
	INSTANCE();//这里意味着可以传入参数
	
	//该方法是默认的
	private Singleton(){
	}
}
```

### 3、创建型模式----原型模式
* 定义：用一个已经创建的实例作为原型，通过复制改原型对象来创建一个和该原型对象相似的新对象。
* java提供了clone方法，可以很好的实现原型模式

```java
public class Realizetype implements Cloneable{
    public Realizetype(){
        System.out.println("具体原型创建成功！");
    }
    public Object clone() throws CloneNotSupportedException{
        System.out.println("具体原型复制成功！");
        return (Realizetype)super.clone();
    }
}
```

### 4、创建型模式----工厂方法模式
* 定义：创建一个工厂的接口，将具体的创建的实现推迟到具体的工厂子类中去实现。
* 四个角色：
	* 抽象工厂
	* 具体工厂
	* 抽象产品
	* 具体产品

示例代码：

```java
public interfact Product{
	public void show();
}

public class Product1 implements Product{
	public void show(){
		System.out.println("Product1--->");
	}
}

public class Product2 implements Product{
	public void show(){
		System.out.println("Product2--->");
	}
}

public interface AbstractFactory{
	public Product produce();
}

public Factory1 implements AbstractFactory{
	public Product produce(){
		return new Product1();
	}
}

public Factory2 implements AbstractFactory{
	public Product produce(){
		return new Product2();
	}
}
```

### 5、创建型模式----抽象工厂模式
* 定义：工厂方法模式的升级版本，可以生产多个等级的产品
示例代码：

```java
interface AbstractFactory{
	public Product1 newProduct1();
	public Product2 newProduct2();
}

public class Factory1 implements AbstracFactory{
	public Product1 newProduct1(){
		return new Product1();
	}
	public Product2 newProduct2(){
		return new Product2();
	}
}
```

### 6、创建型模式----建造者模式
* 定义：将复杂对象的构造和他的表示分开，使得同样的构建可以创建不同的表示
示例代码：

```java
public class Student{
	private String name;
	private int age;
	private List<String> courses;
	public Student(){
	}
	
	public void setName(String name){
		this.name=name;
	}
	
	public void setAge(int age){
		this.age=age;
	}
	
	public void setCourses(List<String> courses){
		this.courses=courses;
	}
}

public class StudentBuilder{
	private String name;
	private int age;
	private List<String> courses;
	
	public StudentBuilder name(String name){
		this.name=name;
		return this;
	}
	
	public StudentBuilder age(int age){
		this.age=age;
		return this;
	}
	
	public StudentBuilder courses(List<String> courses){
		this.courses=courses;
		return this;
	}
	
	public static Student build(){
		Student student=new Student();
		student.setName(this.name);
		student.setAge(this.age);
		student.setCourses(this.courses);
		return student;
	}
}
```

### 7、