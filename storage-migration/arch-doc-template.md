# 🧬 Storage Migration

## 🏛️ Structure

### 1. 🎯 Problem Statement and Context

What is the problem? What is the context of the problem?
Example:

```
Food and Co is a food company that is using AWS S3 to store their food data.
It has started to be too expensive for the company though.
It is needed to make it cheaper without much noise and in a smooth way.
```

Recomended Reading: http://diego-pacheco.blogspot.com/2021/10/breaking-problems-down.html

### 2. 🎯 Goals

List in form of bullets what goals do have. Here it's great to have 5-10 lines.
Example:

```
1. Solution needs to be cheaper than current (AWS S3)
2. Migration needs to be smooth
3. It needs to keep the same functionalities as it has today (analytics, image store, machine learning,...)
```

Recommended Learning: http://diego-pacheco.blogspot.com/2020/05/education-vs-learning.html

### 3. 🎯 Non-Goals

List in form of bullets what non-goals do have. Here it's great to have 5-10 lines.
Example:

```
1. Change data: we don't want to change the existing data
2. New application: we don't necessary need a new application
3. We don't necessary need to migrate out of S3, if we find a cheap way of keeping it
```

Recommended Reading: http://diego-pacheco.blogspot.com/2021/01/requirements-are-dangerous.html

### 📐 3. Principles

List in form of bullets what design principles you want to be followed, it's great to have 5-10 lines.
Example:

```
1. Data Integrity: We can't loose any data while doing the migration
2. Backup: We need to have a way to backup if any of the migration process fails
3. Isolation: Storaging should be as isolated as possible from implementation, so it can be migrated in the future again
4. Scalability: Just like S3, our storaging needs to easily scale for tons of data
5. Availability: We need to have availability along multiple regions just like S3.
```

Recommended Reading: http://diego-pacheco.blogspot.com/2018/01/stability-principles.html

### 🏗️ 4. Overall Diagrams

Here there will be a bunch of diagrams, to understand the solution.

![overall diagram](./overall-architecture.drawio.png)

Recommended Reading: http://diego-pacheco.blogspot.com/2020/10/uml-hidden-gems.html

### 🧭 5. Trade-offs

Major Decisions:

```
1. S3 vs Blackbaze B2
PROS (+)
  * Lower cost on B2: $6 / TB / MONTH vs 23 / TB / MONTH
  * S3 API Compatible: S3 can easily be replaced with B2
CONS (+)
  * Speed: There are reports of slower speed compared to S3 for large workloads

1. Manual migration vs automated with tools like Flexify.IO
PROS (+)
  * Automated: Blackbaze will pay the transfer fees
  * Automated is Faster than manual
  * With manual migration you have more control of what is being passed
  * Flexify will automatically check for updates in data and send only updated data to new storage
CONS (+)
  * High risk of manually creating a script
  * Downtime needed for manual migration
```

|          | B2  | S3  | Azure | Google Cloud |
| -------- | --- | --- | ----- | ------------ |
| TB/month | $6  | $26 | $20   | $23          |

#### Migration costs

- $0.08 - $0.12 / GiB
- Blackbaze pay fees if over 50 TB are transfered (https://flexify.io/clouds/backblaze-b2)

PS: Be careful to not confuse problem with explanation.
<BR/>Recommended reading: http://diego-pacheco.blogspot.com/2023/07/tradeoffs.html

### 🌏 6. For each key major component

```

- Storage layer - Blackbaze B2 Cloud Storage
  - Primary data repository
  - Low cost s3-compatible APIs
  - Redundancy: Each file is stored redundantly across multiple drives, in multiple servers, in multiple locations in the data center.
- Data access layer - API in EC2
  - Abstracts interation with B2
  - Manages access
  - Caching (i.e Redis to speedup frequent requests)
  - Interface compatible with any clients
- Caching with Redis
  - Stores frequently accessed data for faster response
- Load Balancer
  - Distributes traffic
  - Direct traffic to health EC2 instances
  - Least connections strategy to deal with file processing from B2

6.1 -               : classic uml diagram with attributes and methods
6.2 - Contract Documentation     : Operations, Inputs and Outputs
6.3 - Persistence Model          : Diagrams, Table structure, partiotioning, main queries.
6.4 - Algorithms/Data Structures : Spesific algos that need to be used, along size with spesific data structures.
```

Exemplos of other components: Batch jobs, Events, 3rd Party Integrations, Streaming, ML Models, ChatBots, etc...

Recommended Reading: http://diego-pacheco.blogspot.com/2018/05/internal-system-design-forgotten.html

### 🖹 7. Migrations

![migration diagram](./migration-diagram.drawio.png)

### 🖹 8. Use cases & contracts

#### 8.1 Upload file

- Client sends request to upload file to API
- API generates a pre-signed URL from B2
- Client updates file to B2 using the URL
- API stores file metadata in Redis for fast access

- METHOD: `POST`
- URL: `/upload`
- Payload:

```json
{
  "filename": "photo1.jpg",
  "contentType": "image/jpeg",
  "size": 2048000
}
```

- Response:

```json
{
  "uploadUrl": "https://backblazeb2.com/my-bucket/naf7fh2738fh198aka/photo1.jpg",
  "fileId": "naf7fh2738fh198aka",
  "expirationDate": "2025-02-20T15:30:00Z"
}
```

#### 8.2 Download file

- Client requests a file to API
- API checks Redis for pre-signed URL
  - If cached, returns URL to client
  - If not, API asks B2 for a pre-signed URL and stores in Redis and returns to client
- Client can download the file from pre-signed URL

- METHOD: `GET`
- URL: `/download/{id}`

- Response:

```json
{
  "downloadUrl": "https://backblazeb2.com/my-bucket/naf7fh2738fh198aka/photo1.jpg",
  "expirationDate": "2025-02-20T15:30:00Z"
}
```

#### 8.3 Search file

- Client sends a search query to API
- API checks Redis for cached searches

  - If cached, returns the metadata
  - If not, queries metadata from B2 and returns

- METHOD: `GET`
- URL: `/search/{fileName}`

- Response:

```json
{
  "results": [
    {
      "fileId": "naf7fh2738fh198aka",
      "filename": "photo1.jpg",
      "size": 2048000,
      "uploadedAt": "2024-02-06T12:00:00Z"
    }
  ]
}
```

#### 8.4 Delete file

- Client sends delete request to API
- API verifies permissions
- API sends delete command to B2
- API remotes metadata from Redis

- METHOD: `DELETE`
- URL: `/delete/{id}`

- Response:

```json
{
  "message": "File deleted"
}
```

### 🖹 9. Testing strategy

- Benchmark testing: to ensure download/upload speed is similar before & after the migration
- E2E testing: After the migration, e2e can be used to ensure application key functionalities work.
- Visual regression (CSS) testing: Visual regression tests will catch any discrepancies or unloaded assets in the UI.

### 🖹 10. Observability strategy

- Logging strategy: Set up a logging platform splunk to log any errors or timeouts when retrieving / saving assets
- Analytics: Setup an analytics platform like Heap, to observe any analytics discrepancy after the migration and to keep monitoring sessions for discrepancies too.
- Use a platform like Datadog to monitor metrics, performance, usage, etc.

### 🖹 11. Data Store Designs

#### Database - store metadata and migration logs

| Table          | Content                                   | Reason                 |
| -------------- | ----------------------------------------- | ---------------------- |
| files          | file metadata, URLs, and migration status | Ensures data integrity |
| migration_logs | track migration status                    | Debug migrations       |

- Find files that haven't been migrated yet:
  - SELECT \* FROM files WHERE is_migrated = FALSE;
- Get File metadata
  - SELECT id, file_name, file_size, storage_url, is_migrated, created_at
    FROM files
    WHERE id = ?;

#### Redis - caches files pre-signed urls for fast access

| Key     | Value                                                                                                   | TTL |
| ------- | ------------------------------------------------------------------------------------------------------- | --- |
| file_id | { "url": "https://backblazeb2.com/my-bucket/naf7fh2738fh198aka/photo1.jpg", "expires_at": 17126378168 } | 15  |

#### Blackbaze B2 - Stores actual files migrated from S3

| Bucket            | Folder structure                               |
| ----------------- | ---------------------------------------------- |
| b2://food-company | category/{food_category}/{version}/{file_name} |
| b2://food-company | category/{food_category}/thumbnail/{file_name} |

### 🖹 12. Technology Stack

| Technology   | Purpose                                                | Reason                                                        |
| ------------ | ------------------------------------------------------ | ------------------------------------------------------------- |
| Backblaze B2 | File Storage                                           | Cheaper than S3, compatible APIs, support for pre-signed URLs |
| PostgreSQL   | Store files metadata and migration logs                | Scalable, reliable and good for structured data               |
| Redis        | Cache file metadata and pre-signed URLs                | Improve response time for highly accessed files               |
| EC2          | Hosts the Backend APIs that connect the client with B2 | Offers scalability to APIs                                    |
| AWS ALB      | Load balancer to distribute traffic                    | Automatic scaling, high availability                          |

### 🖹 13. References

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
