# Concurrent Queue

This is an implementation of an queue datastructure as a model tested with an producer-consumer workload. It is written in [Promela for the Spin model checker](http://spinroot.com/spin/Man/promela.html). The queue is based on a standard FIFO queue represented with a fixed maximum number of items. This limitation is given, because it is not possible to model dynamic memory in Promela in a simple way. Critical sections are used for the enqueuing and dequeuing operations, which can be enabled with a preprocessor switch `USE_CRITICAL_SECTION`. The algorithm to create mutual exclusion for the critical section is [Dekke's algorithm](https://en.wikipedia.org/wiki/Dekker%27s_algorithm)

### Workload
The `init` process prepares a predefined number of items in the memory. The numbers `0 ... (N - 1)` are set in the corresponding data fields of the items with index `0 ... (N - 1)`. Afterwards both processes `produce()` and `consume()` get started and they try to enqueue/dequeue all the items. 

### Properties
Two properties written in LTL are given in the source file:
- no_item_lost: the amount of items enqueued is equal to the amount of items dequeued
- order_preserved: the order in which the items are dequeued is valid 

## Verifying The Model

Once you installed spin you are ready to check the given model if a given property holds
```
spin [ -DUSE_CRITICAL_SECTION ] -run -ltl [ no_item_lost | order_preserved ] queue.pml
```

If you have found a counter example for a property and got therefore a `queue.trail.pml` file, you can recompute the state of this specific path with:
```
spin -p -t queue.pml
```
