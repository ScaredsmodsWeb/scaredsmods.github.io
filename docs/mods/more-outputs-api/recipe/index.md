# Making a custom recipe

To make a recipe that works with multiple outputs, you need a class implementing Recipe<Inventory>, OutputRecipe<Inventory>.
Inventory can be replaced with any class that extends inventory. Important is that the class between brackets matches between both classes

It would look something like this:

```java

public class ExampleRecipe implements Recipe<SimpleInventory>, Output4Recipe<SimpleInventory> {

    // [Methods]
}
```

For every amount of output slots, it works a little bit different, documentation for those can be found below:

- [2 Outputs](two-output-recipe/index.md)
- [3 Outputs](three-output-recipe/index.md)
- [4 Outputs](four-output-recipe/index.md)