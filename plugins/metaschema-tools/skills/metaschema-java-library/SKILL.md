---
name: metaschema-java-library
description: Use when working with metaschema-java library code - covers key interfaces, exception hierarchy, Metapath evaluation, constraint validation, and serialization/deserialization
---

# Using the metaschema-java Library

This skill covers how to use the metaschema-java library for loading modules, evaluating Metapath expressions, validating constraints, and serializing/deserializing data.

**Related skills from the `metaschema` plugin** (install with `claude plugin install metaschema-framework:metaschema`):
- `metaschema-module-authoring` - Module structure, definitions, data types
- `metaschema-constraints-authoring` - Constraint types and validation patterns
- `metapath-expressions` - Metapath syntax, operators, and functions

## Module Structure

```text
metaschema-framework (parent)
├── core                    - Core API, Metapath engine, model interfaces
├── databind                - Data binding, serialization, code generation
├── schemagen               - XML/JSON schema generation
├── databind-modules        - Metaschema binding modules
├── metaschema-maven-plugin - Maven plugin for code/schema generation
├── metaschema-testing      - Testing utilities
├── cli-processor           - CLI framework
└── metaschema-cli          - Command-line interface
```

## Key Packages

| Package | Purpose |
|---------|---------|
| `dev.metaschema.core.model` | Model interfaces (`IModule`, `IAssemblyDefinition`, etc.) |
| `dev.metaschema.core.metapath` | Metapath expression engine |
| `dev.metaschema.core.metapath.function` | Function library |
| `dev.metaschema.core.metapath.item` | Item types (nodes, atomics) |
| `dev.metaschema.core.model.constraint` | Constraint validation |
| `dev.metaschema.databind` | Data binding context |
| `dev.metaschema.databind.io` | Serialization/deserialization |
| `dev.metaschema.databind.model.annotations` | Binding annotations (`@BoundField`, `@BoundChoice`, etc.) |
| `dev.metaschema.databind.codegen` | Code generation from Metaschema modules |

## Annotation-Based Binding Model

The databind module provides annotation-based bindings that map Java classes to Metaschema definitions.

### Core Binding Annotations

| Annotation | Purpose |
|------------|---------|
| `@MetaschemaAssembly` | Marks a class as a bound assembly definition |
| `@MetaschemaField` | Marks a class as a bound field definition |
| `@BoundAssembly` | Marks a field as a bound assembly instance |
| `@BoundField` | Marks a field as a bound field instance |
| `@BoundFlag` | Marks a field as a bound flag instance |
| `@BoundChoice` | Marks a field as part of a mutually exclusive choice |
| `@BoundChoiceGroup` | Marks a field as a polymorphic collection with discriminator |

### Choice Support

**Choice (`@BoundChoice`)**: Mutually exclusive alternatives at the same position in the model.

```java
@MetaschemaAssembly(name = "my-assembly", moduleClass = MyModule.class)
public class MyAssembly implements IBoundObject {
  @BoundField(useName = "option-a")
  @BoundChoice(choiceId = "choice-1")
  private String optionA;

  @BoundField(useName = "option-b")
  @BoundChoice(choiceId = "choice-1")
  private String optionB;
}
```

**Adjacency requirement**: All fields with the same `choiceId` must be declared consecutively.

**Choice Groups (`@BoundChoiceGroup`)**: Polymorphic collections with type discriminators.

```java
@BoundChoiceGroup(
    maxOccurs = -1,
    assemblies = {
        @BoundGroupedAssembly(useName = "child-a", binding = ChildA.class),
        @BoundGroupedAssembly(useName = "child-b", binding = ChildB.class)
    },
    groupAs = @GroupAs(name = "children", inJson = JsonGroupAsBehavior.LIST))
private List<Object> children;
```

### Choice Instance Interface

The `IChoiceInstance` interface represents a choice in the model:

```java
IChoiceInstance choice = ...;

// Check cardinality
int minOccurs = choice.getMinOccurs();  // 0 = optional, ≥1 = required
int maxOccurs = choice.getMaxOccurs();  // -1 = unbounded

// Get alternatives
Collection<INamedModelInstanceAbsolute> alternatives = choice.getNamedModelInstances();

// Get parent
IAssemblyDefinition parent = choice.getContainingDefinition();
```

For annotation-based bindings, choices are optional by default (`minOccurs = 0`).

## Loading Metaschema Modules

### Using IBindingContext

```java
import dev.metaschema.databind.IBindingContext;
import dev.metaschema.databind.model.IBoundModule;

// Create binding context
IBindingContext bindingContext = IBindingContext.newInstance();

// Load module from a bound Java class
IBoundModule module = bindingContext.registerModule(MyRootClass.class);

// Or use the builder for more control
IBindingContext context = IBindingContext.builder()
    .constraintValidationHandler(handler)
    .build();
```

### Loading Module Files

```java
import dev.metaschema.databind.model.metaschema.IBindingModuleLoader;

IBindingContext bindingContext = IBindingContext.newInstance();
IBindingModuleLoader loader = bindingContext.newModuleLoader();

// Load from URI
IBindingMetaschemaModule module = loader.load(moduleUri);
```

## Metapath Expression Evaluation

### Compiling and Evaluating Expressions

```java
import dev.metaschema.core.metapath.MetapathExpression;
import dev.metaschema.core.metapath.StaticContext;
import dev.metaschema.core.metapath.DynamicContext;
import dev.metaschema.core.metapath.item.ISequence;

// Compile expression with static context
StaticContext staticContext = StaticContext.builder()
    .defaultModelNamespace(namespace)
    .build();

MetapathExpression expr = MetapathExpression.compile("./title", staticContext);

// Evaluate with dynamic context
DynamicContext dynamicContext = new DynamicContext(staticContext);
ISequence<?> result = expr.evaluate(focusItem, dynamicContext);
```

### Static and Dynamic Context

```java
// Static context - compile-time settings
StaticContext staticContext = StaticContext.builder()
    .namespace("prefix", "http://example.com/ns")
    .defaultModelNamespace(namespace)
    .baseUri(baseUri)
    .build();

// Dynamic context - runtime settings
DynamicContext dynamicContext = new DynamicContext(staticContext);
dynamicContext.setDocumentLoader(loader);
```

## Binding Configuration (Code Generation)

Binding configuration files customize how Java classes are generated from Metaschema modules.

### Configuration File Location

Binding configurations are XML files in `{module}/src/main/metaschema-bindings/`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<metaschema-bindings xmlns="https://csrc.nist.gov/ns/metaschema-binding/1.0">
  <metaschema-binding href="../metaschema/my-module.yaml">
    <define-assembly-binding name="my-assembly">
      <java>
        <use-class-name>MyCustomClassName</use-class-name>
        <implement-interface>com.example.MyInterface</implement-interface>
        <extend-base-class>com.example.BaseClass</extend-base-class>
      </java>
    </define-assembly-binding>
  </metaschema-binding>
</metaschema-bindings>
```

### Configuration Options

| Element | Purpose |
|---------|---------|
| `use-class-name` | Override generated class name |
| `implement-interface` | Add interface to implements clause (repeatable) |
| `extend-base-class` | Set base class for generated class |
| `collection-class` | Override default collection type (e.g., `java.util.LinkedList`) |

### Typed Choice Group Collections

By default, choice group fields use `List<Object>`. Use binding configuration to specify a common interface:

```xml
<define-assembly-binding name="parent-assembly">
  <choice-group-binding name="children">
    <item-type use-wildcard="true">com.example.IChildItem</item-type>
  </choice-group-binding>
</define-assembly-binding>
```

This generates:

```java
// With use-wildcard="true" (default)
private List<? extends IChildItem> children;

// With use-wildcard="false"
private List<IChildItem> children;
```

### Property-Level Collection Override

Override collection type for specific properties:

```xml
<define-assembly-binding name="my-assembly">
  <property-binding name="items">
    <java>
      <collection-class>java.util.LinkedHashSet</collection-class>
    </java>
  </property-binding>
</define-assembly-binding>
```

### Targeting Inline Definitions

Use `target` attribute with Metapath expressions to configure inline definitions:

```xml
<define-assembly-binding target="//define-assembly[@name='inline-def']">
  <java>
    <implement-interface>com.example.InlineInterface</implement-interface>
  </java>
</define-assembly-binding>
```

### Classpath Requirements for Code Generation

When configuring custom base classes or superinterfaces, those classes **must be available on the classpath during code generation**. The generator uses reflection to:

1. **Detect method overrides**: Checks if getter/setter methods are declared in superinterfaces to add `@Override` annotations
2. **Determine IBoundObject extension**: Checks if configured superinterfaces extend `IBoundObject` to avoid redundant interface declarations

**Maven Plugin Configuration:**

Add dependencies to the `metaschema-maven-plugin` configuration to ensure classes are available:

```xml
<plugin>
  <groupId>dev.metaschema</groupId>
  <artifactId>metaschema-maven-plugin</artifactId>
  <dependencies>
    <!-- Add dependency containing custom interfaces/base classes -->
    <dependency>
      <groupId>com.example</groupId>
      <artifactId>my-interfaces</artifactId>
      <version>${project.version}</version>
    </dependency>
  </dependencies>
</plugin>
```

**Behavior when classes are not on classpath:**

| Scenario | Behavior |
|----------|----------|
| Base class not found | `@Override` annotations may be missing from getters/setters |
| Superinterface not found | `@Override` annotations may be missing; interface may be redundantly added |
| Generated interface not yet compiled | Use multi-phase generation or ensure build order |

**Multi-module projects:** When the superinterface is defined in another module, ensure that module is built and available as a dependency before code generation runs.

## Serialization and Deserialization

### Deserializing Content

```java
import dev.metaschema.databind.io.IBoundLoader;
import dev.metaschema.databind.io.Format;

IBindingContext bindingContext = IBindingContext.newInstance();
IBoundLoader loader = bindingContext.newBoundLoader();

// Auto-detect format from content
MyRootClass object = loader.load(MyRootClass.class, path);

// Or use format-specific deserializer
IDeserializer<MyRootClass> deserializer = bindingContext.newDeserializer(
    Format.XML, MyRootClass.class);
MyRootClass object = deserializer.deserialize(inputStream);
```

### Serializing Content

```java
import dev.metaschema.databind.io.ISerializer;

ISerializer<MyRootClass> serializer = bindingContext.newSerializer(
    Format.JSON, MyRootClass.class);
serializer.serialize(object, outputStream);
```

### Supported Formats

| Format | Description |
|--------|-------------|
| `Format.XML` | XML serialization |
| `Format.JSON` | JSON serialization |
| `Format.YAML` | YAML serialization |

### Deserialization Features

Control deserialization behavior with `DeserializationFeature`:

```java
IDeserializer<MyClass> deserializer = bindingContext.newDeserializer(Format.JSON, MyClass.class);

// Enable/disable features
deserializer.enableFeature(DeserializationFeature.DESERIALIZE_VALIDATE_REQUIRED_FIELDS);
deserializer.disableFeature(DeserializationFeature.DESERIALIZE_VALIDATE_REQUIRED_FIELDS);
```

| Feature | Default | Description |
|---------|---------|-------------|
| `DESERIALIZE_VALIDATE_REQUIRED_FIELDS` | enabled | Validate that required fields (`minOccurs >= 1`) are present |
| `DESERIALIZE_XML_ALLOW_ENTITY_RESOLUTION` | disabled | Allow XML entity resolution (security consideration) |

### Required Field Validation

When `DESERIALIZE_VALIDATE_REQUIRED_FIELDS` is enabled:

- Fields with `minOccurs >= 1` must be provided in the input
- Default values are applied for missing optional fields
- Choice groups are validated correctly:
  - If any alternative is provided, the choice is satisfied
  - If no alternative is provided, `minOccurs` on the choice determines validity

```java
// Throws IOException if required fields are missing
MyClass obj = deserializer.deserialize(inputStream);
```

## Constraint Validation

### Basic Validation

```java
import dev.metaschema.core.model.constraint.IConstraintValidator;
import dev.metaschema.core.model.constraint.DefaultConstraintValidator;
import dev.metaschema.core.model.constraint.FindingCollectingConstraintValidationHandler;

// Create handler to collect findings
FindingCollectingConstraintValidationHandler handler =
    new FindingCollectingConstraintValidationHandler();

// Create and use validator with try-with-resources
// IConstraintValidator implements AutoCloseable to release thread pools
try (IConstraintValidator validator = new DefaultConstraintValidator(handler)) {
    // Validate document
    validator.validate(documentNodeItem, dynamicContext);
}

// Check results
IValidationResult result = handler.toValidationResult();
if (!result.isPassing()) {
    result.getFindings().forEach(finding -> {
        System.err.println(finding.getMessage());
    });
}
```

### Loading External Constraints

```java
import dev.metaschema.core.model.constraint.IConstraintSet;
import dev.metaschema.databind.model.metaschema.BindingConstraintLoader;

BindingConstraintLoader constraintLoader = new BindingConstraintLoader(bindingContext);
IConstraintSet constraintSet = constraintLoader.load(constraintUri);
```

## Exception Hierarchy

### Base Exception

All Metapath exceptions extend `MetapathException`:

```text
MetapathException (base)
├── StaticMetapathException    - Compile-time errors (MPST prefix)
├── DynamicMetapathException   - Runtime errors (MPDY prefix)
├── TypeMetapathException      - Type-related errors (MPTY prefix)
└── InvalidMetapathGrammarException - Parser errors
```

### Error Code Prefixes

| Prefix | Category | Description |
|--------|----------|-------------|
| `MPST` | Static | Compile-time/static context errors |
| `MPDY` | Dynamic | Runtime/dynamic context errors |
| `MPTY` | Type | Type mismatch errors |
| `FOAR` | Arithmetic | Arithmetic function errors |
| `FOCA` | Cast | Cast function errors |
| `FODC` | Document | Document function errors |
| `FODT` | DateTime | Date/time function errors |
| `FONS` | Namespace | Namespace errors |
| `FORG` | Argument | Invalid argument errors |
| `FORX` | Regex | Regular expression errors |
| `FOTY` | Type | Function type errors |

### Common Static Errors

| Code | Constant | Description |
|------|----------|-------------|
| `MPST0003` | `INVALID_PATH_GRAMMAR` | Invalid Metapath grammar |
| `MPST0008` | `NOT_DEFINED` | Undefined name reference |
| `MPST0017` | `UNKNOWN_FUNCTION` | Unknown function |
| `MPST0051` | `UNSUPPORTED_AXIS` | Unsupported axis |

### Common Dynamic Errors

| Code | Constant | Description |
|------|----------|-------------|
| `MPDY0002` | `DYNAMIC_CONTEXT_ABSENT` | Required context is absent |
| `MPDY0050` | `TREAT_DOES_NOT_MATCH_TYPE` | Type mismatch in treat expression |

### Function-Specific Exceptions

```java
// Arithmetic errors
ArithmeticFunctionException  // FOAR prefix

// Cast errors
CastFunctionException        // FOCA prefix
InvalidValueForCastFunctionException

// Document errors
DocumentFunctionException    // FODC prefix

// DateTime errors
DateTimeFunctionException    // FODT prefix

// Argument errors
InvalidArgumentFunctionException  // FORG prefix
InvalidTypeFunctionException

// Regex errors
RegularExpressionMetapathException  // FORX prefix

// Array errors
ArrayMetapathException
IndexOutOfBoundsArrayMetapathException
NegativeLengthArrayMetapathException
```

## Implementing Metapath Functions

### Function Structure

```java
import dev.metaschema.core.metapath.function.IFunction;
import dev.metaschema.core.metapath.function.IArgument;
import dev.metaschema.core.metapath.MetapathConstants;

public final class FnMyFunction {
  private static final String NAME = "my-function";

  @NonNull
  static final IFunction SIGNATURE = IFunction.builder()
      .name(NAME)
      .namespace(MetapathConstants.NS_METAPATH_FUNCTIONS)
      .deterministic()
      .contextIndependent()
      .focusIndependent()
      .argument(IArgument.builder()
          .name("arg")
          .type(IStringItem.type())
          .zeroOrOne()
          .build())
      .returnType(IStringItem.type())
      .returnOne()
      .functionHandler(FnMyFunction::execute)
      .build();

  private FnMyFunction() {
    // disable construction
  }

  @NonNull
  private static ISequence<IStringItem> execute(
      @NonNull IFunction function,
      @NonNull List<ISequence<?>> arguments,
      @NonNull DynamicContext dynamicContext,
      IItem focus) {

    ISequence<? extends IStringItem> arg = FunctionUtils.asType(
        ObjectUtils.requireNonNull(arguments.get(0)));

    // Implementation logic
    return ISequence.of(result);
  }
}
```

### Function Properties

| Property | Description |
|----------|-------------|
| `deterministic()` | Same input always produces same output |
| `contextIndependent()` | Does not depend on static/dynamic context |
| `focusIndependent()` | Does not depend on focus item |
| `contextDependent()` | Depends on context |
| `focusDependent()` | Depends on focus item |

### Registering Functions

Functions are registered in `DefaultFunctionLibrary`:

```java
// In DefaultFunctionLibrary constructor
registerFunction(FnMyFunction.SIGNATURE);
```

### Multiple Signatures

For functions with optional parameters, create multiple signatures:

```java
static final IFunction SIGNATURE_ONE_ARG = IFunction.builder()
    .name(NAME)
    // ... one argument
    .functionHandler(FnMyFunction::executeOneArg)
    .build();

static final IFunction SIGNATURE_TWO_ARG = IFunction.builder()
    .name(NAME)
    // ... two arguments
    .functionHandler(FnMyFunction::executeTwoArg)
    .build();
```

## Item Types

### Item Hierarchy

```text
IItem (base)
├── INodeItem           - Document model nodes
│   ├── IDocumentNodeItem
│   ├── IAssemblyNodeItem
│   ├── IFieldNodeItem
│   └── IFlagNodeItem
├── IAtomicItem         - Atomic values
│   ├── IStringItem
│   ├── INumericItem
│   │   ├── IIntegerItem
│   │   └── IDecimalItem
│   ├── IBooleanItem
│   ├── IDateItem
│   ├── IDateTimeItem
│   └── ...
├── IArrayItem          - Array values
└── IMapItem            - Map values
```

### Working with Sequences

```java
import dev.metaschema.core.metapath.item.ISequence;

// Create sequences
ISequence<IStringItem> seq = ISequence.of(item1, item2);
ISequence<?> empty = ISequence.empty();

// Query sequences
boolean isEmpty = sequence.isEmpty();
int size = sequence.size();
IItem first = sequence.getFirstItem(false);

// Stream sequences
sequence.stream().forEach(item -> { ... });
```

## Testing Patterns

### Testing Metapath Functions

```java
@Test
void testMyFunction() {
  StaticContext staticContext = StaticContext.instance();
  DynamicContext dynamicContext = new DynamicContext(staticContext);

  MetapathExpression expr = MetapathExpression.compile(
      "my-function('input')", staticContext);

  ISequence<?> result = expr.evaluate(null, dynamicContext);

  assertEquals(1, result.size());
  assertEquals("expected", result.getFirstItem(true).asString());
}
```

### Testing with Document Context

```java
@Test
void testWithDocument() {
  IBindingContext bindingContext = IBindingContext.newInstance();
  IBoundLoader loader = bindingContext.newBoundLoader();

  IBoundObject object = loader.load(MyClass.class, testFile);
  IDocumentNodeItem document = bindingContext.toNodeItem(object);

  DynamicContext dynamicContext = new DynamicContext(staticContext);
  MetapathExpression expr = MetapathExpression.compile("//item", staticContext);

  ISequence<?> result = expr.evaluate(document, dynamicContext);
  // assertions
}
```

## Generated Code Quality

### Null-Safety Annotations

Generated binding classes include SpotBugs null-safety annotations:

```java
// Field annotations
@Nullable
private String optionalField;

@NonNull
private String requiredField;

// Getter annotations
@Nullable
public String getOptionalField() { ... }

@NonNull
public String getRequiredField() { ... }

// Setter annotations
public void setRequiredField(@NonNull String value) { ... }
```

**Rules for generated code:**
- Fields with `minOccurs = 0` → `@Nullable`
- Fields with `minOccurs >= 1` → `@NonNull`
- Getters follow field nullability
- Required field setters have `@NonNull` parameters

### Javadoc Generation

Generated classes include comprehensive Javadoc:

- Class-level documentation describes the Metaschema definition
- Field documentation includes formal name and description
- Getter/setter methods reference the underlying property

## References

- [XPath 3.1 Specification](https://www.w3.org/TR/xpath-31/)
- [XPath Functions 3.1](https://www.w3.org/TR/xpath-functions-31/)
- [Metaschema Specification](https://framework.metaschema.dev/specification/)
