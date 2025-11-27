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

![Architecture Diagram](./architecture.drawio.png)

![Usecases Diagram](./use-case.drawio.png)

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
  - (-) High coupling with NextJS framework

- Astro
  - (+) Lighter bundle, only serves necessary JS
  - (+) Easy to use, simple features
  - (-) Less mature, small community
  - (-) Less robust features out of the box

Overall, Astro fits great for simple static sites, but for robust website with different features and higher complexity, NextJS seems to be the right fit

#### Single shared DB vs Splitted DBs per service

Single shared DB:

- (+) Simpler architecture, less components to maintain
- (-) High coupling between services
- (-) Harder to scale services independently
- (-) Changes to the DB structure can impact all services

Splitted DBs per service:

- (+) With independent DBs, each service manages it's own data, avoiding distributed monolith issues
- (+) Better isolation, changes to one DB shouldn't affect the others
- (-) More operational overhead with multiple DBs to manage

#### Connect client directly to Microservices vs using an API Gateway

Connect client directly to Microservices:

- (+) Simpler architecture, less components to mantain
- (+) Less latency as requests go directly to the microservice
- (-) More complex client, as it needs to know all microservices endpoints
- (-) Harder to migrate from monolith to microservices, as client would need to handle when to call which

Using an API Gateway:

- (+) The API Gateway can act as a facade, hiding complexity of calling multiple microservices from client
- (+) The API Gateway makes it easy to migrate from the monolith to microservices (we can refactor the services as we go, as long as we keep the Gateway contract the same)
- (+) The API Gateway can deal with authentication, so that we don't need to have it on every microservice
- (-) The API Gateway will introduce a new service to be mantained, monitored, scaled, etc.
- (-) The API Gateway Adds another layer to debug, observe

#### Amazon MSK

- (+) Highly available and scalable without much overhead
- (+) AWS ecosystem
- (+) Data persistence and replayability
- (+) Compability with Apache Kafka ecosystem
- (-) More expensive than self managed Kafka

#### Aggregator service vs GraphQL:

Aggregator service:

- (+) Less overhead of adding a new structure (GraphQL server)
- (-) More endpoints to mantain

GraphQL:

- (+) More flexible for clients
- (+) Single endpoint
- (-) More overhead of adding a new structure (GraphQL server)
- (-) More complexity on client side to build queries

#### ECS Fargate vs EKS

ECS Fargate (Elastic Container Service):

- (+) Runs on AWS infrastructure.
- (+) Access managed via IAM roles.
- (+) Simpler to set up and manage.
- (-) Less control over the infrastructure.
- (-) Vendor lock-in to AWS.

EKS (Elastic Kubernetes Service):

- (+) More control over the Kubernetes environment when compared to ECS.
- (+) More options of configurations and customizations comparing to ECS.
- (+) Can run on multiple cloud providers or on-premises.
- (-) More complex to set up and manage.
- (-) Requires knowledge of Kubernetes.

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
  "name": "String" | "The name of the customer",
  "email": "String" | "The email of the customer",
  "password": "String" | "The password of the customer to register in the system"
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
    "message": "String" | "A message describing if the registration was successfull or not"
   }
  ```

  Success Example:

  ```JSON
    {
      "message": "User registered successfully"
    }
  ```

  Failure Example:

  ```JSON
    {
      "message": "There was a problem registering the user"
    }
  ```

2. POST /api/users/login

`User sign in with email and password. Returns a JWT to be used to authenticate subsequent requests.`

- REQ Body:
  ```JSON
  {
    "email": "String" | "The email of the customer",
    "password": "String" | "The password of the customer to access the system"
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
    "token": "String" | "JWT token to be used in subsequent requests",
    "name": "String" | "The name of the customer that logged in",
    "email": "String" | "The email of the customer that logged in",
    "avatar": "String" | "The avatar URL of the customer"
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
          "id": "String" | "The ID of the product",
          "title": "String" | "The title/name of the product",
          "price": "Number" | "The price of the product",
          "thumbnail": "String" | "The thumbnail URL of the product"
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

2. GET /api/products/{id}

`Retrieves details for a single product by ID`

- RESP Body:
  ```JSON
  {
    "id": "String" | "The ID of the product",
    "title": "String" | "The title/name of the product",
    "price": "Number" | "The price of the product",
    "thumbnail": "String" | "The thumbnail URL of the product",
    "description": "String" | "The description of the product",
    "category": "String" | "The category of the product",
    "createdAt": "ISODate" | "The date the product was created",
    "reviews": {
      "score": "Number" | "The average review score of the product",
      "count": "Number" | "The total number of reviews for the product",
      "reviewList": {
        "data": [
          {
            "id": "String" | "The ID of the review",
            "name": "String" | "The name of the reviewer",
            "avatar": "String" | "The avatar URL of the reviewer",
            "rating": "Number" | "The rating given by the reviewer",
            "comment": "String" | "The review comment"
          }
        ],
        "pagination": {
          "page": "Number" | "Current page of reviews",
          "page_size": "Number" | "Number of reviews per page",
          "totalItems": "Number" | "Total number of reviews",
          "totalPages": "Number" | "Total pages of reviews"
      }
    }
  }
  ```
  Example:
  ```JSON
    {
      "id": "12345",
      "title": "Super Mario World",
      "price": 145.00,
      "thumbnail": "https://cdn.example.com/img/123.jpg",
      "description": "Classic Super Nintendo game",
      "category": "Adventure",
      "createdAt": "2025-01-01T12:00:00Z",
      "reviews": {
        "score": 4.5,
        "count": 150,
        "reviewList": {
          "data": [
            {
              "id": "r1",
              "name": "Alice",
              "avatar": "https://cdn.example.com/avatars/alice.jpg",
              "rating": 5,
              "comment": "Amazing game!"
            },
            {
              "id": "r2",
              "name": "Bob",
              "avatar": "https://cdn.example.com/avatars/bob.jpg",
              "rating": 4,
              "comment": "Awesome!!"
            }
          ],
          "pagination": {
            "page": 1,
            "page_size": 10,
            "totalItems": 150,
            "totalPages": 15
          }
        }
      }
    }
  ```

3. **POST /api/products**

`Creates a new product (authentication required)`

- REQ Body:
  ```JSON
  {
    "title": "String" | "The title/name of the product",
    "description": "String" | "The description of the product",
    "price": "Number" | "The price of the product",
    "category": "String" | "The category of the product",
  }
  ```
  Example:
  ```JSON
  {
    "title": "Mario Kart",
    "description": "Original game card, works 100%.",
    "price": 239.90,
    "category": "SNES",
  }
  ```
- RESP Body:
  ```JSON
  {
    "id": "String",
    "message": "String"
  }
  ```
  Example:
  ```JSON
  {
    "id": "123456",
    "message": "Product created successfully"
  }
  ```

4. **POST /api/products/{id}/images**

`Uploads an image for a specific product (authentication required) (multiple calls for multiple images)`

- REQ Body: Multipart form-data with image files

  - Field Name: `image`
  - File Types: JPEG, PNG

- REQ Example:

  ```HTTP
  POST /api/products/123456/images
  Content-Type: multipart/form-data
  Authorization: Bearer <token>
  Form Data:
    image: (file) image.jpg
  ```

- RESP Body:
  ```JSON
  {
    "imageUrl": "String" | "The URL of the uploaded image",
    "message": "String" | "A message indicating the upload status"
  }
  ```
  Example:
  ```JSON
  {
    "imageUrl": "https://cdn.example.com/products/123456/uuid.jpg",
    "message": "Image uploaded successfully"
  }
  ```

**Reviews Service**

1. **POST /api/products/{id}/reviews**

`Submits a review for a specific product`

- REQ Body:
  ```JSON
  {
    "rating": "Number" | "The rating given by the reviewer (1-5)",
    "comment": "String" | "The review comment"
  }
  ```
  Example:
  ```JSON
  {
    "rating": 5,
    "comment": "Amazing game!"
  }
  ```
- RESP Body:
  ```JSON
  {
    "id": "String" | "The ID of the created review",
    "message": "String" | "A message indicating the review submission status"
  }
  ```
  Example:
  ```JSON
  {
    "id": "r123",
    "message": "Review submitted successfully"
  }
  ```

2. **GET /api/products/{id}/reviews**

`Gets reviews for a specific product with pagination`

- QUERY PARAMS:

  |   Param   | Type |  Description   |
  | :-------: | :--: | :------------: |
  |   page    | int  |  Current page  |
  | page_size | int  | Items per page |

- REQ Example:

  ```HTTP
  GET /api/products/123/reviews?page=1&page_size=10
  ```

- RESP Body:

  ```JSON
  {
    "data": [
      {
        "id": "String" | "The ID of the review",
        "name": "String" | "The name of the reviewer",
        "avatar": "String" | "The avatar URL of the reviewer",
        "rating": "Number" | "The rating given by the reviewer",
        "comment": "String" | "The review comment"
      }
    ],
    "pagination": {
      "page": "Number" | "Current page of reviews",
      "page_size": "Number" | "Number of reviews per page",
      "totalItems": "Number" | "Total number of reviews",
      "totalPages": "Number" | "Total pages of reviews"
    }
  }
  ```

  Example:

  ```JSON
  {
    "data": [
      {
        "id": "r1",
        "name": "John Doe",
        "avatar": "https://cdn.example.com/avatars/john.jpg",
        "rating": 5,
        "comment": "Amazing game!"
      }
    ],
    "pagination": {
      "page": 1,
      "page_size": 10,
      "totalItems": 1,
      "totalPages": 1
    }
  }
  ```

**Cart Service**

1. **POST /api/cart/add**

`Add a product to the user's cart`

- REQ Body:
  ```JSON
  {
    "product_id": "String" | "The ID of the product to add to the cart",
    "quantity": "Number" | "The quantity of the product to add"
  }
  ```
  Example:
  ```JSON
  {
    "product_id": "12345",
    "quantity": 2
  }
  ```
- RESP Body:
  ```JSON
  {
    "message": "String" | "A message indicating the add to cart status"
  }
  ```
  Example:
  ```JSON
  {
    "message": "Product added to cart successfully"
  }
  ```

2. **GET /api/cart**
   `Retrieves the current user's cart`

- RESP Body:
  ```JSON
  {
    "items": [
      {
        "product_id": "String" | "The ID of the product",
        "title": "String" | "The title of the product",
        "price": "Number" | "The price of the product",
        "quantity": "Number" | "The quantity of the product in the cart"
      }
    ],
    "total_amount": "Number" | "The total amount of the cart"
  }
  ```
  Example:
  ```JSON
  {
    "items": [
      {
        "product_id": "12345",
        "title": "Super Mario World",
        "price": 14500,
        "quantity": 2
      }
    ],
    "total_amount": 29000
  }
  ```

3. **DELETE /api/cart/remove/{id}**

`Removes a product from the user's cart`

- RESP Body:
  ```JSON
  {
    "message": "String" | "A message indicating the remove from cart status"
  }
  ```
  Example:
  ```JSON
  {
    "message": "Product removed from cart successfully"
  }
  ```

4. **PUT /api/cart/update/{id}**

`Updates the quantity of a product in the user's cart`

- REQ Body:
  ```JSON
  {
    "quantity": "Number" | "The new quantity of the product in the cart"
  }
  ```
  Example:
  ```JSON
  {
    "quantity": 3
  }
  ```
- RESP Body:
  ```JSON
  {
    "message": "String" | "A message indicating the update cart status"
  }
  ```
  Example:
  ```JSON
  {
    "message": "Cart updated successfully"
  }
  ```

**Payment Service**

1. **POST /api/payments/checkout**

`Starts the checkout process for the items in the user's cart`

- REQ Body:

  ```JSON
  {
    "customer_email": "string" | "Email of the customer",
    "line_items": [
      {
        "adjustable_quantity": {
          "enabled": "boolean" | "Tells if the quantity can be adjusted during checkout",
          "maximum": "integer" | "Maximum quantity",
          "minimum": "integer" | "Minimum quantity"
        },
        "price_data": {
          "currency": "string" | "3 letter currency code",
          "product_data": {
            "name": "string" | "Name of the product",
            "images": [
              "string" | "Image URL of the product"
            ]
          },
          "unit_amount": "integer" | "Price in cents"
        },
        "quantity": "integer" | "Quantity of the product"
      }
    ],
    "mode": "string" | "Mode of the checkout session, payment | subscription | setup",
    "return_url": "string" | "URL to redirect if user cancels the payment",
    "success_url": "string" | "URL to redirect after successful payment",
    "ui_mode": "string" | "The mode of the checkout page. custom = embedded components, embedded = iframe, hosted = redirect to Stripe hosted page",
  }
  ```

  Example:

  ```JSON
  {
    "customer_email": "john.doe@example.com",
  }
    "line_items": [
      {
        "adjustable_quantity": {
          "enabled": true,
          "maximum": 10,
          "minimum": 1
        },
        "price_data": {
          "currency": "usd",
          "product_data": {
            "name": "Super Mario World",
            "images": [
              "https://cdn.example.com/img/123.jpg"
            ]
          },
          "unit_amount": 4500
        },
        "quantity": 1
      }
    ],
    "mode": "payment",
    "cancel_url": "https://example.com/cart",
    "success_url": "https://example.com/success",
    "ui_mode": "hosted"
  }
  ```

- RESP Body:
  ```JSON
  {
    "checkout_url": "string" | "The URL to redirect the user to complete the payment",
    "session_id": "string" | "The ID of the created checkout session"
  }
  ```
  Example:
  ```JSON
  {
    "checkout_url": "https://checkout.stripe.com/pay/4e2b9f7a-3c6d-4f1a-9b21d2e3b",
    "session_id": "a1b2c3d4e5f6g7h8i9j0k"
  }
  ```

2. **GET /api/payments/checkout/:id?expand=line_items**

`Retrieves the data of a checkout session`

- RESP Body:
  ```JSON
  {
    "payment_status": "string" | "The payment status of the checkout session",
    "amount_total": "integer" | "The total amount of the checkout session in cents",
    "line_items": [
      {
        "id": "string" | "The ID of the line item",
        "description": "string" | "The description of the line item",
        "quantity": "integer" | "The quantity of the line item",
        "price": {
          "currency": "string" | "The currency of the price",
          "amount": "integer" | "The amount of the price"
        }
      }
    ]
  }
  ```
  Example:
  ```JSON
  {
    "payment_status": "completed",
    "amount_total": 4500,
    "line_items": [
      {
        "id": "li_123",
        "description": "Super Mario World",
        "quantity": 1,
        "price": {
          "currency": "usd",
          "amount": 4500
        }
      }
    ]
  }
  ```

Recommended Reading: http://diego-pacheco.blogspot.com/2018/05/internal-system-design-forgotten.html

### 🖹 7. Migrations

IF Migrations are required describe the migrations strategy with proper diagrams, text and tradeoffs.

### 🖹 8. Testing strategy

- Unit Tests

  - UI unit testing
    - 100% branches coverage
    - 100% functions coverage
    - Jest
    - React Testing Library
  - UI type checking
    - Typescript
    - No components or pages written in JS
  - BE unit testing
    - 100% branches coverage
    - 80% functions coverage
    - JUnit
    - Mockito & PowerMockito

- Integration Tests
  - Test services integration with databases
    - Test database schema migrations
    - Test main queries
  - Test stripe integration
  - Test aggregator service integration with microservices
- End to End Tests & Visual Regression Tests
  - Playwright
  - Test concistensy of UI
  - Critical user flows:
    - Login
    - Create a product
    - Search a product
    - View product details
    - Add product to cart
    - Checkout
    - View order data
- Performance Tests
  - Stress Testing with Grafana K6
    - Test services response time under load for major user flows
      - P95: 500ms for product creation
      - P95: 200ms for product listing
      - P95: 300ms for product details
      - P95: 500ms for checkout process
- A/B Tests
  - Used before productizing major changes
  - Optimizely

### 🖹 9. Observability strategy

- Logging
  - Splunk
  - Correlation IDs for tracing requests
  - Logging Levels:
    - INFO: API calls, service start/stop
    - WARN: Recoverable errors
    - ERROR: Exceptions, API errors
  - Splunk dashboards
    - Error rates
    - Warning rates
    - Important events rates
- UI Insights
  - Heap Analytics
  - User session tracking
  - Measure user engagement, session time, retention
- Metrics
  - Prometheus for collecting service metrics
  - Grafana dashboards for visualizing service performance
    - CPU usage
    - Memory usage
    - Response time
    - Server Load

### 🖹 10. Data Store Designs

#### 10.1 - Database Schemas

##### User Table - Postgres

Stores user data, to be used on login, authentication, authorization and user management.

| Column        | Type      | Constraints                                      |
| ------------- | --------- | ------------------------------------------------ |
| id            | UUID      | Primary Key, NOT NULL                            |
| name          | VARCHAR   | NOT NULL                                         |
| email         | VARCHAR   | UNIQUE, NOT NULL                                 |
| password_hash | VARCHAR   | NOT NULL                                         |
| created_at    | TIMESTAMP | NOT NULL, DEFAULT now()                          |
| updated_at    | TIMESTAMP | NOT NULL, DEFAULT now() (update on modification) |

##### Product Table - Postgres

Stores product data, to be used in product listing, searching, viewing and management.

| Column      | Type      | Constraints                                      |
| ----------- | --------- | ------------------------------------------------ |
| id          | UUID      | Primary Key, NOT NULL                            |
| title       | VARCHAR   | NOT NULL                                         |
| description | TEXT      | NOT NULL                                         |
| price       | DECIMAL   | NOT NULL                                         |
| category    | VARCHAR   | NOT NULL                                         |
| created_at  | TIMESTAMP | NOT NULL, DEFAULT now()                          |
| updated_at  | TIMESTAMP | NOT NULL, DEFAULT now() (update on modification) |
| image_urls  | TEXT[]    |                                                  |

##### Review Table - Postgres

Stores product reviews data, to be used in product rating and commenting by users

| Column     | Type      | Constraints                                      |
| ---------- | --------- | ------------------------------------------------ |
| id         | UUID      | Primary Key, NOT NULL                            |
| product_id | UUID      | Foreign Key (references Product.id), NOT NULL    |
| user_id    | UUID      | Foreign Key (references User.id), NOT NULL       |
| rating     | INT       | NOT NULL, CHECK (rating >= 1 AND rating <= 5)    |
| comment    | TEXT      | NOT NULL                                         |
| created_at | TIMESTAMP | NOT NULL, DEFAULT now()                          |
| updated_at | TIMESTAMP | NOT NULL, DEFAULT now() (update on modification) |

##### Cart Table - Postgres

Stores cart data, to be used in cart management by users. Each row represents a product in the user's cart.

| Column     | Type      | Constraints                                                   |
| ---------- | --------- | ------------------------------------------------------------- |
| id         | UUID      | Primary Key, NOT NULL                                         |
| cart_id    | UUID      | Foreign Key (references Cart.id), NOT NULL                    |
| user_id    | UUID      | Foreign Key (references User.id), NOT NULL                    |
| product_id | UUID      | Foreign Key (references Product.id), NOT NULL                 |
| quantity   | INT       | NOT NULL, CHECK (quantity > 0)                                |
| created_at | TIMESTAMP | NOT NULL, DEFAULT now()                                       |
| updated_at | TIMESTAMP | NOT NULL, DEFAULT now() (update on modification)              |
| status     | VARCHAR   | NOT NULL, CHECK (status IN ('active', 'ordered', 'canceled')) |

##### Payment Table - Postgres

Stores payment data, to be used to track payment status for user carts.

| Column     | Type      | Constraints                                                    |
| ---------- | --------- | -------------------------------------------------------------- |
| id         | UUID      | Primary Key, NOT NULL                                          |
| cart_id    | UUID      | Foreign Key (references Cart.id), NOT NULL                     |
| user_id    | UUID      | Foreign Key (references User.id), NOT NULL                     |
| amount     | DECIMAL   | NOT NULL                                                       |
| status     | VARCHAR   | NOT NULL, CHECK (status IN ('pending', 'completed', 'failed')) |
| created_at | TIMESTAMP | NOT NULL, DEFAULT now()                                        |
| updated_at | TIMESTAMP | NOT NULL, DEFAULT now() (update on modification)               |

#### 10.2 - Main Queries

##### Get Products with Pagination, Sorting and Filtering

```SQL
SELECT * FROM Product
WHERE category = $1 AND title ILIKE '%' || $2 || '%'
ORDER BY CASE WHEN $3 = 'price_asc' THEN price END ASC,
         CASE WHEN $3 = 'price_desc' THEN price END DESC,
         CASE WHEN $3 = 'name_asc' THEN title END ASC,
         CASE WHEN $3 = 'name_desc' THEN title END DESC
LIMIT $4 OFFSET ($5 - 1) * $4;
```

##### Get Product Details

```SQL
SELECT * FROM Product WHERE id = $1;
```

##### Get Reviews for a Product with Pagination

```SQL
SELECT * FROM Review
WHERE product_id = $1
ORDER BY created_at DESC
LIMIT $2 OFFSET ($3 - 1) * $2;
```

##### Get User Cart

```SQL
SELECT * FROM Cart
WHERE user_id = $1 AND status = 'active';
```

##### Get Payment Status for Cart

```SQL
SELECT * FROM Payment
WHERE cart_id = $1 AND user_id = $2;
```

### 🖹 11. Technology Stack

#### Frontend Stack

- **Framework**: Next.js 16
  - ISR support
  - Mature ecosystem
  - Server Components for better performance
  - Page pre-rendering capabitlities
  - Built-in image optimization
- **UI Library**: React 19
  - Easier migration from React 16
  - New features like concurrent rendering and automatic batching
- **State Management**: Zustand
  - Lightweight alternative to Redux
  - Better performance and simpler API
- **Styling**: CSS Modules
  - Scoped styles to avoid conflicts
- **Testing**: Jest + React Testing Library + Playwright
  - Unit, integration, E2E and visual regression testing

#### Backend Stack

- **Language**: Java 25
  - "Easier" migration from Java 1.4 than into other languages
  - Secure with modern language features
  - Performance improvements over Java 1.4
- **Framework**: Spring Boot 4.0
  - Modern microservices framework
  - Muiltiple API built-in features
  - Large community and ecosystem
- **Testing**: JUnit 6 + Mockito
  - Unit and integration testing

#### Databases

- **PostgreSQL 18**
  - Separate database per microservice
  - ACID
    - Atomic: All operations are completed or none are applied, no partial updates.
    - Consistent: No inconsistent data states. Data always change from one valid state to another.
    - Isolated: Concurrent transactions do not interfere with each other.
    - Durable: Once a transaction is committed, it will remain so, even if there is a failure, it will not be lost.
- **Redis 7** (Caching Layer)
  - Cache cart data for faster access

#### Message Queue

- **Amazon MSK**
  - Event-driven communication between services
  - Product creation/update events
  - Review submission events
  - Decouples services and enables async processing

#### Storage

- **Amazon S3**
  - Product image storage
  - CloudFront CDN for image delivery
  - Presigned URLs for secure uploads

#### Payment Integration

- **Stripe API**
  - PCI-compliant payment processing
  - Hosted checkout UI
  - Webhook support for payment events

#### Monitoring & Observability

- **Prometheus** (Metrics collection)
- **Grafana** (Metrics visualization)
- **Splunk** (Log aggregation)
- **Heap Analytics** (User behavior tracking)

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
