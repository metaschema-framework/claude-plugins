---
name: metapath-expressions
description: Use when writing Metapath expressions for constraints, queries, or traversals - covers path syntax, operators, functions, and data model based on XPath 3.1
---

# Metapath Expression Language

Metapath is based on [XPath 3.1](https://www.w3.org/TR/xpath-31/), adapted for Metaschema's data model. This skill covers the expression language used in Metaschema constraints and queries.

## Path Expressions

### Basic Navigation

| Expression | Description |
|------------|-------------|
| `.` | Current node (context item) |
| `..` | Parent node |
| `./child` | Child named "child" |
| `./child/grandchild` | Nested navigation |
| `.//descendant` | Any descendant named "descendant" |
| `@flag` | Flag (attribute) named "flag" |
| `//root` | From document root |

### Axis Navigation

| Axis | Description | Example |
|------|-------------|---------|
| `child::` | Direct children (default) | `child::name` = `./name` |
| `parent::` | Parent node | `parent::*` |
| `ancestor::` | All ancestors | `ancestor::catalog` |
| `ancestor-or-self::` | Ancestors plus current | `ancestor-or-self::*` |
| `descendant::` | All descendants | `descendant::item` |
| `descendant-or-self::` | Descendants plus current | `descendant-or-self::*` = `.//` |
| `self::` | Current node | `self::node()` |
| `preceding-sibling::` | Preceding siblings | `preceding-sibling::item` |
| `following-sibling::` | Following siblings | `following-sibling::item` |

### Wildcards

| Pattern | Description |
|---------|-------------|
| `*` | Any element |
| `@*` | Any flag (attribute) |
| `node()` | Any node |

## Predicates

Filter nodes using conditions in brackets:

```text
./item[@status = 'active']
./user[position() = 1]
./entry[starts-with(@id, 'X-')]
./control[exists(./part)]
./param[@id][not(@class = 'hidden')]
```

### Position Predicates

```text
./item[1]              # First item
./item[last()]         # Last item
./item[position() > 1] # All but first
./item[position() mod 2 = 0] # Even positions
```

## Operators

### Comparison Operators

| Operator | Description | Note |
|----------|-------------|------|
| `=`, `!=` | General comparison | Compares sequences |
| `eq`, `ne` | Value equality | Single values only |
| `<`, `>`, `<=`, `>=` | General comparison | Compares sequences |
| `lt`, `gt`, `le`, `ge` | Value comparison | Single values only |

**General vs Value Comparison:**
- General (`=`, `<`, etc.): Works with sequences, returns true if any pair matches
- Value (`eq`, `lt`, etc.): Requires single values, raises error on sequences

### Logical Operators

| Operator | Description |
|----------|-------------|
| `and` | Logical AND |
| `or` | Logical OR |
| `not()` | Logical NOT (function) |

### Arithmetic Operators

| Operator | Description |
|----------|-------------|
| `+` | Addition |
| `-` | Subtraction |
| `*` | Multiplication |
| `div` | Division |
| `idiv` | Integer division |
| `mod` | Modulo |

### String Operators

| Operator | Description |
|----------|-------------|
| `\|\|` | String concatenation |

### Sequence Operators

| Operator | Description |
|----------|-------------|
| `,` | Sequence construction |
| `\|` | Sequence union |
| `to` | Range (e.g., `1 to 5`) |

## Variable References

Reference variables defined with `let`:

```text
$variable-name
$parent/child
count($items)
$config/@value
```

## Function Library

**Note on function variants:** Many functions have multiple signatures with different argument counts. Optional parameters are shown with `?`. For the authoritative list of all function signatures, see the source code in:

```text
core/src/main/java/dev/metaschema/core/metapath/function/library/
```

### String Functions

| Function | Description |
|----------|-------------|
| `string(item?)` | Convert to string |
| `string-length(string?)` | Length of string |
| `normalize-space(string?)` | Normalize whitespace |
| `upper-case(string)` | Convert to uppercase |
| `lower-case(string)` | Convert to lowercase |
| `concat(str1, str2, ...)` | Concatenate strings |
| `string-join(strings, sep?)` | Join with separator |
| `substring(str, start, len?)` | Extract substring |
| `substring-before(str, search)` | Text before match |
| `substring-after(str, search)` | Text after match |
| `contains(str, search)` | Contains substring |
| `starts-with(str, prefix)` | Starts with prefix |
| `ends-with(str, suffix)` | Ends with suffix |
| `matches(str, regex)` | Regex match |
| `tokenize(str, pattern?)` | Split string |
| `compare(str1, str2)` | Compare strings (-1, 0, 1) |

### Numeric Functions

| Function | Description |
|----------|-------------|
| `abs(num)` | Absolute value |
| `ceiling(num)` | Round up |
| `floor(num)` | Round down |
| `round(num)` | Round to nearest |
| `sum(nums)` | Sum of sequence |
| `avg(nums)` | Average of sequence |
| `min(items)` | Minimum value |
| `max(items)` | Maximum value |

### Boolean Functions

| Function | Description |
|----------|-------------|
| `boolean(item)` | Convert to boolean |
| `true()` | Boolean true |
| `false()` | Boolean false |
| `not(bool)` | Logical negation |

### Sequence Functions

| Function | Description |
|----------|-------------|
| `count(items)` | Count of items |
| `empty(items)` | True if empty |
| `exists(items)` | True if not empty |
| `head(items)` | First item |
| `tail(items)` | All but first |
| `reverse(items)` | Reverse order |
| `distinct-values(items)` | Remove duplicates |
| `index-of(seq, search)` | Find positions |
| `insert-before(seq, pos, items)` | Insert items |
| `remove(seq, pos)` | Remove at position |
| `one-or-more(items)` | Assert at least one |
| `zero-or-one(items)` | Assert zero or one |
| `exactly-one(items)` | Assert exactly one |

### Node Functions

| Function | Description |
|----------|-------------|
| `name(node?)` | Qualified name |
| `local-name(node?)` | Local name |
| `namespace-uri(node?)` | Namespace URI |
| `root(node?)` | Document root |
| `path(node?)` | Path expression |
| `has-children(node?)` | Has child nodes |
| `innermost(nodes)` | Remove ancestors |
| `outermost(nodes)` | Remove descendants |
| `data(items)` | Atomize values |

### Date/Time Functions

| Function | Description |
|----------|-------------|
| `current-date()` | Current date |
| `current-time()` | Current time |
| `current-dateTime()` | Current date-time |
| `year-from-date(date)` | Extract year |
| `month-from-date(date)` | Extract month |
| `day-from-date(date)` | Extract day |
| `year-from-dateTime(dt)` | Extract year |
| `month-from-dateTime(dt)` | Extract month |
| `day-from-dateTime(dt)` | Extract day |
| `hours-from-dateTime(dt)` | Extract hours |
| `minutes-from-dateTime(dt)` | Extract minutes |
| `seconds-from-dateTime(dt)` | Extract seconds |
| `timezone-from-date(date)` | Extract timezone |
| `timezone-from-time(time)` | Extract timezone |
| `timezone-from-dateTime(dt)` | Extract timezone |
| `hours-from-time(time)` | Extract hours |
| `minutes-from-time(time)` | Extract minutes |
| `seconds-from-time(time)` | Extract seconds |
| `implicit-timezone()` | Default timezone |
| `adjust-date-to-timezone(date, tz?)` | Adjust timezone |
| `adjust-time-to-timezone(time, tz?)` | Adjust timezone |
| `adjust-dateTime-to-timezone(dt, tz?)` | Adjust timezone |
| `dateTime(date, time)` | Combine date and time |

### Duration Functions

| Function | Description |
|----------|-------------|
| `years-from-duration(dur)` | Extract years |
| `months-from-duration(dur)` | Extract months |
| `days-from-duration(dur)` | Extract days |
| `hours-from-duration(dur)` | Extract hours |
| `minutes-from-duration(dur)` | Extract minutes |
| `seconds-from-duration(dur)` | Extract seconds |

### Context Functions

| Function | Description |
|----------|-------------|
| `position()` | Current position (1-based) |
| `last()` | Total count in context |
| `static-base-uri()` | Base URI from static context |
| `base-uri(node?)` | Base URI of node |
| `document-uri(node?)` | Document URI |

### Document Functions

| Function | Description |
|----------|-------------|
| `doc(uri)` | Load document |
| `doc-available(uri)` | Check if loadable |
| `resolve-uri(relative, base?)` | Resolve URI |

### Array Functions

| Function | Description |
|----------|-------------|
| `array:size(array)` | Array length |
| `array:get(array, pos)` | Get element |
| `array:head(array)` | First element |
| `array:tail(array)` | All but first |
| `array:reverse(array)` | Reverse order |
| `array:append(array, item)` | Append item |
| `array:join(arrays)` | Concatenate arrays |
| `array:flatten(array)` | Flatten nested arrays |
| `array:insert-before(array, pos, item)` | Insert at position |
| `array:remove(array, pos)` | Remove at position |
| `array:put(array, pos, item)` | Replace at position |
| `array:subarray(array, start, len?)` | Extract subarray |

### Map Functions

| Function | Description |
|----------|-------------|
| `map:size(map)` | Number of entries |
| `map:keys(map)` | All keys |
| `map:contains(map, key)` | Has key |
| `map:get(map, key)` | Get value |
| `map:put(map, key, value)` | Add/update entry |
| `map:remove(map, keys)` | Remove entries |
| `map:entry(key, value)` | Create entry |
| `map:merge(maps)` | Merge maps |
| `map:find(item, key)` | Deep find |
| `map:for-each(map, fn)` | Transform entries |

### Higher-Order Functions

| Function | Description |
|----------|-------------|
| `function-name(fn)` | Get function name |
| `function-arity(fn)` | Get parameter count |
| `function-lookup(name, arity)` | Look up function |

### Comparison Functions

| Function | Description |
|----------|-------------|
| `deep-equal(item1, item2)` | Deep comparison |

### Metaschema-Specific Functions

| Function | Description |
|----------|-------------|
| `mp:base64-decode-text(base64Binary)` | Decode base64 to string |
| `mp:base64-encode-text(string)` | Encode string to base64 |
| `mp:recurse-depth(recursePath)` | Recursive traversal from focus |
| `mp:recurse-depth(context, recursePath)` | Recursive traversal from context |

## Common Expression Patterns

### Constraint Expressions

**Non-empty required value:**
```text
exists(.) and string-length(normalize-space(.)) > 0
```

**Date range validation:**
```text
. <= ../end-date
```

**Mutually exclusive fields:**
```text
not(exists(./option-a) and exists(./option-b))
```

**At least one of:**
```text
exists(./email) or exists(./phone)
```

**Count validation:**
```text
count(./item) >= 1 and count(./item) <= 10
```

**Pattern matching:**
```text
matches(., '^[A-Z]{2}-[0-9]{4}$')
```

**Cross-reference validation:**
```text
. = ancestor::catalog//param/@id
```

**Conditional validation:**
```text
not(exists(../end-date)) or . <= ../end-date
```

### Navigation Patterns

**Find all descendants of a type:**
```text
.//control
```

**Find siblings:**
```text
../item[@type = 'related']
```

**Find ancestor with condition:**
```text
ancestor::group[@class = 'container']
```

**Find by multiple criteria:**
```text
./param[@id][@class = 'public'][exists(./value)]
```

## Error Handling

Metapath follows XPath 3.1 error semantics:

| Error | Cause |
|-------|-------|
| Type error | Wrong type for operation |
| Cardinality error | Wrong number of items for value comparison |
| Cast error | Invalid type conversion |
| Dynamic error | Runtime evaluation failure |

## References

- [XPath 3.1 Specification](https://www.w3.org/TR/xpath-31/)
- [XPath Functions 3.1](https://www.w3.org/TR/xpath-functions-31/)
- [Metapath Specification](https://framework.metaschema.dev/specification/syntax/metapath/)
