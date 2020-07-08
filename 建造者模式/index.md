# 建造者模式


## 意图Intent

Builder Pattern: Separate the construction of a complex object form its representation so that the same construction process can create different representations 造者模式:将一个复杂对象的构建与它的表示分离，使得同样的构建过程可以创建不同的表示。

建造者模式是一步一步创建一个复杂的对象，它允许用户只通过指定复杂对象的类型和内容就可以构建它们，用户不需要知道内部的具体构建细节。建造者模式属于对象创建型模式。根据中文翻译的不同，建造者模式又可以称为生成器模式。

****
## 适用场景

在以下情况下可以使用建造者模式：

* 需要生成的产品对象有复杂的内部结构，这些产品对象通常包含多个成员属性。
* 需要生成的产品对象的属性相互依赖，需要指定其生成顺序。
* 对象的创建过程独立于创建该对象的类。在建造者模式中引入了指挥者类，将创建过程封装在指挥者类中，而不在建造者类中。
* 隔离复杂对象的创建和使用，并使得相同的创建过程可以创建不同的产品。

****
## 四个要素

* 抽象建造者(Builder)：引入抽象建造者的目的，是为了将建造的具体过程交与它的子类来实现。这样更容易扩展。一般至少会有两个抽象方法，一个用来建造产品，一个是用来返回产品。
* 具体建造者(ConcreteBuilder)：实现抽象类的所有未实现的方法，具体来说一般是两项任务：组建产品；返回组建好的产品。
* 导演类(Director)：负责调用适当的建造者来组建产品，导演类一般不与产品类发生依赖关系，与导演类直接交互的是建造者类。一般来说，导演类被用来封装程序中易变的部分。
* 产品类(Product)：一般是一个较为复杂的对象，也就是说创建对象的过程比较复杂，一般会有比较多的代码量。在本类图中，产品类是一个具体的类，而非抽象类。实际编程中，产品类可以是由一个抽象类与它的不同实现组成，也可以是由多个抽象类与他们的实现组成。

Builder Pattern 类图Builder Pattern 类图

****
## Show me the code
```
    class Product {
            private String name;
            private String type;
            public void showProduct(){
                System.out.println("名称："+name);
                System.out.println("型号："+type);
            }
            public void setName(String name) {
                this.name = name;
            }
            public void setType(String type) {
                this.type = type;
            }
        }
        abstract class Builder {
            public abstract void setPart(String arg1, String arg2);
            public abstract Product getProduct();
        }
        class ConcreteBuilder extends Builder {
            private Product product = new Product();
            public Product getProduct() {
                return product;
            }
            public void setPart(String name, String type) {
                product.setName(name);
                product.setType(type);
            }
        }
        public class Director {
            private Builder builder = new ConcreteBuilder();
            public Product getAProduct(){
                builder.setPart("宝马汽车","X7");
                return builder.getProduct();
            }
            public Product getBProduct(){
                builder.setPart("奥迪汽车","Q5");
                return builder.getProduct();
            }
        }
        public class Client {
            public static void main(String[] args){
                Director director = new Director();
                Product product1 = director.getAProduct();
                product1.showProduct();
                Product product2 = director.getBProduct();
                product2.showProduct();
            }
        }
```
看了上面的例子，会感觉建造者模式与工厂模式是极为相似的，总体上，建造者模式仅仅只比工厂模式多了一个”导演类”的角色。在建造者模式的类图中，假如把这个导演类看做是最终调用的客户端，那么图中剩余的部分就可以看作是一个简单的工厂模式了。 工厂模式的区别：工厂模式关注的是创建单个产品，而建造者模式则关注创建复合对象，多个部分。 上面的例子是比较完整的Builder模式的例子，我们再来举一个简单的，也是比较常用的例子。现在手机都可以自己组装了，我们知道每个手机都会有OS，CPU，GPU….等模块，但是不同的手机采用的CPU，GPU都不一样。
```
    package com.builder;
    /**
     *
     * Phone, the class with many parameters.
     *
     */
    public class Phone {
    	private String OS;
    	private String CPU;
    	private String GPU;
    	private String RAM;
    	private String FLASH;
    	private String BATTERY;
    	private String USB;
    	private String DISPLAY;
    	private String CAMERA;
    	private String GPS;
    	public Phone(PhoneBuilder builder) {
    		this.OS = builder.OS;
    		this.CPU = builder.CPU;
    		this.GPU = builder.GPU;
    		this.RAM = builder.RAM;
    		this.FLASH = builder.FLASH;
    		this.BATTERY = builder.BATTERY;
    		this.USB = builder.USB;
    		this.DISPLAY = builder.DISPLAY;
    		this.CAMERA = builder.CAMERA;
    		this.GPS = builder.GPS;
    	}
    	@Override
    	public String toString() {
    		return "Phone [OS=" + OS + ", CPU=" + CPU + ", GPU=" + GPU + ", RAM=" + RAM + ", FLASH=" + FLASH + ", BATTERY="
    				+ BATTERY + ", USB=" + USB + ", DISPLAY=" + DISPLAY + ", CAMERA=" + CAMERA + ", GPS=" + GPS + "]";
    	}
      // 手机Builder类
    	public static class PhoneBuilder {
    		private String OS;
    		private String CPU;
    		private String GPU;
    		private String RAM;
    		private String FLASH;
    		private String BATTERY;
    		private String USB;
    		private String DISPLAY;
    		private String CAMERA;
    		private String GPS;
    		public PhoneBuilder withOS(String os){
    			this.OS = os;
    			return this;
    		}
    		public PhoneBuilder withCPU(String cpu){
    			this.CPU = cpu;
    			return this;
    		}
    		public PhoneBuilder withGPU(String gpu){
    			this.GPU = gpu;
    			return this;
    		}
    		public PhoneBuilder withRAM(String ram){
    			this.RAM = ram;
    			return this;
    		}
    		public PhoneBuilder withFLASH(String flash){
    			this.FLASH = flash;
    			return this;
    		}
    		public PhoneBuilder withBATTERY(String battery){
    			this.BATTERY = battery;
    			return this;
    		}
    		public PhoneBuilder withUSB(String usb){
    			this.USB = usb;
    			return this;
    		}
    		public PhoneBuilder withDISPLAY(String display){
    			this.DISPLAY = display;
    			return this;
    		}
    		public PhoneBuilder withCAMERA(String camera){
    			this.CAMERA = camera;
    			return this;
    		}
    		public PhoneBuilder withGPS(String gps){
    			this.GPS = gps;
    			return this;
    		}
    		public Phone build() {
    			return new Phone(this);
    		}
    	}
    }
```
然后我们组合不同的模块和具体的参数，就可以生产不同的手机：
```
    public static void main(String[] args) {
    		Phone phone = new Phone.PhoneBuilder()
    		.withOS("myos")
    		.withBATTERY("mybattery")
    		.withCAMERA("mycamera")
    		.withCPU("mycpu")
    		.withDISPLAY("mydisplay")
    		.withFLASH("myflash")
    		.build();
    		System.out.println(phone.toString());
    	}

output：

    Phone [OS=myos, CPU=mycpu, GPU=null, RAM=null, FLASH=myflash, BATTERY=mybattery, USB=null, DISPLAY=mydisplay, CAMERA=mycamera, GPS=null]
```
模式应用举例

在Android源码中，我们最常用到的Builder模式就是android/platform/frameworks/base/core/java/android/app/AlertDialog.java， 使用该Builder来构建复杂的AlertDialog对象.
```
     public static class Builder {
            private final AlertController.AlertParams P;
            public Builder(Context context) {
                this(context, resolveDialogTheme(context, 0));
            }
           ...
            public Builder setTitle(@StringRes int titleId) {
                P.mTitle = P.mContext.getText(titleId);
                return this;
            }
            public Builder setTitle(CharSequence title) {
                P.mTitle = title;
                return this;
            }
              public Builder setCustomTitle(View customTitleView) {
                P.mCustomTitleView = customTitleView;
                return this;
            }
            public Builder setMessage(@StringRes int messageId) {
                P.mMessage = P.mContext.getText(messageId);
                return this;
            }
            ...
            public AlertDialog create() {
                // Context has already been wrapped with the appropriate theme.
                final AlertDialog dialog = new AlertDialog(P.mContext, 0, false);
                P.apply(dialog.mAlert);
                dialog.setCancelable(P.mCancelable);
                if (P.mCancelable) {
                    dialog.setCanceledOnTouchOutside(true);
                }
                dialog.setOnCancelListener(P.mOnCancelListener);
                dialog.setOnDismissListener(P.mOnDismissListener);
                if (P.mOnKeyListener != null) {
                    dialog.setOnKeyListener(P.mOnKeyListener);
                }
                return dialog;
            }
```
我们在AlertDialog创建时的示例如下：
```
     AlertDialog.Builder builder = new AlertDialog.Builder(context);  
            builder.setIcon(R.drawable.icon);  
            builder.setTitle("Title");  
            builder.setMessage("Message");  
            builder.setPositiveButton("Ok",  
                    new DialogInterface.OnClickListener() {  
                        public void onClick(DialogInterface dialog, int whichButton) {  
                            setTitle("Hi");  
                        }  
                    });
            builder.create().show();  // 构建AlertDialog， 并且显示
```
还有我们熟悉的android/platform/frameworks/base/core/java/android/app/Notification.java同样也使用了Builder模式。
```
    Notification noti = new Notification.Builder(mContext)
                  .setContentTitle("Title")
                  .setContentText(subject)
                  .setSmallIcon(R.drawable.new_mail)
                  .setLargeIcon(aBitmap)
                  .build();
```
另外一些有名的开源库中比如图片加载库Picasso中也使用了Builder模式。有兴趣的可以自行查看com/squareup/picasso/Picasso.java源码。
****
## 优点

* 在建造者模式中， 客户端不必知道产品内部组成的细节，将产品本身与产品的创建过程解耦，使得相同的创建过程可以创建不同的产品对象。
* 每一个具体建造者都相对独立，而与其他的具体建造者无关，因此可以很方便地替换具体建造者或增加新的具体建造者， 用户使用不同的具体建造者即可得到不同的产品对象 。
* 可以更加精细地控制产品的创建过程 。将复杂产品的创建步骤分解在不同的方法中，使得创建过程更加清晰，也更方便使用程序来控制创建过程。
* 增加新的具体建造者无须修改原有类库的代码，指挥者类针对抽象建造者类编程，系统扩展方便，符合“开闭原则”。

****
## 缺点

* 建造者模式所创建的产品一般具有较多的共同点，其组成部分相似，如果产品之间的差异性很大，则不适合使用建造者模式，因此其使用范围受到一定的限制。
* 如果产品的内部变化复杂，可能会导致需要定义很多具体建造者类来实现这种变化，导致系统变得很庞大。

****
## 总结

建造者模式将一个复杂对象的构建与它的表示分离，使得同样的构建过程可以创建不同的表示。建造者模式是一步一步创建一个复杂的对象，它允许用户只通过指定复杂对象的类型和内容就可以构建它们，用户不需要知道内部的具体构建细节。建造者模式属于对象创建型模式。
