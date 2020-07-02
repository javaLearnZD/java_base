##Effective Java读书笔记

###1、使用静态工厂方法代替构造器
* 静态工厂方法可以重用已经生成的类实例，因为如果每次都新建的话是比较消耗性能的
	* 典型的Integer里面就是有缓存，缓存的是-128到127的对象，其初始化时是通过静态代码块，在类实例没创建的时候就构造出来了
	* 超出这个范围的就重新新建，同时也不缓存

###2、多个构造器参数的时候考虑使用构建器
* 使用建造者模式，在多个构造器参数的方法中通过flow模式构建对象

``` java
public class ClassRoom{
	private String table;
	private String chair;
	private String students;
	private String teacher;
	
	public static class ClassRoomBuilder{
		private String table;
		private String chair;
		private String students;
		private String teacher;
		
		public classRoomBuilder(){
		}
		public classRoomBuilder table(String table){
			this.table=table;
			return this;
		}
		public classRoomBuilder chair(String chair){
			this.chair=chair;
			return this;
		}
		public classRoomBuilder students(String students){
			this.students=students;
			return this;
		}
		public classRoomBuilder teacher(String teacher){
			this.teacher=teacher;
			rerturn this;
		}
		public classRoom build(){
			return new classRoom(this);
		}
	}
	
	private ClassRoom(ClassRoomBuilder builder){
		this.table=builder.table;
		this.chair=builder.chair;
		this.students=builder.students;
		this.teacher=builder.teacher;
	}
}
```

###3、私有构造器的使用
* 通过私有构造器让类无法被外部实例化
* 单例模式下的类构造器使用私有构造，可以保证不会被外部实例化
* 静态工具类和单例模式不适合需要引用底层资源的类，资源使用时使用try-with-resources的方式

```java
1、这里需要注意，内部的resources必须实现或者扩展了AutoCloseable接口，单资源
try(BufferedReader bf=new BufferedReader(new FileReader(path))){
}

2、多资源
try(BufferedReader bf=new BufferedReader(new FileReader(path));
		OutputStream out=new FileOutputStream(src)){
}

```

###4、不要使用finalize方法
* finalize方法的执行时间不确定，不知道什么时候执行，会造成很大的性能损失
* finalize阻止了正常的垃圾回收，将垃圾回收时间拖长，因为垃圾回收的时候会把重写了finalize的对象放到一个finalize的回收队列，尝试执行自定义的finalize方法，这个执行的契机是不确定的

###5、所有对象通用的方法
（1）覆盖equals方法

* 需要满足自反性
* 对称性
* 传递性

（2）覆盖equals方法的同时必须覆盖hashcode方法，否则该类无法结合所有基于散列的集合

* 覆盖equals必须覆盖hashcode
* 相等的对象必须有相同的hashcode值

（3）覆盖toString方法

（4）谨慎覆盖clone方法