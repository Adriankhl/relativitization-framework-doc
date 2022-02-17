# Object introduction

A brief introduction to objects in Relativitization:

1. [Data](#data)
2. [Mechanism](#mechanism)
3. [Global mechanism](#global-mechanism)
4. [AI](#ai)
5. [Universe generation](#universe-generation)

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

`PlayerDataComponentMap` in `components/PlayerDataComponent.kt` stores `PlayerDataComponent` as a map from the
serialization name to the data component.

### Global data

`UniverseGlobalData` in `global/UniverseGlobalData.kt` stores the global data of the universe.

### Global data component

A global data component should inherit `GlobalDataComponent` in `global/components/GlobalDataComponent.kt`, the mutable
counterpart should inherit `MutableGlobalDataComponent`, `@SerialName` annotation should be used to unify the
serialization name for both versions of the class.

`GlobalDataComponentMap` in `global/components/GlobalDataComponent.kt` stores `PlayerDataComponent` as a map from the
serialization name to the data component.

### Command

Whenever a player wants to interact with another player, it is mediated by commands. A command should inherit `Command`
in `commands/Command.kt`.

### Event

An event of a player generates commands based on the choice of the player or the default choice. An event should inherit
`Event` in `events/Event.kt`.

### Command availability

Commands and events can be shared among different models. To explicitly constrain the available commands for a model,
you have to create an object to inherit `CommandAvailability` in `commands/Command.kt`. It stores the names of available
commands and the names of available events to be added by the `AddEventCommand` in `commands/DefaultEventCommands.kt`.

## Mechanism

Mechanism-related code should be located
in `relativitization/universe/src/main/kotlin/relativitization/universe/mechanisms`. Assume that this is the root
directory in this section.

### Mechanism process

Mechanism determines how the data of players should be modified. A mechanism should be an object inheriting
`Mechanism` in `Mechanism.kt`. The `process` function modifies the player data and generate a list of commands to send
from the player.

### Mechanism lists: regular vs dilated

Mechanisms can be shared among different models. To specify the mechanisms to be processed in a model, you have to
create an object to inherit `MechanismLists` in `Mechanism.kt`. It stores a list of regular mechanism and a list of
dilated mechanism to be processed in the model. Regular mechanisms are executed once per turn, where dilated mechanisms
is affected by time dilation and they should be executed once per time in average.

## Global mechanism

Global-mechanism-related code should be located
in `relativitization/universe/src/main/kotlin/relativitization/universe/global`. Assume that this is the root directory
in this section.

### Update universe global data

Global mechanism determines how the universe global data should be modified. Be aware that this may break the
information speed constraint imposed by special relativity if not carefully implemented. A global mechanism should be an
object inheriting `GlobalMechanism` in `GlobalMechanism.kt`. The `updateGlobalData` function modifies the global data.

### Global mechanism list

Global mechanisms can be shared among different models. To specify the global mechanisms to be processed in a model, you
have to create an object to inherit `GlobalMechanismList` in `GlobalMechanism.kt`. It stores a list of global mechanism
to be processed once per turn in the model.

## AI

## Universe generation