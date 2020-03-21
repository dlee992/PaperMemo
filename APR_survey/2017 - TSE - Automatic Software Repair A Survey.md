2017 - TSE - Automatic Software Repair A Survey

- *in-house* to fix a buggy program with observable failures in some test cases.

## Localization

1. **Fault localization**: identifying the faulty statements
   - **Spectrum-based fault localization** (SBFL)
   - Rank program elements by the faulty likelihood
   - Four typical kinds
     1. GenProg (32%)
     2. Tarantula (18%)
     3. Ochiai (18%)
     4.  **Jaccard** (5%) (best support automatic program repair techniques)
2. **Fix locus localization**: detecting the statements where the effect of the fault can be observed and eliminated
   1. **model-based** fix locus localization: finite state machines that represent the object life-cycle
   2. **angelic** fix localization: targets faults that can be repaired by modifying the **if conditions**
      - existing: localize the suspicious conditions
      - non-existing: detect missing conditions

- Both are defined **ad-hoc**, coherently with repair techniques

## Fix Generation

- Approximation

### Generate-and-validate 

- **unsound and incomplete**, thus need validation based on test cases

- generation strategy **X** change model **X** fault model (general or specific)

- static analysis techniques (e.g., taint analysis) can be applied in appropriate situations

- |                                                   | atomic change | pre-defined template | example-based template |
  | ------------------------------------------------- | ------------- | -------------------- | ---------------------- |
  | Search-based                                      |               |                      |                        |
  | Brute-force (explore search space systematically) |               |                      |                        |

### Semantics-driven

- **unsound but complete**, thus need no validation based on test cases again

- address **specific faults** rather than being general

- A key enabler: **program synthesis**

- | Fault model                              | Change model                                               | Techniques                               | Characteritics                         |
  | ---------------------------------------- | ---------------------------------------------------------- | ---------------------------------------- | -------------------------------------- |
  | general                                  | snythesis of new expressions                               | SemFix, DirectFix, Angelix, SearchRepair | symbolic execution & program synthesis |
  | wrong conditions & missing preconditions | condition change & if condition insertion                  | NOPOL, ...                               |                                        |
  | concurrency faults                       | critical region manipulation, parallelization keyword move |                                          |                                        |
  | HTML generation faults                   | string modification                                        |                                          |                                        |
  | string sanitization                      | insertion of checks, string modification                   |                                          |                                        |
  | access control violations                | insertion of role checks                                   |                                          |                                        |
  | memory leaks                             | insertion of *free()* statements                           |                                          |                                        |

### Fix recommenders

### Empirical evidence

### Open challenges