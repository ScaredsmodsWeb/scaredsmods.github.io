# Block entities

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


Now we can make a block entity with [2](../block-entity/two-outputs/index.md), [3](../block-entity/three-outputs/index.md) or [4](../block-entity/four-outputs/index.md) possible outputs.