# Two Phase Commit

- Used for distributed transactions where you can't run a single ACID operation

The idea is to achieve atomic distributed transactions, where either all participants commit or all rollback, all coordinated by a central coordinator

## Process

### Phase 1
- The coordinator asks each participant to lock resources and return if they can commit
- Each participant locks the resource and replies if can or cannot commit

### Phase 2
- If all participants responded `TRUE`, sends a `COMMIT` command to all
- If any participant responded `FALSE`, sends a `ROLLBACK` command to all


## Tradeoffs

- **Locking** 
  - Resources are locked between phases
  - If the coordinator crashes or one participant takes time to respond, participants are stuck waiting with resources locked.
- **High coupling**
  - All resources must adopt the 2PC protocol
- **Latency**
  - Response must wait for all services requests to be finished


