---
title: "Python Webapp (WIP)"
layout: post
---

## Introduction

![](https://raw.githubusercontent.com/Cameron-n/Alchemy/master/assets/dash-example.png)
*Example page of the finished product*

**Note: The associated repo can be found [here](https://github.com/Cameron-n/Morrowind-Alchemy), and the webapp [here](https://cameronn.eu.pythonanywhere.com/).**

During the first year of my software apprenticeship, I heavily used a Python library called Dash to create a dashboard for the company. As I used the library more, and learnt surrounding skills and technologies, I realised that, despite it's name, it can be used to create whole webapps, not just dashboards.

In my previous project, I wanted to showcase my skills yet also enjoy myself. That's why I focused on data from one of my favourite games, Morrowind. My technical skills have improved since that project (almost two years ago!), and I'm still in love with the world of Morrowind.

So, this project will be about building a webapp to showcase my new skills and vastly improve on that past project.

Please note, some familiarity with general programming concepts is assumed. Also, details are covered as and when they are relevant, and many questions will likely be raised that would be out of scope to answer. That said, feel free to reach out to me for these questions. I can at least point you to the documentation!

## Tech Stack

In case you want to follow along, this is what I'll be using for the project.

### Frontend

- [Dash](https://dash.plotly.com/) - A Python library and webapp framework. It has both frontend and backend capabilities.
- [Dash Mantine Components](https://www.dash-mantine-components.com/) - For the frontend, we are specifically going to be using Dash Mantine Components, a library that implements the Mantine React library into Python for Dash.

### Backend

- [Flask](https://flask.palletsprojects.com/en/stable/) - A well known Python web framework that Dash uses. While we will use the underlying Flask objects sparingly, we do need them for some tasks.
- [Python](https://www.python.org/) - This general purpose programming language underpins the whole project. A lot of the logic will just be written in pure Python and with common libraries. Please see the [requirements.txt](https://github.com/Cameron-n/Morrowind-Alchemy/blob/main/requirements.txt) file in the repo for the full list. An example of a 'common' library is Pandas.
- [SQLAlchemy](https://www.sqlalchemy.org/) - A Python library for managing databases and database connections. I will specifically be using [Flask-SQLAlchemy](https://flask-sqlalchemy.readthedocs.io/en/stable/quickstart/) since this combines with Flask. It is largely the same syntax.

### Hosting

- [PythonAnywhere](https://www.pythonanywhere.com/) - An excellent company that offers free (and paid) hosting for Python projects. It runs on AWS under-the-hood but abstracts away most of the infrastructure management.
- [MySQL](https://www.mysql.com/) - PythonAnywhere offers databases as part of their package. The MySQL one is free, so I'll be using that! This is to the store some Morrowind data.
- [Git](https://git-scm.com/)/[GitHub](https://github.com/) - The code is stored on a git repo. The main benefit is the ability to document and share the code, including between my local development computer and the PythonAnywhere servers.

# Goal

The purpose of this project is make it easy to explore alchemy in the game Morrowind. In simple terms, alchemy is where potions are made in the game by combining ingredients. This follows some rules, outlined below, and the goal is to implement these rules, explore the results, and add additional tools to help with related problems (e.g. listing the locations of ingredients).

Definitions:
- An *ingredient* is a list of one, two, three, or four effects.
- A *potential potion* is a combination of two, three, or four ingredients.
- A *valid* potion has each ingredient share at least one of it's effects
with at least one of the other ingredients.

To give an example, **Adamantium Ore** has the effects (**Burden**, **Poison**, **Restore Magicka**, **Reflect**). **Comberry** has the effects (**Drain Fatigue**, **Restore Magicka**, **Fire Shield**, **Reflect**). We see they share the effects (**Restore Magicka**, **Reflect**), so a potion would contain those effects if those ingredients were combined into one.

The vast majority of the project is not about Morrowind, so please don't be put off if you have no interest in it. Equally, my passion for the game fuels my desire to make this app, so please look into the game if it does interest you!

## Project Structure

The psychological benefit of structure and organisation makes the actual programming much more enjoyable. As well as the practical benefits like locating information faster, it's just nicer to look at and work with.

### Overview

[//]: # (Update the folder structure if/when things change)

```
.
├── assets/
│   └── styles.css
├── components/
│   ├── config.py
│   ├── ...
│   └── ...
├── database/
│   ├── ...
│   ├── ...
│   └── ...
├── pages/
│   ├── page one
│   ├── page two
│   └── ...
├── .gitignore
├── .env
├── LICENSE
├── README.md
├── app.py
└── requirements.txt
```

This looks a little complicated, so let's explain some of the simpler items right away.

### Non-project specific

- LICENSE - A standard MIT license. Anyone is welcome to copy from this project.
- README.md - Contains the markdown for the readme you can read on the repo.
- .gitignore - A standard GitHub provided ignore list for Python projects. Ignores things like IDE project files.
- requirements.txt - Contains the libraries used in the project. I have put only independant libraries. For example, Flask is not listed but is a dependancy of Dash so will be installed automatically.
- .env - The environment file is intentionally a file excluded from the repo. It contains some configuration secrets, like passwords, that are only saved on the computer running the app. This is to prevent these secrets becoming public by not putting them in the code.

### Dash

- app.py - This is the entrypoint of the application. In other words, the code execution starts here. As it stands, this file will setup and run the Dash application, and also contains the frontend work for the settings of the navbar, header, and main content area.
- pages/ - This *folder* contains the pages of this multipage app. Dash automatically searches a folder called pages and then searches for a variable called `layout`. It uses this when a page is requested. This is where the majority of our work will be.
- components/ - This is a sort of "catch-all" folder for scripts that don't belong anywhere else. For a smaller size project like this, it works fine. It contains logic that is too extensive to put coveniently in a page script. It also contains `config.py`, while has data for the apps global visual styling.
- assets/ - This is another folder that Dash finds automatically. It is used to contain assets the app can load, such as pictures. It also contains `.css` files for styling.

### Database

- These files are not used by the app, but are used to create and populate the database. More on this later.

## Project Setup

**Note: Some code will be omitted or simplified where relevant for brevities sake. Please see the repo for a full understanding.**

### Boilerplate

The first thing we want to do is create the basic boilerplate for Dash to run.

*[app.py](https://github.com/Cameron-n/Morrowind-Alchemy/blob/main/app.py)*
```py
from dash import Dash

app = Dash(__name__)

app.layout = []

app.run()
```

![](https://raw.githubusercontent.com/Cameron-n/Alchemy/master/assets/dash-blankpage.png)
*Lovely blank page*

If you run this then navigate to `localhost:8050`, you should see a lovely blank page. This appears to be the minimum requirements to create a Dash app. You have to create the app, give it a layout (in our case an empty list), then run the app.

### How Dash Works - Layout

A quick aside to explain how to create layouts with Dash and Dash Mantine Components. Dash components are functions. For example: 

```py
dmc.Button()
```

Dash components can be put inside other components. They are entered as a list, either as the first input, or to the input called `children`. In Dash Mantine Components there are a few layout specific components. Some common ones are:

[//]: # (Add to this list of common layout components)

```py
dmc.Group([a,b,c]) # a, b, and c are arranged in a row
dmc.Stack([a,b,c]) # a, b, and c are arranged in a column
```

There are a large number of components and function inputs. Please see the Dash and Dash Mantine Components documentation for a better understanding.

### Appshell

Next, we want to setup the so-called "AppShell". The AppShell is a premade component that contains layout information for the navbar, header, and main content area. It comes from Dash Mantine Components.

For the actual contents of the navbar, header, and main content area, I've chosen to define them elsewhere. The navbar and header are defined in the components folder in the files `navbar.py` and `header.py`. The main content area draws from the pages folder.

*[app.py](https://github.com/Cameron-n/Morrowind-Alchemy/blob/main/app.py)*
```py
import dash
import dash_mantine_components as dmc

# Contents of navbar and header components
from components.navbar import Navbar
from components.header import Header

layout = dmc.AppShell([
    dmc.AppShellHeader(
        Header(),
    ),
    dmc.AppShellNavbar(
        Navbar(),
    ),
    dmc.AppShellMain(dash.page_container)
],
    header={"height": 60},
    navbar={"width": 200},
)

app.layout = dmc.MantineProvider(layout)
```

Here, the `"height": 60` makes the header 60 pixels tall from the top of the screen, and `"width": 200"` makes the navbar 200 pixels wide from the left of the screen. The `dash.page_container` is the object that defines where the current page is located in the layout. You'll notice it's contained inside the AppShell, so the navbar and header will not change regardless of which page we are on. The `dmc.MantineProvider` is necessary to mantine to work and must be the outermost element in the layout. It provides some information to the components, such as the default styling.

For the navbar itself, it's contents are roughly defined as follows:

*components/[navbar.py](https://github.com/Cameron-n/Morrowind-Alchemy/blob/main/components/navbar.py)*
```py
def Navbar():
    layout = dmc.Stack([
        dmc.Anchor(
            dmc.Button("Home"),
            href="/",
        ),
        dmc.Anchor(
            dmc.Button("Potion Database"),
            href="/potion-database",
        ),
        dmc.Anchor(
            dmc.Button("Potion Maker"),
            href="/potion-maker",
        ),
        dmc.Anchor(
            dmc.Button("Ingredient Info"),
            href="/ingredient-info",
        ),
    ])

    return layout
```

In other words, it's just a stack of links made of buttons.

Then, the header is as follows:

*components/[header.py](https://github.com/Cameron-n/Morrowind-Alchemy/blob/main/components/header.py)*
```py
def Header():
    layout = dmc.Group([
        dmc.Burger(),
        dmc.Anchor(
            dmc.Title("Morrowind Alchemy"),
            href="/",
        ),
    ])

    return layout
```

A burger refers to the three horizontal lines that turn into a cross, used to open menus. The three lines look like a burger, I guess... So, the header is one of those and then a link displayed as a "title". A title is simply a HTML H1-6 header element.

We also need to add at least one page so the `page_container` can refer to something, and modify `app.py` to tell it to use the pages.

pages/[home.py](https://github.com/Cameron-n/Morrowind-Alchemy/blob/main/pages/home.py)
```py
dash.register_page(__name__, path="/")

layout = dmc.Text("HOME Page")
```

pages/[app.py](https://github.com/Cameron-n/Morrowind-Alchemy/blob/main/app.py)
```py
app = Dash(__name__, use_pages=True)
```

The `register_page` tells dash it is a page, and creates a URL for it. Here, we specifically define the URL as the root path. Otherwise, it converts the file name into the URL, replacing `_` with `-`. The `layout` variable then defines the contents of the page. So, if we navigate to `localhost:8050`, it returns the contents of `layout` to the `page_container`.

![](https://raw.githubusercontent.com/Cameron-n/Alchemy/master/assets/dash-appshell.png)
*Appshell*

Styling is omitted from the above code. We'll cover that later, so don't worry if you're following along and your version doesn't look the same.

This covers the basic setup of a working dash app with a navbar and header. For the next step, we need to do some behind-the-scenes stuff to make it actually useful.

### Database

For the app to be useful, it needs to do stuff with data. To do stuff with data, we first need to *have* data. Here we load the data and connect it to the app.

I'll be using MySQL as the database due to PythonAnywhere only having MySQL and PostgreSQL, and PostgreSQL being a paid only option on the platform.

![](https://raw.githubusercontent.com/Cameron-n/Alchemy/master/assets/MySQL_3.png)
*MySQL Workbench*

We don't stricty need to develop the app locally if it's going to be hosted on the cloud, but it makes it much more convenient. So, *optionally*, you can download MySQL (the database) [here](https://dev.mysql.com/downloads/installer/), and MySQL Workbench (a GUI) [here](https://dev.mysql.com/downloads/workbench/). It's possible that Workbench also installs the database itself. I don't remember.

Covering SQL in detail is out of scope, but we basically need to create a new schema, create the tables, and load in the data. The actual SQL required after this stage is minimal.

There are three tables in my design: 
- The main table, called "Ingredient", which contains each ingredients name, effects (in a "Yes/No" format), weight, and cost.
- I then added an "Effect" table to store information about each effect, mainly if it's a positive or negative effect (i.e. useful or harmful, e.g. 'Strength' vs 'Poison', you can only use potions on yourself in Morrowind. This changes in later games in the series).
- I also added a "Tool" table to store some information about the tools used in alchemy.

The purpose of each of these tables is explained in more detail later.

To create these tables (with no data) we run the following script. You can just copy and paste the contents and run them, or run it as a script. Very large scripts are better run as files as, I'm guessing, the graphical overhead of showing each lines success or failure is significant. Note the below is an *excerpt*. Please use the actual file from the repo.

*database/[create_tables.sql](https://github.com/Cameron-n/Morrowind-Alchemy/blob/main/database/create_tables.sql)*
```sql
CREATE TABLE `Ingredient` (
  `Value` int NOT NULL,
  `Weight` float NOT NULL,
  `Ingredient` varchar(50) NOT NULL,
  `Origin` varchar(20) DEFAULT NULL,
  `First Effect` varchar(20) DEFAULT NULL,
  `Weakness to Fire` int DEFAULT NULL,
  -- ...
  -- list shortened for brevity
  -- ...
  PRIMARY KEY (`Ingredient`)
);
CREATE TABLE `Effect` (
  `Spell Effects` varchar(50) NOT NULL,
  `Base Cost` float NOT NULL,
  `Positive` tinyint(1) DEFAULT NULL,
  PRIMARY KEY (`Spell Effects`)
);
CREATE TABLE `Tool` (
  `Name` varchar(50) NOT NULL,
  `Quality` float NOT NULL,
  `Type` varchar(20) DEFAULT NULL,
  PRIMARY KEY (`Name`)
);
```

The original data is located [here](https://docs.google.com/spreadsheets/d/1JQ391ET9lkKRoAdzQnkyIly7XCg2fNmtJqhpqmJitaM/edit?gid=1565250262#gid=1565250262). The database is designed to be extended with additional effects and ingredients. I've also cleaned some of the data as there are some errors (e.g. ingredients have the wrong effects, and replacing the 'x's with 1's and 0's).

The following three excerpts are from files that contain the needed data for each of the three tables.

*database/[ingredient_data.sql](https://github.com/Cameron-n/Morrowind-Alchemy/blob/main/database/ingredient_data.sql)*
```sql
INSERT INTO Ingredient(`Value`,`Weight`,`Ingredient`,`Weakness to Fire`,`Blind`,`Burden`,`Paralyze`,`Poison`,`Frost Damage`,`Damage Health`,`Drain Health`,`Drain Fatigue`,`Drain Magicka`,`Drain Strength`,`Drain Intelligence`,`Drain Willpower`,`Drain Agility`,`Drain Speed`,`Drain Endurance`,`Drain Personality`,`Drain Luck`,`Fortify Health`,`Fortify Fatigue`,`Fortify Magicka`,`Fortify Strength`,`Fortify Intelligence`,`Fortify Willpower`,`Fortify Agility`,`Fortify Speed`,`Fortify Endurance`,`Fortify Personality`,`Fortify Luck`,`Fortify Attack`,`Restore Health`,`Restore Fatigue`,`Restore Magicka`,`Restore Strength`,`Restore Intelligence`,`Restore Willpower`,`Restore Agility`,`Restore Speed`,`Restore Endurance`,`Restore Personality`,`Restore Luck`,`Detect Animal`,`Detect Enchantment`,`Detect Key`,`Dispel`,`Feather`,`Invisibility`,`Levitate`,`Light`,`Night Eye`,`Cure Blight Disease`,`Cure Common Disease`,`Resist Common Disease`,`Cure Paralyzation`,`Resist Paralysis`,`Cure Poison`,`Resist Poison`,`Resist Fire`,`Resist Frost`,`Resist Shock`,`Fire Shield`,`Frost Shield`,`Lightning Shield`,`Resist Magicka`,`Reflect`,`Spell Absorption`,`Telekinesis`,`Swift Swim`,`Water Breathing`,`Water Walking`,`Origin`,`First Effect`,`Sound`) VALUES (300,50,'Adamantium Ore',NULL,NULL,1,NULL,1,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,1,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,1,NULL,NULL,NULL,NULL,NULL,'Tribunal','Burden',NULL);
```

*database/[effect_data.sql](https://github.com/Cameron-n/Morrowind-Alchemy/blob/main/database/effect_data.sql)*
```sql
INSERT INTO Effect(`Spell Effects`,`Base Cost`,`Positive`) VALUES ('Blind',1,NULL);
```

*database/[tool_data.sql](https://github.com/Cameron-n/Morrowind-Alchemy/blob/main/database/tool_data.sql)*
```sql
INSERT INTO Tool(`Name`,`Quality`,`Type`) VALUES ('Apprentice''s Alembic',0.5,'Alembic');
```

After running those scripts to load in the data, that should be it for the direct SQL stuff. We still do, however, need to link the app with the database, but for that we'll be strictly using Python.

### Database, part 2

[//]: # (Explain SQL-Alchemy stuff. E.g. .env stuff, mirroring tables, basic syntax, connect via flask)

So, we have a neat frontend. We have the data in the database. But, how do we connect the app to the database? Flask doesn't come with its own database manager, but the `flask_sqlalchemy` module easily adds this on.

Firstly, we need to store an environment variable that contains the connection information for the database. For our local development, it will look something like this:

*.env*
```bash
DATABASE_URI="mysql+mysqldb://<username>:<password>@localhost:3306/<schema>"
```

For hosting on PythonAnywhere, it looks like this:

*.env*
```bash
DATABASE_URI="mysql+mysqldb://<username>:<password>@<username>.mysql.pythonanywhere-services.com/<username>$<schema>"
```

Where:
- <username> is the owner of the database. For local development, I'm using the `root` user.
- <password> is the password you set for the database. It may default to `admin`.
- <schema> is the name of the database schema.

If you're using PostgreSQL or anyother database, you'll have to modify this.

Now, we basically need to "mirror" the database structure in Python using sqlalchemy. It can then treat database items as Python objects.

First, we have to import the environment variable.

*components/[data_access.py](https://github.com/Cameron-n/Morrowind-Alchemy/blob/main/components/data_access.py)*
```py
import os
from dotenv import load_dotenv

path = os.path.join(os.path.dirname(__file__), "../.env")
load_dotenv(path)

database_uri = os.environ.get('DATABASE_URI')
```

We are using what looks to be a convoluted method to get the filepath name of the `.env` file. Really, this is necessary for the app to run cross-platform since Windows, Linux, and Mac specify filepaths differently. Since I'm developing locally on a Windows computer, I'd have to otherwise have a seperate file to make it run on the Linux servers on PythonAnywhere.

Next, we connect the underlying flask server to the database.

*components/[data_access.py](https://github.com/Cameron-n/Morrowind-Alchemy/blob/main/components/data_access.py)*
```py
from flask import Flask
from flask_sqlalchemy import SQLAlchemy
from sqlalchemy.orm import DeclarativeBase

class Base(DeclarativeBase):
    pass


db = SQLAlchemy(model_class=Base)

server = Flask(__name__)
server.config["SQLALCHEMY_DATABASE_URI"] = database_uri
server.config['SQLALCHEMY_ENGINE_OPTIONS'] = {'pool_recycle': 280}
db.init_app(server)
```

To be honest, I'm not sure what the purpose of the `class Base` is. It was in the documentation. `db` is the main object we'll use to do things to the database. Here, we've also told the flask server how to access the database, and then connected it to the `db` object. The `'pool_recycle': 280` is used to discard and regenerate database connections every 280 seconds. This is because PythonAnywhere, and other services, will close database connections after a timeout of 300 seconds.

Now, we need to write a lot of fairly repetitive code to mirror the database objects in Python to the actual database tables in the database. The excerpts that follow should be fairly self-explanatory.

*components/[data_access.py](https://github.com/Cameron-n/Morrowind-Alchemy/blob/main/components/data_access.py)*
```py
from sqlalchemy import Integer, String, Float
from sqlalchemy.orm import mapped_column

class Ingredient(db.Model):
    __tablename__ = "Ingredient"

    Value = mapped_column(Float)
    Weight = mapped_column(Float)
    Ingredient = mapped_column(String(50), primary_key=True)
    Origin = mapped_column("Origin", String(50))
    First_Effect = mapped_column("First Effect", String(50))
    Weakness_to_Fire = mapped_column("Weakness to Fire", Integer)
    # ...

class Effect(db.Model):
    __tablename__ = "Effect"
    
    Spell_Effects = mapped_column("Spell Effects", String(50), primary_key=True)
    Base_Cost = mapped_column("Base Cost", Float)
    Positive = mapped_column(Integer)

class Tool(db.Model):
    __tablename__ = "Tool"
    
    Name = mapped_column(String(50), primary_key=True)
    Quality = mapped_column(Float)
    Type = mapped_column(String(50))
```

In other words, each `class` is a database table. `__tablename__` is used to define the tablename (optional as it otherwise uses the class name). Each variable maps to a table column, and the `mapped_column` function relates the Python object to the actual columns name and datatype. You might note that naming the columns is optional, as it defaults to the variable name, but is needed in case there are underscores in the variable name but not the actual column name in the database.

For my purposes, the app only needs to load the data on startup. Because of this, I simply define some variables that load data from the database. These are then accessible throughout the app. The only slight issue I've found is that, when the database is updated, a currently running session will not update. This could be fairly easily fixed by having a button the reloads the app which would reload the data.

*components/[data_access.py](https://github.com/Cameron-n/Morrowind-Alchemy/blob/main/components/data_access.py)*
```py
import pandas as pd

with server.app_context():
    DF_INGREDIENTS = pd.DataFrame(db.session.execute(db.select(*Ingredient.__table__.columns)))
    DF_EFFECTS = pd.DataFrame(db.session.execute(db.select(*Effect.__table__.columns)))
    DF_TOOLS = pd.DataFrame(db.session.execute(db.select(*Tool.__table__.columns).order_by(Tool.Quality)))
```

The variables are stored as pandas dataframe. Here, we see an example of how sqlalchemy works. Most requests start with `db.session.execute(db.select(...))`. These operations map fairly easily to SQL. The slight oddity here is using `*Ingredient.__table__.columns` instead of just `Ingredient` to select all the columns. It turns out that selecting individual columns produces a dataframe as expected. However, selecting all the columns just using the table name returns an object where we cannot easily access the underlying data. Try it yourself to see the difference.

Finally, if you've been paying careful attention, you'll realise the server here never actually links back to our dash app! So, we need to make an adjustment to our `app.py` to connect our modified flask server to it.

*[app.py](https://github.com/Cameron-n/Morrowind-Alchemy/blob/main/app.py)*
```py
from components.data_access import server

app = Dash(__name__, server=server, use_pages=True)
```

If we run the app, nothing should change visually. If you don't get any errors, the connection should be working. We'll be using the data from the database soon. To run the app in developer mode to check for errors, use:

```py
app.run(debug=True)
```

### How Dash Works - Styling

The last major component of the general app, before we start diving into the individual pages, is the styling. We can style the app in a few ways:

- Components have parameters that control styling. For example `bg` for background color. These ultimately translate into `css`, and most components can be directly styled with it using the `style` parameter.
- `dmc.MantineProvider` takes a `theme` input. This is used to define styling that applies across the app.
- The assets folder can contain `.css` files. This can be used to create `css` classes to style individual components, or to style components globally similar to the `themes` variable.

Of these, styling components directly in python is the quickest, but the least repeatable. It's useful for one-off components such as the AppShell, or simple styles. Here is how I have styled the AppShell itself (not the layouts of the navbar or header).

*[app.py](https://github.com/Cameron-n/Morrowind-Alchemy/blob/main/app.py)*
```py
layout = dmc.AppShell([
    dmc.AppShellHeader(
        Header(),
        p="md",
        bg="myColors.2"
    ),
    dmc.AppShellNavbar(
        Navbar(),
        p="md",
        bg="myColors.1"
    ),
    dmc.AppShellMain(dash.page_container)
],
    header={"height": 60},
    navbar={"width": 200,},
    p="md",
    bg="myColors.0",
)
```

Here, `p` is for padding, and `bg` is for background color. However, what does `md` refer to? What does `myColors.0` refer to? Mantine has a default theme, and this defines some variables, but we can modify this to create our own theme object. Notably, `myColors` is a custom variable that will not work yet as a custom theme has not been defined. The below is an excerpt of my custom theme:

*components/[config.py](https://github.com/Cameron-n/Morrowind-Alchemy/blob/main/components/config.py)*
```py
myColors = [
    "#fff8e7",
    "#fbefd5",
    "#f5dda7",
    "#f0c976",
    "#ecb94e",
    "#eaaf34",
    "#e9aa26",
    "#cf941a",
    "#b88312",
    "#9f7102",
]

theme = {
    "colors": {
        "myColors": myColors,
    },
    "primaryColor": "myColors",
    "primaryShade": 3,
    "breakpoints": {
        "xs": '30em',  # Default values for reference
        "sm": '48em',
        "md": '64em',
        "lg": '74em',
        "xl": '90em',
    },
    "components": {
        "Select": {
            "styles": {
                "dropdown": {"background": myColors[4]},
            }
        },
        "TagsInput": {
            "styles": {
                "dropdown": {"background": myColors[4]},
            }
        },
        "MultiSelect": {
            "styles": {
                "dropdown": {"background": myColors[4]},
            }
        },
    },
}
```

While not all of this is yet relevant, we can see how `myColors` is defined as a list of 10 colours. These can the be accessed within the app using `myColors.0` to `myColors.9`. Some `css` is also specfied for some components that will apply globally.

Lastly, we can write `css` on its own and attach it to components via class names, or affect all components of a particular type. Mantine exposes various class names we can use which you can read about in the documentation.

For example, I'd like the navbar to change size on different screens so that it remains user friendly. I can define some `css` below:

*assets/[styles.css](https://github.com/Cameron-n/Morrowind-Alchemy/blob/main/assets/styles.css)*
```css
.media-navbar-anchor {
    height: 15%;
    min-height: 35px;

    @media (min-width: 48em) {
        height: 5%;
    }
}

.media-navbar-button {
    height: 100%;
    
    @media (max-width: 48em) {
        font-size: 1rem;
    }
}

.media-navbar {
    height: 100%;
    overflow-y: scroll;
}
```

Then, I can modify the `navbar.py` file to use these classes.

*components/[navbar.py](https://github.com/Cameron-n/Morrowind-Alchemy/blob/main/components/navbar.py)*
```py
def Navbar():
    layout = dmc.Stack([
        dmc.Anchor(
            dmc.Button("Home", fullWidth=True, id="nav-btn-home",
                       className="media-navbar-button"),
            href="/", underline="never", c="myColors.9",
            className="media-navbar-anchor"
        ),
        dmc.Anchor(
            dmc.Button("Potion Database", fullWidth=True, id="nav-btn-data",
                       className="media-navbar-button"),
            href="/potion-database", underline="never", c="myColors.9",
            className="media-navbar-anchor"
        ),
        dmc.Anchor(
            dmc.Button("Potion Maker", fullWidth=True, id="nav-btn-maker",
                       className="media-navbar-button"),
            href="/potion-maker", underline="never", c="myColors.9",
            className="media-navbar-anchor"
        ),
        dmc.Anchor(
            dmc.Button("Ingredient Info", fullWidth=True, id="nav-btn-info",
                       className="media-navbar-button"),
            href="/ingredient-info", underline="never", c="myColors.9",
            className="media-navbar-anchor"
        ),
    ],
        className="media-navbar"
    )

    return layout
```

Now the navbar has some styling written in Python, and uses the styling of the `.css` file. When the screen changes size, the buttons and font should change size. The navbar should also now be scrollable on very small screens. Try it out on different devices, or by resizing the window.

This covers the basics of how to style components.

## Home page

A simple page to describe how to use the webapp and where to go next. Also contains any misc. information like the location of the git repo.

## Potion Database

This page is the crowning achievement of the app. It finds all possible potions based on what effects are desired. There are literally millions of possible combinations, and due to the app allowing custom ingredients to be added, this can grow even further. For context, the unmodded game has around 100 ingredients. If we assume every ingredient can be combined with every other ingredient, the total number of four ingredient potions (the vast majority) would be `100*99*98*97=94,109,400`. There are not nearly this many due to our assumption being very wrong, but it demonstrates the potential.

### Frontend

[//]: # (Instructions, Origins, Effects list, Calculate, Table)

### Backend

[//]: # (Non combos logic)

[//]: # (Combos logic)

## Potion Maker

[//]: # (Test)

## Ingredient Info

## Add Ingredient
