# Django模型

## 简介

模型是有关数据的唯一确定的信息源。它包含要存储数据的基本字段和行为。通常，每个模型都映射到单个数据库表。

- 每一个模型是django.db.models.Model的子类
- 每一个模型属性代表数据表的一个字段。
- Django提供了自动生成的数据库访问API，使用模型操作数据库很方便

## 快速示例

此示例模型定义了一个`Person`，其中包含`first_name`和 `last_name`：

```
from django.db import models

class Person(models.Model):
    first_name = models.CharField(max_length=30)
    last_name = models.CharField(max_length=30)
```

##### first_name并且last_name是场模型。每个字段都指定为类属性，并且每个属性都映射到数据库列。上面的Person模型将创建一个数据库表，如下所示：`CREATE TABLE myapp_person (    "id" serial NOT NULL PRIMARY KEY,    "first_name" varchar(30) NOT NULL,    "last_name" varchar(30) NOT NULL );`

##### 一些技术说明：

- 表的名称myapp_person是自动从某些模型元数据派生而来的，但是可以被覆盖。
- 一个id字段被自动添加，但这种行为可以被覆盖。
- 在此示例中，SQL是使用PostgreSQL语法格式化的，但是值得注意的是Django使用了针对设置文件中指定的数据库后端定制的SQL 。CREATE TABLE

详情参考官网： https://docs.djangoproject.com/en/3.0/topics/db/models/

## 定义模型

Django根据属性的类型确定以下信息：

- 当前选择的数据库支持字段的类型
- 渲染管理表单时使用的默认html控件
- 在管理站点最低限度的验证

django会为表创建自动增长的主键列，每个模型只能有一个主键列，如果使用选项设置某属性为主键列后django不会再创建自动增长的主键列。

默认创建的主键列属性为id，可以使用pk代替，pk全拼为primary key。

pk是主键的别名，若主键名为id2，那么pk是id2的别名。

属性命名限制：

- 不能是python的保留关键字。
- 不允许使用连续的下划线，这是由django的查询方式决定的，在第4节会详细讲解查询。
- 定义属性时需要指定字段类型，通过字段类型的参数指定选项。

​    具体语法如下：

​    **属性=models.字段类型(选项)**

## 模型字段类表

| 字段类          | 默认小组件         | 说明                                                         |
| --------------- | ------------------ | ------------------------------------------------------------ |
| AutoField       | N/A                | 根据 ID 自动递增的 IntegerField                              |
| BigIntegerField | NumberInput        | 64 位整数，与 IntegerField 很像，但取值范围是 -9223372036854775808 到 9223372036854775807 。 |
| BinaryField     | N/A                | 存储原始二进制数据的字段。只支持 bytes 类型。注意，这个字段的功能有限。 |
| BooleanField    | CheckboxInput      | 真假值字段。如果想接受 null 值，使用 NullBooleanField 。     |
| CharField       | TextInput          | 字符串字段，针对长度较小的字符串。大量文本应该使用 TextField 。有个额外的必须参数：max_length ，即字段的最大长度（字符个数）。 |
| DateField       | DateInput          | 日期，在 Python 中使用 datetime.date 实例表示。有两个额外的可选参数： auto_now ，每次保存对象时自动设为当前日期 auto_now_add ，创建对象时自动设为当前日期。 |
| DateTimeField   | DateTimeInput      | 日期和时间，在 Python 中使用 datetime.datetime 实例表示。与 DateField 具有相同的额外参数。 |
| DecimalField    | TextInput          | 固定精度的小数，在 Python 中使用 Decimal 实例表示。有两个必须的参数： max_digits 和 decimal_places 。 |
| DurationField   | TextInput          | 存储时间跨度，在 Python 中使用 timedelta 表示。              |
| EmailField      | TextInput          | 一种 CharField ，使用 EmailValidator 验证输入。max_length 的默认值为 254 。 |
| FileField       | ClearableFileInput | 文件上传字段。详情见下面。                                   |
| FilePathField   | Select             | 一种 CharField ，限定只能在文件系统中的特定目录里选择文件。  |
| FloatField      | NumberInput        | 浮点数，在 Python 中使用 float 实例表示。注意， field.localize 的值为 False 时，默认的小组件是 TextInput 。 |
| ImageField      | ClearableFileInput | 所有属性和方法都继承自 FileField ，此外验证上传的对象是不是有效的图像。增加了 height 和 width 两个属性。需要 Pillow 库支持。 |

## 字段选项

- 通过字段选项，可以实现对字段的约束
- 在字段对象时通过关键字参数指定
- null：如果为True，Django 将空值以NULL 存储到数据库中，默认值是 False
- blank：如果为True，则该字段允许为空白，默认值是 False
- 对比：null是数据库范畴的概念，blank是表单验证证范畴的
- db_column：字段的名称，如果未指定，则使用属性的名称
- db_index：若值为 True, 则在表中会为此字段创建索引
- default：默认值
- primary_key：若为 True, 则该字段会成为模型的主键字段
- unique：如果为 True, 这个字段在表中必须有唯一值

## 关联关系

### 多对一 (ForeignKey)

Django提供了定义了几种最常见的数据库关联关系的方法：多对一，多对多，一对一。

**多对一**关系，需要两个位置参数，一个是关联的模型，另一个是 `on_delete` 选项，外键要定义在多的一方，如一个汽车厂生产多种汽车，一辆汽车只有一个生产厂家

```
from django.db import models
class Manufacturer(models.Model):
    # ...
    pass
class Car(models.Model):
    manufacturer = models.ForeignKey(Manufacturer, on_delete=models.CASCADE)
```

## **多对多**关系的额外字段：throuth

例如：这样一个应用，它记录音乐家所属的音乐小组。 我们可以用一个ManyToManyField 表示小组和成员之间的多对多关系。 但是，有时你可能想知道更多成员关系的细节，比如成员是何时加入小组的。

对于这些情况，Django 允许你指定一个中介模型来定义多对多关系。 你可以将其他字段放在中介模型里面。 源模型的ManyToManyField 字段将使用through 参数指向中介模型。 对于上面的音乐小组的例子，代码如下：

```
from django.db import models

class Person(models.Model):
    name = models.CharField(max_length=128)

    def __str__(self):              # __unicode__ on Python 2
        return self.name

class Group(models.Model):
    name = models.CharField(max_length=128)
    members = models.ManyToManyField(Person, through='Membership')

    def __str__(self):              # __unicode__ on Python 2
        return self.name

class Membership(models.Model):
    person = models.ForeignKey(Person, on_delete=models.CASCADE)
    group = models.ForeignKey(Group, on_delete=models.CASCADE)
    date_joined = models.DateField()
    invite_reason = models.CharField(max_length=64)
```

## 

##### 您需要在设置中间模型的时候，显式地为多对多关系中涉及的中间模型指定外键。这种显式声明定义了这两个模型之间是如何关联的。在中间模型当中有一些限制条件：你的模型中间要么有且仅有一个指向源模型（我们例子当中的Group）的外键，你要么必须通过ManyToManyField.through_fields参数在多个外键当中手动选择一个外键，有如果外个多键盘没有用through_fields 参数选择一个的话，会出现验证错误。对于某个目标模型（我们示例当中的Person）的外键也有同样的限制。在一个有用的描述模型当中自己指向自己的多对多关系的中间模型当中，可以有两个指向同一个模型的外健，但这两个外健分表代表多对多关系（不同）的两端。如果外健的个数超过两个，你必须和上面一样的指定through_fields参数，要不然会出现验证错误。现在你已经通过中间模型完成你的ManyToManyField（示例中的Membership），可以开始创建一些多对多关系了。你通过实例化中间模型来创建关系：`>>> paul = Person.objects.create(name="Paul McCartney") >>> beatles = Group.objects.create(name="The Beatles") >>> m1 = Membership(person=ringo, group=beatles, ...     date_joined=date(1962, 8, 16), ...     invite_reason="Needed a new drummer.") >>> m1.save() >>> beatles.members.all() <QuerySet [<Person: Ringo Starr>]> >>> ringo.group_set.all() <QuerySet [<Group: The Beatles>]> >>> m2 = Membership.objects.create(person=paul, group=beatles, ...     date_joined=date(1960, 8, 1), ...     invite_reason="Wanted to form a band.") >>> beatles.members.all() <QuerySet [<Person: Ringo Starr>, <Person: Paul McCartney>]>`

##### 你也可以使用`add()`、、`create()`或`set()`创建关系，只要你为任何必需的细分指定 `through_defaults`：

```
>>> beatles.members.add(john, through_defaults={'date_joined': date(1960, 8, 1)})
>>> beatles.members.create(name="George Harrison", through_defaults={'date_joined': date(1960, 8, 1)})
>>> beatles.members.set([john, paul, ringo, george], through_defaults={'date_joined': date(1960, 8, 1)})
```

你可能更潜在直接创造中间模型。

如果自定义中间模型没有强制对的唯一性，调用方法会删除所有中间模型的实例：(model1, model2)remove()

```
>>> Membership.objects.create(person=ringo, group=beatles,
...     date_joined=date(1968, 9, 4),
...     invite_reason="You've been gone for a month and we miss you.")
>>> beatles.members.all()
<QuerySet [<Person: Ringo Starr>, <Person: Paul McCartney>, <Person: Ringo Starr>]>
>>> # This deletes both of the intermediate model instances for Ringo Starr
>>> beatles.members.remove(ringo)
>>> beatles.members.all()
<QuerySet [<Person: Paul McCartney>]>
```

方法[`clear()`](https://docs.djangoproject.com/zh-hans/3.0/ref/models/relations/#django.db.models.fields.related.RelatedManager.clear)用于实例的所有多对多关系：

```
>>> # Beatles have broken up
>>> beatles.members.clear()
>>> # Note that this deletes the intermediate model instances
>>> Membership.objects.all()
<QuerySet []>
```

一旦你建立了自定义多对多关联关系，就可以执行查询操作。和一般的多对多关联关系一样，你可以使用多对多关联模型的属性来查询：

```
# Find all the groups with a member whose name starts with 'Paul'
>>> Group.objects.filter(members__name__startswith='Paul')
<QuerySet [<Group: The Beatles>]>
```

当你使用中间模型的时候，你也可以查询他的属性：

```
# Find all the members of the Beatles that joined after 1 Jan 1961
>>> Person.objects.filter(
...     group__name='The Beatles',
...     membership__date_joined__gt=date(1961,1,1))
<QuerySet [<Person: Ringo Starr]>
```

如果你想访问一个关系的信息时你可以直接查询Membership模型：

```
>>> ringos_membership = Membership.objects.get(group=beatles, person=ringo)
>>> ringos_membership.date_joined
datetime.date(1962, 8, 16)
>>> ringos_membership.invite_reason
'Needed a new drummer.'
```

另一种访问同样信息的方法是通过Person对象来查询**多对多递归关系**：

```
>>> ringos_membership = ringo.membership_set.get(group=beatles)
>>> ringos_membership.date_joined
datetime.date(1962, 8, 16)
>>> ringos_membership.invite_reason
'Needed a new drummer.'
```

### 一对一关联

使用OneToOneField来定义一对一关系。就像使用其他类型的Field一样：在模型属性中包含它。

当一个对象以某种方式“继承”另一个对象时，这那个对象的主键非常有用。

OneToOneField需要一个位置参数：与模型相关的类。

例如，当你要建立一个有关“位置”信息的数据库时，你可能会包含通常的地址，电话等分支。然后，如果你想接着建立一个关于关于餐厅的数据库，除了将位置数据库当中的一部分复制到Restaurant模型，你也可以将一个指向Place  OneToOneField放到Restaurant当中（因为餐厅“是一个”地点）；事实上，在处理这样的情况时最好使用模型继承，它隐含的包括了一个一对一关系。

和 ForeignKey一样，可以创建自关联关系也可以创建与尚未定义的模型的关系。

OneToOneField初步还接受一个可选的parent_link参数。

OneToOneField类通常自动的成为模型的主键，这条规则现在不再使用了（而你可以手动指定primary_key参数）。因此，现在可以在其中的模型当中指定多个OneToOneField分段。
