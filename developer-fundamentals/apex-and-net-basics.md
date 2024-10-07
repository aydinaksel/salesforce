# Map .NET Concepts to the Lightning Platform

## Platform Basics

Lightning Platform relies on a metadata-driven architecture. Code, configuration, and apps are specified as metadata. Salesforce operates as a multitenant cloud. API's are used.
<br>[[salesforce-architecture.webp]]

## Classes

A class is a template or blueprint from which objects are created. An object is an instance of a class. Check out [Understanding Classes](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_classes_understanding.htm) to learn more about how classes, objects, and interfaces work. Here is what an Apex class named _HelloWorld_ would look like.
```apex
public with sharing class HelloWorld {
    public void printMessage() {
        String msg = 'Hello World';
        System.debug(msg);
    }
}
```

The basic syntax for defining classes is:
```apex
private | public | global
[virtual | abstract] [with sharing | without sharing | inherited sharing]
class ClassName [implements InterfaceNameList] [extends ClassName]
{
    // The body of the class
}
```

## Class Variables

```apex
[public | private | protected | global] [final] [static] data_type variable_name 
[= value]
```

```apex
private static final Integer MY_INT; 
      private final Integer i = 1;
```

## Class Methods

```apex
[public | private | protected | global] [override] [static] data_type method_name 
(input parameters) 
{
// The body of the method
}
```

```apex
public static Integer getInt() { 
     return MY_INT; 
}
```

## Data Types

Apex supports the kind of data types you would expect. There are primitive types, such as Integer, Double, Long, Date, Datetime, String, and Boolean. There is also an ID data type that is used for any valid 18-character Lightning Platform record identifier assigned by the system.

All variables are initialized to null by default. Strings are always treated as a primitive value type.

An sObject is just a Salesforce object, such as an Account or Contact. You can think of it as a table in a database.

Additionally, a data type can be a typed list of values, also known as an enum. You can use enums with numbers, but you can’t define what these number values are. Also, the ordinal assignment starts at zero.

### Collections

Apex has only the following three collections.

#### List

A list is an ordered collection of elements that works much the same as a traditional array. However, Apex does not support arrays as collections, just lists.
```apex
List<Account> myAccounts = [SELECT Id, Name FROM Account];
String firstAccount = myAccounts.get(0).Name;
String firstAccount = myAccounts[0].Name;		// Uses Array Notation []
```

#### Set

A set is an unordered collection of elements that does not contain duplicates.
```apex
Set<Id> accountIds = new Set<Id>{'001d000000BOaHSAA1','001d000000BOaHTAA1'};
List<Account> accounts = [SELECT Name FROM Account WHERE Id IN :accountIds];
```

#### Map

A map is a collection of key-value pairs. Each key maps to a single value. A map is useful when you need to quickly find something by a key.
```apex
Map<Id, Account> accountMap = new Map<Id, Account>(
    [SELECT Id, Name FROM Account]
);

Id accId = '001d000000BOaHSAA1';
Account acc = accountMap.get(accId);

```

## Apex and Database

Apex code and the Lightning Platform database are tightly coupled to the point where they are sometimes indistinguishable. Each standard or custom object in the database has a "mystical" representation via an Apex class that provides all sorts of functionality to make interacting with the database a snap. The class and its underlying object are essentially a mirror image of one another that is constantly in sync.
<br>
<br>
<br>
# Understanding Execution Context

## Execution Context

In the Lightning Platform world, code executes within an execution context. In a nutshell, this context represents the time between when the code is executed and when it ends. The important thing for you to understand is that the Apex code you write is not always the only code that is executing.

Apex code has access to all objects and fields. Object permissions, field-level security, and sharing rules aren’t applied for the current user. You can use the with sharing keyword to specify that the sharing rules for the current user be taken into account for a class.

Notice that the first line in the execution log marks the EXECUTION_STARTED event and that the last line is the EXECUTION_FINISHED event. Everything in between is the execution context.

## Invoking Apex

|Method|Description|
|---|---|
|Database Trigger|Invoked for a specific event on a custom or standard object.|
|Anonymous Apex|Code snippets executed on the fly in Dev Console & other tools.|
|Asynchronous Apex|Occurs when executing a future or queueable Apex, running a batch job, or scheduling Apex to run at a specified interval.|
|Web Services|Code that is exposed via SOAP or REST inbound or outbound web services.|
|Email Services|Code that is set up to process inbound or outbound email.|
|Visualforce or Lightning Pages|Visualforce controllers and Lightning components can execute Apex code automatically or when a user initiates an action, such as clicking a button. Lightning components can also be executed by Lightning processes and flows.|

The following is an Anonymous Apex snippet that calls an Apex method:
```apex
EmailManager.sendMail('Your email address', 'Trailhead Tutorial', '123 body');
```


## Trigger

Similar to triggers in SQL Server, Apex database triggers execute programming logic before or after events to records in Salesforce. **You only want to resort to using a trigger when you are absolutely sure that the same thing cannot be accomplished with one of our point-and-click automation tools.**

The basic syntax for a trigger looks like the following:
```apex
trigger TriggerName on ObjectName (trigger_events) {
	// code_block
}
```


## Limits

The two limits you will probably be the most concerned with involve the number of SOQL queries or DML statements. Currently, the synchronous limit for the total number of SOQL queries is 100, and 150 for the total number of DML statements issued.

## Bulkify

Design your Apex code to handle bulk operations from the very start. Learn about ways to do this from the [Bulk Apex Triggers](https://developer.salesforce.com/trailhead/apex_triggers/apex_triggers_bulk) module. Make sure that the trigger can handle a load of 200 records since this is the limit for an Apex trigger.

If an insert DML operation is inside the for loop, it is bad, very bad, and something to always avoid.
<br>
<br>
<br>
# Use Asynchronous Apex

## When to Go Asynchronous

The following three reasons are usually behind choosing asynchronous programming:
- **Processing a very large number of records**. This reason is unique to the multi-tenanted world of the Lightning Platform where limits rule. The limits associated with asynchronous processes are higher than those with synchronous processes. Therefore, if you need to process thousands or even millions of records, asynchronous processing is your best bet.  
- **Making callouts to external web services**. Callouts can take a long time to process, but in the Lightning Platform, triggers can’t make callouts directly.  
- **Creating a better and faster user experience** by offloading some processing to asynchronous calls. Why do everything at once? If it can wait, let it.

## Future Methods

Changing a method from synchronous to asynchronous processing is amazingly easy. Essentially, you just add the `@future` annotation to your method. Also, make sure that the method is static and returns only a void type.

## Future Limitations

Here are some limitations to consider before using a future method.

- You can’t track execution because no Apex job ID is returned.  
    
- Parameters must be primitive data types, or collections of primitive data types. Future methods can’t take sObjects as arguments as they might change in the time before the @future method executes.  
    
- You can’t chain future methods and have one call another. Use Queueable apex if you need execution in a certain order.

## Batch or Scheduled Apex

# Debug and Run Diagnostics

## Your Friend, the Debug Log

```apex
System.debug('My Debug Message');
```

You can also specify one of the following logging levels.

- NONE  
- ERROR  
- WARN  
- INFO  
- DEBUG  
- FINE  
- FINER  
- FINEST

Each debug log must be 20 MB or smaller. If it exceeds this amount, you won’t see everything you need. Additionally, each org can retain up to 1,000 MB of debug logs. The oldest logs are overwritten.

Because debug logs are your primary way of getting debug information about your application, you want to make sure to not exceed these limits. If you never see an error message, you can’t possibly do anything to address it. Check out the links in Resources about Advanced Apex Debugging and Best Practices for tips on how to avoid these kinds of issues.

1. Select **Debug > Change Log Levels**.  
2. Click the **Add/Change** link in General Trace Setting for You.  

## Use the Log Inspector

1. Select **Debug > Switch Perspective > All (Predefined)**. This gives access to unique components like *Timeline* and *Executed Units* tabs.

## Set Checkpoints

Checkpoints are similar to breakpoints in that they reveal a lot of detailed execution information about a line of code. They just don’t stop execution on that line. To insert a checkpoint, position your cursor over line in the left margin and click once. A red dot appears next to the line number.