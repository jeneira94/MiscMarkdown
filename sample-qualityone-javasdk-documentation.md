<div id="QualityOne_QMS">

# QualityOne QMS

Built from Vault's *[Email Processor](#Email_Processors)* feature, Admins can define how Vault automatically creates *COA Inspection* records from emails sent to an *Inbound Email Address* in the Vault Admin UI by using a QualityOne-provisioned *Email Processor*: the *Supplier Initiated COA Email Processor*.

## Supplier Initiated COA Email Processor

In QualityOne QMS Vaults, the *Supplier Initiated COA Email Processor* allows your organization to send emails, containing your external parties COA file attachments, to Vault for document upload and record creation using an SDK entry point implementation. After creating a migration package from the implementation, Admins can [import and validate the migration package](https://qualityone.veevavault.help/en/lr/36919/#import) to Vault, which enables the implementation under *QualityOne COA Email Intake Handlers*. You can customize the logic in the implementation to process information extracted from incoming emails and define *COA Inspection* records with the extracted information.

Learn more about [setting up Automated COA Email Intake in Vault Help](https://qualityone.veevavault.help/en/lr/583672/).

### Understanding the SDK Entry Point Implementation

The *QualityOne COA Email Intake Handlers* implementation uses interfaces and annotation from the `coaemailintake` package available only for QualityOne QMS Vaults. The `coaemailintake` package includes the following:

* `QualityOneCoaEmailIntakeHandlerInfo` annotation:
  * Indicates the `QualityOneCoaEmailIntakeHandler` class. 
  * Specifies the inbound email address component name configured for the *Supplier Initiated COA Email Processor*.
* `QualityOneCoaEmailIntakeHandler` interface:
  * Invoked in the *Supplier Initiated COA Email Processor* to process an incoming email that the Vault Email Service receives.
  * Executes custom logic to build the *COA Inspection* record.
  * Expects setting both `QualityOneCoaEmailIntakeHandlerResponse` and `QualityOneIntakeInspection` to process COA email intake.
  * Referencing `QualityOneIntakeInspection` to create *COA Inspection* object records.
* `QualityOneCoaEmailIntakeHandlerContext` interface:
  * Provides access to contextual information exposed in the `QualityOneCoaEmailIntakeHandler`. 
  * Retrieves the sender's email address, the email's subject line, the list of `QualityOneEmailAttachment` objects, and the email body text. 
  * Creates an instance of `QualityOneIntakeInspection.Builder` and `QualityOneCoaEmailIntakeHandlerResponse.Builder`.
* `QualityOneEmailAttachment` interface:
  * Represents a file attached to an email.
  * Retrieves the attachment's file name, extension, and size (measured in bytes).
* `QualityOneIntakeInspection` interface:
  * Represents a *COA Inspection* record.
  * Provides methods to build a `QualityOneIntakeInspection` object.
* `QualityOneIntakeInspection.Builder` interface:
  * Sets the value for a field on the *COA Inspection* record.
  * Builds a `QualityOneIntakeInspection` object.
* `QualityOneCoaEmailIntakeHandlerResponse` interface:
  * Represents a response from executing the `QualityOneCoaEmailIntakeHandler`.
  * Provides methods to build a `QualityOneCoaEmailIntakeHandlerResponse` object.
* `QualityOneCoaEmailIntakeHandlerResponse.Builder` interface:
  * Builds the individual `QualityOneCoaEmailIntakeHandlerResponse` object used to create a *COA Inspection* record during the COA email intake process.
  * Builds a `QualityOneCoaEmailIntakeHandlerResponse` object.

### Processing Email Information for COA Email Intake

The following example presents how the implementation handles incoming emails received by the Vault Email Service to the inbound email address and processes the information extracted. The `QualityOneCoaEmailIntakeHandlerInfo` annotation defines accepted emails when the "To" field matches the [Admin-specified inbound email address](https://qualityone.veevavault.help/en/lr/583672/#setup-external) named `cholecap_coa_supplier_inbound__c`. 

```java
@QualityOneCoaEmailIntakeHandlerInfo(inboundEmailAddress = "cholecap_coa_supplier_inbound__c")
```

After executing the `QualityOneCoaEmailIntakeHandler`, there's a response to retrieve the list of email attachments and to create an empty list for excluded email attachments. The logic then excludes any attachments that are less than 1 megabyte in file size.

```java
@QualityOneCoaEmailIntakeHandlerInfo(inboundEmailAddress = "cholecap_coa_supplier_inbound__c")
public class EmailIntakeHandler implements QualityOneCoaEmailIntakeHandler {

  @Override
  public QualityOneCoaEmailIntakeHandlerResponse execute(QualityOneCoaEmailIntakeHandlerContext context) {
    List<QualityOneEmailAttachment> emailAttachments = context.getEmailAttachments();
    List<QualityOneEmailAttachment> excludedAttachments = VaultCollections.newList();

    for (QualityOneEmailAttachment emailAttachment : emailAttachments) {
      if (emailAttachment.getFileSize() < (long) 1024 * 1024) {
        excludedAttachments.add(emailAttachment);
      }
    }
  }
}
```

Then the implementation extracts the *Inspection Plan*'s ID value from the email subject line and builds a `QualityOneIntakeInspection` object containing the *Inspection Plan* value and the included email attachments.

```java
String inspectionPlan = context.getEmailSubject();

QualityOneIntakeInspection intakeInspection = context.newIntakeInspectionBuilder()
  .withValue("inspection_plan__v", inspectionPlan)
  .build();

return context.newCoaEmailIntakeProcessorResponseBuilder()
  .withIntakeInspection(intakeInspection)
  .withExcludedAttachments(excludedAttachments)
  .build();
```

Once the information has been processed, the implementation sends the `QualityOneIntakeInspection` object to Vault, triggering the *Supplier Initiated COA Email Intake* email processor to create new *COA Inspection* records and upload the email attachments as documents based on the information extracted from the `QualityOneIntakeInspection` object.

```java
@QualityOneCoaEmailIntakeHandlerInfo(inboundEmailAddress = "cholecap_coa_supplier_inbound__c")
public class EmailIntakeHandler implements QualityOneCoaEmailIntakeHandler {

  @Override
  public QualityOneCoaEmailIntakeHandlerResponse execute(QualityOneCoaEmailIntakeHandlerContext context) {
    List<QualityOneEmailAttachment> emailAttachments = context.getEmailAttachments();
    List<QualityOneEmailAttachment> excludedAttachments = VaultCollections.newList();

    for (QualityOneEmailAttachment emailAttachment : emailAttachments) {
      if (emailAttachment.getFileSize() < (long) 1024 * 1024) {
        excludedAttachments.add(emailAttachment);
      }
    }

    String inspectionPlan = context.getEmailSubject();

    QualityOneIntakeInspection intakeInspection = context.newIntakeInspectionBuilder()
      .withValue("inspection_plan__v", inspectionPlan)
      .build();

    return context.newCoaEmailIntakeProcessorResponseBuilder()
      .withIntakeInspection(intakeInspection)
      .withExcludedAttachments(excludedAttachments)
      .build();
  }
}
```

### Customizing Implementation Logic

When customizing the logic for your *Supplier Initiated COA Email Processor*, ensure you include the logic to extract either the *Inspection Plan*, *Purchase Order*, *Purchase Order Line Item*, or *Material* value from the subject line. Extracting either of these values helps to relate the *Inspection Plan* to the newly-created *COA Inspection* record when the *Supplier Initiated COA Email Intake* email processor triggers. You can customize the logic to not exclude any files from an incoming email or to exclude specific files based on your organization's criteria. You can customize the logic in the `QualityOneCoaEmailIntakeHandler` interface for specific ways to handle errors. For example, you may want to set a specific *COA Ingestion Status* and *COA Ingestion Error Type* value when the interface fails to identify the subject line value or when errors occur when running the `QualityOneIntakeInspection` interface.
