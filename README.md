# GRAPH THEORY
## Project 2017

### Table of Contents

##### 1. [Introduction](https://github.com/rndmized/GMIT_Timetable_GraphDB/wiki/1.-Introduction)
##### 2. [Overview](https://github.com/rndmized/GMIT_Timetable_GraphDB/wiki/2.-Overview)
##### 3. [Neo4J](https://github.com/rndmized/GMIT_Timetable_GraphDB/wiki/3.-Neo4J)
##### 4. [Assumptions/Constrains](https://github.com/rndmized/GMIT_Timetable_GraphDB/wiki/4.-Assumptions-Constrains)
##### 5. [Design Decisions](https://github.com/rndmized/GMIT_Timetable_GraphDB/wiki/5.-Design-Decisions)
##### 6. [Queries](https://github.com/rndmized/GMIT_Timetable_GraphDB/wiki/6.-Queries)
##### 7. [Conclusions](https://github.com/rndmized/GMIT_Timetable_GraphDB/wiki/7.-Conclusion)

### 1. Introduction

This repository contains a database design document and a graph database prototype for a third-year undergraduate project for the module Graph Theory. The module is taught to undergraduate students at GMIT in the Department of Computer Science and Applied Physics. The lecturer is Ian McLoughlin.

### 2. Overview

GMIT uses a database to store timetabling information. Courses, modules, different venues, are some of the pieces of information required to build a timetable. The main goal of this project is to design and implement, through a small prototype, a different way of store such data by using a Graph database called [Neo4J](https://neo4j.com/product/). To do so a Database Design Document is required in order to explain and detail what information needs to be stored, why is that information needed, what assumptions are made, contrains...

The project was guided by the following excerpt from the project instructions:
> The minimum standard for this project is a GitHub repository containing
a document detailing the design of the database and a prototype Neo4j
database following that design and using data from GMIT.
The document should clearly state what data needs to be stored by a
timetabling system, and how that data is to be represented in the database.
This means that you must clearly specify the design choices you have made
as to what data are represented as nodes, relationships, labels, relationship
types or properties. It should also specify, and give examples of, Cypher
queries for creating, retrieving, updating and deleting data.
The prototype database must contain data from GMIT’s own timetabling
system, in the structure set out in your design document. The data can be
copied or scraped from GMIT’s timetabling website, or otherwise obtained.
You should include, as a separate section in your design document, how you
obtained the data in your prototype database.

### 3. Neo4J
#### 3.1. What is Neo4J?
***
> [Neo4j](https://neo4j.com) is a graph database management system developed by Neo Technology, Inc. Described by its developers as an ACID-compliant transactional database with native graph storage and processing,[3] Neo4j is the most popular graph database according to db-engines.com.
Neo4j is available in a GPL3-licensed open-source "community edition", with online backup and high availability extensions licensed under the terms of the Affero General Public License. Neo also licenses Neo4j with these extensions under closed-source commercial terms.
Neo4j is implemented in Java and accessible from software written in other languages using the Cypher Query Language through a transactional HTTP endpoint, or through the binary 'bolt' protocol.

Excerpt from [Wikipedia](https://en.wikipedia.org/wiki/Neo4j) about Neo4J.

A graph database or graph-oriented database, is a type of NoSQL database that uses graph theory to store, map and query relationships. A graph database is, in essence, a collection of nodes and edges.

#### 3.2. How does Neo4J Store Information?
***
In Neo4j data is stored in the form of edges or relationships, nodes, or properties, also called attributes. Each node and relationship can have any number of properties amd labels. Labels can be used to group nodes or relationships and ease searches by narrowing them down.

* **Nodes**: A node is data that represents an entity.
* **Relationships**: A relationship occurs between two nodes. It represents a link or a conection between two nodes. There are different types of relationships. A relationship can be either directed or undirected, an action between two nodes, like teach, would be directed where a passive relationship, like sibling, would be undirected. Bi-directional relationships would make use of two relationships in neo4j.
* **Labels**: Labels can be applied to either nodes or relationships and can be used to froup them in a  certain category or to define a type of node or relationship. 
* **Properties**: Nodes and relationshos can have properties, usually relevant data to the node or the relationship and can be use when seaching for specific values. It is not as fast as narrowing down searches using labels but it adds a new layer of complexity for more specific purposes.

### 4. Assumptions/Constrains
#### 4.1. Assumptions
***
* Classes only start at hours on the dot. (Meaning 9:00, 10:00, never 16:30 or 13:22)
* The same module can be taught by different lecturers.
* The same module can be taught in different courses.
* Lectures are taught to every group in the course of every course that has that module.

#### 4.2. Constrains
***
* A lab can only be taught to one course group at a time. (Either A, B or C).
* If a course has only one group the default group will be A.
* A lab can only be taught to one course at a time.
* A class cannot be taught by a lecturer who does not teach the module.

### 5. Design Decisions
#### 5.1. What information needs to be stored?
***
Based on the actual timetable and the information that display we can assume that there are 5 questions the timetable must respond: **What**, **When**, **Where**, **By** **Who** and **To** **Whom**.
* _What_ in this case _what_ is the module is being taught. 
* _When_  is the time slot in which the _what_ (module) is taught.
* _Where_ is the room or venue where a _what_ is taught at a given _when_.
* _By_ _Who_ is the lecturer who teaches a given _what_.
* _To_ _Whom_ is the target of a given _what_ so the group of students who are taught.

#### 5.2. How is that information stored?
***
In order to answer those questions the database has been designed roughly as follows:

- **Module**: A **node** containing the information to a module such as its name. (_What_)
- **Room**: A **node** containing information about a venue where classes are held, such as maximum capacity and room number.(_Where_)
- **Course**: A **node** containing information relative to a course such as course code. (_to_ _whom_) 
- **Class**: A **node** containing the class type: either a lab or a lecture.
- **Lecturer**: A **node** containing data about a lecturer such as name. (_Who_)
- **Has**: **Relationship** between a Module and the Classes it might have (_lab_ or _lecturer_).
- **Taught** **By**: **Relationship** between a Lecturer and a Module.  Indicates _who_ teaches _what_.
- **Taught** **To**: **Relationship** between a Module and a Course. Indicates _what_ is taught _to_ _whom_.
- **On**: **Relationship** between Module and Room. Expressing the relationship between both node, _**time**_ being such relationship the day and time a _what_ is taught on _where_. In addition, in order to comply with the constrains where more than one lecturer can teach more than one module an attribute has been added to store such data. It also contains the group is being taught in case that the module has a lab, and the couse has more than one group. It indicates as well the duration the class is on.

The database is designed to be interfaced with a piece of software with the adequate validation checks in order to ensure data integrity.

#### 5.3. Where does the data come from?
***

Data used to create the prototype database has been scrapped from the GMIT timetable [website](http://timetable.gmit.ie). Rooms/Venues have been obtained by copying the  relevant website´s source code and copied them into notepad, and removed the option tags. Lecturers names are either fictional or historical characters. Cousrses, modules and times are actual elements and have been extracted from the timetable for each one. All base data is in the folder data_sets in this repository. 

### 6. Querying the Database
##### 6.1. Cypher Queries Examples: Retrieving Information
***

Retrieving modules per course (for all courses):
* MATCH (n)-[r:TAUGHT_TO]-(m) RETURN n,m;

Retreiving all modules taught on Tuesday to KSOG73 Group C:

* MATCH (n:MODULE)-[r0]-(c)-[r:ON]-(v) WHERE r.day = 'Tue' and r.to = 'KSOFG73' and r.group = 'C' RETURN n,c;

Retreive modules taught by Charles Xavier:

* MATCH (m)-[r:TAUGHT_BY]-(l) WHERE l.name = 'Charles Xavier' RETURN l,m;

##### 6.2. Cypher Queries Examples: Creating Nodes and Relationships.
***
##### When creating a **node** the structure is as follows: CREATE (alias:Label{attribute-key1:attribute-value1}) 
##### When creating a **relationship** the structure is as follows: CREATE (alias-node1)-[:Label{attribute-key1:attribute-value1, .... }]->(alias-node2)
##### When deleting a **node** the structure is as follows: MATCH (n) DELETE n;  
##### When deleting a **relationship** the structure is as follows: MATCH (n)-[r]-(m) DELETE r;
Labels and Attributes can be used to narrow down or be more specific about what node/relationship to delete.

##### Creating Lecturer
* CREATE (Charles:LECTURER{name:'Charles Xavier'}) 

##### Creating Module
* CREATE (GRAPH:MODULE{name:'GRAPH THEORY'})

##### Creating Class
* CREATE (Lecture1:CLASS{type:'lecture'})
* CREATE (Lab1:CLASS{type:'lab'})

##### Creating Course
* CREATE (SoftDevY3:COURSE{name:'Software Development Year 3',code:'KSOFG73'})

##### Creating Relationships
* CREATE (GRAPH)-[:TAUGHT_BY]->(Charles)

In the folder _**queries**_, in this repository there are a few text files containing queries to create nodes, and to recreate the prototype database _(cypher_query_v2.txt)_.


### 7. Conclusion
***

### Authors
* **Albert Rando** - *Design, Documentation and Coding* - [rndmized](https://github.com/rndmized) - [LinkedIn](https://www.linkedin.com/in/albert-rando-612551121/)

### License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details

