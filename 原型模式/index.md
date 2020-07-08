# 原型模式


## 意图Intent

Prototype Pattern：Specify the kinds of objects to create using a prototypical instance,and create new objects by copying this prototype. 原型模式:用原型实例指定创建对象的种类，并且通过拷贝这些原型创建新的对象。
****
## 模式结构

原型模型涉及到三个角色： * 客户角色（client）：客户端提出创建对象的请求； * 抽象原型（prototype）：这个往往由接口或者抽象类来担任，给出具体原型类的接口； * 具体原型（Concrete prototype）： 实现抽象原型，是被复制的对象，此角色需要实现抽象的原型角色所要求的接口。
****
## 使用场景

* 当一个系统应该独立于它的产品创建，构成和表示时。
* 当要实例化的类是在运行时刻指定时，例如，通过动态装载。
* 为了避免创建一个与产品类层次平行的工厂类层次时。
* 当一个类的实例只能有几个不同状态组合中的一种时。建立相应数目的原型并克隆它们可能比每次用合适的状态手工实例化该类更方便一些。

****
## Show me the code

当我们需要创建与已有对象一样的对象时，我们通常可以有两种容易想到的方法，一种是将已有对象指向另外一个重新创建的对象，例如：
```
    	Object obj = new Object();
    	Object newObj = obj;
```
另外一种常见的做法是，重新创建一个对象，用new来实例化，这样就创建了另外一个对象，即向内存中再写入了一个对象。
```
    	Object obj = new Object();
    	Object newObj = new Object();
```
其实除了上面的两种方法，还有一种使用clone()的方法来实现。

我们知道Java的所有类都是从java.lang.Object类继承而来的,并且Object有一个原生的clone方法，但是该方法的调用必须要求类实现了Cloneable接口。
```
    		public class WuKong implements Cloneable {
    			public void killMonster(){
    				System.out.println(this+" 在打妖怪 ");
    			}
    			@Override
    			protected WuKong clone() {
    				WuKong wuKong = null;
    				try {
    					wuKong = (WuKong) super.clone();
    				} catch (CloneNotSupportedException e) {
    					e.printStackTrace();
    				}
    				return wuKong;
    			}
    		}

    	public static void main(String[] args) {
    		WuKong wuKong = new WuKong();
    		wuKong.killMonster();
    		WuKong wuKong1 = wuKong.clone();
    		wuKong1.killMonster();
    		System.out.println(wuKong == wuKong1);    // false
    		System.out.println(wuKong.getClass() == wuKong1.getClass());  // true
    	}

output：

    com.designpattern.prototype.WuKong@7852e922 在打妖怪
    com.designpattern.prototype.WuKong@4e25154f 在打妖怪
    false
    true
```
一般而言，Java 语言中的 clone() 方法满足：

    (1) 对任何对象 x，都有 x.clone() != x，即克隆对象与原型对象不是同一个对象；
    (2) 对任何对象 x，都有 x.clone().getClass() == x.getClass()，即克隆对象与原型对象的类型一样；

为了获取对象的一份拷贝，我们可以直接利用 Object 类的 clone() 方法，具体步骤如下：

    (1) 派生类实现 Cloneable 接口。
    (2) 在派生类中覆盖基类的 clone() 方法，并声明为 public；
    (3) 在派生类的 clone() 方法中，调用 super.clone()； 此时，Object 类相当于抽象原型类，所有实现了 Cloneable 接口的类相当于具体原型类。

另外需要注意的是Object类提供的方法clone只是拷贝本对象，其对象内部的数组、引用对象等都不拷贝，还是指向原生对象的内部元素地址，这种拷贝就叫做浅拷贝，与浅拷贝相反的就是深拷贝。

浅拷贝：是指拷贝引用，实际内容并没有复制，改变后者等于改变前者，换言之，所有的对其他对象的引用都仍然指向原来的对象。 深拷贝：拷贝出来的东西和被拷贝者完全独立，相互没有影响，换言之，深拷贝把要复制的对象所引用的对象都复制了一遍，而这种对被引用到的对象的复制叫做间接复制。

为了说明Object类提供的clone方法是浅拷贝，我们改造一下我们的Demo，给WuKong类添加一个 String类型 的id，不能是Int类型，因为我们知道Int是值类型，无法说明问题，而Java中String是引用类型。

```
    public class WuKong implements Cloneable {
    	private String id;
    	public WuKong(String id) {
    		this.id = id;
    	}
    	public void setId(String id){
    		this.id = id;
    	}
    	public String getId(){
    		return id;
    	}
    	public void killMonster() {
    		System.out.println(id+": "+this + " 在打妖怪 ");
    	}
    	@Override
    	protected WuKong clone() {
    		WuKong wuKong = null;
    		try {
    			wuKong = (WuKong) super.clone();
    		} catch (CloneNotSupportedException e) {
    			e.printStackTrace();
    		}
    		return wuKong;
    	}
    }
```

我在再来测试，
```
    	WuKong wuKong = new WuKong("1");
    	wuKong.killMonster();
    	WuKong wuKong1 = wuKong.clone();
    	wuKong1.killMonster();
    	System.out.println(wuKong.getId() == wuKong1.getId());  // true
```
我们发现打印的结果是true，也就是说wuKong1.getId()和wuKong1.getId()指向的是同一个地址。那么如何实现深拷贝呢？我们可以通过序列化的方式来实现深拷贝：
```
    public class WuKong implements Cloneable, Serializable {
    	  private static final long serialVersionUID = 1L;
        ***
    	  // 使用序列化技术实现深克隆
    	  public WuKong deepClone() throws IOException, ClassNotFoundException, OptionalDataException {
    		// 将对象写入流中
    		ByteArrayOutputStream bao = new ByteArrayOutputStream();
    		ObjectOutputStream oos = new ObjectOutputStream(bao);
    		oos.writeObject(this);
    		// 将对象从流中取出
    		ByteArrayInputStream bis = new ByteArrayInputStream(bao.toByteArray());
    		ObjectInputStream ois = new ObjectInputStream(bis);
    		return (WuKong) ois.readObject();
    	}
    }
```
Cloneable ,Serializable到底是什么？ Java语言提供的Cloneable接口和Serializable接口的代码非常简单，它们都是空接口，这种空接口也称为标识接口，标识接口中没有任何方法的定义，其作用是告诉JRE这些接口的实现类是否具有某个功能，如是否支持克隆、是否支持序列化等。
模式应用举例
```
Intent 类 frameworks/base/core/java/android/content/Intent.java

    public Intent(Intent o) {
    				this.mAction = o.mAction;
    				this.mData = o.mData;
    				this.mType = o.mType;
    				this.mPackage = o.mPackage;
    				this.mComponent = o.mComponent;
    				this.mFlags = o.mFlags;
    				this.mContentUserHint = o.mContentUserHint;
    				if (o.mCategories != null) {
    						this.mCategories = new ArraySet<String>(o.mCategories);
    				}
    				if (o.mExtras != null) {
    						this.mExtras = new Bundle(o.mExtras);
    				}
    				if (o.mSourceBounds != null) {
    						this.mSourceBounds = new Rect(o.mSourceBounds);
    				}
    				if (o.mSelector != null) {
    						this.mSelector = new Intent(o.mSelector);
    				}
    				if (o.mClipData != null) {
    						this.mClipData = new ClipData(o.mClipData);
    				}
    		}
    		@Override
    		public Object clone() {
    				return new Intent(this);
    		}
```
从代码中，我们可以看到，Intent类是实现Cloneable接口，在clone方法中，通过Intent的构造方法来返回Intent类型的对象。并且非常明显，Intent是拷贝。 Bundle类 frameworks/base/core/java/android/os/Bundle.java
```
    /**
     * A mapping from String values to various Parcelable types.
     *
     */
    public final class Bundle extends BaseBundle implements Cloneable, Parcelable {
       	***
    	/**
         * Constructs a Bundle containing a copy of the mappings from the given
         * Bundle.
         *
         * @param b a Bundle to be copied.
         */
        public Bundle(Bundle b) {
            super(b);
            mHasFds = b.mHasFds;
            mFdsKnown = b.mFdsKnown;
        }
        /**
         * Constructs a Bundle containing a copy of the mappings from the given
         * PersistableBundle.
         *
         * @param b a Bundle to be copied.
         */
        public Bundle(PersistableBundle b) {
            super(b);
        }
    		***
    	/**
     * Clones the current Bundle. The internal map is cloned, but the keys and
     * values to which it refers are copied by reference.
     */
    @Override
    public Object clone() {
    		return new Bundle(this);
    }
```
****
## 优点

* 当创建新的对象实例较为复杂时，使用原型模式可以简化对象的创建过程，通过复制一个已有实例可以提高新实例的创建效率。
* 扩展性较好，由于在原型模式中提供了抽象原型类，在客户端可以针对抽象原型类进行编程，而将具体原型类写在配置文件中，增加或减少产品类对原有系统都没有任何影响。
* 原型模式提供了简化的创建结构，工厂方法模式常常需要有一个与产品类等级结构相同的工厂等级结构，而原型模式就不需要这样，原型模式中产品的复制是通过封装在原型类中的克隆方法实现的，无须专门的工厂类来创建产品。
* 可以使用深克隆的方式保存对象的状态，使用原型模式将对象复制一份并将其状态保存起来，以便在需要的时候使用（如恢复到某一历史状态），可辅助实现撤销操作。

****
## 缺点

* 需要为每一个类配备一个克隆方法，而且该克隆方法位于一个类的内部，当对已有的类进行改造时，需要修改源代码，违背了“开闭原则”。
* 在实现深克隆时需要编写较为复杂的代码，而且当对象之间存在多重的嵌套引用时，为了实现深克隆，每一层对象对应的类都必须支持深克隆，实现起来可能会比较麻烦。

****
## 原型模式的注意事项

* 使用原型模式复制对象不会调用类的构造方法。因为对象的复制是通过调用Object类的clone方法来完成的，它直接在内存中复制数据，因此不会调用到类的构造方法。不但构造方法中的代码不会执行，甚至连访问权限都对原型模式无效.clone方法直接无视构造方法的权限，所以，单例模式与原型模式是冲突的。
* 在使用时要注意深拷贝与浅拷贝的问题。clone方法只会拷贝对象中的基本的数据类型，对于数组、容器对象、引用对象等都不会拷贝，这就是浅拷贝。如果要实现深拷贝，必须将原型模式中的数组、容器对象、引用对象等另行拷贝。
