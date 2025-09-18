---
title: "Python Webapp (WIP)"
layout: post
---

## Introduction

![](https://raw.githubusercontent.com/Cameron-n/Alchemy/master/assets/dash-test-frontcover.png)
*Example page of the finished product*

**Note: The associated repo can be found [here](https://github.com/Cameron-n/Morrowind-Alchemy), and the webapp [here](https://cameronn.eu.pythonanywhere.com/).**

During the first year of my software apprenticeship, I heavily used a Python library called Dash to create a dashboard for the company. As I used the library more, and learnt surrounding skills and technologies, I realised that, despite it's name, it can be used to create whole webapps, not just dashboards.

In my previous project, I wanted to showcase my skills yet also enjoy myself. That's why I focused on data from one of my favourite games, Morrowind. My technical skills have improved since that project (almost two years ago!), and I'm still in love with the world of Morrowind.

So, this project will be about building a webapp to showcase my new skills and vastly improve on that past project.

Please note, I tend to explain things simply and either fully, or with enough detail to understand where to go next. However, due to the complexity of this project, some familiarity with general programming concepts is assumed. Also, details are covered as and when they are relevant, and many questions will likely be raised that would be out of scope to answer. That said, feel free to reach out to me for these questions. I can at least point you to the documentation!

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

The psychological benefit of structure and organisation makes the actually programming much more enjoyable. As well as the practical benefits like locating information faster, it's just nicer to look at and work with.

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

### Dash

- app.py - This is the entrypoint of the application. In other words, the code execution starts here. As it stands, this file will setup and run the Dash application, and also contains the frontend work for the settings of the navbar, header, and main content area.
- pages/ - This *folder* contains the pages of this multipage app. Dash automatically searches a folder called pages and then searches for a variable called `layout`. It uses this when a page is requested. This is where the majority of our work will be.
- components/ - This is a sort of "catch-all" folder for scripts that don't belong anywhere else. For a smaller size project like this, it works fine. It contains logic that is too extensive to put coveniently in a page script. It also contains `config.py`, while has data for the apps global visual styling.
- assets/ - This is another folder that Dash finds automatically. It is used to contain assets the app can load, such as pictures. It also contains `.css` files for styling.

### Database

These files are not used by the app, but are used to create and populate the database. More on this later.

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

### Appshell

Next, we want to setup the so-called "AppShell". The AppShell is a premade component that containers layout information for the navbar, header, and main content area. It comes from Dash Mantine Components.

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

Here, the `"height": 60` makes the header 60 pixels tall from the top of the screen, and `"width": 200"` makes the navbar 200 pixels wide from the left of the screen. The `dash.page_container` is the object that defines where the current page is located in the layout. You'll notice it's contained inside the AppShell, so the navbar and header will not change regardless of which page we are on.

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
            href="/potion-maker"",
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

The styling is largely omitted from the above code. We'll cover that later, so don't worry if you're following along and your version doesn't look the same.

This covers the basic setup of a working dash app with a navbar and header. For the next step, we need to do some behind-the-scenes stuff to make it actually useful.

### Database

For the app to be useful, it needs to do stuff with data. To do stuff with data, we first need to *have* data. Here we load the data and connect it to the app.

I'll be using MySQL as the database due to PythonAnywhere only having MySQL and PostgreSQL, and PostgreSQL being a paid only option on the platform.

[//]: # (Installation of database)

We don't stricty need to develop the app locally if it's going to be hosted on the cloud, but it makes it much more convenient. You can download MySQL (the database) here, and MySQL Workbench (a GUI) here.

Something, something, setup of the database.

[//]: # (Explanation of ERD)

There are three tables in my design: The main table, called "Ingredient", which contains each ingredients name, effects (in a "Yes/No" format), weight, and cost. I then added an "Effect" table to store information about each effect, mainly if it's a positive or negative effect (i.e. useful or harmful, strength vs poison, you can only use potions on yourself in Morrowind. This changes in later games in the series). I also added a "Tool" table to store some information about the tools used in alchemy. These are explained in the "Potion Maker" section below.

[//]: # (Create tables)

To create these tables (with no data) we run the following scripts. You can just copy and paste the contents and run them, or run it as a script. Very large scripts are better run as files as, I'm guessing, the graphical overhead of showing each lines success or failure is significant.

```sql
test
```

An except of the script is below.

[//]: # (Explanation of data)

The original data is thanks to ... and is located here. The database will then be extended with additional effects and ingredients, and also cleaned a little.

[//]: # (Create data)

The following three excerpts are from files that contain the needed data for each of the three tables.

```sql
test
```

```sql
test
```

```sql
test
```

### Styling

We don't want to style the app too much at this point. However, setting up the standard format can allow us to edit things much easier when the times comes.

## Home page

`(WIP)`

A simple page to describe how to use the webapp and where to go next. Also contains any misc. information like the location of the git repo.

`(WIP)`

## Potion Database

This page is the crowning achievement of the app. It finds all possible potions based on what effects are desired. There are literally millions of possible combinations, and due to the app allowing custom ingredients to be added, this can grow even further. For context, the unmodded game has around 100 ingredients. If we assume every ingredient can be combined with every other ingredient, the total number of four ingredient potions (the vast majority) would be `100*99*98*97=94,109,400`. There are not nearly this many due to our assumption being very wrong, but it demonstrates the potential.

### Frontend

[Instructions, Origins, Effects list, Calculate, Table]

### Backend

[Non combos logic]

[Combos logic]

## Potion Maker

[Test]

## Ingredient Info

(WIP)
