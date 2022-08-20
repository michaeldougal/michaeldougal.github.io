# API

## File structure

### Tasks
Tasks are modules that have code intended for immediate execution at runtime. This code should be placed in a function called `:Init()`. Tasks should be placed in the `tasks` folder of the appropriate context.
!!! note
    The `:Init()` function is called asynchronously. This means that it is an ideal location to put any `:WaitForChild()` calls, as these will not slow down the initialization of other modules in your project.
!!! info
    Any module that is placed inside the `tasks` folder will be loaded by default, even if no `:Init()` function exists within it or if it isn't even a table. All other modules are loaded only by request.
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

### File paths
There are three top-level directories by default:

- `client` - StarterPlayer.StarterPlayerScripts
- `server` - ServerScriptService
- `shared` - ReplicatedStorage.Common

Order also supports two other top-level directories, if you wish to create/use them:

- `first` - ReplicatedFirst
- `character` - StarterPlayer.StarterCharacterScripts

For Rojo users, further file path customization can be configured in the `default.project.json` file.

## Loading dependencies
Order makes use of the `shared` global variable to load dependencies. In code, this takes the place of the usual `require` keyword. This function can take either a string of the module's name, a string of a partial or complete path to the module, or a direct object reference to a ModuleScript.
Valid examples:
```lua
local Tweentown = shared("Tweentown")
```
```lua
local Tweentown = shared("lib/Tweentown")
```
```lua
local Tweentown = shared(game:GetService("ReplicatedStorage"):WaitForChild("Common"):WaitForChild("lib"):WaitForChild("Tweentown"))
```
!!! warning
    When two or more modules exist with the same name or partial path, Order will warn you that it found multiple modules for your request, and will ask you to be more specific. You can use any level of the module paths it provides you with, as long as it is unique.

### Supporting modules from other frameworks
If you would like to import a module from another framework that uses `require` to load dependencies, simply add the following line to the beginning of the file:
```lua
local require = shared
```