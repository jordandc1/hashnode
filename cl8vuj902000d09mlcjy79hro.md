---
title: "SQLAlchemy Tutorial 1"
datePublished: Wed Oct 05 2022 16:30:42 GMT+0000 (Coordinated Universal Time)
cuid: cl8vuj902000d09mlcjy79hro
slug: sqlalchemy-tutorial-1
cover: https://cdn.hashnode.com/res/hashnode/image/unsplash/GNyjCePVRs8/upload/v1664987282023/MBqjEfQRf.jpeg
tags: python, databases, sqlalchemy

---

In the last article([How to use alembic to create your DB](https://chaodu.hashnode.dev/how-to-use-alembic-to-create-your-db#heading-create-entity-class)), I already talked about how to create databases by SQLAlchemy and alembic. Please check the link above if you still don't know how to do it.
# Getting Connection
To get a connection to the database, you just need to call the *create_engine* function with a proper string parameter.

```
from sqlalchemy import create_engine
engine = create_engine("postgresql://scott:tiger@localhost/test")
```

The string is formed by
```
dialect[+driver]://user:password@host/dbname[?key=value..]
```

- dialect: MySQL, Oracle, PostgreSQL, SQLite...
- driver: mysqldb, cx_oracle, psycopg2, pysqlite...
- user: database username
- password: database password
- host: localhost:3306
- dbname: schema name or database name
# CRUD with ORM
## Insert

To insert a row, you should create the object first, add it to the session you have created, and then commit the changes to the database.

```
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker

from entity.Student import Student

engine = create_engine("mysql+mysqlconnector://root:admin@localhost/alembic_demo", echo=True)

john = Student(stu_id=1, stu_name='John', stu_addr='123 Main St')
sessionmaker = sessionmaker(bind=engine)
session = sessionmaker()
session.add(john)
session.commit()
```

### How to return id during the insertion
If you set up the database table id column to autogenerate, you may need to know the id of the row you have inserted. The flush function can help you to get the id that the database generated for you.

```
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker

from entity.Student import Student

engine = create_engine("mysql+mysqlconnector://root:admin@localhost/alembic_demo", echo=True)

john = Student(stu_name='John', stu_addr='123 Main St')
sessionmaker = sessionmaker(bind=engine)
session = sessionmaker()
session.add(john)
session.flush()
# from this line you can get the id of the object
print('john's id is: ', john.id)
session.commit()
```

## Query

```
session.query(Student).filter(Student.stu_id == 1)
```

### Query with AND operators

1.  use and_() function
```
session.query(Student).filter(and_(Student.stu_id == 1, Student.stu_name == 'John')
```

2. use multiple filter() operation
```
session.query(Student).filter(Student.stu_id == 1).filter(Student.stu_name == 'John')
```
3. use multiple expressions in filter()
```
session.query(Student).filter(Student.stu_id == 1, Student.stu_name == 'John')
```


## Update

```
session.query(Student).filter(Student.stu_id == 1).update({Student.stu_name: 'John Doe'})
session.commit()
```

## Delete

```
session.query(Student).filter(Student.stu_id == 1).delete()
session.commit()
```

Do not forget to add a commit statement when you want to make changes to the database.



 