# Unit Testing

Unit testing is a method of testing components of software for correct behaviour.

Say you want to test if a particular function works as expected. You come up with a particular set of inputs and know what the expected output is. Then you call the function, giving it those inputs and saving the actual output. Then you compare the actual output to the expected output. If you do this for a few different test cases and they all work, you can be reasonably confident the function works. This also applies if you want to check some condition or state of the program before and after calling a function, even if it is not a return value of the function.

Output values are compared to the expected values using assertions, which describe the expected behaviour. You can assert that some value is true or false, that two values are equal or not equal, etc.

Say in the future, you go back and modify a function, perhaps to make it more efficient or to restructure your program. If you wrote your unit tests properly, as long as your newly implemented function passes all the unit tests, you should be confident the function works.


## Writing Good Unit Tests

- Your tests should not only cover cases where an operation succeeds, it should also check cases where an operation should fail (e.g. invalid message, array/queue is full, passed a NULL pointer)
- Try doing multiple operations in different orders, not always in the same sequence


## Links

[Our unit testing framework](https://utat-ss.readthedocs.io/en/master/software-tools/test-harness.html)

[Excellent unit test example](https://github.com/HeronMkII/lib-common/blob/master/harness_tests/queue/main1.c)
