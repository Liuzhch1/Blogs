# Stack ADT(abstract data type)
## Introduction of Stack
Normally, mathematics is written using what we call *in-fix* notation:
$$(3+4)\times 5-6$$
Any operator is placed between two operands. The advantage is that it's intuitive enough for human, the disadvantage is obvious. When the mathematics formula is too long, it's full of parentheses and is difficult to find where to start calculation, both for people and computers.

Alternatively, we can place the operands first, followed by the operator.
$$3\ \ 4\ \ +\ 5\ \ \times\ 6\ \ -$$
Parsing reads left-to-right and performs any operands on the last two operands.
$$\begin{aligned}
3\ \ 4\ \ +\ 5\ \ \times\ 6&\ \ -\\
7\ \ \ \ \ \ \ \ \ \ 5\ \ \times\ 6&\ \ -\\
35\ \ \ \ 6&\ \ -\\
29&
\end{aligned}$$
This is called ***Reverse-Polish*** notation after the mathematician Jan Łukasiewicz.

Its advantage is that no ambiguity and no brackets are needed. And it's the same process used by a computer to perform computations:
> operands must be loaded into register before operations can be performed on them

*LeetCode:* [150. Evaluate Reverse Polish Notation](https://leetcode.cn/problems/evaluate-reverse-polish-notation/)

The easiest way to parse reverse-Polish notation is to use an operand ***stack***. So what is a stack?

### What is a stack?
Stack is a *last-in-first-out*(LIFO) data structure. It's like a pile of plates. Every time you take the plate, you take the top. After washing the plate, you also put the plate on the top.

#### Deal with Reverse-Polish Notation
Operands are proceeded by pushing them onto the stack.
When processing an operator:
1. pop the last two items off the operand stack
2. perform the operation, and
3. push the result back onto the stack

Example: how do we evaluate `1 2 3 + 4 5 6 × - 7 × + - 8 9 × -` using a stack:
![[stack_process.png]]
The equivalent in-fix notation is:
$$((1-((2+3)+((4-(5\times 6))\times 7)))+(8\times 9))$$
reduce the parentheses using order-of-operations:
$$1-(2+3+(4-5\times 6)\times 7)+8\times 9$$

# Implementation of Stack
We have 5 main operation of stack, they are:
- `Push`: insert an object onto the top of stack
- `Pop`: erase the object on the top of the stack
- `IsEmpty`: determine if the stack is empty
- `IsFull`: determine if a stack is full
- `CreateStack`: generate an empty stack

We expect the optimal asymptotic run time of above operations is $\Theta(1)$, which means that the run time of this algorithm is independent of the number of objects being stored in the container.

## Linked-List Implementation
Operations at the front of a single linked list are all $\Theta(1)$.
![[LinkedList.png]]
The desired behavior of an Abstract Stack may be reproduced by performing all operations at the front.

#### push_front(int)
```C++
void List::push_front(int n){
	list_head = new Node(n, list_head)
}
```

#### pop_front()
```C++
int List::pop_front(){
	if( empty() ){
		throw underflow();
	}
	int e = front();
	Node *ptr = list_head;
	list_head = list_head->next();
	delete ptr;
	return e;
}
```
![[pop_front.png]]

## Array Implementation
For one-ended arrays, all operations at the back are $\Theta(1)$. But the insert at the front or pop at front takes $\Theta(n)$ to move all element one place backwards.

#### top()
If there are $n$ objects in the stack, the last is located at index $n-1$.
```C++
template <typename Type>
T Stack<T>::top() const{
	if( empty() ){
		throw underflow();
	}
	return array[stack_size-1];
}
```

#### pop()
Remove an object simply involves reducing the size. After decreasing the size, the previous top of the stack is now at the location `stack_size`.
```C++
template <typename Type>
T Stack<T>::pop() const{
	if( empty() ){
		throw underflow();
	}
	stack_size--;
	return array[stack_size];
}
```

#### push(obj)
Pushing an object can only be performed if the array is not full
```C++
template <typename Type>
void Stack<T>::push(T const &obj) const{
	if( stack_size == array_capacity ){
		throw overflow();
	}
	array[stack_size] = obj;
	stack_size++;
}
```

### Array Capacity
When the array is full, how to increase the array capacity?
So the question is:
> How must space will be increased? By a constant or a multiple?

First, let see how an increase works. 
Firstly, require a call to new memory: `new T[N]` where `N` is the new capacity.
![[new_space.png|200]]
Next, copy old data to new space:
![[copy_oldData.png|200]]
The memory for original array must be deallocated:
![[delete_oldData.png|200]]
Finally, the pointer should point to new memory address:
![[reassign_pointer.png|200]]

Now, back to the original question: How much do we change the capacity?
We recognize that any time we `push` onto a full stack, this requires $n$ copies and the run time is $\Theta(n)$. Therefore, push is usually $\Theta(1)$ except when new memory is required.

To state the average run time, we introduce the concept of amortized time:
>If `n` operations requires $\Theta(f(n))$, we will say that an individual operation has an amortized run time of $\Theta(f(n)/n)$.

Using this concept, if inserting `n` objects requires:
- $\Theta(n^2)$ copies, the amortized time is $\Theta(n)$
- $\Theta(n)$ copies, the amortized time is $\Theta(1)$, that is what we expect.

##### Capacity increase 1 analysis
If we increase the capacity by 1 each time the array is full. With each insertion when the array is full, its requires all entries to be copied.
![[capacity_plusOne.png|400]]
Suppose we insert $k$ objects
- The pushing of the $k^{\text{th}}$ object on the stack requires $k-1$ copies
- The total number of copies is:
	$$\sum_{k=1}^{n}(k-1)=\left(\sum_{k=1}^{n} k\right)-n=\frac{n(n+1)}{2}-n=\frac{n(n-1)}{2}=\Theta\left(n^{2}\right)$$
- So the amortized number of copies is:
	$$\Theta\left(\frac{n^2}{n}\right)=\Theta(n)$$
- Therefore each push run in $\Theta(n)$ time
- The wasted space, however is $\Theta(1)$

##### Capacity become double size analysis
Suppose we double the number of entries each time the array is full.
Now the copy time become significantly fewer.
![[capacity_double.png|400]]
- Increase $n$ objects would require 1, 2, 4, 8, ..., all the way up to the largest $2^k<n$ or $k=\lfloor \lg(n)\rfloor$
$$\begin{aligned}
\sum_{k=0}^{\lfloor\lg (n)\rfloor} 2^{k} &=2^{\lfloor\lg (n)\rfloor+1}-1 \\
& \leq 2^{\lg (n)+1}-1=2^{\lg (n)} 2^{1}-1=2 n-1=\Theta(n)
\end{aligned}$$
- Therefore the amortized number of copies is $\Theta(1)$
- The wasted space, however is $O(n)$

| Increase Method | Copies per Insertion | Unused Memory |
| --- | --- | --- |
| Increase by 1 | $n-1$ | 0 |
| Increase by $m$ | $n/m$ | $m-1$ |
| Increase by a factor of 2 | 1 | $n$ |
| Increase by a factor of $r>1$ | 1/$(r-1)$ | $(r-1)n$ |

# Example Applications
Most parsing uses stacks.
Examples includes:
- Matching tags in XHTML
- In C++, matching parentheses $\textbf{(...)}$, brackets $\textbf{[...]}$ and braces $\textbf{\{...\}}$

# Exercise Problems

## Quick Exercises
[32. Longest Valid Parentheses](https://leetcode.cn/problems/longest-valid-parentheses/)
[42. Trapping Rain Water](https://leetcode.cn/problems/trapping-rain-water/)
[84. Largest Rectangle in Histogram](https://leetcode.cn/problems/largest-rectangle-in-histogram/)
[155. Min Stack](https://leetcode.cn/problems/min-stack/)
