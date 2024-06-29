# Block Entity

Before we can create our block entity, we need to first make 2 'helper' classes, ```ModBlockEntities``` and a little helper class ```ImplementedInventory```. NOTE: I did not make the last class, all credits are available in that class.


=== "ModBlockEntities"

    ```java
    public class ModBlockEntities {

        public static final BlockEntityType<ExampleBlockEntity> EXAMPLE_BLOCK_ENTITY =  // (1)
            Registry.register(Registries.BLOCK_ENTITY_TYPE, Identifier.of(ExampleMod.MOD_ID, "example_be"),
                    BlockEntityType.Builder.create(ExampleBlockEntity::new,  // (2)
                            ModBlocks.EXAMPLE_BLOCK).build());  // (3)
        
        public static void registerBlockEntities() {
            Main.LOGGER.info("Registering Block Entities for " + Main.MOD_ID);
        }
    }

     

    ```
    
    1. ❌ Here should be an error present, as we haven't made that class yet
    2. ❌ Here should be an error present, as we haven't made that class yet
    3. ❌ Here should be an error present, as we haven't made that class yet



=== "ImplementedInventory"


    ```java 
    
        /**
         * A simple {@code SidedInventory} implementation with only default methods + an item list getter.
         *
         * <h2>Reading and writing to tags</h2>
         * Use {@link Inventories#writeNbt(NbtCompound, DefaultedList)} and {@link Inventories#readNbt(NbtCompound, DefaultedList)}
         * on {@linkplain #getItems() the item list}.
         *
         * License: <a href="https://creativecommons.org/publicdomain/zero/1.0/">CC0</a>
         * @author Juuz
         */
        @FunctionalInterface
        public interface ImplementedInventory extends SidedInventory {
            /**
             * Gets the item list of this inventory.
             * Must return the same instance every time it's called.
             *
             * @return the item list
             */
            DefaultedList<ItemStack> getItems();

            /**
             * Creates an inventory from the item list.
             *
             * @param items the item list
             * @return a new inventory
             */
            static ImplementedInventory of(DefaultedList<ItemStack> items) {
                return () -> items;
            }

            /**
             * Creates a new inventory with the size.
             *
             * @param size the inventory size
             * @return a new inventory
             */
            static ImplementedInventory ofSize(int size) {
                return of(DefaultedList.ofSize(size, ItemStack.EMPTY));
            }

            // SidedInventory

            /**
             * Gets the available slots to automation on the side.
             *
             * <p>The default implementation returns an array of all slots.
             *
             * @param side the side
             * @return the available slots
             */
            @Override
            default int[] getAvailableSlots(Direction side) {
                int[] result = new int[getItems().size()];
                for (int i = 0; i < result.length; i++) {
                    result[i] = i;
                }

                return result;
            }

            /**
             * Returns true if the stack can be inserted in the slot at the side.
             *
             * <p>The default implementation returns true.
             *
             * @param slot the slot
             * @param stack the stack
             * @param side the side
             * @return true if the stack can be inserted
             */
            @Override
            default boolean canInsert(int slot, ItemStack stack, @Nullable Direction side) {
                return true;
            }

            /**
             * Returns true if the stack can be extracted from the slot at the side.
             *
             * <p>The default implementation returns true.
             *
             * @param slot the slot
             * @param stack the stack
             * @param side the side
             * @return true if the stack can be extracted
             */
            @Override
            default boolean canExtract(int slot, ItemStack stack, Direction side) {
                return true;
            }

            // Inventory

            /**
             * Returns the inventory size.
             *
             * <p>The default implementation returns the size of {@link #getItems()}.
             *
             * @return the inventory size
             */
            @Override
            default int size() {
                return getItems().size();
            }

            /**
             * @return true if this inventory has only empty stacks, false otherwise
             */
            @Override
            default boolean isEmpty() {
                for (int i = 0; i < size(); i++) {
                    ItemStack stack = getStack(i);
                    if (!stack.isEmpty()) {
                        return false;
                    }
                }

                return true;
            }

            /**
             * Gets the item in the slot.
             *
             * @param slot the slot
             * @return the item in the slot
             */
            @Override
            default ItemStack getStack(int slot) {
                return getItems().get(slot);
            }

            /**
             * Takes a stack of the size from the slot.
             *
             * <p>(default implementation) If there are less items in the slot than what are requested,
             * takes all items in that slot.
             *
             * @param slot the slot
             * @param count the item count
             * @return a stack
             */
            @Override
            default ItemStack removeStack(int slot, int count) {
                ItemStack result = Inventories.splitStack(getItems(), slot, count);
                if (!result.isEmpty()) {
                    markDirty();
                }

                return result;
            }

            /**
             * Removes the current stack in the {@code slot} and returns it.
             *
             * <p>The default implementation uses {@link Inventories#removeStack(List, int)}
             *
             * @param slot the slot
             * @return the removed stack
             */
            @Override
            default ItemStack removeStack(int slot) {
                return Inventories.removeStack(getItems(), slot);
            }

            /**
             * Replaces the current stack in the {@code slot} with the provided stack.
             *
             * <p>If the stack is too big for this inventory ({@link Inventory#getMaxCountPerStack()}),
             * it gets resized to this inventory's maximum amount.
             *
             * @param slot the slot
             * @param stack the stack
             */
            @Override
            default void setStack(int slot, ItemStack stack) {
                getItems().set(slot, stack);
                if (stack.getCount() > getMaxCountPerStack()) {
                    stack.setCount(getMaxCountPerStack());
                }
                markDirty();
            }

            /**
             * Clears {@linkplain #getItems() the item list}}.
             */
            @Override
            default void clear() {
                getItems().clear();
            }

            @Override
            default void markDirty() {
                // Override if you want behavior.
            }

            @Override
            default boolean canPlayerUse(PlayerEntity player) {
                return true;
            }
        }




    ```



To create a block entity, create a new class in ```net.example.yourmodid.block.entity```, the class extends ```BlockEntity``` and implements ```ExtendedScreenHandlerFactory, ImplementedInventory```

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


1. ❌ Here should be a few errors present, as we haven't made that class yet

```java title="resetProgress()"
private void resetProgress() {
    this.progress = 0;
}
```