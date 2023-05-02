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
Since models are ran concurrently under this framework,
to avoid problems caused by mutability,
it is recommended to have both a mutable version and an immutable version of the same data.
A mutable data class should have a name starting with `Mutable` and inherit `MutablePlayerDataComponent`,
while the immutable counter-part should inherit `PlayerDataComponent`.
To simplify the development, you can mark a mutable data class by `@GenerateImmutable`,
which generates the immutable counter-part automatically.

### Copying data

`DataSerializer.copy()` can be used to copy data from mutable version of a class to the
immutable version, or vice versa.

### Player data

`MutablePlayerData` contains the core data of a player, where `PlayerData` is the corresponding
immutable version. `PlayerData` stores the basic data of a player, such as the position and velocity.
Other data is stored in `PlayerInternalData`, such as events and hierarchy of players. The
`playerDataComponentMap` property in `PlayerInternalData` stores model-specific data.

### Global data

`UniverseGlobalData` stores the global data of the universe,
where the `globalDataComponentMap` stores the model-specific data.

A mutable global data component should inherit `MutableGlobalDataComponent`,
and annotated by `@GenerateImmutable`.

### Command

Whenever a player wants to interact with another player, it is mediated by commands. 
A command should inherit `Command`.

### Event

An event of a player generates commands based on the choice of the player or the default choice.
An event should inherit `Event`.

### Command availability

Commands and events can be shared among different models.
To explicitly constrain the available commands for a model,
you have to create an object to inherit `CommandAvailability`. 
It stores the names of available commands.
Additionally, it stores the names of available events that can be used by any event-related commands.

## Mechanism

Mechanism determines how the data of players should be modified. A mechanism should be an object inheriting
`Mechanism`. The `process` function modifies the player data and generate a list of commands to send
from the player.

### Mechanism lists: regular vs dilated

Mechanisms can be shared among different models. To specify the mechanisms to be processed in a model, you have to
create an object to inherit `MechanismLists`. It stores a list of regular mechanism and a list of
dilated mechanism to be processed in the model. Regular mechanisms are executed once per turn, where dilated mechanisms
is affected by time dilation and they should be executed once per time in average.

## Global mechanism

Global mechanism determines how the universe global data should be modified. Be aware that this may break the
information speed constraint imposed by special relativity if not carefully implemented. A global mechanism should be an
object inheriting `GlobalMechanism`. The `updateGlobalData` function modifies the global data.

### Global mechanism list

Global mechanisms can be shared among different models. To specify the global mechanisms to be processed in a model, you
have to create an object to inherit `GlobalMechanismList`. It stores a list of global mechanism
to be processed once per turn in the model.

## AI

If you are using the framework for modeling only, you may not need to work with AI.

AI determines the list of commands from a player, the commands will self-execute on the sender 
and execute on the target receiver. An AI should be an object inheriting `AI` in `AI.kt`.

`PlayerInternalData` in [player data](#player-data) stores a property called `aiName`, 
it determines which AI the player will be using to compute the list of commands each turn.

## Universe generation

An universe generation object should inherit `GenerateUniverseMethod`.
The `generate()` function takes `GenerateSettings` and generate an `UniverseData`,
which is the initial state of the universe.

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
