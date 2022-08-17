# API

## File structure

### Tasks
Tasks are modules that have code intended for immediate execution at runtime. This code should be placed in a function called `:Init()`. Tasks should be placed in the `tasks` folder of the appropriate context.
!!! note
    The `:Init()` function is called asynchronously. This means that it is an ideal location to put any `:WaitForChild()` calls, as these will not slow down the initialization of other modules in your project.
#### Example
```lua
local NewTask = {}

function NewTask:Init()
	print("We're running a task!")
end

return NewTask
```

### Everything else
All other modules should be placed in the `lib` folder, or any other custom folders that you create. Note that by default, Order will only recognize top-level modules in a folder, but those can be buried as many folders deep as you'd like.

## Loading dependencies
Order makes use of the `shared` global variable to load dependencies. In code, this takes the place of the usual `require` keyword. This function can take either a string of the module's name, or a direct object reference to a ModuleScript.
For example:
```lua
local Tweentown = shared("Tweentown")
```

### Supporting modules from other frameworks
If you would like to import a module from another framework that uses `require` to load dependencies, simply add the following line to the beginning of the file:
```lua
local require = shared
```