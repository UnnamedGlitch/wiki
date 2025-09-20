An API function serves as a wrapper around a submodule function, encapsulating its core functionality into a single function call. It provides a clean and user-friendly interface, allowing framework users to interact with complex internal logic more easily. In addition to simplifying access, each API function offers detailed information on its behavior and proper usage without having to consult this documentation.

All API functions follow a strict execution sequence:
1. Input — Receive input parameters
2. Stack Push — Initialize new function call context
3. Function Call — Execute the submodule's main entry function
4. Stack Pop — Remove old function  call context
5. Output — Provide access to the return values

This sequence is also exemplified in the API function template used throughout the API submodule:

<details style="border: 1px solid #ccc; padding: 10px; border-radius: 5px;">
<summary>📄 API Function Template</summary>

</br>

API functions in this framework always follow a standardized template, which extends the basic function documentation found in every `.mcfunction` file. In addition to the standard `description` field and execution context providers (`as`, `at`, and `macros`), the template also includes specifications for the function’s optional `input` and `output` fields.

```yaml
Template Format
├──(🇸) Description: A short explanation of the exported function's purpose, effects and contingencies.
├──<.> Input / Output: Specification of all input / output fields.
│   ├──(🇪) source: Data source type. Can be one of '(SC)' for score or '(ST)' for storage.
│   ├──(🇸) name: Name of the score or storage location the data is in.
│   └──(🇨) -: '>'
│       ├──(🇪) type: The data type of the field. Can be one of 'Integer', 'Decimal' or 'String'.
│       ├──(🇨) -: '∈'
│       ├──<.> range: The value range of the field. Can be a set, interval or regex.
│       └──<.> info: Type specific information about the field.
│           └──<.>: 'Decimal'
│               └──(🇪) precision: The fixed amount of decimal points. Can be one of 'Px' with x ∈ ℕ.
├──{.} as: Data of the executing entity
│   ├──(🇸) type: The entity type.
│   ├──{.} nbt: NBT entries and values.
│   └──{.} scores: Scoreboard entries with fixed value or range.
├──{.} at: Positional execution context.
│   ├──(🇸) dim: Dimension.
│   ├──{.} pos: Coordinate position.
│   │   ├──(🇮) _x: X-Coordinate.
│   │   ├──(🇮) _y: Y-Coordinate.
│   │   └──(🇮) _z: Z-Coordinate.
│   └──{.} rot: Rotational orientation.
│       ├──(🇮) _pitch: Pitch rotation.
│       └──(🇮) _yaw: Yaw rotation.
└──[.] macros: List of available macros.
```

```
####################################################################################################
#
# Description:
# ...
#
#
# Input:
#   ...
#
# Output:
#   ...
#
#
# as: {
#   type:    ""
#   nbt:     {}
#   scores:  {}
# }
# at: {
#   dim:  ""
#   pos:  {}
#   rot:  {}
# }
# macros: []
####################################################################################################


#~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ Stack Push ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~#
data modify storage uf_api:run return set value {}
data modify storage uf_api:run stack append value {}
#~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~#

#~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ Call ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~#
...
#~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~#

#~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ Stack Pop ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~#
...
data remove storage uf_api:run stack[-1]
#~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~#
```
</details>

Now, before each step of the execution sequence can be introduced, it is necessary to establish the central memory system. It provides the necessary execution context for the internal submodule functions to operate correctly and will be frequently referenced in the sections that follow.

```yaml
uf_api:run
├──[.] stack: Per-function memory space used for temporary data storage.
├──{.} heap: Global memory space used for permanent data storage.
│   ├──{.} args: Arguments field used to pass string arguments to functions.
│   └──{.} ioc: Internal Operations & Commands (IOC) field for function macros.
└──{.} return: Global memory space used for function return values.
``` 

### Input & Output

For proper functionality, this framework must support two basic data types: `Strings` and `Integers`. All other data types can be interpreted as `Strings` (e.g., `Enum`, `Char`) or `Integers` (e.g., `Boolean`, `Double`) by employing mapping structures (e.g.,`Boolean` as `true = 1` and `false = 0`) or documented conventions (e.g., `Double` represented as an `Integer` with a fixed decimal point).

In this framework, `Integers` are always provided using the scoreboard. Each API function is assigned a single scoreboard objective and can receive or return values by varying the scoreboard player name, using the following format:

| Category    | Scoreboard Player Format| Purpose                         |
|-------------|-------------------------|---------------------------------|
| Input       | $UF.INP.                | Receive caller input parameters |
| Temporary   | $UF.TMP.                | Internal calculations           |
| Output      | $UF.OUT.                | Provide access to return values |

In contrast, `Strings` are supplied through the memory system via the heap (see above), since the stack entry for the called function is not yet initialized during input setup at runtime. Output `Strings` are returned through the dedicated `return` field of the memory system, which persists after the function call completes.

> 💡 **Note:** This approach provides the simplest way to bypass the stack restriction without having to temporarily store the data elsewhere and copy it over once the stack entry becomes available. Although these values are stored on the heap, they are always cleaned up after the function call completes, maintaining the same lifecycle as stack-based data.

### Stack Push

To manage temporary memory allocation for each API function call and prevent data overwriting from outer calls, the framework employs a stack-based memory model.

When an API function is invoked, the framework appends a new, empty NBT object to the stack &mdash; a list that represents the call stack. This action creates a new, isolated stack frame that the current function can exclusively access via `stack[-1]`.

This approach ensures two critical properties:

1. **Data Isolation**: Each function call operates on its own stack frame, preventing interference with data belonging to other active calls.
2. **Consistent Access**: By always referencing the current frame through the fixed index `-1`, the function maintains a stable and uniform interface to its temporary memory.

Additionally, to ensure consistent handling of return values, the memory system’s `return` field is reset to an empty NBT object during this step.

> 💡 Note: To optimize performance, the API function may omit parts of this initialization step if no temporary memory allocation or return value assignment is required. Consequently, certain operations in the corresponding _Stack Pop_ phase may also be skipped.

### Function Call

This function call yields execution to the main entry point of the submodule’s functions.

### Stack Pop

Once function execution has completed, the input and temporary scores must be reset, and the current stack entry must be popped from the stack.

> 💡 **Note:** If the _Stack Push_ step was partially omitted (e.g., because no temporary memory was needed), the corresponding parts of this step may also be skipped.