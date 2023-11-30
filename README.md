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
This automation will configure your spaces, assign your admins special priviledges to those spaces, and install all the automations needed to easily maintain your tenant.  This can also be used to check for updated automations, with the option to update or keep the existing automation.  Take the following steps to install the set up automation:
1) Download the "Set Up Tenant_v1.json"
2) As the user who will be the tenant admin, create a new automation and choose the "Blank" template
3) Name that automation "Set Up Tenant_v1" and click Save
4) Right-click on the automation canvas and select "Upload workspace" then select the "Set Up Tenant" JSON
5) Prior to the first run, click on the 3rd block titled "Get Tenant Name and Region" (this block may appear in red).
6) On the right, configure the connection (will be highlighted in red) using the client key and secret key from your OAuth client.
7) Click "Run" at the top of the page and follow the prompts to choose which automations to install and configure. The following automations are required to be installed:
- Reset Tenant
- Auto Assign

See below for a description of each automation and its purpose.

## Automations 
