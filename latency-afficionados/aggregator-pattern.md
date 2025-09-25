# Aggregator Pattern

- Responsible for aggregating data from multiple services into a single response.
- Usually implemented as a separate service that stays between the client and the other microservices.

- (+) Reduces number of requests made by the client to multiple services.
- (+) Can apply business logic before sending data to the client.
- (+) Avoids dependency between services.
- (-) Adds an extra layer of complexity to the architecture.
- (-) Can become a bottleneck if not properly scaled.
- (-) Can increase latency if not correctly set up.

## Simple Aggreagator

- Simply fetches data from multiple services and aggregates them into a single response.
- Does not make any transformation or business logic to the data.

## Complex Aggregator

- Fetches data from multiple services and applies business logic to the data before aggregating them into a single response.
- Can also transform the data into a different format before aggregating them.


## Scatter-Gather Pattern

- Sends out requests to multiple services in parallel and merges all the responses into one.
- Used when each service is independent and does not need data from other services.

## Chained Pattern

- When the response from one service is used on the request for the other service.
- Used when there is a dependency between data from the services.

## Branch Pattern

- Similar to the Chained Pattern, it can create branches to multiple services based on the response from the first service.
- Used when there are multiple dependencies between data from the services.