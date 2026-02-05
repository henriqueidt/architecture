# Compare and Swap (CAS)

Compare and swap is one possible solution to multithreads accessing the same data, without using locks

The `compare_and_swap` function receives 3 parameters:

- location (where the value should be updated)
- old_value (what is the old value to be changed)
- new_value (the new value to replace old_value)

```JS
  function compare_and_swap(location, old_value, new_value) {
    // Checks if the value in the position is still the same as old_value
    current_value = read(location)
    if(current_value === old_value) {
      // if the value at the location is still old_value, replaces with new value
      *location = new_value
    }

    // it returns whatever value was found in the location. So it's up to the consumer to check:
    // 1. if the returned value is the same as old_value - the operation was successful
    // 2. if the returned value is different, it means that another thread has updated it after I read, therefore I need to call the compare_and_swap again or abort.
    return current_value;
  }
```

- Most of the modern kernels and hardwares have their own `compare_and_swap` implementations
- `compare_and_swap` is guaranteed to be atomic. While the `compare_and_swap` routine is happening, no other thread can access the location. This is a low level atomicity architecture.

- It's good in a way that it doesn't require lockings
- It's bad in a way that a thread can be retrying for a long time if some data is highly modified
