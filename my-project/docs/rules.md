# The rules of Order
## No bare code in modules with circular dependencies
In order to support circular dependencies, it must be guaranteed that the module in question has **no bare code that references a circular dependency**. That is to say that any code that references the circular dependency must be contained within a function.

## Module names must be unique
In order to support loading dependencies by name, Order requires that module names using this feature be unique. Otherwise, it would be impossible to know which module with that name you would like to reference. Developers that would like to use submodules with non-unique names can do so, by setting the flag `Order.IndexSubmodules` to `false`. Note that this will disable the ability to load these submodules by name.