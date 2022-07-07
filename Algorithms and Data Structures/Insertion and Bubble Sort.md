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
### The algorithm
For any unsorted list:
- Treat the first element as a sorted list of size 1.
Then, given a sorted list of size $k-1$
- Insert the $k^{th}$ item into the sorted list
- The sorted list is now of size $k$

### Implementation and Analysis
```Java
void insertion_sort(int arr[]){
	int n=arr.length;
	for(int k=1; k<n; ++k){
		for(int j=k; j>0; --j){
			if(arr[j-1]>arr[j]){
				swap(arr, j-1, j);
			}else{
				// so the element is on the correct location. Break loop and handle next element
				break;
			}
		}
	}
}
```
**Explanation:** 
We select an element(`arr[k]`) and compare it with the ordered array elements(`arr: 0~k-1`) from right to left. If the element is smaller, move all the way to the left, and swap the positions of two elements each time until you find an element smaller than it or directly to the end of the array.
![[Insertion Swaps.png|260]]
#### Run-time analysis
The outer loop will be executed `n-1` times. In the worst case, the inner loop will be executed `k` times for each outer loop turn.
So the worse-case run time is $O(n^2)$.

##### swap and inversions
Each time we do a swap, an inversion is eliminated. The average random list has $d=\Theta(n^2)$ inversions.

However, if the inversions $d=O(n)$, the insertion sort will run in $\Theta(n)$ time.

#### Memory analysis
When doing insertion sorting, we just execute swapping, so memory requirement is $\Theta(1)$.

### A Small Improvement-Only assign finally
We know that swapping is expensive, each swap need write to 2 element. So we could just temporarily assign the element to its final position:
```Java
void insertion_sort(int arr[]){
	int n=arr.length;
	for(int k=1; k<n; ++k){
		int val=arr[k];
		for(int j=k; j>0; --j){
			if(arr[j-1]>arr[j]){
				arr[j]=arr[j-1];
			}else{
				// this is the element's correct position.
				arr[j]=val;
				//Break loop and handle next element
				break;
			}
		}
		if(val<arr[0]){
			// if val<arr[0]
			arr[0]=val;
		}
	}
}
```

## Bubble Sort
### The algorithm
Starting with the first item, assume that it is the largest
Compare it with the second item:
- If the first is larger, swap the two,
- Otherwise, assume that the second item is the largest

Continue up the array, either swapping or redefining the largest item. After one pass, the largest item must be the last in the list. And the second pass will bring the second largest element into the second last position.
After repeat $n-1$ times, all entries will be in place.
![[BubbleSortSwap.png|150]]

### Implementation and Analysis
```Java
void bubbleSort(int arr[]){
	int n=arr.length;
	for(int i=n-1; i>0; --i){
		for(int j=0; j<i; ++j){
			if(arr[j]>arr[j+1]){
				swap(arr, j, j+1);
			}
		}
	}
}
```
#### Run-time analysis
There are two nested loops, so the run time:
$$
\sum_{k=1}^{n-1}(n-k)=n(n-1)-\frac{n(n-1)}{2}=\frac{n(n-1)}{2}=\Theta(n^2)
$$

### Implementations and Improvements
Next is some improvements of Bubble sorting algorithm.
1. Reduce the number of swaps
2. halting if the list is sorted
3. limiting the range on which we must bubble
4. alternating between bubbling up and sinking down

#### Reduce swaps
```Java
void bubbleSort(int arr[]){
	int n=arr.length;
	for(int i=n-1; i>0; --i){
		int max=arr[0];          // assum a[0] is the max
		for(int j=1; j<i; ++j){
			if(arr[j]<max){
				arr[j-1]=arr[j]; // move backwards
			}else{
				arr[j-1]=max;    // store the old max back to arr
				max=arr[j];      // get the new max value
			}
		}
		arr[i]=max;              // store the final max
	}
}
```
**Explanation:**
When iterating, we store the max value, if the current value is less than the max, move it one left position. If the current value is bigger than max value, then the current value is the new max value, so store the old max value to the current position. And finally store the max.

#### Flagged Bubble sort
We can check if there is no swaps occur in an iteration.
>If no swap occur, the list is sorted

Use a boolean flag to check if no swaps occurred.
```Java
void bubbleSort(int arr[]){
	int n=arr.length;
	boolean sorted=true; // boolean flag: if the array is sorted
	for(int i=n-1; i>0; --i){
		int max=arr[0];
		for(int j=1; j<i; ++j){
			if(arr[j]<max){
				arr[j-1]=arr[j];
				sorted=false; // turn to false, no sorted
			}else{
				arr[j-1]=max;
				max=arr[j];
			}
		}
		arr[i]=max;
		if(sorted){
			// if no assignment, then it's sorted
			break;
		}
	}
}
```

#### Range-limiting Bubble sort
Each time, we update the loops' range. For example, after the second pass, we are certain all entries after 4 are sorted.
![[RangeLimitingOfBubbleSort.png|200]]
It seems that the operation will greatly speed up the sorting process. But unfortunately, in practice, this does little to affect the number of comparisons.
```Java
void bubbleSort(int arr[]){
	int n=arr.length;
	for(int i=n-1; i>0; ){
		int max=arr[0];
		int iNext=0; // next i position
		for(int j=1; j<i; ++j){
			if(arr[j]<max){
				arr[j-1]=arr[j];
				iNext=j-1; // each time moving, we update the position
			}else{
				arr[j-1]=max;
				max=arr[j];
			}
		}
		arr[i]=max;
		i=iNext; // update next i
	}
}
```

#### Bubbling up and Sinking down!
One operation which does significantly improve the run time is to alternate between
- Bubbling the largest entry from bottom to top, and
- Sinking the smallest entry to the bottom
```Java
void bubbleSort(int arr[]){
	int n=arr.length;
	int lower=0, upper=n-1;

	while(true){
		int newUpper=lower;
		for(int i=lower; i<upper; ++i){
			if(arr[i]>arr[i+1]){
				swap(arr, i, i+1);
				newUpper=i;
			}
		}
		upper=newUpper;
		if(lower==upper){
			break;
		}
		
		int newLower=upper;
		for(int i=upper; i>lower; --i){
			if(arr[i-1]>arr[i]){
				swap(arr, i-1, i);
				newLower=i;
			}
		}
		lower=newLower;
		
		if(lower==upper){
			break;
		}
	}
}
```

### Run time analysis
Because the bubble sort simply swaps adjacent entries, it cannot be any better than insertion sort which does $n + d$ comparisons where $d$ is the number of inversions.

| Case    | Run Time      | Comments                 |
| ------- | ------------- | ------------------------ |
| Worst   | $\Theta(n^2)$ | $\Theta(n^2)$ inversions |
| Average | $\Theta(n+d)$ | Slow if $d=\omega(n)$    |
| Best    | $\Theta(n)$   | $d=O(n)$ inversions      |

They are **all worst than insertion sort in practice.**
