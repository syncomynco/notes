
## AOP with Spring
One of the key components of Spring Framework is the **Aspect oriented programming (AOP)** framework. *Aspect Oriented Programming* entails breaking down program logic into distinct parts called so-called **concerns**. The functions that span multiple points of an application are called **cross-cutting concerns** and these *cross-cutting concerns* are conceptually separate from the application's business logic. There are various common good examples of aspects like logging, auditing, declarative transactions, security, and caching etc.

The key unit of modularity in *OOP* is the *class*, whereas in *AOP* the unit of modularity is the *aspect*. *Dependency Injection* helps you decouple your application objects from each other and *AOP* helps you decouple *cross-cutting concerns* from the objects that they affect. *AOP* is like *triggers* in programming languages such as Perl, .NET, Java and others.

*Spring AOP* module provides interceptors to intercept an application, for example, when a method is executed, you can add extra functionality before or after the method execution.

Before we start working with AOP, let us become familiar with the AOP concepts and terminology. These terms are not specific to Spring, rather they are related to AOP.

| Term | Description |
| ------ | --------------- |
| Aspect | A module which has a set of APIs providing cross-cutting requirements. For example, a logging module would be called AOP aspect for logging. An application can have any number of aspects depending on the requirement.|
| Join Point | This represents a point in your application where you can plug-in AOP aspect. You can also say, it is the actual place in the application where an action will be taken using Spring AOP framework.|
| Advice | This is the actual action to be taken either before or after the method execution. This is actual piece of code that is invoked during program execution by Spring AOP framework. |
| Pointcut | This is a set of one or more joinpoints where an advice should be executed. You can specify pointcuts using expressions or patterns. |
| Introduction | An introduction allows you to add new methods or attributes to existing classes. |
| Target Object | The object being advised by one or more aspects, this object will always be a proxied object. Also referred to as the advised object. |
| Weaving | Weaving is the process of linking aspects with other application types or objects to create an advised object. This can be done at compile time, load time, or at runtime. |

#### Types of Advice
Spring aspects can work with five kinds of advice mentioned below:
| Advice |	Description |
| ------ | -------- |
| before | Run advice before the a method execution. |
| after	 | Run advice after the a method execution regardless of its outcome.|
| after-returning | Run advice after the a method execution only if method completes successfully.|
| after-throwing | Run advice after the a method execution only if method exits by throwing an exception.|
| around |Run advice before and after the advised method is invoked.|

### Custom Aspects Implementation
Spring supports the ***@AspectJ*** annotation style approach and the ***schema-based*** approach to implement custom aspects. 

#### **XML Schema Based AOP**
To use the aop namespace tags described in this section, you need to import the spring-aop schema. 

You will also need following AspectJ libraries on the CLASSPATH of your application. These libraries are available in the 'lib' directory of an AspectJ installation, otherwise you can download them from the internet.

- aspectjrt.jar
- aspectjweaver.jar
- aspectj.jar
- aopalliance.jar

##### **Declaring an Aspect**
An aspect is declared using the ```<aop:aspect>``` element, and the backing bean is referenced using the ref attribute as follows:

```
<aop:config>
   <aop:aspect id="myAspect" ref="aBean">
   ...
   </aop:aspect>
</aop:config>

<bean id="aBean" class="...">
...
</bean>
```
Here "aBean" will be configured and dependency injected just like any other Spring bean.

##### **Declaring a pointcut**
A **pointcut** helps in determining the *join points* (ie methods) of interest to be executed with different advices. While working with *XML Schema Based* configuration, *pointcut* will be defined as follows:
```
<aop:config>
   <aop:aspect id="myAspect" ref="aBean">

   <aop:pointcut id="businessService"
      expression="execution(* com.xyz.myapp.service.*.*(..))"/>
   ...
   </aop:aspect>
</aop:config>

<bean id="aBean" class="...">
...
</bean>
```

##### **Declaring advices**

You can declare any of the five advices inside an ```<aop:aspect>``` using the ```<aop:{ADVICE NAME}>``` element as given below:
```
<aop:config>
   <aop:aspect id="myAspect" ref="aBean">
      <aop:pointcut id="businessService"
         expression="execution(* com.xyz.myapp.service.*.*(..))"/>

      <!-- a before advice definition -->
      <aop:before pointcut-ref="businessService" 
         method="doRequiredTask"/>

      <!-- an after advice definition -->
      <aop:after pointcut-ref="businessService" 
         method="doRequiredTask"/>

      <!-- an after-returning advice definition -->
      <!--The doRequiredTask method must have parameter named retVal -->
      <aop:after-returning pointcut-ref="businessService"
         returning="retVal"
         method="doRequiredTask"/>

      <!-- an after-throwing advice definition -->
      <!--The doRequiredTask method must have parameter named ex -->
      <aop:after-throwing pointcut-ref="businessService"
         throwing="ex"
         method="doRequiredTask"/>

      <!-- an around advice definition -->
      <aop:around pointcut-ref="businessService" 
         method="doRequiredTask"/>
   ...
   </aop:aspect>
</aop:config>

<bean id="aBean" class="...">
...
</bean>
```
You can use same doRequiredTask or different methods for different advices. These methods will be defined as a part of aspect module.

Following is the example configuration file, *Beans.xml*:
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
    xmlns:aop="http://www.springframework.org/schema/aop"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd 
    http://www.springframework.org/schema/aop 
    http://www.springframework.org/schema/aop/spring-aop-3.0.xsd ">

   <aop:config>
      <aop:aspect id="log" ref="logging">
         <aop:pointcut id="selectAll" 
         expression="execution(* com.example.*.*(..))"/>
         <aop:before pointcut-ref="selectAll" method="beforeAdvice"/>
         <aop:after pointcut-ref="selectAll" method="afterAdvice"/>
         <aop:after-returning pointcut-ref="selectAll" 
                              returning="retVal"
                              method="afterReturningAdvice"/>
         <aop:after-throwing pointcut-ref="selectAll" 
                             throwing="ex"
                             method="AfterThrowingAdvice"/>
      </aop:aspect>
   </aop:config>

   <!-- Definition for student bean -->
   <bean id="student" class="com.example.Student">
      <property name="name"  value="Zara" />
      <property name="age"  value="11"/>      
   </bean>

   <!-- Definition for logging aspect -->
   <bean id="logging" class="com.example.Logging"/> 
      
</beans>
```
Above defined ```<aop:pointcut>``` selects all the methods defined under the package *com.example*. Let us suppose, you want to execute your advice before or after a particular method, you can define your *pointcut* to narrow down your execution by replacing stars ( * ) in *pointcut* definition with actual class and method names. Below is a modified XML configuration file to show the concept:
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
    xmlns:aop="http://www.springframework.org/schema/aop"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd 
    http://www.springframework.org/schema/aop 
    http://www.springframework.org/schema/aop/spring-aop-3.0.xsd ">

   <aop:config>
   <aop:aspect id="log" ref="logging">
      <aop:pointcut id="selectAll" 
      expression="execution(* com.example.Student.getName(..))"/>
      <aop:before pointcut-ref="selectAll" method="beforeAdvice"/>
      <aop:after pointcut-ref="selectAll" method="afterAdvice"/>
   </aop:aspect>
   </aop:config>

   <!-- Definition for student bean -->
   <bean id="student" class="com.example.Student">
      <property name="name"  value="Zara" />
      <property name="age"  value="11"/>      
   </bean>

   <!-- Definition for logging aspect -->
   <bean id="logging" class="com.example.Logging"/> 
      
</beans>
```
#### **@AspectJ Based AOP**
**@AspectJ** refers to a style of declaring aspects as regular Java classes annotated with *Java 5 annotations*. The *@AspectJ* support is enabled by including the following element inside your *XML Schema-based* configuration file : 
```<aop:aspectj-autoproxy/>```
You will also need following AspectJ libraries on the classpath of your application. These libraries are available in the 'lib' directory of an AspectJ installation, otherwise you can download them from the internet.

- aspectjrt.jar
- aspectjweaver.jar
- aspectj.jar
- aopalliance.jar

##### **Declaring an aspect**

Aspects classes are like any other normal bean and may have methods and fields just like any other class, except that they will be annotated with **@Aspect** as follows:
```
package org.xyz;

import org.aspectj.lang.annotation.Aspect;

@Aspect
public class AspectModule {

}
```
They will be configured in XML like any other bean as follows:
```
<bean id="myAspect" class="org.xyz.AspectModule">
   <!-- configure properties of aspect here as normal -->
</bean>
```
##### **Declaring A Pointcut**
Aspects classes are like any other normal bean and may have methods and fields just like any other class, except that they will be annotated with **@Aspect** as follows:
A pointcut helps in determining the join points (ie methods) of interest to be executed with different advices. While working with @AspectJ based configuration, pointcut declaration has two parts:

 - A pointcut expression that determines exactly which method executions we are interested in. 
 -  A pointcut signature comprising a name and any number of parameters. The actual body of the method is irrelevant and in fact should be empty.

The following example defines a pointcut named 'businessService' that will match the execution of every method available in the classes under the package com.xyz.myapp.service:
```
import org.aspectj.lang.annotation.Pointcut;

@Pointcut("execution(* com.xyz.myapp.service.*.*(..))") // expression 
private void businessService() {}  // signature
```

The following example defines a pointcut named 'getname' that will match the execution of getName() method available in Student class under the package com.example:

```
import org.aspectj.lang.annotation.Pointcut;

@Pointcut("execution(* com.example.Student.getName(..))") 
private void getname() {}
```

##### **Declaring advices**
You can declare any of the five advices using **@{ADVICE-NAME}** annotations as given below. This assumes that you already have defined a **pointcut** signature method called **businessService()**:

```
@Before("businessService()")
public void doBeforeTask(){
 ...
}

@After("businessService()")
public void doAfterTask(){
 ...
}

@AfterReturning(pointcut = "businessService()", returning="retVal")
public void doAfterReturnningTask(Object retVal){
  // you can intercept retVal here.
  ...
}

@AfterThrowing(pointcut = "businessService()", throwing="ex")
public void doAfterThrowingTask(Exception ex){
  // you can intercept thrown exception here.
  ...
}

@Around("businessService()")
public void doAroundTask(){
 ...
}
```
You can define you *pointcut* **inline** for any of the advices. Below is an example to define **inline pointcut** for before advice:
```
@Before("execution(* com.xyz.myapp.service.*.*(..))")
public doBeforeTask(){
 ...
}
```