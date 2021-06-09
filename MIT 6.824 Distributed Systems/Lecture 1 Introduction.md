[MIT 6.824](https://www.youtube.com/watch?v=cQP8WApzIQQ&list=PLrw6a1wE39_tb2fErI4-WkMbsvGQk9_UB)



# Intro

> why people want to build distributed system?

- Parallelism
- fault tolerance
- physical
- security / isolated



> challenges

- concurrency
- partial failure
- performance



> Assignment

- Lab 1 - MapReduce
- Lab 2 - Raft for fault tolerance
- Lab 3 - K/V server
- Lab 4 - sharded K/V service (切片)



> Infrustructure

- Storage
- Communication
- Computation



> Implementation

RPC, threads, concurrency control

 

> Performance

Salability - 2 * computers -> 2 * throughput (吞吐量)



> Consistency

- Strongly consistent  scheme (expensive)
  - Read both serveal copies and choose the latest one.
  - require high communication

- Weak consistent scheme (people prefer)



----------

# MapReduce

![WeChate3442aa105268517f4f9f790978f08a7](/Users/guohuanjie/Documents/UoM Learning/COMP61342 Computer Vision/blog/未命名.assets/WeChate3442aa105268517f4f9f790978f08a7.png)

**Word count** : The reduce processs just to count the number of value.

```java
Map(k,v)
  split v into word
  for each word
    emit(w,'1')
    
Reduce(k,v)
  emit(len(v))
```



