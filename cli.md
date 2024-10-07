# Resources

- [Salesforce CLI](https://developer.salesforce.com/tools/salesforcecli)
- [Salesforce DX Developer Guide](https://developer.salesforce.com/docs/atlas.en-us.sfdx_dev.meta/sfdx_dev/sfdx_dev_develop_any_org.htm)
- [Salesforce DX Developer Guide PDF](https://resources.docs.salesforce.com/252/latest/en-us/sfdc/pdf/sfdx_dev.pdf)
- [Salesforce CLI Command Reference](https://developer.salesforce.com/docs/atlas.en-us.sfdx_cli_reference.meta/sfdx_cli_reference/cli_reference_top.htm)

## Enable Dev Hub

While you can enable Dev Hub in any paid org, it's always best to practice somewhere other than production. Instead, go ahead and [enable Dev Hub](https://developer.salesforce.com/docs/atlas.en-us.sfdx_dev.meta/sfdx_dev/sfdx_setup_enable_devhub.htm) in a Developer Edition org or Trailhead Playground to use with this project.

## Get Your Trailhead Username and Password

You need your Dev Hub login credentials for this project. If you don't already know the username and password for the Developer Edition or Trailhead Playground org where you enabled Dev Hub, follow the steps in [this article](https://trailhead.salesforce.com/help?article=Find-the-username-and-password-for-your-Trailhead-Playground).

## Install the Command Line Interface (CLI)

To extract files from a `tar.gz` archive:

```bash
tar -zxf ~/Downloads/archive.tar.gz
```

Create a directory in home for the unzipped folder to live. For example:

```bash
mkdir ~/CLI
```

Then move the folder the extracted folder here:

```bash
mv ~/Downloads/archive.tar.gz ~/CLI
```

To update your PATH permanently, add the appropriate entry to your shell’s configuration file. For example:

```bash
PATH=~/CLI/sf/bin:$PATH
```

1. Install the CLI from [Salesforce CLI](https://developer.salesforce.com/docs/atlas.en-us.sfdx_setup.meta/sfdx_setup/sfdx_setup_install_cli.htm#sfdx_setup_install_cli_linux).
2. Confirm the CLI is properly installed and on the latest version by running the following command from the command line.  
    `sf update`

## Log In to the Dev Hub

Let's log in to your Dev Hub enabled org.

1. Create an alias **DevHub** by using **-a** and make this the default org using **-d**. To authorize the Dev Hub, in the command window enter the web login flow. The following command opens the Salesforce login page in the web browser:
	
	```java
	sf org login web -d -a DevHub
	```
	
2. Log in using your Dev Hub org credentials. Please note that this is a special org for Salesforce DX. You must use a Dev Hub enabled org for this project.
3. Click **Allow**.
4. After you authenticate in the browser, the CLI remembers your Dev Hub credentials. You see something like this in the Terminal:
	
	```java
	Successfully authorized rraodv@salesforcedx1.com with org id 00D1I000000n3H5UAI
	```
	
Now you can close the Dev Hub org web page and continue to work. Typically, you start your development work by creating a scratch org that's part of this Dev Hub and use the scratch org for your Salesforce DX projects—we do this in a subsequent step.

## project generate

This command generates the necessary configuration files and directories to get you started.

### Examples for project generate

Generate a project with the minimum files and directories:

```bash
sf project generate --name mywork --template empty
```

## project deploy start

You must run this command from within a project.

### Examples for project deploy start

Deploy a specific Apex class; ignore any conflicts between the local project and org (be careful with this flag, because it will overwrite the Apex class in the org if there are conflicts!):

```bash
sf project deploy start --metadata ApexClass:MyApexClass --ignore-conflicts
```

Deploy all the Apex classes and custom objects that are in the "force-app" directory. The list views, layouts, etc, that are associated with the custom objects are also deployed. Both examples are equivalent:

```bash
sf project deploy start --source-dir force-app/main/default/classes force-app/main/default/objects
```

By default, the command creates an empty Apex class. However, you can select different templates, depending on what you’re creating,
by specifying the --template flag.

```bash
--template ApexUnitTest
```
## apex generate trigger

Generates the Apex trigger `*.trigger` file and associated metadata file. These files must be contained in a parent directory called "triggers" in your package directory. Either run this command from an existing directory of this name, or use the `--output-dir` flag to generate one or point to an existing one.

### Examples for apex generate trigger

Generate the Apex trigger; specify the trigger name with the `--name` flag and the `triggers` directory with the `--output-dir` flag.

```bash
sf apex generate trigger --name ObjectTrigger --output-dir force-app/main/default/triggers
```

Use the `project deploy` start command to deploy the new Apex trigger to your org.

```bash
sf project deploy start --metadata ApexTrigger:ObjectTrigger --target-org myscratch
```

# apex run test

Run the specified Apex test classes in your default org and display results in human-readable form:

```bash
sf apex run test --class-names MyClassTest --result-format human
```