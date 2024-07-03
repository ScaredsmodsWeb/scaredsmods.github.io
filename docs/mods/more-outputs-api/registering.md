## In the main class...

In your main class, we have four things we need to register: The block, block entity, the recipe and the screenhandlers.
To do this, go to your class that implements ```ModInitializer```, go to ```onInitialize()``` and add this if you haven't already

```java
@Override
public void onInitialize() {
    
	ModBlockEntities.registerBlockEntities();
	ModBlocks.registerModBlocks();
	ModRecipes.registerRecipes();
	ModScreenHandlers.registerScreenHandlers();
}
```