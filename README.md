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
```<description
    jcr:primaryType="nt:unstructured"
    sling:resourceType="granite/ui/components/coral/foundation/form/textarea"
    fieldLabel="Description"
    name="./description"/>```

## Checkbox
```<showTitle
    jcr:primaryType="nt:unstructured"
    sling:resourceType="granite/ui/components/coral/foundation/form/checkbox"
    text="Show Title"
    name="./showTitle"
    value="true"/>```

## Dropdown (Select)
```<type
    jcr:primaryType="nt:unstructured"
    sling:resourceType="granite/ui/components/coral/foundation/form/select"
    fieldLabel="Type"
    name="./type">

    <items jcr:primaryType="nt:unstructured">
        <one text="One" value="one"/>
        <two text="Two" value="two"/>
    </items>
</type>```

4. Tabs layout (very common in real projects)
```<content sling:resourceType="granite/ui/components/coral/foundation/tabs">
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
</content>```
