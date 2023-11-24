---
title: "The ELT Pipeline"
layout: post
---

## Introduction
---
* The E stand for Extraction,
* The L stands for Load,
* The T stands for Transform.

So it looks like we only need, at most, three programs to get our data pipeline going, right? 

If only it was that simple...

Welcome to my project on setting up a simple data pipeline using entirely open-source software. Like any project, what starts off simple soon becomes complex, even if the intial task seems straightforward. We are going to cover the instillation of all the needed programs, and then configure them to take some data from a public source on the web and send it to a data analysis tool. This will only focus on locally hosted software. We leave the cloud for another day (when I learn how to do it!).

To be clear, the purpose of this project is partially to demonstrate my knowledge of (basic) Data Engineering, and partially as a resource for when I, inevitably, forget much of this! It is overkill for what is essentially moving a Google Sheets document into a database (once), but the principles used here can be scaled up to massive amounts of data continuously extracted with complex transformations using multiple sources and destinations.

## Overview

This is a list of all the programs we will need to install. We will need a fair amount of space, approximately 30 GBs but it would be wise to have at least 40 to 50 GBs. This is also intended for Windows 10 (or 11 probably works too).

| Program          | Purpose                   | Size (approx.)  |
|------------------|---------------------------|-----------------|
| Anaconda         | Package manager for Python| 8 GBs           |
| Python           | General Purpose Language  | part of Anaconda|
| Spyder           | Python Editor             | part of Anaconda|
| Git              | Version control           | <1 GB           |
| WSL              | Allows Linux on Windows   | ~2 GB           |
| Docker           | Infrastructure            | 20 GBs          |        
| SQL (MySQL)      | Database                  | part of docker  |
| Airbyte          | The EL of ELT             | part of docker  |
| dbt              | The T of ELT              | part of docker  |
| SuperSet         | BI tool                   | part of docker  |
| Total            |                           | ~30 GB          |

We will install them in that order. Feel free to skip ahead if you already have some installed, but check back if something goes wrong.

## Anaconda, Python, and Spyder (and cmd)

Anacondas, and Pythons, and Spyders, oh my!

Python is a general purpose programming language that is used in Data Science for complex transformations of data. Anaconda manages seperate Python environments so you can run multiple projects with different requirements (like python versions). Though we don't 'technically' need it, it comes with a selection of programs, like Python itself, that makes installation extremely easy. Spyder is an editor for Python and is where we will be actually writing code. It also comes with Anaconda.

Go to [https://www.anaconda.com/download/](https://www.anaconda.com/download/) to download anaconda, which includes anaconda navigator, a GUI.

(picture anaconda_1)

Once installed, create a new Python environment with version 3.9.18 *.
We can then install Spyder, as well as the command prompt (cmd). I don't think this is strictly needed, but it makes using the cmd easier. Otherwise, we would have to activate the Python environment from the cmd each time.

`*` I used MySQL and, in retrospect, PostgresSQL might have been better since dbt does not officially support MySQL. This required me to use an unoffical plugin and an older version of Python. Unfortunately, as we will see later on, I could not get it to work as intended. Check the required version of Python (probably latest) if you want to try PostgresSQL or another supported database.

## Git/Github

Git is a tool that tracks changes to software, allowing you to roll-back changes, create multiple copies of software, and log why changes were made, among other things.

Github uses git and is a way to host repositories of code, for instance for collaboration, and is required for Airbyte to run SQL and Python files using dbt. You may also notice this webpage is running on github! 

We don't 'technically' need git locally, but, like Anaconda, it makes life easier in the long run and you will need it eventually.

Install git at [https://git-scm.com/downloads](https://git-scm.com/downloads).

You will also need to setup an account for Github at [https://github.com/](https://github.com/) if you don't have one.

(Github_1 picture)

## WSL 2, Linux, and Docker Desktop

Windows Service for Linux (WSL) is a . . . windows service . . . for linux.

Although not *for* linux, in that sense. It is a virtual machine that allows Linux to run on windows operating systems.

install WSL using the command prompt:

`wsl --install`

We can then list the available distributions of Linux and select one. I chose Ubuntu.

`wsl --list --online`

`wsl --install -d <Distribution>`

Docker allows programs to be run in a container. Containers contains all the neccessary parts to run the programs and are lightweight compared to virtual machines. One advantage is that software can be shared between systems since the container has everything it needs to run, stand-alone. Another advantage is that it is required by several of the programs we are going to use, which sort of forces our hand.

Install docker desktop at [https://www.docker.com/products/docker-desktop/](https://www.docker.com/products/docker-desktop/)

We also need to activate WSL. Navigate to (...) and press (...).

(picture docker_1)

## MySQL

MySQL is a version of SQL, not just a database language but the database language.
Workshop is a GUI. Don't need it. Is nice to have.
Maybe Postgres too.

install MySQL Workbench at [https://dev.mysql.com/downloads/workbench/](https://dev.mysql.com/downloads/workbench/)

## Airbyte

Airbyte is EL of ELT. Also can do T using dbt.

Install using command prompt.
```
git clone --depth=1 https://github.com/airbytehq/airbyte.git
cd airbyte
bash run-ab-platform.sh
```
username: airbyte
password: password

## dbt

The T of dbt. Airbyte uses it. 'Just' runs Python and SQL but powerful. Doesn't work with Python with MySQL which is a shame. Maybe I'll fix it???

Install  using command prompt with Python
`python -m pip install dbt-postgres`

## SuperSet

Buisness Intelligence tool. Like all the other ones, but free!

Install using command prompt using instruction from docker:

# Connecting Google Sheets to MySQL

(google sheet to airbyte with dbt to mysql to superset run on docker)
This hellscape [https://docs.airbyte.com/integrations/sources/google-sheets/](https://docs.airbyte.com/integrations/sources/google-sheets/)

Setup Airbyte

Setup dbt

Setup SQL

Setup Superset

# To the analysis and beyond!

And that's that. Easy...

None of these steps are particularly hard individually, but there are many, and each brings a chance for something to go wrong. However, if all goes according to plan, you should now have a working data pipeline that brings data from a Source to a Destination, and Transforms it in the database, ready to get analysed.

Next time, we will do the actual analysis, which may require a lot more SQL as well as the scope of what problem(s) we are trying to solve. And a (less than) fancy dashboard in SuperSet.

I hope to see you then.
