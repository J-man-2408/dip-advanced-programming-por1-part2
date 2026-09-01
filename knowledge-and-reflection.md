# Overview

These questions are designed to accompany the task "Implementing a Hash Map in Python" in the "Data Structures and Algorithms" module. The questions are intended to test your understanding of hash maps, their implementation in Python, and the process of integrating data from a double linked list into a hash map. You will also be asked to reflect on your learning and the challenges you faced during the task.

# Knowledge questions

The following are all examples of hash functions:

```python
# (1) the simplest hash function (Stupidly Simple Hash)
def ssh(key):
    return 1
```

```python
# (2) hash function that sums the ASCII values of the characters in the key
def sum_of_ascii_values(key: str, size: int) -> int:
    total = 0
    for char in key:
        total += ord(char)
    return total % size
```

A more Pythonic version

```python
# (2a)
def sum_of_ascii_values(key: str, size: int) -> int:
    return sum(ord(char) for char in key) % size
```

A Pearson Hash function

```python
# (3) Pearson hash function
# https://en.wikipedia.org/wiki/Pearson_hashing
import random

random.seed(42)

# This is INCORRECT:
# pearson_table = [random.randint(0, 255) for _ in range(256)]
pearson_table = list(range(256))
random.shuffle(pearson_table)

def pearson_hash(key: str, size: int) -> int:
    hash_ = 0
    for char in key:
        hash_ = pearson_table[hash_ ^ ord(char)]
    return hash_ % size
```

The following is a hash function that uses the built-in `hash` function in Python

```python
# (4) hash function that uses the built-in hash function
def built_in_hash(key: str, size: int) -> int:
    return hash(key) % size
```

Finally, the following is a hash function that uses the `SHA256` hash function from the `hashlib` module

```python
# (5) hash function that uses the SHA256 hash function
# https://docs.python.org/3/library/hashlib.html
# https://en.wikipedia.org/wiki/SHA-2
# https://en.wikipedia.org/wiki/SHA-2#Pseudocode
import hashlib

def sha256_hash(key: str, size: int) -> int:
    return int(hashlib.sha256(key.encode()).hexdigest(), 16) % size
```

1. All of the above functions are hash functions. Explain how so - what key properties do they all share?

> All of the above are hash functions because they take an input key and transform it into a hash value. That can then be used to determine where the key/value should be stored in the hash map. They produce a value that can be mapped to an index within the hash map. However, some are very bad at distribution and have a very high collision rate and some are very slow.

2. What are the advantages and disadvantages of each of the above hash functions? Evaluate in terms of uniformity, determinism, efficiency, collision resistance, sensitivity to input changes, and security[1](#Reference). You may need to do some reasearch to answer this question 😱

> 1. Stupidly Simple Hash - ADVANTAGES: Extremely fast, very simple and deterministic. DISADVANTAGES: Every key produces the same value, terrible uniformity, every key will collide, no security. Kind of pointless really.
> 2. Sum of ASCII values - ADVANTAGES: Simple to understand, fast to calculate, deterministic, different characters can produce different values. DISADVANTAGES: Different keys can produce the same values, meaning collisions are likey and the character order doesn't matter, again leading to poor resistance against collisions.
> 3. Pearson Hash - ADVANTAGES: Good distribution, more sensitive to input changes, better collisions resistance than the ASCII approach. DISADVANTAGES: A little complicated, the implementation requires lookup table to be available and the version here doesn't have cryptographic security.
> 4. Python Built-in `hash()` - ADVANTAGES: Very fast, designed for python so it works well with python data structures, easy to use and has good distribution generally. DISADVANTAGES: Randomisation means string hashes are probably not going to be the same, the distribution depends on Python and it isn't cryptographically secure.
> 5. SHA256 - ADVANTAGES: Excellent distribution, low collision rate, deterministic, fast execution for files. DISADVANTAGES: Slower that simplier hash functions but still faster than other adaptive hashing algorithms.
>    <BR> SOURCES:
>
> - [sha-256 hashing in python](https://stackoverflow.com/questions/48613002/sha-256-hashing-in-python)
> - [Hashing: An Overview of Concepts and Application in Python](https://medium.com/@inandelibas/hashing-an-overview-of-concepts-and-applications-in-python-be3a6d85a5a4)
> - [Pearson Hashing in Python](https://mojoauth.com/hashing/pearson-hashing-in-python)

3. List the three most important attributes (arranged from most to least) in the context of a hash map? Justify your answer.

> 1. Uniformity - I think this would be the most important because a hash function should distribute keys across the available positions as evenly as possible. Poor distribution causes some buckets in chaining to contain way more items then others.
> 2. Collision - Different keys can produce the same hash value but this causes collisions, a good hash functions should minimise this. Fewer collisions generally mean better performance overall.
> 3. Efficiency - A hash map is designed to provide faster access to data. The hash function should calculate the position quickly, otherwise this main benefit is lost.
>    Worth noting that security is very important depending on what you are using it for. This application doesn't particularly need high security or cryptography but if the app did then that would be number one.

4. Which of the above hash functions would you choose to implement the requirements of the task? Why?

> Personally, I would choose Python built-in `hash()` functions because it provides a decent balance between efficiency, uniformity and collisation resistance. It is also already implemented and optimised in Python making it simpler and faster that using SHA256, even though that has better security. For this task I think the good distribution, low collisions and speed are more desirable.

5. In your own words, explain each line in the pearson hash function above in terms of the criteria you listed in question 2.

```python
#Imports the Python random module so that a shuffled lookup table can be created.
import random

# This ensures that the randomisation is repeatable. Makes the table deterministic.
random.seed(42)

# This is INCORRECT:
# pearson_table = [random.randint(0, 255) for _ in range(256)]

#This creates a list containing every integer from 0-255. These values will then be used as the lookup table
pearson_table = list(range(256))
#Randomly rearranges the values in the table. Help distribution of the hash values more evenly
random.shuffle(pearson_table)

def pearson_hash(key: str, size: int) -> int:
    # This creates the inital hash value
    hash_ = 0
    # This processes each character in the key
    for char in key:
        # The `ord(char)` converts the current charater into its numerical ASCII/Unicode value.
        # The `hash_ ^ ord(char)` perfor5ms an XOR operation between the current hash value and the characters numerical value. This causes changes to the input charaters to affect the hash calculation
        # The `pearson_table[...]` uses the XOR result as an index to the shuffled table
        hash_ = pearson_table[hash_ ^ ord(char)]
    # This uses modulo to convert the final hash value into a valid index within the hash map's size.
    return hash_ % size
```

6. Write pseudocode of how you would store Players in PlayerLists in a hash map.

```
Create a hash map with a fixed num of positions
For each Player:
    Get the Player's UID
    Calculate the hash value
    Calculate index = hash value modulo hash map size

    if no PlayerList exisits at index:
        create PlayerList at index

    Insert Player into PlayerList at index
```

## Reflection

1. What was the most challenging aspect of this task?

> Your answer here

2. If you didn't have to use a PlayerList, how would you have changed them implementation of the hash map and why?

> Your answer here

## Reference

### Key Dimensions of Hash Functions

1. **Uniformity**: the probability of any given hash value within the range of possible hash values should be approximately equal.

2. **Determinism**: a given input will always produce the same output.

3. **Efficiency**: the time complexity of computing the hash value should be constant, the hash function should be fast to compute, and utilize the architecture of the computer effectively

4. **Collision Resistance:** minimize the probability of collisions, through a variety of mechanisms.

5. **Sensitivity to input changes:** small changes in the input should produce large changes in the output.

6. **Security**
   - It should be computationally infeasible to find an input key that produces a specific hash value (non-reversibility)
   - The output hash values should appear random and unpredictable.
