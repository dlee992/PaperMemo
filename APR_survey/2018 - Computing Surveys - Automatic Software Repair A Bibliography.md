# 2018 - Computing Surveys - Automatic Software Repair A Bibliography

- **bug** is used as an umbrella word for **faliure**, **error**, and **fault**
- An **oracle** is only a part of **specifications**; it’s the part related to the expected output

## Behavioral repair

> Behavioral repair consists of changing the behavior of the program under repair, i.e., changing its code. The modification can be done on source code but also on binary code (e.g. Java bytecode or x86 native code). Behavioral repair can be done offline or online at runtime.

​	Ordered by kinds of *oracles*:

1. Repair and oracles
   1. Test suites
   2. Pre- and Post-conditions
   3. Abstract behavioral models
2. Static analysis
3. Crashing inputs
4. Other oracles
5. Domain specific repair
6. Fault classes and repair (*Coccinelle*)

## State repair

> State repair consists of changing the state of the program under repair. The state is meant in its largest acceptation: It can be changing the input, the heap, the stack, or the environment. For instance, automatic breaking a cycle in a linked list is one kind of state repair. As opposed to behavioral repair, state repair is necessarily done at runtime.

Ordered by *repair operators*:

1. Reinitialization and restart
2. Checkpoint and rollback
3. Alternatives
4. Reconfiguration
5. Input modification
6. Environment perturbation
7. Rollforward
8. Collaborative repair

## Empirical knowledge on repair

## Related techniques

1. Forward engineering for repair
2. Repair suggestions
3. Theoretical software repair