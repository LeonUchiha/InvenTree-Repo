# InvenTree Parts — UI Testing Scenarios
## Requirement Analysis Report

> **Scope:** InvenTree "Parts" Section — Sub-pages covered:
> - Part Views
> - Part Parameters (via Parameters tab in Part Views)
> - Part Templates
> - Part Revisions
> - Creating a Part
>
> **Purpose:** This document provides structured, actionable testing scenarios to assist a downstream Test Case Creator Agent in generating manual UI test cases.

---

## Table of Contents

1. [Creating a Part](#1-creating-a-part)
2. [Part Views](#2-part-views)
3. [Part Parameters](#3-part-parameters)
4. [Part Templates](#4-part-templates)
5. [Part Revisions](#5-part-revisions)

---

## 1. Creating a Part

**Sub-Page/Component Name:** Creating a Part (`/part/create/`)

**Description:** This sub-page allows users to create new parts via a form-based wizard interface or import parts from external files and supplier integrations. The creation form contains required and optional fields; on successful submission the browser is redirected to the new part's detail page. The page includes options for creating initial stock and adding supplier data.

---

### Testing Scenarios

#### 1.1 Entity: Part Creation Form — Basic Fields

**Preconditions:** User is logged in with "create" permission for the Part permission group. The user is on the Parts list view.

---

**Scenario 1.1.1 (Positive) — Create a Part with Required Fields Only**
- **Action:** Navigate to the Parts view. Click the *Add Parts* dropdown and select *Create Part*. Fill in only the required field (Part Name) and click *Submit*.
- **Expected Behavior:** A new part is created. The browser redirects to the new part's detail page showing the part name correctly. A success indicator is displayed.

---

**Scenario 1.1.2 (Positive) — Create a Part with All Fields Populated**
- **Action:** Open the *Create Part* form. Fill in all available fields: Name, Description, IPN (Internal Part Number), Category, Keywords, External Link, Units of Measure, Revision, and all attribute toggles (Trackable, Assembly, Component, Purchaseable, Salable, Virtual, Testable). Click *Submit*.
- **Expected Behavior:** Part is successfully created with all fields saved accurately. The new part detail page reflects every entered value.

---

**Scenario 1.1.3 (Negative) — Submit the Form Without a Part Name**
- **Action:** Open the *Create Part* form. Leave the Name field empty. Click *Submit*.
- **Expected Behavior:** Form submission is blocked. An inline validation error is displayed near the Name field indicating the field is required (e.g., "This field is required."). No new part is created.

---

**Scenario 1.1.4 (Negative) — Submit a Duplicate Part Name**
- **Action:** Open the *Create Part* form. Enter a part name identical to an already-existing part. Click *Submit*.
- **Expected Behavior:** An appropriate validation error is shown either at the field level or as a form-level alert. No duplicate part is created.

---

**Scenario 1.1.5 (Negative) — Enter an Invalid External Link**
- **Action:** Open the *Create Part* form. In the *External Link* field, type plain text that is not a valid URL (e.g., `not-a-url`). Click *Submit*.
- **Expected Behavior:** Form submission fails with a validation error on the External Link field indicating an invalid URL format.

---

**Scenario 1.1.6 (Edge Case) — Enter Extremely Long Part Name**
- **Action:** Open the *Create Part* form. Enter a very long string (e.g., 500+ characters) in the Name field. Click *Submit*.
- **Expected Behavior:** The system either truncates to the allowed limit and saves, or displays a validation error specifying the maximum character count. No crash or silent data loss occurs.

---

**Scenario 1.1.7 (Edge Case) — Create Part with Special Characters in Name**
- **Action:** Open the *Create Part* form. Enter a name containing special characters (e.g., `<Widget & Gadget "v2">`). Click *Submit*.
- **Expected Behavior:** The part is either created successfully with the name stored/displayed safely (properly escaped), or a validation error is shown explaining which characters are not allowed.

---

#### 1.2 Entity: Part Creation Form — Initial Stock Option

**Preconditions:** The *Create Initial Stock* global setting is enabled. User is on the Create Part form.

---

**Scenario 1.2.1 (Positive) — Create Part with Initial Stock**
- **Action:** Open the *Create Part* form. Fill in the required fields. Check the *Create Initial Stock* checkbox. Enter a valid quantity and location for initial stock. Click *Submit*.
- **Expected Behavior:** Part is created and an initial stock item is created with the specified quantity. The Stock tab on the new part page shows the initial stock item.

---

**Scenario 1.2.2 (Negative) — Create Initial Stock with Zero Quantity**
- **Action:** Open the *Create Part* form. Check *Create Initial Stock*. Enter `0` as the quantity. Click *Submit*.
- **Expected Behavior:** Either a validation error is displayed indicating the quantity must be greater than zero, or the stock item is created with zero quantity (system-defined behavior should be confirmed and validated).

---

**Scenario 1.2.3 (Negative) — Create Initial Stock with Negative Quantity**
- **Action:** Open the *Create Part* form. Check *Create Initial Stock*. Enter `-5` as the quantity. Click *Submit*.
- **Expected Behavior:** A validation error is shown indicating the quantity must be a non-negative number. No stock item is created.

---

#### 1.3 Entity: Part Creation Form — Supplier Options

**Preconditions:** The Part is marked as *Purchaseable*. User is on the Create Part form.

---

**Scenario 1.3.1 (Positive) — Add Supplier Data During Part Creation**
- **Action:** Open the *Create Part* form. Mark the part as *Purchaseable*. Check *Add Supplier Data*. Fill in a valid Supplier and Manufacturer Part number. Click *Submit*.
- **Expected Behavior:** Part is created. The Suppliers tab on the new part detail page shows the entered supplier and manufacturer information.

---

**Scenario 1.3.2 (Negative) — Add Supplier Data with Missing Supplier**
- **Action:** Open the *Create Part* form. Mark as *Purchaseable*, check *Add Supplier Data*, but leave the Supplier field empty. Click *Submit*.
- **Expected Behavior:** A validation error is displayed indicating the Supplier field is required when adding supplier data.

---

#### 1.4 Entity: Add Parts Dropdown — Permissions

**Preconditions:** User is logged in WITHOUT "create" permission for the Part permission group.

---

**Scenario 1.4.1 (Negative) — Access Create Part Without Permission**
- **Action:** Navigate to the Parts list view as a user without "create" permission.
- **Expected Behavior:** The *Add Parts* dropdown button is not visible or is disabled for the user. The user cannot access the Create Part form.

---

#### 1.5 Entity: Import from File

**Preconditions:** User has "create" permission for Parts. A valid CSV/spreadsheet file with part data is available.

---

**Scenario 1.5.1 (Positive) — Import Parts from a Valid File**
- **Action:** Click *Add Parts* > *Import from File*. Upload a valid CSV file with properly mapped fields. Step through the import wizard, confirm field mappings. Submit.
- **Expected Behavior:** The data import wizard launches. Parts are imported successfully and appear in the parts list.

---

**Scenario 1.5.2 (Negative) — Import Parts from an Invalid File Format**
- **Action:** Click *Add Parts* > *Import from File*. Attempt to upload a file in an unsupported format (e.g., `.exe` or `.png`).
- **Expected Behavior:** The upload is rejected with an appropriate error message indicating the file format is not supported.

---

**Scenario 1.5.3 (Negative) — Import Parts from an Empty File**
- **Action:** Click *Add Parts* > *Import from File*. Upload an empty CSV file (no data rows, only headers or completely empty).
- **Expected Behavior:** The wizard either shows a warning that the file contains no data to import, or an informative error is shown. No parts are created.

---

## 2. Part Views

**Sub-Page/Component Name:** Part Views (`/part/views/`)

**Description:** The Part detail view page is the central hub for viewing and managing all information about a specific part. It is divided into a Part Details header panel and multiple tabbed sections (Stock, Variants, Allocations, BOM, Build Orders, Used In, Suppliers, Purchase Orders, Sales Orders, Stock History, Test Templates, Related Parts, Parameters, Attachments, Notes). The page also features a category breadcrumb and a part image panel.

---

### Testing Scenarios

#### 2.1 Entity: Part Detail Header & Category Breadcrumb

**Preconditions:** At least one part exists and is assigned to a category.

---

**Scenario 2.1.1 (Positive) — View Part Details Panel**
- **Action:** Navigate to a part detail page. Click the *Show Part Details* toggle button to expand the header panel.
- **Expected Behavior:** The header panel expands to show all part detail fields: Name, Description, IPN, Revision, Keywords, External Link, Creation Date, Units, and active attributes (Assembly, Trackable, etc.).

---

**Scenario 2.1.2 (Positive) — Verify Category Breadcrumb Navigation**
- **Action:** Navigate to a part assigned to a nested category (e.g., Electronics > Resistors > SMD). Observe the breadcrumb at the top of the page.
- **Expected Behavior:** The breadcrumb correctly displays the full category path. Each segment of the breadcrumb is a clickable link that navigates to the respective category page.

---

**Scenario 2.1.3 (Negative) — Navigate to Invalid Part URL**
- **Action:** Manually type a non-existent part ID in the URL (e.g., `/part/99999/`).
- **Expected Behavior:** The application displays an appropriate error message (e.g., "Part not found" or an HTTP 404 page). The user is not shown any part data.

---

#### 2.2 Entity: Part Image

**Preconditions:** User is viewing a part detail page.

---

**Scenario 2.2.1 (Positive) — Upload a New Part Image**
- **Action:** On the part detail page, hover over the part image area. Click *Upload new image*. Select a valid image file (JPG/PNG). Confirm the upload.
- **Expected Behavior:** The image uploads successfully and is displayed on the part detail page and as a thumbnail in list views.

---

**Scenario 2.2.2 (Positive) — Select Part Image from Existing Images**
- **Action:** Hover over the part image area. Click *Select from existing images*. Choose an image from the list.
- **Expected Behavior:** The selected image is associated with the part and displayed immediately.

---

**Scenario 2.2.3 (Positive) — Delete a Part Image**
- **Action:** Hover over the part image area. Click *Delete image*. Confirm the deletion.
- **Expected Behavior:** The part image is removed. The image area reverts to a default placeholder.

---

**Scenario 2.2.4 (Negative) — Upload an Invalid File as Part Image**
- **Action:** Hover over the part image area. Click *Upload new image*. Select a non-image file (e.g., a `.pdf` or `.txt` file).
- **Expected Behavior:** The upload is rejected with an error message indicating only valid image file types are accepted.

---

#### 2.3 Entity: Part Tabs — Stock Tab

**Preconditions:** A part exists with at least one stock item.

---

**Scenario 2.3.1 (Positive) — View Stock Items for a Part**
- **Action:** Navigate to a part's detail page. Click the *Stock* tab.
- **Expected Behavior:** A table is displayed listing all stock items for the part, with columns including quantity, location, and status. The stock count is accurate.

---

**Scenario 2.3.2 (Positive) — Create a New Stock Item from Part View**
- **Action:** On the *Stock* tab, click the *New Stock Item* button. Fill in the required fields (quantity, location). Submit the form.
- **Expected Behavior:** A new stock item is created and immediately appears in the stock table. The total stock count updates accordingly.

---

**Scenario 2.3.3 (Positive) — Export Stock Data**
- **Action:** On the *Stock* tab, click the *Export* button. Select a file format and confirm.
- **Expected Behavior:** A file download is initiated containing the stock data for all stock items of the selected part.

---

**Scenario 2.3.4 (Positive) — Perform Stock Actions on Selected Items**
- **Action:** On the *Stock* tab, select one or more stock items using the checkboxes. Open the *Stock Actions* dropdown menu.
- **Expected Behavior:** The Stock Actions dropdown becomes enabled and shows available actions (e.g., Move, Count, Transfer). The actions can be triggered on the selected items.

---

**Scenario 2.3.5 (Edge Case) — View Stock Tab for a Part with No Stock**
- **Action:** Navigate to the detail page of a part that has no stock items. Click the *Stock* tab.
- **Expected Behavior:** The stock table is displayed but empty, with an informative message (e.g., "No stock items found"). The *New Stock Item* button is still available.

---

#### 2.4 Entity: Part Tabs — Conditional Visibility

**Preconditions:** Parts with various attribute combinations exist.

---

**Scenario 2.4.1 (Positive) — Variants Tab Visible for Template Parts**
- **Action:** Navigate to a part that is marked as a *Template*. Check the available tabs.
- **Expected Behavior:** The *Variants* tab is visible and accessible.

---

**Scenario 2.4.2 (Negative) — Variants Tab Not Visible for Non-Template Parts**
- **Action:** Navigate to a standard (non-template) part. Check the available tabs.
- **Expected Behavior:** The *Variants* tab is NOT visible.

---

**Scenario 2.4.3 (Positive) — Suppliers Tab Visible for Purchaseable Parts**
- **Action:** Navigate to a part marked as *Purchaseable*. Check the available tabs.
- **Expected Behavior:** The *Suppliers* tab and *Purchase Orders* tab are both visible and contain relevant data.

---

**Scenario 2.4.4 (Negative) — Suppliers Tab Not Visible for Non-Purchaseable Parts**
- **Action:** Navigate to a part that is NOT marked as *Purchaseable*. Check the available tabs.
- **Expected Behavior:** The *Suppliers* tab and *Purchase Orders* tab are NOT visible.

---

**Scenario 2.4.5 (Positive) — BOM Tab Visible for Assembly Parts**
- **Action:** Navigate to a part marked as *Assembly*. Check the available tabs.
- **Expected Behavior:** The *Bill of Materials* tab is visible and accessible.

---

**Scenario 2.4.6 (Positive) — Allocations Tab Visible for Component or Salable Parts**
- **Action:** Navigate to a part marked as *Component* or *Salable*. Check the available tabs.
- **Expected Behavior:** The *Allocations* tab is visible and displays pending build/sales order allocations.

---

#### 2.5 Entity: Part Tabs — Notes & Attachments

**Preconditions:** User is viewing a part detail page.

---

**Scenario 2.5.1 (Positive) — Add a Note with Markdown Formatting**
- **Action:** Click the *Notes* tab on a part detail page. Enter a note with markdown (e.g., `# Heading`, `**bold text**`, `- list item`). Save the note.
- **Expected Behavior:** The note is saved and the markdown is rendered correctly in the notes display view.

---

**Scenario 2.5.2 (Positive) — Upload a File Attachment**
- **Action:** Click the *Attachments* tab. Click the upload button. Select a valid file (e.g., a PDF datasheet). Add a comment. Upload.
- **Expected Behavior:** The file attachment is saved and listed in the attachments table with its filename and comment.

---

**Scenario 2.5.3 (Negative) — Attempt to Upload an Oversized Attachment**
- **Action:** Click the *Attachments* tab. Attempt to upload a file that exceeds the maximum allowed file size.
- **Expected Behavior:** The upload is rejected with a clear error message indicating the file size limit.

---

#### 2.6 Entity: Related Parts

**Preconditions:** The "Related Parts" feature is enabled in Global Settings. At least two parts exist.

---

**Scenario 2.6.1 (Positive) — Add a Related Part**
- **Action:** On a part detail page, navigate to the *Related Parts* section. Click *Add Related Part*. Select another part from the list. Save.
- **Expected Behavior:** The selected part appears in the Related Parts table showing a link between the two parts.

---

**Scenario 2.6.2 (Negative) — View Related Parts When Feature is Disabled**
- **Action:** Disable the *Related Parts* feature in global part settings. Navigate to any part detail page.
- **Expected Behavior:** The *Related Parts* section is not visible on any part detail page.

---

## 3. Part Parameters

**Sub-Page/Component Name:** Part Parameters (Parameters tab within Part Views)

**Description:** Parts can have multiple custom parameters defined. Parameters are displayed in the *Parameters* tab on the Part detail view. Parameters are defined by a template (name + units) and assigned a value for each part instance. Locked parts restrict parameter modification.

---

### Testing Scenarios

#### 3.1 Entity: Viewing Parameters

**Preconditions:** A part exists with one or more parameters defined.

---

**Scenario 3.1.1 (Positive) — View Parameters for a Part**
- **Action:** Navigate to a part detail page. Click the *Parameters* tab.
- **Expected Behavior:** A table is displayed showing all parameters for the part, with columns for parameter name, value, and units.

---

**Scenario 3.1.2 (Edge Case) — View Parameters Tab for Part with No Parameters**
- **Action:** Navigate to a part detail page that has no parameters defined. Click the *Parameters* tab.
- **Expected Behavior:** The parameters table is empty with an informative message. An option to add a parameter is still available.

---

#### 3.2 Entity: Adding/Editing Parameters

**Preconditions:** User has edit permission. Part is NOT locked.

---

**Scenario 3.2.1 (Positive) — Add a New Parameter to a Part**
- **Action:** On the *Parameters* tab, click the *Add Parameter* button. Select a valid parameter template from the dropdown. Enter a valid value. Save.
- **Expected Behavior:** The new parameter appears in the parameters table with the correct name, value, and units.

---

**Scenario 3.2.2 (Positive) — Edit an Existing Parameter Value**
- **Action:** On the *Parameters* tab, click the edit button next to an existing parameter. Change the value to a different valid value. Save.
- **Expected Behavior:** The parameter value is updated in the table immediately. The old value is no longer displayed.

---

**Scenario 3.2.3 (Positive) — Delete a Parameter from a Part**
- **Action:** On the *Parameters* tab, click the delete button next to a parameter. Confirm the deletion.
- **Expected Behavior:** The parameter is removed from the table. A success notification may be shown.

---

**Scenario 3.2.4 (Negative) — Add a Parameter with No Value**
- **Action:** On the *Parameters* tab, click *Add Parameter*. Select a parameter template but leave the value field empty. Try to save.
- **Expected Behavior:** A validation error is displayed indicating the value is required.

---

**Scenario 3.2.5 (Negative) — Add the Same Parameter Template Twice**
- **Action:** On the *Parameters* tab, add a parameter for a given template. Attempt to add the same parameter template again.
- **Expected Behavior:** An error is shown indicating a duplicate parameter cannot be added for the same template on the same part.

---

#### 3.3 Entity: Locked Part — Parameter Restrictions

**Preconditions:** A part is marked as *Locked*.

---

**Scenario 3.3.1 (Negative) — Attempt to Add Parameter to a Locked Part**
- **Action:** Navigate to a locked part's detail page. Click the *Parameters* tab. Attempt to add a new parameter.
- **Expected Behavior:** The *Add Parameter* button is either disabled or absent. If accessible, an error message is shown preventing the action: "Cannot modify parameters of a locked part."

---

**Scenario 3.3.2 (Negative) — Attempt to Edit Parameter of a Locked Part**
- **Action:** Navigate to a locked part's *Parameters* tab. Attempt to edit the value of an existing parameter.
- **Expected Behavior:** The edit action is blocked. The edit button is disabled or an error prevents saving changes.

---

**Scenario 3.3.3 (Negative) — Attempt to Delete Parameter of a Locked Part**
- **Action:** Navigate to a locked part's *Parameters* tab. Attempt to delete a parameter.
- **Expected Behavior:** The delete action is blocked. The delete button is disabled or an error is shown.

---

## 4. Part Templates

**Sub-Page/Component Name:** Part Templates (`/part/template/`)

**Description:** A Template Part is one that can have *Variants* derived from it. Enabling the Template attribute on a part allows for the creation of variant parts, which inherit the relationship. Serial numbers must be unique across all variants of a template. Stock for a template includes stock for all its variants.

---

### Testing Scenarios

#### 4.1 Entity: Enabling Template Attribute

**Preconditions:** User has edit permissions on a part. A standard (non-template) part exists.

---

**Scenario 4.1.1 (Positive) — Enable Template Attribute on a Part**
- **Action:** Navigate to a part's detail page. Click the *Details* tab. Locate the *Template* toggle switch and toggle it to the ON (green) position. Save changes.
- **Expected Behavior:** The part is successfully marked as a template. The *Variants* tab becomes visible on the part detail page. The template toggle shows as active (green/on).

---

**Scenario 4.1.2 (Positive) — Disable Template Attribute on a Part with No Variants**
- **Action:** On a template part that has no variants, navigate to *Details*. Toggle the *Template* switch to OFF. Save.
- **Expected Behavior:** The template attribute is disabled. The *Variants* tab is no longer visible. The part behaves as a standard part.

---

**Scenario 4.1.3 (Negative) — Disable Template Attribute on a Part with Existing Variants**
- **Action:** On a template part that already has variants, attempt to toggle the *Template* attribute to OFF.
- **Expected Behavior:** The system blocks the action and displays an error message (e.g., "Cannot disable template: variants exist"). The part remains a template.

---

#### 4.2 Entity: Creating Variants

**Preconditions:** A Template part exists. User has "create" permission.

---

**Scenario 4.2.1 (Positive) — Create a Variant of a Template Part**
- **Action:** Navigate to a template part's detail page. Click the *Variants* tab. Click the *New Variant* button. The Duplicate Part form appears. Fill in a unique name and other fields. Click *Submit*.
- **Expected Behavior:** A new variant part is created and linked to the template. The variant appears in the *Variants* tab of the template part. The variant's detail page shows a reference back to the template.

---

**Scenario 4.2.2 (Positive) — Create Multiple Variants with Unique Names**
- **Action:** Create two different variants from the same template part, each with a distinct name.
- **Expected Behavior:** Both variants are created successfully. Both appear in the *Variants* tab of the template. Each variant has a unique identity.

---

**Scenario 4.2.3 (Negative) — Create a Variant Without Changing the Name**
- **Action:** Navigate to the *Variants* tab of a template part. Click *New Variant*. In the Duplicate Part form, do NOT change the part name (keeping it identical to the template). Click *Submit*.
- **Expected Behavior:** An error is shown indicating the name must be unique or different from the template. No duplicate part is created.

---

**Scenario 4.2.4 (Negative) — Create a Variant on a Non-Template Part**
- **Action:** Navigate to a standard (non-template) part detail page. Check the *Variants* tab.
- **Expected Behavior:** The *Variants* tab is not visible, and therefore no variant creation button is accessible.

---

#### 4.3 Entity: Template/Variant Serial Number Uniqueness

**Preconditions:** A template part with at least two variants exists. Parts are *Trackable*.

---

**Scenario 4.3.1 (Positive) — Assign Unique Serial Numbers Across Variants**
- **Action:** Assign serial number `SN-001` to a stock item of Variant A. Assign a different serial number `SN-002` to a stock item of Variant B.
- **Expected Behavior:** Both serial numbers are accepted and stored successfully.

---

**Scenario 4.3.2 (Negative) — Assign Duplicate Serial Number Across Variants**
- **Action:** Assign serial number `SN-001` to a stock item of Variant A. Attempt to assign the same serial number `SN-001` to a stock item of Variant B (or the Template part itself).
- **Expected Behavior:** The system rejects the duplicate serial number with an error message (e.g., "Serial number SN-001 is already in use for this template's family").

---

#### 4.4 Entity: Template Stock Reporting

**Preconditions:** A template part with variants exists. Variants have stock items.

---

**Scenario 4.4.1 (Positive) — Template Stock Count Includes Variant Stock**
- **Action:** Navigate to the template part's *Stock* tab.
- **Expected Behavior:** The total stock count displayed for the template includes the combined stock of all its variants, not just direct template stock items.

---

## 5. Part Revisions

**Sub-Page/Component Name:** Part Revisions (`/part/revision/`)

**Description:** Part Revisions allow users to track changes to a part over time without overwriting existing data. Each revision is itself a full Part with its own data (stock, BOM, parameters, etc.) but linked to the original part via the *Revision Of* field. Revision navigation is available via a dropdown selector when multiple revisions exist.

---

### Testing Scenarios

#### 5.1 Entity: Revision Settings

**Preconditions:** User has Admin access to global settings.

---

**Scenario 5.1.1 (Positive) — Enable Part Revisions Global Setting**
- **Action:** Navigate to Administration > Global Settings. Find *Part Revisions* setting and ensure it is enabled (`True`). Save.
- **Expected Behavior:** The Revision field is visible and editable on part detail/creation pages.

---

**Scenario 5.1.2 (Positive) — Disable Part Revisions Global Setting**
- **Action:** Navigate to Administration > Global Settings. Set *Part Revisions* to disabled. Save.
- **Expected Behavior:** The Revision field is hidden from part detail pages. No revision-related options are exposed in the UI.

---

**Scenario 5.1.3 (Positive) — Enable Assembly Revision Only Setting**
- **Action:** Navigate to Administration > Global Settings. Enable *Assembly Revision Only*. Save.
- **Expected Behavior:** The Revision field and revision creation options are only visible for parts marked as *Assembly*. Non-assembly parts do not show revision functionality.

---

#### 5.2 Entity: Creating a Revision

**Preconditions:** Part Revisions is enabled in global settings. A part exists. User has "create" permission.

---

**Scenario 5.2.1 (Positive) — Create a New Revision of a Part**
- **Action:** Navigate to a part's detail page. Click the *Part Actions* menu (three vertical dots, top right). Select *Duplicate Part*. In the Duplicate Part form, set the *Revision Of* field to the original part. Enter a unique revision code in the *Revision* field. Click *Submit*.
- **Expected Behavior:** A new revision part is created and linked to the original. The browser redirects to the new revision's detail page. The page shows the *Revision Of* reference pointing to the original part.

---

**Scenario 5.2.2 (Positive) — Verify Revision Selector Appears with Multiple Revisions**
- **Action:** Create at least two revisions of the same part. Navigate to any of the revisions' detail pages.
- **Expected Behavior:** A *Select Part Revision* dropdown is visible at the top of the part page, listing all available revisions of the part.

---

**Scenario 5.2.3 (Positive) — Navigate Between Revisions Using the Dropdown**
- **Action:** On a part page with multiple revisions, use the *Select Part Revision* dropdown to switch to a different revision.
- **Expected Behavior:** The browser navigates to the selected revision's detail page. The page content updates to reflect the selected revision's data (name, parameters, BOM, etc.).

---

**Scenario 5.2.4 (Edge Case) — Revision Selector Not Visible for Single Revision**
- **Action:** Navigate to a part that has a *Revision* field filled in but is the ONLY revision of that part (no sibling revisions).
- **Expected Behavior:** The *Select Part Revision* dropdown is NOT visible, as there are no other revisions to navigate to.

---

**Scenario 5.2.5 (Negative) — Attempt to Create a Circular Revision Reference**
- **Action:** Navigate to Part A's *Duplicate Part* form. Set the *Revision Of* field to Part A itself (circular reference). Click *Submit*.
- **Expected Behavior:** The system rejects this with a validation error: "A part cannot be a revision of itself."

---

**Scenario 5.2.6 (Negative) — Attempt to Create a Revision with a Duplicate Revision Code**
- **Action:** Create Revision "B" of Part A. Then attempt to create another revision of Part A also named "B".
- **Expected Behavior:** The system rejects the duplicate revision code with an error: "A revision with this code already exists for this part."

---

**Scenario 5.2.7 (Negative) — Attempt to Create a Revision of a Template Part**
- **Action:** Navigate to a part that is marked as a *Template*. Attempt to create a revision using the Duplicate Part form with *Revision Of* set to this template part.
- **Expected Behavior:** The system blocks the action with a validation error: "Template parts cannot have revisions."

---

#### 5.3 Entity: Revision Field on Part Detail

**Preconditions:** Part Revisions is enabled. User is viewing a part detail page.

---

**Scenario 5.3.1 (Positive) — View Revision Field in Part Details**
- **Action:** Navigate to a part detail page. Expand the *Show Part Details* panel.
- **Expected Behavior:** The *Revision* field is visible in the details panel. If a revision code is set, it is displayed.

---

**Scenario 5.3.2 (Positive) — Edit the Revision Field of a Part**
- **Action:** Navigate to a part detail page. Edit the part. Change the *Revision* field to a new unique revision code. Save.
- **Expected Behavior:** The revision code is updated and the new value is reflected in the Part Details panel.

---

#### 5.4 Entity: Revision Restrictions for Variant Parts

**Preconditions:** A template part with variants exists. Part Revisions is enabled.

---

**Scenario 5.4.1 (Positive) — Create a Revision of a Variant Part**
- **Action:** Navigate to a variant part (one linked to a template). Attempt to create a revision (Duplicate Part with *Revision Of* set to this variant).
- **Expected Behavior:** The revision is allowed and created successfully. The new revision also inherits the template reference of the original variant.

---

**Scenario 5.4.2 (Negative) — Create a Variant Part Revision Not Pointing to the Same Template**
- **Action:** Take Variant A of Template T. Create a revision of Variant A. In the revision form, attempt to point the new part to a different template (not T).
- **Expected Behavior:** The system blocks this with a validation error: "A revision of a variant must reference the same template as the original variant."

---

## Cross-Cutting Testing Scenarios

### Navigation & UI Consistency

**Scenario X.1 (Positive) — Breadcrumb Navigation from Part View Back to Category**
- **Preconditions:** A part is assigned to a category.
- **Action:** From a part's detail page, click a link in the category breadcrumb.
- **Expected Behavior:** The browser navigates to the category page, displaying the list of parts under that category.

**Scenario X.2 (Positive) — Verify "Back to Top" Button on Long Pages**
- **Preconditions:** User is on a part detail page with many tabs/content.
- **Action:** Scroll to the bottom of the page. Click the *Back to top* button.
- **Expected Behavior:** The page scrolls back to the top.

**Scenario X.3 (Positive) — Verify Tab Navigation Persistence**
- **Preconditions:** User is on a part detail page and has navigated to the *Parameters* tab.
- **Action:** Refresh the browser page.
- **Expected Behavior:** The active tab is remembered (either via URL hash or browser state) and the *Parameters* tab is still active after refresh.

---

*Document generated from InvenTree documentation (stable release). Last updated: April 2026.*
