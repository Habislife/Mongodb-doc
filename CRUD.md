# MongoDB CRUD Operations

## Data types

### Data Types in MongoDB (BSON Format)

MongoDB stores data in a format called BSON (Binary JSON). BSON extends JSON by adding additional data types like Date and Binary. Below are the key data types:

### 1. String

- **Description:** A string is the most common data type used to store text.

- **How it's stored:** UTF-8 encoded strings.

- **Example:**

```javascript
{ "name": "John Doe" }
```

- **Use case:** Storing names, descriptions, addresses, etc.

### 2. Number

MongoDB supports several numeric types:

- Integer (`int32` or `int64`)

  - 32-bit or 64-bit integers depending on value size.
  - **Example:**
  
  ```javascript
  { "age": 30 }
  ```

- Double
  - 64-bit floating point numbers.
  - **Example:**

  ```javascript
  { "rating": 4.5 }
  ```

- Long (`int64`)

  - Specifically for very large integers that exceed 32 bits.

  - **Example:**

  ```javascript
  { "population": NumberLong("9876543210") }
  ```

- Decimal (`Decimal128`)

  - High-precision decimal for financial or scientific calculations.
  - **Example:**

  ```javascript
  { "price": NumberDecimal("99.99") }
  ```

Note::writing_hand: BSON differentiates between `int`, `long`, `double`, and `decimal`, but in JSON all are just "number".

### 3. Boolean

- **Description**: Represents a binary value: true or false.

- **Example:**

```javascript
{ "isActive": true }
```

- **Use case:** Flags for active status, feature toggles, etc.

### 4. Arrays

- **Description:** An array holds multiple values — it can include strings, numbers, objects, or even other arrays.

- **Example:**

```javascript
{ "tags": ["mongodb", "database", "NoSQL"] }
```

- **Use case:** Lists of items like categories, hobbies, product features, etc.

### 5. Object (Embedded Document)

- **Description:** A document can contain nested documents (objects inside objects).

- **Example:**

```javascript
{
  "address": {
    "street": "123 Main St",
    "city": "New York",
    "zip": "10001"
  }
}
```

- **Use case:** Representing structured data within a document (addresses, profiles, settings).

### 6. ObjectId

- **Description:** A special 12-byte identifier typically used as the default primary key (_id) in MongoDB documents.

- **Structure:** Contains timestamp, machine ID, process ID, and counter.

- **Example:**

```javascript
{ "_id": ObjectId("5f1e9b9d5d1f1c1a2c3e8d1e") }
```

- **Use case:** Unique identification of documents across collections.

Tip::safety_pin: ObjectId embeds creation time — you can even extract it!

### 7. Date

- **Description:** Stores date and time as a 64-bit integer representing milliseconds since the Unix epoch (January 1, 1970).

- **Example:**

```javascript
{ "createdAt": ISODate("2025-04-28T10:20:30Z") }
```

- **Use case:** Timestamps for creation, update, expiry, scheduling, etc.

### :fire: Quick Recap

| Data Type | Purpose | Example |
|:----------|:--------|:--------|
| **String** | Store text data | `"John"` |
| **Number** | Store numeric data (`int`, `float`) | `30`, `4.5` |
| **Boolean** | Store true/false values | `true` |
| **Array** | Store multiple values | `["a", "b", "c"]` |
| **Object** | Store nested documents | `{ address: { city: "NY" } }` |
| **ObjectId** | Unique ID for documents | `ObjectId(...)` |
| **Date** | Store dates and times | `ISODate(...)` |

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

### Querying Documents Using Conditions

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

### Projection (Selecting Specific Fields)

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

### Update Operators

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

### Understanding Deletion Behavior

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
