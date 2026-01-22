# HASHMAPS

Built on top of arrays

The values are stored in an array, but the problem is how to find the index of a value in the array in a fast way

The solution is to use a hash function that maps a certain key to an index in the array

A hash function will always produce the same output for the same input and ALMOST ALWAYS produce different outputs for different inputs [more details in here](./Hashing.md)

The hash function on hashmaps will break down the key into chunks, perform some mathematical operations on those chunks and produce an output that will be used as the index of the array of values

The problem now is that the hash function can produce very large numbers. If we just use it as an index directly, it would need to allocate too much memory for the array.

Other problem is that the hash function can produce the same output for different inputs (collision)
For example, a 32-bit has function can produce a bit over 4 billion (2^32) outputs, but if we use only 7 lowercase characters as the input, it can already poroduce more than 8 billion (26^7) different inputs, which means we are going to have collisions

Solution:

1. Reduce the array size to a manageable number that can fit all the values we want to store
2. Use the modulo (%) operator to map the output of the hash function to the size of the array:

```python
hash = hash_function("john")
index = hash % array.length

array[index] = value
```

This way, the index will always be within the limits of the array size, but we still have collision problems

Let's say for example that the hash function produces the same output for two different inputs:

```python
array.length = 20
hash("john") = 123456
index = 123456 % array.length = 16

array[16] = { id: '1234', name: 'john' }


hash("jane") = 123456
index = 123456 % array.length = 16
# We will have a collision here
```

## Open Addressing

One solution could be checking if the `name` property inside the position is the same as the input key. If not keep looking for the next position until we find an empty position or the value we are looking for `(Linear Probing)`:

```python
index = hash % array.length
for i in range(array.length):
    if array[index] is empty:
        # insert value here
        break
    elif array[index].name == "jane":
        # found the value
        break
```

![open-addressing](./open-addressing.png)

- This approach has some problems like clustering, where a group of consecutive filled positions can make the search very long and slow
- As the load factor (number of elements / size of array) increases, the performance of the hashmap decreases
- If we want to increase the size of the array, we would need to rehash all the elements, because the modulo operation would change the resulting index
- If we try to delete an element, we couldn't just remove it from the array, as it could cause the search for elements that collided with it to fail.

## Closed Addressing (Chaining)

This solution is similar to the Open Addressing. It starts by calculating the position in the array using the hash function and modulo operation:

```python
index = hash % array.length
```

But now, the position in the array will not only store the value, but the head of a linked list that will store all the values that collided in that position:

```
struct HashMapEntry {
  value: any
  next: NextItemIndex
}
```

![collision-chaining](./collision-array.png)

That solves a few problems:

- If there's no space left in the hashmap, we can just add more items to the linked list
- Deleting an item is as simple as removing it from the linked list and updating the pointers

The downside here is that we'll need to allocate more memory for the linked lists and that the performance will degrade as we're now acessing multiple lists to get a value instead of just an array position.


## Multi-thread

What happens when multiple threads try to access/modify a hashmap at concurrently?
Multiple issues can happen:

1. Read/write - if one thread is reading the same datapoint that another is writing to, it could return corrupted, partial data or even crash
2. Write/write - Two threads inserting or modifying the same datapoint can cause conflicts, with overrides and corrupt internal pointers

### Solutions


1. Coarse-grained locking - Will lock the entire map, not allowing any operation in it while another thread operation is being done.
   
    1.2. Mutex (Mutual exclusion): Use a mutex to protect the data. It only allows one thread to access that given data at a time. It will lock the resource while it's being accessed by one thread and release afterwards

2. Fine-grained locking: - Will lock individual buckets that are being modified, instead of the whole map. There's more complexity, but allows concurrent changes on different datapoints

3. Read-write locks - Will distinguish between readers (allowing them to share access) and writters (allowing exclusive access)

