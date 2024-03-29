# Tanium Connect
Managed by Tooling Support & Automations.

This repository contains Tanium Connect module configurations and jobs for direct import into Tanium.

When creating a Connect Job or any configuration the job will depend on, you must configure it identically across all Tanium environments, starting with Dev, and follow the rules and environment-specific naming conventions as described below.

## Rules and Guidelines
### Configuration
Connect Jobs and its dependent configurations must have:
* A name that corresponds with the naming conventions described below.
* A description (where a Description field is provided) that clearly describes its purpose and indicate relationships and dependencies.
* A documented schedule, where one is set, with consistent values that demonstrate the thought put into scheduling
  * This is important because every connection must be created multiple times - one for each Tanium environment
  * Even more important where the integration/exports being built require multiple connections per Tanium environment
  * Typically the schedule would be documented in a README in version control next to JSON exports for the job
* Connect Jobs must be exported and recorded in version control. The correct place for them is inside this repo!

### Uploading Connect Jobs to Version Control
* : Create the first Connect Job in Tanium Dev and when ready, export it as JSON. Commit to version control.
    * Using a copy of the exported JSON, modify the name, destination, scheudle and other environment-specific variables to match the next Tanium environment for deployment, and then save that as a separate JSON file. Upload to version control.
    * Commit the modified job to its respective Tanium environment. If you saved an authentication password for the destination into the JSON, ensure you do not commit the password
    * Now you have ensured the job configuration is identical across environments!
## Naming Conventions
Following a consistent naming standard for integrations helps align content across different Tanium modules so it's easy to determine the purpose of a configuration and its relationship to other configurations, regardless of where it is located. The PatchViz integration, for example, depends on Saved Questions, Asset Views, and Connect Jobs, and their configuration is implemented in Tanium, documented in Confluence, and saved to GitHub for version control. Naming must be consistent across all uses of the configuration to improve maintainability and reduce the risk of error.

When creating configuration in Tanium for integration purposes, name them according to these conventions:
### Tanium Environments
Environment Name and Short Name are frequently prepended by the word ```Tanium``` in communications and configurations.
| Environment Name | Short Name | ServiceNow ID | Connect Job CSV Export File Name |
|---|---|---|---|
Development | Dev | tanos-dev | ```<filename>.tanos-dev.csv```
Development (Legacy) | Dev Legacy | dev | ```<filename>.dev.csv```
Production EMEA | Prod EMEA | tanos-emea | ```<filename>.tanos-emea.csv```
Production EMEA (Legacy) | Prod EMEA Legacy | emea | ```<filename>.emea.csv```
Production US | Prod US | tanos-us | ```<filename>.tanos-us.csv```
Production US (Legacy) | Prod US Legacy | us | ```<filename>.us.csv```
Cloud | Cloud | cloud | ```<filename>.cloud.csv```
### Saved Questions, Asset Reports/Views, Patch Lists, etc.
Follow this standard when constructing a module configuration in Tanium that is not a Connect Job; for example, a Saved Question (SQ), Patch List, Asset View, or Asset Report:

    <GroupName> - <Purpose> - <SourceName> - Tanium <EnvShortName> to <Destination> <DestEnv>

* ```<GroupName>``` is the owning group (the consumer at the destination) as represented in AD/ServiceNow.
  * Example: ```ENG-TSA``` for Tooling Support and Automations.
  * Example: ```ENG-IMA``` for the CMDB team.
* ```<Purpose>``` should be a condensed descriptor of the project or purpose.
  * This field can be flexible because not everything is a project, but each thing has a purpose.
  * Example: ```SecViz``` Where the project name is Security Visibility, and its purpose is Security Visibility.
  * Example: ```IPViz``` Where the project name is 100% IP Visibility, and its purpose is Security Visibility.
  * Example: ```Tanium Patch Visibility``` where the initiative is PatchViz, but Tanium Patch data is the specific information being retrieved by a Patch List named according to this convention, so we write that explicitly.
* ```<SourceName>``` List the unique sensor name(s) in the Saved Question or provide a condensed description if there are too many sensors to list. Concatenate with ampersands (&).
  * Example: ```Computer ID & GoDaddy Serial``` lists the two specific sensors used by the SQ.
  * Example: ```Missing Patches & Compliance``` condenses a combination of sensors into a shorter, but accurate description of what information the SQ is retrieving.
* ```<EnvShortName>``` refers to the ```Short Name``` field in the **Tanium Environments** table above.
  * Example: ```Tanium Prod US```
  * Example: ```Tanium Cloud```
* ```<Destination>``` is where the collected data will be sent. When figuring out how to describe this, use the list of destinations in Connect to guide your thinking.
  * Example: ```ServiceNow``` when the information is going to the ServiceNow CMDB.
  * Example: ```Splunk``` when the information is going to Splunk.
* ```<DestEnv>``` refers to the operating environment of the destination. Data exports sometimes need to be configured to deliver to multiple environments, so our SQs and Connect Jobs must differentiate between them.
  * If the destination only has one environment _and_ it is a production environment, you can omit this variable.
  * Example: ```Dev``` where the destination is ServiceNow in the development environment. 
  * Example: **Omit** where the destination is email and emails are production.

### Connect Jobs
Follow this standard when constructing a Connect Job in Tanium:

    <GroupName> - <Purpose> - <SourceName> - Tanium <Module> <EnvShortName> to <Destination> <DestEnv>

The convention is the same as the non-Connect Job configuration standard above, but with the addition of ```<Module>```.
#### Module Variable

```<Module>``` refers to the Tanium Module where that configuration was created.
  * The identifier chosen here can be somewhat flexible because different modules have different configuration or profile types.
  * A rule of thumb in deciding how to word this variable is whether a different configuration type might be part of a different Connect Job where an administrator would want to know the difference between them.
  * Patch Lists are one example:
    * Connect Jobs call the SQs and Asset Views that retrieve data generated by Patch Scans that use the Patch List, but they don't call the Patch List themselves.
    * Since the PatchViz process depends on new Patch Lists, we must now name the Patch Lists according to convention.
    * Given the Connect Jobs call SQs and Asset Views, they never refer to the Patch Lists directly.
    * The decision to call the variable ```Asset``` vs. ```Asset View``` is also open to interpretation:
      * Given Asset Reports are no longer recommended by Tanium for exports, we won't use them - leaving Asset View the only standard config worth retrieving by Connect and allowing us to simplify the variable by just calling it ```Asset```.
    * See **Example 2** for a more detailed explanation of this use case, as its design decisions have a flexible interpretation of this convention.
#### Example Variable Names for Module Configurations in Connect Jobs

| Tanium Module | Configuration | Connect Job Short Name |
|---|---|---|
| Interact | Saved Question | SQ |
| Asset | Asset Report | Asset |
| Asset | Asset View | Asset |
| Patch | Patch List | Patch |
#### Connect Job Destinations

When configuring **Destination** in any Connect Job, the destination name must copy the exact name of the Connect Job it belongs to.

Exceptions to this rule can be made if the destination is shared across jobs - however, this practice introduces risk of error when a destination is inadvertently modified in a way that affects other jobs unintentionally. Use with care.
### Examples
Note that while these examples are based on real-world configuration, this standard was implemented after most existing configurations were created, so the configurations these examples were based on may not reflect the standard exactly. Use the examples below as an expression of the convention above when modifying existing configurations or creating new configurations.
#### Example 1
An end-to-end export to the CMDB as part of the Security Visibility initiative as configured in Tanium Production US.

* Asset View

        ENG-TSA - SecViz - Computer ID & GoDaddy Serial

* Saved Question

        ENG-TSA - SecViz - Computer ID & GoDaddy Serial

* Connect Jobs

        ENG-TSA - SecViz - Computer ID & GoDaddy Serial - Saved Question Prod US to ServiceNow Dev
        ENG-TSA - SecViz - Computer ID & GoDaddy Serial - Saved Question Prod US to ServiceNow Test
        ENG-TSA - SecViz - Computer ID & GoDaddy Serial - Saved Question Prod US to ServiceNow Prod
        ENG-TSA - SecViz - Computer ID & GoDaddy Serial - Asset Prod US to ServiceNow Dev
        ENG-TSA - SecViz - Computer ID & GoDaddy Serial - Asset Prod US to ServiceNow Test
        ENG-TSA - SecViz - Computer ID & GoDaddy Serial - Asset Prod US to ServiceNow Prod

#### Example 2
A more complex configuration, also exporting to the CMDB, supporting Patch Visibility as configured in Tanium Production US.

* Patch Lists
    
        ENG-TSA - Tanium Patch Visibility - CMDB Export - Prod US to ServiceNow Dev - Linux
        ENG-TSA - Tanium Patch Visibility - CMDB Export - Prod US to ServiceNow Dev - Windows
        ENG-TSA - Tanium Patch Visibility - CMDB Export - Prod US to ServiceNow Test - Linux
        ENG-TSA - Tanium Patch Visibility - CMDB Export - Prod US to ServiceNow Test - Windows
        ENG-TSA - Tanium Patch Visibility - CMDB Export - Prod US to ServiceNow Prod - Linux
        ENG-TSA - Tanium Patch Visibility - CMDB Export - Prod US to ServiceNow Prod - Windows      

* Asset Views

        ENG-TSA - PatchViz - Missing Patches & Compliance - Asset Prod US to ServiceNow Dev
        ENG-TSA - PatchViz - Missing Patches & Compliance - Asset Prod US to ServiceNow Test
        ENG-TSA - PatchViz - Missing Patches & Compliance - Asset Prod US to ServiceNow Prod

* Saved Questions

        ENG-TSA - PatchViz - Missing Patches & Compliance - Saved Question Prod US to ServiceNow Dev
        ENG-TSA - PatchViz - Missing Patches & Compliance - Saved Question Prod US to ServiceNow Test
        ENG-TSA - PatchViz - Missing Patches & Compliance - Saved Question Prod US to ServiceNow Prod

* Connect Jobs

        ENG-TSA - PatchViz - Missing Patches & Compliance - Asset Prod US to ServiceNow Dev
        ENG-TSA - PatchViz - Missing Patches & Compliance - Asset Prod US to ServiceNow Test
        ENG-TSA - PatchViz - Missing Patches & Compliance - Asset Prod US to ServiceNow Prod
        ENG-TSA - PatchViz - Missing Patches & Compliance - Saved Question Prod US to ServiceNow Dev
        ENG-TSA - PatchViz - Missing Patches & Compliance - Saved Question Prod US to ServiceNow Test
        ENG-TSA - PatchViz - Missing Patches & Compliance - Saved Question Prod US to ServiceNow Prod

    Note that the Patch Lists are named outside of the standard convention. This is a great example of a situation where it can make sense to bend the rules.
    
    Unlike other configurations, Patch Lists are not called directly by Connect Jobs. The data generated by Patch Scans performed on endpoints using these lists is retrieved via the Asset Views and Saved Questions below that are used by their respective Connect Jobs.
    
    These Patch Lists are named differently (and slightly modified from convention) to better explain their purpose and differentiate between operating systems. Tanium Patch Lists can be used to collect data (like for PatchViz), but Patch Lists are also used to apply patches with Tanium Patch. The Patch Lists in this workflow are not used to apply patches, so we explicitly describe them as ```CMDB Export``` and expand ```PatchViz``` to ```Tanium Patch Visibility``` to avoid confusion with Patchmon data collection, which was also collected and exported by Tanium at the time these jobs were created. In theory, today we could consolidate ```Tanium Patch Visibility``` down to ```PatchViz``` now that Patchmon data collection no longer occurs in Tanium; this would better align their names with the other configuration types again.
    
    We shouldn't, however, title the Patch Lists as ```Missing Patches & Compliance``` because while their configuration supports that activity, they aren't specifically collecting compliance data. Patch Lists help determine which patches are installed and which ones are not on endpoints. This helps other components calculate which missing patches to report as part of PatchViz (severity calculations) as well as patch compliance, but Patch Lists don't perform that work on their own. The Tanium sensors used in Asset Views and Saved Questions are where those calculations are made, not in the Patch Lists themselves.

    All told, any naming convention is subject to interpretation and debate, and there are exceptions to every rule.