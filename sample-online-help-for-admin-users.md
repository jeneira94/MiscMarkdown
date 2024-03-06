---
title: Setting Up Automated COA Email Intake (QMS)
---

Users and external parties can use this option to email single or multi-batch documents for existing {% include link.html path="_qualityone/configuring-coa-inspections-qualityone.md" text="_COA Inspection_ records" %} or to create new _COA Inspection_ records from automated email validation and data ingestion. After Vault receives emailed documents, Vault verifies if the files are valid to attach to a record according to certain parameters and ingests the data. You must set up this feature to allow users, _External Collaborators_, and external parties to upload COA documents to Vault for record analysis.

{% include note.html content='This feature is currently available only to Early Adopters. Contact your Veeva Representative for more details.' %}

## <a id="about"></a>About COA Email Intake Types

There are two (2) types of email intake processors, which require different setup instructions:

* **[Vault Interaction with Email Intake][1]**: Set up this email intake for users, which include the _External Collaborators_, to receive an email from Vault for an assigned task to submit single or multi-batch COA files. For external users to interact directly with Vault, ensure you've configured the external user as an {% include link.html path="_qualityone/configuring-external-collaboration-management-qualityone.md" text="_External Collaborator_" %} to assign and notify any workflow tasks.
* **[External Interaction with Email Intake][2]**: Set up this email intake by specifying inbound email addresses to allow specific external parties to send single or multi-batch COA attachments by email to Vault, unprompted by Vault. This type of email intake does not require initial external user setup as Vault interaction does, and only requires your user to have access to the inbound email address.

### <a id="about-intake"></a>Vault Interaction with Email Intake

For Vault interaction with COA email intake, when an _External Collaborator_ receives an [email for COA submission][3], the user can upload one (1) or more single or multi-batch COA files to a _COA Inspection_ record, or multiple relevant COA files to subsequent copies of the initial _COA Inspection_ record by email reply. Vault does one (1) of the following when an _External Collaborator_ sends a reply to validate the incoming emails and ingest any data found in each attachment:

* If an _External Collaborator_ emails one (1) valid single or multi-batch COA file for intake, Vault automatically attaches that file to the matching record name (as defined in the email subject line) and sends the _External Collaborator_ a success email reply.
  * Vault updates the lifecycle state for the matching record.
* If an _External Collaborator_ emails more than one (1) valid single or multi-batch COA file for intake, Vault automatically attaches the first valid file to the matching record name (as defined in the email subject line) as the initial record and sends a success email reply.
  * Vault verifies the remaining number of valid COA files and invokes the standard {% include link.html path="_platform/user/copying-object-records.md" text="_Copy Record_ action" %} (a shallow copy) until there is a matching record for each valid file. 
  * Each subsequent record copied has the initial record name as the _Source Inspection_ value with all valid available fields and their values copied to the new record.
  * Once Vault copies the valid amount of records, Vault attaches the remaining subsequent files to each record copied.
  * Vault updates the lifecycle state for each copied record.
* If an _External Collaborator_ emails zero (0) valid single or multi-batch COA files for intake, Vault sends a failure email reply for each error type detected.
* If an _External Collaborator_ emails a mixture of valid and invalid single or multi-batch COA files for intake, Vault handles all valid files according to the first or second criteria and handles all invalid files according to the third criteria. 
  * Vault sends a single success email reply for all valid files and failure email replies for each error type detected. 
  * Any COA files with the same error types are grouped into each error type email reply.

The COA file must be in the PNG, JPG, or JPEG file types for Vault to automate the email intake feature.

### <a id="about-external"></a>External Interaction with Email Intake

For external interaction with COA email intake, your user can upload one (1) or more related single or multi-batch COA files to Vault to trigger _COA Inspection_ record creation. These COA files are compiled from your organization's external parties, such as suppliers. Unlike [Vault interaction with email intake][1], external interaction allows the user to send COA files as long as you've configured a {% include link.html path="_platform/admin/viewing-vault-java-sdk-solutions.md" text="Vault Java SDK" %} extension for the inbound email address. Vault does one (1) of the following when a user sends an email to a specified Vault inbound email address for file validation and data ingestion:

* If the user emails one (1) or more valid single or multi-batch COA files for intake, Vault creates a _COA Inspection_ record for each valid attachment.
  * Vault classifies the attachments as _Documents_.
  * If there is a _Purchase Order_ number defined in the email subject line, Vault may associate the following related records: _Inspection Plan_, _Material_, and _Purchase Order Line Item_ records. Including the _Purchase Order_ number allows Vault to automatically analyze the COA file after validation and ingestion, if configured.
  * For each newly-created _COA Inspection_ record, Vault populates the fields with data values from the custom Vault Java SDK extension for all records.
  * Vault updates the lifecycle state for each newly-created record and sets the _COA Ingestion Status_ field to "Automatic".
* If the user emails zero (0) valid single or multi-batch COA files for intake, Vault sends a failure email reply with each error type detected.
* If the user emails a mixture of valid and invalid single or multi-batch COA files for intake, Vault handles all valid files according to the first criteria and handles all the invalid files according to the second criteria.
  * Only invalid files will be mentioned in the failure email reply with each error type detected.

The COA file must be in the PNG, JPG, or JPEG file types for Vault to automate the email intake feature.

## <a id="setup"></a>Setting Up Email Intake for COA Uploads

Ensure you follow the appropriate steps for the following email intake types:

* **[Setting Up Vault Interaction][4]**
* **[Setting Up External Interaction][5]**

### <a id="setup-intake"></a>Setting Up Vault Interaction

To allow internal and external users to upload single or multi-batch COA files using email intake to an existing _COA Inspection_ and copied records of a source _COA Inspection_, you must set up the following:

* Enable {% include link.html path="_platform/admin/dynamic-access-control-for-object-records.md" text="DAC" %} for the _Inspection_ object. When _External Collaborators_ trigger Vault to copy records from a _Source Inspection_ for multiple valid COA files, the _Sharing Settings_, such as DAC configurations and manual assignments, are also copied over to the new records as part of the trigger.
* Create a {% include link.html path="_platform/admin/managing-the-user-and-person-objects.md" anchor="#about-the-person-object" text="_Person_ record" %} and ensure the _Email_ field contains your organization's Reply-To email address for COA email intake. For example, your Reply-To email address may be "coa@organization.com".
* Navigate to **Admin > Settings > Application Settings > Inspection Management** and select the _Enable Automated COA Analysis_ checkbox. Enter the same Reply-To email address used for the _Person_ record. Setting up the Reply-To email allows _External Collaborators_ to reply to your organization's specified email address instead of the generic Vault email address. This ensures that the COA file is uploaded to the correct organization and the correct record.
* Create an {% include link.html path="_platform/admin/configuring-email-to-vault.md" text="inbound email address" %} for users to email to for automated COA email intake. When creating the inbound email address, ensure you select **COA Email Intake** for your _Email Processor_ and select **All Users in this Vault** for _Allowed Senders_.
* Configure {% include link.html path="_qualityone/configuring-external-collaboration-management-qualityone.md" text="External Collaboration Management" %} on the _Inspection_ object for _COA Inspection_ as the target object. When _External Collaborators_ trigger Vault to copy records from a _Source Inspection_ for multiple valid COA files, the _Sharing Settings_, such as _External Collaborator_ values, are also copied over to the new records as part of the trigger.
* Create an object {% include link.html path="_platform/admin/configuring-object-workflows.md" text="workflow" %} or modify the existing _Inspection_ object workflow, and include _Send CoA Upload Workflow Task Email_ as your _System Action_ step with _COA Submission_ as your _[Notification Template][3]_.
* Optional: When configuring the _COA Uploaded_ and _COA Uploaded with Missing Data_ lifecycle states for the _Inspection Lifecycle_, we recommend adding the {% include link.html path="_qualityone/configuring-coa-inspections-qualityone.md" anchor="#config-coa-actions" text="_Analyze COA_" %} entry action to trigger Vault to analyze the emailed COA file attached to the record.

### <a id="setup-external"></a>Setting Up External Interaction

To allow your organization's user to upload single or multi-batch COA files (compiled from external parties) and create new _COA Inspection_ records for each file outside of Vault, you must set up the following to allow external interaction with Vault's email intake feature:

* Set up your email intake for external interaction by importing a {% include link.html path="_platform/admin/using-configuration-migration-packages.md" anchor="#import" text="configuration migration inbound package" %}. Contact your Veeva Representative to access a VPK to deploy the custom Vault Java SDK configuration package. Implementing a custom SDK package allows your Vault to extract information from an incoming email and its attachments, and create _COA Inspection_ records for each valid attachment detected. See {% include external_link.html url="https://developer.veevavault.com/sdk/#QualityOne_QMS" text="Vault Java SDK in the Developer Portal" %} for more details.
* Navigate to **Admin > Configuration > Vault Java SDK > QualityOne COA Email Intake Handlers** and ensure that the custom SDK implementation's _Operational Status_ is set to "Enabled". Contact your Veeva Representative if your custom SDK implementation is missing or is disabled.
* Navigate to **Admin > Settings > Application Settings > Inspection Management** and select the appropriate **User Group for COA Email Intake Notification** options in the drop-down.
* Create an {% include link.html path="_platform/admin/configuring-email-to-vault.md" text="inbound email address" %} for users to email for automated COA email intake. When creating the inbound email address, ensure you do the following:
    * Enter the **Email Address** that matches the inbound email address defined in the custom SDK implementation. If you're unsure of what the email address should be, contact your Veeva Representative for assistance.
    * Select **Supplier Initiated COA Email Intake** for the _Email Processor_.
    * Select **All Users in this Vault** for the _Allowed Senders_.
* Optional: When configuring the _COA Uploaded_ and _COA Uploaded with Missing Data_ lifecycle states for the _Inspection Lifecycle_, we recommend adding the {% include link.html path="_qualityone/configuring-coa-inspections-qualityone.md" anchor="#config-coa-actions" text="_Analyze COA_" %} entry action to trigger Vault to analyze the emailed COA file attached to the record.

## <a id="notification"></a>Configuring Notification Templates

There are several email notification templates for the _COA Inspection_ object you can {% include link.html path="_platform/admin/email-and-messages-administration.md" text="customize" %} to which Vault automatically sends to _External Collaborators_ assigned on a _COA Inspection_ record and when Vault receives an external email from an approved email address.

Depending on your configuration for [Vault interaction][1], you may have a _System Action_ step that triggers an email notification to the assigned _External Collaborator_ requesting a COA file submission. Other Vault interaction email notification templates are sent to _External Collaborators_ and workflow owners depending on the validity of the email reply; whether the COA file was received by Vault, the COA file is submitted to the record, or there was a failure when sending the file in the email.

For external interaction email notification templates, Vault sends them depending on the errors found in the received email; if Vault fails to upload because the files are faulty, the files cannot be ingested, Vault cannot create a _COA Inspection_ record for certain COA files, or the Vault receiving the email is missing important [Admin setup][5].

### <a id="notif-template"></a>Notification Templates

These are the following notification templates used for email intake:

* Vault Interaction
  * _CoA Submission_ (`coa_submission__v`)
  * _Success: CoA Received_ (`success_coa_received__v`)
  * _CoA Email: Failed to upload the file_ (`coa_email_fail_upload_file__v`)
* External Interaction
  * _COA Email: Failed to Ingest Attachment_ (`coa_email_failed_to_ingest_attachment__v`)
  * _COA Email: No Files Ingested_ (`coa_email_no_files_ingested__v`)
  * _COA Email Processor Cannot Create Record_ (`coa_email_processor_cannot_create_record__v`)
  * _COA Missing Material Feature Flag Config_ (`coa_missing_material_ff_config__v`)

### <a id="message-temp-token"></a>Message Template Tokens

QualityOne Vaults can use additional token support using standard {% include link.html path="_platform/admin/email-and-messages-administration.md" text="object notification configurations" %}. {% include link.html path="_platform/admin/using-tokens.md" text="Tokens" %} are pieces of text that are replaced at the time the notification is used. To configure the notification templates, use tokens within the email content for document notification templates.

[0]: #about
[1]: #about-intake
[2]: #about-external
[3]: #notification
[4]: #setup-intake
[5]: #setup-external
