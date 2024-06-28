# Making a custom manager class

Before you can add a subcommand, you need to have a central manager that collects all subcommmands
Create a new class and let it extend either extends ```CommandManager``` or ```TabCompletionCommandManager```. The difference is that ```TabCompletionCommandManager``` subcommands are visible when typing them in the chat. 
Create a new, empty constructor.

```java
public class ExampleManager extends TabCompletionCommandManager {

    public ExampleManager() {
        
    }
}
```

If you want to make custom logic for the ```onTabComplete()``` & ```onCommand()``` methods, which are required to work, override them to apply that code to my code.