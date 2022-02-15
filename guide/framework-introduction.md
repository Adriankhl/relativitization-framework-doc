# Framework introduction

1. [Data](#data)

## Data

Data-related code should be located in `relativitization/universe/src/main/kotlin/relativitization/universe/data`.
Assume that this is the root directory in this section.

### Immutable data and mutable data

To avoid error caused by mutability, especially in parallelized process, most data has an immutable version and a
mutable version.

Typically, if the immutable version of a class is named `A`, then the mutable version of the class should be named as
`MutableA`.