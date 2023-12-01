# Automated Workshop Tenant 
Qlik Cloud workshops tend to mostly have the same requirements, more or less.  With that in mind, I have created a series of automations designed to make maintaining a workshop tenant easier.

## Pre-requisites
1) Have a Qlik Cloud Enterprise tenant that will only be used for workshops.
2) Configure an IDP to your tenant - https://help.qlik.com/en-US/cloud-services/Subsystems/Hub/Content/Sense_Hub/Admin/mc-create-idp-configuration.htm
3) Identify the users that will act as administrators to the tenant and provide them with at least one of the Admin roles - https://help.qlik.com/en-US/cloud-services/Subsystems/Hub/Content/Sense_Hub/Admin/SaaS-roles.htm
4) In the management console, create a new OAuth client with the client type "Web" and with "Allow machine-to-machine (M2M)" turned on.  Securely save the client key and secret key.  You will want to enable the following scopes:
  - user_default
  - admin_classic
  - apps (all)
  - automations (all)
  - spaces.managed (all)
  - spaces.shared (all)
  - offline_access

See https://help.qlik.com/en-US/cloud-services/Subsystems/Hub/Content/Sense_Hub/Admin/mc-create-oauth-client.htm for more details on creating and managing OAuth clients.

## Running the "Set Up Tenant" Automation
This automation will configure your spaces, assign your admins special priviledges to those spaces, and install all the automations needed to easily maintain your tenant.  This can also be used to check for updated automations, with the option to update or keep the existing automation.  This is designed to be run manually as needed, either for initial set up or to check for updates.  Take the following steps to install the set up automation:
1) Download the "Set Up Tenant_v1.json"
2) As the user who will be the tenant admin, create a new automation and choose the "Blank" template
3) Name that automation "Set Up Tenant_v1" and click Save
4) Right-click on the automation canvas and select "Upload workspace" then select the "Set Up Tenant" JSON
5) Prior to the first run, click on the 3rd block titled "Get Tenant Name and Region" (this block may appear in red).
6) On the right, configure the connection (will be highlighted in red) using the client key and secret key from your OAuth client.
7) Click "Run" at the top of the page and follow the prompts to choose which automations to install and configure. The following automations are required to be installed:
- Reset Tenant
- Auto Assign
- Remove Data Files

See the "Automations" section for description of each automation and its purpose.

## Spaces
The "Set Up Tenant" automation configures 4 spaces used to run the workshop.  3 of these spaces are required and a 4th is optional and can be deleted if desired.  Please do NOT change the description of any spaces as automations do reference these descriptions to take appropriate actions.  Additional spaces can be added by Admins and will not be deleted by automations.
### Workshop
This managed space is required and is used as a central place for workshop participants to access applications for exploring an app interface or creating their own sheets.  It is NOT designed for data loading or data model building.  It can also be used to provide any reference links you want your participants to potentially access, such as a download link for a workbook or an information page about your orginization.  When the "Reset Tenant" automation is run, links are ignored, but any published apps are deleted (and potentially re-published) to wipe out work done by participants after the workshop is completed to keep the tenant clean.  Workshop participants are given "Can View" and "Can Edit" roles to this space when logging in.  This can be changed in the "Auto Assign" automation under the "Main" case
### Workshop Stage
This shared space is for users identified as Admins only.  This is a staging space for apps in the environment that are either published to the "Workshop" space or distributed to the personal space of users, as well as to auto-remove applications from users personal spaces if they were distributed in error.
#### Auto Publish to a managed space
To have an application in the Workshop Stage space be auto-published to a managed space in the tenant, you must provide the desired application with 2 tags.  To add / remove tags, you must click on the elipses on the app in the hub and click "Rename".  You will need to add the tags "publish" and "space-<spaceName>" (e.g. "space-workshop").  When the tenant is reset, this will re-publish the application to the desired space listed in the tag automatically.
#### Auto / Manual Distribute to personal spaces
Any application in the Workshop Stage space that has a tag "distribute" will be automatically distributed to users personal spaces when they first log into the environment for the workshop IF you choose to install the "Auto-Distribute App" automation.  This is useful for workshops where working with data modeling is desired, and you wish to have a pre-built starting point.  Additionally, if you choose to install the "Manually Distribute App" automation, you can trigger this automation to distribute the application to anyone who is already logged into the environment but does not yet have the application.  More information included on these two automations in the "Automations" section.  The "distribute" tag is removed from the applications when the "Reset Tenant" automation is run and therefore needs to be re-applied before each workshop.
#### Recall applications from personal spaces
Any application in the Workshop Stage space that has already been distributed to personal spaces can be recalled by including the tag "recall".  When applying this tag and manually running the "Recall Applications" automation, any application that matches the name of the app with the "recall" tag will be deleted from all personal spaces and then the "recall" tag will be automatically removed.  This is useful in the case of distributing the wrong application by mistake.
### Workshop Data
This is the space that is designed strictly for data connections and data files.  Workshop users will only have the role of "data consumer" for this space and cannot create any new content here.  The purpose of this space is to have control over the data sources users access during the workshop.
### Publish Apps
This is an optional managed space.  The purpose of this space is to have a landing zone for users to publish apps if the desire is to demonstrate how someone publishes an application.  If there is no step within your workshop where publishing takes place, this space is not required and can be removed after running the "Set Up Tenant" automation.
