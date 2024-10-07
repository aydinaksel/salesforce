# Choose the Right Search Solution

## Search, the Salesforce Way

From the user’s perspective, the search process is similar to when a record is created. When users enter a term in the search field (1), the search engine breaks up the search term into tokens (2). It matches those tokens to the record information stored in the search index (3), ranks the associated records by relevance (4), and returns the results that users have access to (5).

In general, you need a custom search solution when your org uses a custom UI instead of the standard Salesforce UI. Examples of home grown UIs are a customer-facing knowledge base or an internal product data site for your employees. Just a word of caution: Building a custom user interface isn’t for everyone and requires extra work. The good news is that a custom search still allows you to take advantage of some of the advanced features of Salesforce search. So, if your company has decided to build a custom UI and needs a custom search, this is the module for you.

Here are some guidelines on when to use SOQL or SOSL.

## SOQL

Use SOQL when you know in which objects or fields the data resides and you want to:

- Retrieve data from a single object or from multiple objects that are related to one another.
- Count the number of records that meet specified criteria.
- Sort results as part of the query.
- Retrieve data from number, date, or checkbox fields.
- Works best when you know which field the data resides in

## SOSL

Use SOSL when you don’t know in which object or field the data resides and you want to:

- Retrieve data for a specific term that you know exists within a field. Because SOSL can tokenize multiple terms within a field and build a search index from this, SOSL searches are faster and can return more relevant results.
- Retrieve multiple objects and fields efficiently, and the objects might or might not be related to one another.
- Retrieve data for a particular division in an organization using the divisions feature, and you want to find it in the most efficient way possible.


# Build Search for Common Use Cases

## Search Within a Single Object

To search within a single object using SOSL, simply specify that object in the request. It’s just that easy.

```javascript
FIND {term} RETURNING ObjectTypeName
```

In the example, `term` is what the user enters. ObjectTypeName limits search results to include only the sObject specified. So if the user wants to find the March 2016 email campaign, the request looks like:

```javascript
FIND {march 2016 email} RETURNING Campaign
```

## Search Within Multiple Objects

```javascript
FIND {recycled materials} RETURNING Product2, ContentVersion, FeedItem
```

# Optimize Search Results

## Create Efficient Text Searches

Search queries can be expensive. The more data you’re searching through and the more results you’re returning, the more you can slow down the entire operation.

How do you combat sluggish searches? With two basic strategies.

- Limit which data you’re searching through
- Limit which data you’re returning

But you don’t want to go too far. If you’re too efficient, the search is less useful. Users don’t like it when they see **record not found** when they know that the record exists.

To limit which data is searched, use IN SearchGroup. You can search for name, email, phone, sidebar, or all fields. For example, if you want to search only for an email, you search through only email fields.

```javascript
FIND {jsmith@cloudkicks.com} IN EMAIL FIELDS RETURNING Contact
```

Again, turning to SOSL, you can use `RETURNING FieldSpec` to specify which data is returned. We used it in the last unit, but let’s talk about the more advanced elements it includes.

- `ObjectTypeName`—Specifies the object to return.
- `FieldList`—Specifies the fields to return.
- `ORDER By`—Specifies which fields to order the results by. You can also specify ascending or descending order.
- `LIMIT n`—Sets the maximum number of records returned for the given object.
- `OFFSET n`—Sets the starting row offset into the result set returned by your query.

```javascript
FIND {Cloud Kicks} RETURNING Account (Name, Industry ORDER BY Name LIMIT 10 OFFSET 25)
```

Now that you’re getting the hang of this, let’s try out some WITH statements, which filter records by certain predefined fields. By prefiltering results, you return fewer results and improve performance.

```javascript
FIND {Cloud Kicks} RETURNING Account (Name, Industry)
	WITH DIVISION = 'Global'
```

## Synonyms
The first step is setting up and optimizing synonym groups, which have words or phrases that are treated equally in searches. A search for one term in a synonym group returns results for all terms in the group. For example, a search for USB returns results for all terms in the synonym group, which contains _USB_, _thumb drive_, _flash stick_, and _memory stick_. You’re probably seeing the value of synonym groups already. Users can search using one term and still get the results they want even if they don’t use the “right” search term. No renaming of products required.