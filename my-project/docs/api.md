# API

## File structure

### Tasks
Tasks are modules that have code intended for immediate execution at runtime. This code should be placed in a function called `:Init()`, which is called asynchronously and automatically. Tasks should be placed in the `tasks` folder of the appropriate context.
!!! info
    Any module that is placed inside the `tasks` folder will be loaded by default, even if no `:Init()` function exists within it or if it isn't even a table. All other modules are loaded only by request.
#### Priorities
If you wish to give a task priority during the initialization phase, set a `Priority` key in the main module table to the priority level you would like. Setting a priority is optional - if you don't set one, the task will be set to the lowest priority.
!!! warning
    Tasks with priorities are still initialized asynchronously, so if the task yields during initialization there is no guarantee that it will finish before a lower priority task starts initializing. Tasks with the same priority are initialized in an arbitrary order.
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

Note that these two optional directories are not scanned for modules. If you wish to do so, you may add custom support in the default client or server script. For Rojo users, further file path customization can be configured in the `default.project.json` file.

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