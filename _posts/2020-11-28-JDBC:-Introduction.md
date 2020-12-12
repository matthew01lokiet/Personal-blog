---
title: 'JDBC - Practical Introduction'
date: 2020-11-28 00:00:00
description: A brief introduction to providing the capability of data flow between the application and chosen database.
featured_image: '/images/posts/jdbc/jdbc_main.png'
tags: database postgresql java programming
---

### Prerequisites:
* SQL syntax knowledge
* Knowing how to set up a simple database server (check my previous post)
* Basic java programming and maven knowledge

### What I will use:
* IDE - <span class="inline_text">IntelliJ IDEA</span>
* Database administration tool - <span class="inline_text">pgAdmin4</span>
* Database engine - <span class="inline_text">PostgreSQL</span>
* Project management tool - <span class="inline_text">Maven</span>

## JDBC - What is this all about?
In this post, I will focus more on the practical aspect of JDBC usage rather than the whole theory standing behind 'how this useful API is implemented underneath'. 

In short - JDBC is an API (as mentioned earlier) created by the Oracle Corporation, which allows us to connect our existing application to the running database instance by providing an easy-to-use abstraction layer with many useful methods.

#### To get an overview of how it works:
1. Choose which database engine suits you the most (PostgreSQL, MySQL, etc.), then go to their vendor website and look for <span class="inline_text">JDBC drivers</span>.
2. Download these drivers and include them in your java project.
3. Create and start an instance of your database.
4. Within java code, register acquired drivers using <span class="inline_text">DriverManager</span> class, by providing URL and properties associated with the given database engine and your running server (don’t worry, every major database vendor has its JDBC driver documentation to lookup needed parameters).
5. In return, you will get a <span class="inline_text">Connection</span> object, which you'll be able to use as an intermediator to process queries between your app and database.

I hope this quick introduction gave you some confidence, so you won't be afraid to dive into the coding!

## JDBC - Setting up the project and environment
To begin with, you should create a new <span class="inline_text">Maven project</span> inside the <span class="inline_text">IntelliJ IDEA</span> environment:

<div class="gallery" data-columns="1">
	<img src="{{site.baseurl}}/images/posts/jdbc/project_creation.png">
</div>

We won't be using any archetypes, so leave <span class="inline_text">Create from archetype</span> option blank and move on.
Name your project as you wish, it doesn't really matter.

The reason I wanted to use <span class="inline_text">Maven</span> is that it will save you some time by letting you include PostgreSQL JDBC drivers with the only couple of lines. Instead of explicit need to manually download drivers, just make use of the dependency system by changing <span class="inline_text">pom.xml</span> from this:

<div class="gallery" data-columns="1">
	<img src="{{site.baseurl}}/images/posts/jdbc/first_screen.png">
</div>

To this:

<div class="gallery" data-columns="1">
    <img src="{{site.baseurl}}/images/posts/jdbc/changed_screen.png">
</div>

Then reload project with <span class="inline_text">Maven</span> and voilà.

The drivers have been included in your application.

<div style="text-align:center; color: black; font-size: 25px; margin-top: 20px; margin-bottom: 20px;">🤔 But wait, aren't we missing something? 🤔</div>
How can you connect to the database, if this database doesn't even exist?

Let’s fix this by using the <span class="inline_text">pgAdmin4</span> panel to create the database, which will serve as an example to be used complementary with our app.

I’m using PostgreSQL instance running on my local machine, which I’ve set up in the previous post and as a means of access, I will use the previously mentioned pgAdmin4 administration tool through web panel on  <span class="file_path">127.0.0.1 / pgadmin4</span>  URL:

<div class="gallery" data-columns="1">
    <img src="{{site.baseurl}}/images/posts/jdbc/admin_page.png">
</div>

Let’s create a new database:

<div class="gallery" data-columns="1">
    <img src="{{site.baseurl}}/images/posts/jdbc/create_database.png">
</div>

I will name it <span class="inline_text">ExampleDatabase</span> (I know, very original):

<div class="gallery" data-columns="1">
    <img src="{{site.baseurl}}/images/posts/jdbc/created_database.png">
</div>

In our database, to simulate some real-life example, I will create a table consisting of the client’s info and name it <span class="inline_text">clients</span> by using <span class="inline_text">Query Tool</span>:

<div class="gallery" data-columns="1">
    <img src="{{site.baseurl}}/images/posts/jdbc/create_table.png">
</div>

<hr>

<div class="gallery" data-columns="1">
    <img src="{{site.baseurl}}/images/posts/jdbc/create_and_execute.png">
</div>

I believe the fields in the created table are self-explanatory, so I won’t cover them in detail.

After issuing a query, to see changes, move to the left panel and refresh the database:

<div class="gallery" data-columns="1">
    <img src="{{site.baseurl}}/images/posts/jdbc/refresh.png">
</div>

Now you should be able to see just created <span class="inline_text">clients</span> table with columns names:

<div class="gallery" data-columns="1">
    <img src="{{site.baseurl}}/images/posts/jdbc/after_exec.png">
</div>

But what’s the use of an empty table?

Let's populate it with something:

<div class="gallery" data-columns="1">
    <img src="{{site.baseurl}}/images/posts/jdbc/inserting_into.png">
</div>


## JDBC - Connection and Queries
Let’s come back to the previously created maven project.

Now that we have a running database, the only thing we should cover is how to access it from code.

To make things more readable and convenient, let’s write a simple class, which will wrap the data obtained from the <span class="inline_text">clients</span> table:

```java
public class Client
{

    //Number of field equals the number of columns in the 'clients' table
    private int id;
    private String first_name;
    private String last_name;
    private int age;
    private String phone_number;
        

    //Some getters and setters
    public int getId(){ return id;}
        
    public void setId(int id){ this.id = id;}
        
    public String getFirst_name(){ return first_name;}
        
    public void setFirst_name(String first_name){ this.first_name = first_name;}
        
    public String getLast_name(){ return last_name;}
        
    public void setLast_name(String last_name){ this.last_name = last_name;}
        
    public int getAge(){ return age;}
        
    public void setAge(int age){ this.age = age;}
        
    public String getPhone_number(){ return phone_number;}
        
    public void setPhone_number(String phone_number){ this.phone_number = phone_number;}
}
```

As you can see, the number of fields and their type of data in the <span class="inline_text">Client</span> class equal the number of columns and data in the <span class="inline_text">clients</span> table. So every time we fetch one record from the table, we will wrap it in a <span class="inline_text">Client</span> object. 

We are getting closer to our main goal...

As you remember (I hope), we must register our already included drivers with the <span class="inline_text">Driver Manager</span> class and create a <span class="inline_text">Connection</span> object with it, to make queries.

To accomplish this, let's create second class <span class="inline_text">TestingJDBC</span> and write a function in it, which will return a ready-to-use <span class="inline_text">Connection</span> object:

```java
class TestingJDBC
{

    public Connection createConnection() throws SQLException
    {
        //format -> "jdbc:_name_of_the_driver_://_ip_address_/name_of_your_database
        String url = "jdbc:postgresql://127.0.0.1/ExampleDatabase";

        //we must provide user login name and password
        Properties props = new Properties();
        props.setProperty("user","postgres");
        props.setProperty("password","_password_to_your_database");

        /*
            !!!Important Step!!!

            We are using the 'DriverManager' class static method

        'getConnection( String _url_to_connect_ , Properties _properties_of_connection_ )'
                
            to create a working 'Connection' object instance,
            which will allow us to issue queries.

        */
        return DriverManager.getConnection(url, props);
    }

}
```

Let's make use of this <span class="inline_text">Connection</span> object, by writing another method to fetch all the records
from the <span class="inline_text">clients</span> table with a query:

```java
class TestingJDBC
{

    public Connection createConnection() throws SQLException{ ... }
            

    public void fetchAllFromTableStatement(Connection connection) throws SQLException
    {
            
        //First of all, we must create a 'Statement' object
        Statement statement = connection.createStatement();
            
        //Then we execute the query with it, storing the result in a 'ResultSet' object
        ResultSet resultSet = statement.executeQuery("SELECT * FROM clients");
            
        //Because we are clever and we thought about it earlier,
        //we will wrap our returned set of records in 'List<Client> clients'
        List<Client> clients = new ArrayList<>();
            
        //As you might expect, we are fetching records one at the time
        while(resultSet.next())
        {
            Client client = new Client();
                
        /*
            we make use of predefined 'ResultSet' methods:
            
            -   getInt( _name_of_the_column_in_the_table_ );
            -   getString( _name_of_the_column_in_the_table );

        */
                
        /*
            The whole logic behind it:
                1. Get one record from the 'ResultSet' object.
                2. Create a 'Client' object to wrap this record data.
                3. Using setters of 'Client' class and predefined
                    methods of 'ResultSet' object, we are setting field
                    of created 'Client' object by getting data from the record,
                    which exist in a given column.  
        */

            client.setId(resultSet.getInt("client_id"));
            client.setFirst_name(resultSet.getString("first_name"));
            client.setLast_name(resultSet.getString("last_name"));
            client.setAge(resultSet.getInt("age"));
            client.setPhone_number(resultSet.getString("phone_number"));
                
            clients.add(client);
        }
            
        //We are printing our fetched list of clients
        System.out.printf("%20s%20s%20s%20s%20s\n\n", "client_id", "first_name", "last_name", "age", "phone_number");
            
        for(int i = 0; i < clients.size(); i++)
        {
            System.out.printf("%20d%20s%20s%20d%20s\n\n",
                    clients.get(i).getId(),
                    clients.get(i).getFirst_name(),
                    clients.get(i).getLast_name(),
                    clients.get(i).getAge(),
                    clients.get(i).getPhone_number());
        }
            

        //Don't forget to close objects!
        resultSet.close();
        statement.close();
    }    

}
```

Last thing to do - we must create <span class="inline_text">main()</span> function, so we can see the results of our work:

```java
public class TestingJDBC
{
    //Main function
    public static void main(String... args) throws SQLException
    {
            
        var jdbc = new TestingJDBC();

        //Creating connection            
        var connection = jdbc.createConnection();
            
        //Using function to fetch the records from the table 
        jdbc.fetchAllFromTableStatement(connection);
            
    }
        
    public Connection createConnection() throws SQLException{ ... }
        
    public void fetchAllFromTableStatement(Connection connection) throws SQLException{ ... }
            
}
```

When we run it:

<div style="text-align: center; color: black;"><b>Output:</b></div>
<div class="gallery" data-columns="1">
    <img src="{{site.baseurl}}/images/posts/jdbc/results.png">
</div>

And that's it, you have successfully connected to the database from a java code and made a query 
to gather records from the table.

From there, you can furthermore experiment with different queries (insert, delete, etc.),
so you can see that there is no difference in the mechanism of connection between the database and your app.

<hr>

### Github resources:
- Maven project used in my post: <a href="https://github.com/matthew01lokiet/Blog-posts-resources/tree/main/jdbc">Link</a>