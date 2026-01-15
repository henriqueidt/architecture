## How to create relations between databases, like in this problem: product vs review DB

1. Use Amazon MSK (Kafka) to keep them consistent:

- Product service deletes a product
- Publishes event to MSK
- Review service deletes related reviews

2. Validate product existance on Review Service

- Review service attempt to add a review
- Call Product service to check the product exists
- Add / or not the review entry

3. Soft deletes

- Instead of removing entries from the Product DB, only set a flag `deleted` to `true`
- Make all opeartions on Product DB operate over a `VIEW` that only includes non-deleted products
