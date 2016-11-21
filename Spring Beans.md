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