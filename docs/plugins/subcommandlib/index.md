# SubCommandLib

## Getting started
What are subcommands you might ask? Subcommands are commands that are registered after a main, central command. Think of them as arguments of a command that are a commands themselves.
Ex: ```/deluxehub reload```

To get started with subcommand lib you need to either do 1 or 2 depending on your build system:

=== "Gradle"

    ```groovy title="In your build.gradle"
    //This should be at the top repository block
    maven {
    url "https://repo.repsy.io/mvn/scaredrabbit/scaredsplugins"
    }
    
    //This should be in the dependencies block of your build.gradle
    modImplementation "io.github.scaredsplugins:SubCommandLib:${project.subcommandlib_version}"
    ```

    ```groovy title="In your gradle.properties"
    subcommandlib_version = VERSION
    ```
    Note:
    ```groovy
    ${project.subcommandlib_version}
    ```
    Is completely optional and is not neccessary at all, just for convenience of easier version swapping.
    

=== "Maven"
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
        <version>VERSION</version>
    </dependency>
    ```
```VERSION``` & ```can either be found on [curseforge](https://www.curseforge.com/minecraft/mc-mods/more-outputs-api/files/all?page=1&pageSize=20) or on [repsy](https://repsy.io/mvn/scaredrabbit/scaredsmods/io/github/scaredsmods/moreoutputsapi/MoreOutputsAPI/)



## Adding Subcommands

To do that, we need to do a few things:

- Make a custom [manager](../subcommandlib/manager.md) class
- Make a class [extending subcommand](../subcommandlib/subcommands.md)
- [Register](../subcommandlib/registering.md) the command & main command







