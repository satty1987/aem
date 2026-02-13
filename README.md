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
