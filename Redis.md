In theoretical computer science, the CAP theorem, also named Brewer's theorem after computer scientist Eric Brewer, states that any distributed data store can only provide two of the following three guarantees:

Consistency  
Every read receives the most recent write or an error.

Availability  
Every request receives a (non-error) response, without the guarantee that it contains the most recent write.

Partition tolerance  
The system continues to operate despite an arbitrary number of messages being dropped (or delayed) by the network between nodes.
When a network partition failure happens, it must be decided whether to cancel the operation and thus decrease the availability but ensure consistency or to
proceed with the operation and thus provide availability but risk inconsistency.
Thus, if there is a network partition, one has to choose between consistency and availability. Note that consistency as defined in the CAP theorem is quite different from the consistency guaranteed in ACID database transactions.
