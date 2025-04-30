# Advanced MongoDB Features

## Transactions in MongoDB

### What are Transactions in MongoDB?

In databases, a transaction is a sequence of operations that are treated as a single unit of work. Either all operations succeed, or none of them are applied. This ensures data consistency, especially in critical systems like banking or inventory management.

MongoDB originally supported atomic operations at the document level, meaning each document's operations were isolated and consistent. However, starting from ***MongoDB 4.0***, MongoDB supports ***multi-document ACID transactions***, allowing you to perform changes across multiple documents (and even collections) reliably.

***ACID*** stands for:

- Atomicity: All or nothing
- Consistency: Keeps data in a valid state
- Isolation: Transactions don't interfere with each other
- Durability: Changes persist even if the system crashes

Let’s say you want to transfer $100 from User A to User B:

```javascript
const session = await client.startSession();

session.startTransaction();
try {
  await accounts.updateOne(
    { name: "User A" },
    { $inc: { balance: -100 } },
    { session }
  );

  await accounts.updateOne(
    { name: "User B" },
    { $inc: { balance: 100 } },
    { session }
  );

  await session.commitTransaction();
  console.log("Transaction successful");
} catch (error) {
  await session.abortTransaction();
  console.error("Transaction aborted", error);
}
session.endSession();
```

## When to Use Transactions and Their Limitations

You should use transactions when multiple changes must be made together to ensure consistency, such as:

- Money transfer between accounts
- Creating linked documents (like orders and inventory updates)
- Multi-step data processing
***Limitations:***

- Transactions can be slower than single-document operations.
- They require replica sets or sharded clusters to work properly.
- Should not be overused — MongoDB is optimized for atomic document operations.

***:pushpin:Tip:*** Use **transactions only when absolutely necessary** for data consistency.

## MongoDB Atlas

### Introduction to MongoDB Atlas (Cloud Service)

**MongoDB Atlas** is MongoDB’s fully-managed cloud database platform, which handles deployment, scaling, backups, and security — all without manual setup. It allows you to run MongoDB on cloud providers like **AWS**, **Azure**, or **Google Cloud**.

With Atlas, developers can:

- Deploy production-ready clusters in minutes
- Enable security best practices (encryption, access control)
- Monitor databases in real time

***Practical:***
Visit: <https://www.mongodb.com/cloud/atlas>

Create an account and choose:

1. Cloud Provider (AWS/GCP/Azure)
2. Cluster Tier (free/shared or dedicated)
3. Cluster Name and Region
Then click "**Deploy**". That’s it!

### Deploying MongoDB on Atlas

**Step-by-Step:**

1. **Sign Up/Login** to MongoDB Atlas.
2. Click "**Build a Cluster**".
3. Choose a **free tier** for testing.
4. Set **Region** close to your users.
5. Configure **Database Access** (create user + password).
6. Add your **IP Address to Access List**.
7. Connect using a **connection string**.

Example MongoDB URI:

```javascript
mongodb+srv://bishal:<password>@cluster0.mongodb.net/mydb?retryWrites=true&w=majority
```

Use this string in your app to connect Atlas with **Spring Boot**, **Node.js**, or **Python Flask**.

### Using Atlas Features like Data Lake, Search, and Charts

**1. Atlas Data Lake:**

- Allows you to query data stored in AWS S3 using MongoDB Query Language (MQL).
- Useful for analyzing raw or archived data without loading it into MongoDB.

**2. Atlas Search:**

- A full-text search engine built on Apache Lucene.
- Enables complex searches (autocomplete, fuzzy search) with high performance.

**3. Atlas Charts:**

- A built-in data visualization tool.
- You can create dashboards, graphs, and charts without exporting data.

Example:
Create a dashboard showing:

- Top-selling products
- User registrations over time
- Sales by region

## Full-Text Search with MongoDB

### Setting Up Full-Text Search with Text Indexes

MongoDB allows you to search text data in fields like names, descriptions, or articles using text indexes.

**Text Index:** A special index that lets you search for words inside string fields.

```javascript
db.products.createIndex({ description: "text" });
```

This allows you to perform searches like:

```javascript
db.products.find({ $text: { $search: "organic coffee" } });
```

### Querying for Text-Based Content

You can:

- Use **exact keywords**
- Combine multiple words
- Exclude terms

Examples:

```javascript
// Find documents with "organic" and "coffee"
db.products.find({ $text: { $search: "organic coffee" } });

// Find documents with "organic" but not "instant"
db.products.find({ $text: { $search: "organic -instant" } });
```

You can also sort by relevance using the textScore:

```javascript
db.products.find(
  { $text: { $search: "organic coffee" } },
  { score: { $meta: "textScore" } }
).sort({ score: { $meta: "textScore" } });
```

### Advanced Full-Text Search Features

For more sophisticated needs, you can use MongoDB Atlas Search which supports:

- **Autocomplete**
- **Fuzzy matching**
- **Synonyms**
- **Custom analyzers**

Example (Atlas Search):

```javascript
{
  "index": "default",
  "text": {
    "query": "coffe",
    "path": "description",
    "fuzzy": {
      "maxEdits": 1
    }
  }
}
```

This query will find "coffee" even if the user typed "coffe".

## :white_check_mark:Summary

| Feature | Description | Use Case |
|--------|-------------|----------|
| **Transactions** | Ensure multiple changes succeed/fail together | Financial apps, order systems |
| **Atlas** | Cloud-hosted MongoDB with advanced features | Scalable production apps |
| **Full-Text Search** | Text-based search capabilities | E-commerce, blog, search tools |
