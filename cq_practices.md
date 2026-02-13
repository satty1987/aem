# AEM Dialog: name="./property" vs name="property"

## Quick Answer

Both will work in simple cases, but `./` is the **recommended convention** in AEM.

## The Difference

### With `./` Prefix (Recommended)
```xml
<title_field name="./title"/>
```
- **Explicitly relative** to the current resource
- **Standard AEM convention**
- **Clear and predictable** behavior
- **Works consistently** in all contexts (including multifields)

### Without `./` Prefix
```xml
<title_field name="title"/>
```
- **Implicitly relative** to current resource (in most cases)
- **Can work** but less explicit
- **May cause issues** in complex scenarios
- **Not the recommended practice**

## How They Save Data

### Example Dialog:
```xml
<textfield
    name="./title"/>        <!-- Saves to ./title -->

<textfield
    name="title"/>          <!-- Also saves to title (usually) -->
```

### Resulting JCR Content:
```xml
<jcr:root
    sling:resourceType="myproject/components/mycomponent"
    title="Hello World">    <!-- Both approaches save here -->
```

## When It Matters

### ✅ Simple Fields - Both Work Similarly
```xml
<!-- These behave almost identically -->
<textfield name="./description"/>
<textfield name="description"/>
```

### ⚠️ Multifield - Use `./` Always
```xml
<multifield
    name="./items">                      <!-- Must use ./ -->
    <field
        name="./title"/>                 <!-- Must use ./ for nested -->
</multifield>
```

**Without `./` in multifield** can cause:
- Properties saved in wrong location
- Data not properly nested
- Unpredictable behavior

### ⚠️ Complex Structures - Use `./`
```xml
<container name="./metadata">
    <field name="./author"/>             <!-- Saves to metadata/author -->
    <field name="author"/>               <!-- May save incorrectly -->
</container>
```

## Special Cases

### 1. JCR Namespace Properties
```xml
<textfield name="jcr:title"/>           <!-- No ./ for namespaced properties -->
<textfield name="jcr:description"/>
```

### 2. Absolute Paths
```xml
<hidden name="/content/dam/path"/>      <!-- Absolute path -->
```

### 3. Parent Path Reference
```xml
<hidden name="../parentProperty"/>      <!-- Saves to parent node -->
```

## Best Practices

### ✅ Do This:
```xml
<!-- Always use ./ prefix -->
<textfield name="./title"/>
<textarea name="./description"/>
<numberfield name="./count"/>

<!-- Multifield with composite -->
<multifield name="./items" composite="{Boolean}true">
    <field name="./itemTitle"/>
    <field name="./itemDescription"/>
</multifield>
```

### ❌ Avoid This:
```xml
<!-- Inconsistent usage -->
<textfield name="title"/>
<textarea name="./description"/>
<numberfield name="count"/>

<!-- Will cause issues in multifield -->
<multifield name="items">
    <field name="itemTitle"/>
</multifield>
```

## Real-World Example

### Dialog Definition:
```xml
<dialog>
    <content>
        <items>
            <!-- Correct: Using ./ consistently -->
            <textfield name="./heading"/>
            <richtext name="./body"/>
            
            <multifield name="./cards" composite="{Boolean}true">
                <field>
                    <items>
                        <cardTitle name="./title"/>
                        <cardText name="./text"/>
                    </items>
                </field>
            </multifield>
        </items>
    </content>
</dialog>
```

### Saved Content:
```xml
<jcr:root
    sling:resourceType="myproject/components/mycomponent"
    heading="Welcome"
    body="<p>Content here</p>">
    <cards>
        <item0
            title="Card 1"
            text="Description 1"/>
        <item1
            title="Card 2"
            text="Description 2"/>
    </cards>
</jcr:root>
```

## Sling Model Impact

### Both work with Sling Models:
```java
@Model(adaptables = Resource.class)
public class MyModel {
    
    @ValueMapValue
    private String title;        // Works with both ./title and title
    
    @ValueMapValue
    private String description;  // Works with both ./description and description
}
```

**But:** Using `./` in dialog ensures the property is saved where you expect it.

## Summary Table

| Aspect | With `./` | Without `./` |
|--------|-----------|--------------|
| **Clarity** | ✅ Explicit | ⚠️ Implicit |
| **Convention** | ✅ Standard | ❌ Not standard |
| **Simple fields** | ✅ Works | ✅ Usually works |
| **Multifield** | ✅ Works | ❌ May fail |
| **Complex structures** | ✅ Predictable | ⚠️ Unpredictable |
| **Recommendation** | ✅ Always use | ❌ Avoid |

## Conclusion

**Always use `./` prefix in the `name` attribute** for:
- Consistency
- Predictability
- Following AEM best practices
- Avoiding issues in complex scenarios

```xml
<!-- Best Practice -->
<textfield name="./propertyName"/>
```
