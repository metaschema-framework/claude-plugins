---
name: unit-test-writing
description: Use when writing or expanding unit tests - provides edge case checklist, test structure patterns, and coverage workflow
---

# Unit Test Writing Skill

## When to Use

- Writing new unit tests
- Expanding test coverage on existing code
- Reviewing test completeness

## Edge Case Checklist

**Create TodoWrite items for each applicable category:**

| Category | Examples | Check |
|----------|----------|-------|
| Empty/Null | Empty string, null reference, empty collection | ☐ |
| Boundaries | Zero, negative, max int, min int, boundary values | ☐ |
| Invalid | Wrong type, malformed input, out of range | ☐ |
| Missing | Required field absent, partial data, missing nested element | ☐ |
| Duplicates | Repeated values, duplicate keys | ☐ |
| Ordering | First, last, middle, unsorted | ☐ |
| Concurrency | Race conditions, thread safety (if applicable) | ☐ |
| State | Uninitialized, already processed, closed | ☐ |
| Combinations | Multiple flags, conflicting options | ☐ |

## Configuration/Parsing Edge Cases

When testing XML/JSON/YAML configuration loading, always test:

| Scenario | Test Case | Expected |
|----------|-----------|----------|
| Missing optional child | Parent element exists, optional child absent | Null or default value |
| Empty element | `<element/>` or `<element></element>` | Empty string or null |
| Unknown module | Query from module not in config | Null, not exception |
| Unknown definition | Query definition not in config | Null, not exception |
| Partial config | Some fields present, others missing | Only specified fields set |
| Multiple types | Same pattern for different parent types (assembly vs field) | Both work correctly |

**Example edge case test structure:**

```java
@Test
void testMissingOptionalElement() throws IOException {
    // Load config with element that has no optional child
    config.load(new File("edge-cases.xml"));

    // Query should return null, not throw
    IPropertyBindingConfiguration result = config.getPropertyBindingConfiguration(definition, "missing-child");
    assertNull(result, "Missing optional element should return null");
}
```

## Coverage Expansion Workflow

When touching existing code:

### Step 1: Assess Current Coverage

```bash
# Check if tests exist
find . -name "*Test.java" | xargs grep -l "ClassName"

# Run with coverage (if configured)
mvn -pl module test jacoco:report
```

### Step 2: Identify Gaps

Review the code and ask:
- What error conditions aren't tested?
- What boundary values aren't tested?
- What edge cases are missing?

### Step 3: Create Test Plan

Use TodoWrite to track each test to add:

```text
- [ ] Test: returns error when input is null
- [ ] Test: returns error when input is empty
- [ ] Test: handles maximum allowed value
- [ ] Test: throws on negative input
```

### Step 4: Write Tests

For each test, follow this structure:

```java
@Test
@DisplayName("descriptive name of scenario")
void descriptiveMethodName() {
    // Arrange - set up test data and dependencies

    // Act - execute the code under test

    // Assert - verify the expected outcome
}
```

## Test Naming Convention

Use names that describe the scenario and expected outcome:

```java
// Pattern: [action]When[condition] or [expectedResult]When[condition]

// Good
void returnsErrorWhenRequiredArgumentMissing()
void throwsOnMultipleInvalidOptions()
void returnsEmptyWhenNoTargetCommand()

// Bad
void testValidate()
void test1()
void testMethod()
```

## Test Fixture Patterns

### Focused Fixtures

Create dedicated test classes for specific scenarios:

```java
/**
 * A test command that requires an extra argument.
 */
class TestCommandWithRequiredArg extends AbstractTerminalCommand {
    // Focused on one scenario
}
```

### Helper Methods

Create reusable setup methods:

```java
@NonNull
private CallingContext createContext(@NonNull String... args) {
    return new CallingContext(processor, Arrays.asList(args));
}
```

### Nested Test Classes

Group related tests with `@Nested`:

```java
@Nested
@DisplayName("validateExtraArguments()")
class ValidateExtraArgumentsTests {

    @Test
    @DisplayName("returns empty when no target command")
    void returnsEmptyWhenNoTargetCommand() { }

    @Test
    @DisplayName("returns error when required argument missing")
    void returnsErrorWhenRequiredArgumentMissing() { }
}
```

## Integration with TDD

When following TDD, prioritize tests in this order:

1. **Edge cases first** - They reveal design issues early
2. **Error/failure cases** - Often undertested
3. **Happy path last** - Most obvious case

## Quick Reference

| Do | Don't |
|----|-------|
| Test edge cases thoroughly | Test getters/setters |
| Test error paths | Test trivial constructors |
| Use descriptive test names | Use vague names like `test1` |
| Create focused test fixtures | Create god-object test utilities |
| Group related tests with `@Nested` | Mix unrelated tests in one class |
| Use `@DisplayName` for clarity | Rely only on method names |
