# Block Entity with two output slots


To create a block entity with two output slots, create a new class in ```net.example.yourmodid.block.entity```, the class extends ```BlockEntity``` and implements ```ExtendedScreenHandlerFactory, ImplementedInventory```

In action, it would look like this:

```java

public class ExampleBlockEntity extends BlockEntity implements ExtendedScreenHandlerFactory, ImplmentedInventory {


}
```

Before we can make all the required methods, we need a few fields and a constructor.

```java 
private final DefaultedList<ItemStack> inventory = DefaultedList.ofSize(3, ItemStack.EMPTY); // (1)!

private static final int INPUT_SLOT = 0; // (2)!
private static final int OUTPUT_SLOT_1 = 1; // (3)!
private static final int OUTPUT_SLOT_2 = 2; // (4)!

protected final PropertyDelegate propertyDelegate;
private int progress = 0; // (5)!
private int maxProgress = 100; // (6)!
```

1. IMPORTANT: This should be the total amount of slots in the block entity & recipe. Including the input & output slots
2. This is the slot you put the item to be crafted in.
3. This is the first of the outputs from the recipe
4. This is the seconds output from the recipe
5. At this point, the block entity (machine) starts crafting
6. This is the max the crafting takes



The constructor should look like this:

```java
public ExampleBlockEntity(BlockPos pos, BlockState state) {
    super(ModBlockEntities.EXAMPLE_BLOCK_ENTITY, pos, state);
    this.propertyDelegate = new PropertyDelegate() {
        @Override
        public int get(int index) {
            return switch (index) {
                case 0 -> ExampleBlockEntity.this.progress;
                case 1 -> ExampleBlockEntity.this.maxProgress;
                default -> 0;
            };
        }

        @Override
        public void set(int index, int value) {
            switch (index) {
                case 0 -> ExampleBlockEntity.this.progress = value;
                case 1 -> ExampleBlockEntity.this.maxProgress = value;
            }
        }

        @Override
        public int size() {
            return 3; // (1)
        }
    };
}

```

1. IMPORTANT: This should be the total amount of slots in the block entity & recipe. Including the input & output slots

Now we can make all the required methods neccessary: 

```java title="writeScreenOpeningData()"
@Override
public void writeScreenOpeningData(ServerPlayerEntity player, PacketByteBuf buf) {
    buf.writeBlockPos(this.pos);
}
```

```java title="getDisplayName()"
@Override
public Text getDisplayName() {
    return Text.literal("Example Block"); // (1)!
}
```

1. I would recommend that you make this translatable by replacing ```Text.literal()``` with ```Text.translatable```

```java title="getItems()"
@Override
public DefaultedList<ItemStack> getItems() {
    return inventory;
}
```

```java title="readNbt() and writeNbt()"
@Override
public void writeNbt(NbtCompound nbt) {
    super.writeNbt(nbt);
    Inventories.writeNbt(nbt, inventory);
    nbt.putInt("example_block.progress", progress); // (1)!
}

@Override
public void readNbt(NbtCompound nbt) {
    super.readNbt(nbt);
    Inventories.readNbt(nbt, inventory);
    progress = nbt.getInt("example_block.progress"); // (2)!
}

```

1. These two strings should match!
2. These two strings should match!

```java title="createMenu()"
@Nullable
@Override
public ScreenHandler createMenu(int syncId, PlayerInventory playerInventory, PlayerEntity player) {
    return new ExampleScreenHandler(syncId, playerInventory, this, this.propertyDelegate); // (1)!
}
```

1. ❌ Error: Class not created



```java title="tick()"

public void tick(World world, BlockPos pos, BlockState state) { // (1)!
    if(world.isClient()) {
        return;
    }

    
    if(areOutputSlotsEmptyOreReceivable()) {
        if(this.hasRecipe()) {
            this.increaseCraftProgress();
            markDirty(world, pos, state);

            if(hasCraftingFinished()) {
                this.craftItem();
                this.resetProgress();
            }
        } else {
            this.resetProgress();
        }
    } else {
        this.resetProgress();
        markDirty(world, pos, state);
    }
}
```


1. ❌ Here should be a few errors present, as we haven't made those methods yet



Now we are going to make the methods that are going to have influence on how the ```tick()``` method above behaves.
Im doing it from top to bottom, and the first method (or boolean) is:

```java title="areOutputSlotsEmptyOrReceivable()"
private boolean areOutputsSlotEmptyOrReceivable() {
    return this.getStack(OUTPUT_SLOT_1).isEmpty() || this.getStack(OUTPUT_SLOT_1).getCount() < this.getStack(OUTPUT_SLOT_1).getMaxCount()
            ||
            this.getStack(OUTPUT_SLOT_2).isEmpty() || this.getStack(OUTPUT_SLOT_2).getCount() < this.getStack(OUTPUT_SLOT_2).getMaxCount();
}
```
What we are doing here, is checking if the output slots are empty or in the case that there ís an item in the slot, we check if the amount of items is smaller than 64 (```getMaxCount()```)



Then, we check if the block entity has a recipe with ```hasRecipe()``` but before that methods, we need two other helper methods:

```java title="canInsertItemIntoOutputSlots() & canInsertAmountIntoOutputSlots()"
private boolean canInsertItemIntoOutputSlots(Item item, Item item2) {
    return this.getStack(OUTPUT_SLOT_1).getItem() == item || this.getStack(OUTPUT_SLOT_1).isEmpty() &&
        this.getStack(OUTPUT_SLOT_2).getItem() == item2 || this.getStack(OUTPUT_SLOT_2).isEmpty();
    }

private boolean canInsertAmountIntoOutputSlots(ItemStack result, ItemStack result2) {
    return this.getStack(OUTPUT_SLOT_1).getCount() + result.getCount() <= getStack(OUTPUT_SLOT_1).getMaxCount() &&
        this.getStack(OUTPUT_SLOT_2).getCount() + result2.getCount() <= getStack(OUTPUT_SLOT_2).getMaxCount();
}
```
As you can see, we have two items in the first method, we check if the same item is in output slot 1 or 2 (or 3 or 4)  or that the output slot is empty.
In the second method we compore the amount of items already in the output slots added to the result if thats smaller or equal to the maximum count of 64.




Here we check if there is an item that can be inserted into the output slots and if there is a correct amount of it.

```java title="hasRecipe()"

private boolean hasRecipe() {
    Optional<RecipeEntry<ExampleRecipe>> recipe = getCurrentRecipe();

    return recipe.isPresent() && canInsertAmountIntoOutputSlot(recipe.get().value().getResult(null), recipe.get().value().getSecondResult(null))
            && canInsertItemIntoOutputSlot(recipe.get().value().getResult(null).getItem(), recipe.get().value().getSecondResult(null).getItem());
}
```

The ```increaseCraftProgress()```, ```resetProgress()``` and ```hasCraftingFinished``` methods aren't really that special so I'll list them here:




```java title="resetProgress() & increaseCraftProgress() & hasCraftingFinished()"
private void resetProgress() {
    this.progress = 0;
}
private void increaseCraftProgress() {
    progress++;
}
private boolean hasCraftingFinished() {
    return progress >= maxProgress;
}
```
Before we can make the last method use in the ```tick()``` method, we need a helper method called ```getCurrentRecipe()``` which gets the current recipe by accessing