## 控制反转（IOC）

### 背景
在日常开发过程中，对象之间的耦合关系是无法避免的，也是对象与对象之间关系处理的一种必然性，但是在系统规模越来越大，或者参与的人越来越多，对象之间的依赖关系也越来越复杂，也经常会出现对象之间的多重依赖关系，而我们在对其修改时，也必然面临对象依赖关系的处理，必然会出现牵一发而对全身的情形。

也是为了解决对象间的高耦合的问题，也才有了之后“解耦”的理论。

控制反转是Martin Fowler（就是《重构 —— 改善既有代码的设计》的作者） 在2004年提出了，它总结出是依赖对象的获得被反转了，如果这个获取过程要靠自身实现，那么这将导致代码高度耦合并且难以维护和调度。

### 什么是控制反转
控制反转（Inversion of Control）是一种面向对象编程中的设计原则，借助于“第三方容器”实现具有强依赖关系或耦合度过高的对象之间的解耦。

控制反转的实现方式有依赖注入（Denpendency Injection）和依赖查找（Denpendency Lookup）两种方式。

#### 什么是依赖注入
依赖注入（Dependency Injection，简称DI）是实现IOC的一种最常见的方式，通过控制反转，对象在被创建的时候，由一个调控系统内所有对象的外界实体，将其所依赖的对象的引用传递给它，可以说是被注入到对象中了。

#### 什么是依赖查找
主要是通过调用框架提供方法来获取对象，获取时需要提供相关的配置文件路径、key等信息来确定获取对象的状态。

#### 区别
另一种方式叫做依赖查找（Dependency Lookup）。它跟DI的区别是， DI是被的接收对象，在类的实例创建过程中即创建了依赖B的对象，通过类型或者名称来判断将不同的对象注入到不同的属性中，而依赖查找则是主动索取相应类型的对象，获取依赖对象的时候也可以在代码中自由控制。


### 举例说明

![](./resource/example.jpeg)

我们先看一段代码

```
class Water {
    public function drink () {
        echo "I'm dranking water";·
    }

}

class Sprite {
    public function drink () {
        echo "I'm dranking sprite";·
    }

}


class Drink {
    private $d;
    public function __construct () {
        $this->d = new Water();·
    }

    public function doing () {
        echo $this->d->drink();·
    }
}

$d = new Drink();

$d->doing();

```

简单分析下，我们会发现类`Drink`强依赖类`Water`，也就是说类`Water`硬编码到类`Drink`中，比较某天，不想喝水了，想喝雪碧，就需要把`Drink`中的`Water`实例强制换成类 `Sprite`，哪天我又想喝水了， 又要再切换回去，累么？累。如果N个人各有不同的喝的需求，又该如何？

所以，我们需要类`Drink`不依赖他们任何一种饮品，需要反过来在我们需要喝的时候，注入到`Drink`中。看下面修改后的事例。

```
Interface Liquid {
    public function drink();
}

class Water implements Liquid {
    public function drink () {
        echo "I'm drinking water";·
    }
}


class Sprint implements Liquid {
    public function drink () {
        echo "I'm drinking sprint";·
    }
}


class Drink {
    private $d;

    public function __construct (Liquid $liquid) {
        $this->d = $liquid;·
    }

    public function doing () {
        $this->d->drink();·
    }
}


$water = new Water();

$d = new Drink($water);
echo $d->doing();

$d1 = new Drink(new Sprint());

echo $d1->doing();


```

这样， 我们在有不同的需求时，也不用频繁的去修改`Drink`的强依赖了，同时也满足了多种不同的需求。下次我想喝可乐了，我只要把可乐的实例注入到`Drink`中即可了。


### 总结

控制反转可以说是一种解耦合的思想，调用类只依赖接口，减少了耦合。控制权交给了“第三方容器”，由容器决定将具体实现动态的“注入”到调用的类当中。依赖注入是一种设计模式，是控制反转的一种实现方式。