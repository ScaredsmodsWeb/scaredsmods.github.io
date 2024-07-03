## Connecting the screen to the client

### ClientModInitializer
To do this you need a class that implements ```ClientModInitializer```, in my case ```ExampleModClient```. Implement the ```onClientInitialize()``` method.

The following line adds the screen from the client:

```java

HandledScreens.register(ModScreenHandlers.EXAMPLE_SCREEN_HANDLER, ExampleScreen::new);

```

Full method:

```java

@Override
public void onInitializeClient() {
    [... Other client stuff ...]
    HandledScreens.register(ModScreenHandlers.EXAMPLE_SCREEN_HANDLER, ExampleScreen::new);
    [... Other client stuff ...]
}

```

This is dependent on the ```fabric.mod.json``` file in ```resources```. To let fabric know that this class exists, add this to your ```fabric.mod.json``` under the entrypoints section:

```json

"client": [
	"your.path.to.ModClient"
]

```