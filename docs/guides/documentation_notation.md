This section defines the data type notations and name prefixes used across the project and its documentation. Following these conventions helps you understand which fields are editable, what values they accept, and how the system handles them internally.

## Data Fields

### Types

- `(S) String`
  : A sequence of characters (e.g., `"Hello"`)
- `(C) Char`
  : A single character (e.g., `'H'`)
- `(E) Enum`
  : A defined set of named values (e.g., `'top'` and `'bottom'`)
- `(B) Boolean`
  : `true` or `false`
- `(I) Integer`
  : A whole number (e.g., `-1` or `5`)
- `(D) Double`
  : A floating-point number (e.g., `3.14`)
- `[.] List Object`
  : An ordered list of elements
- `{.} Dict Object`
  : A key-valued dictionary of elements
- `<.> Custom Object`
  : A custom-structured object of elements


### Type Prefixes

These prefixes are prepended in front of the data field type (e.g., `~(I)` or `$(I)`).

- `x`
  : Regular field
- `~x`
  : Field accepts a range or fixed value
- `$x`
  : Internal-use only. Do not modify


## Functions

### Function Folder Names

- `.x`
  : Regular function folder
- `_x_`
  : Special function folder with a specific task (e.g., `_init_` for data initialization)
- `-x-`
  : Export function folder which holds the internal functions of an exported API function (e.g., `-get_uuid-`)

### Function Names

- `x`
  : Regular function
- `_x_`
  : Special function function with a specific task (e.g., `_main_` as a central function entry point) 