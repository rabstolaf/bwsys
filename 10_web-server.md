<!---
<pre><code>    </code></pre> are used to create long code snipets that are to be executed or inserted
<br> leaves an empty line
&#60; and &#62; are used to show < and > respectively.
-->
[**PREVIOUS: Introduction to Kubernetes**](09_Kubernetes.md) 

# Setting up a Django Webserver [HiPerCiC]
This section is dedicated to the creation of a Django App using PostgreSQL, which is the base for HiPerCiC in St. Olaf.
First of all, Django is an open-source framework for creating web applications that was created and is maintained and updated by the Django Software Foundation. It is based on Python (and you will be working with Python in order to create a proper app) and uses the Model-Template-View model for its architecture (not too critical to understand, but still might be useful). Here is a small summary of what they mean:
* **Model**: Handles how the data is handled from and to the database as well as how the web app is supposed to be structured. 
* **Template**: Pretty much the HTML that dictates how a website will look like. 
* **View:** Responsible for the interactivity between the user and the app. Gets and gives data to the Model and presents the right webpage based on the Templates

PostgreSQL is a robust database that can be used as the database for Django. Django defaults to SQLite, but sometimes it cannot undertake tasks with greater demands, unlike PostgreSQL
**HiPerCiC: HIgh PERformance Computing In Context.** Essentially a way for professors in St. Olaf to be able to ccreate various web apps. HiPerCiC includes more things than just Django and PostgreSQL, and there are many versions of it, each with its own little details. But that is beyond the basics.

<br><br>

## 1. Making the required installations
In order to get started things need to install the required packages. Run:

<pre><code>sudo apt update
sudo apt install python-pip python-dev libpq-dev postgresql postgresql-contrib libpq-dev python3-dev</code></pre>

This will download the required packages for PostgreSQL.
<br>
After that, run:

<pre><code>pip install django psycopg2 </code></pre>

Django is python based, so we install it from the online repository for packages that is provided by Python (instead of the repository provided by apt). Psycopg2 is a tool that essentially helps django communicate with PostreSQL. 
Now that the required installations are done, we will start configurations.

<br><br>

## 2. Setting up the Database
Unfortunately, just installing the database package isn't enough. In this section, we will create a database and configure it.
Enter the shell for configuring PostgreSQL through the `postgres` user, using:

<pre><code> sudo -u postgres psql </code></pre>

You shell should nnow be looking something like this: `postgres=#`
After that run these commands (&#60;variable&#62; mean insert the thing of your choice here and don't include the <> in your code):

<pre><code>CREATE DATABASE &#60;postgres database name&#62;;
CREATE USER &#60;postgres user&#62; WITH PASSWORD '&#60;postgres password&#62;';
ALTER ROLE &#60;postgres user&#62; SET client_encoding TO 'utf8';
ALTER ROLE &#60;postgres user&#62; SET default_transaction_isolation TO 'read committed';
ALTER ROLE &#60;postgres user&#62; SET timezone TO 'UTC';
GRANT ALL PRIVILEGES ON DATABASE #60;postgres database name&#62; TO &#60;postgres user&#62;;
\q</code> </pre>

`CREATE DATABASE` creates the database, and `CREATE USER` creates the user. `ALTER ROLE` commands are generally advised for creating a Django PostgreSQL database. Finally `GRANT ALL PRIVILEDGES` will allow the user you created to acces the database. 
Also take note of the database name the user and the password, as those will be needed later.

<br><br>

## 3. Setting up and Launching Django 
Create a directory to house your project and enter it (by now this should be easy). From now on the tutorial will assume that you will be in this directory at all times.
To create a django project run:

<pre><code>django-admin.py startproject &#60;app name&#62;&#60;app name&#62; .</code></pre>

This will create a django app project in the project directory with the name that you specified in &#60;app name&#62;
<br>
Now create a user for webpage administration using:

<pre><code>python manage.py createsuperuser</code></pre>

`python manage.py` runs the script in the project directory allowing control over the apps. Also, you can ignore the email section.

<br>
At this stage we COULD launch our app, but why not make some configurations to connect it to our database?

<br>
Open and modify the `&#60;app name&#62;/settings.py` to change the settings in your app.
At some point, you will see `ALLOWED_HOSTS = []`. Change this line to `ALLOWED_HOSTS = ['*']`
This allows the app to be accessed using any IP address that the VM uses to connect to the Internet. You COULD specify the St. Olaf facing IP address using `ip ad` to find the address, but that requires closing and reopening the file, making the process more tedious with little benefit. Though for future reference, if an application is launched, this might be useful to know.
At some point, you will see something like this in the file.

<pre><code>DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    }
}</code></pre>

Change it to this:

<pre><code>DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql_psycopg2',
        'NAME': '&#60;postgres database name&#62;',
        'USER': '&#60;postgres user&#62;',
        'PASSWORD': '&#60;postgres password&#62;',
        'HOST': 'localhost',
    }
}</code></pre>

Now save and close the file
<br>

Now run these 2 commands:

<pre><code>python manage.py makemigrations
python manage.py migrate</code></pre>

`makemigrations` is used to find and take note of any changes in the apps. `migrate` is used to apply those changes.
<br>

It's finally time to run our application. Run:

<pre><code>python manage.py runserver 0.0.0.0:8000</pre></code>

`0.0.0.0:8000` makes the app accessible to other computers. Without it, you would need to access it via `127.0.0.1:8000` from your VM only, and assuming that for this training you created a server, this would be no fun. If you don't know the VM's St. Olaf facing interface and its IPv4, use CONTROL+C and `ip ad` to find it. Then run the above command again. Now in, your computer, open up a browser and access the django app using `<St. Olaf facing IP>:8000`
<br>

Congratulations! You just created your very own Django app. Fun fact: `<St. Olaf facing IP>:8000/admin ` takes you to the administration page where you can log in with your super user credentials.


[**Beginning: README**](README.md)
