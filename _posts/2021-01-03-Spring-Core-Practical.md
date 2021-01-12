---
title: 'Spring Core - Sample project with XML configuration'
date: 2021-01-03 00:00:00
description: Practical introduction to the Spring Core main functionalities - Inversion of Control & Dependency Injection.
featured_image: '/images/posts/springCorePractical/spring_core_practical.png'
tags: spring java programming
---

### Prerequisites:
* Spring Core basic theoretical knowledge - <span class="inline_text">Spring Container, Beans, IoC, DI</span> 
* Basic java programming and maven knowledge

### What I will use:
* IDE - <span class="inline_text">IntelliJ IDEA</span>
* Project management tool - <span class="inline_text">Maven</span>

## Sample project with XML
In the last post, I've tackled the topic of <span class="inline_text">Inversion of Control</span> and <span class="inline_text">Dependency Injection</span>,
and how it works in <span class="inline_text">Spring</span> internally.
But I didn't say anything about how we can use it directly in our java code.

Sorry that I've kept you waiting, but for me, it's logical that at first, you must know how something works before you can make use of it!

<span class="inline_text">Spring Core</span> gives us 3 ways of how we can implement his functionalities in our app:
* With the usage of the <span class="inline_text">XML</span> config file
* With the usage of the <span class="inline_text">Annotations</span>
* Using <span class="inline_text">Pure Java Code</span>

We will walk through each of these, step by step, using a simple example:
1. Create a basic java interface named <span class="inline_text">Car.java</span>.
2. Create an implementation of this interface named <span class="inline_text">Ferrari.java</span>.
3. Create a basic java interface named <span class="inline_text">CarComponent.java</span>.
4. Create 3 implementations of this interface named respectively: <span class="inline_text">Wheels.java</span>, <span class="inline_text">Chassis.java</span>, 
<span class="inline_text">Doors.java</span>.

The <span class="inline_text">Ferrari.java</span> class will serve us as the object which creation we want to outsource (<b>Inversion of Control</b>), and we will treat the
<span class="inline_text">CarComponent</span> implementations as the objects which should be injected into our <span class="inline_text">Ferrari</span>
object (<b>Dependency Injection</b>).

We must take into consideration one more topic - types of how dependencies can be injected:
1. <b><span style="color: black;">Setter injection</span></b> - we create a setter method and use it to inject the dependency.
2. <b><span style="color: black;">Constructor injection</span></b> - we create a constructor that takes an argument of dependency type and injects it.
3. <b><span style="color: black;">Field injection</span></b> - not possible with XML, but allowed by using annotations. 

## Step 1: Create project example

Let's open <span class="inline_text">IntelliJ IDEA</span> and create an empty (no archetypes) maven project:

<div class="gallery" data-columns="1">
	<img src="{{site.baseurl}}/images/posts/springCorePractical/maven_project_creation.png">
</div>

I will name it <span class="inline_text">SpringCoreXML</span>:

<div class="gallery" data-columns="1">
	<img src="{{site.baseurl}}/images/posts/springCorePractical/maven_project_creation2.png">
</div>

After the project has been created, go to the <span class="inline_text">pom.xml</span> file and add needed dependencies:

<div class="gallery" data-columns="1">
	<img src="{{site.baseurl}}/images/posts/springCorePractical/maven_dependencies.png">
</div>

As you can see, we have added 3 dependencies:
1. <b><span style="color: black;">spring-core</span></b> - provides core <b>Spring</b> libraries so it can function properly.
2. <b><span style="color: black;">spring-beans</span></b> - as you can imagine, it gives us libraries needed to create and register beans.
3. <b><span style="color: black;">spring-context</span></b> - last but not least, thanks to these libs, we are allowed to use <b>Spring Container</b> to manage beans.

Now we should focus on creating interfaces and classes.

A little snippet of how our project structure should look like after we have finished:

<div class="gallery" data-columns="1">
	<img src="{{site.baseurl}}/images/posts/springCorePractical/project_structure.png">
</div>

Short description:
* <b><span style="color: black;">com.matthew.springcore</span></b> - a package that consists of everything (Interfaces, Classes), which we are using in this example.
* <b><span style="color: black;">dependencies</span></b> - package in which I have put our <span class="inline_text">CarComponent.java</span> interface and its implementations, which will be injected in <span class="inline_text">Ferrari</span> object. 
* <b><span style="color: black;">outsourced</span></b> - in this package, I have put our <span class="inline_text">Car.java</span> interface and its <span class="inline_text">Ferrari.java</span> implementation.
* <b><span style="color: black;">MainApp.java</span></b> - class in which we will be requesting and retrieving beans from <b>Spring Container</b>.

#### You can download the whole project from my Github repository (link on the bottom of this page).
I will start from the <span class="inline_text">dependencies</span> package; here are source codes:

<hr>

#### CarComponent.java
```java
package com.matthew.springcore.dependencies;

public interface CarComponent
{
    String carComponentName();
}
```
<hr>

#### Chassis.java
```java
package com.matthew.springcore.dependencies;

public class Chassis implements CarComponent
{
    
    @Override
    public String carComponentName()
    {
        
        return "Chassis";
    }
    
}
```
<hr>

#### Doors.java
```java
package com.matthew.springcore.dependencies;

public class Doors implements CarComponent
{
    
    @Override
    public String carComponentName()
    {
        
        return "Doors";
    }
    
}
```
<hr>

#### Wheels.java
```java
package com.matthew.springcore.dependencies;

public class Wheels implements CarComponent
{
    
    @Override
    public String carComponentName()
    {
        
        return "Wheels";
    }
    
}
```
<hr>

And now to the <span class="inline_text">outsourced</span> package:

<hr>

#### Car.java
```java
package com.matthew.springcore.outsourced;

import com.matthew.springcore.dependencies.CarComponent;

public interface Car
{
    void startDriving();
    
    void stop();
    
    CarComponent getWheels();
    CarComponent getDoors();
    CarComponent getChassis();
}
```
<hr>

#### Ferrari.java
```java
package com.matthew.springcore.outsourced;

import com.matthew.springcore.dependencies.CarComponent;

public class Ferrari implements Car
{
    
    private CarComponent wheels;
    
    private CarComponent chassis;
    
    private CarComponent doors;
    
    
    @Override
    public void startDriving()
    {
    
        System.out.println("Ferrari engine started, driving...");
    }
    
    @Override
    public void stop()
    {
    
        System.out.println("Ferrari engine stopped, parking...");
    }

    public CarComponent getWheels()
    {
        
        return wheels;
    }
    
    
    public CarComponent getChassis()
    {
        
        return chassis;
    }
    
    
    public CarComponent getDoors()
    {
        
        return doors;
    }
    
}
```
<hr>

Our project template has been created; we will use it throughout this post from now!

For convenience, you can create a copy of this project, so you'll be able to bootstrap it to be used for later examples (<b>Annotations</b>, <b>Pure Java Code</b>).

## Step 2: IoC & DI with XML
Having our project open, go to the <span class="inline_text">resources</span> folder and create <span class="inline_text">applicationContext.xml</span> file:

<div class="gallery" data-columns="1">
	<img src="{{site.baseurl}}/images/posts/springCorePractical/resources_app_context.png">
</div>

We will use it to register beans and setup dependency injection capabilities.

Let's open this and add the given lines so it would become recognized by our <b>Spring Container</b>:

<div class="gallery" data-columns="1">
	<img src="{{site.baseurl}}/images/posts/springCorePractical/config_file_bootstrap.png">
</div>

Now, the part for which we have been waiting for - <b>registering beans</b>!

We will start by registering the dependency objects:

<div class="gallery" data-columns="1">
	<img src="{{site.baseurl}}/images/posts/springCorePractical/dependencies_registering.png">
</div>

Our dependency objects beans consist of 2 properties:
* <b><span style="color: black;">id</span></b> - distinctive name that we choose for our bean, so it can be used later in the code.
* <b><span style="color: black;">class</span></b> - class definition path, so <b>Spring</b> knows how a given bean should get created.

Okay, now funny part. 

Let's modify our <span class="inline_text">Ferrari.java</span> class, so we can inject the dependencies:

#### Ferrari.java
```java
package com.matthew.springcore.outsourced;

import com.matthew.springcore.dependencies.CarComponent;

public class Ferrari implements Car
{
    
    private CarComponent wheels;
    
    private CarComponent chassis;
    
    private CarComponent doors;
    
    
    // injecting 2 dependency objects using CONSTRUCTOR injection
    public Ferrari(CarComponent chassis, CarComponent doors)
    {
        this.chassis = chassis;
        this.doors = doors;
    }
    
    
    // injecting 1 dependency object using SETTER METHOD injection
    public void setWheels( CarComponent wheels )
    {
        
        this.wheels = wheels;
    }
    
    
    
    @Override
    public void startDriving(){..}
    
    @Override
    public void stop(){..}
    
    @Override
    public CarComponent getWheels(){..}
    
    @Override
    public CarComponent getChassis(){..}
    
    @Override
    public CarComponent getDoors(){..}
    
}
```

Our class definition is ready; let's come back to the <span class="inline_text">applicationContext.xml</span> file.

Currently, what we must do, is register bean consisting of our <span class="inline_text">Ferrari.java</span> class object, which we want to get outsourced
and created by <b>Spring Container</b> :

<div class="gallery" data-columns="1">
	<img src="{{site.baseurl}}/images/posts/springCorePractical/config_file_ferrari_bean.png">
</div>

Our newly created bean consists of:
* <b><span style="color: black;">id</span></b> - mentioned earlier, the distinctive name which we give to our bean.
* <b><span style="color: black;">class</span></b> - path to the class definition, from which bean should be created.
* <b><span style="color: black;">constructor-arg</span></b> - we are telling <b>Spring</b> that we have set up a constructor, so dependency could be injected with it.
We also provide the name for <span class="inline_text">ref</span> property, so <b>Spring</b> knows which bean should be injected.
This name is nothing else but the <span class="inline_text">id</span> of the <b>dependency bean</b>!
* <b><span style="color: black;">property</span></b> - we are telling <b>Spring</b> that we have set up the setter method, so dependency could be injected with it.
We must also give it a <span class="inline_text">name</span> value, which should be the name of the field in class from which we
are creating bean (<b>wheels</b> field in the <b>Ferrari.java</b> class; go check it) and <span class="inline_text">ref</span> value which is the
<span class="inline_text">id</span> of our dependency bean.

The <span class="inline_text">applicationContext.xml</span> configuration file is ready, what's left is to write some code in <span class="inline_text">MainApp.java</span>
class, so we can see the effects of our work!

<hr>

#### MainApp.java
```java
package com.matthew.springcore;

import com.matthew.springcore.outsourced.Car;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class MainApp
{
    public static void main(String... args)
    {
    
        // We load our "applicationContext.xml" file and register defined beans
        ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
    
        
        // We are retrieving bean from Spring Container by issuing
        /*
            
            context.getBean( id_of_the_bean_in_applicationContext.xml_ , _interface_or_class_type_of_the_bean )
            
         */
        Car ferrari = context.getBean( "ferrari", Car.class );
    
        
        // We are checking that dependencies have been injected
        System.out.println(ferrari.getChassis().carComponentName());
        System.out.println(ferrari.getDoors().carComponentName());
        System.out.println(ferrari.getWheels().carComponentName());
        
    }
}
```
<hr>

When we run it:

<div style="text-align: center; color: black;"><b>Output:</b></div>
<div class="gallery" data-columns="1">
    <img src="{{site.baseurl}}/images/posts/springCorePractical/output.png">
</div>

That's it! 

Now we are sure, that our dependencies have been injected, so we haven't got any problems calling methods on objects on which our
outsourced <span class="inline_text">Ferrari</span> object depends on.

To compare, that's how would looks like <span class="inline_text">MainApp.java</span> class if we hadn't used <b>Spring Core</b> functionality:

<hr>

#### MainApp.java
```java
package com.matthew.springcore;

import com.matthew.springcore.dependencies.CarComponent;
import com.matthew.springcore.dependencies.Chassis;
import com.matthew.springcore.dependencies.Doors;
import com.matthew.springcore.dependencies.Wheels;
import com.matthew.springcore.outsourced.Ferrari;

public class MainApp
{
    public static void main(String... args)
    {
    
        CarComponent chassis = new Chassis();
        CarComponent doors = new Doors();
        CarComponent wheels = new Wheels();
        
        Ferrari ferrari = new Ferrari( chassis, doors );
        
        ferrari.setWheels( wheels );
    

        System.out.println(ferrari.getChassis().carComponentName());
        System.out.println(ferrari.getDoors().carComponentName());
        System.out.println(ferrari.getWheels().carComponentName());
        
    }
}
```

<hr>

In the next posts, I will continue what we have started today,
by showing the more modern approach to how we can implement <span class="inline_text">Inversion of Control & Dependency Injection</span> in our apps.

I've told you before to make a copy of this sample project because we will be operating on the same example, so you can come back later and
compare the "<b>legacy vs modern</b>" way of doing the same thing.

See you next time!

<hr>

### Github resources:
- Maven project used in this post: <a href="https://github.com/matthew01lokiet/Blog-posts-resources/tree/main/springCoreProjects/SpringCoreXML">Link</a>