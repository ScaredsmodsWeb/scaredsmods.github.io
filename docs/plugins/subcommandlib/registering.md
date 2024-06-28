### Registering the main command
The last two things you need to is:

1. Register the subcommand to the Manager class

2. Register the main command

To register the subcommand to the command, return to the class you created at the beginning.
In the constructor add this:

```java
getSubCommands().add(new ExampleCommand());
```
The manager class should look a bit like this:

```java
public class ExampleManager extends TabCompletionCommandManager {

    public ExampleManager() {
        getSubCommands().add(new ExampleCommand());
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

