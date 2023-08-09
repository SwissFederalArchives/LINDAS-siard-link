## Introduction

### Goal of the project

The goal of this project is to convert databases to SIARD as archival
format and from SIARD to a RDF knowledge graph for access and
visualization.

For this purpose a SIARD ontology has to be developed. This will be done
step by step based on examples.

### Test Data

The project uses several datasets. For testing/development, the database
**olympics.siard** of Kost Ceco is an example of a very simple SIARD
file. This database is used to develop the basic mechanisms of the SIARD
mapping. It can be downloaded from the Kost Ceco website:
[ttps://kost-ceco.ch/cms/siard.html](https://kost-ceco.ch/cms/siard.html)

The **Stapfer-Enquête database** contains the results from a survey of
the school situation in Switzerland in the Helvetic Republic from 1799.

MySQL-Dump:
[https://opendata.swiss/de/dataset/stapfer-enquete-schule-in-der-schweiz-1799](https://opendata.swiss/de/dataset/stapfer-enquete-schule-in-der-schweiz-1799)

## Creating SIARD from MySQL databases

The goal is to create a SIARD-File out of a MySQL database schema.

The BAR SIARD Tools
([https://github.com/sfa-siard](https://github.com/sfa-siard))
have problems with MySQL Server v5.7 and higher versions because of the
internal "performace-schema" database. A possible solution would be to
disable the performance-tools in MySQL but this route hasn't been
investigated further.

In a second try, the Database Preservation Toolkit (
[https://database-preservation.com/](https://database-preservation.com/))
has been used. This tool can connect to a local MySQL database server.
The following screenshots are from the Desktop edition. There are also
command line tools available for the conversion
([https://github.com/keeps/dbptk-developer](https://github.com/keeps/dbptk-developer)).

Visualisation of the Stapfer-Enquête database:

![](media/image9.png){width="6.267716535433071in"
height="4.069444444444445in"}

To create a SIARD-file, a custom view could be defined (not necessary
for the Stapfer-Enquête):

![](media/image3.png){width="6.267716535433071in"
height="2.3333333333333335in"}

SIARD-File is the created and can be imported into the viewer

![](media/image5.png){width="5.25in" height="4.71875in"}

The DBPTK can also visualize SIARD files:

![](media/image7.png){width="6.267716535433071in"
height="5.069444444444445in"}

## Structure of SIARD files

A SIARD file contains two folders:

-   **header:** this folder contains the metadata.xml file with all
    > meta-information about the database schemas, their tables and
    > fields, as well as additional information regarding primary keys,
    > user rights etc.

-   **content:** this folder contains the tables for one or several
    > database schemas. The content of the table is stored in XML-Files.

To map this structure to RDF, the information that is distributed over
the two folders must be brought together.

![](media/image1.png){width="6.267716535433071in"
height="5.180555555555555in"}

*SIARD 2.2 (2021), p. 17*

## Structure in RDF

For the mapping of SIARD to RDF the information in metadata.xml and in
the XML files of the various tables has to be combined.

### Metadata in metadata.xml

Based on the metadata.xml file, a first approach to organize the
information into classes and properties looks as follows:


  **Class**     |  **Property**     |  **Remarks**      |
|  -------------| ------------------| ------------------|
| Database      |                   |                   |
|               | dbname            |                   |
|               | dataOwner         |                   |
|				|producerApplication|					|
|				|	archivalDate	|					|
|				|	messageDigest	|					|
|				|	clientMachine	|					|
|				|databaseProduct	|					|
|				|connection			|					|
|				|databaseUser		|					|
|Schemas		|					|					|
|				|					|					|
|Schema			|					|					|
|				|name				|					|
|				|folder				|Points to schema-folder|
|				|					|					|
|Tables			|					|					|
|				|					|					|
|Table			|					|					|
|				|name				|					|
|				|folder				| Points to table-folder (within schema-folder)|
|				|description					|					|
|				|					|					|
|Columns		|					|					|
|				|					|					|
|Column			|					|					|
|				|name				|No direct pointer from name to cell number (eg. C1) in table.xml. Depends on the sequence.|
|				|type				|					|
|				|typeOriginal		|					|
|				|nullable			|					|
|				|					|					|
|PrimaryKey		|					|					|
|				|name				|					|
|				|column				|Points to Column name|
|				|					|					|
|Users			|					|					|
|				|					|					|
|User			|					|					|
|				|name				|					|
|				|admin				|					|
|				|					|					|
|Roles			|					|					|
|				|					|					|                       
|Role			|					|					|
|				|name				|					|                                        
|				|admin				|					|

### Table values in table.xml-files)

In SIARD, the content of tables, rows and cells is encoded in the
following way: 

\\<row\\>

\\<c1\\>76\\</c1\\>

\\<c2\\>1\\</c2\\>

\\<c3\\>Bourgain\\</c3\\>

\\<c4\\>Mikael\\</c4\\>

\\<c5\\>6\\</c5\\>

\\</row\\>

\\<row\\>

\\<c1\\>22\\</c1\\>

\\<c2\\>1\\</c2\\>

\\<c3\\>Rasmussen\\</c3\\>

\\<c4\\>Mads Reinholdt\\</c4\\>

\\<c5\\>3\\</c5\\>

\\</row\\>

As it is typical for relational databases, the recordsets (rows) in a
table have no specific order.

|  Table    |       				|        			|
|  ---------| ----------------------| ------------------|
| Row       |                   	|                   |
|	 		|                       |
|           | c1                    |
|           | c2                    |
|           | c...                  |
|           | cn                    | 

### Interlinking between metadata.xml and tableN.xml

![](media/image4.png){width="6.267716535433071in"
height="4.194444444444445in"}

*Link between metadata.xml and table1.xml*

## Mapping

### URI conventions

One important aspect of mapping SIARD to RDF is the creation of unique
identifiers for each entity.

**Conventions**

Base: https://\[theme\].ld.admin.ch/

The \[theme\] is defined by the Federal Archive, eg.
[https://register.ld.admin.ch](https://register.ld.admin.ch)


Database: Base:		\[dbname\]

(from metadata.xml: /siardArchive/dbname)

The dbname is defined by the delivering agency in the administration


Schema Database:	\[schema\]

(from metadata.xml: /siardArchive/schemas/schema/folder)


Table 				https://ld.admin.ch/dbname/schema/table

(from metadata.xml: /siardArchive/schemas/schema/tables/table/folder)


Column 				https://ld.admin.ch/dbname/schema/table/column

(from metadata.xml:
/siardArchive/schemas/schema/tables/table/columns/column/name)


Row 				https://ld.admin.ch/dbname/schema/table/row/r1

(from table.xml: count /table/row)


Cell 				https://\[ld.admin.ch\]/dbname/schema/table/cell/r1/c1)

(from table.xml: /table/row/c1)


**Example**

Base https://ld.admin.ch/

Database https://ld.admin.ch/olympics

Schema https://ld.admin.ch/olympics/OLYMPICS

Table https://ld.admin.ch/olympics/OLYMPICS/Countries

Column https://ld.admin.ch/olympics/OLYMPICS/Countries/ID

https://ld.admin.ch/olympics/OLYMPICS/Countries/Country

Row https://ld.admin.ch/olympics/OLYMPICS/Countries/row/r1

Cell https://ld.admin.ch/olympics/OLYMPICS/Countries/cell/r1/c1

### Metadata.xml

The metadata.xml file stores static information about the SIARD
database, its schema(s), tables and attributes. Converted to turtle
these are the main informations about the database in the siard:DB
class:

\\<https://ld.admin.ch/olympics\\> a \\<http://siard.link#DB\\>;

\\<http://siard.link#archivalDate\\> \\"2010-04-26\\";

\\<http://siard.link#clientMachine\\> \\"edixp1096.edi.intra.admin.ch\\";

\\<http://siard.link#connection\\>
\\"jdbc:oracle:thin:@dbhost.enternet.ch:1521:SIARD1\\";

\\<http://siard.link#dataOriginTimespan\\> \\"(\\...)\\";

\\<http://siard.link#dataOwner\\> \\"(\\...)\\";

\\<http://siard.link#databaseProduct\\> \\"\\"\\"

Oracle Oracle9i Enterprise Edition Release 9.2.0.1.0 -

Production\\\\\\\\u000AWith the Partitioning, OLAP and Oracle Data Mining

options\\\\\\\\u000AJServer Release 9.2.0.1.0 - Production

\\"\\"\\";

\\<http://siard.link#databaseUser\\> \\"OLYMPICS\\";

\\<http://siard.link#dbname\\> \\"olympics\\";

\\<http://siard.link#messageDigest\\>
\\"MD57A1F5B19710EB2F17BA76D19C76FFD59\\";

\\<http://siard.link#producerApplication\\> \\"SiardEdit 1.19 Swiss Federal
Archives, Berne, Switzerland, 2007, 2008\\";

\\<http://siard.link#hasSchema\\>
\\<https://ld.admin.ch/olympics/OLYMPICS\\>;

\\<http://siard.link#hasUser\\> \\<https://ld.admin.ch/user/OLYMPICS\\>;

\\<http://siard.link#hasRole\\> \\<https://ld.admin.ch/role/CONNECT\\>,
\\<https://ld.admin.ch/role/RESOURCE\\> .

Database schema mapped to siard:Schema:

\\<https://ld.admin.ch/olympics/OLYMPICS\\> a
\\<http://siard.link#Schema\\>;

\\<http://siard.link#name\\> \\"OLYMPICS\\";

\\<http://siard.link#folder\\> \\"schema0\\";

\\<http://siard.link#hasTable\\> \\<https://ld.admin.ch/table/Countries\\>,
\\<https://ld.admin.ch/table/Disciplines\\>,

\\<https://ld.admin.ch/table/Events\\>,
\\<https://ld.admin.ch/table/Participants\\>,
\\<https://ld.admin.ch/table/Sports\\> .

For each table we have the following information:

\\<https://ld.admin.ch/olympics/OLYMPICS/Countries\\> a
\\<http://siard.link#Table\\>;

\\<http://siard.link#folder\\> \\"table1\\";

\\<http://siard.link#name\\> \\"Countries\\";

\\<http://siard.link#primaryKeyColumn\\> \\"ID\\";

\\<http://siard.link#primaryKeyName\\> \\"PrimaryKey\\";

\\<http://siard.link#rows\\> \\"6\\";

\\<http://siard.link#hasColumn\\>
\\<https://ld.admin.ch/olympics/OLYMPICS/Countries/ID\\>,
\\<https://ld.admin.ch/olympics/OLYMPICS/Countries/Country\\> .

For each database field (column) we can map the name and datatype,
extracted from the metadata.xml file:

\\<https://ld.admin.ch/olympics/OLYMPICS/Countries/ID\\> a
\\<http://siard.link#Column\\>;

\\<http://siard.link#name\\> \\"ID\\";

\\<http://siard.link#nullable\\> \\"false\\";

\\<http://siard.link#type\\> \\"DECIMAL(38)\\";

\\<http://siard.link#typeOriginal\\> \\"NUMBER(38)\\" .

\\<https://ld.admin.ch/olympics/OLYMPICS/Countries/Country\\> a
\\<http://siard.link#Column\\>;

\\<http://siard.link#name\\> \\"Country\\";

\\<http://siard.link#nullable\\> \\"true\\";

\\<http://siard.link#type\\> \\"CHARACTER VARYING(50)\\";

\\<http://siard.link#typeOriginal\\> \\"VARCHAR2(50)\\" .

### Mapping of database content

When mapping the content of the database tables, the following mapping
rules apply:

  
|  **SIARD**                          | **SIARD RDF**
|  -----------------------------------| -----------------------------------
|  DB                                 | Instance of class siard:DB
|  Schema                             | Instance of class siard:Schema
|  Table                              | Instance of class siard:Table
|  Record (row)                       | Instance of class siard:Row
|  Attribute of a record              | Instance of class siard:Cell
|  Database field definition          | Instance of class siard:Column

The tables of the SIARD database are mapped to the class siard:Table,
the records to the class siard:Row.

The attributes with the values of the database fields are mapped to the
class siard:Cell. The attributes cannot simply be converted to RDF
properties, because they include a relation to the field definition in
siard:Column and must therefore be mapped into a dedicated class.

![](media/image8.png){width="4.883171478565179in"
height="5.827083333333333in"}

**siard:Schema**

First, the database schema has to be described, based on information in
metadata.xml. The schema points to one or several tables with the
siard:hasTable relation.

\\<https://ld.admin.ch/olympics/OLYMPICS\\> a
\\<http://siard.link#Schema\\>;

\\<http://siard.link#name\\> \\"OLYMPICS\\";

\\<http://siard.link#folder\\> \\"schema0\\";

\\<http://siard.link#hasTable\\>
\\<https://ld.admin.ch/olympics/OLYMPICS/Countries\\>,
\\<https://ld.admin.ch/olympics/OLYMPICS/Disciplines\\>,

\\<https://ld.admin.ch/olympics/OLYMPICS/Events\\>,
\\<https://ld.admin.ch/olympics/OLYMPICS/Participants\\>,
\\<https://ld.admin.ch/olympics/OLYMPICS/Sports\\> ;

\\<http://www.w3.org/ns/prov#wasDerivedFrom\\> \\"metadata.xml\\".

**siard:Table**

Second, the database tables have to be identified and describeld. This
is done with the name in metadata.xml and not just with "table0". The
table can be empty or points to its record(s):

\\<https://ld.admin.ch/olympics/OLYMPICS/Countries\\> a
\\<http://siard.link#Table\\>;

\\<http://siard.link#folder\\> \\"table1\\";

\\<http://siard.link#name\\> \\"Countries\\";

\\<http://siard.link#primaryKeyColumn\\> \\"ID\\";

\\<http://siard.link#primaryKeyName\\> \\"PrimaryKey\\";

\\<http://siard.link#rows\\> \\"6\\";

\\<http://siard.link#hasColumn\\>
\\<https://ld.admin.ch/olympics/OLYMPICS/Countries/ID\\>,
\\<https://ld.admin.ch/olympics/OLYMPICS/Countries/Country\\> ;

\\<http://siard.link#hasRow\\>
\\<https://ld.admin.ch/olympics/OLYMPICS/Countries/row/r1\\>,
\\<https://ld.admin.ch/olympics/OLYMPICS/Countries/row/r2\\>,
\\<https://ld.admin.ch/olympics/OLYMPICS/Countries/row/r3\\>,
\\<https://ld.admin.ch/olympics/OLYMPICS/Countries/row/r4\\>,
\\<https://ld.admin.ch/olympics/OLYMPICS/Countries/row/r5\\>,
\\<https://ld.admin.ch/olympics/OLYMPICS/Countries/row/r6\\> ;

\\<http://www.w3.org/ns/prov#wasDerivedFrom\\> \\"metadata.xml\\".

**siard:Column**

The siard:Table points to the siard:Column class that contains the
definition of the database fields.

\\<https://ld.admin.ch/olympics/OLYMPICS/Countries/ID\\> a
\\<http://siard.link#Column\\>;

\\<http://siard.link#name\\> \\"ID\\";

\\<http://siard.link#nullable\\> \\"false\\";

\\<http://siard.link#type\\> \\"DECIMAL(38)\\";

\\<http://siard.link#typeOriginal\\> \\"NUMBER(38)\\" ;

\\<http://www.w3.org/ns/prov#wasDerivedFrom\\> \\"metadata.xml\\".

\\<https://ld.admin.ch/olympics/OLYMPICS/Countries/Country\\> a
\\<http://siard.link#Column\\>;

\\<http://siard.link#name\\> \\"Country\\";

\\<http://siard.link#nullable\\> \\"true\\";

\\<http://siard.link#type\\> \\"CHARACTER VARYING(50)\\";

\\<http://siard.link#typeOriginal\\> \\"VARCHAR2(50)\\" ;

\\<http://www.w3.org/ns/prov#wasDerivedFrom\\> \\"metadata.xml\\".

**siard:Row**

In this example, the table contains six database records, represented
with the class siard:Row. The database record contains two attributes.

\\<https://ld.admin.ch/olympics/OLYMPICS/Countries/row/r1\\> a
\\<http://siard.link#Row\\>;

\\<http://siard.link#hasCell\\>
\\<https://ld.admin.ch/olympics/OLYMPICS/Countries/cell/r1/c1\\>,
\\<https://ld.admin.ch/olympics/OLYMPICS/Countries/cell/r1/c2\\>;

\\<http://www.w3.org/ns/prov#wasDerivedFrom\\> \\"table1.xml\\".

**siard:Cell**

The value of the attributes are represented in their own class
siard:Cell. The siard:Cell contains not only the value in a literal, but
also a siard:columnType link to the definition of the database field in
siard:Column.

\\<https://ld.admin.ch/olympics/OLYMPICS/Countries/cell/r1/c1\\> a
\\<http://siard.link#Cell\\>;

\\<http://siard.link#value\\> \\"1001\\";

\\<http://siard.link#columnType\\>
\\<https://ld.admin.ch/olympics/OLYMPICS/Countries/ID\\>;

\\<http://www.w3.org/ns/prov#wasDerivedFrom\\> \\"table1.xml\\".

\\<https://ld.admin.ch/olympics/OLYMPICS/Countries/cell/r1/c2\\> a
\\<http://siard.link#Cell\\>;

\\<http://siard.link#value\\> \\"Germany\\";

\\<http://siard.link#columnType\\>
\\<https://ld.admin.ch/olympics/OLYMPICS/Countries/Country\\>;

\\<http://www.w3.org/ns/prov#wasDerivedFrom\\> \\"table1.xml\\".

### Primary Keys / Foreign Keys

In relational databases, relations between database tables are created
with primary keys / foreign keys pointing to each other. The foreign key
of table A points to the primary key (unique identifier) of table B.

![](media/image2.png){width="6.267716535433071in"
height="2.861111111111111in"}

The keys can also consist of several concatenated fields. SIARD can
handle this use case.

![](media/image6.png){width="6.267716535433071in"
height="3.1805555555555554in"}

In the second example, two cells together form the foreign key / primary
key. In addition, primary keys and foreign keys can have attributes, as
it is described in the SIARD specification, chapters 5.8 and 5.9.

## Ontology 



## Implementation and automation of the Mapping

[\[Florian\]]{.mark}

[Dokumentation Werkzeug]{.mark}

 

## References

Bytyçi, E., Ahmedi, L., & Gashi, G. (2018). RDF Mapper: Easy Conversion
of Relational Databases to RDF: Proceedings of the 14th International
Conference on Web Information Systems and Technologies, 161--165.
[https://doi.org/10.5220/0006925501610165](https://doi.org/10.5220/0006925501610165)

SIARD 2.2 Format Specification (2021).
[https://siard.dilcis.eu/SIARD%202.2/SIARD%202.2.pdf](https://siard.dilcis.eu/SIARD%202.2/SIARD%202.2.pdf).

Gelati, F. (2019). Die nachhaltige Bewahrung einer Forschungsdatenbank
durch Linked Data. Laut welchem Vokabular? Library Ideas:
[https://hal.archives-ouvertes.fr/hal-02427346/](https://hal.archives-ouvertes.fr/hal-02427346/)

## Appendix

### Appendix A: metadata.xml expressed in RDF-Turtle
