# design-princples

## Code locality
- locate code used together in the same file if possible
- if not possible prefer the same package

## Packages are for solving one particular problem
- Try to limit the scope of a package to one problem, for instance "items" related code would naturally form a package, including what is currently in api and db
- This makes it easier to find and reason about code related to a particular domain
- The package forms an inituitive namespace so that naming becomes easier, eg `items.Validate` cannot be confused with `users.Validate()`
- Packages make a clearer namespace than a struct

## Prefer longer functions to fragmentation
- Highly fragmented code is hard to read, small functions break the flow of the reader, especially when the function is far away and a comment can have the same effect as the function name without affecting the reader's flow.

## Prefer standards to custom wrappers
- Try to use more standard objects as they have more predictable behaviour, augment their behaviour by calling custom functions

## Use interfaces sparingly
- In most cases you can use a function from a package to achieve what we have done with an interface.
- Where we need generalisation we can pass a function as an argument to another function.

# Explicit inputs and outputs
## Function side effects should be visible, eg if changing an attribute on a struct
```Go
item.Name = functionNameHere(inputObject)
```
makes it clearer than Name is being adjusted, than inside a struct's method with no obvious return:
```Go
item.functionNameHere(inputObject)
```
## Pass only the data we need to a function
```Go
item.Name = functionNameHere(organisationPrefix, Name)
```
Rather than
```Go
item.Name = functionNameHere(largeConfigObject)
```

# Unit test logic
- Unit testing IO is not particularly realstic and can easily diverge from reality and give false positives, as well as being more code to write and maintain
- Unit testing logic gives confidence on tough to understand processing

# Integration testing the API
- Docker compose env started prior to running tests
- Tests hit as close to real env as we can
- Tests are quick as no network involved as all local in Docker
- Stateful tests need set up and tare down steps which can be automated, eg DELETE, PUT
- Non-Stateful tests could be done against a fixed set of data, eg GET

# Confidence driven testing
- Unit or integration tests are most valuable where something is critically important or it is non-trivial to validate something is correct
- Conversely if we accumalate tests which don't really give us real confidence, we just burden ourselves with fragile test code to maintain



 
