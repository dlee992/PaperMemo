# 2018 - PLDI - Accelerating search-based program synthesis using learned probabilistic models

## Main ideas

1. Enumerate programs in order of ***likelihood*** instead of size
2. ***Probabilistic higher-order grammar*** (PHOG) proposed in ICML’16
   1. **A[context] -> B**, s.t. context is a list of symbols
   2. ***Transfer learning*** enables PHOGs to ***generalize*** well across synthesis problems
   3. ***Pivot PHOG***
   4. 

## Appendix: A* search

- [Wikipedia]: https://en.wikipedia.org/wiki/A*_search_algorithm

- At each iteration of its main loop, A* needs to determine which of its paths to extend. It does so based on the cost of the path and an estimate of the cost required to extend the path all the way to the goal.

## Code repo

- https://github.com/wslee/euphony (Python & C++) based on EUSolver