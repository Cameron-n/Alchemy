---
title: "The ELT Pipeline"
layout: post
---

## Introduction
---
* The E stand for Extraction,
* The L stands for Load,
* The T stands for transform.

So it looks like we only need, at most, three programs to get our data pipeline going, right? 

If only it was that simple...

Welcome to my project on setting up a simple data pipeline using entirely open-source software. Like any project, what starts off simple soon becomes complex, even if the intial task seems straightforward. We are going to cover the instillation of all the needed programs, and then configure them to take some data from a public source on the web and send it to a data analysis tool. This will only focus on locally hosted software. We leave the cloud for another day.

To be clear, the purpose of this project is partially to demonstrate my knowledge of (basic) Data Engineering, and partially as a resource for when I, inevitably, forget much of this!

## Overview

This is a list of all the programs we will need to install.

| Program          | Purpose          |
|------------------|------------------|
| Anaconda         | Package manager for Python|
| Python           | General Purpose Language  |
| Spyder           | Python Editor             |
| Git              | Version control           |
| WSL              | Allows Linux on Windows   |
| Docker           | Infrastructure            |
| SQL (MySQL)      | Database                  |
| Airbyte          | The EL of ELT             |
| dbt              | The T of ELT              |
| SuperSet         | BI tool                   |

We will install them in that order. Feel free to skip ahead if you already have some installed, but check back if something goes wrong.

## Anaconda, Python, and Spyder (and cmd)

Anacondas, and Pythons, and Spyders, oh my!

Python is a general purpose programming language that is used in Data Science for complex transformations of data. Anaconda manages seperate Python environments so you can run multiple projects with different requirements (like python versions). Though we don't 'technically' need it, it comes with a selection of programs, like Python itself, that makes installation extremely easy. Spyder is an editor for Python and is where we will be actually writing code. It also comes with Anaconda.

Go to [https://www.anaconda.com/download/](https://www.anaconda.com/download/) to download anaconda, which includes anaconda navigator, a GUI.

Once installed, create a new python environment with version ...````*````.
We can then install Spyder, as well as the command prompt (cmd). I don't think this is strictly needed, but it makes using the cmd easier. Otherwise, we would have to activate the Python environment from the cmd each time.

````*```` I used MySQL and, in retrospect, PostgresSQL might have been better since dbt does not officially support MySQL. This required me to use an unoffical plugin and an older version of Python. Unfortunately, as we will see later on, I could not get it to work as intended. Use the latest version of Python if you use PostgresSQL or another supported database.

## Git

(install git using command prompt)
(list of useful commands)

## WSL 2, Linux, and Docker Desktop

(install WSl using command prompt)
(Download Linux)
(Download docker desktop and activate wsl)

## MySQL

(Download MySQL Workbench or PostgresSQL (and sql itself?))

## Airbyte

(install using cmd)

## dbt

(install  using dbt)

## SuperSet

(install using cmd using instruction from docker)

# Connecting Google Sheets to MySQL

(google sheet to airbyte with dbt to mysql to superset run on docker)

# To the analysis and beyond!

(summary, see you next time...)



