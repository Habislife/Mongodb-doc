# Advanced Queries

## Query Operators

MongoDB provides a variety of query operators to allow complex queries and fine-tuned filtering. These can be grouped into different categories:

### Comparison Operators

- **$eq:** Matches documents where the value of the field is equal to the specified value.

 ***Example:***

```javascript 
db.users.find({ age: { $eq: 25 } })
This will find all users whose age is exactly 25.
```

- **$ne:** Matches documents where the field value is not equal to the specified value.

***Example:***

```javascript
db.users.find({ age: { $ne: 25 } })
This will find all users whose age is not 25.
```

- **$gt:** Matches documents where the field value is greater than the specified value.

***Example:***

```javascript
db.users.find({ age: { $gt: 30 } })
This will find all users whose age is greater than 30.
```

- **$lt:** Matches documents where the field value is less than the specified value.

***Example:***

```javascript
db.users.find({ age: { $lt: 30 } })
This will find all users whose age is less than 30.
```

- **$gte:** Matches documents where the field value is greater than or equal to the specified value.

***Example:***

```javascript
db.users.find({ age: { $gte: 18 } })
This will find all users who are 18 or older.
```

- **$lte:** Matches documents where the field value is less than or equal to the specified value.

***Example:***

```javascript
db.users.find({ age: { $lte: 60 } })
This will find all users who are 60 or younger.
```

### Logical Operators

- **$and:** Combines multiple conditions and matches documents that satisfy all the conditions.

***Example:***

```javascript
db.users.find({ $and: [ { age: { $gte: 18 } }, { age: { $lte: 35 } } ] })
This finds all users whose age is between 18 and 35, inclusive.
```

- **$or:** Matches documents that satisfy at least one of the conditions.

***Example:***

```javascript
db.users.find({ $or: [ { age: { $lt: 20 } }, { age: { $gt: 60 } } ] })
This finds all users who are either under 20 or over 60.
```

- **$not:** Inverts the effect of the query. It can be used to find documents that do not match a condition.

***Example:***

```javascript
db.users.find({ age: { $not: { $gt: 30 } } })
This will find all users whose age is not greater than 30.
```

- **$nor:** Matches documents that do not satisfy any of the conditions.

***Example:***

```javascript
db.users.find({ $nor: [ { age: { $gt: 30 } }, { age: { $lt: 18 } } ] })
This finds all users who are between the ages of 18 and 30, inclusive.
```

### Element Operators

- **$exists:** Matches documents where the field exists (or does not exist).

- ***Example:***

```javascript
db.users.find({ email: { $exists: true } })
This finds all users who have an email field.
```

- **$type:** Matches documents where the field's value is of the specified type.

***Example:***

```javascript
db.users.find({ age: { $type: "int" } })
This finds all users whose age field is an integer.
```

## Indexing in MongoDB

Indexes in MongoDB are critical for improving query performance. Without indexes, MongoDB would need to scan every document in the collection to find the ones that match the query, which can be very slow, especially as data grows.

### What is an Index and Why is it Important?

An index is a data structure that improves the speed of data retrieval operations on a collection at the cost of additional space and slightly slower write operations. Indexes are important because they allow MongoDB to quickly locate documents without scanning the entire collection.

#### Creating Indexes with `createIndex()`

You can create an index on a field or multiple fields to speed up queries. The syntax for creating an index is:

```javascript
db.collection.createIndex({ field: 1 })  // Ascending index
```

- ***Ascending index (`1`):*** This index sorts the field in ascending order.

- ***Descending index (`-1`):*** This index sorts the field in descending order.

**Example:**

```javascript
db.users.createIndex({ name: 1 })  // Creates an ascending index on the "name" field
```

### Types of Indexes

- **Single-field Index:** An index on one field.

***Example:***

```javascript
db.users.createIndex({ age: 1 })
```

- **Compound Index:** An index on multiple fields. Useful when queries filter by multiple fields.

***Example:***

```javascript
db.users.createIndex({ age: 1, name: -1 })
```

- **Text Index:** Allows text search on string fields.

***Example:***

```javascript
db.users.createIndex({ bio: "text" })
```

You can now perform text search queries:

```javascript
db.users.find({ $text: { $search: "developer" } })
```

- **Geospatial Index:** Used for location-based queries.

***Example:***

```javascript
db.places.createIndex({ location: "2dsphere" })
```

### Managing Indexes and Optimizing Queries

- **Listing Indexes:** To view the indexes on a collection:

```javascript
db.users.getIndexes()
```

- **Dropping Indexes:** You can drop an index with:

```javascript
db.users.dropIndex("indexName")
```

- **Optimizing Queries:** Indexes can significantly speed up queries by reducing the number of documents MongoDB needs to scan. You can also use the `.explain()` method to understand how MongoDB is executing a query and whether it's using indexes.

## Aggregation Framework

The aggregation framework provides powerful tools for transforming and processing data in MongoDB. It allows you to perform complex queries, group documents, and transform the result into a different format.

### Introduction to Aggregation

Aggregation is the process of processing data records and returning computed results. MongoDB's aggregation pipeline consists of a series of stages, each performing a specific operation.

### Using `$match`, `$group`, `$sort`, `$project`

- **$match:** Filters the documents to pass only those that match the specified condition.

***Example:***

```javascript
db.orders.aggregate([{ $match: { status: "shipped" } }])
```

This will filter the orders that have a status of "shipped".

- **$group:** Groups the documents by a specified expression and allows you to perform operations like summing, averaging, etc.

***Example:***

```javascript
db.orders.aggregate([
  { $group: { _id: "$customerId", totalAmount: { $sum: "$amount" } } }
])
```
This groups orders by customer and calculates the total amount spent by each customer.

- **$sort:** Sorts the documents by the specified fields.

***Example:***

```javascript
db.orders.aggregate([{ $sort: { orderDate: -1 } }])
```

This sorts the orders by the order date in descending order.

- **$project:** Reshapes each document by including, excluding, or adding new fields.

***Example:***

```javascript
db.orders.aggregate([{ $project: { _id: 0, customerId: 1, amount: 1 } }])
```
This will include only the `customerId` and `amount` fields and exclude the `_id` field.

### Aggregation Pipelines: Chaining Stages

The aggregation pipeline allows you to chain multiple stages together to process data in sequence.

Example:

```javascript
db.orders.aggregate([
  { $match: { status: "shipped" } },
  { $group: { _id: "$customerId", totalAmount: { $sum: "$amount" } } },
  { $sort: { totalAmount: -1 } }
])
```

This example filters shipped orders, groups by customer, and sorts the results by the total amount spent.

**`$lookup` for Joining Collections**
The `$lookup` stage is used to perform a join between two collections. It allows you to combine documents from one collection with those from another.

***Example:***

```javascript
db.orders.aggregate([
  {
    $lookup: {
      from: "customers",
      localField: "customerId",
      foreignField: "_id",
      as: "customerInfo"
    }
  }
])
````
This joins the `orders` collection with the `customers` collection based on the `customerId` field in `orders` and the `_id` field in `customers`. The result will have an array field `customerInfo` containing matching customer details.

