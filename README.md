# adonisJS
Boilerplate for Adonis JS(MVC)


# installation

> npm i --global @adonisjs/cli
> adonis new <project name>
> adonis serve --dev


#
Great! You should now be able to visit your project in the browser at http://127.0.0.1:3333/
 
Adonis Templating (The View in MVC)
Open up the jobpostr folder your preferred code editor. 

 First, you might wonder where the template and graphics are coming from when you viewed the site in the browser. We know from the URL that the route is the home route / so, let's find out where that's taken care of.

# Open up /start/routes.js and you will notice the following code:

const Route = use('Route')
Route.on('/').render('welcome')


#
This is telling Adonis that when the root of the site is loaded, render a template/view called welcome.

That welcome template can be found in /resources/views/welcome.edge. Here, you will find the associated HTML for the page you see in your browser.

#
Also notice on line 6 we have this code:

{{ style('style') }}


#

That's a quick and easy Adonis helper function that allows you to reference CSS files with the style() function. It's referencing a CSS file called style, which can be found in /public/style.css.

For now, let's focus on the view and structuring out how our views will be situated with a navigation and content section.

Rename welcome.edge to index.edge and create a new folder in /resources/views called layouts with a file inside of it called main.edge.

main.edge will be responsible for storing the head information and the basic layout that we're going to use. This will help us avoid html duplication.

#

Inside main.edge, paste the following contents:

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    {{ style('style') }}

    @!section('extracss')

    <title>
        @!section('title')
    </title>
</head>
<body>
    <header>
        <a href="/" id="logo">JobPostr</a>
        <nav>
            <ul>
                <li><a href="/">Jobs</a></li>
                <li><a href="/login">Login</a></li>
                <li><a href="/signup">Signup</a></li>
            </ul>
        </nav>
    </header>

    <div class="container">
        @!section('content')
    </div>
</body>
</html>


#

A few things are happening here, but most importantly, we see @!section('title') and content. Both of these are placeholders, and are therefore dynamic. We can define these content sections in the other edge templates, and their contents will be placed inside of these placeholders.

The extracss section will be used later when we create additional edge templates that require additional CSS imports.

Open our index.edge file and paste the following:

@layout('layouts.main')

@section('title')
JobPostr - Post your Jobs 
@endsection

@section('content')
  <h1>All Jobs</h1>

  <div class="job-container">
    <div class="blank"></div>
    <div class="job-info">
      <h3>Job Title</h3>
      <p>Future job description can go here.</p>
    </div>
  </div>
@endsection


#

First, we tell the edge system to use the layouts.main edge file, and then we define our sections that are defined within main.edge. For now, we don't have anything dynamic, so the job listing information is simply static. But we'll come back to that later.

Next, visit the /start/routes.js file and update line 18 to:

Route.on('/').render('index')

Refresh the browser, and you will see our content. But everything looks really ugly right now, so let's fix that by stepping into public/style.css and pasting the following content:

#

@import url('https://fonts.googleapis.com/css?family=Montserrat:300,700');

html, body {
  height: 100%;
  width: 100%;
}

body {
  font-family: 'Montserrat', sans-serif;
  font-weight: 300;
  background-color: #EFEFEF;
}

* {
  margin: 0;
  padding: 0;
}
ul {
  list-style-type: none;
}

header {
  background-color: #303C49;
  display: grid;
  grid-template-columns: 30% auto;
}
header a {
  color: white;
  text-decoration: none;
  text-transform: uppercase;
}
#logo {
  font-weight: bold;
  font-size: 1.3em;
  padding: 1.5em 0 0 2em;
  margin: 0;
}
nav {
  justify-self: right;
}
nav ul li {
  display: inline;
}
nav ul li a {
  padding: 2em;
  display: inline-block;
}
nav ul li a:hover {
  background: #455668;
}
.container {
  width: calc(100% - 4em);
  padding: 2em;
  margin-top: 2em;
}
h1 {
  margin-bottom: 1em;
}
.job-container, .job-container2 {
  background: white;
  border-radius: 7px;
  padding: 1em;
  margin-bottom: 10px;
  display: grid;
  grid-template-columns: 120px auto;
}
.blank {
  width: 100px;
  height: 100px;
  border: 1px solid lightgray;
  border-radius: 5px;
}

# Databases

Now that we've touched on some of the View related stuff, let's talk about the backend stuff. Adonis 5 supports the following databases to store your data:

PostgreSQL

SQLite

MySQL

MariaDB

Oracle

MSSQL

For this tutorial, we're going to use MySQL. This will require installing MySQL on your machine if you plan to develop locally. I won't be covering how to get this set up and running, so, perform some Google searches on installing MySQL if you have a hard time. 

Visit the /.env file and change DB_CONNECTION to mysql: along with setting the database to db:

DB_CONNECTION=mysql
DB_DATABASE=db

This will tell Adonis that we want to use MySQL as our database.

Next, let's use the command line to install mysql:

#
> adonis install mysql


#

Now, we have to create the db database in the command line. After installing MySQL and ensuring you have access to the mysql command from the command line, type:


> mysql -u root -p
:: hit enter when prompted for a password ::

mysql> create database jobpostr;
mysql> exit;

#

After running the final command, our database will be created.

If you installed MySQL Workbench and open it up, you wil see the new jobpostr database listed in your databases!

Next, how do we actually populate this database with tables, data and such? That's done with the help of Adonis Migrations.

# Migrations


Migration files allow you to create and delete tables. 

Open up /database/migrations and you will see two migrations already created. Open up the _user.js migration file.

You will see we have both and up() and down() method. Up is for creating or altering a table of some sort, and down is for rolling back the changes made in Up. 

The way we run these migrations is through the console:

> adonis migration:run

output:

migrate: 1503248427885_user.js
migrate: 1503248427886_token.js
Database migrated successfully in 613 ms

#
If you use MySQL workbench of the mysql command line tool, you will see that our database now has 3 tables. One for users, tokens, and schemas. The schemas table keeps track of which database migrations were ran!

Let's make a new migration with the Adonis CLI for storing our job posting data:

> adonis make:migration jobs
> Choose an action Create table
√ create  database\migrations\1536680712439_jobs_schema.js

#
When prompted, choose create table and hit enter. 

This results in a new migrations file being created in the /database/migrations folder. Open up the new file and update the following section:

this.create('jobs', (table) => {
      table.increments()
      table.string('title')
      table.string('link')
      table.string('description')
      table.integer('user_id')
      table.timestamps()
    })
    
    Here, we're adding 4 columns called title, link, description, user_id. The user_id field will allow us to keep track of who posted which job.

Let's run this migration file now:

adonis migration:run
migrate: 1536680712439_jobs_schema.js
Database migrated successfully in 313 ms





