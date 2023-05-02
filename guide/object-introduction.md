# Object introduction

A brief introduction to objects in Relativitization:

1. [Data](#data)
2. [Mechanism](#mechanism)
3. [Global mechanism](#global-mechanism)
4. [AI](#ai)
5. [Universe generation](#universe-generation)

For a concrete example, see [flocking model](./flocking-model.md).

## Data

### Immutable data and mutable data

While the data of a player should be mutable,
what the player sees, which involves data of other players,
should be immutable.



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

If you are useing the framework for modeling only, you don't have to work with AI.
AI-related code should be located in `relativitization/universe/src/main/kotlin/relativitization/universe/ai`. Assume
that this is the root directory in this section.

### AI computation

AI determines the list of commands from a player, the commands will self-execute on the sender and execute on the target
receiver. An AI should be an object inheriting `AI` in `AI.kt`.

`PlayerInternalData` in [player data](#player-data) stores a property called `aiName`, it determines which AI the player
will be using to compute the list of commands each turn.

## Universe generation

Universe-generation-related code should be located
in `relativitization/universe/src/main/kotlin/relativitization/universe/generate`. Assume that this is the root
directory in this section.

### Method category: abm, random, testing

To clarify the usage of the generation methods, they are divied into three categories:

1. `ABMGenerateUniverseMethod` in `abm/ABMGenerateUniverseMethod.kt`
2. `RandomGenerateUniverseMethod` in `random/RandomGenerateUniverseMethod.kt`
3. `TestingGenerateUniverseMethod` in `testing/TestingGenerateUniverseMethod.it`

They have exactly the same functionality. Pick one of them to inherit and create an object to generate universe for your
model.

### Things to consider

Make sure the universe generated is valid:

* Has a valid `UniverseSettings`
* Has a valid `UniverseState`
* Include the player data component needed in `PlayerDataComponentMap`
* Include the global data component needed in `GlobalDataComponentMap`
* Pick the `aiName` of player
* Specify the name of the desired `CommandAvailability` in `commandCollectionName` of `UniverseSettings`
* Specify the name of the desired `MechanismLists` in `mechanismCollectionName` of `UniverseSettings`
* Specify the name of the desired `GlobalMechanismList` in `globalMechanismCollectionName` of `UniverseSettings`
