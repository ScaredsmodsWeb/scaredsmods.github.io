# Adding Subcommands



### Making new subcommands

Then create a new class ```SomeCommand.java``` and make it extend ```SubCommand```. Implement all the neccessary methods your IDE asks for. It should look something like this:

```java
import io.github.scaredsplugins.subcommandlib.api.command.SubCommand;
import org.bukkit.entity.Player;

import java.util.List;

public class ExampleCommand extends SubCommand {
    @Override
    public String getName() {
        return "example";
    }

    @Override
    public String getDescription() {
        return "This is an example!";
    }

    @Override
    public String getSyntax() {
        return "/exampleplugin example <your-provided-subcommand-arguments>";
    }

    @Override
    public void perform(Player player, String[] strings) {

        player.sendMessage("This is an example!")
    }

    @Override
    public List<String> getSubcommandArguments(Player player, String[] strings) {
        return null;
    }
}
```
Remember that the default return value of ```getSubcommandArguments(Player, player, String[] strings)``` is ```List.of()```. If you don't plan on adding arguments for the subcommands, let ```getSubcommandArguments(Player, player, String[] strings)``` return ```null```.

- The ``getName()`` should return a string containing the name of your *****subcommand***
- The ``getDescription()`` is the description of the command that will appear when doing /help
- The ``getSyntax()`` method is what you get sent in chat when entering the command wrong or when doing /help. Generally: How the command should be used.
- The ``perform()`` method is what will be executed when the subcommand is run. For example: ```/example reload``` reloads the ```example``` plugin configs.