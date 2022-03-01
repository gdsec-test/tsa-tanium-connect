# Tanium Connect
Managed by Tooling Support & Automations.

This repository contains Tanium Connect module configurations and jobs for direct import into Tanium.

When creating a Connect Job or any configuration the job will depend on, you must configure it identically across all Tanium environments, starting with Dev, and follow environment-specific naming conventions as described below.
## Naming Conventions
Following a consistent naming standard for integrations helps align content across different Tanium modules so it's easy to determine the purpose of a configuration and its relationship to other configurations, regardless of where it is located. The PatchViz integration, for example, depends on Saved Questions, Asset Views, and Connect Jobs, and their configuration is implemented in Tanium, documented in Confluence, and saved to GitHub for version control. Naming must be consistent across all uses of the configuration to improve maintainability and reduce the risk of error.

When creating configuration in Tanium for integration purposes, name them according to these conventions:
### Tanium Environments
Environment Name and Short Name are frequently prepended by the word ```Tanium``` in communications and configurations.
| Environment Name | Short Name | ServiceNow ID | Connect Job CSV Export File Name |
|---|---|---|---|
Development | Dev | tanos-dev | ```<filename>-tanos-dev.csv```
Development (Legacy) | Dev Legacy | dev | ```<filename>-dev.csv```
Production EMEA | Prod EMEA | tanos-emea | ```<filename>-tanos-emea.csv```
Production EMEA (Legacy) | Prod EMEA Legacy | emea | ```<filename>-emea.csv```
Production US | Prod US | tanos-us | ```<filename>-tanos-us.csv```
Production US (Legacy) | Prod US Legacy | us | ```<filename>-us.csv```
Cloud | Cloud | cloud | ```<filename>-cloud.csv```
### Saved Questions, Asset Reports/Views, Patch Lists, etc.
Follow this standard when constructing a module configuration in Tanium that is _not_ a Connect Job; for example a Saved Question (SQ), Patch List, Asset View, or Asset Report:

    <GroupName> - <Purpose> - <SourceName> - Tanium <EnvShortName> to <Destination> <DestEnv>

* ```GroupName``` is the owning group (the consumer at the destination) as represented in AD/ServiceNow.
  * Example: ```ENG-TSA``` for Tooling Support and Automations.
  * Example: ```ENG-IMA``` for the CMDB team.
* ```Purpose``` should be a condensed descriptor of the project or purpose.
  * This field can be flexible because not everything is a project, but each thing has a purpose.
  * Example: ```SecViz``` Where the project name is Security Visibility and its purpose is Security Visibility.
  * Example: ```IPViz``` Where the project name is 100% IP Visibility and its purpose is Security Visibility.
  * Example: ```Tanium Patch Visibility``` where the initiative is PatchViz _but_ Tanium Patch data is the specific information being retrieved by a Patch List named according to this convention, so we specify explicitly.
* ```SourceName``` List the unique sensor name(s) in the Saved Question, or provide a condensed description if there are too many sensors to list. Concatenate with ampersands (&).
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
  * Example: **Omit** where the destination is email and email is production.

### Connect Jobs
Follow this standard when constructing a Connect Job in Tanium:

    <GroupName> - <Purpose> - <SourceName> - Tanium <Module> <EnvShortName> to <Destination> <DestEnv>

The convention is, more or less, the same as the non-Connect Job configuration standard above, but with the addition of ```<Module>```.

* ```<Module>``` refers to the Tanium Module where that configuration was created.
  * The specific identifier used here can be somewhat flexible, because different modules have different configuration or profile types. A rule of thumb in deciding how to phrase this variable is whether or not a different configuration type might be part of a different Connect Job where an administrator would want to know the difference between them. Patch Lists are one example; Connect Jobs call the SQs and Asset Views that retrieve data generated by Patch Scans that use the Patch List, but they don't call the Patch List themselves. Since the process overall depends on Patch Lists created for that purpose, we must name the lists according to convention, but the Connect Job calls SQs and Asset Views, so the Job only needs to name the SQ and Asset View. Further, the decision to call the variable ```Asset``` vs. ```Asset View``` is also open to interpretation - especially given Asset Reports are no longer recommended by Tanium for exports, so we shouldn't use them - leaving Asset View the only standard config worth retrieving by Connect, and allowing us to simplify the variable by just calling it ```Asset```.

    | Tanium Module | Configuration | Short Name |
    |---|---|---|
    | Interact | Saved Question | SQ |
    | Asset | Asset Report | Asset |
    | Asset | Asset View | Asset |
    | Patch | Patch List | Patch |

The **Destination** name of the Connect Job should always mirror the name of the Connect Job.

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

    Note that the Patch Lists are named outside of the standard convention. This is a great example of a situation where it makes make sense to bend the rules.

    Unlike other configurations, Patch Lists are not called directly by Connect Jobs. The data generated by Patch Scans performed on endpoints using these lists is retrieved via the Asset Views and Saved Questions below that are used by their respective Connect Jobs.
    
    These Patch Lists are named differently (and slightly modified from convention) to better explain their purpose and differentiate between operating systems. Tanium Patch Lists can be used to collect data (like for PatchViz), but Patch Lists are also used to apply patches with Tanium Patch. The Patch Lists in this workflow are not used to apply patches, so we explicitly describe them as ```CMDB Export``` and also expand ```PatchViz``` to ```Tanium Patch Visibility``` to avoid confusion with Patchmon data collection, which was also collected and exported by Tanium at the time these jobs were created. In theory, today we could consodlidate ```Tanium Patch Visibility``` down to ```PatchViz``` now that Patchmon data collection no longer occurs in Tanium; this would  better align their names with the other configuration types again.
    
    We shouldn't, however, title the Patch Lists as ```Missing Patches & Compliance``` because while their configuration supports that activity, they aren't specifically collecting compliance data. Patch Lists help determine which patches are installed and which ones are not on endpoints. This helps other components calculate which missing patches to report as part of PatchViz (severity calculations) as well as patch compliance by but Patch Lists don't perform that work on their own. The Tanium sensors used in Asset Views and Saved Questions are where those calculations are made, not in the Patch Lists themselves.

    All told, any naming convention is subject to interpretation and debate, and there are exceptions to every rule.