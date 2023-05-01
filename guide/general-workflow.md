# General workflow

These steps are probably necessary in all models/games:

1. Define model-specific data
2. Define commands and events needed in the model
3. Define the player mechanisms of the model, and put the mechanisms in a `MechanismLists` object
4. Define a `GenerateUniverseMethod` to generate the universe with proper settings and state

The following steps might be needed for some models/games:

1. Put the available commands and events in a `CommandAvailability` object to limit which commands 
  can be sent by players
2. Define the global mechanisms of the model, and put the mechanisms in a `GlobalMechanismList` object
3. Define `AI` of players

Before running any simulation/game, you need to initialize the framework by registering the
mechanisms/data/ai you have defined, see `GameUniverseInitializer.initializer()` for an example.
Now the model should be ready to run, `./simulations/src/main/kotlin/relativitization/game/TypicalGame.kt`
demonstrates how to run the full game model.

See [Object introduction](./object-introduction.md) for details on how the objects should be defined.
