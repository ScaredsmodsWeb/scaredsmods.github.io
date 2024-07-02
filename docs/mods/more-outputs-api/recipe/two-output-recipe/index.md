# Making a recipe with two outputs

Like already said, we need a class that implements Recipe<Inventory>, Output2Recipe<Inventory> so lets get started. We also need a RecipeType and RecipeSerializer and we need a ModRecipes class.

## Base recipe class
Start by making a new package in your main package called ```recipe```, then create a new class ```ExampleRecipe``` and for the type of inventory i will go for is ```SimpleInventory```. 

```java title="ExampleRecipe"
public class ExampleRecipe implements Recipe<SimpleInventory>, Output2Recipe<SimpleInventory> {
}
```
Now implement the methods your IDE (Prefferably Intellij IDEA) asks for, don't create the constructor matching super yet. 

We need a few fields that will depict how the class works, namely a List<Ingredient> for the inputs and 2 ItemStacks for the two outputs we have.

```java title="Fields"
private final ItemStack output_1;
private final ItemStack output_2;

private final List<Ingredient> recipeItems;
```

Now you can hover over the class name, and create the constructor matching super.
It should look something like this:

```java title="Implemented methods"

public class ExampleRecipe implements Recipe<SimpleInventory>, Output2Recipe<SimpleInventory> {


    private final ItemStack output_1;
    private final ItemStack output_2;

    private final List<Ingredient> recipeItems;

    public ExampleRecipe(List<Ingredient> ingredients, ItemStack itemStack, ItemStack output2) {
        this.output_1 = itemStack;
        this.recipeItems = ingredients;
        this.output_2 = output2;
    }

    @Override
    public ItemStack getSecondResult(DynamicRegistryManager registryManager) {
        return null;
    }

    @Override
    public ItemStack craftSecond(SimpleInventory inventory, DynamicRegistryManager registryManager) {
        return null;
    }

    @Override
    public boolean matches(SimpleInventory inventory, World world) {
        return false;
    }

    @Override
    public ItemStack craft(SimpleInventory inventory, DynamicRegistryManager registryManager) {
        return null;
    }

    @Override
    public boolean fits(int width, int height) {
        return false;
    }

    @Override
    public ItemStack getResult(DynamicRegistryManager registryManager) {
        return null;
    }

    @Override
    public RecipeSerializer<?> getSerializer() {
        return null;
    }

    @Override
    public RecipeType<?> getType() {
        return null;
    }
}
```
Now in the ```getSecondResult() & craftSecond()``` methods, return ```output_2```, and ```getResult() & craft()``` return ```output_1```
The ```matches()``` method should look like this:

```java title="matches()"
@Override
public boolean matches(SimpleInventory inventory, World world) {
    if(world.isClient()) {
        return false;
    }

    return recipeItems.get(0).test(inventory.getStack(0)); //(1)!
}
```

1. get(0) can also be replaced with getFirst()

The ```fits()``` method should return ```true```
Since can't fill in the ```getSerializer()``` & ```getType()``` methods just yet, remove ```null;``` from the return statements to make a deliberate error to help us remember that we need to fill in that method.

## RecipeType & Serializer
Now we can make the recipe type and recipe serializer, make a new sub class called ```Type``` and let it implement ```RecipeType<ExampleRecipe>```.
This is a small subclass that only contains two fields: ```INSTANCE``` which creates a new instance of the ```Type``` class and ```ID``` which is the RecipeType ID(entifier)

```java title="RecipeType"
public class Type implements RecipeType<ExampleRecipe> {

    public static final Type INSTANCE = new Type();
    public static final String ID = "example_recipe";
}
``` 
example_recipe can be whatever you want, just make sure its recognizeable.


