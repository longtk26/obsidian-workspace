## What is knowledge graph?

A knowledge graph is a structured representation of real-world entities and the relationships between them, stored as a directed, labeled graph. The fundamental unit is the **triple**: a `(subject, predicate, object)` statement — e.g., `(Marie Curie, wonAward, Nobel Prize in Physics)`. Subjects and objects are **nodes** (entities or literal values); predicates are typed, directed **edges** encoding semantic meaning.

The concept is rooted in the **Semantic Web** initiative (W3C, late 1990s–2000s), which standardized **RDF** (Resource Description Framework) as a universal data model for machine-readable web content. Google popularized the commercial term "Knowledge Graph" in May 2012 when it announced its entity-centric search index covering ~500 million entities and 3.5 billion facts.

**Ontologies** define the schema layer on top of raw triples: they formally specify entity classes (`Person`, `Drug`, `Organization`), allowed relationship types, cardinality constraints, and inference rules using languages like **OWL** (Web Ontology Language) or **RDFS**. An ontology transforms a bag of triples into a semantically coherent, machine-reasoning-capable model.

## The usage of knowledge graph

- **Semantic search**: Query expansion and entity disambiguation replace keyword matching. A KG understands that "Apple" in a tech context means the company, not the fruit, and can surface related entities without them appearing verbatim in documents.
- **Recommendation systems**: Instead of collaborative filtering on a flat user-item matrix, a KG models `User → purchased → Product → belongsToCategory → Electronics → madeBy → Brand`, enabling path-based reasoning that explains *why* a recommendation was made and handles cold-start better.
- **NLP / NLU**: KGs supply entity resolution, coreference, and relation extraction pipelines with a ground-truth reference. Named Entity Linking (NEL) maps "the president" in a sentence to a specific `Person` node, grounding language model outputs.
- **Data integration**: Disparate databases (CRM, ERP, data warehouse) each map to a shared ontology. The KG becomes the integration layer — a query can traverse across formerly siloed systems without ETL into a single schema.
- **Question answering (KGQA)**: Systems like Google's answer box traverse the graph to return a precise entity answer (`"Who directed Inception?" → Christopher Nolan`) rather than a ranked list of documents.
- **RAG with KGs (GraphRAG)**: Augmenting LLM retrieval with a KG provides structured, factual, editable context at query time — dramatically reducing hallucination on entity-centric questions and supporting multi-hop reasoning (e.g., "who is the CEO of the company that acquired DeepMind?").
- **Enterprise knowledge management**: KGs index expertise (`Employee → hasSkill → GraphQL`), connect documentation to product releases, and support compliance queries by linking regulations to policies to data assets — use cases where flat search fails.

## Use cases

- **Google Knowledge Panel**: When you search "Albert Einstein," Google renders a sidebar with his birth date, nationality, spouse, and notable works — served directly from a KG of ~1,000 types and 35,000 attributes, not crawled HTML. This enables factoid answers, carousel disambiguation, and "People also search for" relationship traversal.
- **Amazon product recommendations**: Amazon's product graph links items to attributes, categories, co-purchases, and reviews. Graph traversal powers "Frequently bought together" and "Customers who viewed X also viewed Y," capturing multi-hop product relationships that matrix factorization cannot represent.
- **Drug discovery (biomedical KGs)**: Projects like Hetionet integrate millions of triples across drugs, genes, diseases, and pathways. Researchers run link-prediction queries to identify drug repurposing candidates — e.g., predicting that an existing compound might inhibit a newly characterized protein target.
- **Fraud detection (financial services)**: Banks like HSBC and PayPal model transactions, accounts, devices, IP addresses, and identities as a graph. Fraud rings are detected by finding densely connected subgraphs or short-path anomalies (e.g., `Account A → shares device → Account B → transfers to → Account C → cashes out`).
- **Supply chain / digital twins**: Companies like Siemens and Bosch build KGs over parts, suppliers, logistics nodes, and compliance certifications. A single query can answer: "Which finished goods are at risk if Supplier X in Region Y is disrupted?" — a traversal requiring multiple SQL joins across isolated ERP tables.
- **Customer 360**: Salesforce and similar CRMs use KGs to unify a customer's identity across touchpoints (purchases, support tickets, social interactions, contracts) into a single entity node with typed edges to all interactions — enabling agents and ML models to reason over the full customer history holistically.

## How a knowledge graph works

**RDF Triples and Storage**
Every fact is stored as a `(subject, predicate, object)` triple using URIs as globally unique identifiers (e.g., `<dbr:Marie_Curie> <dbo:award> <dbr:Nobel_Prize_in_Physics>`). A **triplestore** (Apache Jena TDB, Stardog, Virtuoso) is a purpose-built database for storing and indexing triples, optimized for SPARQL pattern matching.

**SPARQL Query Language**
SPARQL is the W3C-standard query language for RDF. It uses graph pattern matching — you declare a pattern of triples with variables and the engine finds all bindings that satisfy the pattern:
```sparql
SELECT ?drug WHERE {
  ?drug rdf:type :Compound .
  ?drug :targetsGene :BRCA1 .
  ?drug :approvedFor :BreastCancer .
}
```

**Property Graph Databases (Neo4j, Amazon Neptune)**
Neo4j uses the **Labeled Property Graph** model where nodes and edges both carry key-value properties, queried with Cypher. Amazon Neptune supports both RDF/SPARQL and LPG/Gremlin. Unlike RDF, edges in property graphs can carry properties directly (e.g., an `INVESTED_IN` edge has an `amount` property).

**Ontologies and Reasoning**
OWL defines axioms like `owl:TransitiveProperty` (`locatedIn` is transitive), enabling automatic inference. A reasoner (HermiT, Pellet, ELK) applies axioms to materialize implicit triples not explicitly stored — e.g., if `Paris locatedIn France` and `France locatedIn Europe`, the reasoner infers `Paris locatedIn Europe`.

**Knowledge Graph Embeddings (KGE)**
Methods like **TransE**, **RotatE**, and **ComplEx** project entities and relations into a continuous vector space, optimizing so that `head_vector + relation_vector ≈ tail_vector`. This enables **link prediction** (predict missing edges — widely used in drug discovery), entity classification, and similarity scoring. These embeddings also enrich RAG retrieval with fuzzy, learned similarity on top of exact graph traversal.

## Compare with grep finding

| Dimension                   | Grep / Full-Text Search (BM25, Lucene)                                                                                                                                                                                               | Knowledge Graph                                                                                                                           |
| --------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------- |
| **Matching paradigm**       | Lexical: scores documents by term frequency. Requires the query token to appear in the document.                                                                                                                                     | Semantic: matches entities and relationships by identity and type. "Barack Obama" and "the 44th U.S. president" resolve to the same node. |
| **Context & relationships** | None. Each document is scored independently.                                                                                                                                                                                         | Explicit. Typed edges let you query "all colleagues of person X who also worked on project Y" in a single graph pattern.                  |
| **Query traversal**         | Linear scan with inverted index — no concept of hops or paths.                                                                                                                                                                       | Graph traversal (BFS/DFS) across an arbitrarily deep neighborhood. Multi-hop queries are natural.                                         |
| **Precision**               | Lower for ambiguous terms — "Python" matches both the snake and the language equally.                                                                                                                                                | Higher for entity-centric queries once entities are disambiguated — the node `Python (programming language)` has no ambiguity.            |
| **Recall**                  | High on exact/near-exact matches; misses synonyms unless query expansion is layered on.                                                                                                                                              | Can miss entities if the graph is incomplete — KG coverage gaps directly hurt recall.                                                     |
| **When to prefer**          | Large unstructured text corpora, log search, document retrieval where exact phrasing matters. Zero-setup, scales to petabytes.                                                                                                       | When queries are relational ("find all X connected to Y through Z"), entity identity matters, or inference over facts is needed.          |
| **Hybrid best practice**    | Production systems (GraphRAG, enterprise search) combine both: BM25 or vector search retrieves candidates, KG traversal re-ranks or augments results with structured context, improving faithfulness and reducing LLM hallucination. |                                                                                                                                           |
