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
