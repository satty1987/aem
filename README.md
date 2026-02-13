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
