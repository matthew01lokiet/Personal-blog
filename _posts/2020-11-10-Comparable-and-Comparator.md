---
title: 'Comparable & Comparator'
date: 2020-11-10 00:00:00
description: The way of providing sorting functionality to the objects of a custom class. 
featured_image: '/images/posts/comparator/comparator_main.png'
tags: java programming
---

### Prerequisites:
* Functional interfaces
* Lambda expressions

### What I will use:
* IDE - <span class="inline_text">IntelliJ IDEA</span>

## Java - Sorting Capabilities
Sorting is one of the most fundamental problems in the programming world.

Thankfully to the rich <b>Java</b> environment, we don't have to implement any sorting methods at all (of course if you are ambitious, you are allowed to do this).

Our job is to provide behaviour implementation, which states how two objects of the same type should be compared; in other words -
<b>why a given object should be put before another in an <span style="color: black;">array</span> or <span style="color: black;">java collection</span> respectively</b>.

We have 2 ways in which we can provide the implementation for this comparison:
* by implementing <span class="inline_text">Comparable\<T\></span> interface method <span class="inline_text">compareTo​(T o)</span> in class for which
we want to provide <b>sorting</b> functionality.
* by creating a custom lambda expression that complies with <span class="inline_text">Comparator\<T\></span> functional interface.

At first, we will focus on <span class="inline_text">Comparable\<T\></span> interface, then we will jump to <span class="inline_text">Comparator\<T\></span> functional interface, and in the end, we will state the pros and cons of both of these approaches.

But before all of this, let's create the most basic <b>command line</b> project and add <span class="inline_text">Employee.java</span> class, which will serve us as the
example with which we will initiate objects to be sorted later.

## Example Setup
We choose <b>Command Line App</b> project:

<div class="gallery" data-columns="1">
    <img src="{{site.baseurl}}/images/posts/comparator/command_line_cre.png">
</div>

Then we name it (in my case - <b>comparableAndComparator</b>):

<div class="gallery" data-columns="1">
    <img src="{{site.baseurl}}/images/posts/comparator/project_name.png">
</div>

After our project has been created, let's add <span class="inline_text">Employee.java</span> class.

Here is source code:

<hr>

#### Employee.java
```java
package com.matthew;

public class Employee
{
    // class fields
    private String name;
    private String lastName;
    private int age;
    private int salary;
    
    // constructor
    public Employee( String name, String lastName, int age, int salary )
    {
        
        this.name = name;
        this.lastName = lastName;
        this.age = age;
        this.salary = salary;
    }
    
    // default getters and 'toString' method for printing objects
    public String getName()
    {
        
        return name;
    }
    
    
    public String getLastName()
    {
        
        return lastName;
    }
    
    
    public int getAge()
    {
        
        return age;
    }
    
    
    public int getSalary()
    {
        
        return salary;
    }
    
    
    @Override
    public String toString()
    {
        
        return "Employee{" +
               "name='" + name + '\'' +
               ", lastName='" + lastName + '\'' +
               ", age=" + age +
               ", salary=" + salary +
               '}';
    }
    
}
```
<hr>

Your project structure should look like this:

<div class="gallery" data-columns="1">
    <img src="{{site.baseurl}}/images/posts/comparator/proj_struct.png">
</div>

## Implementing Interface - Comparable\<T\>
In order to sort objects of our <span class="inline_text">Employee.java</span> class by using <span class="inline_text">Comparable\<T\></span> interface, we must implement its <span class="inline_text">compareTo​(T o)</span> method in our class.

<hr>

<span style="color: black; font-weight: bold;">Quick Note:</span>

You might seem confused why we do not address <span class="inline_text">Comparable\<T\></span> interface as <b>Functional Interface</b> - after all, it only has one abstract method.

It is one of these rare cases where the interface becomes <b><u>unintentionally</u></b> the <b>Functional Interface</b>.

You don't have to believe me on my word, we can check it by having a quick look into <span class="inline_text">Comparable\<T\></span> declaration:

<div class="gallery" data-columns="1">
    <img src="{{site.baseurl}}/images/posts/comparator/src_comparable.png">
</div>

You see - no <span class="inline_text">@FunctionalInterface</span> annotation.

<hr>

To implement <span class="inline_text">compareTo​(T o)</span> method properly, we must first understand how <b>Java</b>
uses our provided implementation to sort things.

As you might have guessed, what's being used, is returned value from our method - in the case of <span class="inline_text">compareTo​(T o)</span> it is <b>int</b> value.

I hope you remember what has been told in the first paragraph of this post - <span style="color:black;">our job is not to sort things, but to provide behaviour with which Java will know how to differentiate between two objects and put one before another</span>!

So we have <b>3</b> cases when comparing objects:

1. The **first** object should be put **before the second** object.
2. The **second** object should be put **before the first** object.
3. Both objects are **equal** so **keep it as it is**.

These cases are handled in a very logical way - by returning <b>int</b> value from our method implementation, which matches given behaviour (my assumption is that we want to achieve sorting in ascending order):

1. For the **first** case - return a <b>*negative integer*</b> value.
2. For the **second** case - return a <b>*positive integer*</b> value.
3. For the **third** case - return <b>0</b>.

Straightforward, don't you think?

Our <span class="inline_text">Employee.java</span> class consist of <b>4</b> fields:

* private <b>String</b> name;
* private <b>String</b> lastName;
* private <b>int</b> age;
* private <b>int</b> salary;
 
My implementation states given order resolution:

1. Compare <b>names</b> of objects; if they are not the same, return.
2. Compare <b>salaries</b> of objects; if they are not the same, return.
3. Compare <b>ages</b> of objects; if they are not the same, return.
4. Compare <b>last names</b> of objects; if they are not the same, return.
5. If we have achieved this point, we are sure that both objects are equal; return <b>0</b>.

<span class="inline_text">String</span> class has already implemented <span class="inline_text">compareTo​(T o)</span> method, so we will use it for our convenience.

But we do not have this luxury when working with primitive <b>int</b> type.

Nonetheless, it won't be hard to achieve our goal.

Let's assume we have <b>2</b> objects - <b>employee1</b> and <b>employee2</b>.

Their <b>names</b> are equal, so we proceed and compare <b>salary</b> values.

This will work:

<hr>

```java

    // 'employee1.getSalary()' == 2500
    // 'employee2.getSalary()' == 2000

    int compSalaries = employee1.getSalary() - employee2.getSalary();

    // so 'compSalaries' == 500

    return compSalaries;
```

<hr>

It returns a <b>*positive integer*</b>, which matches the case where the **second** object should be put **before the first** object.

And this is exactly what we want - to put an object with a <b>smaller</b> salary before an object with a <b>higher</b> salary (ascending order).

Implementation of our overridden <span class="inline_text">compareTo​(T o)</span> method:

<hr>

```java
@Override
public int compareTo( Employee employee )
{
    
    // compare names lexicographically
    int compNames = this.getName().compareTo( employee.getName() );
    
    // if they are not the same, return
    if(compNames != 0 )
    {
        return compNames;
    }
    else
    {
        // compare salaries values
        int compSalaries = this.getSalary() - employee.getSalary();
        
        // if they are not the same, return
        if(compSalaries != 0)
            return compSalaries;
        else
        {
            // compare age values
            int compAge = this.getAge() - employee.getAge();
            
            // if they are not the same, return
            if(compAge != 0)
                return compAge;
            else
            {
                // compare last names lexicographically
                int compLastNames = this.getLastName().compareTo( employee.getLastName() );
                
                // if they are not the same, return
                if(compLastNames != 0)
                    return compLastNames;
                
            }
        }
    }
    
    // if we have reached this point, it means that all objects values are equal
    return 0;
}
```

<hr>

Take a moment to analyze this code and understand it fully.

Source code of <span class="inline_text">Employee.java</span> class at this point:

<hr>

```java
package com.matthew;

public class Employee implements Comparable<Employee>
{
    private String name;
    private String lastName;
    private int age;
    private int salary;
    
    
    public Employee( String name, String lastName, int age, int salary )
    {
        
        this.name = name;
        this.lastName = lastName;
        this.age = age;
        this.salary = salary;
    }
    
    
    // method of 'Comparable<T>' interface that we must to implement
    @Override
    public int compareTo( Employee employee )
    {
        
        // compare names lexicographically
        int compNames = this.getName().compareTo( employee.getName() );
        
        // if they are not the same, return
        if(compNames != 0 )
        {
            return compNames;
        }
        else
        {
            // compare salaries values
            int compSalaries = this.getSalary() - employee.getSalary();
            
            // if they are not the same, return
            if(compSalaries != 0)
                return compSalaries;
            else
            {
                // compare age values
                int compAge = this.getAge() - employee.getAge();
                
                // if they are not the same, return
                if(compAge != 0)
                    return compAge;
                else
                {
                    // compare last names lexicographically
                    int compLastNames = this.getLastName().compareTo( employee.getLastName() );
                    
                    // if they are not the same, return
                    if(compLastNames != 0)
                        return compLastNames;
                    
                }
            }
        }
        
        // if we have reached this point, it means that all objects values are equal
        return 0;
    }
    
    
    // default getters and 'toString' method for printing objects
    public String getName()
    {
        
        return name;
    }
    
    
    public String getLastName()
    {
        
        return lastName;
    }
    
    
    public int getAge()
    {
        
        return age;
    }
    
    
    public int getSalary()
    {
        
        return salary;
    }
    
    
    @Override
    public String toString()
    {
        
        return "Employee{" +
               "name='" + name + '\'' +
               ", lastName='" + lastName + '\'' +
               ", age=" + age +
               ", salary=" + salary +
               '}';
    }
    
    
}
```

<hr>

## Using Arrays.sort() and Collections.sort() methods

Now that we have implemented <span class="inline_text">Comparable\<T\></span> interface properly in our class, let's
use it to sort <span class="inline_text">Employee</span> objects.

For more readability, I would rename <span class="inline_text">Main.java</span> to <span class="inline_text">MainComparable.java</span> - we will use this file to present a <b>sorting</b> example.

As mentioned earlier, our job <b>is not</b> to sort things - it is something that <b>Java</b> will handle for us.

In order to tell <b>Java</b> that we want to sort our <b>Collection</b> / <b>Array</b> of objects, we will use two static methods ( <b>T</b> type symbolizes generic):

* <span class="inline_text">Arrays.sort( Object[] )</span> - <a href="https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/Arrays.html#sort(java.lang.Object%5B%5D)">link to documentation</a>
* <span class="inline_text">Collections.sort( List\<T\> )</span> - <a href="https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/Collections.html#sort(java.util.List)">link to documentation</a>

I've included links to documentation for given methods, but what you should focus on, are these lines - <span style="color:black;">'All elements in the array must implement the <b>Comparable</b> interface'</span> and <span style="color:black;">'All elements in the list must implement the <b>Comparable</b> interface'</span>.

That's why we have been working so hard so far - <b>Java</b> will only let us sort objects if the class of these objects implements <span class="inline_text">Comparable</span> interface.

In which case <b>Java</b> knows that we have overridden <span class="inline_text">compareTo​(T o)</span> method, which is being used to differentiate between objects and put them in the correct order.

Take a look at this simple example I've written down:

<hr>

#### MainComparable.java
```java
package com.matthew;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.Collections;
import java.util.List;

public class MainComparable
{

    public static void main(String... args) {
    
        // list initialization
        List< Employee > employeeList = new ArrayList<>( Arrays.asList
                (
                    new Employee( "James", "Bond", 25, 15000 ),
                    new Employee( "Jack", "Sparrow", 37, 2500 ),
                    new Employee( "Jack", "Daniels", 37, 2500 )
                ));
        
        System.out.println("\nCollection before sorting: \n");
        
        // before sorting
        for( Employee emp : employeeList )
        {
            System.out.println( emp );
        }
    
        // sorting objects of Employee class, which implements Comparable interface,
        // by providing List<Employee> object
        Collections.sort(employeeList);
    
        System.out.println("\nCollection after sorting: \n");
    
        // after sorting
        for( Employee emp : employeeList )
        {
            System.out.println(emp);
        }
        
        //////////////////////////////////////////////////////////////////////////////////
        
        // no Collection, just simple array initialization
        Employee[] employees = {
                new Employee( "James", "Bond", 25, 15000 ),
                new Employee( "Jack", "Sparrow", 37, 2500 ),
                new Employee( "Jack", "Daniels", 37, 2500 )
        };
    
        System.out.println("\nArray before sorting: \n");
        
        // before sorting
        for( Employee emp : employees )
        {
            System.out.println(emp);
        }
    
        // sorting objects of Employee class, which implements Comparable interface,
        // by providing Employee[] object
        Arrays.sort(employees);
    
        System.out.println("\nArray after sorting: \n");
    
        // after sorting
        for( Employee emp : employees )
        {
            System.out.println(emp);
        }
        
    }
}

```

<hr>

When we run it:

<hr>

<div class="gallery" data-columns="1">
    <img src="{{site.baseurl}}/images/posts/comparator/comparable_output.png">
</div>

<hr>

As you can see, everything works - our <span class="inline_text">compareTo​(T o)</span> method implementation has been used to sort objects in our custom order of resolution ( **name** -> **salary** -> **age** -> **last name** ).

But there is one problem with this <span class="inline_text">Comparable\<T\></span> approach - we have the only one hardcoded sorting order, which we can't change *in place* to achieve different result depending on ours needs (for example: descending order, some other order resolution).

To tackle this problem, I will go a step further and introduce you to the <span class="inline_text">Comparator\<T\></span> functional interface.

## Using Functional Interface - Comparator\<T\>
This time we won't modify <span class="inline_text">Employee.java</span> class - we can keep it as it is.

Let's create another java class in our project - <span class="inline_text">MainComparator.java</span>.

We will provide sorting behaviour by using overloaded versions of <b>Arrays.sort()</b> and
<b>Collections.sort()</b> methods ( <b>T</b> type symbolizes generic): 

* <span class="inline_text">Arrays.sort( T[], Comparator\<T\> )</span> - <a href="https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/Arrays.html#sort(T%5B%5D,java.util.Comparator)">link to documentation</a>
* <span class="inline_text">Collections.sort( List\<T\>, Comparator\<T\> )</span> - <a href="https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/Collections.html#sort(java.util.List,java.util.Comparator)">link to documentation</a>

As you can see, we are compelled to provide the second parameter of type <span class="inline_text">Comparator\<T\></span>, which is a
<b>Functional Interface</b> - this means instead of implementing an interface, we can create and use <b>lambda expression</b>!

This makes code more flexible, cause every time we want to sort objects of our class, we can provide different sorting behaviour in contrary to having only one hardcoded solution in <span class="inline_text">Employee.java</span> class 
(overridden <b>compareTo()</b> method).

Our custom <b>lambda expression</b> must match the signature of <span class="inline_text">Comparator\<T\></span> abstract method - 
<span class="inline_text">int compare( T , T )</span>.

So we need to remember about 2 things:
* **int** value should be returned
* both objects (arguments) should be of the same type

Now I will show you the source code of <span class="inline_text">MainComparator.java</span> class, where we are testing different sorting ways (I've outsourced them outside the <b>main()</b> method to make code more readable):

<hr>

```java
package com.matthew;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.Collections;
import java.util.List;

public class MainComparator
{
    public static void main(String... args)
    {
        // list initialization
        List< Employee > employeeList = new ArrayList<>( Arrays.asList
                (
                        new Employee( "James", "Bond", 25, 15000 ),
                        new Employee( "James", "Daniels", 37, 2500 ),
                        new Employee( "Jack", "Daniels", 25, 2500 )
                ));

        System.out.println("\nBefore sorting:\n");

        // before sorting
        for( Employee emp : employeeList )
        {
            System.out.println( emp );
        }

        System.out.println();

        ///////////////////////////////////////////////

        sortByLastNameAndFirstName( employeeList );
        System.out.println("Sorted by last name and first name alphabetically:\n");

        // after sorting 'sortByLastNameAndFirstName'
        for( Employee emp : employeeList )
        {
            System.out.println( emp );
        }

        System.out.println();

        ///////////////////////////////////////////////

        sortBySalaryAndAge( employeeList );
        System.out.println("Sorted by salary and age:\n");

        // after sorting 'sortBySalaryAndAge'
        for( Employee emp : employeeList )
        {
            System.out.println( emp );
        }

        System.out.println();

        ///////////////////////////////////////////////

        sortBySalaryAndAgeDescending( employeeList );
        System.out.println("Sorted by salary and age in descending order:\n");

        // after sorting 'sortBySalaryAndAgeDescending'
        for( Employee emp : employeeList )
        {
            System.out.println( emp );
        }

        System.out.println();


    }


    private static void sortByLastNameAndFirstName(List<Employee> employeeList)
    {
        // sorted with lambda expression, which uses 'Comparator' functional interface
        // sorted by last name and first name (in this order) alphabetically
        Collections.sort( employeeList, (emp1, emp2) ->{

            int compLastNames = emp1.getLastName().compareTo( emp2.getLastName() );

            if( compLastNames != 0)
                return compLastNames;
            else
                return emp1.getName().compareTo( emp2.getName() );

        } );
    }


    private static void sortBySalaryAndAge(List<Employee> employeeList)
    {
        // sorted with lambda expression, which uses 'Comparator' functional interface
        // sorted by salary and age (in this order) by comparing values
        Collections.sort(employeeList, (emp1, emp2) ->{

            int compSalaries = emp1.getSalary() - emp2.getSalary();

            if( compSalaries != 0)
                return compSalaries;
            else
                return emp1.getAge() - emp2.getAge();

        });
    }


    private static void sortBySalaryAndAgeDescending(List<Employee> employeeList)
    {
        // sorted with lambda expression, which uses 'Comparator' functional interface
        // sorted by salary and age in descending order
        Collections.sort(employeeList, (emp1, emp2) ->{

            int compSalaries = emp1.getSalary() - emp2.getSalary();

            // Only change -> inverse returned value by multiplying with -1
            if( compSalaries != 0)
                return compSalaries * (-1);
            else
                return (emp1.getAge() - emp2.getAge())*(-1);

        });
    }
}
```

<hr>

When we run it:

<hr>

<div class="gallery" data-columns="1">
    <img src="{{site.baseurl}}/images/posts/comparator/comparator_output.png">
</div>

<hr>

The reasoning is still the same - return <b>int</b> value, which matches chosen sorting behaviour.

But we are not forced to use only one sorting implementation from <span class="inline_text">Employee.java</span> - we are able 
to provide it *in place*, depending on our needs.

In our example there are 3 different sortings:
* by <b>last name</b> and <b>name</b>
* by <b>salary</b> and <b>age</b>
* by <b>salary</b> and <b>age</b> in descending order 

## Summary
To sum up - the most efficient way is to use <span class="inline_text">Comparable\<T\></span> and <span class="inline_text">Comparator\<T\></span> interfaces complementary.

Start by providing a <b>default sorting behaviour and embedding it into your class</b>, then if you feel it isn't enough when you want to sort objects in different manner - <b>create a custom lambda expression</b> on demand. 

I think that from now on you will feel more confident when it comes to sorting objects of your choice in <b>Java</b> programming environment!

<hr>

### Github resources:
- Project used in this post: <a href="https://github.com/matthew01lokiet/Blog-posts-resources/tree/main/comparableAndComparator">Link</a>


