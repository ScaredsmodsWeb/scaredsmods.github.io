# Making a recipe with four outputs

Like already said, we need a class that implements Recipe<Inventory>, Output3Recipe<Inventory> so lets get started. We also need a RecipeType and RecipeSerializer and we need a ModRecipes class.

## Base recipe class
Start by making a new package in your main package called ```recipe```, then create a new class ```ExampleRecipe``` and for the type of inventory I will go for is ```SimpleInventory```. 

```java title="ExampleRecipe"
public class ExampleRecipe implements Recipe<SimpleInventory>, Output4Recipe<SimpleInventory> {
}
```
Now implement the methods your IDE (Prefferably Intellij IDEA) asks for, don't create the constructor matching super yet. 

We need a few fields that will depict how the class works, namely a List<Ingredient> for the inputs and 2 ItemStacks for the two outputs we have.

```java title="Fields"
private final ItemStack output_1;
private final ItemStack output_2;
private final ItemStack output_3;
private final ItemStack output_4;

private final List<Ingredient> recipeItems;
```

Now you can hover over the class name, and create the constructor matching super.
It should look something like this:

```java title="Implemented methods"

public class ExampleRecipe implements Recipe<SimpleInventory>, Output2Recipe<SimpleInventory> {


    private final ItemStack output_1;
    private final ItemStack output_2;
    private final ItemStack output_3;
    private final ItemStack output_4;

    private final List<Ingredient> recipeItems;

    public ExampleRecipe(List<Ingredient> ingredients, ItemStack itemStack, ItemStack output2, ItemStack output3, ItemStack output4) {
        this.output_1 = itemStack;
        this.recipeItems = ingredients;
        this.output_2 = output2;
        this.output_3 = output3;
        this.output_4 = output4;
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
    public ItemStack getThirdResult(DynamicRegistryManager registryManager) {
        return null;
    }

    @Override
    public ItemStack craftThird(SimpleInventory inventory, DynamicRegistryManager registryManager) {
        return null;
    }
    
    @Override
    public ItemStack getFourthResult(DynamicRegistryManager registryManager) {
        return null;
    }
    
    @Override
    public ItemStack craftFourth(SimpleInventory inventory, DynamicRegistryManager registryManager) {
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
```getThirdResult() & craftThird()``` should return ```output_3```
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

### RecipeType
Now we can make the recipe type and recipe serializer, make a new sub class called ```Type``` and let it implement ```RecipeType<ExampleRecipe>```.
This is a small subclass that only contains two fields: ```INSTANCE``` which creates a new instance of the ```Type``` class and ```ID``` which is the RecipeType ID(entifier)

```java title="RecipeType"
public class Type implements RecipeType<ExampleRecipe> {

    public static final Type INSTANCE = new Type();
    public static final String ID = "example_recipe";
}
``` 
example_recipe can be whatever you want, just make sure its recognizeable.

### Recipe Serializer

The recipe serializer is a little more complicated but still pretty easy to do.
Create another sub class in your class implementing Recipe, ill name it ```Serializer```. ```Serializer``` also needs the ```INSTANCE``` & ```ID``` fields but ```INSTANCE``` is equal to ```new Serializer()```.

This is what the class should look like: 

```java title="Serializer"

public static class Serializer implements RecipeSerializer<ExampleRecipe> {

    public static final Serializer INSTANCE = new Serializer();
    public static final String ID = "example_recipe"
    /*
    This method is neccessary because of the codec, this is how it works
    If you have more outputs, duplicate the third or fourth line and adjust it to your needs
    */  
    public static final Codec<ExampleRecipe> CODEC = RecordCodecBuilder.create(in -> in.group(
            validateAmount(Ingredient.DISALLOW_EMPTY_CODEC, 9).fieldOf("ingredients").forGetter(ExampleRecipe::getIngredients),
            ItemStack.RECIPE_RESULT_CODEC.fieldOf("output_1").forGetter(r -> r.output_1),
            ItemStack.RECIPE_RESULT_CODEC.fieldOf("output_2").forGetter(r -> r.output_2),
            ItemStack.RECIPE_RESULT_CODEC.fieldOf("output_3").forGetter(r -> r.output_3),
            ItemStack.RECIPE_RESULT_CODEC.fieldOf("output_4").forGetter(r -> r.output_4)
        ).apply(in, ExampleRecipe::new));
    
    


    private static Codec<List<Ingredient>> validateAmount(Codec<Ingredient> delegate, int max) {
        return Codecs.validate(Codecs.validate(
                delegate.listOf(), list -> list.size() > max ? DataResult.error(() -> "Recipe has too many ingredients!") : DataResult.success(list)
        ), list -> list.isEmpty() ? DataResult.error(() -> "Recipe has no ingredients!") : DataResult.success(list));
    }

    @Override
    public Codec<ExampleRecipe> codec() {
        return CODEC;
    }

    @Override
    public ExampleRecipe read(PacketByteBuf buf) {
        DefaultedList<Ingredient> inputs = DefaultedList.ofSize(buf.readInt(), Ingredient.EMPTY);

        for(int i = 0; i < inputs.size(); i++) {
            inputs.set(i, Ingredient.fromPacket(buf));
        }

        ItemStack output_1 = buf.readItemStack();
        ItemStack output_2 = buf.readItemStack();
        ItemStack output_3 = buf.readItemStack();
        ItemStack output_4 = buf.readItemStack();
        return new ExampleRecipe(inputs, output_1, output_2, output_3, output_4);//(1)!
    }

    @Override
    public void write(PacketByteBuf buf, ExampleRecipe recipe) {
        buf.writeInt(recipe.getIngredients().size());

        for (Ingredient ingredient : recipe.getIngredients()) {
                ingredient.write(buf);
        }

        buf.writeItemStack(recipe.getResult(null));
        buf.writeItemStack(recipe.getSecondResult(null));
        buf.writeItemStack(recipe.getThirdResult(null));
        buf.writeItemStack(recipe.getFourthResult(null));
    }
}
```

1. If you have more outputs, declare more ItemStack's like above and add them to the constructor the same way as in the beginning.

Now we can fill in ```getSerializer()``` & ```getType()``` :

```java title=""
@Override
public RecipeSerializer<?> getSerializer() {
    return Serializer.INSTANCE;
}

@Override
public RecipeType<?> getType() {
    return Type.INSTANCE;
}
```


## Registering 

To register the recipes, make a new class in the ```recipe``` package, I will call that ```ModRecipes```

Make a method called ```registerModRecipes()```, it should be a void, in the method add these lines to the method:


=== "Lines"

    ```java title="Registering RecipeType & Serializer"
        Registry.register(Registries.RECIPE_SERIALIZER, new Identifier(ExampleMod.MOD_ID, ExampleRecipe.Serializer.ID),
            ExampleRecipe.Serializer.INSTANCE);
        Registry.register(Registries.RECIPE_TYPE, new Identifier(ExampleMod.MOD_ID, ExampleRecipe.Type.ID),
            ExampleRecipe.Type.INSTANCE);
    ```

=== "Full method"
    
    
    ```java title="Full method"

    public static void registerRecipes() {
        Registry.register(Registries.RECIPE_SERIALIZER, new Identifier(ExampleMod.MOD_ID, ExampleRecipe.Serializer.ID),
                ExampleRecipe.Serializer.INSTANCE);
        Registry.register(Registries.RECIPE_TYPE, new Identifier(ExampleMod.MOD_ID, ExampleRecipe.Type.ID),
                ExampleRecipe.Type.INSTANCE);
    }
    ```




