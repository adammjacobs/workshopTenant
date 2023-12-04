# Automated Workshop Tenant 
Qlik Cloud workshops tend to mostly have the same requirements.  With that in mind, I have created a series of automations designed to make maintaining a workshop tenant easier.

## Pre-requisites
1) Have a Qlik Cloud Enterprise tenant that will only be used for workshops.
2) Configure an IDP to your tenant - https://help.qlik.com/en-US/cloud-services/Subsystems/Hub/Content/Sense_Hub/Admin/mc-create-idp-configuration.htm
3) Identify the users that will act as administrators to the tenant and provide them with at least one of the Admin roles - https://help.qlik.com/en-US/cloud-services/Subsystems/Hub/Content/Sense_Hub/Admin/SaaS-roles.htm
4) In the management console, using the left hand options menu, create a new OAuth client with the client type "Web" and with "Allow machine-to-machine (M2M)" turned on.  Securely save the client key and secret key.  You will want to enable the following scopes:
  - user_default
  - admin_classic
  - apps (all)
  - automations (all)
  - spaces.managed (all)
  - spaces.shared (all)
  - offline_access

See https://help.qlik.com/en-US/cloud-services/Subsystems/Hub/Content/Sense_Hub/Admin/mc-create-oauth-client.htm for more details on creating and managing OAuth clients.

## Running the "Set Up Tenant" Automation
This automation will configure your spaces, assign your admins special privileges to those spaces, and install all the automations needed to easily maintain your tenant.  This can also be used to check for updated automations, with the option to update or keep the existing automation.  This is designed to be run manually as needed, either for initial set up or to check for updates.  Take the following steps to install the set up automation:
1) Download the "Set Up Tenant_v2.json"
2) As the user who will be the tenant admin, create a new automation and choose the "Blank" template
3) Name that automation "Set Up Tenant_v2" and click Save
4) Right-click on the automation canvas and select "Upload workspace" then select the "Set Up Tenant" JSON
5) Prior to the first run, click on the 3rd block titled "Get Tenant Name and Region" (this block may appear in red).
6) On the right, configure the connection (will be highlighted in red) using the client key and secret key from your OAuth client.  When complete, the block connection will look like this: \
   ![alt text](https://github.com/adammjacobs/workshopTenant/blob/main/configureblock.png?raw=true) 
7) Click "Run" at the top of the page and follow the prompts to choose which automations to install and configure. The following automations are required to be installed:
- Reset Tenant
- Auto Assign Roles and Distribute App
- Remove Data Files

![alt text](https://github.com/adammjacobs/workshopTenant/blob/main/install-update-automations.png?raw=true)

See the "Automations" section for description of each automation and its purpose.  

NOTE: If the automation appears to hang for longer than 30 seconds without completing, check the chronological block execution and if new blocks are not appearing at the end of the listed run, refresh the browser page.  Options that need to be selected might have been hidden by the browser and should appear after a refresh.

## Spaces 📄
The "Set Up Tenant" automation configures 4 spaces used to run the workshop - "Workshop", "Workshop Stage", "Workshop Data", and "Publish Apps".  Of these four spaces, three are required and only "Publish Apps" is optional and can be deleted if desired.  Please do NOT change the description of any spaces as automations do reference these descriptions to take appropriate actions.  Additional spaces can be added by Admins and will not be deleted by automations.
### Workshop 📄
This managed space is required and is used as a central place for workshop participants to access applications for exploring an app interface or creating their own sheets.  
- It is NOT designed for data loading or data model building.
- It can be used to provide any reference links you want your participants to potentially access, such as a download link for a workbook or an information page about your organization.
- When the "Reset Tenant" automation is run, links are ignored, but any published apps are deleted (and potentially re-published) to wipe out work done by participants after the workshop is completed to keep the tenant clean.
- Workshop participants are given "Can View" and "Can Edit" roles to this space when logging in.
- This can be changed in the "Auto Assign" automation under the "Main" case.
### Workshop Data 📄
This is the space that is designed strictly for data connections and data files.  Workshop users will only have the role of "Can Consume Data" for this space and cannot create any new content here.  The purpose of this space is to have control over the data sources users access during the workshop.
### Publish Apps 📄
This is an optional managed space.  The purpose of this space is to have a landing zone for users to publish apps if the desire is to demonstrate how someone publishes an application.  If there is no step within your workshop where publishing takes place, this space is not required and can be removed after running the "Set Up Tenant" automation.
### Workshop Stage 📄
This shared space is only accessible by users with Admin privileges in the tenant.  This is a staging space for apps in the environment that are either published to the "Workshop" space or distributed to the personal space of users, as well as to auto-remove applications from users personal spaces if they were distributed in error.  Nothing in this space is removed when the "Reset Tenant" automation runs.  Automations look for application tags (🏷️) in this space to take the following actions (all are optional):
#### Auto Publish to a managed space 🏷️
Auto publishing an app to a managed space is optional but can be useful if you reuse the same application for most or all of your workshops.  
- To have an application in the Workshop Stage space be auto-published to a managed space in the tenant, you must provide the desired application with 2 tags.
- To add / remove tags, you must click on the ellipses on the app in the hub and click "Rename".
- You will need to add the tags "publish" and "space-<spaceName>" (e.g. "space-workshop").
- When the tenant is reset, this will re-publish the application to the desired space listed in the tag automatically. \
![alt text](https://github.com/adammjacobs/workshopTenant/blob/main/tags.png?raw=true)
#### Auto / Manual Distribute to personal spaces 🏷️
Any application in the Workshop Stage space that has a tag "distribute" will be automatically distributed to users personal spaces when they first log into the environment for the workshop.  This is useful for workshops where working with data modeling is desired, and you wish to have a pre-built starting point.  

Additionally, if you choose to install the "Manually Distribute App" automation, you can trigger this automation to distribute the application to anyone who is already logged into the environment but does not yet have the application.  More information about this automation is in the "Automations" section.  
#### Recall applications from personal spaces 🏷️
Any application in the Workshop Stage space that has already been distributed to personal spaces can be recalled by including the tag "recall".  When applying this tag and manually running the "Recall Applications" automation, any application that matches the name of the app with the "recall" tag will be deleted from all personal spaces and then the "recall" tag will be automatically removed.  This is useful in the case of distributing the wrong application by mistake.
## Automations ⛓️
The automations installed are instrumental in reducing the workload to maintain a workshop tenant and saves admins a lot of time and energy.  Here is a breakdown of those automations.
### Set Up Tenant ⛓️
This automation is designed to be run manually and not on a schedule because user inputs are required that can only be accomplished via a manual run.  This automation will configure your spaces, assign your admins special privileges to those spaces, and install all the automations needed to easily maintain your tenant.  This can also be used to check for updated automations, with the option to update or keep the existing automation. \
![alt text](https://github.com/adammjacobs/workshopTenant/blob/main/setuptenant_updateavailable.png?raw=true) \
NOTE: The user running this automation must also be the owner of all other automations below in order to avoid duplicate automations running in your environment.
### Reset Tenant ⛓️
This is a required automation.  This resets the tenant on a schedule (default is daily at midnight America/New York, but this can be changed by selecting the "Start" block and adjusting the schedule).

By default, this will remove the alias hostname (more on why under "Preparing for a new workshop"), but you can right-click and "enable" the third block in the automation to have a hostname of 5 random letters and 3 random numbers set instead.  Do this if you would rather automate the process of setting the hostname than setting one yourself. \
![alt text](https://github.com/adammjacobs/workshopTenant/blob/main/hostname.png?raw=true)

The reset includes deleting the workshop users who do not have admin roles, and deleting their created content.
### Remove Data Files ⛓️
This is a required automation to keep non-admins from storing their data in the workshop environment.  If there is a requirement during the workshop for users to load their own data sets into the environment, this can be disabled, but it is recommended to have all users access all necessary files via the "Workshop Data" space.  

This automation is configured to run hourly by default, but can be changed by selecting the "Start" block and adjusting the schedule.
### Auto Assign Roles and Distribute App ⛓️
This is a required automation to automatically assign users to appropriate spaces when they first log into the environment for the workshop, as well as distribute any apps appropriately marked for distribution (See "Auto / Manual Distribute to personal spaces" above).  

Users are given "Can View" and "Can Edit" roles to the "Workshop" space, "Can Consume Data" to the "Workshop Data" space, and "Can View", "Can Edit" and "Can Publish" roles to the "Publish Apps" space.  
### Manually Distribute App ⛓️
This is an optional automation that is recommended if you will be distributing applications to the personal spaces of workshop participants.  It is recommended because there is always a chance of forgetting to add the "distribute" tag to an app prior to users logging in, and this allows you to distribute apps to existing users after the tag has been added. 

When this automation is installed, a link to execute the automation quickly is also installed in the "Workshop Stage" space.  This is important as this means non-owners of the automation can execute the automation, but keeping it in the "Workshop Stage" space ensures it is still only Admins that can execute the automation.
### Recall Applications ⛓️
This is an optional automation that is recommended if you will be distributing applications to the personal spaces of workshop participants.  It is recommended because there is always a chance of accidentally distributing the wrong application to personal spaces.  This can avoid confusion and declutter the personal spaces of your participants.  

When this automation is installed, a link to execute the automation quickly is also installed in the "Workshop Stage" space.  This is important as this means non-owners of the automation can execute the automation, but keeping it in the "Workshop Stage" space ensures it is still only Admins that can execute the automation.
## Preparing for a new workshop
Use the following as a base checklist to prep for a new workshop.
1) Set a new alias hostname (if you choose not to have that set automatically for you in the automation "Reset Tenant") by going to "Settings" on the left-hand options menu in the management console.  Using distinct hostnames for each new workshop allows you to re-use user names from your IDP without allowing previous users to log in again.  It is NOT recommended to send the base hostname to workshop users.
2) Check that all necessary applications are published in "Workshop"
3) Check that any applications from "Workshop Stage" that should be distributed to users personal spaces have the tag "distribute"
4) Check that all data connections needed for the workshop can fetch the needed data
