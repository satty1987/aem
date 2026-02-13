## Basic structure of a CQ dialog
A dialog is a Granite UI form, built using JCR nodes.

Minimal example:
```
<?xml version="1.0" encoding="UTF-8"?>
<jcr:root
    xmlns:sling="http://sling.apache.org/jcr/sling/1.0"
    xmlns:cq="http://www.day.com/jcr/cq/1.0"
    xmlns:jcr="http://www.jcp.org/jcr/1.0"
    jcr:primaryType="nt:unstructured"
    jcr:title="My Component"
    sling:resourceType="cq/gui/components/authoring/dialog">

    <content
        jcr:primaryType="nt:unstructured"
        sling:resourceType="granite/ui/components/coral/foundation/container">

        <items jcr:primaryType="nt:unstructured">
            <!-- fields go here -->
        </items>

    </content>
</jcr:root>
```
Adding fields (most common ones)
## Text field
```
<title
    jcr:primaryType="nt:unstructured"
    sling:resourceType="granite/ui/components/coral/foundation/form/textfield"
    fieldLabel="Title"
    name="./title"/>
```

## Text area
```
<description
    jcr:primaryType="nt:unstructured"
    sling:resourceType="granite/ui/components/coral/foundation/form/textarea"
    fieldLabel="Description"
    name="./description"/>
```

## Checkbox
```
<showTitle
    jcr:primaryType="nt:unstructured"
    sling:resourceType="granite/ui/components/coral/foundation/form/checkbox"
    text="Show Title"
    name="./showTitle"
    value="true"/>
```

## Dropdown (Select)
```
<type
    jcr:primaryType="nt:unstructured"
    sling:resourceType="granite/ui/components/coral/foundation/form/select"
    fieldLabel="Type"
    name="./type">

    <items jcr:primaryType="nt:unstructured">
        <one text="One" value="one"/>
        <two text="Two" value="two"/>
    </items>
</type>
```

### No, it's NOT mandatory for them to be the same. They serve different purposes:
#### Understanding the Difference:
```
<newfield                          <!-- Node name: JCR structure only -->
    jcr:primaryType="nt:unstructured"
    name="./newField"/>            <!-- Property name: Where data is stored -->
```
# Dialog Field: Node Name vs Name Attribute

## 1. Node Name (`<newfield>`)

* **Purpose**: Organizes dialog structure in JCR repository
* **Scope**: Only used internally in dialog XML
* **Can be**: Any valid JCR node name
* **Not visible**: To authors or in stored content

## 2. name Attribute (`name="./newField"`)

* **Purpose**: Defines where the value is stored in JCR
* **Scope**: This is what gets saved to content
* **Important**: This must match your Sling Model property
* **Visible**: In content nodes and referenced in code

## Tabs layout (very common in real projects)
```
<content sling:resourceType="granite/ui/components/coral/foundation/tabs">
    <items>
        <basic jcr:primaryType="nt:unstructured" jcr:title="Basic">
            <items>
                <!-- basic fields -->
            </items>
        </basic>

        <advanced jcr:primaryType="nt:unstructured" jcr:title="Advanced">
            <items>
                <!-- advanced fields -->
            </items>
        </advanced>
    </items>
</content>
```

# Component structure
```
/apps/demo/components/user-card
 ├── user-card.html
 └── _cq_dialog
     └── .content.xml

```
## Basic User Creation Dialog (.content.xml)
```
<?xml version="1.0" encoding="UTF-8"?>
<jcr:root
    xmlns:sling="http://sling.apache.org/jcr/sling/1.0"
    xmlns:cq="http://www.day.com/jcr/cq/1.0"
    xmlns:jcr="http://www.jcp.org/jcr/1.0"
    jcr:primaryType="nt:unstructured"
    jcr:title="User Details"
    sling:resourceType="cq/gui/components/authoring/dialog">

    <content
        jcr:primaryType="nt:unstructured"
        sling:resourceType="granite/ui/components/coral/foundation/container">

        <items jcr:primaryType="nt:unstructured">

            <!-- Full Name -->
            <fullName
                jcr:primaryType="nt:unstructured"
                sling:resourceType="granite/ui/components/coral/foundation/form/textfield"
                fieldLabel="Full Name"
                name="./fullName"
                required="{Boolean}true"/>

            <!-- Email -->
            <email
                jcr:primaryType="nt:unstructured"
                sling:resourceType="granite/ui/components/coral/foundation/form/textfield"
                fieldLabel="Email"
                name="./email"
                type="email"/>

            <!-- Role -->
            <role
                jcr:primaryType="nt:unstructured"
                sling:resourceType="granite/ui/components/coral/foundation/form/select"
                fieldLabel="Role"
                name="./role">

                <items jcr:primaryType="nt:unstructured">
                    <admin text="Admin" value="admin"/>
                    <editor text="Editor" value="editor"/>
                    <viewer text="Viewer" value="viewer"/>
                </items>
            </role>

            <!-- Active -->
            <active
                jcr:primaryType="nt:unstructured"
                sling:resourceType="granite/ui/components/coral/foundation/form/checkbox"
                text="Active User"
                name="./active"
                value="true"/>
                
          <!-- Interests -->  
          <interests
              jcr:primaryType="nt:unstructured"
              sling:resourceType="granite/ui/components/coral/foundation/form/multifield"
             fieldLabel="Interests">

            <field
             jcr:primaryType="nt:unstructured"
             sling:resourceType="granite/ui/components/coral/foundation/form/textfield"
             name="./interests"
            fieldLabel="Interest"/>
        </interests>

                
        </items>

    </content>
</jcr:root>

```

## How data is saved in JCR
When an author fills the dialog, data is stored on the component node:
```
fullName = "John Doe"
email    = "john@test.com"
role     = "admin"
active   = "true"
interests = ["Cricket", "Music", "Coding"]

```

## Display values in HTL (user-card.html)
```
<div class="user-card">
    <h3>${properties.fullName}</h3>
    <p>Email: ${properties.email}</p>
    <p>Role: ${properties.role}</p>

    <sly data-sly-test="${properties.active}">
        <span class="status active">Active</span>
    </sly>
</div>

```
## Sling Model (Java)
```
package com.yourproject.core.models;

import java.util.List;

import javax.inject.Inject;

import org.apache.sling.api.resource.Resource;
import org.apache.sling.models.annotations.DefaultInjectionStrategy;
import org.apache.sling.models.annotations.Model;
import org.apache.sling.models.annotations.injectorspecific.ValueMapValue;

@Model(
    adaptables = Resource.class,
    defaultInjectionStrategy = DefaultInjectionStrategy.OPTIONAL
)
public class UserDetailsModel {

    @ValueMapValue
    private String fullName;

    @ValueMapValue
    private String email;

    @ValueMapValue
    private String role;

    @ValueMapValue
    private Boolean active;

    @ValueMapValue
    private List<String> interests;

    public String getFullName() {
        return fullName;
    }

    public String getEmail() {
        return email;
    }

    public String getRole() {
        return role;
    }

    public Boolean isActive() {
        return active;
    }

    public List<String> getInterests() {
        return interests;
    }
}

```
## Notes Specific to Your Dialog

- `./fullName`, `./email`, `./role`, `./active` map directly to simple properties on the component node.

- The multifield stores multiple values under the same property name (`./interests`), so `List<String>` (or `String[]`) is the correct type in the Sling Model.

- The checkbox stores the value `"true"` when checked. Sling automatically converts this to a `Boolean`. When unchecked, the property does not exist.


### 1. Property Storage
- All fields use relative names (`./propertyName`), so values are stored directly on the component node.
- Example:
  - `fullName` → `String`
  - `email` → `String`
  - `role` → `String`
  - `active` → `Boolean`
  - `interests` → multi-value `String[]`

---

### 2. Text Fields
- `fullName` is marked as required in the dialog, but this is **author-side validation only**.
- If backend validation is needed, it must be handled in the Sling Model or business logic.
- `email` uses `type="email"` which provides basic client-side validation.

---

### 3. Select Field (Role)
- The `role` dropdown stores only the `value` attribute (`admin`, `editor`, `viewer`).
- The `text` attribute is for author UI display only and is not stored in JCR.
- Always handle unexpected or missing values in your model or HTL.

---

### 4. Checkbox Field (Active)
- When checked, the checkbox stores the string value `"true"`.
- When unchecked, the property does **not exist** in JCR.
- Using `Boolean` in the Sling Model is recommended to safely handle null values.

---

### 5. Multifield (Interests)
- This is a **non-composite multifield**.
- All values are stored under the same property name (`interests`) as a multi-value array.
- In Sling Models, map this field as `List<String>` or `String[]`.
- No child nodes are created for this multifield.

---

### 6. Default Injection Strategy
- Using `DefaultInjectionStrategy.OPTIONAL` prevents model instantiation errors when fields are empty or not authored.
- Always code defensively for null values in HTL or Java.

---

### 7. Recommended Best Practices
- Add null checks in HTL using `data-sly-test` where needed.
- Avoid business logic in HTL; keep it inside the Sling Model.
- If the dialog grows in complexity (nested fields, objects), consider switching to a **composite multifield**.

---

### 8. Common Pitfalls
- Expecting unchecked checkboxes to store `false` (they don’t).
- Treating non-composite multifields as child nodes.
- Relying solely on dialog `required` validation for backend logic.


## HTL usage example
```
<div data-sly-use.model="com.yourproject.core.models.UserDetailsModel">
    <p>Name: ${model.fullName}</p>
    <p>Email: ${model.email}</p>
    <p>Role: ${model.role}</p>
    <p>Active: ${model.active}</p>

    <ul data-sly-list.interest="${model.interests}">
        <li>${interest}</li>
    </ul>
</div>

```
