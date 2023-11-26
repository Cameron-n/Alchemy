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

We also need to activate WSL. Navigate to Settings -> Resources -> WSL Integration and enable Ubuntu (or you chosen distribution).

(picture docker_1)

## MySQL

MySQL is a version of SQL, a language even more ubiquitous than Python in data science. It is a database language for relational databases (basically, data in tables). SQL and Python are the backbone of data Transformations.

Install with Docker by using the search bar at the top.

(image here of MySQL_1)

Another 'technically' optional program is MySQL Workshop, a GUI to make SQL statements and monitor databases. You don't need it, but it's nice to have.

(image here of Workshop)

install MySQL Workbench at [https://dev.mysql.com/downloads/workbench/](https://dev.mysql.com/downloads/workbench/)

## Airbyte

Airbyte is the EL of ELT. It can take data from a Source (Like Google Sheets) to a Destination (Like our MySQL database).

Install using the command prompt (after having installed docker).

```
git clone --depth=1 https://github.com/airbytehq/airbyte.git
cd airbyte
bash run-ab-platform.sh
```

Go to [http://localhost:8000](http://localhost:8000) to see it up and running. The default credentials are:

username: airbyte

password: password

## dbt

Data Build Tool, the T of ELT. It allows SQL scripts to be run with ease, performing data transformations with simple SELECT statements. Airbyte uses it behind the scenes and we can use it in our transformations by reading files hosted from a Github repository. Python scripts can also be used as of dbt-core version 1.3.  

Now, unfortunately, dbt-mysql (the adaptor to make dbt work with MySQL) requires an older version of dbt-core and so we cannot use Python scripts from Airbyte. If I were to redo this (and if you want to have a go) I would use PostgresSQL instead. As you will see later, we will just run Python locally. 

Install using command prompt with Python. This will be installed locally, then we will have our files uploaded to Github for Airbyte to use.

`python -m pip install dbt-postgres`

We also need to install a version on docker for Airbyte to use.

`docker pull ghcr.io/dbeatty10/dbt-mysql`

## SuperSet

A Business Intelligence tool, similar to PowerBi, Looker, Tableau etc. It's like all the other ones, but free!

(Yes, some of the others can be used for free as well, in varying capacities).

This tools allows us to do analytics on data. It can create dashboards with interactive graphs, allowing the creation of intuitive high and low level views of the underlying data.

Install using the command prompt as follows:

`docker run -d -p 8080:8088 -e "SUPERSET_SECRET_KEY=your_secret_key_here" --name superset apache/superset`

your_secret_key_here can be replaced with anything you like. Just make sure to write it down somewhere. Security isn't our focus here, but a secret key is required for Superset to work.


`docker exec -it superset superset fab create-admin`

We use this to create an account that can access Superset.

`docker exec -it superset superset db upgrade`

Admitedly, I don't know what this does.

`docker exec -it superset superset load_examples`

This just loads example data and dashboards, and can be skipped if desired.

`docker exec -it superset superset init`

Finally, we can get up other roles beside the admin account. I am not sure if this is needed either, but it doesn't hurt.

Navigate to [http://localhost:8080/login/](http://localhost:8080/login/) to see Superset in action.

# Connecting Google Sheets to MySQL

Phew. Now that everything is installed, in a timely and simple manner where absolutely nothing can go wrong. . . we can begin.

What we are about to do, in a sentence, is:

Connect a Google Sheets document to our MySQL database using Airbyte with dbt doing some transformations which we will then connect to Superset for analysis, almost all run on Docker.

(image explination_1)

### The Data:

First, we need some data. I will be using [this](https://docs.google.com/spreadsheets/d/1JQ391ET9lkKRoAdzQnkyIly7XCg2fNmtJqhpqmJitaM/edit#gid=1565250262) Google Sheet. It's a list of ingredients and their effects from (Morrowind)[https://en.wikipedia.org/wiki/The_Elder_Scrolls_III:_Morrowind]. I suggest taking a took at the data to get an idea of what it's like. 

Since we only need the first tab, and Airbyte seems to not like the second tab, I copied the first tab of the Google Sheet to a version on my own account.

Next, we'll need to set up access in Googles API. To do this, follow the instructions here: 

[https://docs.airbyte.com/integrations/sources/google-sheets/](https://docs.airbyte.com/integrations/sources/google-sheets/)

They look hellish but it should go smoothly.

### Setup Airbyte:

Start up Airbyte in Docker. Then navigate to ()[] and (source, destination).

The information we need for the source is:

The information we need for the destination is:

### Setup dbt:

Navigate to the folder you want to save your dbt folders to. Then, from the command prompt, type:

`dbt init <project_name>`
`cd <project_name>`

some seomthing yaml file.

We can then setup the connection to the local database.

open profiles.yml and replace the content with the following:

Use dbt debug to check the connection.

dbt run.

```
git init
git branch -M main
git add .
git commit -m "Create a dbt project"
git remote add origin https://github.com/USERNAME/<project_name>.git
git push -u origin main
```

You can delete the `models/example/` directory.

We are going to use the following SQL script:

And the following Python script:

We can now go briefly back to Airbyte to add a transformation by navigating to . . . and entering these values:

### Setup SQL

Start SQL in Docker and type the following in the command prompt.

`i have no idea what i'm doing`

### Setup Superset

Start Superset in Docker. Navigate to balh and press blah to connect data.

# To the analysis and beyond!

And that's that. Easy...

None of these steps are particularly hard individually, but there are many, and each brings a chance for something to go wrong. However, if all goes according to plan, you should now have a working data pipeline that brings data from a Source to a Destination, and Transforms it in the database, ready to get analysed.

This literally is the beginning. Basic infrastructure that can be expanded upon in enourmous ways, from security, sources, and destinations, to the cloud, schedualing, and beyond.

But, enough for now.

Next time, we will do the actual analysis, which may require some more SQL as well as the scope of what problem(s) we are trying to solve. And a (less than) fancy dashboard in SuperSet.

I hope to see you then.
