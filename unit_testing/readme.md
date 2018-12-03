# Unit Testing

Not all code is suitable for unit testing, but where possible unit tests should be created for all code that we create. 

For PL/SQL we use the [UTPL/SQL unit testing framework](utplsql.md) to create and run unit tests.

At a high level, a good definition of Unit Testing would be:

>Unit testing refers to the practice of testing certain functions and areas – or units – of our code. This gives us the ability to verify that our functions work as expected. That is to say that for any function and given a set of inputs, we can determine if the function is returning the proper values and will gracefully handle failures during the course of execution should invalid input be provided.

For example, if a function was to be created that requires a id number and returns information about applications the student has made to the university, it is possible to test this unit of functionality to make sure that: 

1. The returned information is in the correct format.
2. The application information returned is correct for given inputs.
3. The function adequately handles incomplete/erroneous input types such as null values and invalid id numbers. 

By using a unit testing framework such as [junit](https://junit.org/junit5/) for Java, or [utpl/sql](http://utplsql.org/) for Oracle pl/sql, these tests can be automated and repeated. 

The **Test Driven Development** approach requires failing unit tests to be created before any code is written, and implementation will be a case of creating the code to pass these tests. 

The advantages of unit testing are:

* **Improved Regression Testing**. When creating new functionality, developers can test existing code to make sure that new code has not negatively affected other areas of the development. Unit tests can also be run on upgrade of applications to make sure that custom developments are not affected. 

* **Less Coupled Code**. Creating effective unit tests requires developers to write code in logical, self contained areas. Therefore, the act of creating unit tests also encourages us to create less coupled, more robust code. 

However, there are some things to be aware of:

* **It does not confirm if a application actually works as required**. Functional testing, which includes end-user interface testing, involves testing whether the entire development does what it is supposed to. Even a comprehensive set of unit tests will only show that the individual program units that make up a development work as specified. They will not show that all of the program units work together to do what is required.   

* **More time is required during development to create unit tests**. Creating unit tests adds additional time during development, but will  reduce the time required to complete internal code reviews and regression testing.    


This documentation has been paraphrased from / inspired by the following style guides:

* [https://docs.microsoft.com/en-us/dotnet/core/testing/unit-testing-best-practices](https://docs.microsoft.com/en-us/dotnet/core/testing/unit-testing-best-practices)
* [https://code.tutsplus.com/articles/the-beginners-guide-to-unit-testing-what-is-unit-testing--wp-25728](https://code.tutsplus.com/articles/the-beginners-guide-to-unit-testing-what-is-unit-testing--wp-25728)
