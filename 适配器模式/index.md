# 适配器模式

## 意图Intent

Adapter Pattern:Convert the inface of a class into another interface clients expect.Adapter lets classes work together that couldn’t otherwise because of incompatible interface. 适配器模式:将一个类的接口变换成客户端所期待的另一种接口，从而使原本因接口不匹配而无法在一起工作的两个类能够在一起工作。

其别名为包装器(Wrapper)，适配器模式既可以作为类结构型模式，也可以作为对象结构型模式。

****

##适用场景

在以下情况下可以使用适配器模式：

* 系统需要使用现有的类，而这些类的接口不符合系统的需要。
* 想要建立一个可以重复使用的类，用于与一些彼此之间没有太大关联的一些类，包括一些可能在将来引进的类一起工作。

****
## 模式结构

适配器模式包含如下角色：

* Target：目标抽象类
* Adapter：适配器类
* Adaptee：适配者类
* Client：客户类

****
## Show me the code

我们知道我们国家交流电电压是220V，而美国日本则是110V，如果有天你带着你的电脑去美国出差是无法直接使用电源的，这时候就需要适配器。 在中国的时候使用的是220V的电源供电：

```
    public interface V220Power {
    	// 220V电源供电
    	public void powerByV220();
    }
```

以及我们的笔记本类:

```
    public class MacbookPro {
    	// 只接受V220Power的电源
    	public void charge(V220Power power){
    		power.powerByV220();
    		System.out.println("正常充电中...");
    	}
    }
```

但如果我们出差到了美国，这边只有110V的电源。
```
    public class V110Power {
    	// 110V电源供电
    	public void powerByV110() {
    		System.out.println("powerByV110");
    	}
    }
```
很显然现在我们无法直接使用110V的电源，因为我们的Macbook只能使用220V的电源。
```
    MacbookPro macbook = new MacbookPro();
    macbook.charge(??) // 这里没有220V的电源。
```
这时候我们就需要适配器了，
```
    public class PowerAdapter implements V220Power {
    	private V110Power v110Power;
    	public PowerAdapter(V110Power v110Power){
    		this.v110Power = v110Power;
    	}
    	@Override
    	public void powerByV220() {
    		System.out.println("将110V转化为220V");
    		v110Power.powerByV110();
    	}
    }
```
我们的PowerAdapter也提供220V的电源，但实际上我们powerByV220中调用的仍是V110Power提供的电源。只是在使用之前进行了转化。 现在我们的电脑就可以正常使用电源了。
```
    MacbookPro macbook = new MacbookPro();
    V110Power v110Power = new V110Power();
    V220Power power = new PowerAdapter(v110Power); // 通过PowerAdapter使用110V电源充电
    macbook.charge(power);
```
****

## 模式应用举例

在Android开发中ListView中的Adapter模式，因为不同的界面有不同的数据展示方式，所以不同的Listview所要呈现的视图也是不同的，为了应对这种变化，就通过一个适配器来将这种变化和我们的ListView实现一个隔离和适配。我们可以通过一个继承自BaseAdapter类来实现自己的适配器，来将我们对ListView中的每一个item视图的配置。
****

## 优点

* 将目标类和适配者类解耦，通过引入一个适配器类来重用现有的适配者类，而无须修改原有代码。
* 增加了类的透明性和复用性，将具体的实现封装在适配者类中，对于客户端类来说是透明的，而且提高了适配者的复用性。
* 灵活性和扩展性都非常好，通过使用配置文件，可以很方便地更换适配器，也可以在不修改原有代码的基础上增加新的适配器类，完全符合“开闭原则”。

****

## 缺点

* 类适配器模式的缺点如下： 对于Java、C#等不支持多重继承的语言，一次最多只能适配一个适配者类，而且目标抽象类只能为抽象类，不能为具体类，其使用有一定的局限性，不能将一个适配者类和它的子类都适配到目标接口。

* 对象适配器模式的缺点如下： 与类适配器模式相比，要想置换适配者类的方法就不容易。如果一定要置换掉适配者类的一个或多个方法，就只好先做一个适配者类的子类，将适配者类的方法置换掉，然后再把适配者类的子类当做真正的适配者进行适配，实现过程较为复杂。
