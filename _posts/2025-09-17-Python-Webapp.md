---
title: "Python Webapp (WIP)"
layout: post
---

## Introduction

**Note: The associated repo can be found [here](https://github.com/Cameron-n/Morrowind-Alchemy).**

**Note: The associated website can be found [here](https://cameronn.eu.pythonanywhere.com/).**

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

## Test
