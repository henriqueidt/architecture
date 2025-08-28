# 🧬 Latency Afficionados

## 🏛️ Structure

### 1. 🎯 Problem Statement and Context

What is the problem? What is the context of the problem?
Example:

```
We need to work on a project for a RETRO video game marketplace platform.
The operations it support are:
- post products
- search products
- view products description
- rate products with review and comments
- recommend products to users based on preview browsing
The UI is running on React 16
The backend is in a monolith on Java 1.4. We need to migrate it to java 21, while also decomposing the monolith and having the best rendering time possible

```

### 2. 🎯 Goals

```
1. Speed up rendering time as much as possible
2. Migrate from Java 1.4 to Java 21
3. Decompose the monolith into separate repos
4. Keep existing functionalities (post, search, view, rate, comment, recommendation)
```

### 3. 🎯 Non-Goals

```
1. Kepp all in a single monolith. We want to decompose as possible.
2. Create a new UI application. React 16 is not that far from newer technology, so we can migrate without needing to recreate everything.
3. Create new functionalities. There's no need for new functionalities, the pain is with current performance
```

### 📐 3. Principles

```
1. Testability: We need to test the application remains working as before (unit, integration and visual regression tests).
1. Efficient rendering: The application must render efficiently and fast
2. Code splitting: We need to split the monolith into meaningful different projects (Single Responsability Principle)
3. Observability: Metrics like Time to Interactive, user session time, users retention must be got to compare before/after the rendering optimization
5. Cache efficiency: We need to have an efficient cache system in order to make it faster for UI to render
```

### 🏗️ 4. Overall Diagrams

### 🧭 5. Trade-offs

List the tradeoffs analysis, comparing pros and cons for each major decision.
Before you need list all your major decisions, them run tradeoffs on than.
example:
Major Decisions:

```
1. Performatic UI rendering with ISR
2. Splitting DBs per microservice
```

Tradeoffs:

1. Modernize the frontend rendering with SSR vs CSR

https://github.com/henriqueidt/poc-rendering-techniques

- SSR (SERVER SIDE RENDERING):

  - (+) More performatic as most of computation is done on the server
  - (+) Better for Dynamic content as pages are render on request
  - (-) Low latency as pages are rendered on the server on each page request

- CSR (CLIENT SIDE RENDERING) CURRENT:

  - (+) Less backend computation = cheaper
  - (+) Good for pages with high interaction
  - (-) Bad SEO
  - (-) Slow rendering time

- ISR (INCREMENTAL STATIC REGENERATION) - hybrid solution with Next.js
  - (+) Combines static generation with real-time updates for frequently changed data
  - (+) Faster load as pages are prebuilt in the background
  - (-) Complex cache revalidation

2. NextJS vs Others

- NextJS

  - (+) Mature ecossystem, less boilerplate, large community
  - (+) Easy to implement multiple rendering techniques Out of the Box
  - (+) Good Out of the Box BE integration tools (API routes, server actions)
  - (+) Built in performance features like Link prefetching, image optimization
  - (-) Bundle size

- Astro
  - (+) Lighter bundle, only serves necessary JS
  - (+) Easy to use, simple features
  - (-) Less mature, small community
  - (-) Less robust features out of the box

Overall, Astro fits great for simple static sites, but for robust website with different features and higher complexity, NextJS seems to be the right fit

3. Single shared DB vs Splitted DBs per service

- (+) With independent DBs, each service manages it's own data, avoiding distributed monolith issues
- (+) Better isolation, changes to one DB shouldn't affect the others
- (-) Keeping data consistent between all services and DBs can be more complex
- (-) More operational overhead with multiple DBs to manage

4. Connect client directly to Microservices vs using an API Gateway

- (+) The API Gateway can act as a facade, hiding complexity of calling multiple microservices from client
- (+) The API Gateway makes it easy to migrate from the monolith to microservices (we can refactor the services as we go, as long as we keep the Gateway contract the same)
- (+) The API Gateway can deal with authentication, so that we don't need to have it on every microservice
- (-) The API Gateway will introduce a new service to be mantained, monitored, scaled, etc.
- (-) The API Gateway Adds another layer to debug, observe

### 🌏 6. For each key major component

What is a majore component? A service, a lambda, a important ui, a generalized approach for all uis, a generazid approach for computing a workload, etc...

```
6.1 - Class Diagram              : classic uml diagram with attributes and methods
6.2 - Contract Documentation     : Operations, Inputs and Outputs
6.3 - Persistence Model          : Diagrams, Table structure, partiotioning, main queries.
6.4 - Algorithms/Data Structures : Spesific algos that need to be used, along size with spesific data structures.
```

#### CONTRACTS:

**User Service:**

1. **POST /api/users/register**

`Registers a new user`

- REQ Body:

```JSON
{
  "name": "String",
  "email": "String",
  "password": "String"
}
```

Example:

```JSON
  {
    "name": "Henrique Eidt",
    "email": "henrique@example.com",
    "password": "q938j89a4g8"
  }
```

- RESP Body:
  ```JSON
   {
    "message": "String"
   }
  ```
  Example:
  ```JSON
    {
      "message": "User registered successfully"
    }
  ```

2. POST /api/users/login

`User sign in with email and password. Returns a JWT to be used to authenticate subsequent requests.`

- REQ Body:
  ```JSON
  {
    "email": "String",
    "password": "String"
  }
  ```
  Example:
  ```JSON
    {
      "email": "henrique@example.com",
      "password": "q938j89a4g8"
    }
  ```
- RESP Body:

  ```JSON
  {
    "token": "String",
    "name": "String",
    "email": "String",
    "avatar": "String"
  }
  ```

  Example:

  ```JSON
    {
      "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiYWRtaW4iOnRydWUsImlhdCI6MTUxNjIzOTAyMn0.KMUFsIDTnFmyG3nMiGM6H9FNFUROf3wh7SmqJp-QV30",
      "name": "Henrique Eidt",
      "email": "henrique@example.com",
      "avatar": "https://example.com/avatar.jpg"
    }
  ```

  **Product Service:**

1. GET /api/products

`Retrieves a list of products. Includes pagination, sorting and filtering`

- QUERY PARAMS:

  |   Param   |  Type  |             Description             |
  | :-------: | :----: | :---------------------------------: |
  |   page    |  int   |            Current page             |
  | page_size |  int   |           Items per page            |
  |   sort    | string | Optional: i.e. price_asc, name_desc |
  | category  | string |           Optional filter           |
  |  search   | string |        Optional search query        |

- REQ Example:
  ```HTTP
  GET /api/products?page=1&page_size=20&sort=price_asc
  ```
- RESP Body:

  ```JSON
    {
      "data": [
        {
          "id": "String",
          "title": "String",
          "price": "Number",
          "thumbnail": "String"
        }
      ]
    }
  ```

  ```JSON
    {
      "data": [
        {
          "id": "12345",
          "title": "Super Mario World",
          "price": 145.00,
          "thumbnail": "https://cdn.example.com/img/123.jpg"
        },
        {
          "id": "123456",
          "title": "Mario Kart",
          "price": 239.90,
          "thumbnail": "https://cdn.example.com/img/124.jpg"
        }
      ],
      "pagination": {
        "page": 2,
        "page_size": 20,
        "totalItems": 120,
        "totalPages": 6
      }
    }
  ```

Exemplos of other components: Batch jobs, Events, 3rd Party Integrations, Streaming, ML Models, ChatBots, etc...

Recommended Reading: http://diego-pacheco.blogspot.com/2018/05/internal-system-design-forgotten.html

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

- Architecture Anti-Patterns: https://architecture-antipatterns.tech/
- EIP https://www.enterpriseintegrationpatterns.com/
- SOA Patterns https://patterns.arcitura.com/soa-patterns
- API Patterns https://microservice-api-patterns.org/
- Anti-Patterns https://sourcemaking.com/antipatterns/software-development-antipatterns
- Refactoring Patterns https://sourcemaking.com/refactoring/refactorings
- Database Refactoring Patterns https://databaserefactoring.com/
- Data Modelling Redis https://redis.com/blog/nosql-data-modeling/
- Cloud Patterns https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/introduction.html
- 12 Factors App https://12factor.net/
- Relational DB Patterns https://www.geeksforgeeks.org/design-patterns-for-relational-databases/
- Rendering Patterns https://www.patterns.dev/vanilla/rendering-patterns/
- REST API Design https://blog.stoplight.io/api-design-patterns-for-rest-web-services
