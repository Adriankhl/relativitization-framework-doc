# General workflow

The general workflow to create agent-based models or alternative game mechanics:

1. Define model-specific data
2. Define commands and events needed in the model, and put the available commands and events in a `CommandAvailability`
   object
3. Define the player mechanisms of the model, and put the mechanisms in a `MechanismLists` object
4. Define the global mechanisms of the model, and put the mechanisms in a `GlobalMechanismList` object
5. If needed, define the AI of the player
6. Define a `GenerateUniverseMethod` to generate the universe with proper settings and state

Now the model should be ready to run, `./simulations/src/main/kotlin/relativitization/game/TypicalGame.kt` is an example
on how to run a simulation.

See [Object introduction](./object-introduction.md) for details on how the objects should be defined.
