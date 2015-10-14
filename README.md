# Intro to NoSQL with Mongo

### Objectives
- Describe how Mongo databases came about & why they're useful
- Compare and contrast NoSQL with SQL
- Define what a document is in the context of MongoDB
- Explain the difference between embedded and referenced documents, and how we use each to model relationships in MongoDB
- Issue basic CRUD commands to a database from the Mongo Shell

#### Overview - Mongo & NoSQL Databases

MongoDB is one of the new breeds of databases known as NoSQL databases. NoSQL databases are heavily used in realtime, big data and social media applications and generally called NoSQL because they do things a little differently than traditional SQL databases. We'll see what that means in a minute.

So you know – there are software "drivers" that allow MongoDB to be used with a multitude of programming languages and frameworks, including both Node and Ruby on Rails. We'll be talking about it in Express applications later today. If you want to research using it in Sinatra or Rails, take a look at the `mongoid` gem.

For this module, we're using straight up Mongo. So what's a Mongo database look like?

#### Data Format

- A MongoDB database consists of _documents_.
- A _document_ in MongoDB is composed of _field_ and _value_ pairs.

Lets take a look of what a MongoDB _document_ may look like:

```js
{
    _id: ObjectId("5099803df3f4948bd2f98391"),
    name: { first: "Alan", last: "Turing" },
    birth: new Date('Jun 23, 1912'),
    death: new Date('Jun 07, 1954'),
    contribs: [ "Turing machine", "Turing test", "Turingery" ],
    views: 1250000
}
```

__What does this data structure remind you of?__ JSON!

A MongoDB _document_ is very much like JSON, except it is stored in the database in a format known as _BSON_ (think - _Binary JSON_).

_BSON_ basically extends _JSON_ with additional data types, such as __ObjectID__ and __Date__ shown above.

#### The Document *_id*

The *_id* is a special field represents the document's _primary key_ and will always be listed as the first field. It must be unique.

We can explicitly set the *_id* like this:

```js
{
  _id: 2,
  name: "Suzy"
}
```
or this...

```js
{
  _id: "ABC",
  name: "Suzy"
}
```
However, it's more common to allow MongoDB to create it implicitly for us using its _ObjectID_ data type.


#### MongoDB vs. Relational SQL Databases, Terminology

![](http://4.bp.blogspot.com/-edz2_QrFvCE/UnzBhKZE3FI/AAAAAAAAAEs/bTEsqnZFTXw/s1600/SQL-MongoDB+Correspondence.PNG)

#### Key Differences of MongoDB

- Schema-less
The documents in a MongoDB collection can have completely different types and number of fields from each other.<br>__How does this compare to a SQL database like PostgreSQL?__

- No Table Joins
In a SQL DB, we break up related data into separate tables.

In MongoDB, we often _embed_ related data in a single document, you'll see an example of this later.

The supporters of MongoDB highlight the lack of table joins as a performance advantage since joins are expensive in terms of computer processing.

## Installing, Creating a DB, and Inserting Documents 

#### Installation

You may already have MongoDB installed on your system, lets check in terminal `? mongod` (note the lack of a "b" at the end").

If you receive an error, lets use _Homebrew_ to install MongoDB:

1. Update Homebrew's database (this might take a bit of time)<br>`brew update`
2. Then install MongoDB

 `brew install mongodb`

MongoDB by default will look for data in a folder named `/data/db`. We would have had to create this folder, but Homebrew did it for us (hopefully).

#### Start Your Engine

`mongod` is the name of the actual database engine process. The installation of MongoDB does not set mongoDB to start automatically. A common source of errors when starting to work with MongoDB is forgetting to start the database engine.

To start the database engine, type `mongod` in terminal.

Press `control-c` to stop the engine.

#### Creating a Database and Inserting Documents

MongoDB installs with a client app, a JavaScript-based shell, that allows us to interact with MongoDB directly.

Start the app in terminal by typing `mongo`.

The app will load and change the prompt will change to `>`.

List the shell's commands available: `> help`.

Show the list of databases: `> show dbs`.

Show the name of the currently active database: `> db`.

Switch to a different database: `> use [name of database to switch to]`.

Lets switch to the `local` database: `> use local`.

Show the collections of the current database `> show collections`.

#### Creating a new Database

To create a new database in the Mongo Shell, we simply have to _use_ the database.  Lets create a database named _myDB_:

```
> use myDB
```

#### Inserting Data into a Collection

This how we can create and insert a document into a collection named _people_:

```
> db.people.insert({
    name: "Fred", // Don't type the dots, they are from the
    age: 21     // shell, indicating multi-line mode
})
```

Using a collection for the first time creates it!

#### Adding Lots of New Documents

In a moment we'll practice querying our database, but let's get more data in there. Here are few more documents to put in your _people_ collection. We can simply provide this __array__ to the _insert_ method and it will create a document for each object in the array.

```js
[
  {
    "name": "Emma",
    "age": 20
  },
  {
    "name": "Ray",
    "age": 45
  },
  {
    "name": "Celeste",
    "age": 33
  },
  {
    "name": "Stacy",
    "age": 53
  },
  {
    "name": "Katie",
    "age": 12
  },
  {
    "name": "Adrian",
    "age": 47
  }
]
```

> Note: Be sure to type the closing paren of the _insert_ method!


## Querying Documents

To list all documents in a collection, we use the _find_ method on the collection without any arguments:

```
> db.people.find()
```

Again, unlike the rows in a relational database, our documents don't have to have the same fields!

### More Specific Queries

We can also use the `find()` method to query the collection by passing in an argument – a JS object containing criteria to query with.

```
> db.people.find( {name: "Miguel"} )
```

There are a handful of special criteria variables we can use, too. Here's how we can use MongoDB's `$gt` query operator to return all _people_ documents with an age greater than 20:

```
> db.people.find( {age: { $gt: 20 } } )
```

MongoDB comes with a slew of built-in [query operators](http://docs.mongodb.org/manual/reference/operator/query/#query-selectors) we can use to write complex queries.

__How would we write a query to retrieve people that are less than or equal to age 20?__

In addition to selecting which data is returned, we can modify how that data is returned by limiting the number of documents returned, sorting the documents, and by projecting which fields are returned.

This sorts our age query and sorts by _name_:

```
> db.people.find( {age: { $gt: 20 } } ).sort( {name: 1} )
```
The "1" indicates ascending order.

[This documentation](http://docs.mongodb.org/manual/core/read-operations-introduction/) provides more detail about reading data.

## Updating Data

In MongoDB, we use the `update()` method of collections by specifying the _update criteria_ (like we did with `find()`), and use the `$set` action to set the new value.

```
> db.people.update( { name: "Miguel" }, { $set: { age: 99 } })
```

By default `update()` will only modify a single document. However, with the `multi` option, we can update all of the documents that match the query.

```
> db.people.update( { name: { $lt: "M" } }, { $inc: { age: 10 } }, { multi: true } )
```
We used the `$inc` update operator to increase the existing value.

Here is the [list of Update Operators](http://docs.mongodb.org/manual/reference/operator/update/) available.

## Removing Data

We use the `remove()` method to data from collections.

If you want to completely remove a collection, including all of its indexes, use `[name of the collection].drop()`.

Call `remove({})` on the collection to remove all docs from a collection. Note: all documents will match the "empty" criteria.

Otherwise, specify a criteria to remove all documents that match it:

```
>db.people.remove( { age: { $lt: 16 } } )
```

## Data Modeling in MongoDB 

There are two ways to modeling related data in MongoDB:

- via __embedding__
- via __referencing__ (linking)

Both approaches can be used simultaneously in the same document.

### Embedded Documents

In MongoDB, by design, it is common to __embed__ data in a parent document.

Modeling data with the __embedded__ approach is different than what we've seen in a relational DB where we spread our data across multiple tables. However, this is the way MongoDB is designed to work and is the reason MongoDB can read and return large amounts of data far more quickly than a SQL DB that requires join operations.

To demonstrate __embedding__, we will add another person to our _people_ collection, but this time we want to include contact info. A person may have several ways to contact them, so we will be modeling a typical one-to-many relationship.

## Modeling Data

Let's walk through this command by entering it together:

```js
> db.people.insert({
    name: "Manny",
    age: 33,
    contacts: [
      {
        type: "email",
        contact: "manny@domain.com"
      },
      {
        type: "mobile",
        contact: "(555) 555-5555"
      }
    ]})
```

__What do you imagine could be a downside of embedding data?__

If the embedded data's growth is unbound, MongoDB's maximum document size of 16 megabytes could be exceeded.

The above approach of embedding _contact_ documents provides a great deal of flexibility in what types and how many contacts a person may have.  However, this flexibility slightly complicates querying.

However, what if our app only wanted to work with a person's multiple _emails_ and _phoneNumbers_?

__Knowing this, pair up and discuss how you might alter the above structure.__

#### Referencing Documents

We can model data relationships using a __references__ approach where data is stored in separate documents. These documents, due to the fact that they hold different types of data, are likely be stored in separate collections.

It may help to think of this approach as _linking_ documents together by including a reference to the related document's *_id* field.

Earlier, we created a _bankAccounts_ collection to demonstrate the __references__ approach.

>Note: Use the person has a bank account scenario to work through the following.

 That bank account might be a _joint account_, owned by more than one person.

 For the sake of _data consistency_, keeping the account data in its own document would be a better design decision. In more clear terms, it would not be a good idea to store a bank account's balance in more than one place.

 In our app, we have decided that all bank accounts will be retrieved through a person. This decision allows us to include a reference on the person document only.

Implementing the above scenario is as simple as assigning a _bankAccount_ document's *_id* to a new field in our person document:

```js
> db.people.insert({
    name: "Miguel",
    age: 46,
    bankAccount: 5678
})
```

Again, because there are no "joins" in MongoDB, retrieving a person's bank account information would require a separate query on the _bankAccounts_ collection.


## Data Modeling Best Practices

MongoDB was designed from the ground up with application development in mind. More specifically, what can and can't be done in regards to data is enforced in your application, not the database itself (like in a SQL database).

Here are a few things to keep in mind:

- For performance and simplicity reasons, lean toward _embedding_ over _referencing_.
- Prefer the _reference_ approach when the amount of child data is unbound.
- Prefer the _reference_ approach when multiple parent documents access the same child document and that child's document changes frequently.
- Obtaining _referenced_ documents requires multiple queries by your application.
- In the _references_ approach, depending upon your application's needs, you may choose to maintain links to the related document's *_id* in either document, or both.

For more details regarding data modeling in MongoDB, start with [this section of mongoDB's documentation ](http://docs.mongodb.org/manual/core/data-modeling-introduction/) or this [hour long YouTube video](https://www.youtube.com/watch?v=PIWVFUtBV1Q)

