数据库学习资源整理
=======

## MySQL

### 经典学习路线推荐

- [MySQL官方手册](https://dev.mysql.com/doc/refman/5.6/en/)，学习MySQL使用的经典教程，推荐通篇学习。
- 经典著作《高性能MySQL》。这本书是 MySQL 领域的经典之作，拥有广泛的影响力。不但适合数据库管理员（DBA）阅读，也适合开发人员参考学习。不管是数据库新手还是专家，都能从本书中有所收获。本书对MySQL的设计实现有着全方位的介绍，不适宜新手直接阅读。
- 《MySQL 技术内幕：InnoDB 存储引擎》及官网的MySQL Internals Manual。在看完《高性能MySQL》后，作为理解引擎实现的深入学习书籍。
- 《数据库的索引设计与优化》，《数据密集型应用系统设计》。两本书高屋建瓴的指导了数据库设计的方法，可以作为引申读物。

### 补充资料

- How to Analyze and Tune MySQL Queries for Better Performance
- MySQL Performance Tuning 101
- MySQL Performance Schema & Sys Schema
- MySQL Performance: Demystified Tuning & Best Practices
- MySQL Security Best Practices
- MySQL Cluster Deployment Best Practices
- MySQL High Availability with InnoDB Cluster
- MySQL 索引背后的数据结构及算法原理
- Some study on database storage internals
- Sharding Pinterest: How we scaled our MySQL fleet
- Guide to MySQL High Availability
- Choosing MySQL High Availability Solutions
- High availability with MariaDB TX: The definitive guide
- 使用经验向文章推荐
  - Booking.com: Evolution of MySQL System Design ，Booking.com 的 MySQL 数据库使用的演化，其中有很多不错的经验分享，我相信也是很多公司会遇到的的问题。
  - Tracking the Money - Scaling Financial Reporting at Airbnb ，Airbnb 的数据库扩展的经验分享。
  - Why Uber Engineering Switched from Postgres to MySQL ，无意比较两个数据库谁好谁不好，推荐这篇 Uber 的长文，主要是想让你从中学习到一些经验和技术细节，这是一篇很不错的文章。
- MySQL 集群复制参考文献，都是很不错的实践性比较强的文章。
  - Monitoring Delayed Replication, With A Focus On MySQL
  - Mitigating replication lag and reducing read load with freno
  - 另外，Booking.com 给了一系列的文章可以看看：
    - Better Parallel Replication for MySQL
    - Evaluating MySQL Parallel Replication Part 2: Slave Group Commit
    - Evaluating MySQL Parallel Replication Part 3: Benchmarks in Production
    - Evaluating MySQL Parallel Replication Part 4: More Benchmarks in Production 
    - Evaluating MySQL Parallel Replication Part 4, Annex: Under the Hood
- MySQL 的数据分区参考文献。
  - StackOverflow: MySQL sharding approaches?
  - Why you don’t want to shard
  - How to Scale Big Data Applications
  - MySQL Sharding with ProxySQL
- 各个公司做 MySQL Sharding 的一些经验分享。
  - MailChimp: Using Shards to Accommodate Millions of Users 
  - Uber: Code Migration in Production: Rewriting the Sharding Layer of Uber’s Schemaless Datastore
  - Sharding & IDs at Instagram
  - Airbnb: How We Partitioned Airbnb’s Main Database in Two Weeks

## NoSQL

#### 通用资料

- Martin Fowler 在 YouTube 上分享的 NoSQL 介绍 Introduction To NoSQL， 以及他参与编写的 NoSQL Distilled - NoSQL 精粹，这本书才 100 多页，是本难得的关于 NoSQL 的书，很不错，非常易读。
- NoSQL Databases: a Survey and Decision Guidance，这篇文章可以带你自上而下地从 CAP 原理到开始了解 NoSQL 的种种技术，是一篇非常不错的文章。
- Distribution, Data, Deployment: Software Architecture Convergence in Big Data Systems，这是卡内基·梅隆大学的一篇讲分布式大数据系统的论文。其中主要讨论了在大数据时代下的软件工程中的一些关键点，也说到了 NoSQL 数据库。
- No Relation: The Mixed Blessings of Non-Relational Databases，这篇论文虽然有点年代久远。但这篇论文是 HBase 的基础，你花上一点时间来读读，就可以了解到，对各种非关系型数据存储优缺点的一个很好的比较。
- NoSQL Data Modeling Techniques ，NoSQL 建模技术。
- MongoDB - Data Modeling Introduction ，虽然这是 MongoDB 的数据建模介绍，但是其很多观点可以用于其它的 NoSQL 数据库。
- Firebase - Structure Your Database ，Google 的 Firebase 数据库使用 JSON 建模的一些最佳实践。因为 CAP 原理，所以当你需要选择一个 NoSQL 数据库的时候，你应该看看这篇文档 Visual Guide to NoSQL Systems。
- SQL vs. NoSQL Databases: What’s the Difference?
- Salesforce: SQL or NoSQL

### 数据结构数据库 Data structure Database   -   Redis

- 官方手册
- 《Redis核心原理与实践》
- Learn Redis the hard way (in production) at Trivago
- Twitter: How Twitter Uses Redis To Scale - 105TB RAM, 39MM QPS, 10,000+ Instances 
- Slack: Scaling Slack’s Job Queue - Robustly Handling Billions of Tasks in Milliseconds Using Kafka and Redis
- GitHub: Moving persistent data out of Redis at GitHub
- Instagram: Storing Hundreds of Millions of Simple Key-Value Pairs in Redis
- Redis in Chat Architecture of Twitch (from 27:22)
- Deliveroo: Optimizing Session Key Storage in Redis
- Deliveroo: Optimizing Redis Storage
- GitHub: Awesome Redis

### 文档数据库 Document Database - MongoDB, SimpleDB, CouchDB

- Data Points - What the Heck Are Document Databases?
- eBay: Building Mission-Critical Multi-Data Center Applications with MongoDB
- The AWS and MongoDB Infrastructure of Parse: Lessons Learned
- Migrating Mountains of Mongo Data
- Couchbase Ecosystem at LinkedIn
- SimpleDB at Zendesk
- Github: Awesome MongoDB