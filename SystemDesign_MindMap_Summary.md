# System Design - Comprehensive Mind Map Summary

## 1. SOFTWARE DESIGN PRINCIPLES

### 1.1 SCALABILITY

#### Single-Server Design
- **Components**: HTTP server + Database on same machine
- **Issue**: Single point of failure
- **Use**: Only for very small applications

#### Separating Database
- **Approach**: Move database to separate server
- **Benefit**: Reduces single point of failure slightly
- **Limitation**: Still limited scalability

#### Vertical Scaling (Scale Up)
- **Definition**: Adding more resources to a single server (CPU, RAM, storage)
- **Limitations**:
  - Servers have physical limits
  - Still creates single points of failure
  - Eventually reaches cost/performance ceiling

#### Horizontal Scaling (Scale Out)
- **Definition**: Adding more servers to distribute load
- **Key Requirement**: Stateless web servers
- **Benefits**:
  - Can scale indefinitely
  - Better fault tolerance
  - More cost-effective at scale
- **Consideration**: Choose simplest architecture that meets projected traffic requirements

#### Server Sources
- **On-Premises**: Company-owned data centers
- **Cloud Services**: 
  - Amazon EC2
  - Google Compute Engine
  - Azure VMs
- **Serverless**: Lambda, Kinesis, Athena

---

## 2. SCALING THE DATABASE

### 2.1 Failover Strategies

#### Cold Standby
- **Method**: Periodic backups to standby server
- **Recovery Time**: Slowest
- **Data Loss**: Possible (up to last backup)
- **Cost**: Lowest

#### Warm Standby
- **Method**: Continuous replication to standby
- **Recovery Time**: Moderate
- **Data Loss**: Minimal
- **Cost**: Moderate

#### Hot Standby
- **Method**: Real-time active standby server
- **Recovery Time**: Fast (near instant)
- **Data Loss**: None
- **Cost**: Higher

#### Multi-Primary
- **Method**: Multiple active primary servers
- **Recovery Time**: Immediate (no failover needed)
- **Benefit**: Load distribution + high availability
- **Complexity**: Highest

### 2.2 Horizontal Database Scaling

#### Sharding
- **Definition**: Splitting database across multiple servers
- **Components**:
  - Client & request router
  - Multiple shards (Shard 1, 2, 3...N)
- **Challenges**:
  - Difficult to do joins across shards
  - Resharding complexity
  - Hotspot problems (uneven distribution)

#### MongoDB Example
- **Structure**: Replica Sets (RS) with Primary + Secondaries
- **Components**:
  - mongos process (query router)
  - Config servers
  - Multiple replica sets per shard range
- **Example Sharding**: 
  - RS1: users min → 1000
  - RS2: users 1000 → 5000  
  - RS3: users 5000 → max

#### Cassandra Example
- **Architecture**: No single master
- **Consistency**: Eventually consistent
- **Benefit**: High availability, partition tolerance

### 2.3 NoSQL Databases

#### Characteristics
- **Challenges**:
  - Tough to do joins across shards
  - Resharding difficulties
  - Hotspot issues
- **Best For**:
  - Simple key/value lookups
  - Applications where formal schema not needed
- **SQL Support**: Most NoSQL databases actually support SQL operations
- **Examples**: MongoDB, DynamoDB, Cassandra, HBase

### 2.4 Denormalization

#### Normalized Data
- **Pros**: Less storage space, updates in one place
- **Cons**: More lookups required

#### Denormalized Data
- **Pros**: Single lookup, faster reads
- **Cons**: More storage, updates are hard, data redundancy

### 2.5 Data Lakes

#### Cloud Solutions Approach
- **Storage**: Amazon S3 (or equivalent)
- **Format**: CSV, JSON files in distributed storage
- **Schema Creation**: Amazon Glue
- **Querying**:
  - Amazon Athena (serverless)
  - Amazon Redshift (distributed data warehouse)
- **Performance**: Requires careful data partitioning

### 2.6 ACID Compliance

#### Properties
- **Atomicity**: Transaction succeeds completely or fails completely
- **Consistency**: All database rules enforced, or transaction rolled back
- **Isolation**: Transactions don't affect each other
- **Durability**: Committed transactions persist even after crashes

### 2.7 CAP Theorem

#### Trade-offs (Pick 2 of 3)
- **Consistency**: All nodes see same data
- **Availability**: System remains operational
- **Partition Tolerance**: System works despite network failures

#### Database Positioning
- **CP (Consistency + Partition Tolerance)**: 
  - HBase
  - MongoDB (in strongly consistent mode)
  - Amazon DynamoDB
- **AP (Availability + Partition Tolerance)**: 
  - Cassandra
- **CA (Consistency + Availability)**: 
  - MySQL (when not distributed)

---

## 3. CACHING

### 3.1 Caching Layers

#### Where Caches Can Exist
- **Client-side caching**: Browser cache
- **CDN caching**: Geographic distribution
- **Application server cache**: In-memory cache
- **Database cache**: Query result caching

### 3.2 How Caches Work

#### Architecture
- **Horizontally scaled cache servers**
- **Client hashing**: Requests distributed to specific servers
- **In-memory storage**: Fast access

#### Best Use Cases
- Applications with more reads than writes

#### Key Considerations
- **Expiration Policy**:
  - Too long: Data goes stale
  - Too short: Cache ineffective
- **Hotspot Problem**: Celebrity problem (uneven distribution)
- **Cold Start**: How to warm up cache initially

### 3.3 Eviction Policies

- **LRU (Least Recently Used)**: Evict items not accessed recently
- **LFU (Least Frequently Used)**: Evict items accessed least often
- **FIFO (First In First Out)**: Evict oldest items first

#### LRU Implementation
- Uses Doubly Linked List + Hash Table
- O(1) operations for get and put

### 3.4 Caching Technologies

#### Redis
- **Type**: In-memory key/value store
- **Features**:
  - Snapshots, replication, transactions, pub/sub
  - Advanced data structures
  - Open source
- **Complexity**: More complex than Memcached

#### Memcached
- **Type**: Simple distributed caching
- **Benefit**: Easy to use
- **Languages**: .NET, Java, Node.js

#### Hazelcast
- **Type**: Distributed Map
- **Languages**: Java

#### Amazon ElastiCache
- **Type**: Fully-managed Redis or Memcached
- **Benefit**: AWS-managed solution

### 3.5 Content Delivery Networks (CDNs)

#### Function
- **Geographic Distribution**: Edge locations worldwide
- **Content Types**:
  - JavaScript files
  - Images
  - Static web pages
  - Limited computation

#### Providers
- **Cloudflare**
- **Google Cloud CDN**
- **Microsoft Azure CDN**

---

## 4. RESILIENCY

### 4.1 Failure Scenarios

#### Levels of Failure
- Single server
- Entire rack
- Entire data center (availability zone)
- Entire region
- Beyond regional (catastrophic)

### 4.2 Geo-Routing

- **Geographic Distribution**: NA, EU, India, etc.
- **Load Balancing**: Route traffic to nearest/healthiest region
- **Failover**: Automatic routing to healthy regions

### 4.3 Distribution Best Practices

#### Server Placement
- **Spread secondaries across**:
  - Multiple racks
  - Multiple availability zones
  - Multiple regions

#### Capacity Planning
- **Overprovisioning**: Ensure capacity to survive failures
- **Budget vs. Availability**: Balance cost and reliability
- **Alternative**: Offsite backups with slower provisioning

---

## 5. DISTRIBUTED STORAGE

### 5.1 Cloud Storage Solutions

#### Features
- **Scalable object storage**
- **High availability**
- **Security**
- **Fast access**

#### Use Cases
- Data lakes
- Websites
- Backups
- Big data analytics

### 5.2 Durability and SLAs

#### Amazon S3 Example
- **Durability**: 99.999999999% (11 nines)
- **Meaning**: 0.000000001% chance of data loss

#### Percentiles
- **99% availability**: 3.65 days downtime/year
- **99.9999% (6 nines)**: ~30 seconds downtime/year

#### Latency Percentiles
- **Example**: "3 nines latency" = 100ms
- **Meaning**: 99.9% of requests complete within 100ms

### 5.3 Storage Tiers

#### Hot Storage
- **Access**: Frequent
- **Cost**: Higher
- **Speed**: Fastest

#### Cool Storage
- **Access**: Infrequent
- **Cost**: Moderate
- **Speed**: Moderate

#### Cold Storage (Glacier)
- **Access**: Archival
- **Cost**: Lowest
- **Speed**: Slowest (hours to retrieve)

### 5.4 Storage Providers

- **Amazon S3** (+ Glacier)
- **Google Cloud Storage**
- **Microsoft Azure**
- **Hadoop HDFS** (self-hosted)
- **Consumer**: Dropbox, Box, Google Drive (not for system design)

### 5.5 HDFS Architecture

#### Components
- **Name Node**: Coordinates operations, stores metadata
- **Data Nodes**: Store actual data blocks
- **Rack Awareness**: Replication across racks

#### Features
- Files broken into blocks
- Blocks replicated across cluster
- Clients read from nearest replica
- Writes replicated across different racks
- High availability with 3+ name nodes

---

## 6. ALGORITHMS AND DATA STRUCTURES

### 6.1 Singly Linked List

- **Access**: O(n)
- **Insert at head**: O(1)
- **Insert at end**: O(n)
- **Best for**: Sequential access, stacks (LIFO), queues (FIFO)
- **Memory**: Low (one pointer per node)

### 6.2 Doubly Linked List

- **Structure**: Next + Previous pointers
- **Insert front/back**: O(1)
- **Access**: O(n)
- **Use cases**: Deques, MRU (Most Recently Used)

### 6.3 Binary Tree / Binary Search Tree

- **Structure**: Each node has left and right child
- **Access**: O(log n) average, O(n) worst case
- **Insert/Delete**: O(log n)
- **Best for**: In-order traversals, sorted data

### 6.4 Hash Table

- **Components**: Hash function, Buckets, Collision handling
- **Operations**: O(1) average, O(n) worst case
- **Use**: Fast lookups needed
- **Challenge**: Hash collisions

### 6.5 Graph

- **Components**: Nodes (vertices) and Edges
- **Traversal**: BFS (Breadth-First Search), DFS (Depth-First Search)
- **Access**: O(V+E) where V=vertices, E=edges
- **Use cases**: Social networks, routing, networks

### 6.6 Search Algorithms

#### Linear Search
- **Method**: Iterate from beginning to end
- **Complexity**: O(n)
- **Requirement**: No special structure needed

#### Binary Search
- **Method**: Divide array in half repeatedly
- **Complexity**: O(log n)
- **Requirement**: Sorted array

### 6.7 Sorting Algorithms

- **Insertion Sort**: O(n) best, O(n²) worst - good for small/mostly-sorted
- **Merge Sort**: O(n log n) - scales well to large lists
- **Quick Sort**: O(n log n) average, O(n²) worst - very fast usually
- **Bubble Sort**: O(n²) - simple but inefficient

---

## 7. SEARCH AND INFORMATION RETRIEVAL

### 7.1 General Recipe

#### Forward Index
- **Structure**: Document ID → Keywords
- **Example**: Document 123 => "the quick red fox"
- **Considerations**: Capitalization, punctuation, offensive terms, phrases
- **Signals**: Position, formatting, relevance indicators

#### Inverted Index
- **Structure**: Keywords → Document IDs + Positions
- **Example**: 
  - "Palm tree" → (432,1), (36,1235), (432,55)
  - "Dinosaur" → (22,2), (22,253), (724,4342)

### 7.2 TF-IDF (Term Frequency-Inverse Document Frequency)

#### Components
- **Term Frequency (TF)**: How often word appears in a document
- **Document Frequency (DF)**: How often word appears across all documents
- **Inverse Document Frequency (IDF)**: 1/DF

#### Formula
```
Relevance Score = Term Frequency / Document Frequency
```
or
```
TF-IDF = TF × IDF
```

#### Purpose
- Identifies important and unique words for each document
- Common words (a, the, and) get low scores
- Unique, frequent words get high scores

#### Simple Search Algorithm
1. Compute TF-IDF for every word in corpus
2. For search word, sort documents by TF-IDF score
3. Display results

### 7.3 PageRank

#### Concept
- **Inspiration**: Academic paper citations
- **Key Ideas**:
  - Analyze backlinks to a page
  - Use anchor text as additional keywords
  - Weight by number of links on referring page
  - Apply dampening factor

#### Formula
```
PR(A) = (1-d) + d × (PR(T₁)/C(T₁) + ... + PR(Tₙ)/C(Tₙ))
```
Where:
- PR = PageRank
- d = dampening factor
- T = referring pages
- C = count of outbound links

#### Modern Search
- Google has moved beyond PageRank and TF-IDF
- Deep learning plays major role in ranking

---

## 8. MESSAGE QUEUES

### 8.1 Purpose

- **Decoupling**: Separates producers from consumers
- **Buffering**: Handles consumer backups gracefully
- **Asynchronous Processing**: Producers don't wait for consumers

### 8.2 Architecture

- **Publishers/Producers**: Send messages
- **Queue**: Stores messages temporarily
- **Subscribers/Consumers**: Process messages

### 8.3 Types

- **Single-consumer**: One consumer per message
- **Pub/Sub**: Multiple consumers can receive same message

### 8.4 Difference from Streaming

- **Message Queues**: Task-based, async processing
- **Streaming Data**: Real-time, massive continuous data

### 8.5 Example Technology

- **Amazon SQS**: Simple Queue Service

---

## 9. APACHE SPARK

### 9.1 Overview

- **Type**: Distributed processing framework for big data
- **Languages**: Scala, Python, Java, R
- **Storage**: In-memory caching
- **Not for**: OLTP (Online Transaction Processing)

### 9.2 Advantages over MapReduce

- Up to 100x faster than MapReduce
- Code reuse across different workloads
- Optimized query execution

### 9.3 Components

#### Spark SQL
- Structured data processing
- JDBC, ODBC support
- Formats: JSON, HDFS, ORC, Parquet

#### MLLib
- Machine learning library
- Classification, regression, clustering
- Collaborative filtering
- Pattern mining

#### GraphX
- Graph processing
- ETL, analysis, iterative graph computation
- Note: No longer widely used

#### Spark Streaming
- Real-time streaming analytics
- Sources: Twitter, Kafka, Flume, HDFS, ZeroMQ
- Structured streaming

### 9.4 Architecture

#### Components
- **Driver Program**: Contains SparkContext
- **Cluster Manager**: Coordinates resources (YARN, Mesos, Standalone)
- **Executors**: Run computations and store data
- **Tasks**: Individual units of work

#### How It Works
1. SparkContext coordinates independent processes
2. Works through Cluster Manager
3. Sends application code and tasks to executors
4. Executors run computations and cache data

---

## 10. CLOUD COMPUTING

### 10.1 Major Providers Comparison

| Service | AWS | Google Cloud | Azure |
|---------|-----|--------------|-------|
| **Storage** | S3 | Cloud Storage | Disk/Blob/Data Lake |
| **Compute** | EC2 | Compute Engine | Virtual Machines |
| **NoSQL** | DynamoDB | Bigtable | CosmosDB/Table Storage |
| **Containers** | Kubernetes/ECR/ECS | Kubernetes | Kubernetes |
| **Streams** | Kinesis | DataFlow | Stream Analytics |
| **Big Data** | EMR | Dataproc | Databricks |
| **Data Warehouse** | Redshift | BigQuery | Azure SQL/Database |
| **Caching** | ElastiCache (Redis) | Memorystore | Redis |

### 10.2 Data Warehouse Example (AWS)

#### Architecture
1. **Source**: Server logs
2. **Stream**: Kinesis Data Firehose
3. **Storage**: Amazon S3
4. **Schema**: AWS Glue (ETL)
5. **Query Options**:
   - Amazon Athena (serverless)
   - Amazon Redshift (managed)

### 10.3 Hybrid Cloud

#### Definition
- Combines on-premises (private cloud) with public cloud

#### Benefits
- Easy scaling of on-premises systems
- Meets regulations requiring on-premises data
- Flexibility and cost optimization

#### Requirements
- Bridges between data center and cloud
- Implementation varies by provider

#### Multi-Cloud
- Using more than one public cloud provider
- Reduces vendor lock-in
- Increases complexity

---

## 11. GENERATIVE AI SYSTEMS

### 11.1 AI as Black Box

#### Generative AI Definition
- Large Language Models (LLMs)
- Text-to-image generation
- Foundation models

#### API Approach
- **Input**: System prompt + User prompt
- **Processing**: Large Language Model (GPT, Claude, Llama, Gemini)
- **Output**: Generated response

#### Example
```python
from openai import OpenAI
client = OpenAI()
client.chat.completions.create(
    model="gpt-4o",
    messages=[
        {"role": "system", "content": "Always talk like a pirate."},
        {"role": "user", "content": "What is the meaning of life?"},
    ]
)
```

### 11.2 Other API Endpoints

- **Image Generation**: Create images from text
- **Moderation**: Content filtering
- **Speech to Text**: Audio transcription
- **Fine Tuning**: Extend training with custom data
- **Embedding**: Create semantic vectors

### 11.3 Customizing with Context

#### Context Windows
- Modern LLMs have large context windows
- Include relevant data with prompt
- Maintain chat history by appending

#### Considerations
- **Costs**: Charged per token
- **Big context** = Higher costs

#### Use Cases
- Guide answers
- Include proprietary data
- Provide examples

### 11.4 Retrieval-Augmented Generation (RAG)

#### Concept
- "Open-book exam" for LLMs
- Query external database for relevant information
- Include search results in prompt as context

#### Benefits
- Incorporates data not in LLM training
- Reduces hallucinations
- Easy to add new data

#### Process
1. User asks question
2. System queries (vector) database
3. Retrieved information added to prompt
4. LLM generates response with context

### 11.5 Embeddings

#### Definition
- Vector representation of data
- Point in multi-dimensional space (100s-1000s of dimensions)
- Similar items are close in this space

#### Purpose
- Encodes "meaning" or semantics
- Enables similarity search
- Foundation for RAG systems

#### Generation
- Use embedding APIs (OpenAI, etc.)
- Compute en masse for entire dataset

### 11.6 Vector Databases

#### Purpose
- Store data alongside embedding vectors
- Enable semantic search (K-Nearest Neighbor)

#### Retrieval Process
1. Compute embedding for search query
2. Query vector database
3. Return top-N most similar items
4. "Vector search"

#### Technologies

**Adapted Existing Databases:**
- Elasticsearch
- SQL databases
- Neptune
- Redis
- MongoDB
- Cassandra

**Purpose-Built Vector DBs:**
- **Commercial**: Pinecone, Weaviate
- **Open Source**: Chroma, Marqo, Vespa, Qdrant, LanceDB, Milvus, vectordb

### 11.7 RAG Example Workflow

```
1. User Query: "Data, tell me about your daughter Lal"
2. Compute embedding vector for query
3. Search vector database of Star Trek script lines
4. Retrieve similar lines mentioning Lal
5. Construct prompt:
   "You are Commander Data from Star Trek.
   How might Data respond to 'tell me about your daughter Lal'
   taking into account the following related lines: [retrieved context]"
6. LLM generates response
```

### 11.8 LLM Agents

#### Components (Nvidia Framework)
- **Memory**: Chat history + external data stores
- **Planning Module**: Break questions into sub-questions
- **Tools**: Functions LLM can use
- **Agent Core**: Coordinates everything

#### Tools
- Just functions provided to API
- Prompts guide LLM on tool usage
- Can access external information, services, APIs

#### Model Context Protocol (MCP)
- Standard for connecting tools to agents
- Enables consistent tool integration

#### Process
1. User makes request
2. Planning module breaks down task
3. Agent selects appropriate tools
4. Tools execute and return results
5. Agent synthesizes final response

---

## 12. INTERVIEW STRATEGIES

### 12.1 Clarifying Requirements

#### Initial Steps
1. Repeat the question
2. Confirm understanding with interviewer
3. **ASK LOTS OF QUESTIONS**
4. **THINK OUT LOUD**

#### Problem
- Given vague problem (e.g., "Design YouTube")
- Must turn into concrete requirements

### 12.2 Working Backwards

#### Amazon Approach
- Start from customer experience
- Highly valued at Amazon, but works generally

#### YouTube Example Questions
- How will users discover videos?
- Need search engine?
- Recommender engine?
- Advertising engine?

#### Framework
1. **Identify WHO**: The customers
2. **Determine WHAT**: Their use cases
3. **Decide WHICH**: Use cases to focus on
4. **Scope**: You can't design all of YouTube in 20 minutes

#### Benefits
- Defines clear requirements
- Limits scope appropriately
- Focuses on customer value
- Shows strategic thinking

### 12.3 Requirements Analysis

#### Functional Requirements
- What should the system do?
- What features are needed?
- What user actions are supported?

#### Non-Functional Requirements
- **Scale**: Users, requests per second, data size
- **Performance**: Latency requirements, throughput
- **Availability**: Uptime requirements (99.9%? 99.99%?)
- **Consistency**: Strong vs. eventual consistency
- **Security**: Authentication, authorization, encryption

### 12.4 Common Patterns

#### Always Consider
- Load balancing
- Caching strategy
- Database choice and scaling
- API design
- Monitoring and logging
- Disaster recovery

#### Red Flags to Avoid
- Jumping to solutions without understanding requirements
- Ignoring trade-offs
- Not asking about scale
- Not considering failure scenarios
- Over-engineering or under-engineering

---

## KEY PRINCIPLES TO REMEMBER

### Design Philosophy
1. **Simplest that works**: Choose simplest architecture meeting requirements, but no simpler
2. **Ask questions**: Always clarify requirements before proposing solutions
3. **Think about scale**: Understand consistency, availability, and scalability needs
4. **Plan for failure**: Every component can fail - plan accordingly
5. **Consider trade-offs**: CAP theorem, cost vs. availability, consistency vs. performance

### Common Considerations
- **Scalability**: Horizontal vs. vertical scaling
- **Reliability**: Redundancy, failover, disaster recovery
- **Performance**: Caching, CDNs, database optimization
- **Maintainability**: Monitoring, logging, debugging
- **Security**: Authentication, authorization, encryption
- **Cost**: Infrastructure, operational, development costs
