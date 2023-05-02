# General workflow

## Dependencies

We recommend to use [gradle](https://gradle.org/) as the build system of your project.

Add [Maven Central](https://central.sonatype.com/) to `repositories`:

```kotlin
repositories {
    mavenCentral()
}
```

To setup a project, we recommend the following plugins:

* `org.jetbrains.dokka`
* `org.jetbrains.kotlin.jvm`
* `org.jetbrains.kotlin.plugin.serialization`
* `com.google.devtools.ksp`

and libraries:

* `org.jetbrains.kotlinx:dataframe`
* `org.jetbrains.kotlinx:kotlinx-serialization-json`
* `io.github.adriankhl:ksergen-annotations`
* `io.github.adriankhl:ksergen-ksp` (note: this is an ksp library, should be in `ksp()` instead of `implementation()`)
* `io.github.adriankhl:relativitization-core`

## Steps to create a model

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
of [Relativitization](https://github.com/Adriankhl/relativitization)
demonstrates how to run the full game model.

See [Object introduction](./object-introduction.md) for details on how the objects should be defined.
