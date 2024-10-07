# Write Secure Apex Controllers

## Apex Security and Sharing

By default, Apex classes code executes in system mode and has the ability to read and update all data within an organization. Therefore, you must enforce sharing rules, set object and field permissions, and protect against CRUD and FLS violations.

There are three keywords to remember for sharing rules. You use the `with sharing` or `without sharing` keywords on a class to specify whether sharing rules must be enforced. You use the `inherited sharing` keyword on an Apex class to run the class in the sharing mode of the class that called it.

## Enforcing Object and Field Permissions

User mode operation is the recommended way to avoid sharing, CRUD and FLS violations.

Access Records in User mode ensures the enforcement of sharing rules, CRUD/FLS, and Restriction Rules. By utilizing SOQL queries with the USER_MODE keyword, such as in this example.

```apex
List<Account> acc = [SELECT Id FROM Account WITH USER_MODE];
```

System mode privileges are temporarily dropped, ensuring retrieval of only the records accessible to the user. System mode resumes after the query execution is complete.

```apex
Opportunity o = new Opportunity();
o.Amount=500;
insert as user o;
```

## Using CRUD/FLS Check Methods

You can verify if the current user has the necessary permission and only if they do, can you perform a specific DML operation or query.

Let’s walk through the`DescribeSObjectResult` class helper functions that you can use to verify a user’s level of access. These include:

- `IsCreateable()`
- `IsAccessible()`
- `IsUpdateable()`
- `IsDeleteable()`

### isCreateable()

Suppose the user needs to create an opportunity with $500 in the Amount field. To ensure that the user calling the function has the authorization to create opportunities and opportunity amounts, your Apex code should perform a check to see if the user has the create permission on`isCreateable()` permission on `Opportunity.Amount`. Here’s how you write the code.

```apex
if (!Schema.sObjectType.Opportunity.isCreateable() || !Schema.sObjectType.Opportunity.fields.Amount.isCreateable()){
   ApexPages.addMessage(new ApexPages.Message(ApexPages.Severity.ERROR,
   'Error: Insufficient Access'));
   return null;
}
Opportunity o = new Opportunity();
o.Amount=500;
database.insert(o);
```

### Using stripInaccessible()

You use the`stripInaccessible`method to enforce field- and object-level data protection. This method can be used to strip the fields and relationship fields from query and subquery results that the user can’t access. The method can also be used to remove inaccessible sObject fields before DML operations to avoid exceptions and to sanitize sObjects that have been deserialized from an untrusted source.

# Mitigate SOQL Injection

## Static Query and Bind Variables

The first and most recommended method to prevent SOQL injection is to use static queries with bind variables. Consider the following query.

```java
String query = ‘select id from contact where firstname =\’’+var+’\’’;
queryResult = Database.execute(query);
```

As you’ve learned, using user input (the var variable) directly in a SOQL query opens the application up to SOQL injection. To mitigate the risk, translate the query into a static query like this:

```java
queryResult = [select id from contact where firstname =:var]
```

This step ensures that the user input is treated as a variable, not as an executable element of the query. If a user types a value like `test’ LIMIT 1` when the database performs the query, it looks for any first names that are `“test’ LIMIT 1”` in the database. With a bind variable, the attacker can’t break out and control the SOQL query.

### Typecasting

Another strategy to prevent SOQL injection is to use typecasting. By casting all variables as strings, user input can drift outside of expectation. By typecasting variables as integers or Booleans, when applicable, erroneous user input is not permitted. The variable can then be transformed back to a string for insertion into the query using `string.valueOf()` (remember with dynamic queries, the `database.query()` method accepts only a string).

### Escaping Single Quotes

Another cross-site scripting (XSS) mitigation option that is commonly used by developers who include user-controlled strings in their queries is the platform-provided escape function `string.escapeSingleQuotes()`.

## Replacing Characters

What if you have a scenario in your code where `string.escapeSingleQuotes`, typecasting, and allowlisting are not valid defenses against SOQL injection? A final tool in your tool belt is character replacement, also known as blocklisting. This approach removes “bad characters” from user input

## Allowlisting

Restrict the allowed parameters.

# Mitigate Cross-Site Request Forgery

CSRF is a common web application vulnerability where a malicious application causes a user’s client to perform an unwanted action on a trusted site for which the user is currently authenticated. But what does that actually mean?

For this prevention technique to be successful, four things must happen.
- All sensitive state-changing requests (anything performing database operations) must include a token.  
- A token must be unique to the request or user’s session.  
- A token must be difficult to predict (long with advanced encryption).  
- The server must validate a token to ensure the request originated from the intended user.

## Salesforce Platform Prevention of CSRF

By default, requests made against Salesforce resources have CSRF tokens attached.

Another place for CSRF vulnerabilities is when server-side DML operations are executed automatically as part of a page-loading event such as onInit or afterRender. To mitigate the risk of a page load CSRF, ensure that DML operations are only performed as a result of an interaction with a page (clicking a button, selecting an option, or other actions).

# Mitigate Server Side Request Forgery

Server-side request forgery (SSRF) is a security vulnerability in web applications where an attacker can make unauthorized requests, both internal and external, on behalf of the server. In an SSRF attack, the malicious application tricks the server into making requests to internal and external services or systems, potentially leading to unauthorized access or data exposure.

## Prevention of SSRF

Preventing SSRF attacks involves implementing measures to validate and restrict the scope of requests. Effective prevention techniques include a combination of the following:
- Validate and Sanitize Inputs
- Implement Allowlisting
- Use URL Parsing Libraries
- Network Segmentation

## Salesforce Platform Protections Against SSRF

Salesforce provides built-in protections against SSRF for developers. Requests made against Salesforce resources include safeguards to prevent the exploitation of SSRF vulnerabilities. Additionally, Lightning application developers can use the following best practices to minimize the risk of SSRF:
- Avoid GET Requests
- Validate Origin Headers
- Implement Anti-SSRF Tokens