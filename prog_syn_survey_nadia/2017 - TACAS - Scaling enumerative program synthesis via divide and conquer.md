# 2017 - TACAS - Scaling enumerative program synthesis via divide and conquer

The key idea is to employ a divide-and-conquer approach by separately enumerating:

1. ***smaller expressions*** that are correct on subsets of inputs, and 
2. ***predicates*** that distinguish these subsets.

Then, these expressions and predicates are combined using ***decision trees*** to obtain an expression that is correct on all inputs.

## Extensions and Optimizations

1. ***The anytime extension***: due to the lack of sufficiently good predicates, the decision tree and the resulting solution can be large
2. ***Decision tree repair***: store terms and predicates that cover the same subset of inputs
3. ***Branch-wise verfication***: add a premise, i.e., the predicate in that branch, to the solver