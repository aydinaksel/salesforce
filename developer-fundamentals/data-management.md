# Import Data

## Introduction to Data Import

Salesforce offers two main methods for importing data.

- **Data Import Wizard**—this tool, accessible through the Setup menu, lets you import data in common standard objects, such as contacts, leads, accounts, as well as data in custom objects. It can import up to 50,000 records at a time. It provides a simple interface to specify the configuration parameters, data sources, and the field mappings that map the field names in your import file with the field names in Salesforce.  
    
- **Data Loader**—this is a client application that can import up to 150 million records at a time, of any data type, either from files or a database connection. It can be operated either through the user interface or the command line. In the latter case, you need to specify data sources, field mappings, and other parameters via configuration files. This makes it possible to automate the import process, using API calls.

## Data Import Wizard Considerations

This information can help you integrate your imported data into Salesforce.

- New Values for Picklists and Multi-Select Picklists—If you import a picklist value that doesn’t match an existing picklist value:
	- For an unrestricted picklist, the Data Import Wizard uses the value that’s in the import file. 
	- For a restricted picklist, the Data Import Wizard uses the picklist’s default value.  
- **Multi-Select Picklists**—To import multiple values into a multi-select picklist, separate the values by a semicolon in your import file.
- **Checkboxes**—To import data into a checkbox field, use 1 for checked values and 0 for unchecked values.
- **Default Values**—For picklist, multi-select picklist, and checkbox fields, if you do not map the field in the import wizard, the default value for the field, if any, is automatically inserted into the new or updated record.
- **Date/Time Fields**—Ensure that the format of any date/time fields you are importing matches how they display in Salesforce per your locale setting.
- **Formula Fields**—Formula fields cannot accept imported data because they are read-only.
- **Field Validation Rules**—Salesforce runs validation rules on records before they are imported. Records that fail validation aren’t imported. Consider deactivating the appropriate validation rules before running an import if they affect the records you are importing.

# Export Data

## Introduction to Data Export

Salesforce offers two main methods for exporting data.

- **Data Export Service**—an in-browser service, accessible through the Setup menu. It allows you to export data manually once every 7 days (for weekly export) or 29 days (for monthly export). You can also export data automatically at weekly or monthly intervals. Weekly exports are available in Enterprise, Performance, and Unlimited Editions. In Professional Edition and Developer Edition, you can generate backup files only every 29 days, or automatically at monthly intervals only.  
    
- **Data Loader**—a client application that you must install separately. It can be operated either through the user interface or the command line. The latter option is useful if you want to automate the export process, or use APIs to integrate with another system.