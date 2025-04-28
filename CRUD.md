#  MongoDB CRUD Operations

## Creating Documents

MongoDB provides the following methods to insert documents into a collection:

- ```insertOne():``` Inserts a single document into a collection.

- ```insertMany():``` Inserts multiple documents into a collection.

**Syntax & Examples:**
***insertOne():***

```javascript
db.collection.insertOne({
  name: "John Doe",
  age: 30,
  email: "johndoe@example.com"
});
```
***insertMany():***
```javascript
db.collection.insertMany([
  { name: "Alice", age: 25, email: "alice@example.com" },
  { name: "Bob", age: 35, email: "bob@example.com" }
]);
```

## Reading Documents

`find()`: Retrieves multiple documents that match a query.

`findOne()`: Retrieves a single document that matches a query.

### Querying Documents Using Conditions:

**find():**

```javascript
db.collection.find({ age: { $gt: 25 } });
// Finds all documents where age is greater than 25
```

**findOne():**

```javascript
db.collection.findOne({ email: "johndoe@example.com" });
// Finds one document with the specified email
```

### Projection (Selecting Specific Fields):

You can specify which fields to include or exclude in the result using projection. By default, MongoDB returns all fields except the `_id` field.

Example (including only the `name` and `email` fields):

```javascript
db.collection.find({ age: { $gt: 25 } }, { name: 1, email: 1 });
// Returns only the name and email fields
```

## Updating Documents

- `updateOne()`: Updates a single document that matches the query.

- `updateMany()`: Updates all documents that match the query.

- `replaceOne()`: Replaces a single document with a new one.

### Update Operators:

- `$set`: Modifies an existing field or adds a new field.
- `$unset`: Removes a field.
- `$inc`: Increments the value of a field.
- `$push`: Adds an element to an array.

Examples:
**updateOne**() with `$set`:

```javascript
db.collection.updateOne(
  { name: "John Doe" },
  { $set: { age: 31 } }
);
// Updates the age of John Doe to 31
```

**updateMany**() with `$inc`:

```javascript
db.collection.updateMany(
  { age: { $gte: 30 } },
  { $inc: { age: 1 } }
);
// Increments age by 1 for all documents where age is 30 or greater
```

**replaceOne**():

```javascript
db.collection.replaceOne(
  { name: "Alice" },
  { name: "Alicia", age: 26, email: "alicia@example.com" }
);
// Replaces the document with name "Alice" with the new data
```

## Deleting Documents

- `deleteOne()`: Deletes a single document that matches the query.
- `deleteMany()`: Deletes all documents that match the query.

### Understanding Deletion Behavior:

- **deleteOne()** removes the first document that matches the condition.

- **deleteMany()** removes all documents that match the condition.

***Examples:***
**deleteOne():**

```javascript
db.collection.deleteOne({ name: "John Doe" });
// Deletes the first document where name is "John Doe"
```

**deleteMany():**

```javascript
db.collection.deleteMany({ age: { $lt: 30 } });
// Deletes all documents where age is less than 30
```