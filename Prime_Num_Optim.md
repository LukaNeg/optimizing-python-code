# Finding Prime Numbers

## Summary
Here I explore coding a simple script for finding prime numbers using Python. I try my first attempt without any optimization by creating the script off the top of my head. As you will see, that script works but runs very slowly. I then follow some online lessons on how to optimize the script and get much faster results.

## Prime Finder version 1

Now, here is the code for figuring out if any one number is prime.



```python
# First, here is the empty set we will use to store all the primes:
# Note that I added 1 to it since that will always be on the set
prime_set = {1}
```


```python
# A simple demonstration:
number = int(input("Please enter a number: "))
number_factors = list()
for i in range(2,number,1):
    number_factors.append(int(number % i == 0)) # if evenly divisible, then it's a factor

if sum(number_factors) == 0: # if our number has zero factors...
    print("It's prime!")
else:
    print("Not a prime...")
```

    Please enter a number: 5
    It's prime!


But let's allow the user to get all the prime numbers from 1 to the value they enter:


```python
maximum = int(input("Please enter a number: "))
prime_set = {1}

for j in range(1, maximum+1): #maximum+1 to include the number they entered if prime
    number_factors = list()
    for i in range(2,j,1):
        number_factors.append(int(j % i == 0))
    if sum(number_factors) == 0: #if the number is prime...
        prime_set.add(j) # then add it to the set
# show the set:
print(f"Your prime numbers are: ")
print(prime_set)

```

    Please enter a number: 10
    Your prime numbers are: 
    {1, 2, 3, 5, 7}


Cool! Now turn that into a function:


```python
def find_primes(maximum):
    prime_set = {1}

    for j in range(1, maximum+1): #maximum+1 to include the number they entered if prime
        number_factors = list()
        for i in range(2,j,1):
            number_factors.append(int(j % i == 0))
        if sum(number_factors) == 0: #if the number is prime...
            prime_set.add(j) # then add it to the set
            
    return(prime_set)
```

And now run this:


```python
maximum = int(input("Please enter a number: "))

my_primes = find_primes(maximum)

print(f"Your prime numbers are: ")
print(my_primes)
```

    Please enter a number: 10
    Your prime numbers are: 
    {1, 2, 3, 5, 7}


This seems to work, but let's time how long this takes for larger numbers since it has to search all those numbers and might take a while!


```python
# import the time library to evaluate elapsed time
import time
```


```python
start = time.time()
find_primes(10000)
end = time.time()
elapsed = end - start
print(f"Time elapsed: ",{elapsed})
```

    Time elapsed:  {8.450064897537231}


So, finding all primes between 1 and 10,000 required 8+ seconds! What about 100,000?
start = time.time()
find_primes(100000)
end = time.time()
elapsed = end - start
print(f"Time elapsed: ",{elapsed})
Ok, so after a few minutes I gave up and terminated the kernel. Is there a more efficient way to do this??

## Prime Finder 2.0

Need to optimize the prime number search space with each iteration so that the code doesn't just use brute force and check every single number. For example, we know that even numbers are not prime, so why not remove those first? The following methodology does that, but also removes multiples of every new prime number found.

Essentially we can work in reverse. Rather than finding each number that doesn't have any multiples, work through the list of multiples and remove all numbers from the final list that those multiples can create.

For example, the first prime number is 2, so we can remove all other values from the set that are multiples of 2. The next prime number is 3, so we can remove all other values from the set that are multiples of 3. The next is 5, since the 4 was already removed when the multiples of 2 were removed, and so on. Thus, the search list becomes smaller and smaller!


```python
# First create a set of potential prime numbers (which is the whole range from 2 to max):
# (note: started with 2 since 2 is actually the first true prime number)

# example max value:
maximum = 10

potential_primes = set(range(2,maximum+1))
actual_primes = set({})
```


```python
# Then, since we know the first value on the list is prime, pop that off and add it to
# the actual_primes set:
#this pops the first value from potential and adds it to actual:
prime = potential_primes.pop()
actual_primes.add(prime)

# to create the list of potential multiples to remove, first start at the prime * 2 since
# that's the first potential next number (since 2 is the lowest possible multiple).
# Then go up to the maximum+1 so that the top value is included, and step through that
# range by the value of that prime number:
multiples = set(range(prime*2, maximum+1, prime))

# now update the potential range of prime values by extracting only those where they are 
# different from the list of multiples (essentially removing all of the multiple values):
potential_primes.difference_update(multiples)
```

Now each time you repeat that set of code, you add values to the `actual_primes` set and remove values from the `potential_primes` set, making the search space smaller and smaller.

So let's repeat that process with a while loop:


```python
maximum = 100
potential_primes = set(range(2,maximum+1))
actual_primes = set({})

while len(potential_primes) > 0:#while there are still values left in potential_primes, repeat:
    prime = potential_primes.pop()
    actual_primes.add(prime)
    multiples = set(range(prime*2, maximum+1, prime))
    potential_primes.difference_update(multiples)

print(actual_primes)
```

    {2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37, 41, 43, 47, 53, 59, 61, 67, 71, 73, 79, 83, 89, 97}


Great! Now time to package it up into a function again:


```python
def find_primes_v2(maximum):
    potential_primes = set(range(2,maximum+1))
    actual_primes = set({})

    while len(potential_primes) > 0:#while there are still values left in potential_primes, repeat:
        prime = potential_primes.pop()
        actual_primes.add(prime)
        multiples = set(range(prime*2, maximum+1, prime))
        potential_primes.difference_update(multiples)

    return(actual_primes)
```


```python
maximum = int(input("Please enter a number: "))

my_primes = find_primes_v2(maximum)

print(f"Your prime numbers are: ")
print(my_primes)
```

    Please enter a number: 10
    Your prime numbers are: 
    {2, 3, 5, 7}


Now let's time it again!

## All primes through 10,000:


```python
# all primes up through 10,000:
start = time.time()
find_primes_v2(10000)
end = time.time()
elapsed = end - start
print(f"Time elapsed: ",{elapsed})
```

    Time elapsed:  {0.00568699836730957}


Wow!! **Only 0.006 seconds** compared to 8+ seconds with version 1!

## All primes through 100,000:


```python
# all primes up through 100,000:
start = time.time()
find_primes_v2(100000)
end = time.time()
elapsed = end - start
print(f"Time elapsed: ",{elapsed})
```

    Time elapsed:  {0.03528904914855957}


**Only 0.035 seconds for 100,000 compared to several minutes + with version 1**

## All primes through 1,000,000:

Now for the mega test: search for all prime numbers from one to one million:


```python
# all primes up through 1,000,000:
start = time.time()
find_primes_v2(1000000)
end = time.time()
elapsed = end - start
print(f"Time elapsed: ",{elapsed})
```

    Time elapsed:  {0.39777088165283203}


**Less. Than. A. Second. WOW**


## Conclusion
So, as you can see, code optimization can make all the difference between having time for happy hour or not :P

This was a great experience in learning how to optimize code, and that it is not just about the types of data structures (though I'm sure using sets here helped a ton), it's also about optimizing the algorithm itself so that it does as little reduntant work as possible. I credit Andrew Jones (at Data Science Infinity) for sharing this very elegant but powerful algorithm for cutting through the redundancy and efficiently extracting a list of prime numbers.


```python

```
