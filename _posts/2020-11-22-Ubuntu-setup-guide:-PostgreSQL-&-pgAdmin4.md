---
title: 'PostgreSQL & pgAdmin4'
date: 2020-11-22 00:00:00
description: Descriptive guide of how to set up a PostgreSQL database and pgAdmin4 in a local Ubuntu environment.
featured_image: '/images/posts/postgresqlAndPgAdmin4/postgres.jpg'
tags: database ubuntu postgresql
---


## Step 1 - Installing PostgreSQL
Let's refresh repositories packages and dependencies on the local machine:

```bash
    sudo apt update
```

Then go install PostgreSQL, including additional packages, so you won't miss any extra utilities and functionality in the future:

```bash
    sudo apt install postgresql postgresql-contrib
```

Resultingly you will see some output. PostgreSQL packages shall be installed by now. To see if the database service is running, use the given command:

```bash
    sudo systemctl status postgresql
```

<div style="text-align: center; color: black;"><b>Output:</b></div>
<div class="gallery" data-columns="1">
	<img src="{{site.baseurl}}/images/posts/postgresqlAndPgAdmin4/systemctl_output.png">
</div>

You can also check if your database is accepting incoming connections on the PostgreSQL default port (5432):


```bash
    sudo pg_isready
```

<div style="text-align: center; color: black;"><b>Output:</b></div>
<div class="gallery" data-columns="1">
    <img src="{{site.baseurl}}/images/posts/postgresqlAndPgAdmin4/accepting_connections.png">
</div>


<hr>

## Step 2 - Configuring database access
Currently, your database is running, but you won't be able to connect with the pgAdmin4 administration panel.
To achieve this, we must tinker for a moment with a database default configuration.


Locate file <span class="inline_text">pg_hba.conf</span> (it should be at 
<span class="file_path">/etc/postgresql/_version_of_your_postgresql_/main</span>)
and find these lines in it:

```
    #Database administrative login by Unix domain socket
    local   all              postgres                           peer
```

Then change to:

```
    #Database administrative login by Unix domain socket
    local   all              postgres                           trust
```

Now save, quit, and restart the service from a terminal:

```bash
    sudo systemctl restart postgresql
```

The newly edited configuration file will be loaded by PostgreSQL and you will be able to access the database with the default <span class="inline_text">postgres</span> account.

In a terminal run:

```bash
    psql -U postgres
```

You should get access to the database, which will be indicated by a screen like this:

<div class="gallery" data-columns="1">
	<img src="{{site.baseurl}}/images/posts/postgresqlAndPgAdmin4/postgres_out.png">
</div>

Now, without exiting, provide a password to secure access to your database by running:


```sql
    ALTER USER postgres with password '_your_password_here_';
```


Exit with:

```
    \q
```

Furthermore, locate <span class="inline_text">pg_hba.conf</span> again and change already edited line:

```
    #Database administrative login by Unix domain socket
    local   all              postgres                           trust
```

To:

```
    #Database administrative login by Unix domain socket
    local   all              postgres                           md5
```

Save, exit, and restart your database:

```bash
    sudo systemctl restart postgresql
```

Finally, log in with <span class="inline_text">postgres</span> account:

```bash
    psql -U postgres
```

You will be prompted for a password (which you've set up in previous steps).<br> 
Provide it; if everything goes correctly, you should get access to the database:

<div class="gallery" data-columns="1">
	<img src="{{site.baseurl}}/images/posts/postgresqlAndPgAdmin4/postgres_pass.png">
</div>

Exit with:

```
    \q
```

At this point, connection to your PostgreSQL server is secured, because you've set up a password for
<span class="inline_text">postgres</span> account, but you didn't create any other accounts that
can access the database.

#### Quick note:
We have used three methods of authentication when changing the <span class="inline_text">pg_hba.conf</span> file:
* <span class="inline_text">trust</span> - allows any connection to access the database.
* <span class="inline_text">peer</span> - allows connection where the client's operating system user name is the same as the database user name with access permission.
* <span class="inline_text">md5</span> - password-base authentication.

For more info check this <a href="https://www.postgresql.org/docs/12/auth-pg-hba-conf.html">link</a>.

<hr>


## Step 3 - Installing pgAdmin4
By default, pgAdmin4 isn't available in Ubuntu repositories. You must add it on your own.

You can achieve this by issuing given commands in the terminal:

```bash
    curl https://www.pgadmin.org/static/packages_pgadmin_org.pub | sudo apt-key add
    sudo sh -c 'echo "deb https://ftp.postgresql.org/pub/pgadmin/pgadmin4/apt/$(lsb_release -cs) pgadmin4 main" > /etc/apt/sources.list.d/pgadmin4.list && apt update'
```

Now you are ready to install:

```bash
    sudo apt install pgadmin4
```

#### Before proceeding, create an e-mail address, which will be used for creating a pgAdmin4 administrator panel account!
After installation you must go to <span class="file_path"> /usr/pgadmin4/bin </span> and run <span class="inline_text">setup-web.sh</span> script:

```bash
    cd /usr/pgadmin4/bin
    sudo ./setup-web.sh
```


It will configure the Apache Web server on your machine and enable the WSGI module.
Also, to access the pgAdmin4 panel, you will be asked to create an account:

<div class="gallery" data-columns="1">
	<img src="{{site.baseurl}}/images/posts/postgresqlAndPgAdmin4/pgadmin4_script.png">
</div>

After the script stopped running you will be able to access the pgAdmin4 panel through <br><span class="file_path"> 127.0.0.1/pgadmin4 </span> URL in your browser.

The landing page should look like this:

<div class="gallery" data-columns="1">
	<img src="{{site.baseurl}}/images/posts/postgresqlAndPgAdmin4/landing_page.png">
</div>

Log in with credentials you have set up in <span class="inline_text">setup-web.sh</span> script.

You will be redirected to the administration panel page:

<div class="gallery" data-columns="1">
	<img src="{{site.baseurl}}/images/posts/postgresqlAndPgAdmin4/redirect_pgadmin4.png">
</div>

We are near the end, do not retreat! 
The only thing you must do now is creating a connection between PostgreSQL running on your machine and already opened the pgAdmin4 administration panel.

To make things work, find the option for creating a new server connection:

<div class="gallery" data-columns="1">
	<img src="{{site.baseurl}}/images/posts/postgresqlAndPgAdmin4/server_creation.png">
</div>

On the newly opened window, provide a name for your server (whatever, it doesn’t affect anything important), then go to the next tab <span class="inline_text">Connection</span> :

<div class="gallery" data-columns="1">
	<img src="{{site.baseurl}}/images/posts/postgresqlAndPgAdmin4/creating_conn.png">
</div>

There as <span class="inline_text">Host name/address</span> you should input <span class="inline_text">localhost</span> and as <span class="inline_text">Username</span>
-> <span class="inline_text">postgres</span>. <span class="inline_text">Password</span> -> the credential used during alternation of PostgreSQL database on your machine (when you've been providing a password to secure database).

Everything should run smoothly, and after choosing your server from the list on the left, it will be populated with different properties associated with your running database:

<div class="gallery" data-columns="1">
	<img src="{{site.baseurl}}/images/posts/postgresqlAndPgAdmin4/ending_im.png">
</div>

#### Hurra!

It indicates that you've set up a connection between the pgAdmin4 administration panel and the PostgreSQL database on your local machine correctly.

From there, you can dive into the details of your database server, but for now, there is nothing to be interested in yet...

See you next time!













