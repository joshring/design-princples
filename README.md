# Formatting

Let's start with an example and we'll see what this gives us:

```go
// FunctionName adds RefreshToken to the database
func FunctionName(
    db *sql.DB,
    timeNow time.Time,
    refreshToken *RefreshToken,
    session *HttpSession,
    sessionEncryptionKey []byte,
) error {

    // Function body
    if session == nil || session.Data.Secret == "" {
        return errors.New("missing session secret")
    }
    ...
    return nil
}
```
## Each line has unique information

- Function docstring
- Function name
- Function arguments
- Function return type
- Line break to distinguish function signature from function body
- Function body
- Function return

# Function Naming

Follow current Go standard using standard camel case for whole name including acronyms, for example:

`FunctionNameApi`, `FunctionNameFindById`, `AuthOtp` 

As opposed to:

`FunctionNameAPI`, `FunctionNameFindByID`, `AuthOTP`

Since they read easier in larger names


## Database functions

`{FunctionName}UsingDb`

For example:

`FindRefreshTokenByIdUsingDb`



## Api functions


`{FunctionName}{HttpMethod}Api`


For example:

`UsersGetApi`

`UsersPostApi`

`UsersDeleteApi`

## Calling Functions

Split lines for args when more than 3 arguments 
```go
value := functionName(arg1, arg2, arg3)
```

```go
value := functionName(
    arg1,
    arg2,
    arg3,
    arg4,
)
```
Unless the arguments are long on screen, in which case split lines, for instance when using a `slice` or `struct` as input

```go
value := functionName(
    arg1,
    []string{"entry1", "entry2"},
)
```
```go
value := functionName(
    arg1,
    myStruct{
        A: "entry1",
        B: "entry2",
    },
)
```
```go
value := functionName(
    arg1,
    complex.Nested.Deeply.LongFuncCall(param1, param2),
)
```

# Design Ideas

## Code locality
- Locate code used together in the same file if possible
- If not possible prefer the same package
- Avoid many small files as it makes code harder to read
- If a function is only used once consider inlining the code
- If inlining what was previously a function, prefer comment blocks to show the code sections, like you would with a chapter in a book. Code is meant to be read and the less jumping around to read it the easier the flow of logic will be to follow. 

## Use interfaces sparingly
- In most cases we can use a function from a package to achieve what we have done with an interface.
- Where we need generalisation we can pass a function as an argument to another function.

## Structs
- If a struct has a single member, there is likely little value in the struct and it's likely adding noise, consider using a temporary anoymous struct if you need to.

## Prefer longer functions to fragmentation
- Highly fragmented code is hard to read, small functions break the flow of the reader, especially when the function is far away and a comment can have the same effect as the function name without affecting the reader's flow.
- Duplication is good if what we're duplicating isn't that complex. Overly DRY codebases are difficult to read as the code is highly fragmented and abstracted.
- If a function is used once, the code should probably be inline, unless it's sufficiently complex it cannot be tested inline.

## Prefer standards to custom wrappers
- Try to use more standard objects as they have more predictable behaviour, augment their behaviour by calling custom functions

## Prefer standard types to putting custom names on standard types
- If a parameter is an `any` prefer to call it as such, as opposed to using a new name to describe that.

## Packages are for solving one particular problem
- Try to limit the scope of a package to one problem, for instance "items" related code would naturally form a package, including what is currently in api and db
- This makes it easier to find and reason about code related to a particular domain
- The package forms an inituitive namespace so that naming becomes easier, eg `items.Validate()` cannot be confused with `users.Validate()`
- Packages make a clearer namespace than a struct
- Packages may be nested without adding additional confusion, eg models/user  could be two different packages one inside the other
- Prefer functions over methods on a struct, as then we have less noise from otherwise empty structs and it's clearer what the function's input is, see below.

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

## Struct members vs functions with more parameters
- **The most important thing a function can do with it's signature (inputs and outputs) is show what changed and what data was needed to change it**.
- The opposite of this is a method on a struct with no arguments, it is not clear what changed or which data in the struct was used to make that change.
- Making side effects explicit helps build a mental model of the interactions of different parts of the system and how they interract


# Unit test logic
- Unit testing IO is not particularly realistic and can easily diverge from reality and give false positives, as well as being more code to write and maintain
- Unit testing logic gives confidence on tough to understand processing

# Integration testing the API
- Docker compose env started prior to running tests
- Tests hit as close to real env as we can
- Tests are quick as no network involved as all local in Docker
- Stateful tests need set up and tare down steps which can be automated, eg DELETE, PUT
- Non-Stateful tests could be done against a fixed set of data, eg GET

# Confidence driven testing
- Unit or integration tests are most valuable where something is critically important or it is non-trivial to validate something is correct
- Conversely if we accumulate tests which don't really give us real confidence, we just burden ourselves with fragile test code to maintain

# Tests requiring infrastructure
- Prefer to avoid mocks, they can easily give false positives.
- If something needs a lot of mocks and can't be replicated easily in Docker, it might be better to test against "testing infrastructure"
- "testing infrastructure" gives us real signals about if the tests passed or not


 
