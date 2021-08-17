# Setting up a Django Webserver [HiPerCiC]
<p> This section is dedicated to the creation of a Django App using PostgreSQL, which is the base for HiPerCiC in St. Olaf. </p>
<p> First of all, Django is an open-source framework for creating web applications that was created and is maintained and updated by the Django Software Foundation. It is based on Python (and you will be working with Python in order to create a proper app) and uses the Model-Template-View model for its architecture (not too critical to understand, but still might be useful). Here is a small summary of what they mean: </p>
    <ul>
        <li> <strong>Model:</strong> Handles how the data is handled from and to the database as well as how the web app is supposed to be structured. </li>
        <li> <strong>Template:</strong> Pretty much the HTML that dictates how a website will look like. </li>
        <li> <strong>View:</strong> Responsible for the interactivity between the user and the app. Gets and gives data to the Model and presents the right webpage based on the Templates </li>
    </ul>
<p> PostgreSQL is a robust database that can be used as the database for Django. Django defaults to SQLite, but sometimes it cannot undertake tasks with greater demands, unlike PostgreSQL</p>
<p> <strong>HiPerCiC: HIgh PERformance Computing In Context.</strong> Essentially a way for professors in St. Olaf to be able to ccreate various web apps. HiPerCiC includes more things than just Django and PostgreSQL, and there are many versions of it, each with its own little details. But that is beyond the basics. </p>

<br><br>

<h3> Making the required installations </h3>
<p> In order to get started things need to install the required packages. Run: </p>

<pre><code>sudo apt update
sudo apt install python-pip python-dev libpq-dev postgresql postgresql-contrib libpq-dev python3-dev</code></pre>

<p> This will download the required packages for PostgreSQL. </p>
<br>
<p> After that, run: </p>

<pre><code>pip install django psycopg2 </code></pre>

<p> Django is python based, so we install it from the online repository for packages that is provided by Python (instead of the repository provided by apt). Psycopg2 is a tool that essentially helps django communicate with PostreSQL. </p>
<p> Now that the required installations are done, we will start configurations. </p>

<br><br>

<h3> Setting up the Database </h3>
<p> Unfortunately, just installing the database package isn't enough. In this section, we will create a database and configure it. </p>
<p> Enter the shell for configuring PostgreSQL through the <code> postgres </code> user, using: </p>

<pre><code> sudo -u postgres psql </code></pre>

<p> You shell should nnow be looking something like this: <code>postgres=#</code></p>
<p> After that run these commands (&#60;variable&#62; mean insert the thing of your choice here and don't include the &#60;&#62; in your code): </p>

<pre><code>CREATE DATABASE &#60;postgres database name&#62;;
CREATE USER &#60;postgres user&#62; WITH PASSWORD '&#60;postgres password&#62;';
ALTER ROLE &#60;postgres user&#62; SET client_encoding TO 'utf8';
ALTER ROLE &#60;postgres user&#62; SET default_transaction_isolation TO 'read committed';
ALTER ROLE &#60;postgres user&#62; SET timezone TO 'UTC';
GRANT ALL PRIVILEGES ON DATABASE #60;postgres database name&#62; TO &#60;postgres user&#62;;
\q</code> </pre>

<p> <code>CREATE DATABASE</code> creates the database, and <code>CREATE USER</code> creates the user. <code>ALTER ROLE</code> commands are generally advised for creating a Django PostgreSQL database. Finally <code>GRANT ALL PRIVILEDGES</code> will allow the user you created to acces the database. 
Also take note of the database name the user and the password, as those will be needed later. </p>

<br><br>

<h3> Setting up and Launching Django </h3>
<p> Create a directory to house your project and enter it (by now this should be easy). From now on the tutorial will assume that you will be in this directory at all times. </p>
<p>To create a django project run: </p>

<pre><code>django-admin.py startproject &#60;app name&#62;&#60;app name&#62; .</code></pre>

<p> This will create a django app project in the project directory with the name that you specified in &#60;app name&#62;</p>

<br>
<p> Now create a user for webpage administration using:</p>

<pre><code>python manage.py createsuperuser</code></pre>

<p><code>python manage.py</code> runs the script in the project directory allowing control over the apps. Also, you can ignore the email section. </p>

<br>
<p> At this stage we COULD launch our app, but why not make some configurations to connect it to our database?</p>
<br>
<p> Open and modify the <code> &#60;app name&#62;/settings.py </code> to change the settings in your app.</p>
<p> At some point, you will see <code>ALLOWED_HOSTS = []</code>. Change this line to <code>ALLOWED_HOSTS = ['*']</code><p>
<p> This allows the app to be accessed using any IP address that the VM uses to connect to the Internet. You COULD specify the St. Olaf facing IP address using <code> ip ad </code> to find the address, but that requires closing and reopening the file, making the process more tedious with little benefit. Though for future reference, if an application is launched, this might be useful to know. </p>
<p> At some point, you will see something like this in the file:</p>

<pre><code>DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    }
}</code></pre>

<p> Change it to this: </p>

<pre><code>DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql_psycopg2',
        'NAME': '&#60;postgres database name&#62;',
        'USER': '&#60;postgres user&#62;',
        'PASSWORD': '&#60;postgres password&#62;',
        'HOST': 'localhost',
    }
}</code></pre>

<p> Now save and close the file </p>

<br>
Now run these 2 commands:

<pre><code>python manage.py makemigrations
python manage.py migrate</code></pre>
<p><code>makemigrations</code> is used to find and take note of any changes in the apps. <code>migrate</code> is used to apply those changes. <p>

<br>
<p> It's finally time to run our application. Run:</p>

<pre><code>python manage.py runserver 0.0.0.0:8000</pre></code>

<p><code>0.0.0.0:8000</code> makes the app accessible to other computers. Without it, you would need to access it via <code>127.0.0.1:8000</code> from your VM only, and assuming that for this training you created a server, this would be no fun. If you don't know the VM's St. Olaf facing interface and its IPv4, use CONTROL+C and <code>ip ad</code> to find it. Then run the above command again. Now in, your computer, open up a browser and access the django app using <code>&#60;St. Olaf facing IP&#62;:8000</code></p>

<br>

<p> Congratulations! You just created your very own Django app. Fun fact: <code>&#60;St. Olaf facing IP&#62;:8000/admin</code> takes you to the administration page where you can log in with your super user credentials


