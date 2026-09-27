# Amazon DocumentDB

## What Is DocumentDB?

- **Amazon DocumentDB** — fast, reliable, fully managed **document database** with **MongoDB compatibility**.
- Used to store, query, and index **JSON-like documents**.
- Storage automatically grows in **10 GB increments** up to **64 TB**.
- Scales to millions of requests per second.

## Document Data Model

A document is a JSON-like structure with nested key-value pairs:
```json
{
  "ProductID": "12345",
  "Category": "Mobile Phones",
  "Specifications": {
    "RAM": "8 GB",
    "Storage": "128 GB"
  },
  "Connectivity": ["Wi-Fi", "Bluetooth 5.0", "NFC"]
}
```

This flexible schema allows storing rich, nested data without a rigid table structure.

## Key Features

- **MongoDB-compatible** — existing MongoDB applications can migrate with minimal changes.
- **Fully managed** — AWS handles provisioning, patching, backup, and replication.
- **HA and Replication** — 6 copies of data across 3 AZs (like Aurora's shared storage model).
- **Automatic failover** — if the primary fails, a replica is promoted automatically.
- **Read scaling** — up to 15 read replicas.

## Use Cases

- **User profiles** — nested, flexible user data (LinkedIn-style profiles)
- **Content management** — blog posts, video metadata, articles
- **E-commerce product catalog** — complex product specifications with varied attributes
- **Mobile and gaming apps** — player state, inventory, settings stored as documents

## DocumentDB vs DynamoDB

| Feature | DocumentDB | DynamoDB |
|---|---|---|
| Data model | JSON documents (MongoDB-compatible) | Key-value / document (DynamoDB JSON) |
| Query language | MongoDB query API | DynamoDB API (Scan, Query) |
| Compatibility | MongoDB | DynamoDB SDK only |
| Schema | Flexible (nested JSON) | Flexible (attributes per item) |
| Best for | Rich document queries, MongoDB migration | Serverless NoSQL at scale |

---

## Key Points / Exam Tips

- **Trigger:** "MongoDB-compatible," "JSON documents," "document database" → **Amazon DocumentDB**
- **Trigger:** "migrate MongoDB workload to AWS managed service" → **Amazon DocumentDB**
- DocumentDB shares Aurora's **storage architecture** — 6 copies across 3 AZs with auto-scaling storage
- DocumentDB is the AWS answer to "managed MongoDB"; DynamoDB is the AWS answer to "managed NoSQL key-value at scale"
- Both DocumentDB and DynamoDB can store JSON-like data — key difference is the query model and compatibility requirements
