# day03

## 多态

> 面向对象三大特征：封装性、继承性、多态性。`extends(class)`和`implements(interface)`实现，是多态的前提。

* 对象拥有多种形态， 一般在类中表示为：表面形态和内部形态；
* `父类名称 对象名 = new 子类名();` 或 `接口名称 对象名 = new 实现类名称();`

> 编译看左边，运行看右边。

### 多态下的成员变量和方法

* 成员变量：
  * 直接通过对象名称访问成员变量，看等号左边是谁，优先用谁，没有则向上找；
  * 简介通过成员方法访问成员变量，看该方法属于谁，优先用谁，没有则向上找。
* 成员方法：
  * 看`new`的是谁，就优先用谁，没有就向上找。

### Java多态转型

* 向上转型：子类转父类，父类引用指向子类对象。
* 向下转型：父类转子类，用对象向下转型，还原。`对象名称 对象名 = (子类名称)父类对象;`
  * 向下转型可能会出现不安全的情况，当向下转型错误时，出现，`ClassCastException`类型的错误。

## 多态案例

### Compter

```java
package com_03.jianmo.Example;

public class Computer {
	public void powerOn() {
		System.out.println("开机");
	}

	public void powerOff() {
		System.out.println("关机");
	}
	public void useDevice(USB u) {  // 参数注入
		if (u instanceof Mouse) {  // 先判断再转型调用
			((Mouse) u).click();
		} else if (u instanceof Keyboard) {  // 先判断再转型
			((Keyboard) u).typing();
		}
		u.openDevice();
		u.closeDevice();
	}
}
```

### USB接口类

```java
package com_03.jianmo.Example;

public interface USB {
	public void openDevice();
	public void closeDevice();
}
```

### 实现类：Keyboard

```java
package com_03.jianmo.Example;

public class Keyboard implements USB {
	@Override
	public void openDevice() {
		System.out.println("安装键盘驱动");
	}

	@Override
	public void closeDevice() {
		System.out.println("卸载键盘驱动");
	}

	public void typing() {
		System.out.println("打字");
	}

}
```

### 实现类：Mouse

```java
package com_03.jianmo.Example;

public class Mouse implements USB {
	@Override
	public void openDevice() {
		System.out.println("安装鼠标驱动");
	}

	@Override
	public void closeDevice() {
		System.out.println("卸载鼠标驱动");
	}

	public void click() {
		System.out.println("点击");
	}
}
```

### 测试类：ComputerTest

```java
package com_03.jianmo.Example;


public class ComputerTest {
	public static void main(String[] args) {
		Computer computer = new Computer();
		computer.powerOn();

		Mouse mouse = new Mouse();
		computer.useDevice(mouse);

		Keyboard keyboard = new Keyboard();
		// 方法参数是USB类型，传递进去的是实现类
		computer.useDevice(keyboard);  // 不是多态，但是自动类型转化可以成功
		computer.useDevice(new Keyboard());  // 匿名对象

		computer.powerOff();
	}
}
```

## final关键字

> 对于类和方法来说，`abstract`和`final`关键字不能同时使用，因为`abstract`意味着继承或重写，但`final`表示不能继承或重写。明显冲突了。

### final关键字的四种用法

1. 用来修饰一个类，当前这个类不能有任何子类；
2. 用来修饰一个方法，该方法不能被覆盖重写；
3. 用来修饰一个局部变量，这个变量就不能再进行更改了，一次赋值，终生不变；
4. 用来修饰一个成员变量，这个变量也无法改变。成员变量有默认值，用了`final`就不会有默认值了，所以用了`final`修饰必须手动赋值。可以采用直接赋值或者在构造函数(所有重载形式中都需要赋值)中赋值，且只能一次。

> 对于一个基本类型而言，不可变指的是变量的数据不能改变。
>
> 对于一个引用类型而言，不可变指的是引用指针的指向不能改变。

## 权限修饰符

| 不同位置\\修饰符(权限大到小) | public | protected | (default) | private |
| :--------------------------: | :----: | :-------: | :-------: | :-----: |
|           同一个类           |  YES   |    YES    |    YES    |   YES   |
|           同一个包           |  YES   |    YES    |    YES    |   NO    |
|         不同包的子类         |  YES   |    YES    |    NO     |   NO    |
|         不同包非子类         |  YES   |    NO     |    NO     |   NO    |

## 内部类

> 一个实务的内部包含另一个事务，那么这就是一个类内部包含另一个类。

```java
修饰符 class 类名称 {
    修饰符 class 类名称 {  // 这个类就是内部类
        // ...
    }
    // ...
}
```

> **内用外，随意用，外用内，需要内部对象。**

### 分类

* 成员内部类
* 局部内部类(包含匿名内部类)

### 成员内部类

* 间接使用：在外部类的方法中，使用内部类，然后`main`只是调用外部类的方法；
* 直接使用：公式`外部类名称.内部类名称 = new 外部类名称().new 内部类名称();`。
* 成员变量冲突

```java
public class Outer {
	int num = 10;  // 外部类成员变量
	public class Inner{
		int num = 20;  // 内部类成员变量
		public void methodInner() {
			int num = 30;  // 内部类方法的局部变量
			System.out.println("num = " + num);  // 局部变量:30
			System.out.println("num = " + this.num);  // 内部类变量:20
			System.out.println("num = " + Outer.this.num);  // 外部类变量:10
		}
	}
}
```

> `new Outer().new Inner().methodInner();`，匿名变量进行测试调用。

### 局部内部类

> 如果一个类是定义在一个方法内部的，那么这就是一个局部内部类。`“局部”`：即只有当前所属的方法才能使用它，出了这个方法就不能用来。

```java
修饰符 class 外部类名称 {
    修饰符 返回值类型 外部类方法名称(参数列表) {
        class 局部内部类名称 {
            // ...
        }
    }
    // ...
}
```

> 局部内部类，如果希望访问所在方法的局部变量，那么这个局部变量必须是`有效final的`。

### 匿名内部类

> 如果接口的实现类或者父类的子类，只需要使用一次。那么这种情况就可以省略掉该类的定义，而使用`匿名内部类`。

```java
接口名称 对象名 = new 接口名称() {
    // 重写所有抽象方法
}
```

> `new 接口名称() {...}`

* `new`代表创建对象的动作；
* `接口名称`就是匿名内部类所需要实现那个接口；
* `{...}`才是匿名内部类的内容。

#### 匿名内部类注意事项

1. 匿名内部类，在`创建对象`的时候只能使用唯一一次，如果希望多次创建对象，而且类的内容一样的时候，那么就必须使用单独定义的实现类了；
2. 匿名对象，在`调用方法`的时候，只能调用唯一一次，如果希望同一个对象，调用多次方法，那么必须给对象起个名字；
3. 匿名内部类是省略了`实现类/子类`，但是匿名对象是省略了`对象名称`，`匿名对象`和`匿名内部类`不是一回事。

## 类修饰符

> `public` > `protected` > `(default)` > `private`

定义一个类的时候，权限修饰符规则：

1. 外部类：`public`/`(default)`；
2. 成员内部类：`public`/`protected`/`(default)`/`private`；
3. 局部内部类：什么都不能写。