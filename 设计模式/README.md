# 设计模式

## 一.单例模式

### 1.初级模式_1饿汉式

- 操作流程：定义private 无参构造函数，防止外部可以进行实例化，内部定义静态变量，进行实例化类，可以在类加载的时候进行实例化，由虚拟机进行单例控制，一个类只会加载一次。简单方便
- 缺点：未使用类时，就已经进行了初始化，但是简单易用，还是不错的
- 代码 : 

```java
public class Singleton01 {
	private static Singleton01 singleton01 = new Singleton01();

	private Singleton01() {
	}

	public static Singleton01 getInstance() {
		return singleton01;
	}
}
```



### 2.初级模式_2懒汉式

- 缺点：存在线程安全问题
- 代码 : 

```java
public class Singleton02 {
    
    private static Singleton02 instance;

    private Singleton02() {
    }

    public static Singleton02 getInstance() {
        if (instance == null) {
            instance = new Singleton02();
        }
        return instance;
    }
    
}
```

### 3.初级模式_3

- 操作流程：定义静态内部类，和模式_1类似，可以解决__1的缺点,静态内部类不会随着外部类一起进行加载，只会在外部类调用内部类的时候进行加载。推荐使用
- 代码 : 

```java
public class Singleton03 {
	private static class Singleton03Factory {
		private final static Singleton03 singleton03 = new Singleton03();
	}

	public static Singleton03 getInstance() {
		return Singleton03Factory.singleton03;
	}
}
```

### 4.中级模式_饿汉式升级版

- 操作流程：执行同步代码块，进行双重检查，防止A线程第一次判断实例为空，此时B线程进行访问，拿到锁，进行实例化，释放锁，最后A线程又拿到锁，重复进行实例化的问题。加volatile 可以防止指令重排。
- 代码 : 

```java
public class Singleton04 {
	private static volatile Singleton04 singleton04;

	private Singleton04() {
	}

	public static Singleton04 getInstance() {
		if (singleton04 == null) {
			synchronized (Singleton04.class) {
				// **双重判断
				if (singleton04 == null) {
					singleton04 = new Singleton03();
				}
			}
		}
		return singleton04;
	}
}
```

### 5.高级模式

- 操作流程：定义枚举类型，只定义一个INSTANCE枚举项。jvm会控制单例，也可以访问枚举内部的方法。枚举因为没有构造方法，可以防止反序列化。

- 代码：

  ```java
  public enum Singleton04 {
  	INSTANCE;
  
  	public void test() {
  		System.out.println("实例化后可执行方法");
  	}
  
  	public static void main(String[] args) {
  		for (int i = 0; i < 100; i++) {
  			new Thread(() -> {
  				Singleton04 singleton04 = Singleton04.INSTANCE;
  				System.out.println(Singleton04.INSTANCE.hashCode());
  				singleton04.test();
  			}).start();
  		}
  	}
  }
  ```

## 二.策略模式

