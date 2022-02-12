# Chapter 2: Data Models and Query Languages

- [Data Model](#data-model)
- [Relational Model vs Document Model](#relational-model-vs-document-model)
  * [Relational model](#relational-model)
  * [NoSQL](#nosql)
    + [Why NoSQL?](#why-nosql-)
  * [One-to-many relationship : Document > Relational](#one-to-many-relationship---document---relational)
    + [Example: LinkedIn/resume as a relational schema](#example--linkedin-resume-as-a-relational-schema)
  * [Many-to-one and many-to-many relationship : Relational > document](#many-to-one-and-many-to-many-relationship---relational---document)
  * [Document databases reimerging](#document-databases-reimerging)
    + [Network model](#network-model)
    + [Relational model](#relational-model-1)
    + [Document model](#document-model)
  * [Relational vs Document Today](#relational-vs-document-today)
    + [Why document?](#why-document-)
    + [Why relational?](#why-relational-)
    + [Which model leads to simpler application code?](#which-model-leads-to-simpler-application-code-)
    + [Schema flexibility](#schema-flexibility)
    + [Data locality](#data-locality)
    + [Convergence of relational and document](#convergence-of-relational-and-document)

## Data Model

* Data Model means how data is represented. It could be for reading, writing, querying or processing.
* Most applications are built by layers of various data model. The layers are built by assuming how the data can be represented in the nth layer given the representation in the (n-1)th layer

    - Real world entities are modelled as classes or objects (data structures) which are generally specific to the application.
    - These objects/data structures are represented in a general-purpose data model like relational table, JSON or XML documents or graph in databases for storage
    - In databases, JSON/table/graph is represented as bytes in memory or disk
    - In lower levels, these bytes are represented as voltage, electrical current and more.

This chapter focuses on general-purpose data models which are used for storage and querying the data.
* compare relational, document and graph-based model
* introduce query languages and compare their usecases

## Relational Model vs Document Model

### Relational model

* data is organized into relations (tables)
* each relation is an unorganized collection of tuples (rows)
* roots of relational database is business data processing - transactions (entire sales, reservations, etc) and batch processing (payroll, invoicing)
* Problem before relational databases - other databases forced to think a lot about internal data representation. Relational solved the problem=> hiding implementation details with cleaner interface
* Since 1980s the network model and hierarchical model were main alternatives to the domination of relational.
* Early 2000, XMLs had niche adoption but never took off
* Relational has been dominating and generalizing well beyond business data processing usecases - social networking, games, SaaS applications, etc.

### NoSQL

* Doesn't refer to any particular technology
* Started as a catchy Twitter hashtag for an meetup on open-source, distributed non-relational databases but the name stuck
* Retroactively interpreted as *Not Only SQL*

#### Why NoSQL?

* more scalable database than relational for very large datasets or very high throughput
* more dynamic and expressive schema rather than restrictiveness of relational model

### One-to-many relationship : Document > Relational

* Most applications written in OOPs. Some transformation is required to store objects in relational tables, called Object-Relational Mapping

#### Example: LinkedIn/resume as a relational schema

![Resume as a relational schema](https://i.imgur.com/EXDwJcw.jpg)

* each person could be uniquely identified by `user_id`
* `first_name` and `last_name` appear once - so columns of user table
* but people have multiple `jobs`, `education` and `contact` (one-to-many relationship from user to these items)

    * Approach 1 - keep `jobs`, `education` and `contact` info in separate tables. Use `user_id` as foreign key
    * Approach 2 - Later versions of SQL allowed structured datatype, XML or JSON type (PostgreSQL) - multiple values in a row to be stored. Values inside these structure could be indexed as well
    * Approach 3 - Encode the info into JSON and store as text. Can't index inside the JSON/document

```json
{
    "user_id": 251,
    "first_name": "Bill",
    "last_name": "Gates",
    "summary": "Co-chair of the Bill & Melinda Gates... Active blogger.",
    "region_id": "us:91",
    "industry_id": 131,
    "photo_url": "/p/7/000/253/05b/308dd6e.jpg",
    "positions": [
        {"job_title": "Co-chair", "organization": "Bill & Melinda Gates Foundation"},
        {"job_title": "Co-founder, Chairman", "organization": "Microsoft"}
    ],
    "education": [
        {"school_name": "Harvard University", "start": 1973, "end": 1975},
        {"school_name": "Lakeside School, Seattle", "start": null, "end": null}
    ],
    "contact_info": {
        "blog": "http://thegatesnotes.com",
        "twitter": "http://twitter.com/BillGates"
    }
}
```

* JSON is more natural to store resume than a relational model
* JSON better data-locality (relational either multiple queries or messy joins)
* One-to-many relation has an implicit tree representation and JSON more suited for this
* JSON less impedance between storage and application code than ORMs


### Many-to-one and many-to-many relationship : Relational > document

* multiple people living in the same region or working in the same company
* Previous example, `regions` and `industry` were stored with ID (standardized) instead of plain text
* Advantages
    + consistent styling
    + ease of update (need to update in only one place)
    + translation easier
* IDs prevent duplications due to inconsistencies
* Normalization - removing the scope of duplication and inconsistencies
    + Doesn't fit well in document model but nicely fit for relational

**NOTE**: Initially application data fits nicely in one-to-many model. Joins are not needed/weak but can be handled in the application code. As application/features grow, data becomes more interconnected and then make sense to think about joins and many-to-many relationsips.

![Many-to-many relationship](https://i.imgur.com/wD4brgD.jpg)

### Document databases reimerging

* Before SQL, IBM's Information Management System (IMS) was most popular for business data processing.
* IMS data model was called hierarchical model (similar to JSON). Very difficult to maintain many-to-many relationships and did not support joins (like a document model).
* Solutions proposed to solve limitations of hierarchical - *relational* model and *network* model

#### Network model

* Hierarchical - each record has exactly one parent
* Network - each record can have multiple parents (allowed many-to-one and many-to-many relationships)
    * e.g. one record of "Bengaluru" and multiple "users" living in Bengaluru could be linked to it
    * links are NOT foreign key but paths like pointers
    * to access a record, traverse a path from root (called **access path**)

* Problem - querying and updating is complicated, slow and inflexible. Need to know the access path.
    * Simplest case - traversing a linked list
    * in many-to-many relationships, multiple path can lead to same record
    * need to keep track of different paths
    * live traversing in an n-dimensional space
    * to query, you need to know the access path, otherwise lost

#### Relational model

* Simple idea - relation is a simple collection of tuples(rows). No nested structure.
    * Can read any number of records
    * Can select rows based on arbritary condition
    * Can update by simply inserting a row (without worrying about foreign key relationships)

* The **query optimizer** decides
    * which part of the query to run first
    * which indexes to choose

* essentially does the job of the access path automatically

* need to write query optimizer once and all applications using the database can benefit from it. 
* Initially might seem easier to harcode the access path for a particular type of query but query optimizer makes sense in the longer run

#### Document model

* Trying to be best of both world
* Similar to hierarchical model - storing nested record (one-to-many relationship)
* Similar to relational model - for many-to-many and many-to-one
    * related items are referenced by unique indentifier
    * relational - *foreign key*, document - *document reference*
    * references resolved during joins

### Relational vs Document Today

#### Why document?
* Schema flexibility
* Data locality
* Less impedance between storage and object (ORM)
* Better for heterogenous data

#### Why relational?
* Better support for joins
* many-to-one and many-to-many relationships

#### Which model leads to simpler application code?
* If application data has tree-like structure (one-to-many)
    * choose **document**
    * relational lead to unnecessary complicated schema

* If application data has many-to-many relationship
    * document database less appealing because poor support of join
    * logic for join need to be tackled in application code => complicated code and may lead to worse performance
    * **relational** makes more sense
    * if highly interconnected, **graph** models are more natural

#### Schema flexibility

* Most document databases and JSON do not enforce schema (schemaless)
* But application code does assume some kind of schema so kind of implicit schema
* *Schema-on-write*: relational
* *Schema-on-read*: document
* If schema needs to changes or update the format of data
* document: write documents with new fields and handle it in the application code

```C
if (user && user.name && !user.first_name) {
    // Documents written before Dec 8, 2013 don't have first_name
    user.first_name = user.name.split(" ")[0];
}
```

* relational: perform *migration* which is slow.

```sql
ALTER TABLE users ADD COLUMN first_name text;
UPDATE users SET first_name = split_part(name, ' ', 1); -- PostgreSQL
UPDATE users SET first_name = substring_index(name, ' ', 1); -- MySQL
```

* If the application code is hetergenous in nature or it is controlled by some external/third-party services, having a schema will hurt. Schemaless better.

#### Data locality

* Documents are stored as continuous strings encoded as JSON/XML/BSON(MongoDB), etc.
* If application needs data from entire document, documents better locality. Relational would need to fetch from multiple indexes, more disk seeks, more time.
* Keep documents smaller since the entire document is loaded in memory even if a small part is required.
* **Google Spanner** - locality in the relational model by allowing schema to declare that table's row should be nested within a parent table

#### Convergence of relational and document

* Most relationals allow XML and querying and indexing within XML structure
* PostgreSQL, MySQL also allows JSON support
* Document databases like RethinkDB allows relational-like join in its query
* MongoDB drivers resolves document references on the client-side (effectively slower than if it had happened at the database itself)