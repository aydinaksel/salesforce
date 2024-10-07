# Optimize Customer Data with Standard and Custom Objects

## Overview of Objects

Start with the **data model**. A data model is more or less what it sounds like. It’s a way to model what database tables look like in a way that makes sense to humans. Think about storing data in a spreadsheet. You can use a spreadsheet to track all properties. Columns can store the address, cost, and other important attributes. Rows can store this information for each property that DreamHouse is selling. Database tables are set up in a similar way.

In Salesforce CRM, we think about database tables as **objects**, we think about columns as **fields**, and rows as **records**. So instead of an account spreadsheet or table, we have an Account object with fields and a bunch of identically structured records.

## Customizability

**Customize Responsibly.** While it can seem easy to add and customize objects, remember that what’s going on under the hood is technically complicated. Here are some best practices to keep in mind as you start customizing your own org.

**Be thoughtful about names.** Once you start creating a bunch of objects, it can be tempting to give them “lazy” names. For example, if D’Angelo created another custom object to track condominiums, he might be tempted to name it Property2 instead of Condominium. That’s a recipe for confusion in your org. Give your objects and fields descriptive, unique names to improve clarity.

**Help out your users.** Even with careful naming, your users might not always be clear about the purpose of a particular object or field. Include descriptions for your custom objects and fields. For specialized or complicated customizations, use help text to give more details.

**Require fields when necessary.** Sometimes, you’ll want to force your users to fill out a field when they’re creating a record on a certain object. Every property needs a price, right? Make important fields required to avoid incomplete data.

# Create Object Relationships

Typically, you use lookup relationships when objects are only related in some cases. Sometimes a contact is associated with a specific account, but sometimes it’s just a contact. Objects in lookup relationships usually work as stand-alone objects and have their own tabs in the user interface. Lookup relationships can be one-to-one or one-to-many.

In a master-detail relationship, the detail object doesn’t work as a stand-alone. It’s highly dependent on the master. In fact, if a record on the master object is deleted, all its related detail records are deleted as well. When you’re creating master-detail relationships, you always create the relationship field on the detail object. The master object controls certain behaviors of the detail object, like who can view the detail’s data.

When modeling a many-to-many relationship, you use a junction object to connect the two objects you want to relate to each other. You can use master-detail relationships to model many-to-many relationships between any two objects.

# Work with Schema Builder

Any field you add through Schema Builder isn’t automatically added to the object’s page layout. You must edit the page layout to specify where the field should be displayed. By default, the field-level security for custom fields is set to visible and editable for internal profiles—those not cloned from Partner User or Customer Portal Manager.