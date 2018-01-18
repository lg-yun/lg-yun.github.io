## 六大原则
### 单一功能原则  
- 类、模块、方法承担的职责越多，复用的可能就越小。  
- 实现高内聚、低耦合的指导方针，它是最简单但又最难运用的原则。

### 开闭原则    
一个软件实体应当对扩展开放，对修改关闭。  
即软件实体应尽量在不修改原有代码的情况下进行扩展。

### 里氏替换原则	
- 所有引用基类（父类）的地方必须能透明地使用其子类的对象。
- 子类的所有方法必须在父类中声明，或子类必须实现父类中声明的所有方法。
- 里氏代换原则是实现开闭原则的重要方式之一

### 依赖倒置原则  
- 抽象不应该依赖于细节，细节应当依赖于抽象。换言之，要针对接口编程，而不是针对实现编程。
- 开闭原则是目标，里氏代换原则是基础，依赖倒转原则是手段


### 接口隔离原则
- 使用多个专门的接口，而不使用单一的总接口，即客户端不应该依赖那些它不需要的接口。
- 接口不能太小，如果太小会导致系统中接口泛滥，不利于维护。
- 接口也不能太大，太大的接口将违背接口隔离原则，灵活性较差，使用起来很不方便。

### 迪米特法则
- 一个软件实体应当尽可能少地与其他实体发生相互作用。
- 迪米特法则可降低系统的耦合度，使类与类之间保持松散的耦合关系。


## 访问者模式
访问者模式是一种将算法与对象结构分离的软件模式。  
这个模式的基本想法如下：首先我们拥有一个由许多对象构成的对象结构，这些对象的类都拥有一个accept方法用来接受访问者对象；访问者是一个接口，它拥有一个visit方法，这个方法对访问到的对象结构中不同类型的元素作出不同的反应；在对象结构的一次访问过程中，我们遍历整个对象结构，对每一个元素都实施accept方法，在每一个元素的accept方法中回调访问者的visit方法，从而使访问者得以处理对象结构的每一个元素。我们可以针对对象结构设计不同的实在的访问者类来完成不同的操作。 --from wiki

### 访问者通用类图
![](/Users/yan/Library/Containers/com.tencent.WeWorkMac/Data/Library/Application Support/WXWork/Data/1688850556269687/Cache/Image/2018-01/53c7cdbf-75a1-486a-a22a-3cf1f83837f5.jpg)

- Viistor ---- 抽象访问者  
	抽象类或者接口，声明访问者可以访问哪些元素，通过程序中的visit的参数来定义哪些对象是可以被访问的。
- ConcreteVisitor ---- 具体访问者  
	具体实现类，在该类中实现不同的visit方法
- Element ---- 抽象元素
	接口或者抽象类，声明接受哪一类访问者可以访问。
- ConcreteElement --- 具体元素
	实现accept方法，通常是visitor.visit(this)
- ObjectStruture ---- 结构对象
	元素生产者，一般容纳多个不同类，不同接口的容器,实际项目很少抽象出该类

### 代码示例
- 抽象访问者  
 
```
package com.design.pattern.visitor;

/**
 * 抽象访问者
 */
public interface IVisitor {

    /**
     * @param concreteElement1 能够访问的对象
     */
    void  visit(ConcreteElement1 concreteElement1);

    void visit(ConcreteElement2 concreteElement1);
}
```
- 访问者实现

```
package com.design.pattern.visitor;

/**
 * 访问者实现
 */
public class Visitor implements IVisitor{
    /**
     * 访问对象ConcreteElement1
     * @param concreteElement1 能够访问的对象
     */
    @Override
    public void visit(ConcreteElement1 concreteElement1) {
        concreteElement1.doSomething();
    }

    @Override
    public void visit(ConcreteElement2 concreteElement1) {
        concreteElement1.doSomething();
    }
}
``` 

- 抽象被访问者
 
```
package com.design.pattern.visitor;

/**
 * 抽象访问对象
 */
public abstract class Element {
    /**
     * 业务处理
     */
    public abstract void doSomething();

    /**
     * 允许访问者visitor访问
     * @param visitor 访问者
     */
    public abstract void accept(IVisitor visitor);
}
```
- 具体元素

```
package com.design.pattern.visitor;

public class ConcreteElement1 extends Element {
    
    @Override
    public void doSomething() {
        System.out.println("this concreteElement1...");
    }

    /**
     * @param visitor 访问者
     */
    @Override
    public void accept(IVisitor visitor) {
        visitor.visit(this);
    }
}
```
```
package com.design.pattern.visitor;

public class ConcreteElement2 extends Element {
    @Override
    public void doSomething() {
        System.out.println("this concreteElement2...");
    }

    /**
     * @param visitor 访问者
     */
    @Override
    public void accept(IVisitor visitor) {
        visitor.visit(this);
    }
}
``` 
- 结构对象
 
```
package com.design.pattern.visitor;

import java.util.Random;

public class ObjectStructure {

    /**
     * 对象生成
     * @return
     */
    public static Element createElement(){
        Random random = new Random();
        int randomNumber = random.nextInt();
        if(randomNumber % 2 == 0){
            return new ConcreteElement1();
        } else {
            return new ConcreteElement2();
        }
    }
}
```

```
package com.design.pattern.visitor;

public class Client {
    public static void main(String[] args) {
        for(int i = 0; i < 10; i++){
            Element element = ObjectStructure.createElement();
            //接受访问者访问
            element.accept(new Visitor());
        }
    }
}
```
- 执行结果  

```
this concreteElemt2...
this concreteElemt1...
this concreteElemt1...
this concreteElemt1...
this concreteElemt2...
this concreteElemt2...
this concreteElemt2...
this concreteElemt1...
this concreteElemt2...
this concreteElemt2...
``` 

###优点  
- 符合单一职责原则,数据结构和对数据的操作分离了。
- 功能上具有良好的扩展性，当添加新的操作时，不需要修改原有的类，只需要重新实现一个visitor就可以了。  
 
###缺点
- 破坏封装  
访问者方法假定ConcreteElement接口的功能足够强,足以让访问者进行它 们的工作。结果是,该模式常常迫使你提供访问元素内部状态的公共操作,这可能会破坏它 的封装性。 

- 具体元素变更比较困难
具体元素角色的增加、删除、修改都是比较困难的。

##使用场景
访问者是一种集中规整模式,特别适合用于大规模重构的项目，在这一个阶段的需求已经非常清晰，原系统的功能点也已经明确，通过访问者模式可以很容易把一些功能进行梳理，达到最终目的功能集中化  
Javac在构建、优化、解析语法树的时候就是采用的是Visitor模式(语法、语义分析阶段)。
