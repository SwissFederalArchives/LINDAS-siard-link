# Mapping SIARD to SIARD RDF


Tobias Wildi, FHGR

Version 29.3.2023

Content

**[Introduction](#introduction) 3**

> [Goal of the project](#goal-of-the-project) 3
>
> [Test Data](#test-data) 3

**[Creating SIARD from MySQL
databases](#creating-siard-from-mysql-databases) 4**

**[Structure of SIARD files](#structure-of-siard-files) 6**

**[Structure in RDF](#structure-in-rdf) 7**

> [Metadata in metadata.xml](#metadata-in-metadata.xml) 7
>
> [Table values in table.xml-files)](#table-values-in-table.xml-files) 9
>
> [Interlinking between metadata.xml and
> tableN.xml](#interlinking-between-metadata.xml-and-tablen.xml) 10

**[Mapping](#mapping) 11**

> [URI conventions](#uri-conventions) 11
>
> [Metadata.xml](#metadata.xml) 12
>
> [Database mapping](#mapping-of-database-content) 14

**[Implementation of the
Mapping](#implementation-and-automation-of-the-mapping) 19**

> [Declarative mapping with RML](#_fe4zwjnzfeye) 19
>
> [Mapping results for metadata.xml](#_jabu0yi0p6zu) 19
>
> [Mapping results for the content](#_ne5xyauoo7vv) 19

**[References](#references) 20**

**[Appendix](#appendix) 21**

> [Appendix A: metadata.xml expressed in
> RDF-Turtle](#appendix-a-metadata.xml-expressed-in-rdf-turtle) 21

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

##  
