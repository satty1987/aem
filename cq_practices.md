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
---

# AEM Multifield: Composite vs Non-Composite

## What is Composite?

`composite="{Boolean}true"` determines **how multifield data is structured and stored** in JCR.

---

## Non-Composite Multifield (Simple)

### Definition
- **Stores**: Simple array of single values (String array)
- **Use case**: List of simple items (names, URLs, keywords)
- **Structure**: Flat array

### Dialog Example:
```xml
<!-- Simple Multifield - WITHOUT composite -->
<multifield
    jcr:primaryType="nt:unstructured"
    sling:resourceType="granite/ui/components/coral/foundation/form/multifield"
    fieldLabel="Keywords"
    name="./keywords">
    <field
        jcr:primaryType="nt:unstructured"
        sling:resourceType="granite/ui/components/coral/foundation/form/textfield"
        name="./keyword"/>
</multifield>
```

### How It's Stored in JCR:
```xml
<jcr:root
    sling:resourceType="myproject/components/mycomponent"
    keywords="[java,aem,sling,htl]"/>
    <!-- Simple String array -->
```

### Sling Model:
```java
@ValueMapValue
private String[] keywords;

public String[] getKeywords() {
    return keywords;
}
```

### HTL Rendering:
```html
<ul>
    <li data-sly-repeat="${properties.keywords}">${item}</li>
</ul>
```

---

## Composite Multifield (Complex)

### Definition
- **Stores**: Array of complex objects (nodes with multiple properties)
- **Use case**: List of items with multiple fields each
- **Structure**: Nested nodes with properties

### Dialog Example:
```xml
<!-- Composite Multifield - WITH composite -->
<multifield
    jcr:primaryType="nt:unstructured"
    sling:resourceType="granite/ui/components/coral/foundation/form/multifield"
    fieldLabel="Team Members"
    name="./teamMembers"
    composite="{Boolean}true">
    <field
        jcr:primaryType="nt:unstructured"
        sling:resourceType="granite/ui/components/coral/foundation/container"
        name="./member">
        <items jcr:primaryType="nt:unstructured">
            <name
                jcr:primaryType="nt:unstructured"
                sling:resourceType="granite/ui/components/coral/foundation/form/textfield"
                fieldLabel="Name"
                name="./name"/>
            <role
                jcr:primaryType="nt:unstructured"
                sling:resourceType="granite/ui/components/coral/foundation/form/textfield"
                fieldLabel="Role"
                name="./role"/>
            <email
                jcr:primaryType="nt:unstructured"
                sling:resourceType="granite/ui/components/coral/foundation/form/textfield"
                fieldLabel="Email"
                name="./email"/>
        </items>
    </field>
</multifield>
```

### How It's Stored in JCR:
```xml
<jcr:root
    sling:resourceType="myproject/components/mycomponent">
    <teamMembers jcr:primaryType="nt:unstructured">
        <item0
            jcr:primaryType="nt:unstructured"
            name="John Doe"
            role="Developer"
            email="john@example.com"/>
        <item1
            jcr:primaryType="nt:unstructured"
            name="Jane Smith"
            role="Designer"
            email="jane@example.com"/>
        <item2
            jcr:primaryType="nt:unstructured"
            name="Bob Johnson"
            role="Manager"
            email="bob@example.com"/>
    </teamMembers>
</jcr:root>
```

### Sling Model:
```java
@ValueMapValue
private Resource teamMembers;

private List<TeamMember> members;

@PostConstruct
protected void init() {
    members = new ArrayList<>();
    if (teamMembers != null && teamMembers.hasChildren()) {
        teamMembers.getChildren().forEach(child -> {
            TeamMember member = new TeamMember();
            member.setName(child.getValueMap().get("name", String.class));
            member.setRole(child.getValueMap().get("role", String.class));
            member.setEmail(child.getValueMap().get("email", String.class));
            members.add(member);
        });
    }
}

public List<TeamMember> getMembers() {
    return members;
}

// Inner class
public static class TeamMember {
    private String name;
    private String role;
    private String email;
    
    // Getters and setters...
}
```

### HTL Rendering:
```html
<div data-sly-repeat.member="${model.members}">
    <h3>${member.name}</h3>
    <p>Role: ${member.role}</p>
    <p>Email: ${member.email}</p>
</div>
```

---

## Side-by-Side Comparison

| Aspect | Non-Composite | Composite |
|--------|---------------|-----------|
| **Property** | `composite="{Boolean}false"` (default) | `composite="{Boolean}true"` |
| **Storage** | String array | Child nodes |
| **Data Type** | Single value per item | Multiple values per item |
| **Use Case** | Simple lists | Complex objects |
| **JCR Structure** | `property="[val1,val2,val3]"` | `<node><item0/><item1/></node>` |
| **Field Container** | Direct textfield | `granite/ui/components/coral/foundation/container` |
| **Sling Model Type** | `String[]` | `Resource` |
| **Processing** | Direct array access | Need to iterate child resources |

---

## When to Use Each

### Use Non-Composite When:
✅ Storing simple lists
✅ Single property per item
✅ Examples:
- List of keywords/tags
- List of URLs
- List of names
- Social media links

```xml
<!-- List of social media URLs -->
<multifield name="./socialLinks">
    <field name="./link"/>
</multifield>
```

### Use Composite When:
✅ Multiple properties per item
✅ Complex data structures
✅ Examples:
- Team members (name, role, email, photo)
- Product features (title, description, icon)
- FAQ items (question, answer)
- Testimonials (quote, author, position, company)

```xml
<!-- FAQ with question and answer -->
<multifield name="./faqItems" composite="{Boolean}true">
    <field>
        <items>
            <question name="./question"/>
            <answer name="./answer"/>
        </items>
    </field>
</multifield>
```

---

## Complete Examples

### Example 1: Product Features (Composite)

**Dialog:**
```xml
<features
    sling:resourceType="granite/ui/components/coral/foundation/form/multifield"
    fieldLabel="Product Features"
    name="./features"
    composite="{Boolean}true">
    <field
        sling:resourceType="granite/ui/components/coral/foundation/container"
        name="./feature">
        <items jcr:primaryType="nt:unstructured">
            <icon
                sling:resourceType="granite/ui/components/coral/foundation/form/textfield"
                fieldLabel="Icon"
                name="./icon"/>
            <title
                sling:resourceType="granite/ui/components/coral/foundation/form/textfield"
                fieldLabel="Title"
                name="./title"/>
            <description
                sling:resourceType="granite/ui/components/coral/foundation/form/textarea"
                fieldLabel="Description"
                name="./description"/>
        </items>
    </field>
</features>
```

**Stored as:**
```xml
<features>
    <item0 icon="fa-check" title="Quality" description="High quality product"/>
    <item1 icon="fa-truck" title="Fast Shipping" description="2-day delivery"/>
    <item2 icon="fa-shield" title="Warranty" description="1-year warranty"/>
</features>
```

### Example 2: Tags/Keywords (Non-Composite)

**Dialog:**
```xml
<tags
    sling:resourceType="granite/ui/components/coral/foundation/form/multifield"
    fieldLabel="Tags"
    name="./tags">
    <field
        sling:resourceType="granite/ui/components/coral/foundation/form/textfield"
        name="./tag"/>
</tags>
```

**Stored as:**
```xml
tags="[technology,innovation,digital,cloud]"
```

---

## Key Differences in Dialog Structure

### Non-Composite:
```xml
<multifield name="./items">
    <field
        sling:resourceType="granite/ui/components/coral/foundation/form/textfield"
        name="./item"/>
    <!-- Single field directly -->
</multifield>
```

### Composite:
```xml
<multifield name="./items" composite="{Boolean}true">
    <field
        sling:resourceType="granite/ui/components/coral/foundation/container"
        name="./item">
        <!-- Must use container -->
        <items jcr:primaryType="nt:unstructured">
            <!-- Multiple fields here -->
            <field1 name="./prop1"/>
            <field2 name="./prop2"/>
        </items>
    </field>
</multifield>
```

---

## Common Mistakes

### ❌ Wrong: Composite without container
```xml
<multifield composite="{Boolean}true">
    <field sling:resourceType="granite/ui/components/coral/foundation/form/textfield"/>
    <!-- Missing container! -->
</multifield>
```

### ✅ Correct: Composite with container
```xml
<multifield composite="{Boolean}true">
    <field sling:resourceType="granite/ui/components/coral/foundation/container">
        <items>
            <textfield/>
        </items>
    </field>
</multifield>
```

### ❌ Wrong: Non-composite with multiple fields
```xml
<multifield>
    <field>
        <items>
            <field1/>
            <field2/>
        </items>
    </field>
    <!-- Can't store multiple values per item without composite! -->
</multifield>
```

---

## Summary

**Composite Multifield** = List of complex objects with multiple properties each

```
Non-Composite: ["item1", "item2", "item3"]
Composite: [
    {title: "Title 1", desc: "Desc 1", link: "/link1"},
    {title: "Title 2", desc: "Desc 2", link: "/link2"},
    {title: "Title 3", desc: "Desc 3", link: "/link3"}
]
```

**Rule of Thumb:**
- 1 property per item → **Non-Composite**
- 2+ properties per item → **Composite**
