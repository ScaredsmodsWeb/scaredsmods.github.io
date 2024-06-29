# Block

Now we can create the ```ModBlocks``` class in the ```block``` package. For this to work, in your main class, in this case ```ExampleMod``` there should be a field like this: 

```java

public static final String MOD_ID = "yourmodid";
```

## ModBlocks class


```java
public class ModBlocks {

    public static final Block EXAMPLE_BLOCK = registerBlock("example_block",
        new ExampleBlock(FabricBlockSettings.copyOf(Blocks.IRON_BLOCK).nonOpaque())); // (1)!
    
    private static Block registerBlock(String name, Block block) {
        registerBlockItem(name, block);
        return Registry.register(Registries.BLOCK, new Identifier(Main.MOD_ID, name), block);
    }

    private static Item registerBlockItem(String name, Block block) {
        return Registry.register(Registries.ITEM, new Identifier(Main.MOD_ID, name),
            new BlockItem(block, new FabricItemSettings()));
    }

    public static void registerModBlocks() {
        ExampleMod.LOGGER.info("Registering ModBlocks for " + ExampleMod.MOD_ID); // (2)!
    }
}

```

1. ❌ Here should be an error present, as we haven't made that class yet
2. This method can be empty and is recommended for large projects

As the names might suggest, ```registerBlock()``` and ```registerBlockItem()``` add the block the ```registerBlock()``` method is used on to the registry, by taking a name, a block and adding it as a block under your mod's modid.

## ExampleBlock block class

You can call the classes and packages whatever you want, I will call them ```block```, ```block/entities``` and ```block/custom```.
Start by creating a class I will call ```ExampleBlock``` in the ```block/custom``` package. That class should look something like this:

``` java
public class ExampleBlock extends BlockWithEntity implements BlockEntityProvider {
    private static final VoxelShape SHAPE = Block.createCuboidShape(0, 0, 0, 16, 12, 16);

    public static final MapCodec<ExampleBlock> CODEC = ExampleBlock.createCodec(ExampleBlock::new);

    public ExampleBlock(Settings settings) {
        super(settings);
    }

    @Override
    protected MapCodec<? extends BlockWithEntity> getCodec() {
        return CODEC;
    }

    @Override
    public VoxelShape getOutlineShape(BlockState state, BlockView world, BlockPos pos, ShapeContext context) {
        return SHAPE;
    }

    @Override
    public BlockRenderType getRenderType(BlockState state) {
        return BlockRenderType.MODEL;
    }

    @Nullable
    @Override
    public BlockEntity createBlockEntity(BlockPos pos, BlockState state) {
        return new ExampleBlockEntity(pos, state); // (1)!
    }

    @Override
    public void onStateReplaced(BlockState state, World world, BlockPos pos, BlockState newState, boolean moved) {
        if (state.getBlock() != newState.getBlock()) {
            BlockEntity blockEntity = world.getBlockEntity(pos);
            if (blockEntity instanceof ExampleBlockEntity) { // (2)! 
                ItemScatterer.spawn(world, pos, (ExampleBlockEntity)blockEntity); // (3)!
                world.updateComparators(pos,this);
            }
            super.onStateReplaced(state, world, pos, newState, moved);
        }
    }
 
   

    @Override
    public ActionResult onUse(BlockState state, World world, BlockPos pos, PlayerEntity player, Hand hand, BlockHitResult hit) {
        if (!world.isClient) {
            NamedScreenHandlerFactory screenHandlerFactory = ((ExampleBlockEntity) world.getBlockEntity(pos)); // (4)!

            if (screenHandlerFactory != null) {
                player.openHandledScreen(screenHandlerFactory);
            }
        }

        return ActionResult.SUCCESS;
    }

    @Nullable
    @Override
    public <T extends BlockEntity> BlockEntityTicker<T> getTicker(World world, BlockState state, BlockEntityType<T> type) {
        return validateTicker(type, ModBlockEntities.EXAMPLE_BLOCK_ENTITY, // (5)!
                (world1, pos, state1, blockEntity) -> blockEntity.tick(world1, pos, state1));
    }
}
```


1. ❌ Here should be an error present, as we haven't made that class yet
2. ❌ Here should be an error present, as we haven't made that class yet
3. ❌ Here should be an error present, as we haven't made that class yet
4. ❌ Here should be an error present, as we haven't made that class yet
5. ❌ Here should be an error present, as we haven't made that class yet