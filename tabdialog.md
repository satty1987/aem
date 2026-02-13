## 1. CQ Dialog (Tab Layout)
`_cq_dialog/.content.xml`

```
<?xml version="1.0" encoding="UTF-8"?>
<jcr:root
    xmlns:sling="http://sling.apache.org/jcr/sling/1.0"
    xmlns:cq="http://www.day.com/jcr/cq/1.0"
    xmlns:jcr="http://www.jcp.org/jcr/1.0"
    jcr:primaryType="nt:unstructured"
    sling:resourceType="cq/gui/components/authoring/dialog"
    jcr:title="User Component">

    <content
        jcr:primaryType="nt:unstructured"
        sling:resourceType="granite/ui/components/coral/foundation/container">

        <items jcr:primaryType="nt:unstructured">

            <tabs
                jcr:primaryType="nt:unstructured"
                sling:resourceType="granite/ui/components/coral/foundation/tabs">

                <items jcr:primaryType="nt:unstructured">

                    <!-- Basic Info Tab -->
                    <basic
                        jcr:primaryType="nt:unstructured"
                        jcr:title="Basic Info"
                        sling:resourceType="granite/ui/components/coral/foundation/container">

                        <items jcr:primaryType="nt:unstructured">

                            <fullName
                                sling:resourceType="granite/ui/components/coral/foundation/form/textfield"
                                fieldLabel="Full Name"
                                name="./fullName"
                                required="{Boolean}true"/>

                            <email
                                sling:resourceType="granite/ui/components/coral/foundation/form/textfield"
                                fieldLabel="Email"
                                name="./email"
                                type="email"/>

                            <age
                                sling:resourceType="granite/ui/components/coral/foundation/form/numberfield"
                                fieldLabel="Age"
                                name="./age"/>

                        </items>
                    </basic>

                    <!-- Preferences Tab -->
                    <preferences
                        jcr:primaryType="nt:unstructured"
                        jcr:title="Preferences"
                        sling:resourceType="granite/ui/components/coral/foundation/container">

                        <items jcr:primaryType="nt:unstructured">

                            <role
                                sling:resourceType="granite/ui/components/coral/foundation/form/select"
                                fieldLabel="Role"
                                name="./role">
                                <items>
                                    <admin text="Admin" value="admin"/>
                                    <editor text="Editor" value="editor"/>
                                    <viewer text="Viewer" value="viewer"/>
                                </items>
                            </role>

                            <active
                                sling:resourceType="granite/ui/components/coral/foundation/form/checkbox"
                                text="Active User"
                                name="./active"
                                value="true"/>

                            <interests
                                sling:resourceType="granite/ui/components/coral/foundation/form/multifield"
                                fieldLabel="Interests">
                                <field
                                    sling:resourceType="granite/ui/components/coral/foundation/form/textfield"
                                    name="./interests"/>
                            </interests>

                        </items>
                    </preferences>

                    <!-- Profile Tab -->
                    <profile
                        jcr:primaryType="nt:unstructured"
                        jcr:title="Profile"
                        sling:resourceType="granite/ui/components/coral/foundation/container">

                        <items jcr:primaryType="nt:unstructured">

                            <dob
                                sling:resourceType="granite/ui/components/coral/foundation/form/datepicker"
                                fieldLabel="Date of Birth"
                                name="./dob"/>

                            <bio
                                sling:resourceType="granite/ui/components/coral/foundation/form/textarea"
                                fieldLabel="Bio"
                                name="./bio"/>

                            <profileImage
                                sling:resourceType="granite/ui/components/coral/foundation/form/pathfield"
                                fieldLabel="Profile Image"
                                name="./profileImage"
                                rootPath="/content/dam"/>

                        </items>
                    </profile>

                </items>
            </tabs>

        </items>
    </content>
</jcr:root>

```

## 2. Sling Model (User Schema)

```
package com.yourproject.core.models;

import java.util.List;
import java.util.Calendar;

import org.apache.sling.api.resource.Resource;
import org.apache.sling.models.annotations.DefaultInjectionStrategy;
import org.apache.sling.models.annotations.Model;
import org.apache.sling.models.annotations.injectorspecific.ValueMapValue;

@Model(
    adaptables = Resource.class,
    defaultInjectionStrategy = DefaultInjectionStrategy.OPTIONAL
)
public class UserModel {

    @ValueMapValue
    private String fullName;

    @ValueMapValue
    private String email;

    @ValueMapValue
    private Integer age;

    @ValueMapValue
    private String role;

    @ValueMapValue
    private Boolean active;

    @ValueMapValue
    private List<String> interests;

    @ValueMapValue
    private Calendar dob;

    @ValueMapValue
    private String bio;

    @ValueMapValue
    private String profileImage;

    public String getFullName() { return fullName; }
    public String getEmail() { return email; }
    public Integer getAge() { return age; }
    public String getRole() { return role; }
    public Boolean getActive() { return active; }
    public List<String> getInterests() { return interests; }
    public Calendar getDob() { return dob; }
    public String getBio() { return bio; }
    public String getProfileImage() { return profileImage; }
}

```

## 3. HTL (Rendering)

```
<div data-sly-use.user="com.yourproject.core.models.UserModel">

    <h2>${user.fullName}</h2>
    <p>Email: ${user.email}</p>
    <p>Age: ${user.age}</p>
    <p>Role: ${user.role}</p>
    <p>Status: ${user.active ? 'Active' : 'Inactive'}</p>

    <p data-sly-test="${user.bio}">
        Bio: ${user.bio}
    </p>

    <p data-sly-test="${user.dob}">
        DOB: ${user.dob @ format='yyyy-MM-dd'}
    </p>

    <img
        data-sly-test="${user.profileImage}"
        src="${user.profileImage}"
        alt="${user.fullName}" />

    <ul data-sly-test="${user.interests}"
        data-sly-list.item="${user.interests}">
        <li>${item}</li>
    </ul>

</div>

```

## Key Takeaways

- Tabs keep large dialogs clean and scalable.
- All fields use ./propertyName, so data lives on the component node.
- Multifield stores a multi-value property, not child nodes.
- Checkbox stores "true" only when checked.
- This structure is production-ready and easy to extend.
