# Screen


## Prerequisites
For the screen handler, we need a class extending ```ScreenHandler```. In action it would look like this:

```java title="ExampleScreenHandler empty"
public class ExampleScreenHandler extends ScreenHandler {

}
```

Your IDE will now ask for a few methods, hover over the class (above) and implement the methods asked for but don't create the constructor matching super yet. We are going to do that manually.

But first, we need a few fields:

```java title="Fields"

private final Inventory inventory;//(1)!
private final PropertyDelegate propertyDelegate;
public final ExampleBlockEntity blockEntity;//(2)!

```

1. This is the inventory that represents the block entity inventory
2. This is the actual block entity

## Constructors (matching super)
Now we can actually create the what is really two constructors so lets go!

```java title="2 Constructors"

public ExampleScreenHandler(int syncId, PlayerInventory inventory, PacketByteBuf buf) {
    this(syncId, inventory, inventory.player.getWorld().getBlockEntity(buf.readBlockPos()),
            new ArrayPropertyDelegate(3));//(1)!
}


public ExampleScreenHandler(int syncId, PlayerInventory playerInventory,
                                BlockEntity blockEntity, PropertyDelegate arrayPropertyDelegate) {
    super(ModScreenHandlers.EXAMPLE_SCREEN_HANDLER, syncId);
    checkSize(((Inventory) blockEntity), 2);
    
    this.inventory = ((Inventory) blockEntity);
    inventory.onOpen(playerInventory.player);
    this.propertyDelegate = arrayPropertyDelegate;
    this.blockEntity = ((ExampleBlockEntity) blockEntity);

    this.addSlot(new Slot(inventory, 0, 80, 11));
    this.addSlot(new Slot(inventory, 1, 62, 59));
    this.addSlot(new Slot(inventory, 2, 98,59));

    //If you have more slots, add them here
    
    /* 3rd output slot
    this.addSlot(new Slot(inventory, 3, x ,y));
    */

   /* 4th output slot
   this.addSlot(new Slot(invenotry, 4, x, y));
   */
   //(2)!
    addPlayerInventory(playerInventory);
    addPlayerHotbar(playerInventory);

    addProperties(arrayPropertyDelegate);
}
```

1. This is also how big your inventory is, just like in the block entity class. This should match the sizes specified in your block entity. For two outputs it is 3, for three outputs it is 4 and for 4 outputs it is 5 because we also have an input slot.
2. From here, there are a few errors because we haven't created those methods yet.


## Required methods
The methods that are absolutely required, are the following:

```java title="Required methods"
@Override
public ItemStack quickMove(PlayerEntity player, int invSlot) {
    ItemStack newStack = ItemStack.EMPTY;
    Slot slot = this.slots.get(invSlot);
    if (slot != null && slot.hasStack()) {
        ItemStack originalStack = slot.getStack();
        newStack = originalStack.copy();
        if (invSlot < this.inventory.size()) {
            if (!this.insertItem(originalStack, this.inventory.size(), this.slots.size(), true)) {
                return ItemStack.EMPTY;
            }
        } else if (!this.insertItem(originalStack, 0, this.inventory.size(), false)) {
            return ItemStack.EMPTY;
        }

        if (originalStack.isEmpty()) {
            slot.setStack(ItemStack.EMPTY);
        } else {
            slot.markDirty();
        }
    }

    return newStack;
}

@Override
public boolean canUse(PlayerEntity player) {
    return this.inventory.canPlayerUse(player);
}

private void addPlayerInventory(PlayerInventory playerInventory) {
    for (int i = 0; i < 3; ++i) {
        for (int l = 0; l < 9; ++l) {
            this.addSlot(new Slot(playerInventory, l + i * 9 + 9, 8 + l * 18, 84 + i * 18));
        }
    }
}

private void addPlayerHotbar(PlayerInventory playerInventory) {
    for (int i = 0; i < 9; ++i) {
        this.addSlot(new Slot(playerInventory, i, 8 + i * 18, 142));
    }
}
```
This will make sure your hotbar and inventory will be visible from the inventory of our block entity. It loops through every slot you have and adds that slot on a certain position.


## Non-required but recommended methods

These two methods will help render a progress arrow for your recipe, it will be a nice thing for players to have.

```java title="Progress arrow methods"
public boolean isCrafting() {
    return propertyDelegate.get(0) > 0;
}

public int getScaledProgress() {
    int progress = this.propertyDelegate.get(0);
    int maxProgress = this.propertyDelegate.get(1);  // Max Progress
    int progressArrowSize = 26; // This is the width in pixels of your arrow

    return maxProgress != 0 && progress != 0 ? progress * progressArrowSize / maxProgress : 0;
}
```

Now we can create the actual screen!


[ExampleScreen](../screen/examplescreen.md)