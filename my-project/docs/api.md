# API

## Settings
The main framework module (found in `src/shared/Order/init.lua`) has a few different settings that developers can tweak for their projects.

- `DebugMode` - When set to `true` this will make the loading process verbose so that you can iterate on the framework or tweak behavior.
- `SilentMode` - When set to `true` this will disable all regular output (version printing, notifications about when initialization has finished, etc.) This will not disable warnings. By default this is turned off during Studio development, and turned on for production.
- `ForceSyncInit` - When set to `true`, forces the task initialization process to be performed synchronously regardless of individual task settings.
- `SlowInitWarnTime` - The numbers of seconds that Order will wait before warning you that a task is taking too long to initialize.
---
## File structure

### Tasks
Tasks are modules that have code intended for immediate execution at runtime. This code should be placed in a function called `:Init()`, which is called asynchronously and automatically. Tasks should be placed in the `tasks` folder of the appropriate context.
!!! info
    Any module that is placed inside the `tasks` folder will be loaded by default, even if no `:Init()` function exists within it or if it isn't even a table. All other modules are loaded only by request.
<br>
#### Priorities
If you wish to give a task priority during the initialization phase, set a `Priority` key in the main module table to the priority level you would like (higher values load first, negative values load after default). Setting a priority is optional - if you don't set one, the task will be set to the lowest priority.
!!! warning
    Tasks with priorities are still initialized asynchronously, so if the task yields during initialization there is no guarantee that it will finish before a lower priority task starts initializing. Tasks with the same priority are initialized in an arbitrary order.
<br>
#### Synchronization
By default, tasks are initialized asynchronously. This means that if any task yields during its `:Init()` function, the next task in the initialization queue will begin to execute while it is doing so.

This behavior can be modified in two ways. The first is by setting `Order.ForceSyncInit` to `true` (see [Settings](#settings)), which forces all tasks to load synchronously, meaning that any yielding task will hold up the initialization queue. The second is by setting a `SyncInit` key to `true` in the main module table, which will tell Order to load that particular task synchronously. These settings can be useful for forcing a certain init order, but can slow down that stage at the same time.
<br>
<br>

#### Example
```lua
local NewTask = {
    Priority = 5,
    SyncInit = true,
}

function NewTask:Init()
	print("We're running a task!")
end

return NewTask
```

### Everything else
All other modules can be placed in the provided `lib` folder, or any other custom folders that you create.

### File paths
There are five top-level directories by default:

- `character` - StarterPlayer.StarterCharacterScripts
- `client` - StarterPlayer.StarterPlayerScripts
- `first` - ReplicatedFirst
- `server` - ServerScriptService
- `shared` - ReplicatedStorage.Common

!!! note
    `first` and `character` are not scanned for task modules, as it is assumed that developers will simply use LocalScripts/Scripts instead. If you wish to add tasks there, you may add custom support at the end of the main framework module. For Rojo users, further file path customization can be configured in the `default.project.json` file.
---
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
local Tweentown = shared(game:GetService("ReplicatedStorage").Common.lib.Tweentown)
```
!!! warning
    When two or more modules exist with the same name or partial path, Order will warn you that it found multiple modules for your request, and will ask you to be more specific. You can use any level of the module paths it provides you with, as long as it is unique.

As of v0.5.0, you can also now use the Order module in place of `shared` if you have other uses for that keyword, or want to avoid globals like the plague. For example:
```lua
local require = require(game:GetService("ReplicatedStorage").Common.Order)
```

### Supporting modules from other frameworks
If you would like to import a module from another framework that uses `require` to load dependencies, simply redirect `require` to `shared`:
```lua
local require = shared
```
Or, alternatively, you can redirect their require call to the main Order module:
```lua
local require = require(game:GetService("ReplicatedStorage").Common.Order)
```
