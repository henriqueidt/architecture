# 🧬 Jurassic Social

John Hammond wants a new social network for Dino junkies. The idea is to build a brand new social network with the features of: profiles, timelines, posts, comments, upload images and videos. However John wants to go further and wants the solution to also generate some content for him, so the content will come from user and from AI. He wants a LLM model capable of generating Jurassic facts, stories and even short comic stories. John wants the frontend in HTMX and the backend written in Java or Python. John wants the website to recommend users to follow and also want to have an online store where users can buy products. He wants the products to ahve links for every dino pic posted or video.

John wants a solution that scale and is multi-region by default. John does not like monoliths neither lambdas, those are banned from the solution. John wants to be able to censorship some posts in case they do not talk about dinos and want to be able to create a score system for users that are more engaged which will be rewarded.

## 🏛️ Structure

### 1. 🎯 Problem Statement and Context

The problem is to have a social network for Dino junkies. It should have all the common social network features such as: profiles, timelines, posts, comments, upload images and videos. The social network must recommend users to follow.
The solution must have a score system that ranks users by engagement in the platform.
Along with that, the solution must also have AI generated content, like facts, stories and short comic stories.
Additionally, the solution must also have an online store where users can buy products. Product pages must have links for the social media posts that references the product itself.
On the backoffice side, the management team must be able to censorship posts that do not talk about dinos and reward the users that are more engaged within the platform.

Constrains:

- Frontend written in HTMX
- Backend written in Java or Python
- Solution must be multi-region
- No monoliths
- No lambdas

### 2. 🎯 Goals

1. Connection between store and social media: Every image and video from social media must be connected with relevant store product
2. Multi-region: Solution must be scalable and operating in multiple AWS regions
3. Recommendation: The system must make smart recommendations of users to follow based on user's navigation history
4. Score system: The solution must automatically qualify users by their engagement
5. Assets optimization: The system must handle the high number of images/videos gracefully with low latency strategies (image optimization, lazy loading, etc)

### 3. 🎯 Non-Goals

1. No native mobile: Given the UI must be written in HTMX, no iOS or Android native builds are supported
2. No lambdas or serverless functions
3. No monolithic backend: Services must be separated per business rules
4. Not a general social network: It must not contain non-dino content
5. No auto-removal of content: Only the admin can censorship content, although the system can suggest what should be censored.

### 📐 3. Principles

1. Service Boundaries: Services communicate only through API contracts. No shared database.
2. Fail-safe moderation: If there is an outage or failure in the relevance score system, content must be held until it can be scored properly.
3. Stateless services: all state lives in an external store. That makes horizontal scaling and multi-region failover work
4. Graceful degradation: Non-core features like store, recommendation and AI content must have a fallback and not stop the whole application

### 🏗️ 4. Overall Diagrams

Here there will be a bunch of diagrams, to understand the solution.

```
🗂️ 4.1 Overall architecture: Show the big picture, relationship between macro components.
🗂️ 4.2 Deployment: Show the infra in a big picture.
🗂️ 4.3 Use Cases: Make 1 macro use case diagram that list the main capability that needs to be covered.
```

Recommended Reading: [UML hidden gems](http://diego-pacheco.blogspot.com/2020/10/uml-hidden-gems.html)

### 🧭 5. Trade-offs

List the tradeoffs analysis, comparing pros and cons for each major decision.
Before you need list all your major decisions, them run tradeoffs on than.
example:
Major Decisions:

```
1. One mobile code base - should be (...)
2. Reusable capability and low latency backends should be (...)
3. Cache efficiency therefore should do (...)
```

Tradeoffs:

```
1. React Native vs (Flutter and Native)
2. Serverless vs Microservices
3. Redis vs Enbeded Caches
```

Each tradeoff line need to be:

```
PROS (+)
  * Benefit: Explanation that justify why the benefit is true.
CONS (+)
  * Problem: Explanation that justify why the problem is true.
```

PS: Be careful to not confuse problem with explanation.
<BR/>Recommended reading: [Tradeoffs](http://diego-pacheco.blogspot.com/2023/07/tradeoffs.html)

### 🌏 6. For each key major component

What is a majore component? A service, a lambda, a important ui, a generalized approach for all uis, a generazid approach for computing a workload, etc...

```
6.1 - Class Diagram              : classic uml diagram with attributes and methods
6.2 - Contract Documentation     : Operations, Inputs and Outputs
6.3 - Persistence Model          : Diagrams, Table structure, partiotioning, main queries.
6.4 - Algorithms/Data Structures : Spesific algos that need to be used, along size with spesific data structures.
```

Exemplos of other components: Batch jobs, Events, 3rd Party Integrations, Streaming, ML Models, ChatBots, etc...

Recommended Reading: [Internal system design forgotten](http://diego-pacheco.blogspot.com/2018/05/internal-system-design-forgotten.html)

### 🖹 7. Migrations

IF Migrations are required describe the migrations strategy with proper diagrams, text and tradeoffs.

### 🖹 8. Testing strategy

Explain the techniques, principles, types of tests and will be performaned, and spesific details how to mock data, stress test it, spesific chaos goals and assumptions.

### 🖹 9. Observability strategy

Explain the techniques, principles,types of observability that will be used, key metrics, what would be logged and how to design proper dashboards and alerts.

### 🖹 10. Data Store Designs

For each different kind of data store i.e (Postgres, Memcached, Elasticache, S3, Neo4J etc...) describe the schemas, what would be stored there and why, main queries, expectations on performance. Diagrams are welcome but you really need some dictionaries.

### 🖹 11. Technology Stack

Describe your stack, what databases would be used, what servers, what kind of components, mobile/ui approach, general architecture components, frameworks and libs to be used or not be used and why.

### 🖹 12. References

- [Architecture Anti-Patterns](https://architecture-antipatterns.tech/)
- [Enterprise Integration Patterns](https://www.enterpriseintegrationpatterns.com/)
- [SOA Patterns](https://patterns.arcitura.com/soa-patterns)
- [Microservice API Patterns](https://microservice-api-patterns.org/)
- [Software Development Anti-Patterns](https://sourcemaking.com/antipatterns/software-development-antipatterns)
- [Refactoring Patterns](https://sourcemaking.com/refactoring/refactorings)
- [Database Refactoring Patterns](https://databaserefactoring.com/)
- [Data Modelling Redis](https://redis.com/blog/nosql-data-modeling/)
- [Cloud Design Patterns](https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/introduction.html)
- [12 Factors App](https://12factor.net/)
- [Relational DB Patterns](https://www.geeksforgeeks.org/design-patterns-for-relational-databases/)
- [Rendering Patterns](https://www.patterns.dev/vanilla/rendering-patterns/)
- [REST API Design Patterns](https://blog.stoplight.io/api-design-patterns-for-rest-web-services)
