# Amazon Neptune

## What Is Neptune?

- **Amazon Neptune** — fully managed, serverless **graph database** service.
- Stores data as a **network of entities (nodes) and relationships (edges)**.
- Designed for datasets with tens of **billions of relationships** — queried in seconds using built-in graph algorithms.
- Supports vector similarity search for Generative AI RAG (Retrieval-Augmented Generation) applications.

## Graph Data Model

- **Node** — represents an entity (person, product, location, event).
- **Edge** — represents a relationship between two nodes (e.g., "is friends with," "purchased," "located in").
- **Properties** — key-value attributes on both nodes and edges.

Example: A social network graph where users (nodes) are connected by friendships (edges), and each user node has properties like name and age.

## Key Features

- **Serverless** — no infrastructure to manage; auto-scales compute and storage.
- **Highly Available** — 6 copies of data across 3 AZs.
- **Fast graph analytics** — built-in graph traversal algorithms (shortest path, centrality, community detection).
- **Multiple graph models** — supports Property Graph (Gremlin, openCypher) and RDF/SPARQL for knowledge graphs.
- **Vector search** — store vector embeddings alongside graph data for Generative AI use cases.

## Use Cases

| Use Case | Why Graph DB? |
|---|---|
| **Social networking** | Find friends-of-friends, mutual connections, influence graphs |
| **Fraud detection** | Identify suspicious transaction patterns, connected fraudsters |
| **Recommendation engines** | "People who bought X also bought Y" — relationship traversal |
| **Route optimization** | Shortest path algorithms over road/network graphs |
| **Knowledge graphs** | Wikipedia-style interconnected concept networks |
| **Drug discovery** | Biological interaction networks (genes, proteins, compounds) |

---

## Key Points / Exam Tips

- **Trigger:** "graph database," "nodes and edges," "relationships," "social network," "fraud detection," "recommendations" → **Amazon Neptune**
- **Trigger:** "shortest path between nodes," "highly connected data" → **Neptune**
- Neptune is **serverless** — no cluster management
- Neptune is purpose-built for **relationship-heavy queries** that would require many expensive JOINs in a relational DB
- Neptune also supports **vector embeddings** — relevant for Generative AI RAG patterns
- Do NOT use Neptune for: simple key-value, document, or time-series data — wrong data model
