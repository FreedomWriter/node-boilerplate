# What is a database?

tl;dr:

A database is an organized collection of data, generally stored and accessed electronically from a computer system

## Before databases there was the File Processing System.

Essentially resources were stored in files and had no connection to each other. In the case of an order processing system, instead of tables for each resource, there would be a file that existed for each resource. None of those files stored references to other files, so if something changed in one file, it would not be reflected in the rest of the system. Every one was building their own systems - no standardization. This meant every system was specific to a particular data, resulting in a user needing to learn a new system each time then needed to use a new FPS. Systems couldn't talk to each other, data was stored redundantly, it was difficult!

## Dabase Management System

From [techopedia]("https://www.techopedia.com/definition/24361/database-management-systems-dbms")

A database management system (DBMS) is a software package designed to define, manipulate, retrieve and manage data in a database. A DBMS generally manipulates the data itself, the data format, field names, record structure and file structure. It also defines rules to validate and manipulate this data.

Database management systems are set up on specific data handling concepts, as the practice of administrating a database evolves. The earliest databases only handled individual single pieces of specially formatted data. Today’s more evolved systems can handle different kinds of less formatted data and tie them together in more elaborate ways.

### Relation Database Management System (RDBMS)

A subset of DBMS.

The most common type of databases

## Database Model

A way to orgainze and store data

Some examples include:

- Hierarchical
- Networking
- Entity-Relationship
- Relaional
- Object Oriented
- Flat
- Semi-Structured
- And the list goes on...

## Relational Model

From [wiki](<"https://en.wikipedia.org/wiki/Relational_model#:~:text=The%20relational%20model%20(RM)%20for,of%20tuples%2C%20grouped%20into%20relations.">)

The relational model (RM) for database management is an approach to managing data using a structure and language consistent with first-order predicate logic, first described in 1969 by English computer scientist Edgar F. Codd,[1][2] where all data is represented in terms of tuples, grouped into relations. A database organized in terms of the relational model is a relational database.

The table structure was introduced with the relational model.
