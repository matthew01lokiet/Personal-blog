---
title: 'Spring Core - IoC & DI'
date: 2021-01-02 00:00:00
description: Theoretical overview of the Spring Core main functionalities - Inversion of Control & Dependency Injection.
featured_image: '/images/posts/springCoreIoC/spring_core_main.png'
tags: spring java programming
---

## Inversion of Control & Dependency Injection - Definitions
To begin with, let’s take a look at the official descriptions:

* <b><span style="color: black;">Inversion of Control</span></b> - The approach of outsourcing the construction and management of objects.
* <b><span style="color: black;">Dependency Injection</span></b> - A task involving the delivery of other objects to a given object, which depends on them.

Seems kinda confusing, isn't it?

But don't worry - learning programming from definitions is like learning linear algebra equations from Wikipedia.

To understand these mysterious terms, I will come up with a simple analogy that I hope will dispel your doubts:

<div class="gallery" data-columns="1">
	<img src="{{site.baseurl}}/images/posts/springCoreIoC/car_engineer_diagram.png">
</div>

But you might ask - how it relates to what we were talking about earlier?

Take a look at this and compare:

<div class="gallery" data-columns="1">
	<img src="{{site.baseurl}}/images/posts/springCoreIoC/car_engineer_modified.png">
</div>

Do you see it now?

The whole idea is - define how a given object should be created and care no more; it's someone's other job to create and manage it.

And this is where <span class="inline_text">Spring</span> shines - he is your good friend who wants to help and manage objects on your behalf!

But we should stop for a minute and explain two main topics related to <span class="inline_text">Spring Core</span>:
* <b><span style="color: black;">Spring Container</span></b> - known also as "<b>Application Context</b>", he creates and manages lifetime of the beans.
* <b><span style="color: black;">Bean</span></b> - java object created by <b>Spring Container</b> and managed by it. 

To sum up and end with a big picture of how <span class="inline_text">Spring Core</span> ecosystem helps our app:

<div class="gallery" data-columns="1">
	<img src="{{site.baseurl}}/images/posts/springCoreIoC/car_engineer_end.png">
</div>

But why even bother - such a complex system (not complex at all now when you know it, but still); where would you want to use it?

I will answer you - in almost every java application ranging from Big Web-Apps to the projects a little bigger than simple "Hello World"!

In the modern world, even basic ideas implementations can rapidly grow and become a large, unmanageable piece of code, and Spring helps
by letting us focus on our part of a job and him doing the rest.

Pros of this solution:
* <b><span style="color: black;">More manageable and configurable app.</span></b>
* <b><span style="color: black;">Clear data flow overview when you get hang of it.</span></b>
* <b><span style="color: black;">You focus on your job, Spring focuses on his job - complementary cooperation.</span></b>

There is one last thing we should take into the consideration - and this is <span class="inline_text">Bean Scope and Lifecycle</span>.

## Bean Scope and Lifecycle
As you remember, <span class="inline_text">Bean</span> is just a fancy name for the java object created and managed by <span class="inline_text">Spring Container</span>.
This "<b>managing</b>" (which you might think about like "<b>observing and keeping created bean alive</b>") can take many different forms.

To be more specific, Spring provides us with 5 different types of this "<b>bean lifetime management</b>" (known also as <span class="inline_text">Scope</span> in which beans exist):
1. <b><span style="color: black;">Singleton</span></b> - java object is created only once when we request bean, every other time we will request the bean again, we will get the same instance of the object.
Default bean scope, if we don't tell Spring explicitly to choose other one.
2. <b><span style="color: black;">Prototype</span></b> - java object is created not only once, but rather on every bean request; we won't get the same instance of the object again.
3. <b><span style="color: black;">Request</span></b> - only used for web apps. Scoped to the HTTP web request.
4. <b><span style="color: black;">Session</span></b> - only used for web apps. Scoped to the single HTTP web session.
5. <b><span style="color: black;">Global-Session</span></b> - only used for web apps. Scoped to the HTTP web session of your whole web-app.

For now, you should mainly focus on <span class="inline_text">Singleton</span> and <span class="inline_text">Prototype</span> scopes.
In simple words:
* <b><span style="color: black;">Prototype</span></b> is what you know from daily basis coding, where every time you create java object of a given class, you will get new instance.
* <b><span style="color: black;">Singleton</span></b> might be associated with a "Singleton Design Pattern", whose goal is to ensure that only one instance of a given class exist at the time.
For example, when you've initiated "Application" object, what sense does it make to create other instances of "Application", if one is all we need to have?

Furthermore, let's take a look at how the initiation and destruction process of bean looks like:

<div class="gallery" data-columns="1">
	<img src="{{site.baseurl}}/images/posts/springCoreIoC/bean_lifecycle.png">
</div>

As you can see, you are not entirely at the mercy of the <span class="inline_text">Spring</span> in the context of the <span class="inline_text">Bean Lifecycle</span> - you can provide two custom methods:
* <b><span style="color: black;">init()</span></b> method, which will be called moment before bean creation process finished. For instance, you can use it to log the date when the given bean has been created.
* <b><span style="color: black;">destroy()</span></b> method, which will be called moment before destroying bean and erasing it from Application Context. Useful, if you want to close some external resources that bean might use (for example: database connection).

<hr>
#### Quick note:
Your custom <span class="inline_text">destroy()</span> method only applies to the bean of <span class="inline_text">Singleton</span> scope; it can't be applied to the <span class="inline_text">Prototype</span>.
<hr>

That’s it, you have a full overview of how <b>Spring Core</b> functionalities help our app in terms of keeping everything clean and more configurable.

In the next post, I will focus on a practical aspect of how to implement what we have learned in real world java application.

See you next time!



