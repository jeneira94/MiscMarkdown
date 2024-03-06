# Writing Exercise

Please see my [process outline](veevaProcess.md) for more information on the following exercise below. **Note**: the links shown are clickable, but the link to [section help configuration](#section-help-configuration) is the only one that really works within the document because this is just a snippet and mimic of [this site page](http://vaulthelp.vod309.com/wordpress/admin-user-help/fields-objects/configuring-object-page-layouts/).

---

## How to Add Detail Forms
To add a detail form section:

1. In the object page editor, click **Add Section**.
2. Select **Detail Form**.
3. Enter a **Section Label** and **Section Name**. Only the label will appear for users.
4. Choose a **Section Layout: Detail Form – One Column** or **Detail Form – Two Columns**.
5. Optional: In the **Show the section only in these lifecycle states** field, select one or more lifecycle states. This option only appears for objects that use lifecycles. See [lifecycle state details](#state-details).
6. Optional: Enter the **Section Help** content in the field. Only a maximum length of 255 unformatted characters would be shown. See [section help configuration](#section-help-configuration).
7. Click **Done**.

(insert screenshot)

## How to Add Related Object Sections

To add a section showing related object records:

1. In the object page editor, click **Add Section**.
2. Select **Related Object**.
3. Select a [related object](#related-objects). 
4. Enter a **Section Label** and **Section Name**. Only the label will appear for users.
5. Optional: In the **Show the section only in these lifecycle states** field, select one or more lifecycle states. See [lifecycle state details](#state-details).
6. Optional: Enter the **Section Help** content in the field. Only a maximum length of 255 unformatted characters would be shown. See [section help configuration](#section-help-configuration).
7. Optional: Prevent users from creating new object records with the [**Prevent record creation** option](#prevent-creation).
8. Optional: Allow users to create new records using a [pop-up dialog](#pop-up-dialog).
9. Optional: In the **Filter Related List** section, create filters for the related object section. Object records that appear in this section adhere to the filters you define. See [Filtering on Related Object Sections](#filter-sections).
10. Click **Done**.

(insert screenshot)

(insert related object note)

## How to Add Related Documents Sections

To add a section showing related documents: 

1. In the object page editor, click **Add Section**.
2. Select **Related Documents**.
3. Select the document field referencing the object. This must be an *Object* field configured on one or more document types.
4. Enter a **Section Label** and **Section Name**. Only the label will appear for users.
5. Optional: In the **Show the section only in these lifecycle states** field, select one or more lifecycle states. See [lifecycle state details](#state-details).
6. Optional: Enter the **Section Help** content in the field. Only a maximum length of 255 unformatted characters would be shown. See [section help configuration](#section-help-configuration).
7. Click **Done**.

(insert screenshot)

## Section Help Configuration
You can add configurable help content for a section to communicate to users about the section and its usage for the organization. The user will be able to view the localized help content by clicking on the '?' icon in the applicable section. While the help configuration can be applied to the built-in sections, it can also be used with custom-made sections like attachments or workflow timelines.

To add help content to custom sections:

1. In the object page editor, click the **Edit** (pencil) icon to the right of the section.
2. Enter the **Section Help** content in the field. Only a maximum length of 255 unformatted characters would be shown.
3. Click **Done**.
