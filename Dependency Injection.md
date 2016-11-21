


### Dependency Injection
When writing a complex Java application, application classes should be as independent as possible of other Java classes to increase the possibility to reuse these classes and to test them independently of other classes while doing unit testing. **Dependency Injection** (or sometime called ***wiring***) helps in gluing these classes together and same time keeping them independent.

Consider you have an application which has a text editor component and you want to provide spell checking. Your standard code would look something like this:
```
public class TextEditor {
   private SpellChecker spellChecker;
   
   public TextEditor() {
      spellChecker = new SpellChecker();
   }
}
```
What we've done here is create a dependency between the TextEditor and the SpellChecker. In an inversion of control scenario we would instead do something like this:
```
public class TextEditor {
   private SpellChecker spellChecker;
   
   public TextEditor(SpellChecker spellChecker) {
      this.spellChecker = spellChecker;
   }
}
```
Here TextEditor should not worry about SpellChecker implementation. The SpellChecker will be implemented independently and will be provided to TextEditor at the time of TextEditor instantiation and this entire procedure is controlled by the Spring Framework.

Here, we have removed the total control from TextEditor and kept it somewhere else (ie. XML configuration file) and the dependency ( ie. class SpellChecker) is being injected into the class TextEditor through a **Class Constructor**. Thus flow of control has been *"inverted"* by *Dependency Injection (DI)* because you have effectively delegated dependencies to some external system.

Second method of injecting dependency is through *Setter Methods* of *TextEditor* class where we will create *SpellChecker* instance and this instance will be used to call setter methods to initialize *TextEditor*'s properties.

Thus, *DI* exists in two major variants as following :

- **Constructor-based Dependency Injection** :  
   Constructor-based DI is accomplished when the container invokes a class constructor with a number of arguments, each representing a dependency on other class.

- **Setter-based Dependency Injection** : 
  Setter-based DI is accomplished by the container calling setter methods on your beans after invoking a no-argument constructor or no-argument static factory method to instantiate your bean.
 
Code is cleaner with the DI principle and decoupling is more effective when objects are provided with their dependencies. The object does not look up its dependencies, and does not know the location or class of the dependencies rather everything is taken care by the Spring Framework.

##### **Constructor-based Dependency Injection** 
Following is the configuration file Beans.xml which has configuration for the constructor-based injection:
```
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">

   <!-- Definition for textEditor bean -->
   <bean id="textEditor" class="com.example.TextEditor">
      <constructor-arg ref="spellChecker"/>
   </bean>

   <!-- Definition for spellChecker bean -->
   <bean id="spellChecker" class="com.example.SpellChecker">
   </bean>

</beans>
```
**Constructor arguments resolution**
There may be a ambiguity exist while passing arguments to the constructor in case there are more than one parameters. To resolve this ambiguity, the order in which the constructor arguments are defined in a bean definition is the order in which those arguments are supplied to the appropriate constructor. Consider the following class:

```
package x.y;

public class Foo {
   public Foo(Bar bar, Baz baz) {
      // ...
   }
}
```
The following configuration works fine:
```
<beans>
   <bean id="foo" class="x.y.Foo">
      <constructor-arg ref="bar"/>
      <constructor-arg ref="baz"/>
   </bean>

   <bean id="bar" class="x.y.Bar"/>
   <bean id="baz" class="x.y.Baz"/>
</beans>
```
Let us check one more case where we pass different types to the constructor. Consider the following class:
```
package x.y;

public class Foo {
   public Foo(int year, String name) {
      // ...
   }
}
```
The container can also use type matching with simple types if you explicitly specify the type of the constructor argument using the type attribute. For example:
```
<beans>

   <bean id="exampleBean" class="examples.ExampleBean">
      <constructor-arg type="int" value="2001"/>
      <constructor-arg type="java.lang.String" value="Zara"/>
   </bean>

</beans>
```
Finally and the best way to pass constructor arguments, use the index attribute to specify explicitly the index of constructor arguments. Here the index is 0 based. For example:
```
<beans>

   <bean id="exampleBean" class="examples.ExampleBean">
      <constructor-arg index="0" value="2001"/>
      <constructor-arg index="1" value="Zara"/>
   </bean>

</beans>
```
A final note, in case you are passing a reference to an object, you need to use ref attribute of **constructor-arg** tag and if you are passing a value directly then you should use **value** attribute as shown above.

##### **Setter-based Dependency Injection**
Setter-based DI is accomplished by the container calling setter methods on your beans after invoking a no-argument constructor or no-argument static factory method to instantiate your bean.

Following is the configuration file Beans.xml which has configuration for the setter-based injection:  
```
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">

   <!-- Definition for textEditor bean -->
   <bean id="textEditor" class="com.example.TextEditor">
      <property name="spellChecker" ref="spellChecker"/>
   </bean>

   <!-- Definition for spellChecker bean -->
   <bean id="spellChecker" class="com.example.SpellChecker">
   </bean>

</beans>
```
You should note the difference in Beans.xml file defined in constructor-based injection and setter-based injection. The only difference is inside the <bean> element where we have used <constructor-arg> tags for constructor-based injection and <property> tags for setter-based injection.

Second important point to note is that in case you are passing a reference to an object, you need to use ref attribute of <property> tag and if you are passing a value directly then you should use value attribute.

**XML Configuration using p-namespace**
If you have many setter methods then it is convenient to use p-namespace in the XML configuration file. Let us check the difference:

Let us take the example of a standard XML configuration file with **property** tags:
```
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">

   <bean id="john-classic" class="com.example.Person">
      <property name="name" value="John Doe"/>
      <property name="spouse" ref="jane"/>
   </bean>

   <bean name="jane" class="com.example.Person">
      <property name="name" value="John Doe"/>
   </bean>

</beans>
```
Above XML configuration can be re-written in a cleaner way using p-namespace as follows:
```
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:p="http://www.springframework.org/schema/p"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">

   <bean id="john-classic" class="com.example.Person"
      p:name="John Doe"
      p:spouse-ref="jane"/>
   </bean>

   <bean name="jane" class="com.example.Person"
      p:name="John Doe"/>
   </bean>

</beans>
```
Here you should not the difference in specifying primitive values and object references with **p-namespace**. The **-ref** part indicates that this is not a straight value but rather a reference to another bean.

#### Injecting Inner Beans
As you know Java inner classes are defined within the scope of other classes, similarly, inner beans are beans that are defined within the scope of another bean. Thus, a ```<bean/>``` element inside the ```<property/>``` or ```<constructor-arg/>``` elements is called **inner bean** and it is shown below.
```
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">

   <bean id="outerBean" class="...">
      <property name="target">
         <bean id="innerBean" class="..."/>
      </property>
   </bean>

</beans>
```
#### Injecting Collections
You have seen how to configure primitive data type using value attribute and object references using ref attribute of the ```<property>``` tag in your Bean configuration file. Both the cases deal with passing singular value to a bean.

Now what about if you want to pass plural values like *Java Collection* types *List, Set, Map,* and *Properties*. To handle the situation, Spring offers four types of collection configuration elements which are as follows:

| Element | Description |
| ----------- | --------------- |
|```<list>```| This helps in wiring ie injecting a list of values, allowing duplicates.|
|```<set>``` | This helps in wiring a set of values but without any duplicates.|
|```<map>```| This can be used to inject a collection of name-value pairs where name and value can be of any type.|
|```<props>``` | This can be used to inject a collection of name-value pairs where the name and value are both Strings.|

You can use either <list> or <set> to wire any implementation of java.util.Collection or an array.

You will come across two situations :

- Passing direct values of the collection 
- Passing a reference of a bean as one of the collection elements

Following is the configuration file Beans.xml which has configuration for all the type of collections:
```
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">

   <!-- Definition for javaCollection -->
   <bean id="javaCollection" class="com.example.JavaCollection">

      <!-- results in a setAddressList(java.util.List) call -->
      <property name="addressList">
         <list>
            <value>INDIA</value>
            <value>Pakistan</value>
            <value>USA</value>
            <value>USA</value>
         </list>
      </property>

      <!-- results in a setAddressSet(java.util.Set) call -->
      <property name="addressSet">
         <set>
            <value>INDIA</value>
            <value>Pakistan</value>
            <value>USA</value>
            <value>USA</value>
        </set>
      </property>

      <!-- results in a setAddressMap(java.util.Map) call -->
      <property name="addressMap">
         <map>
            <entry key="1" value="INDIA"/>
            <entry key="2" value="Pakistan"/>
            <entry key="3" value="USA"/>
            <entry key="4" value="USA"/>
         </map>
      </property>
      
      <!-- results in a setAddressProp(java.util.Properties) call -->
      <property name="addressProp">
         <props>
            <prop key="one">INDIA</prop>
            <prop key="two">Pakistan</prop>
            <prop key="three">USA</prop>
            <prop key="four">USA</prop>
         </props>
      </property>

   </bean>

</beans>
```
##### **Injecting Bean References** 
Following Bean definition will help you understand how to inject bean references as one of the collection's element. Even you can mix references and values all together as shown below:
```
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">

   <!-- Bean Definition to handle references and values -->
   <bean id="..." class="...">

      <!-- Passing bean reference  for java.util.List -->
      <property name="addressList">
         <list>
            <ref bean="address1"/>
            <ref bean="address2"/>
            <value>Pakistan</value>
         </list>
      </property>
      
      <!-- Passing bean reference  for java.util.Set -->
      <property name="addressSet">
         <set>
            <ref bean="address1"/>
            <ref bean="address2"/>
            <value>Pakistan</value>
         </set>
      </property>
      
      <!-- Passing bean reference  for java.util.Map -->
      <property name="addressMap">
         <map>
            <entry key="one" value="INDIA"/>
            <entry key ="two" value-ref="address1"/>
            <entry key ="three" value-ref="address2"/>
         </map>
      </property>
      
   </bean>

</beans>
```
To use above bean definition, you need to define your setter methods in such a way that they should be able to handle references as well.

##### **Injecting null and empty string values**
If you need to pass an empty string as a value then you can pass it as follows:
```
<bean id="..." class="exampleBean">
   <property name="email" value=""/>
</bean>
```
The preceding example is equivalent to the Java code: ```exampleBean.setEmail("")```

If you need to pass an NULL value then you can pass it as follows:
```
<bean id="..." class="exampleBean">
   <property name="email"><null/></property>
</bean>
```
The preceding example is equivalent to the Java code: ```exampleBean.setEmail(null)```

#### Beans Auto-Wiring
You have learnt how to declare beans using the ```<bean>``` element and inject ```<bean>``` with using ```<constructor-arg>``` and ```<property>``` elements in XML configuration file.

The Spring container can auto-wire relationships between collaborating beans without using ```<constructor-arg>``` and ```<property>``` elements which helps cut down on the amount of XML configuration you write for a big Spring based application.

##### **Beans Auto-wiring Modes**
There are following autowiring modes which can be used to instruct Spring container to use autowiring for dependency injection. You use the autowire attribute of the ```<bean/>``` element to specify autowire mode for a bean definition.

| Mode | Description |
| --- | ---- |
| no | This is default setting which means no autowiring and you should use explicit bean reference for wiring. You have nothing to do special for this wiring. |
| byName | Autowiring by property name. Spring container looks at the properties of the beans on which autowire attribute is set to byName in the XML configuration file. It then tries to match and wire its properties with the beans defined by the same names in the configuration file. |
| byType | Autowiring by property datatype. Spring container looks at the properties of the beans on which autowire attribute is set to byType in the XML configuration file. It then tries to match and wire a property if its type matches with exactly one of the beans name in configuration file. If more than one such beans exists, a fatal exception is thrown. | 
| constructor | Similar to byType, but type applies to constructor arguments. If there is not exactly one bean of the constructor argument type in the container, a fatal error is raised. |
| autodetect | Spring first tries to wire using autowire by *constructor*, if it does not work, Spring tries to autowire by *byType*. |

You can use byType or constructor autowiring mode to wire arrays and other typed-collections.

##### **Autowiring byName**
This mode specifies autowiring by property name. Spring container looks at the beans on which auto-wire attribute is set to byName in the XML configuration file. It then tries to match and wire its properties with the beans defined by the same names in the configuration file. If matches are found, it will inject those beans otherwise, it will throw exceptions.
```


<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">

   <!-- Definition for textEditor bean -->
   <bean id="textEditor" class="com.example.TextEditor" 
      autowire="byName">
      <property name="name" value="Generic Text Editor" />
   </bean>

   <!-- Definition for spellChecker bean -->
   <bean id="spellChecker" class="com.example.SpellChecker">
   </bean>

</beans>
```
##### **Autowiring byConstructor**
This mode is very similar to byType, but it applies to constructor arguments. Spring container looks at the beans on which autowire attribute is set to constructor in the XML configuration file. It then tries to match and wire its constructor's argument with exactly one of the beans name in configuration file. If matches are found, it will inject those beans otherwise, it will throw exceptions.

Following is the example configuration file Beans.xml :
```
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">

   <!-- Definition for textEditor bean -->
   <bean id="textEditor" class="com.example.TextEditor" 
      autowire="constructor">
      <constructor-arg value="Generic Text Editor"/>
   </bean>

   <!-- Definition for spellChecker bean -->
   <bean id="SpellChecker" class="com.example.SpellChecker">
   </bean>

</beans>
```

##### **Limitations with Auto-wiring**
Autowiring works best when it is used consistently across a project. If autowiring is not used in general, it might be confusing to developers to use it to wire only one or two bean definitions. Though, autowiring can significantly reduce the need to specify properties or constructor arguments but you should consider the limitations and disadvantages of autowiring before using them.

| Limitations |	Description |
| --------------- | --------------- |
|Overriding possibility |	You can still specify dependencies using <constructor-arg> and <property> settings which will always override autowiring.|
| Primitive data types	| You cannot autowire so-called simple properties such as primitives, Strings, and Classes.|
|Confusing nature	| Autowiring is less exact than explicit wiring, so if possible prefer using explict wiring.|


----------

### Annotation Based Configuration
Starting from Spring 2.5 it became possible to configure the dependency injection using annotations. So instead of using XML to describe a bean wiring, you can move the bean configuration into the component class itself by using annotations on the relevant class, method, or field declaration.

Annotation injection is performed before XML injection, thus the latter configuration will override the former for properties wired through both approaches.

Annotation wiring is not turned on in the Spring container by default. So, before we can use annotation-based wiring, we will need to enable it in our Spring configuration file. So consider to have following configuration file in case you want to use any annotation in your Spring application.
```
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
    http://www.springframework.org/schema/context
    http://www.springframework.org/schema/context/spring-context-3.0.xsd">

   <context:annotation-config/>
   <!-- bean definitions go here -->

</beans>
```
Once ```<context:annotation-config/>```  is configured, you can start annotating your code to indicate that Spring should automatically wire values into properties, methods, and constructors. Let us see few important annotations to understand how they work:

1. **@Required** :  The @Required annotation applies to bean property setter methods and it indicates that the affected bean property must be populated in XML configuration file at configuration time otherwise the container throws a *BeanInitializationException* exception.

2. **@Autowired**: The @Autowired annotation provides more fine-grained control over where and how autowiring should be accomplished. The @Autowired annotation can be used to autowire bean on the setter method just like @Required annotation, constructor, a property or methods with arbitrary names and/or multiple arguments. By default, the @Autowired annotation implies the dependency is required similar to *@Required* annotation, however, you can turn off the default behavior by using **(required=false)** option with *@Autowired*.

3. **@Qualifier**  : There may be a situation when you create more than one bean of the same type and want to wire only one of them with a property, in such case you can use **@Qualifier** annotation along with *@Autowired* to remove the confusion by specifying which exact bean will be wired. 

4.**@JSR-250** : Spring also JSR-250 based annotations which include **@PostConstruct**, **@PreDestroy** and **@Resource** annotations. Though these annotations are not really required because you already have other alternates. You can use **@PostConstruct** annotation as an alternate of initialization callback and **@PreDestroy** annotation as an alternate of destruction callback. You can use **@Resource** annotation on fields or setter methods and it works the same as in *Java EE 5*. The **@Resource** annotation takes a *'name'* attribute which will be interpreted as the bean name to be injected. 

-----------

### Java Based Configuration
Java based configuration option enables you to write most of your Spring configuration without XML but with the help of few Java-based annotations explained below.

#### **@Configuration & @Bean Annotations**:
Annotating a class with the @Configuration indicates that the class can be used by the Spring IoC container as a source of bean definitions. The @Bean annotation tells Spring that a method annotated with @Bean will return an object that should be registered as a bean in the Spring application context. The simplest possible @Configuration class would be as follows:

```
import org.springframework.context.annotation.*;

@Configuration
public class HelloWorldConfig {

   @Bean 
   public HelloWorld helloWorld(){
      return new HelloWorld();
   }
}
```
Above code will be equivalent to the following XML configuration:
```
<beans>
   <bean id="helloWorld" class="com.example.HelloWorld" />
</beans>
```
Here the method name annotated with *@Bean* works as bean *ID* and it creates and returns actual bean. Your configuration class can have declaration for more than one *@Bean*. Once your configuration classes are defined, you can load and provide them to Spring container using **AnnotationConfigApplicationContext** as follows:
```public static void main(String[] args) {
   ApplicationContext ctx = 
   new AnnotationConfigApplicationContext(HelloWorldConfig.class);
   
   HelloWorld helloWorld = ctx.getBean(HelloWorld.class);

   helloWorld.setMessage("Hello World!");
   helloWorld.getMessage();
}
```
You can load various configuration classes as follows:
```
public static void main(String[] args) {
   AnnotationConfigApplicationContext ctx = 
   new AnnotationConfigApplicationContext();

   ctx.register(AppConfig.class, OtherConfig.class);
   ctx.register(AdditionalConfig.class);
   ctx.refresh();

   MyService myService = ctx.getBean(MyService.class);
   myService.doStuff();
}
```
#### Injecting Bean Dependencies :
When *@Beans* have dependencies on one another, expressing that dependency is as simple as having one bean method calling another as follows:

```
import org.springframework.context.annotation.*;

@Configuration
public class AppConfig {
   @Bean
   public Foo foo() {
      return new Foo(bar());
   }
   @Bean
   public Bar bar() {
      return new Bar();
   }
}
```
#### @Import Annotation:
The @Import annotation allows for loading @Bean definitions from another configuration class. Consider a ConfigA class as follows
```
@Configuration
public class ConfigA {
   @Bean
   public A a() {
      return new A(); 
   }
}
```
You can import above Bean declaration in another Bean Declaration as follows:
```
@Configuration
@Import(ConfigA.class)
public class ConfigB {
   @Bean
   public B a() {
      return new A(); 
   }
}
```
Now, rather than needing to specify both ConfigA.class and ConfigB.class when instantiating the context, only ConfigB needs to be supplied as follows:
```
public static void main(String[] args) {
   ApplicationContext ctx = 
   new AnnotationConfigApplicationContext(ConfigB.class);
   // now both beans A and B will be available...
   A a = ctx.getBean(A.class);
   B b = ctx.getBean(B.class);
}
```
#### Lifecycle Callbacks : 
The @Bean annotation supports specifying arbitrary initialization and destruction callback methods, much like Spring XML's init-method and destroy-method attributes on the bean element:
```
public class Foo {
   public void init() {
      // initialization logic
   }
   public void cleanup() {
      // destruction logic
   }
}

@Configuration
public class AppConfig {
   @Bean(initMethod = "init", destroyMethod = "cleanup" )
   public Foo foo() {
      return new Foo();
   }
}
```
#### Specifying Bean Scope:

The default scope is singleton, but you can override this with the @Scope annotation as follows:
```
@Configuration
public class AppConfig {
   @Bean
   @Scope("prototype")
   public Foo foo() {
      return new Foo();
   }
}
```

-------------