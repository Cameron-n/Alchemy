---
title: "The ELT Pipeline"
layout: post
---

## Introduction
---

![](https://raw.githubusercontent.com/Cameron-n/Alchemy/master/assets/ELT1.jpg)

* #### The E stand for Extraction,
* #### The L stands for Load,
* #### The T stands for Transform.

So it looks like we only need, at most, three programs to get our data pipeline going, right? 

If only it was that simple...

Welcome to my project on setting up a simple data pipeline using entirely open-source software. We are going to cover the installation of all the needed programs, and then configure them to take some data from a public source on the web and send it to a data analysis tool. This will focus only on locally hosted software.

The purpose of this project is partially to demonstrate my knowledge of Data Engineering, and partially as a resource for when I, inevitably, forget much of this! It is overkill for what is essentially moving a Google Sheets document into a database, once, but the principles used here can be scaled up to massive amounts of data continuously extracted with complex transformations using multiple sources and destinations.

## Part 1: Installation
---

This is a list of all the programs we will need to install. We will need a fair amount of space, approximately 30 GBs but it would be wise to have at least 40 to 50 GBs. This is also intended for Windows 10 or 11.

| Program          | Purpose                   | Size (approx.)  |
|------------------|---------------------------|-----------------|
| Anaconda         | Package manager for Python| 8 GB            |
| Python           | General Purpose Language  | part of Anaconda|
| Spyder           | Python Editor             | part of Anaconda|
| Git              | Version control           | <1 GB           |
| WSL              | Allows Linux on Windows   | ~2 GB           |
| Docker           | Infrastructure            | 20 GB           |        
| SQL              | Database                  | part of docker  |
| Airbyte          | The EL of ELT             | part of docker  |
| SuperSet         | BI tool                   | part of docker  |
| Total            |                           | ~30 GB          |

### Anaconda, Python, and Spyder (and cmd)
---

Anacondas, and Pythons, and Spyders. Who knew programmers had a sense of humour?

#### What they are:

* Python is a general purpose programming language that is used in Data Science for complex transformations of data. 

* Anaconda manages seperate Python environments so you can run multiple projects with different requirements (like python versions). Though we don't 'technically' need Anaconda, it comes with a selection of programs, like Python itself, that makes installation extremely easy. 

* Spyder is an editor for Python and is where we will be actually writing code. It also comes with Anaconda.

#### Installation:

Go to [https://www.anaconda.com/download/](https://www.anaconda.com/download/) to download anaconda, which includes anaconda navigator, a GUI.

We can then install Spyder, as well as the command prompt (cmd). I don't think this is strictly needed, but it makes using the cmd easier. Otherwise, we would have to activate the Python environment from the cmd each time.

![](https://raw.githubusercontent.com/Cameron-n/Alchemy/master/assets/Anaconda_1.jpg)

### Git/Github --> OUTDATED. NOT REQUIRED
---

#### What they are:

* Git is a tool that tracks changes to software, allowing you to roll-back changes, create multiple copies of software, and log why changes were made, among other things.

* Github uses git and is a way to host repositories of code, for instance for collaboration, and is required for Airbyte to run SQL and Python files using dbt. You may also notice this webpage is running on github! 

#### Installation:

Install git at [https://git-scm.com/downloads](https://git-scm.com/downloads).

You will also need to setup an account for Github at [https://github.com/](https://github.com/) if you don't have one.

![](https://raw.githubusercontent.com/Cameron-n/Alchemy/master/assets/Github_2.png)

### WSL 2, Linux, and Docker Desktop
---

#### What they are:

* Windows Service for Linux (WSL) is a . . . windows service . . . for Linux. Although not *for* Linux, in that sense. It is a virtual machine that allows Linux to run on Windows operating systems.

* Docker allows programs to be run in a container. Containers contains all the neccessary parts to run the programs and are lightweight compared to virtual machines. One advantage is that software can be shared between systems since the container has everything it needs to run, stand-alone. Another advantage is that it is required by several of the programs we are going to use, which sort of forces our hand.

#### Installation:

Install WSL using the command prompt:

`wsl --install`

We can then list the available distributions of Linux and select one. I chose Ubuntu.

`wsl --list --online`

`wsl --install -d <Distribution>`

Install docker desktop at [https://www.docker.com/products/docker-desktop/](https://www.docker.com/products/docker-desktop/)

Once installed, we also need to activate WSL in Docker. Navigate to *Settings* -> *Resources* -> *WSL Integration* and enable Ubuntu (or your chosen distribution).

![](https://raw.githubusercontent.com/Cameron-n/Alchemy/master/assets/WSL_1.png)

### SQL/MySQL/PostgreSQL
---

#### What they are:

* SQL is a language even more ubiquitous than Python in data science. It is a database language for relational databases (basically, data in tables). SQL and Python are the backbone of data Transformations, with SQL more limited than Python but faster and easier to use for common transformations and general database tasks.

* MySQL and PostgreSQL are both version of SQL and databases. I've used both for this project are the differences are minor. You can choose either one.

#### Installation:

Install with Docker by typing 'MySQL' or 'PostgreSQL' in the search bar at the top, and click Pull.

![](https://raw.githubusercontent.com/Cameron-n/Alchemy/master/assets/MySQL_1.png)

Another 'technically' optional program is MySQL Workshop or pgAdmin, GUIs to make SQL statements and monitor databases. You don't need one, but it's nice to have.

Install MySQL Workbench at [https://dev.mysql.com/downloads/workbench/](https://dev.mysql.com/downloads/workbench/)
Install pgAdmin4 at [https://www.pgadmin.org/download/pgadmin-4-windows/](https://www.pgadmin.org/download/pgadmin-4-windows/)

![](https://raw.githubusercontent.com/Cameron-n/Alchemy/master/assets/MySQL_3.png)

### Airbyte
---

#### What it is:

Airbyte is the EL of ELT. It can take data from a Source (Like Google Sheets) to a Destination (Like our MySQL database).

#### Installation:

Install using the command prompt (Docker may need to be running).

```
git clone --depth=1 https://github.com/airbytehq/airbyte.git
cd airbyte
bash run-ab-platform.sh
```

You may need to navigate to the file `run-ab-platform.sh` and change it from Windows CRLF to Unix LF. I used notepad++ (yet another tool, I know) for this with the option in the lower right corner. If you're interested, it's just that Windows and Unix use different characters (CR and LF) to indicate a newline. 

Go to [http://localhost:8000](http://localhost:8000) to see it up and running. The default credentials are:

username: airbyte

password: password

![](https://raw.githubusercontent.com/Cameron-n/Alchemy/master/assets/Airbyte_1.png)

### *dbt
---

#### What it is:

Data Build Tool, the T of ELT. It allows SQL scripts to be run with ease, performing data transformations with simple SELECT statements. Airbyte uses it behind the scenes and we can use it in our transformations by reading files hosted from a Github repository. Python scripts can also be used as of dbt-core version 1.3.  

Now, unfortunately, dbt-mysql (the adaptor to make dbt work with MySQL) requires an older version of dbt-core and so we cannot use Python scripts from Airbyte. If I were to redo this (and if you want to have a go) I would use PostgresSQL instead. Furthermore, the SQL script I want to use must be run *after* the Python script. So, we actually can't use dbt at all.

Nevertheless, dbt is an extremely useful tool and we can still learn to use it and demonstrate its integration with Airbyte and Github. If I redo this project with PostgresSQL, I will update this section.

#### Installation:

Install using the command prompt from Anaconda. This will be installed locally, then we will have our files uploaded to Github for Airbyte to use.

`python -m pip install dbt-mysql`

We also need to install a version on docker for Airbyte to use.

`docker pull ghcr.io/dbeatty10/dbt-mysql`

### SuperSet
---

#### What it is:

A Business Intelligence tool, similar to PowerBi, Looker, Tableau etc. It's like all the other ones, but free!

(Yes, some of the others can be used for free as well, in varying capacities).

This tools allows us to do analytics on data. It can create dashboards with interactive graphs, allowing the creation of intuitive high and low level views of the underlying data.

#### Installation:

Install using the command prompt as follows:

`docker run -d -p 8080:8088 -e "SUPERSET_SECRET_KEY=your_secret_key_here" --name superset apache/superset`

your_secret_key_here can be replaced with anything you like. Just make sure to write it down somewhere. Security isn't our focus here, but a secret key is required for Superset to work.

`docker exec -it superset superset fab create-admin`

We use this to create an account that can access Superset.

`docker exec -it superset superset db upgrade`

This updates Supersets database.

`docker exec -it superset superset load_examples`

This just loads example data and dashboards, and can be skipped if desired.

`docker exec -it superset superset init`

Finally, we can get up other roles beside the admin account. I am not sure if this is needed either, but it doesn't hurt.

Navigate to [http://localhost:8080/login/](http://localhost:8080/login/) to see Superset in action.

![](https://raw.githubusercontent.com/Cameron-n/Alchemy/master/assets/Superset_1.png)

## Part 2: Connecting Google Sheets to MySQL
---

Phew. Now that everything is installed, we can begin transporting some data.

What we are about to do, in a sentence, is:

_Connect a Google Sheets document to our MySQL database using Airbyte, with dbt doing some transformations, which we will then connect to Superset for analysis, almost all run on Docker._

### The Data:
---

First, we need some data. I will be using [this](https://docs.google.com/spreadsheets/d/1JQ391ET9lkKRoAdzQnkyIly7XCg2fNmtJqhpqmJitaM/edit#gid=1565250262) Google Sheet. It's a list of ingredients and their effects from [Morrowind](https://en.wikipedia.org/wiki/The_Elder_Scrolls_III:_Morrowind). I suggest taking a took at the data to get an idea of what it's like. Here's a screenshot:

![](https://raw.githubusercontent.com/Cameron-n/Alchemy/master/assets/data_dark.png)

Since we only need the first tab, and Airbyte seems to not like the second tab, I copied the first tab of the Google Sheet to a version on my own account.

Next, we'll need to set up access in Googles API. To do this, follow the instructions here: 

[https://docs.airbyte.com/integrations/sources/google-sheets/](https://docs.airbyte.com/integrations/sources/google-sheets/)

### Setup SQL

Start SQL in Docker and type the following in the command prompt.

`docker run --name <container-name> -e -p 3306:3306 MYSQL_ROOT_PASSWORD=<password> -d mysql:latest`

Write down <password> as you will need it to access the database. The username is 'root'. 

### Setup Airbyte:

Start up Airbyte in Docker and connect to it at [http://localhost:8000](http://localhost:8000).

Navigate to the Source tab and search for Google Sheets.

![](https://raw.githubusercontent.com/Cameron-n/Alchemy/master/assets/Airbyte_2.png)

The information we need for the source is:

Source name: You can choose any name you like.

Service Account Information: You will have gotten this from the Google API. It will be in JSON format.

Spreadsheet Link: Click Share then Copy link in the spreadsheet to get this link.

Now, go to the destination tab and search for MySQL.

![](https://raw.githubusercontent.com/Cameron-n/Alchemy/master/assets/Airbyte_3.png)

The information we need for the destination is:

Destination name: Any name you like

Host: localhost

Port: 3306

DB Name: Name of the database

User: root

In Optional fields,

Password: your password from the MySQL setup

We can now, finally, move the data. Go to the Connections tab, select the Source and Destination we set up, and press Sync now. With a bit of luck, Airbyte should populate your MySQL database with the data from Google Sheets!

### Setup dbt:

All that's left is to do some transformations, and connect our Analysis tool to the data. 

However, first, let's setup dbt just to see how it works. You can skip this step for now, but as I said earlier, I will likely redo this project to integrate the Transformations into Airbyte using dbt.

Navigate to the folder you want to save your dbt folders to. Then, from the command prompt in Anaconda, type:

`dbt init <project_name>`

`cd <project_name>`

In the file `profiles.yml` located in `.dbt` replace the contents with:

```yaml
your_profile_name:
  target: dev
  outputs:
    dev:
      type: mysql
      server: localhost
      port: 3306
      schema: schema_name
      username: your_mysql_username
      password: your_mysql_password
      ssl_disabled: True
```

This contains the information for a connection to the database.

Replace:  

* _your_profile_name_ with the name of your dbt folder.

* _schema_name_ with the name of your database.

* _your_mysql_username_ with your MySQL username (root if using the default admin account).

* _your_mysql_password_ with your MySQL password.

Use 

`dbt debug` 

to check the connection.

You can create a simple SQL script in the models folder, like

```SQL
SELECT * FROM TABLE_NAME LIMIT 10
```

To see dbt in action. dbt will create a new table using SELECT statements.

We can now submit our dbt folder to Github as follows (You'll first need to create a repository on Github).

```
git init
git branch -M main
git add .
git commit -m "Create a dbt project"
git remote add origin https://github.com/USERNAME/project_name.git
git push -u origin main
```

Replace _USERNAME_ with your username.

_project_name_ with your repositories name.

You can delete the `models/example/` directory. Note that `dbt run` will run all files and so will run the example scripts if they are not either deleted or specific models are specified/excluded.

We can now go briefly back to Airbyte to add a transformation by navigating to our connection, pressing transformation, and entering these values:

* _Transformation name_: anything you like

* _Entrypoint arguments for dbt cli to run the project_: `run --models <name_of_model_script>`

* _Docker image URL with dbt installed_: URL of dbt-mysql adaptor from docker e.g. ddb2d4049f9c

* _Git repository URL of the custom transformation project_: the git repo you saved the dbt files to e.g. https://github.com/Cameron-n/alchemy-transform

The transformation should create an extra table in addition to the raw data, so you dont _have_ to remove it, but it might be a good idea regardless.

_Note, newer versions of dbt use `--select` instead of `--models`. We are intentionally using an older version due to using the dbt-mysql adaptor._

### Transformations:

You'll need to install SQLalchemy and pymysql using `python -m pip install sqlalchemy` and `python -m pip install pymysql` from the command prompt in Anaconda. These are libraries used to connect to the database. It is by pure, entertaining, coincidence that Alchemy is the mixing of ingredients in Morrowind, and also the name of the SQL library. This will include using pandas which I will not explain in detail. 

We are going to use the following Python script:

```python
# MySQL python transformation

from sqlalchemy import create_engine

import pymysql

import pandas as pd

 
# Setup connection. CHANGE <database_password> TO YOUR PASSWORD
sqlEngine       = create_engine('mysql+pymysql://root:<database_password>@127.0.0.1:3306/alchemy', pool_recycle=3600)
dbConnection    = sqlEngine.connect()

# Create dataframe
frame           = pd.read_sql("select * from <database_name>.<database_table>", dbConnection);

# I don't know what this does
pd.set_option('display.expand_frame_repr', False)

# Remove unneeded columns. We don't need 'value' or 'weight' or Airbyte's additional data.
frame.drop('value', axis=1, inplace=True)
frame.drop('weight', axis=1, inplace=True)
frame.drop('_airbyte_ab_id', axis=1, inplace=True)
frame.drop('_airbyte_emitted_at', axis=1, inplace=True)
frame.drop('_airbyte_normalized_at', axis=1, inplace=True)
frame.drop('_airbyte_ingredients_hashid', axis=1, inplace=True)

# Replace None (blank spaces) with 0 and 'x' with 1. This allows us to do some maths
frame = frame.replace([None,'x'],[0,1])

# Create new reshaped dataframe for output. We change the data's format to
# Ingredients, effect 1, effect 2, effect 3, effect 4
# This works as each ingredient has up to 4 effects
final_frame=pd.DataFrame(columns=['ingredients','effect 1','effect 2','effect 3','effect 4'])

# This creates each row of the table, searching each original row for 1's and
# adding those effects
for row in range(len(frame.index)):
    new_row = [frame['ingredient'][row]]
    column_number = 0
    for value in frame.iloc[row]:
        if value == 1:
            new_row.append(frame.columns[column_number])
        column_number += 1
    while len(new_row) < 5:
        new_row.append("")
    final_frame.loc[row] = new_row
 
# Name of new table to create      
tableName = "<new_table_name>" 

try:

    final_frame           = final_frame.to_sql(tableName, dbConnection, if_exists='fail');

except ValueError as vx:

    print(vx)

except Exception as ex:   

    print(ex)

else:

    print("Table %s created successfully."%tableName);   

finally:

    dbConnection.close()

```

You can copy the above into Spyder and run it with the MySQL database running in Docker. Hopefully, you should see a new table created if you look in MySQL Workbench, or later in Superset.

We now want to create a new table from the table we created using Python. We'll do this with SQL since it's a simpler transformation (a join). This script creates all possible pairs of ingredients where at least one effect matches, containing no duplicates, and where any single ingredient cannot combine with itself.

_Note, in dbt we don't need the CREATE statement as dbt uses the SELECT statement to construct a new table_

```sql
CREATE TABLE ingredient_pairs AS
SELECT t1.ingredients AS t1ing,t2.ingredients AS t2ing,
t1.`effect 1` AS t1e1, t1.`effect 2` AS t1e2, t1.`effect 3` AS t1e3, t1.`effect 4` AS t1e4,
t2.`effect 1` AS t2e1, t2.`effect 2` AS t2e2, t2.`effect 3` AS t2e3, t2.`effect 4` AS t2e4,
(
  (t1.`effect 1` IN (t2.`effect 1`,t2.`effect 2`,t2.`effect 3`,t2.`effect 4`) AND t1.`effect 1` != '')
+ (t1.`effect 2` IN (t2.`effect 1`,t2.`effect 2`,t2.`effect 3`,t2.`effect 4`) AND t1.`effect 2` != '')
+ (t1.`effect 3` IN (t2.`effect 1`,t2.`effect 2`,t2.`effect 3`,t2.`effect 4`) AND t1.`effect 3` != '')
+ (t1.`effect 4` IN (t2.`effect 1`,t2.`effect 2`,t2.`effect 3`,t2.`effect 4`) AND t1.`effect 4` != '')
) AS number_of_effects
FROM 
(
ingredients_final t1, ingredients_final t2
)
WHERE t1.ingredients < t2.ingredients
AND (
   t1.`effect 1` IN (t2.`effect 1`,t2.`effect 2`,t2.`effect 3`,t2.`effect 4`) AND t1.`effect 1` != ''
or t1.`effect 2` IN (t2.`effect 1`,t2.`effect 2`,t2.`effect 3`,t2.`effect 4`) AND t1.`effect 2` != ''
or t1.`effect 3` IN (t2.`effect 1`,t2.`effect 2`,t2.`effect 3`,t2.`effect 4`) AND t1.`effect 3` != ''
or t1.`effect 4` IN (t2.`effect 1`,t2.`effect 2`,t2.`effect 3`,t2.`effect 4`) AND t1.`effect 4` != ''
) 
```

You can run this from MySQL Workbench, using dbt, or the shell if you know how.

We now have a dataset we can connect to Superset for analysis.

### Setup Superset

Start Superset in Docker. Navigate to datasets and select the database, schema, and table.

![](https://raw.githubusercontent.com/Cameron-n/Alchemy/master/assets/Superset_3.png)

![](https://raw.githubusercontent.com/Cameron-n/Alchemy/master/assets/Superset_2.png)

## To the analysis and beyond!

And that's that. Easy...

If all went according to plan, you should now have a working data pipeline that brings data from a Source to a Destination, and Transforms it in the Database, ready to get Analysed.

This literally is the beginning. It's a basic infrastructure that can be expanded upon in enourmous ways, from security, sources, and destinations, to the cloud, schedualing, and beyond.

But, enough for now.

Next time, we will do the actual analysis, which will require some more SQL as well as the scope of what problem(s) we are trying to solve. And a (less than) fancy dashboard in SuperSet.

I hope to see you then.
