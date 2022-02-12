# Chapter 2: Data Models and Query Languages

- [Data Model](#data-model)
- [Relational Model vs Document Model](#relational-model-vs-document-model)
  - [Relational model](#relational-model)
  - [NoSQL](#nosql)
  - [One-to-many relationship : JSON > Relational](#one-to-many-relationship--json--relational)
  - [Many-to-one and many-to-many relationship](#many-to-one-and-many-to-many-relationship)

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

### One-to-many relationship : JSON > Relational

* Most applications written in OOPs. Some transformation is required to store objects in relational tables, called Object-Relational Mapping

#### Example: LinkedIn/resume as a relational schema

* each person could be uniquely identified by `user_id`
* `first_name` and `last_name` appear once - so columns of user table
* but people have multiple `jobs`, `education` and `contact` (one-to-many relationship from user to these items)

![Resume as a relational schema](https://i.imgur.com/EXDwJcw.jpg)

    + Approach 1 - keep `jobs`, `education` and `contact` info in separate tables. Use `user_id` as foreign key
    + Approach 2 - Later versions of SQL allowed structured datatype, XML or JSON type (PostgreSQL) - multiple values in a row to be stored. Values inside these structure could be indexed as well
    + Approach 3 - Encode the info into JSON and store as text. Can't index inside the JSON/document

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


### Many-to-one and many-to-many relationship

* multiple people living in the same region or working in the same company
* Previous example, `regions` and `industry` were stored with ID (standardized) instead of plain text
* Advantages
    + consistent styling
    + ease of update (need to update in only one place)
    + translation easier
* IDs prevent duplications due to inconsistencies
* Normalization - removing the scope of duplication and inconsistencies
    + Doesn't fit well in document model but nicely fit for relational

NOTE: Initially application data fits nicely in one-to-many model. Joins are not needed/weak but can be handled in the application code. As application/features grow, data becomes more interconnected and then make sense to think about joins.

![Many-to-many relationship](https://i.imgur.com/wD4brgD.jpg)