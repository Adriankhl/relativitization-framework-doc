# Object introduction

A brief introduction to objects in Relativitization:

1. [Data](#data)
2. [Mechanism](#mechanism)
3. [AI](#ai)
4. [Universe generation](#universe-generation)

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
* If a polymorphic class is stored in some collections, you need to enforce the same serialization name in both the
  immutable version and the mutable version of the class by the `@SerialName` annotation

### Player data

`PlayerData` in `PlayerData.kt` contains the core data of a player, where `MutablePlayerData` is the corresponding
mutable version. `PlayerData` stores the basic data of a player, such as the position and velocity. Other data is stored
in `PlayerInternalData`, such as events and hierarchy of players. The
`playerDataComponentMap` property in `PlayerInternalData` stores model-specific data.

### Player data component

A player data component should inherit `PlayerDataComponent` in `components/PlayerDataComponent.kt`, the mutable
counterpart should inherit `MutablePlayerDataComponent`, `@SerialName` annotation should be used to unify the
serialization name for both versions of the class.

`PlayerDataComponentMap` in `components/PlayerDataComponent.kt` store `PlayerDataComponent` as a map from the
serialization name to the data component.

### Global data

## Mechanism

## AI

## Universe generation