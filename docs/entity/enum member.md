## Entity: Enum Member

An `Enum Member Entity` is a member defined inside an enum body.

### Supported pattern

```yaml
name: enumMemberDeclaration
```

#### Syntax: Enum member Definitions

```text
EnumMember:
    PropertyName
    PropertyName `=` EnumValue

EnumValue:
    AssignmentExpression
```

> (Not documented) The preceding `PropertyName` only
> accepts `IdentifierName` and `StringLiteral`. In
> addition, `StringLiteral` cannot be
> evaluated to base10 integers or decimals.

**Examples:**

* Default numeric enum member declaration

Default enum members will be initialized to number starting from
0, and the number is incremented in sequence.

```ts
enum Direction {
    Up,             // 0
    Down,           // 1
    Left,           // 2
    Right,          // 3
}
```

```yaml
name: defaultNumericEnumMemberDeclaration
entities:
    filter: enum member
    exact: true
    items:
        -   name: Up
            loc: [ 2, 5 ]
            value: 0
        -   name: Down
            loc: [ 3, 5 ]
            value: 1
        -   name: Left
            loc: [ 4, 5 ]
            value: 2
        -   name: Right
            loc: [ 5, 5 ]
            value: 3
```

* Numeric enum member with (partial) initializers

```ts
enum Foo {
    Alpha,          // 0, by default
    Beta = 2,       // 2, set by initializer
    Gamma,          // 3, auto-incremented
}
```

```yaml
name: numericEnumMemberInitializer
entities:
    filter: enum member
    exact: true
    items:
        -   name: Alpha
            loc: [ 2, 5 ]
            value: 0
        -   name: Beta
            loc: [ 3, 5 ]
            value: 2
        -   name: Gamma
            loc: [ 4, 5 ]
            value: 3
```

* String enum member

String literals can be used as initializers, in which case, each
member has to be constant-initialized with a string literal, or
with another string enum member.

```ts
enum Foo {
    Bar = "BAR",
    Baz = "BAZ",
    Bza = Baz,      // Refers to a previously defined member
}
```

```yaml
name: stringEnumMemberInitializer
entities:
    filter: enum member
    exact: true
    items:
        -   name: Bar
            loc: [ 2, 5 ]
            value: BAR
        -   name: Baz
            loc: [ 3, 5 ]
            value: BAZ
        -   name: Bza
            loc: [ 4, 5 ]
            value: BAZ
```

* Heterogeneous enum

Numeric literals and string literals can be mixed, but it is not
advised to do so.

```ts
enum Foo {
    Bar = 0,
    Baz = "BAZ",
}
```

```yaml
name: heterogeneousEnum
entities:
    filter: enum member
    exact: true
    items:
        -   name: Bar
            loc: [ 2, 5 ]
            value: 0
        -   name: Baz
            loc: [ 3, 5 ]
            value: BAZ
```

* Constant expressions as initializers

A constant enum expression is a subset of TypeScript expressions
that can be fully evaluated at compile time.

```ts
enum FileAccess {
    // constant members
    Read = 1 << 1,
    Write = 1 << 2,
    ReadWrite = Read | Write,
    // computed member
    G = "123".length,
}
```

```yaml
name: constantEnumExpression
entities:
    filter: enum member
    exact: true
    items:
        -   name: Read
            loc: [ 3, 5 ]
            value: 2
        -   name: Write
            loc: [ 4, 5 ]
            value: 4
        -   name: ReadWrite
            loc: [ 5, 5 ]
            value: 6
        -   name: G
            loc: [ 7, 5 ]
```

* Ambient enums

TODO

* Corner cases

```ts
enum CornerCase {
    // Valid cases
    'StringLiteral',
    '✅',
    '1e20',
    '0x123',
    '100_000',

    // Invalid cases
    '123',
    '123.456',
}
```

```yaml
name: enumCornerCases
entities:
    filter: enum member
    exact: true
    items:
        -   name: <Modified raw="StringLiteral" as="StringLiteral">
            loc: [ 3, 5 ]
            value: 0
        -   name: <Modified raw="✅" as="StringLiteral">
            loc: [ 4, 5 ]
            value: 1
        -   name: <Modified raw="1e20" as="StringLiteral">
            loc: [ 5, 5 ]
            value: 2
        -   name: <Modified raw="0x123" as="StringLiteral">
            loc: [ 6, 5 ]
            value: 3
        -   name: <Modified raw="100_000" as="StringLiteral">
            loc: [ 7, 5 ]
            value: 4
        -   name: <Modified raw="123" as="StringLiteral">
            loc: [ 10, 5 ]
            negative: true
        -   name: <Modified raw="123.456" as="StringLiteral">
            loc: [ 11, 5 ]
            negative: true
```

* Numeric literals are not allowed

[//]: # (```ts)

[//]: # (//// @no-test)

[//]: # (enum DefinitivelyIllegal {)

[//]: # (    123,)

[//]: # (    123.456,)

[//]: # (    1e2,)

[//]: # (    0x123,)

[//]: # (    0o123,)

[//]: # (    0b10,)

[//]: # (})

[//]: # (```)

These kinds of enum members are actually illegal, and will cause
parse errors or be silently omitted by some third-party parsers.
