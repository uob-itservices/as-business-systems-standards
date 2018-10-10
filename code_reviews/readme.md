# Code review

These guidelines are addapted from [Code Review Checklist – To Perform Effective Code Reviews](https://www.evoketechnologies.com/blog/code-review-checklist-perform-effective-code-reviews/).

## Principals

When writing and reviewing code, concider the following points:

1. Am I able to understand the code easily?
2. Is the code written following the coding standards/guidelines?
3. Is the same code duplicated more than twice?
4. Can I unit test / debug the code easily to find the root cause?
5. Is this function or class too big? If yes, does the function or class having too many responsibilities?

##  Checklist

### Code formatting

Does the source code comply to the style guide for the language?

### Architecture

Code conforms with existing code patterns, conventions and practices.

### Best practice

1. No hard coding, use constants/configuration values.
2. Comments – Do not write comments for what you are doing, instead write comments on why you are doing. Specify about any hacks, workaround and temporary fixes. Additionally, mention pending tasks in your to-do comments, which can be tracked easily.
3. Use framework or library features wherever possible instead of writing custom code.

### Non Functional requirements

a. __Maintainability__ (Supportability) – The application should require the least amount of effort to support in near future. It should be easy to identify and fix a defect.

  1. __Readability__: Code should be self-explanatory. Get a feel of story reading, while going through the code. Use appropriate name for variables, functions and classes. If you are taking more time to understand the code, then either code needs refactoring or at least comments have to be written to make it clear.
  2. __Testability__: The code should be easy to test. Refactor into a separate function (if required). Use interfaces while talking to other layers, as interfaces can be mocked easily. Try to avoid static functions, singleton classes as these are not easily testable by mocks.
  3. __Debuggability__: Provide support to log the flow of control, parameter data and exception details to find the root cause easily. If you are using Log4Net like component then add support for database logging also, as querying the log table is easy.
  4. __Configurability__: Keep the configurable values in place (XML file, database table) so that no code changes are required, if the data is changed frequently.
  
b. __Reusability__

  1. DRY (Do not Repeat Yourself) principle: The same code should not be repeated more than twice.
  2. Consider reusable services, functions and components.
  3. Consider generic functions and classes.
  
c. __Reliability__ – Exception handling and cleanup (dispose) resources.

d. __Extensibility__ – Easy to add enhancements with minimal changes to the existing code. One component should be easily replaceable by a better component.

e. __Security__ – Authentication, authorization, input data validation against security threats such as SQL injections and Cross Site Scripting (XSS), encrypting the sensitive data (passwords, credit card information etc.)

f. __Performance__

  1. Use a data type that best suits the needs such as StringBuilder, generic collection classes.
  2. Lazy loading, asynchronous and parallel processing.
  3. Caching and session/application data.
  
g. __Scalability__ – Consider if it supports a large user base/data? Can this be deployed into web farms?

h. __Usability__ – Put yourself in the shoes of a end-user and ascertain, if the user interface/API is easy to understand and use.
If you are not convinced with the user interface design, then start discussing your ideas with the business analyst.

### Actions

* Should the style guide be ammended?
* Should any new libraries be created?
