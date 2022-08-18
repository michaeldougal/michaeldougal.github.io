# Some special notes
## No bare code in modules with circular dependencies
In order to support circular dependencies, it must be guaranteed that the module in question has **no bare code that references a circular dependency**. That is to say that any code that references the circular dependency must be contained within a function. If Order detects this bare code, you will be notified with a warning in the output and no operation will be processed on the module in question.

## Loading modules with non-unique names
Modules can be specified through several different paths. If the name is unique, you can reference it simply through that. If two modules exist with the same name however, Order will warn you that it found two or more possible modules for your request, and will ask you to be more specific. You can use any level of the module paths it provides you with, as long as they're unique as well.

## Printing module tables
One of the complications with the method Order uses to support circular dependencies is that if you try to print out one of these module's tables, it would return a table address instead of an output-friendly table. In order to preserve as much functionality as possible, Order will list out table keys and values in a custom format when converting the table to a string.