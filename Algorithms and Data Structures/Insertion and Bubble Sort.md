## Introduction to Sorting
### What is sorting?
Sorting is the process of **Taking a list of objects which could be stored in a linear order**.
$$
(a_0,a_1,...,a_{n-1})\Rightarrow (a_0^{'},a_1^{'},...,a_{n-1}^{'})
$$
such that $a_0^{'}\leq a_1^{'}\leq...\leq a_{n-1}^{'}$.
A conversion of an Abstract List into an Abstract Sorted List.

### Assumption
In the sorting topics, we will assume that:
- We are sorting Integers.
	- The algorithm can also be applied to any other type that can be compared.
- Arrays are used for input and output for those sorting algorithms.

### What is In-place Sorting?
A sorting algorithm is called **in-place** sorting when the allocation of addition memory is $\Theta(1)$ (that means fixed number of local variables).

### What is Stability of Sorting Algorithm?
The stability of a sorting algorithm is concerned with how the algorithm treats equal(or repeated) elements.
**Stable** sorting algorithms preserve the relative order of equal element, while **unstable** sorting algorithms don't.

e.g.
Sort: `3,_1,2,4,1_`, where `_1` and `1_` are equal element, we use underline to distinguish them.
A stable sorting result: `_1,1_,2,3,4`
A unstable sorting result: `1_,_1,2,3,4`

### Run time
There are three categories of sorting algorithms:
$\Theta(n)$,    $\Theta(n\ln(n))$,    $O(n^2)$

#### Lower-bound of Run-time
Any sorting algorithm must examine each entry at once so that any sorting algorithm must be $\Omega(n)$.

#### Non-best-sorting algorithm
There is no optimal sorting algorithm which can be used in all places.
In various circumstance, different sorting algorithms will reach different run-time and memory-allocation requirements.

#### A sub-optimal sorting algorithm: Bogosort
Let's consider the Bogosort algorithm:
1. Randomly order the objects
2. Check if they're sorted, if not, go back to Step 1.

Run time:
- best case: $\Theta(n)$
- average: $\Theta((n+1)!)$
- worst: unbounded

## Insertion Sort
