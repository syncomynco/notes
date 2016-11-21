
### Spring Web MVC Framework

 -   The *Model* encapsulates the application data and in general they will consist of *POJO*.
 -  The *View* is responsible for rendering the model data and in general it generates HTML output that the client's browser can interpret.
 - The *Controller* is responsible for processing user requests and building appropriate model and passes it to the view for rendering.

#### IOC Containerss
The Spring container is at the core of the Spring Framework. The container will create the objects, wire them together, configure them, and manage their complete lifecycle from creation till destruction. The Spring container uses dependency injection (DI) to manage the components that make up an application. 
The container gets its instructions on what objects to instantiate, configure, and assemble by reading configuration metadata provided. The configuration metadata can be represented either by XML, Java annotations, or Java code. 
The Spring IoC container makes use of Java POJO classes and configuration metadata to produce a fully configured and executable system or application.

Spring provides following two distinct types of containers :
| Container | Description |
| --------- | ----------- |
| Spring BeanFactory Container | This is the simplest container providing basic support for DI and defined by the org.springframework.beans.factory.BeanFactory interface. The BeanFactory and related interfaces, such as BeanFactoryAware, InitializingBean, DisposableBean, are still present in Spring for the purposes of backward compatibility with the large number of third-party frameworks that integrate with Spring.|
|Spring ApplicationContext Container |This container adds more enterprise-specific functionality such as the ability to resolve textual messages from a properties file and the ability to publish application events to interested event listeners. This container is defined by the org.springframework.context.ApplicationContext interface.|

The ApplicationContext container includes all functionality of the BeanFactory container, so it is generally recommended over the BeanFactory. BeanFactory can still be used for light weight applications like mobile devices or applet based applications where data volume and speed is significant.



#### The DispatcherServlet

The Spring Web model-view-controller (MVC) framework is designed around a DispatcherServlet that handles all the HTTP requests and responses. 
Following is the sequence of events corresponding to an incoming HTTP request to DispatcherServlet:


-   After receiving an HTTP request, DispatcherServlet consults the HandlerMapping to call the appropriate Controller.
-  The Controller takes the request and calls the appropriate service methods based on used GET or POST method. The service method will set model data based on defined business logic and returns view name to the DispatcherServlet.
- The DispatcherServlet will take help from ViewResolver to pickup the defined view for the request.

-   Once view is finalized, The DispatcherServlet passes the model data to the view which is finally rendered on the browser.

----------

### Spring Beans
#### Bean Definition

The objects that form the backbone of your application and that are managed by the Spring IoC container are called beans. A bean is an object that is instantiated, assembled, and otherwise managed by a Spring IoC container. These beans are created with the configuration metadata that you supply to the container, for example, in the form of XML <bean/> definitions which you have already seen in previous chapters.

The bean definition contains the information called configuration metadata which is needed for the container to know the followings:

- How to create a bean
- Bean's lifecycle details
-  Bean's dependencies

All the above configuration metadata translates into a set of the following properties that make up each bean definition.

<table>
<tbody><tr><th>Properties</th><th>Description</th></tr>
<tr><td>class</td><td>This attribute is mandatory and specify the bean class to be used to create the bean.</td></tr>
<tr><td>name</td><td>This attribute specifies the bean identifier uniquely. In XML-based configuration metadata, you use the id and/or name attributes to specify the bean identifier(s).</td></tr>
<tr><td>scope</td><td>This attribute specifies the scope of the objects created from a particular bean definition and it will be discussed in bean scopes chapter.</td></tr>
<tr><td>constructor-arg</td><td>This is used to inject the dependencies and will be discussed in next chapters.</td></tr>
<tr><td>properties</td><td>This is used to inject the dependencies and will be discussed in next chapters.</td></tr>
<tr><td>autowiring mode</td><td>This is used to inject the dependencies and will be discussed in next chapters.</td></tr>
<tr><td>lazy-initialization mode</td><td>A lazy-initialized bean tells the IoC container to create a bean instance when it is first requested, rather than at startup.</td></tr>
<tr><td>initialization method</td><td>A callback to be called just after all necessary properties on the bean have been set by the container. It will be discussed in bean life cycle chapter.</td></tr>
<tr><td>destruction method</td><td>A callback to be used when the container containing the bean is destroyed.  It will be discussed in bean life cycle chapter.</td></tr>
</tbody></table>

#### Configuration Meta-data

Spring IoC container is totally decoupled from the format in which this configuration meta-data is actually written. There are following three important methods to provide configuration meta-data to the Spring Container:

- XML based configuration file.
- Annotation-based configuration
- Java-based configuration

#### Bean Scopes

When defining a *bean* in Spring, you have the option of declaring a scope for that bean. For example, To force Spring to produce a new bean instance each time one is needed, you should declare the bean's scope attribute to be prototype. Similar way if you want Spring to return the same bean instance each time one is needed, you should declare the bean's scope attribute to be singleton.

The Spring Framework supports following five scopes, three of which are available only if you use a web-aware *ApplicationContext*.

<table class="table table-bordered">
<tbody><tr><th>Scope</th><th>Description</th></tr>
<tr><td>singleton</td><td>This scopes the bean definition to a single instance per Spring IoC container (default).</td></tr>
<tr><td>prototype</td><td>This scopes a single bean definition to have any number of object instances.</td></tr>
<tr><td>request</td><td>This scopes a bean definition to an HTTP request. Only valid in the context of a web-aware Spring ApplicationContext.</td></tr>
<tr><td>session</td><td>This scopes a bean definition to an HTTP session. Only valid in the context of a web-aware Spring ApplicationContext.</td></tr>
<tr><td>global-session</td><td>This scopes a bean definition to a global HTTP session. Only valid in the context of a web-aware Spring ApplicationContext.</td></tr>
</tbody></table>

#### Life cycle of  Beans
**Initialization callbacks**:

The org.springframework.beans.factory.InitializingBean interface specifies a single method:
```
void afterPropertiesSet() throws Exception;
```

So you can simply implement above interface and initialization work can be done inside afterPropertiesSet() method as follows:

```
public class ExampleBean implements InitializingBean {
   public void afterPropertiesSet() {
      // do some initialization work
   }
}
```

In the case of XML-based configuration metadata, you can use the init-method attribute to specify the name of the method that has a void no-argument signature. For example:

```
<bean id="exampleBean" 
         class="examples.ExampleBean" init-method="init"/>
```
Following is the class definition:

```
public class ExampleBean {
   public void init() {
      // do some initialization work
   }
}
```
**Destruction callbacks**

The org.springframework.beans.factory.DisposableBean interface specifies a single method:

```
void destroy() throws Exception;
```

So you can simply implement above interface and finalization work can be done inside destroy() method as follows:

```
public class ExampleBean implements DisposableBean {
   public void destroy() {
      // do some destruction work
   }
}
```
In the case of XML-based configuration metadata, you can use the destroy-method attribute to specify the name of the method that has a void no-argument signature. For example:

```
<bean id="exampleBean"
         class="examples.ExampleBean" destroy-method="destroy"/>
```
Following is the class definition:
```
public class ExampleBean {
   public void destroy() {
      // do some destruction work
   }
}
```

If you are using Spring's IoC container in a non-web application environment; for example, in a rich client desktop environment; you register a shutdown hook with the JVM. Doing so ensures a graceful shutdown and calls the relevant destroy methods on your singleton beans so that all resources are released.

It is recommended that you do not use the *InitializingBean* or *DisposableBean* callbacks, because XML configuration gives much flexibility in terms of naming your method.

#### Default initialization and destroy methods:

If you have too many beans having initialization and or destroy methods with the same name, you don't need to declare *init-method* and *destroy-method* on each individual bean. Instead framework provides the flexibility to configure such situation using **default-init-method** and **default-destroy-method** attributes on the <beans> element as follows:
```
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd"
    default-init-method="init" 
    default-destroy-method="destroy">

   <bean id="..." class="...">
       <!-- collaborators and configuration for this bean go here -->
   </bean>

</beans>
```
#### Bean Post-processors
The BeanPostProcessor interface defines callback methods that you can implement to provide your own instantiation logic, dependency-resolution logic etc. You can also implement some custom logic after the Spring container finishes instantiating, configuring, and initializing a bean by plugging in one or more BeanPostProcessor implementations.

You can configure multiple BeanPostProcessor interfaces and you can control the order in which these BeanPostProcessor interfaces execute by setting the order property provided the BeanPostProcessor implements the Ordered interface.

The BeanPostProcessors operate on bean (or object) instances which means that the Spring IoC container instantiates a bean instance and then BeanPostProcessor interfaces do their work.

An ApplicationContext automatically detects any beans that are defined with implementation of the BeanPostProcessor interface and registers these beans as post-processors, to be then called appropriately by the container upon bean creation.

```
import org.springframework.beans.factory.config.BeanPostProcessor;
import org.springframework.beans.BeansException;

public class MyBeanProcessor implements BeanPostProcessor {
 
   public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
      System.out.println("BeforeInitialization : " + beanName);
      return bean;  // you can return any other object as well
   }

   public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
      System.out.println("AfterInitialization : " + beanName);
      return bean;  // you can return any other object as well
   }
   
}
```

#### Bean Definition Inheritance

A bean definition can contain a lot of configuration information, including constructor arguments, property values, and container-specific information such as initialization method, static factory method name, and so on.

A child bean definition inherits configuration data from a parent definition. The child definition can override some values, or add others, as needed.

Spring Bean definition inheritance has nothing to do with Java class inheritance but inheritance concept is same. You can define a parent bean definition as a template and other child beans can inherit required configuration from the parent bean.

When you use XML-based configuration metadata, you indicate a child bean definition by using the parent attribute, specifying the parent bean as the value of this attribute.

Following is the configuration file *"Beans.xml"* where we defined *"helloWorld"* bean which has two properties *message1* and *message2*. Next *"helloIndia"* bean has been defined as a child of *"helloWorld"* bean by using parent attribute. The child bean inherits *message2* property as is, and overrides *message1* property and introduces one more property *message3*.

```
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">

   <bean id="helloWorld" class="com.example.HelloWorld">
      <property name="message1" value="Hello World!"/>
      <property name="message2" value="Hello Second World!"/>
   </bean>

   <bean id="helloIndia" class="com.example.HelloIndia" parent="helloWorld">
      <property name="message1" value="Hello India!"/>
      <property name="message3" value="Namaste India!"/>
   </bean>

</beans>
```

#### Bean Definition Template

You can create a Bean definition template which can be used by other child bean definitions without putting much effort. While defining a Bean Definition Template, you should not specify class attribute and should specify abstract attribute with a value of true as shown below:

```
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">

   <bean id="beanTeamplate" abstract="true">
      <property name="message1" value="Hello World!"/>
      <property name="message2" value="Hello Second World!"/>
      <property name="message3" value="Namaste India!"/>
   </bean>

   <bean id="helloIndia" class="com.example.HelloIndia" parent="beanTeamplate">
      <property name="message1" value="Hello India!"/>
      <property name="message3" value="Namaste India!"/>
   </bean>

</beans>
```
The parent bean cannot be instantiated on its own because it is incomplete, and it is also explicitly marked as abstract. When a definition is abstract like this, it is usable only as a pure template bean definition that serves as a parent definition for child definitions.


----------


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

### Event Handling
You have seen in all the chapters that core of Spring is the *ApplicationContext*, which manages complete life cycle of the beans. The *ApplicationContext* publishes certain types of events when loading the beans. For example, a *ContextStartedEvent* is published when the context is started and *ContextStoppedEvent* is published when the context is stopped.

Event handling in the ApplicationContext is provided through the ApplicationEvent class and ApplicationListener interface. So if a bean implements the ApplicationListener, then every time an ApplicationEvent gets published to the ApplicationContext, that bean is notified.

Spring provides the following standard events:

1. **ContextRefreshedEvent**
This event is published when the *ApplicationContext* is either initialized or refreshed. This can also be raised using the **refresh()** method on the *ConfigurableApplicationContext* interface.

2. **ContextStartedEvent**
This event is published when the *ApplicationContext* is started using the **start()** method on the *ConfigurableApplicationContext* interface. You can poll your database or you can re/start any stopped application after receiving this event.

3. **ContextStoppedEvent**
This event is published when the *ApplicationContext* is stopped using the **stop()** method on the *ConfigurableApplicationContext* interface. You can do required housekeep work after receiving this event.

4. **ContextClosedEvent**
This event is published when the *ApplicationContext* is closed using the **close()** method on the *ConfigurableApplicationContext* interface. A closed context reaches its end of life; it cannot be refreshed or restarted.

5. **RequestHandledEvent**
This is a web-specific event telling all beans that an HTTP request has been serviced.

Spring's event handling is single-threaded so if an event is published, until and unless all the receivers get the message, the processes are blocked and the flow will not continue. Hence, care should be taken when designing your application if event handling is to be used.

####Listening to Context Events
To listen a context event, a bean should implement the *ApplicationListener* interface which has just one method ***onApplicationEvent()***.

Here is the content of **HelloWorld.java** file:
```
package com.example;

public class HelloWorld {
   private String message;

   public void setMessage(String message){
      this.message  = message;
   }

   public void getMessage(){
      System.out.println("Your Message : " + message);
   }
}
```
Following is the content of the **CStartEventHandler.java** file:
```
package com.example;

import org.springframework.context.ApplicationListener;
import org.springframework.context.event.ContextStartedEvent;

public class CStartEventHandler 
   implements ApplicationListener<ContextStartedEvent>{

   public void onApplicationEvent(ContextStartedEvent event) {
      System.out.println("ContextStartedEvent Received");
   }
}
```
Following is the content of the **CStopEventHandler.java** file:
```
package com.example;

import org.springframework.context.ApplicationListener;
import org.springframework.context.event.ContextStoppedEvent;

public class CStopEventHandler 
   implements ApplicationListener<ContextStoppedEvent>{

   public void onApplicationEvent(ContextStoppedEvent event) {
      System.out.println("ContextStoppedEvent Received");
   }
}
```
Following is the content of the **MainApp.java** file:

```
package com.example;

import org.springframework.context.ConfigurableApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class MainApp {
   public static void main(String[] args) {
      ConfigurableApplicationContext context = 
      new ClassPathXmlApplicationContext("Beans.xml");

      // Let us raise a start event.
      context.start();
	  
      HelloWorld obj = (HelloWorld) context.getBean("helloWorld");

      obj.getMessage();

      // Let us raise a stop event.
      context.stop();
   }
}
```
Following is the configuration file **Beans.xml**:
```
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">

   <bean id="helloWorld" class="com.example.HelloWorld">
      <property name="message" value="Hello World!"/>
   </bean>

   <bean id="cStartEventHandler" 
         class="com.example.CStartEventHandler"/>

   <bean id="cStopEventHandler" 
         class="com.example.CStopEventHandler"/>

</beans>
```
If you like, you can publish your own custom events and later you can capture the same to take any action against those custom events.

#### Custom Events
There are number of steps to be taken to write and publish your own custom events. 

1. Create an event class by extending *ApplicationEvent*. This class must define a default constructor which should inherit constructor from ApplicationEvent class.

2. Once your event class is defined, you can publish it from any class, let us say *EventClassPublisher* which implements *ApplicationEventPublisherAware*. You will also need to declare this class in XML configuration file as a bean so that the container can identify the bean as an event publisher because it implements the *ApplicationEventPublisherAware* interface.

3. A published event can be handled in a class, let us say EventClassHandler which implements ApplicationListener interface and implements onApplicationEvent method for the custom event.

**Example**
Here is the content of CustomEvent.java file:
```
package com.tutorialspoint;

import org.springframework.context.ApplicationEvent;

public class CustomEvent extends ApplicationEvent{
   
   public CustomEvent(Object source) {
      super(source);
   }

   public String toString(){
      return "My Custom Event";
   }
}

```
Following is the content of the CustomEventPublisher.java file:
```
package com.tutorialspoint;

import org.springframework.context.ApplicationEventPublisher;
import org.springframework.context.ApplicationEventPublisherAware;

public class CustomEventPublisher 
   implements ApplicationEventPublisherAware {
   
   private ApplicationEventPublisher publisher;

   public void setApplicationEventPublisher
              (ApplicationEventPublisher publisher){
      this.publisher = publisher;
   }

   public void publish() {
      CustomEvent ce = new CustomEvent(this);
      publisher.publishEvent(ce);
   }
}
```
Following is the content of the CustomEventHandler.java file.
```
package com.tutorialspoint;

import org.springframework.context.ApplicationListener;

public class CustomEventHandler 
   implements ApplicationListener<CustomEvent>{

   public void onApplicationEvent(CustomEvent event) {
      System.out.println(event.toString());
   }

}
```
Following is the content of the MainApp.java file:
```
package com.tutorialspoint;

import org.springframework.context.ConfigurableApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class MainApp {
   public static void main(String[] args) {
      ConfigurableApplicationContext context = 
      new ClassPathXmlApplicationContext("Beans.xml");
	  
      CustomEventPublisher cvp = 
      (CustomEventPublisher) context.getBean("customEventPublisher");
      cvp.publish();  
      cvp.publish();
   }
}
```
Following is the configuration file Beans.xml:
```
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">

   <bean id="customEventHandler" 
      class="com.tutorialspoint.CustomEventHandler"/>

   <bean id="customEventPublisher" 
      class="com.tutorialspoint.CustomEventPublisher"/>

</beans>
```