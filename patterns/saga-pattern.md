# Saga Pattern

- Used to manage distributed transactions across multiple services.
- Useful when you can't use a singe ACID transaction as each service have its own DB
- Each transaction is independant of the other
- Log based
- For each transaction there is a compansating transaction that can revert it

## Implementations

### Choreography
- Services emit events and react to each other
- (+) Decoupled from an orchestrator
- (-) Implicit flow

#### Phase 1
- Service A completes T1 -> publish event

#### Phase 2
- Service B listens T1 event -> completes T2 -> publish event

#### Phase 3
- Service B listens T2 event -> completes T3


### Orchestration
- A centralized orchestrator drives each step
- (+) Centralized control (explicit flow)
- (-) Orchestrator complexity


#### Phase 1
- Orchestrator -> Service A completes T1 -> OK

#### Phase 2
- Orchestrator -> Service B completes T2 -> OK

#### Phase 3
- Orchestrator -> Service C error on T3 -> FAIL

#### Phase 3
- Orchestrator -> Service B undo T2

#### Phase 3
- Orchestrator -> Service A error on T1


## Tradeoffs
- Compensatory transactions can be very complex
- High availability (log based)
- Low coupling
- High scalability