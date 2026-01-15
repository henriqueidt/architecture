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

## Changing IDs problems

1. First and most obvious problem is that changing one entry id will break all other DBs/tables that had a reference to that specific id

- Would need to update every DB that references it

2. Broken URLs/APIs that referenced that id

- Client side bookmarked URLs will now break or even worse, point to the wrong entry
- Same for Search engine indexed URLs
- Other APIs/websites references might break aswell

3. Audit trail loss

- As the logs usually point to ids, they'll now be useless, as the old logs are pointing to the old ID

4. Cache invalidation

- Cache Services like Redis will need to invalidate all the cache related to the updated IDs

### Solutions

1. Never change IDs

- Could be enforced by setting ids with a DB automated generation function like Postgres `uuidv7()` that will generate a V7 UUID with timestamp computed

2. Use a different id for business

- One primary ID that never changes and is used for relations
- One second ID that is used for business, i.e. in URLs

3. Create a separate table to store an alias of old and new id
