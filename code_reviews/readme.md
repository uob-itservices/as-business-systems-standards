# Code review

These guidelines are addapted from [Code Review Checklist – To Perform Effective Code Reviews](https://www.evoketechnologies.com/blog/code-review-checklist-perform-effective-code-reviews/).

## Principals

When writing and reviewing code, consider the following points:

1. Am I able to understand the code easily?
2. Is the code written following the coding standards/guidelines?
3. Is the same code duplicated more than twice?
4. Can I unit test / debug the code easily to find the root cause?
5. Is this function or class too big? If yes, does the function or class having too many responsibilities?

##  Checklist

### Code formatting

- [ ] Does the source code comply to the style guide for the language?

---

### Architecture

- [ ] Code conforms with existing code patterns, conventions and practices.

---

### Best practice

- [ ] 1. No hard coding, use constants/configuration values.
- [ ] 2. Comments – Do not write comments for what you are doing, instead write comments on why you are doing. Specify about any hacks, workaround and temporary fixes. Additionally, mention pending tasks in your to-do comments, which can be tracked easily.
- [ ] 3. Use framework or library features wherever possible instead of writing custom code.

---

### Non Functional requirements

__Maintainability__ – The application should require the least amount of effort to support in the near future. It should be easy to identify and fix a defect.

  - [ ] 1. __Readability__: Code should be self explanatory. Use appropriate names for variables, functions and classes.
  - [ ] 2. __Testability__: The code should be easy to test. Refactor into a separate function if required.
  - [ ] 3. __Debuggability__: Provide support to log the flow of control, parameters and exceptions to find root causes of bugs easily.
  - [ ] 4. __Configurability__: Keep the configurable values separate in. Do not hard code optional values.
  
__Reusability__

  - [ ] 1. DRY (Do not Repeat Yourself): The same code should not be repeated more than twice.
  - [ ] 2. Consider reusable services, functions and components.
  
__Reliability__

- [ ] Exception handling and cleanup resources.

__Extensibility__

- [ ] Easy to add enhancements with minimal changes to the existing code.

__Security__

- [ ] Authentication, authorization, input data validation against security threats such as SQL injection attacks and encrypting sensitive data (passwords, credit card information etc.)

__Performance__

  - [ ] 1. Use a data type that best suits the needs.
  - [ ] 2. Lazy loading, asynchronous and parallel processing.
  - [ ] 3. Caching and session/application data.
  
__Scalability__

- [ ] Consider if it supports a large user base/data? Can this solution scale to meet those demands?

__Usability__

- [ ] Put yourself in the shoes of a end-user and ascertain, if the user interface/API is easy to understand and use.

---

### Outcomes

* Are there any changes that should be made to the solution?
* Process improvement:
   * Should a style guide be amended?
   * Should any new libraries be created?
   * Should any new documentation be created?
