---
name: javadoc-style-guide
description: Use when writing or reviewing Javadoc - covers coverage requirements, formatting rules, tag order, and common mistakes to avoid
---

# Javadoc Style Guide

## When to Use

- Writing new public/protected classes, methods, or fields
- Reviewing Javadoc compliance
- Fixing Checkstyle Javadoc violations

## Coverage Requirements (BLOCKING)

### Required Documentation

Javadoc is **required** for all `protected` and `public` members:
- Classes, interfaces, and enums
- Methods (including constructors)
- Fields

### Exceptions (No Javadoc Required)

- Methods annotated with `@Override` (inherited documentation applies)
- Methods annotated with `@Test` (test method names should be self-documenting)
- Private and package-private members (encouraged if complex)
- Generated code (`*.antlr` packages)

## Structure and Formatting

### Class/Interface/Enum Documentation

```java
/**
 * Brief summary sentence describing the type's purpose.
 * <p>
 * Additional paragraphs providing more detail as needed.
 *
 * @param <T>
 *          description of type parameter (indent 10 spaces from asterisk)
 */
public class Example<T> {
```

### Method Documentation

```java
/**
 * Brief summary sentence describing what the method does.
 * <p>
 * Additional detail about behavior, side effects, or usage notes.
 *
 * @param paramName
 *          description of the parameter (indent 10 spaces from asterisk)
 * @return description of return value
 * @throws ExceptionType
 *          when this exception is thrown (indent 10 spaces from asterisk)
 */
public String exampleMethod(String paramName) throws ExceptionType {
```

### Tag Order (Enforced by Checkstyle)

Tags must appear in this order:
1. `@param` (all parameters, in declaration order)
2. `@return`
3. `@throws` / `@exception`
4. `@deprecated`

### Multi-line Tag Indentation

Continuation lines must be indented 10 spaces from the asterisk:

```java
/**
 * @param config
 *          the configuration object used to initialize the component
 *          and establish default settings
 */
```

## Summary Sentence Rules

### Forbidden Patterns (Checkstyle Enforced)

Do NOT start summaries with:
- `@return the` - e.g., "Returns the value" (redundant)
- `This method returns` - redundant phrasing
- `A {@code ClassName} is a` - weak opening

### Good Examples

```java
/** Computes the hash code for the given input. */
/** Retrieves the module definition associated with this instance. */
/** Validates the configuration and throws if invalid. */
```

### Bad Examples

```java
/** This method returns the hash code. */  // BAD: "This method returns"
/** @return the hash code */  // BAD: starts with @return
/** A {@code HashComputer} is a utility class. */  // BAD: weak "is a" pattern
```

## Tag Content Requirements

### Non-Empty Descriptions Required

All tags must have meaningful descriptions:

```java
// GOOD
@param name the identifier used for lookup
@return the resolved configuration, or null if not found
@throws IllegalArgumentException if name is null or empty

// BAD - empty or trivial descriptions
@param name
@return the name
@throws IllegalArgumentException
```

### Documenting Exceptions

Document all checked exceptions and significant unchecked exceptions:

```java
/**
 * @throws IllegalArgumentException
 *          if the path is null or refers to a non-existent file
 * @throws IOException
 *          if an I/O error occurs while reading the file
 */
```

### Deprecation Documentation

When using `@deprecated`, document:
1. **Why** it is deprecated
2. **What** to use instead

```java
/**
 * Loads a module from the given path.
 *
 * @param path the file path to load
 * @return the loaded module
 * @deprecated This method does not support URI-based loading. Use
 *          {@link #loadModule(URI)} instead, which provides consistent
 *          handling of both file and classpath resources.
 */
@Deprecated
public IModule loadModule(Path path) {
```

## Override Methods and {@inheritDoc}

Override methods inherit documentation by default. Add Javadoc when:
- The implementation has behavior beyond what the parent documents
- There are additional constraints or postconditions
- The implementation throws additional exceptions
- Performance characteristics differ significantly

Use `{@inheritDoc}` to extend parent documentation:

```java
/**
 * {@inheritDoc}
 * <p>
 * This implementation additionally validates that the module has been
 * initialized before returning the definition.
 *
 * @throws IllegalStateException
 *          if the module has been closed
 */
@Override
public IDefinition getDefinition(QName name) {
```

## Inline Tags

Use inline tags for cross-references and code formatting:

| Tag | Usage |
|-----|-------|
| `{@code text}` | Code snippets, class names, method names |
| `{@link ClassName#method}` | Cross-references (rendered as links) |
| `{@linkplain ClassName text}` | Cross-references with custom text |
| `{@inheritDoc}` | Inherit documentation from parent |

## HTML in Javadoc

Use HTML sparingly:

| Element | Usage |
|---------|-------|
| `<p>` | Paragraph breaks (before new paragraphs, not after) |
| `<ul>/<li>` | Unordered lists |
| `<ol>/<li>` | Ordered lists |
| `<pre>` | Preformatted code blocks |

## Verification Commands

```bash
# Check Javadoc compliance
mvn checkstyle:check

# See all warnings
mvn checkstyle:checkstyle
# Review target/checkstyle-result.xml

# Generate full Javadoc
mvn javadoc:javadoc
```

## Quick Reference

| Do | Don't |
|----|-------|
| Start with action verb ("Computes", "Retrieves") | Start with "This method" or "Returns the" |
| Document all `@param`, `@return`, `@throws` | Leave tags empty |
| Use `{@code}` for code references | Use plain text for code |
| Indent continuation lines 10 spaces | Use inconsistent indentation |
| Add `{@inheritDoc}` when extending behavior | Repeat parent documentation verbatim |
| Document why deprecated and replacement | Just add `@deprecated` without explanation |
