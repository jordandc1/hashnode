---
title: "How to use alembic to create your DB"
seoTitle: "create databases, alembic tutorial, database autogeneration"
datePublished: Mon Sep 12 2022 10:16:05 GMT+0000 (Coordinated Universal Time)
cuid: cl7ym0w3300eefxnv7fyu1js5
slug: how-to-use-alembic-to-create-your-db
cover: https://cdn.hashnode.com/res/hashnode/image/unsplash/PkbZahEG2Ng/upload/v1662977656941/iBCtMHeXz.jpeg
tags: 4articles4weeksweek4

---

## Design Database
Before you create any tables for your system, you should design the database. An Entity-relationship model will help you to understand the structure of your database. The ER diagram is not the intention of this article so we will use a simple ER diagram (figure 1) for this tutorial.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1662977223445/GMNHJhwsS.png align="left")
(figure 1)

## Create Project
Next step, you should create a project. I will use PyCharm as the IDE for this tutorial, but you can use any IDE as you like. Click the file menu, click new project, and the following dialog (figure 2) will show up. Choose the location of the environment, then click create button and wait for the project set up to finish.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1662977247614/jUEJNnrOJ.png align="left")
(figure 2)
## Install alembic
Open up the project you have created, then open the terminal and install the alembic as follows:

/path/to/your/project/.venv/bin/pip install alembic


The above packages will be installed as well.
## Initialize the environment
Open up the terminal and initialize the environment as follows:

/path/to/your/project/.venv/bin/alembic init alembic

The alembic directory (figure 3) will be created.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1662977260630/RT78T2Wln.png align="left")
(figure 3)
Open up the alembic.ini file, and change the sqlalchemy.url(figure 4) to your database address.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1662977290137/mF8VKPTCX.png align="left")
(figure 4)
driver: database driver
User: database username
Pass: database password
Localhost: database IP address
Port: port number
dbname: database name
In my case, the address should be: 
mysql+mysqlconnector://root:admin@localhost:3306/alembic
## Create entity class
Create a new directory and create entity classes as follows.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1662977427062/QrHaB6zzd.png align="left")(figure 5)

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1662977491205/giVtyPz4G.png align="left")(figure 6)

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1662977501658/h7WiSgt-p.png align="left")(figure 7)

## Setup the env file for auto-generation
Import the entity classes you have created, and set up the target_metadata to it.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1662977526336/pW2CjmDrm.png align="left")(figure 8)
Open the terminal and generate the table creation script as follows:

alembic revision -- autogenerate -m “create tables” 

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1662977544686/5xNJVfn-L.png align="left")(figure 9)

Now we open the generated script and check it out.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1662977558869/0MmFNgdRZ.png align="left")(figure 10)

## Create tables by the script
The script looks fine, then we start to create the tables as follows:

alembic update head


![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1662977577131/rsxZGnlgc.png align="left")(figure 11)

Now, let’s check out the tables we created by database tools.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1662977588178/Xy5HKqqTl.png align="left")(figure 12)


Looks great!