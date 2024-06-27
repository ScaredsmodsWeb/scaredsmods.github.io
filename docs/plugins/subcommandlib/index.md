# SubCommandLib

## Getting started
What are subcommands you might ask? Subcommands are commands that are registered after a main, central command. Think of them as arguments of a command that are a commands themselves.
Ex: ```/deluxehub reload```

To get started with subcommand lib you need to either do 1 or 2 depending on your build system:

### Gradle

```groovy
//This should be at the top repository block
maven {
   url "https://repo.repsy.io/mvn/scaredrabbit/scaredsplugins"
}
```
```groovy
//This should be in the dependencies block of your build.gradle
modImplementation "io.github.scaredsplugins:SubCommandLib:${project.subcommandlib_version}"
```
Note:
```groovy
${project.subcommandlib_version}
```
Is completely optional and is not neccessary at all, just for convenience of easier version swapping.
If you do decide to do this, you should add this to your ```gradle.properties```:
```groovy
//{VERSION} is the version you chose on curseforge/github (Modrinth coming soon)
subcommandlib_version = {VERSION}
```

### Maven
```xml
 <repository>
    <id>repsy</id>
    <url>https://repo.repsy.io/mvn/scaredrabbit/scaredsmods</url>
 </repository>
```
```xml
 <dependency>
   <groupId>io.github.scaredsplugins</groupId>
   <artifactId>SubCommandLib</artifactId>
   <version>{VERSION}</version>
 </dependency>
```
{VERSION} is the version you chose on curseforge/github (Modrinth coming soon)

## Adding Subcommands

### Preparation
Before you can add a subcommand, you need to have a central manager that collects all subcommmands
Create a new class and let it extend either extends ```CommandManager``` or ```TabCompletionCommandManager```. The difference is that ```TabCompletionCommandManager``` subcommands are visible when typing them in the chat. 
Create a new, empty constructor.
Ex.:

```java
public class SomeManager extends TabCompletionCommandManager {

    public SomeManager() {
        
    }
}
```

### Making new subcommands

Then create a new class ```SomeCommand.java``` and make it extend ```SubCommand```. Implement all the neccessary methods your IDE asks for. It should look something like this:

```java
import io.github.scaredsplugins.subcommandlib.api.command.SubCommand;
import org.bukkit.entity.Player;

import java.util.List;

public class TestCommand extends SubCommand {
    @Override
    public String getName() {
        return "";
    }

    @Override
    public String getDescription() {
        return "";
    }

    @Override
    public String getSyntax() {
        return "";
    }

    @Override
    public void perform(Player player, String[] strings) {

        
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



### Registering the main command
The last two things you need to is:

1. Register the subcommand to the Manager class

2. Register the main command

To register the subcommand to the command, return to the class you created at the beginning.
In the constructor add this:

```java
getSubCommands().add(new TestCommand());
```
The manager class should look a bit like this:

```java
public class SomeManager extends TabCompletionCommandManager {

    public Manager() {
        getSubCommands().add(new TestCommand());
        [... Any other subcommands ...]
    }
}
```
```getSubCommands()``` is a method that returns a ArrayList<SubCommand> and adds them to the ```TabCompletionCommandManager``` because we extend from that class. 

To register the main command, go to the class that extends ```JavaPlugin```.
In the ```onEnable()``` method, you should add ```getCommand("example").setExecutor(new SomeManager());```.

- example should be the first command you type in. Ex.: ```/dhub```
- by doing ```new SomeManager()``` we simply say: Hey server, here you have a class that can execute multiple commands. As specified above, in the constructor the subcommands are added to the constructor. So by calling the constructor, you basically say that all the subcommands should be added alongside the main command.

The last to do will be adding the command to the ```plugin.yml``` file
Go to ```resources/plugin.yml```, and add this (you probably already know this, and should know it):

```yaml
commands:
  example:
    description: Example
```
```example``` should be the string you provided in ```onEnable()```


### Finishing up
Repeat these steps for more subcommands

Have a question? You are allowed to ask questions in the issues of the github repository. Or DM me on discord, my username is ```scaredrabbitnl_```

