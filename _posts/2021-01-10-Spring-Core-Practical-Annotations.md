---
title: 'Spring Core - Annotations and Pure Java Code configuration'
date: 2021-01-10 00:00:00
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

## Sample project with Annotations and Pure Java Code
Now that we know how to create a project which uses an <b>XML</b> file as a starting point for app configuration and beans registration, we will take a step further and learn a more modern approach - <b>no XML at all</b>!

We will use the same example that we've set up in the last post (if you don't know what I'm talking about, go check my previous post and comeback shortly) :

<div class="gallery" data-columns="1">
	<img src="{{site.baseurl}}/images/posts/springCorePractical/project_structure.png">
</div>

So at this point, I assume that our project template is ready and steady, so we continue and dive into coding.

## Step 1: Pure Java Code Configuration File
Instead of creating an <span class="inline_text">ApplicationContext.xml</span> configuration file, we will come up with a basic java class named
<span class="inline_text">AppConfig.java</span>, which we will put in the <span class="inline_text">configuration</span> package:

<div class="gallery" data-columns="1">
	<img src="{{site.baseurl}}/images/posts/springCorePracticalNoXML/app_config.png">
</div>

Then we should add some code to it, so our app configuration would become complete:

<hr>

#### AppConfig.java
```java
package com.matthew.springcore.configuration;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;


@Configuration
@ComponentScan("com.matthew.springcore")
public class AppConfig
{
    
}
```
<hr>

You might think now - how is it possible, that's all we have to do?

Not at all - we are just getting started. 

But because of using annotations, which we will put directly in our java class implementations to register beans,
the only thing we have to do currently is let <b>Spring Container</b> recognize these annotations 
by explicitly stating in which packages he should look after them. 

In our case it is <span class="inline_text">com.matthew.springcore</span> .

To be more specific, let's briefly discuss used annotations:
* <b><span style="color: black;">@Configuration</span></b> - it lets <b>Spring</b> know, that he should treat 
<span class="inline_text">AppConfig.java</span>, not like any other ordinary class, but more as a configuration file.
* <b><span style="color: black;">@ComponentScan</span></b> - we are telling <b>Spring</b>, in which packages he should look after annotations
(as explained above).

## Step 2: Annotating java class implementations
The next step covers the way in which we will make our java classes to be recognized as beans by <b>Application Context</b>.

To achieve that, we should modify our classes (<b>implementations, not interfaces!</b>) by annotating them with <span class="inline_text">@Component</span>:

<hr>

#### Chassis.java
```java
package com.matthew.springcore.dependencies;

import org.springframework.stereotype.Component;

@Component
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

import org.springframework.stereotype.Component;

@Component
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

import org.springframework.stereotype.Component;

@Component
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

#### Ferrari.java
```java
package com.matthew.springcore.outsourced;

import com.matthew.springcore.dependencies.CarComponent;
import org.springframework.stereotype.Component;

@Component
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

That's how <b>Spring</b> will know to register them as beans!

Nonetheless, the question arises - what is an <span class="inline_text">id</span> of such as created bean?

Without <span class="inline_text">id</span>, we can't even use it later, but we are never providing any distinctive name to be used by bean - 
so how it works?

<b>Spring</b> has a simple mechanism that creates a distinctive <span class="inline_text">id</span> of a bean by getting the name of the class which has been 
annotated with <span class="inline_text">@Component</span> and translating it to the <span class="inline_text">camelCase</span> format.

For example - if we want to retrieve our <span class="inline_text">Ferrari.java</span> class bean, we will use <span class="inline_text">ferrari</span>
as an <span class="inline_text">id</span>. 

If the class name consists of a couple of words, then it would still work:
* <span class="inline_text">SomeClassNameExample</span> - class <b>name</b>.
* <span class="inline_text">someClassNameExample</span> - <b>id</b> of this class bean.

## Step 3: Dependency Injection with Annotations

<hr>

#### Quick reminder:

1. <b><span style="color: black;">Setter injection</span></b> - we create a setter method and use it to inject the dependency.
2. <b><span style="color: black;">Constructor injection</span></b> - we create a constructor that takes an argument of dependency type and injects it.
3. <b><span style="color: black;">Field injection</span></b> - not possible with <b>XML</b>, but allowed by using annotations. 
<hr>

We must modify our <span class="inline_text">Ferrari.java</span> class once again, this time focusing on providing the capability of the dependency injection:

<hr>

```java
package com.matthew.springcore.outsourced;

import com.matthew.springcore.dependencies.CarComponent;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Component;

@Component
public class Ferrari implements Car
{
    
    private CarComponent wheels;
    
    private CarComponent chassis;
    
    // injecting 1 dependency object using FIELD injection
    @Autowired
    @Qualifier("doors")
    private CarComponent doors;
    
    
    // injecting 1 dependency object using CONSTRUCTOR injection
    @Autowired
    public Ferrari( @Qualifier("chassis")CarComponent chassis)
    {
        this.chassis = chassis;
    }
    
    
    // injecting 1 dependency object using SETTER METHOD injection
    @Autowired
    @Qualifier("wheels")
    public void setWheels( CarComponent wheels )
    {
        
        this.wheels = wheels;
    }
    
    
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
    
    @Override
    public CarComponent getWheels()
    {
        
        return wheels;
    }
    
    @Override
    public CarComponent getChassis()
    {
        
        return chassis;
    }
    
    @Override
    public CarComponent getDoors()
    {
        
        return doors;
    }
    
}
```
<hr>

Okey, this time, another two annotations appeared:
* <b><span style="color: black;">@Autowired</span></b> - we are injecting the dependency by “<b>Autowiring</b>” process - in simple words, <b>Spring</b> looks after registered bean in <b>Spring Container</b> which type matches dependency object type and try to inject it.
* <b><span style="color: black;">@Qualifier</span></b> - if there is a need, we can explicitly state which bean should be injected by providing
bean id.

In our project we have a <span class="inline_text">CarComponent.java</span> interface and three different implementations 
(<b>Doors.java</b>, <b>Wheels.java</b>, <b>Chassis.java</b>), so we can't
just use <span class="inline_text">@Autowired</span> and skip <span class="inline_text">@Qualifier</span> annotation. 

It would be only applicable if we've had one implementation so <b>Spring</b> would know by himself which registered bean to choose, but because
he has 3 beans of the <span class="inline_text">CarComponent</span> type, we must help him by providing the bean <span class="inline_text">id</span>
of chosen implementation!

<hr>
<div style="text-align:center; color: black; font-size: 25px; margin-top: 20px; margin-bottom: 20px;">REMEMBER</div>
<div style="text-align: center; color: black; font-size: 20px;"><b>bean id</b> == <b>class name in a camelCase format</b></div>
<hr>

Great job, our dependencies have been prepared to be injected, and we are good to go and try to retrieve <span class="inline_text">ferrari</span> bean.

## Step 4: Application Test
Let's open our <span class="inline_text">MainApp.java</span> class and get bean:

<hr>

#### MainApp.java
```java
package com.matthew.springcore;

import com.matthew.springcore.configuration.AppConfig;
import com.matthew.springcore.outsourced.Car;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class MainApp
{
    public static void main(String... args)
    {
        // as config file we use "AppConfig.java" class
        AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext( AppConfig.class);
        
        Car ferrari = context.getBean( "ferrari", Car.class );
        
        System.out.println(ferrari.getWheels().carComponentName());
        System.out.println(ferrari.getDoors().carComponentName());
        System.out.println(ferrari.getChassis().carComponentName());
    }
}
```
<hr>

When we run it:

<div style="text-align: center; color: black;"><b>Output:</b></div>
<div class="gallery" data-columns="1">
    <img src="{{site.baseurl}}/images/posts/springCorePracticalNoXML/output_test.png">
</div>

Everything works; dependencies have been injected successfully!

But let's try one more thing...

## Step 5: Registering external class bean
This whole time we’ve been working with classes that have been written by ourselves, but what if we would want to register some external 
class object as bean and use it in our app?

Your first thought might be - let's annotate it with <span class="inline_text">@Component</span>.

We can't do this!

It’s not our class, we are not allowed to modify its source code, so we can't add <span class="inline_text">@Component</span> annotation.

In a couple of minutes, I will show you how to tackle this problem by expanding our <span class="inline_text">Ferrari.java</span> class with one more dependency.

Let's say we want to be able to generate a random license plate number for our <span class="inline_text">Ferrari</span> object.

We will add a couple of lines to the 3 files: 
* <b><span style="color: black;">Car.java</span></b>
* <b><span style="color: black;">Ferrari.java</span></b>
* <b><span style="color: black;">AppConfig.java</span></b>

Modified source codes of our classes:

<hr>

#### Car.java
```java
package com.matthew.springcore.outsourced;

import com.matthew.springcore.dependencies.CarComponent;

import java.util.Random;

public interface Car
{
    void startDriving();
    
    void stop();
    
    CarComponent getWheels();
    CarComponent getDoors();
    CarComponent getChassis();
    Random getRandomLicensePlateNumber();
}
```
<hr>

#### Ferrari.java
```java
package com.matthew.springcore.outsourced;

import com.matthew.springcore.dependencies.CarComponent;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Component;

import java.util.Random;

@Component
public class Ferrari implements Car
{
    // injecting external class bean
    @Autowired
    private Random randomLicensePlateNumber;
    
    private CarComponent wheels;
    
    private CarComponent chassis;
    
    // injecting 1 dependency object using FIELD injection
    @Autowired
    @Qualifier("doors")
    private CarComponent doors;
    
    
    // injecting 1 dependency object using CONSTRUCTOR injection
    @Autowired
    public Ferrari( @Qualifier("chassis")CarComponent chassis)
    {
        this.chassis = chassis;
    }
    
    
    // injecting 1 dependency object using SETTER METHOD injection
    @Autowired
    @Qualifier("wheels")
    public void setWheels( CarComponent wheels )
    {
        
        this.wheels = wheels;
    }
    
    
    @Override
    public Random getRandomLicensePlateNumber()
    {
        
        return this.randomLicensePlateNumber;
    }
    
    
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
    
    @Override
    public CarComponent getWheels()
    {
        
        return wheels;
    }
    
    @Override
    public CarComponent getChassis()
    {
        
        return chassis;
    }
    
    @Override
    public CarComponent getDoors()
    {
        
        return doors;
    }
    
}
```
<hr>

#### AppConfig.java
```java
package com.matthew.springcore.configuration;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

import java.util.Random;

@Configuration
@ComponentScan("com.matthew.springcore")
public class AppConfig
{
    // configuring external class bean of type "Random"
    @Bean
    public Random random(){return new Random();}
}
```
<hr>

To generate a random license plate number we are using <span class="inline_text">java.util.Random</span> class object, 
which is external class that we would want to register as bean and inject in our <span class="inline_text">Ferrari</span> object.

We've added the getter method so we will be able to retrieve this <span class="inline_text">Random</span> object dependency from
<span class="inline_text">Ferrari</span>.

And so, to be able to do this, we must add a field of <span class="inline_text">Random</span> type and inject the dependency.

As you can see, I've annotated it with <span class="inline_text">@Autowired</span>, and nothing else, because we will have only
one bean of <span class="inline_text">Random</span> type, so <b>Spring</b> will know which bean to inject.

Besides all of that, we haven't come up with a solution to our main concern - how to register the bean of some external class?

Focus now, because I will tell you how to do this.

The process of registering an external class bean consists of 2 steps:
1. In the class annotated with <span class="inline_text">@Configuration</span>, create a method that will return an object which we want to register as a bean 
( in our case, object of <span class="inline_text">Random</span> type ).
2. Annotate this method with <span class="inline_text">@Bean</span>, which will tell <b>Spring</b>, that the returned object should get registered as bean, and put for use
in <b>Spring Container</b>.

That's it, our external class bean is ready to be used.

Thanks to this, we are allowed to auto-wire this <span class="inline_text">Random</span> object in our <span class="inline_text">Ferrari.java</span> class,
because there is only one bean of <span class="inline_text">Random</span> type.

## Step 6: Application Test 2
Let's test our app again, this time to see if the external class (<span class="inline_text">Random</span>) bean has been injected properly:

<hr>

#### MainApp.java
```java
package com.matthew.springcore;

import com.matthew.springcore.configuration.AppConfig;
import com.matthew.springcore.outsourced.Car;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class MainApp
{
    public static void main(String... args)
    {
        AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext( AppConfig.class);
        
        Car ferrari = context.getBean( "ferrari", Car.class );
        
        System.out.println(ferrari.getWheels().carComponentName());
        System.out.println(ferrari.getDoors().carComponentName());
        System.out.println(ferrari.getChassis().carComponentName());
        System.out.println(ferrari.getRandomLicensePlateNumber().nextInt(90000) + 10000);
    }
}
```
<hr>

When we run it:

<div style="text-align: center; color: black;"><b>Output:</b></div>
<div class="gallery" data-columns="1">
    <img src="{{site.baseurl}}/images/posts/springCorePracticalNoXML/output_test2.png">
</div>

Everything works again, nothing is broken!

It’s taken us a hell of a journey to get there, but you've made it, and hopefully, you've got a clear understanding of how to adapt your app so it could use the more modern approach of <span class="inline_text">Inversion of Control & Dependency Injection</span> provided by <b>Spring</b>.  

See you in the near future!

<hr>

### Github resources:
- Maven project used in this post: <a href="https://github.com/matthew01lokiet/Blog-posts-resources/tree/main/springCoreProjects/SpringCoreNoXML">Link</a>












