# Ring Buffer and Iterator

[Ring buffer](https://en.wikipedia.org/wiki/Circular_buffer) is a data sturcture that uses a single, fixed size buffer as if it were connected end-to-end.

[TOC]

## Uses

The advantage of ringbuffer is that it doesn't need to have it's elements shift left when removing one element(a non-circular buffer would need to, like vector). So it's well-suited as a FIFO(first-in, first out) buffer like Queue.

## Implementation Overview

- Initial size of the ring buffer is fixed as `RING_BUFFER_INIT_SIZE`.
- When your buffer is full and you need to insert more data, you should increase the capacity of your buffer by factor `RING_BUFFER_GROW_FACTOR1` if the current capacity less than 1024. Else increase the capacity by factor `RING_BUFFER_GROW_FACTOR2`.
- When increasing the buffer size, you need to copy all old data to new allocated space.
- Implement an iterator for your ring buffer. Which should support operation like `=`, `*`, `->`, `==`, `!=`, `++`, `--`, `+`, `-`, `+=` and `-=`.

## Hazard to handle

If the buffer is full or empty, the read pointer and write pointer may point to the same position.

There are multiple ways to solve this problem. Like always keep a storage unit empty, use data count or use **mirror indicator bit**.

We will implement it using the mirror indicator bit.

### Mirror indicator bit

If the buffer length is `n`, the logical address space is 0 to `n-1`. Then, specify `n` to `2n-1` address as the 'mirror logical address space'. So this strategy specifies that the address space of the read pointer and write pointer is 0 to `2n-1`. When the pointer address is bigger than `2n`, let it wrap back to `ptr-2n`. When we want to access the real data in the pointer, we just need to do a modular operation: `data[read_ptr%n]`.

## Function Declarations

We will implement those class and functions with a Template. Assume that type `_Tp` has default constructor and destructor. And it's both copy constructible and assignable.

```C++
template <typename _Tp>
```

### Ring Buffer Class declarations

Define a class `RingBuffer` which has a constructor and a destructor.

```C++
// Create a new ring buffer. Allocate memory for its _M_content, initialize the _M_read_pos,
// _M_write_pos, and its _M_capacity to RING_BUFFER_INIT_SIZE
RingBuffer();

// Destroy the given ring buffer, free all resources you've allocated.
~RingBuffer();
```

Some private field for store the ring buffer data.

```C++
private:
   size_t      _M_capacity;  // The capacity of the ring buffer
   size_t      _M_read_pos;  // The read position of the ring buffer
   size_t      _M_write_pos; // The write position of the ring buffer
   _Tp*          _M_content; // A pointer point to the ring buffer data.
```

A function for get the capacity of the ring buffer.

```C++
// Returns its _M_capacity
size_t get_capacity() const;
```

Check if the ring buffer is empty.

```C++
// Returns true if the given ring buffer is empty, false otherwise
bool is_empty() const;
```

Read a single data from the ring buffer and remove it from the buffer.

```C++
// Read an element from the ring buffer (i.e., the dequeue operation)
// Returns true if the operation succeeded, false otherwise.
bool read(_Tp& data);
```

Write an single data into the ring buffer.

```C++
// Write an element which value is `data` to the ring buffer (i.e., the enqueue operation),
// returns true if the operation succeeded, false otherwise.
bool write(const _Tp& data);
```

Read multiple data from ring buffer and write them into a vector.

```C++
// Read "rdsize" elements from the ring buffer, and fill them to "data".
// Notice that you should only return true when this operation succeeds.
// If there are not enough elements in the buffer, just DO NOTHING and return false.
// DO NOT modify the ring buffer and data for such cases.
bool read_multi(size_t rdsize, std::vector<_Tp>& data);
```

Write multiple data from a vector into the ring buffer.

```C++
// Write "wrtsize" elements to the ring buffer. The values to write are provided in "data".
// Returns true if succeeds, false otherwise.
bool write_multi(size_t wrtsize, const std::vector<_Tp>& data);
```

Apply a function to all data in the ring buffer.

```C++
// For every element in the ring buffer, apply "func" to it.
bool map(std::function<_Tp(_Tp)>&& func);
```

A iterator for travel through the ring buffer. One function for get the begin iterator and one function to get the end iterator.

```C++
// The iterator is used to interate through the ringbuffer.
// By iterating through the ring buffer, it means start from the oldest element in the
// ring buffer (i.e. the element pointed by "_M_read_pos"), to the latest one.
// end() should return the slot after the last element.
iterator begin();
iterator end();
```

And a auxiliary function to grow the capacity of ring buffer.

```C++
private:
    // Grows the ringbuffer _M_capacity according to the growth factor
    void grow();
```

In order for the iterator to access the private data of the ring buffer, we need to put iterator class declarations inside `RingBuffer` class as a `friend class`.

```C++
private:
    friend class __detail::__iterator<_Tp>;
    friend class __detail::__const_iterator<_Tp>;
```

### Iterator Class declarations and implementation

Define a class `__iterator` which has two private field:

1. `_Tp *p`: point to the data where the iterator is.
2. `RingBuffer<_Tp> *rb`: a pointer point to the ringbuffer instance

And `__iterator` class has three constructor and one destructor. `__detail` is a namespace define in head file as:

```C++
namespace __detail{
    // Iterator of RingBuffer
    template <typename _Tp>
    class __iterator; 
}
```

```C++
class __detail::__iterator{
private:
    _Tp *p;
    RingBuffer<_Tp> *rb; // ringbuffer instance
public:
    // Default Constructor
    __iterator() {
        p = nullptr;
        rb = nullptr;
    }

    // Copy Constructor
    __iterator(const __iterator &other) {
        p = other.p;
        rb = other.rb;
    }

    // Pointer Constructor, find the *ptr in the buffer and init the iterator with it.
    __iterator(_Tp *ptr, RingBuffer<_Tp> *buffer) {
        __iterator<_Tp> tmp;
        p=buffer->_M_content;
        rb=buffer;
        // find the *ptr pointed position
        while (tmp.p != ptr)
            tmp++;
    }

    // Destructor
    ~__iterator() = default;
}
```

**copy assignment operator: `=`** Just assign `p`. Because `=` must operated of two iterator of the same ring buffer, so no need to assign `rb`.

```C++
    __iterator &operator=(const __iterator &other) {
        // self-assignment check
        if (this == &other)
            return *this;
        delete p;
        // assign new iterator
        p = other.p;
        return *this;
    }
```

**dereference operator: `*`** Return the data the iterator point to. So just dereference pointer `p`.

```C++
    _Tp &operator*() {
        return *p;
    }
```

**arrow pointer: `->`** Just return the `p` pointer.

```C++
    _Tp *operator->() {
        return p;
    }
```

**comparison operator: `==`,`!=`** Compare if two iterator is equal or not. Because the iterator point to same ring buffer, so just compare `p`. So does not equal.

```C++
    // == comparison operator
    bool operator==(const __iterator &other) const {
        return p == other.p;
    }
    // != comparison operator
    bool operator!=(const __iterator &other) const {
        return p != other.p;
    }
```

**self increment: `++`** There are two kind of `++`: prefix `++it` and postfix `it++`. `++it` return the iterator after self increment. While `it++` return the orign iterator. Both them do self increment.
Note the difference between the function declarations of prefix(`__iterator &operator++()`) and postfix(`__iterator operator++(int)`). They are different so that compiler can distinguish them, that parameter `int` in postfix is just for distinguish.

```C++
    // prefix self increment
    __iterator &operator++() {
        p += 1;
        // If the pointer reach the end of ringbuffer, jump to head(so that it works like a cicle).
        if (p == rb->_M_content + rb->_M_capacity)
            p = rb->_M_content;
        // return this interator itself
        return *this;
    }

    // postfix self increment
    __iterator operator++(int){
        // get the current iterator for return
        auto tmp= __iterator(*this);
        // self increment this iterator
        ++*this;
        // return a 'not' increased iterator.
        return tmp;
    }
```

**self decrement: `--`** There are two kind of `--`: prefix `--it` and postfix `it--`, just like `++`.

```C++
    // prefix self decrement
    __iterator &operator--(){
        if(p==rb->_M_content){
            // if in the beginning, go to the end.(like a circle)
            p=rb->_M_content+rb->_M_capacity-1;
        }else{
            // not in the beginning, just minus 1
            p=p-1;
        }
        return *this;
    }

    // postfix self decrement
    __iterator operator--(int){
        // get the current iterator for return
        auto tmp= __iterator(*this);
        // self increment this iterator
        --*this;
        // return a 'not' increased iterator.
        return tmp;
    }
```

**arithmetic operation: `+` and `-`** Plus a constant or minus a constant to the iterator, that operation is equal to do self increment or decrement multiple times. Be careful the constant maybe a negative number.

```C++
    // + operator
    __iterator operator+(size_t n) const{
        // if n<0, equal to call -(-n)
        if(n<0)
            return this->operator-(-n);
        __iterator<_Tp> tmp(*this);
        // do self self increment n times.
        while(n--)
            ++tmp;
        return tmp;
    }

    // -operator
    __iterator operator-(size_t n) const{
        // if n<0, equal to call +(-n)
        if(n<0)
            return this->operator+(-n);
        __iterator<_Tp> tmp(*this);
        // do self decrement n times
        while(n--)
            --tmp;
        return tmp;
    }
```

**compound assignment operator: `+=` and `-=`** The operation is equal to `it=it+constant` or `it=it-constant`.

```C++
    __iterator &operator+=(ptrdiff_t n){
        *this = *this + n;
        return *this;
    }

    __iterator &operator-=(ptrdiff_t n){
        *this = *this - n;
        return *this;
    }
```

---

**Problem Requirement and Description:** [CS110 Homework7](https://robotics.shanghaitech.edu.cn/courses/ca/22s/hw/7/)
