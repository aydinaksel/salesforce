When you save a record with an insert, update, or upsert statement, Salesforce performs a sequence of events in a certain order.

Before Salesforce executes these events on the server, the browser runs JavaScript validation if the record contains any dependent picklist fields. The validation limits each dependent picklist field to its available values. No other validation occurs on the client side.

> For a diagrammatic representation of the order of execution, see [Order of Execution Overview](https://architect.salesforce.com/1/asset/immutable/s/e6cf2ac/assets/images/Salesforce-Order-Of-Execution-Diagram.png "HTML (New Window)") on the [Salesforce Architects](https://architect.salesforce.com/ "HTML (New Window)") site. The diagram is specific to the API version indicated on it, and can be out-of-sync with the information here. This Apex Developer Guide page contains the most up-to-date information on the order of execution for this API version. To access a different API version, use the version picker for the [Apex Developer Guide](https://developer.salesforce.com/docs/atlas.en-us.250.0.apexcode.meta/apexcode/apex_dev_guide.htm "HTML (New Window)").

On the server, Salesforce performs events in this sequence.

1. Loads the original record from the database or initializes the record for an upsert statement.
2. Loads the new record field values from the request and overwrites the old values.
    
    Salesforce performs different validation checks depending on the type of request.
    
    - For requests from a standard UI edit page, Salesforce runs these system validation checks on the record:
        
        - Compliance with layout-specific rules
        - Required values at the layout level and field-definition level
        - Valid field formats
        - Maximum field length
        
        Additionally, if the request is from a User object on a standard UI edit page, Salesforce runs custom validation rules.
        
    - For requests from multiline item creation such as quote line items and opportunity line items, Salesforce runs custom validation rules.
    - For requests from other sources such as an Apex application or a SOAP API call, Salesforce validates only the foreign keys and restricted picklists. Before executing a trigger, Salesforce verifies that any custom foreign keys don’t refer to the object itself.
3. Executes record-triggered flows that are configured to run before the record is saved.
4. Executes all before triggers.
5. Runs most system validation steps again, such as verifying that all required fields have a non-null value, and runs any custom validation rules. The only system validation that Salesforce doesn't run a second time (when the request comes from a standard UI edit page) is the enforcement of layout-specific rules.
6. Executes duplicate rules. If the duplicate rule identifies the record as a duplicate and uses the block action, the record isn’t saved and no further steps, such as after triggers and workflow rules, are taken.
7. Saves the record to the database, but doesn't commit yet.
8. Executes all after triggers.
9. Executes assignment rules.
10. Executes auto-response rules.
11. Executes workflow rules. If there are workflow field updates:
    
    > This sequence applies only to workflow rules.
    
    1. Updates the record again.
    2. Runs system validations again. Custom validation rules, flows, duplicate rules, processes built with Process Builder, and escalation rules aren’t run again.
    3. Executes before update triggers and after update triggers, regardless of the record operation (insert or update), one more time (and only one more time)
12. Executes escalation rules.
13. Executes these Salesforce Flow automations, but not in a guaranteed order.
    
    - Processes built with Process Builder
    - Flows launched by workflow rules (flow trigger workflow actions pilot)
    
    > To control the order of execution of Salesforce Flow automations, use record-triggered flows. See [Manage Record-Triggered Flows](https://help.salesforce.com/s/articleView?id=sf.flow_trigger_explorer.htm&language=en_US "HTML (New Window)")
    
    When a process or flow executes a DML operation, the affected record goes through the save procedure.
    
14. Executes record-triggered flows that are configured to run after the record is saved
15. Executes entitlement rules.
16. If the record contains a roll-up summary field or is part of a cross-object workflow, performs calculations and updates the roll-up summary field in the parent record. Parent record goes through save procedure.
17. If the parent record is updated, and a grandparent record contains a roll-up summary field or is part of a cross-object workflow, performs calculations and updates the roll-up summary field in the grandparent record. Grandparent record goes through save procedure.
18. Executes Criteria Based Sharing evaluation.
19. Commits all DML operations to the database.
20. After the changes are committed to the database, executes post-commit logic. Examples of post-commit logic (in no particular order) include:
    - Sending email
    - Enqueued asynchronous Apex jobs, including queueable jobs and future methods
    - Asynchronous paths in record-triggered flows