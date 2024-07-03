# Screen (handlers)

We need a few things, 

- A class that will register the screen handlers (ModScreenHandlers)
- A [screen](../screen/examplescreen.md)
- A [screenhandler](../screen/examplescreenhandler.md)

## ModScreenHandlers

In the main package, create a new package called ```screen```, then create a new class I will call ```ModScreenHandlers```. In that class, create a new void method called ```registerModScreenHandlers```. Note: This class will have errors at first but once we create the screen(handlers), they will all go away. The method should look like this:

```java
public static final ScreenHandlerType<ExampleScreenHandler> EXAMPLE_SCREEN_HANDLER =
    Registry.register(Registries.SCREEN_HANDLER, new Identifier(ExampleMod.MOD_ID, "example"),
            new ExtendedScreenHandlerType<>(ExampleScreenHandler::new));

public static void registerScreenHandlers() {
    ExampleMod.LOGGER.info("Registering Screen Handlers for " + ExampleMod.MOD_ID);//(1)!
}
```

1. This can be empty and is recommended to be empty

Now we can create a [screen](../screen/examplescreen.md) and a [screenhandler](../screen/examplescreenhandler.md). 