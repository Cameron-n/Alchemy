---
title: "Python Webapp (WIP)"
layout: post
---

[Picture of Page]

## Introduction

**Note: The associated repo can be found [here](https://github.com/Cameron-n/Morrowind-Alchemy), and the website [here](https://cameronn.eu.pythonanywhere.com/).**

During the first year of my software apprenticeship, I heavily used a Python library called Dash to create a dashboard for the company. As I used the library more, and learnt surrounding skills and technologies, I realised that, despite it's name, it can be used to create whole webapps, not just dashboards.

In my previous project, I wanted to showcase my skills yet also enjoy myself. That's why I focused on data from one of my favourite games, Morrowind. My technical skills have improved since that project (almost two years ago!), and I'm still in love with the world of Morrowind.

So, this project will be about building a webapp to showcase my new skills and vastly improve on that past project.

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

- [PythonAnywhere](https://www.pythonanywhere.com/) - An excellent company that offers free (and paid) hosting for Python projects. It runs of AWS under-the-hood but abstracts away most of the infrastructure management.
- [MySQL](https://www.mysql.com/) - PythonAnywhere offers databases as part of their package. The MySQL one is free, so I'll be using that! This is to the store some Morrowind data.
- [Git](https://git-scm.com/)/[GitHub](https://github.com/) - The code is stored on a git repo. The main benefit is the ability to document and share the code, including between my local development computer and the PythonAnywhere servers.

# Goal

???

## Project Structure

The psychological benefit of structure and organisation makes the actually programming much more enjoyable. As well as the practical benefits like locataing information faster, it's just nicer to look at and work with.

### Overview

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

- app.py - This is the entrypoint of the application. In other words, the code execution starts here. As it stands, this file will setup and run the Dash application, and also contains the frontend work for the framework of the navbar, header, and main content area.
- pages - This *folder* contains the pages of this multipage app. Dash automatically searches a folder called pages and then searches for a variable called `layout`. It uses this when a page is requested. This is where the majority of our work will be.
- components - This is a sort of "catch-all" folder for scripts that don't belong anywhere else. For a smaller size project like this, it works fine. It contains logic that is too extensive to put coveniently in a page script. It also contains `config.py`, while has data for some of the apps visual theme.
- assets - This is another folder that page finds automatically. It is used to contain assets the app can load, such as pictures. It also contains `.css` files for styling.

### Database

These files are not used by the app, but are used to create and populate the database. More on this later.
