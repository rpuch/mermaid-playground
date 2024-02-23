# Normal cases

## Embedded mode

### KV get (embedded mode) on partition colocated with current node

```mermaid
sequenceDiagram
participant View as KVView
participant InternalTable
participant RepService
participant MsgService
participant RepManager
participant PRL as PartitionReplicaListener

View ->> RepManager: User thread
RepManager ->> PRL: partition-operations
PRL ->> PRL: partition-operations
PRL ->> View: partition-operations
```

### KV put (embedded mode) on partition colocated with current node

```mermaid
sequenceDiagram
participant View as *KVView
participant InternalTable
participant RepService
participant MsgService
participant RepManager
participant PRL as PartitionReplicaListener

View ->> RepManager: User thread
RepManager ->> ActionRequestProcessor: partition-operations
ActionRequestProcessor ->> NodeImpl: JRaft-Request-Processor
NodeImpl ->> LogManager: JRaft-NodeImpl-Disruptor
LogManager ->> PartitionListener: JRaft-LogManager-Disruptor
PartitionListener ->> PartitionListener: JRaft-FSMCaller-Disruptor
PartitionListener ->> PRL: JRaft-FSMCaller-Disruptor
PRL ->> PRL: partition-operations
PRL ->> View: partition-operations
```

### SQL get by PK (embedded mode) on partition colocated with current node

```mermaid
sequenceDiagram
participant SQLProc as SqlQueryProc
participant ParserService
participant PrepareService
participant ExecutionService
participant KVGetPlan
participant InternalTable
participant RepService
participant MsgService
participant RepManager
participant PRL as PartitionReplicaListener

SQLProc ->> ParserService: User thread
ParserService ->> PrepareService: sql-execution-pool
PrepareService ->> PrepareService: sql-planning-pool(X)
PrepareService ->> KVGetPlan: sql-planning-pool(Y)
KVGetPlan ->> RepManager: tableManager-io
RepManager ->> PRL: partition-operations
PRL ->> PRL: partition-operations
PRL ->> SQLProc: partition-operations
```
