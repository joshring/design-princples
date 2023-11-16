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

# Explicit inputs and outputs
## Function side effects should be visible, eg if changing an attribute on a struct
```Go
item.Name = functionNameHere(inputObject)
```
makes it clearer than Name is being adjusted by than function than:
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

# Use interfaces sparingly
- In most cases you can use a function from a package to achieve what we have done with an interface.
- Where we need generalisation we can pass a function as an argument to another function.

 
