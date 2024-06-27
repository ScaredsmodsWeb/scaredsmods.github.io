# More Ouputs API


## Version scheme for More Outputs API

Note: it isnt worth backporting to version prior to 1.18 and certainly not prior to 1.14 so those versions won't be backported. Where ```x``` stands for the update of the mod & ```y``` stands for minecraft version suffix and/or bugfixes. 

Minecraft Version | Mod Version| Mod Loader | Status
------------ | ------------- | --------- |------------
< 1.18 | -  | Fabric| *skipped*
1.18.x | 0.2.y | Fabric | *in development*
1.19.x | 0.5.y | Fabric | *in development*
1.20.x | 1.x.y | Fabric | *current version*
1.21.x | 2.x.y | Fabric | *to be updated*

## Usage
I would assume that you already know most of the things we are about to do, but for those that don't know, here is the tutorial!

### What we'll need

- A custom block
- A (custom) block entity
- A custom recipe of course
- A screenhandler for the block entity

### Block (Entity)

You can call the classes and packages whatever you want, I will call them ```block```, ```block/entities``` and ```block/custom```.
Start by creating a class I will call ```ExampleBlock``` in the ```block/custom``` package. That class should look something like this:
``` java
public class SomeBlock extends BlockWithEntity implements BlockEntityProvider {
    private static final VoxelShape SHAPE = Block.createCuboidShape(0, 0, 0, 16, 12, 16);

    public static final MapCodec<SomeBlock> CODEC = SomeBlock.createCodec(SomeBlock::new);

    public SomeBlock(Settings settings) {
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
        return new SomeBlockBlockEntity(pos, state); // (1) 
    }

    @Override
    public void onStateReplaced(BlockState state, World world, BlockPos pos, BlockState newState, boolean moved) {
        if (state.getBlock() != newState.getBlock()) {
            BlockEntity blockEntity = world.getBlockEntity(pos);
            if (blockEntity instanceof SomeBlockBlockEntity) { // (2) 
                ItemScatterer.spawn(world, pos, (SomeBlockBlockEntity)blockEntity); // (3)
                world.updateComparators(pos,this);
            }
            super.onStateReplaced(state, world, pos, newState, moved);
        }
    }
 
   

    @Override
    public ActionResult onUse(BlockState state, World world, BlockPos pos, PlayerEntity player, Hand hand, BlockHitResult hit) {
        if (!world.isClient) {
            NamedScreenHandlerFactory screenHandlerFactory = ((GemPolishingStationBlockEntity) world.getBlockEntity(pos)); // (4)

            if (screenHandlerFactory != null) {
                player.openHandledScreen(screenHandlerFactory);
            }
        }

        return ActionResult.SUCCESS;
    }

    @Nullable
    @Override
    public <T extends BlockEntity> BlockEntityTicker<T> getTicker(World world, BlockState state, BlockEntityType<T> type) {
        return validateTicker(type, ModBlockEntities.GEM_POLISHING_STATION_BLOCK_ENTITY, // (5)
                (world1, pos, state1, blockEntity) -> blockEntity.tick(world1, pos, state1));
    }
}
```


1. ❌ Here should be an error present, as we haven't made that class yet
2. ❌ Here should be an error present, as we haven't made that class yet
3. ❌ Here should be an error present, as we haven't made that class yet
4. ❌ Here should be an error present, as we haven't made that class yet
5. ❌ Here should be an error present, as we haven't made that class yet


Now we can create the ```ModBlocks``` class in the ```block``` package. For this to work, in your main class, in this case ```Main``` there should be a field like this: 

```java

public static final Logger LOGGER = LoggerFactory.getLogger("yourmodid");
```


```java
public class ModBlocks {

    public static final Block SOME_BLOCK = registerBlock("example_block",
        new SomeBlock(FabricBlockSettings.copyOf(Blocks.IRON_BLOCK).nonOpaque()));
    
    private static Block registerBlock(String name, Block block) {
        registerBlockItem(name, block);
        return Registry.register(Registries.BLOCK, new Identifier(Main.MOD_ID, name), block);
    }

    private static Item registerBlockItem(String name, Block block) {
        return Registry.register(Registries.ITEM, new Identifier(Main.MOD_ID, name),
            new BlockItem(block, new FabricItemSettings()));
    }

    public static void registerModBlocks() {
        Main.LOGGER.info("Registering ModBlocks for " + Main.MOD_ID); // (1)
    }
}

```

1. This method can be empty and is recommended for large projects

Test test