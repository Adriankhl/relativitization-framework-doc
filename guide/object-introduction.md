# Object introduction

A brief introduction to objects in Relativitization:

1. [Data](#data)

## Data

Data-related code should be located in `relativitization/universe/src/main/kotlin/relativitization/universe/data`.
Assume that this is the root directory in this section.

### Immutable data and mutable data

To avoid error caused by mutability, especially in parallelized processes, most data has an immutable version and a
mutable version.

Typically, if the immutable version of a class is named `A`, then the mutable version of the class should be named as
`MutableA`.

`DataSerializer.copy` in `serializer/DataSerializer.kt` is used to copy data from mutable version of a class to the
immutable version, or vice versa. To ensure that the serializer works:

* The property names in the immutable version and the mutable version of a class should be the same
* Use `sealed class` if you need polymorphism
* If a polymorphic class is stored in some collections, enforce the same serialization name in both the immutable
  version and the mutable version of the class by the `@SerialName` annotation

### Player data

`PlayerData` and `MutablePlayerData` in `PlayerData.kt` are the immutable version and imm