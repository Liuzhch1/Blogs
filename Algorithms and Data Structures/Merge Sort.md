## Merge Sort Algorithm
The merge sort algorithm is defined recursively:
- If the list is of size 1, it's sorted, we are done.
- Otherwise:
	- Divide an unsorted list into two sub-lists,
	- Sort each sub-list recursively using merge sort, and
	- Merge the two sorted sub-lists into a single sorted list

This strategy is called [[Divide and Conquer]].

### Merging two lists
Programming a merge is straight-forward:
- the sorted arrays, `arr1` and `arr2`, are of size `n1` and `n2`.
- we have an empty array, `arrOut`, of size `n1+n2`.
And use three pointer to indicate where we are processing for each array.

```Java
int[] merge(int[] arr1, int[] arr2) {
    int p1 = 0, p2 = 0, k = 0;
    int n1 = arr1.length, n2 = arr2.length;
    int arrOut[] = new int[n1 + n2];
    while (p1 < n1 && p2 < n2) {
        // continue put smaller elements of arr1&arr2 to arrOut
        if (arr1[p1] < arr2[p2]) {
            arrOut[k] = arr1[p1++];
        } else {
            arrOut[k] = arr2[p2++];
        }
        ++k;
    }
    // put rest to arrOut
    while (p1 < n1) {
        arrOut[k++] = arr1[p1++];
    }
    while (p2 < n2) {
        arrOut[k++] = arr2[p2++];
    }
    return arrOut;
}
```

### Analysis of Merging
Time: we copied `n1+n2` elements to `arrOut` array.
- Hence, merging may performed in $\Theta(n1+n2)$ time
- If the two arrays are approximately the same size, $n=n1\approx n2$, we can say that the run time is $\Theta(n)$.

Space: we have to allocate a new array to merge the two array, can't be done in-place. So the memory requirements are also $\Theta(n1+n2)$.

### Implementation
In practice, if the list size is less than a threshold, use an algorithm like insertion sort, which is fast when array is small.

Suppose the function `void merge(int[] arr, int a, int b, int c)` will merge two sorted subarrays `arr[a]~arr[b-1]` and `arr[b]~arr[c-1]` into a single sorted array from index `a` to `c-1`.

`void merge_sort(int[] arr, int first, int last)`:
Sort the entries from position `first<=i` to `last>i`.
- If the number of entries is less than `N`, call insertion sort.
- Otherwise 
	- Find the mid-point,
	- call merge sort recursively on each of the halves, and
	- merge the result

```Java
void mergeSort(int[] arr, int first, int last) {
    if (last - first <= N) {
        insertionSort(arr, first, last);
    } else {
        int mid = (first + last) / 2;
        mergeSort(arr, first, mid);
        mergeSort(arr, mid, last);
        merge(arr, first, mid, last);
    }
}
```

### Run-time Analysis of Merge Sort
The time required to sort an array of size $n>1$ is:
- the time required to sort the first half
- the time required to sort the second half, and
- the time required to merge the two lists
That is:
$$
T(n)=\begin{cases}
\Theta(1)&n=1\\
2T(\frac{n}{2})+\Theta(n)&n>1
\end{cases}
$$
So: $T(n)=\Theta(n\ln(n))$.
![[RecursionTreeOfMergeSort.png|500]]
The worst case and best case has the same run time, they are all $\Theta(n\ln(n))$.

### Idea behind Merge Sort: Divide and Conquer
#### What is divide and conquer?
First, divide up problem into several subproblems(of the same kind).
Second, solve(conquer) each subproblem recursively.
Finally, combine solutions to subproblems into overall solution.

#### Most common usage
1. Divide problem of size `n` into two subproblems of size `n/2`.
2. Conquer two subproblems recursively.
3. Combine two solutions into overall solution.
