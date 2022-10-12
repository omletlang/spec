# Omlet Language Specification v1.0.0

> **O**bvious **m**inimal **l**anguag**e** **t**o end all config languages

Omlet is an extremely simple programming language, with only three datatypes: strings (the only atomic type), lists, and maps.
It is intended to be used for writing highly readable config files.

## Strings

There is only one atomic datatype: the (UTF-8-encoded) _String_.

```omlet
this is a string
```

There are 3 ways to write a String: Simple Strings, Quoted Strings, and
Block Strings.

### Simple String Syntax

Summary: Simply write the string as-is.

For example, if I wanted to express the string "John", I would write

```omlet
John
```

There are several restrictions on the types of strings you can express as a Simple String:

1. The only permitted characters are those in one of the following Unicode categories:

   1. Letter
   2. Mark
   3. Number
   4. Punctuation
   5. Symbol

   In addition, space (U+0020) is allowed.

2. The following characters `(){}[],:` are forbidden.
3. Consecutive (i.e., 2 or more adjacent) spaces are forbidden.
4. Leading and trailing spaces are forbidden (they will be ignored by the parser).

### Quoted String Syntax

Summary: Surround the string in quotes.

For example, if I wanted to express the string "John", I would write

```omlet
"John"
```

You cannot use linebreaks, `"`, or `\` without escaping them.

Escape sequences:

- `\"` produces the double quote.
- `\\` produces the backslash.
- `\n` produces a newline
- `\r` produces a carriage return
- `\u{XXXX}` produces the Unicode character given by the hexadecimal code `{XXXX}`. For example, the Quoted String `"\u0041"` is equal to `"A"` (and also to the Simple String `A`).

### Block String Syntax

Summary: Surround the string in `()`; then ident the quoted content. Indentation matters.

Block Strings are useful when you want to write lengthy text with lots of linebreaks and indentation, in a readable manner.

For example, if I wanted to express the following paragraph as a String...

```text
nats = [1, 2, 3]

for x in nats:
    y = x * x
    print(y)
```

I _could_ write it as

```omlet
"nats = [1, 2, 3]\n\nfor x in nats:\n    y = x * x\n    print(y)"
```

...but this is very hard to read. Using Block Strings, it becomes much more readable:

```omlet
(
    nats = [1, 2, 3]

    for n in nats:
        y = x * x
        print(y)
)
```

Important note: **Indentation matters**.

- Use 4 spaces, not tabs.
- The `(` must be on its own line. That is, there can only be spaces preceding it, and no characters can come after it on that line.
- The `)` must also be on its own line.
- The `(` and `)` must have the same number of preceding spaces.
- Each line in between the `(` line and the `)` line must be start with at least `n + 4` spaces, where `n` is the number of spaces that preceeded the `(`. The first `n + 4` spaces will be discarded, and any remaining spaces will be included in the resulting string.
  - For example, in the above example, the `(` is preceeded by n=0 spaces.
    Therefore, the first n+4=4 spaces are discarded for each line.
    Thus, the `print(y)` line, which has 8 spaces, will have 4 discarded,
    leaving the remaining 4 spaces included in the resulting string.

## Lists

A _List_ is an ordered sequence of values.

Syntax:

- Separate the values with `,`
- Enclose the entirety of comma-separated-values with `[` and `]`.
- Trailing commas are supported, and highly encouraged for multi-line lists.

Examples:

1.

```omlet
[foo, "bar", baz]
```

2.

```omlet
[
    foo,
    "bar",
    (
        baz
    ),
]
```

### Comma Elision

You can omit commas if values are on separate lines. Example:

```omlet
[
    foo
    "bar"
    (
        baz
    )
]
```

...is equivalent to `[foo, bar, baz]`.

## Maps

A _Map_ is an ordered sequence of key-value pairs. Each key-value pair is called an _entry_.

- Write each entry as `key: value`
  - `key` must be a String
  - `value` may be any datatype
- Separate the entries with `,`
- Enclose the entirety of comma-separated-values with `{` and `}`.
- Trailing commas are supported, and highly encouraged for multi-line maps.

Simple example:

```
{
    full name: Jane Smith,
    degrees: [
        B.S. in Computer Science from the University of Skaborfen,
        B.S. in Computer Science from the University of Mlatcad,
    ],
    family: {
        father: Rob Smith,
        mother: Susan Smith,
        husband: John Doe,
    },
}
```

### Comma Elision

You can omit commas if values are on separate lines. Example:

```omlet
{
    full name: Ann Doe
    age: 22
    family: {
        father: Bill Doe
        mother: Regina Doe
        brother: Jim Doe
    }
}
```

## File top-level values

The file's top-level value can be any type. For example, all 3 of the below files are valid Omlet files:

`my_awesome_string.omlet`:

```omlet
"Just a string :)"
```

`my_awesome_list.omlet`:

```omlet
[Top, { level: list }]
```

`my_awesome_map.omlet`:

```omlet
{
    r: 0,
    "g": 128,
    (
        b
    ): 255,
}
```

### `{}` Elision

If a file's top-level value is a `Map`, you may omit the `{}`. Example:

`package.omlet`:

```omlet
version: 1.0.0,
name: "omlet-parser",
license: "MIT",
```

...or, if we also make use of comma elision:

```omlet
version: 1.0.0
name: "omlet-parser"
license: "MIT"
```
