---
title: "Managing Update Sets in ServiceNow"
date: 2019-05-03T07:24:55+10:00
draft: false
---
# Managing Update Sets via Standardized Naming Conventions in ServiceNow
ServiceNow uses [Update Sets](https://docs.servicenow.com/bundle/london-application-development/page/build/system-update-sets/concept/system-update-sets.html) in order to better manage the release/migration of changes throughout environments.

In order to better identify the nature of the Change being applied, Developers and Administrators can leverage the usage of the following template when creating/populating an Update Set.

## Update Set Principals

Update Sets should be created with the following core principals in mind:

- Update Sets are Feature/Solution Specific
- Complex Feature = Multiple Update Sets
- Can this Update Set be *easily* reverted?

### Update Sets are Feature/Solution Specific

In order to mitigate the confusion that can easily arise from customization/configuration within ServiceNow, creating specific update sets for features (or sub-features as the case may require) ensures that development works are adequately grouped and categorized, while also minimizing the risk of migrating unintended changes between environments. 

### Complex Feature = Multiple Update Sets

When developing a feature, or implementing a solution, consider the Update Sets that need to created and their relation to the deliverable/s. When creating or performing a change to a complex feature, perhaps it's better to break the Change into Multiple Update Sets; this provides Developers and Administrators with a high-level overview of the complexity of the feature (simply by viewing the number of Update Sets included in the feature), and with the ability to build upon configuration with confidence.

### Can this Update Set be *easily* reverted?

 By using smaller, better defined Update Sets, Developers are inherently building their own rollback solution; especially when considering the usage of Automated Test Frameworks within ServiceNow. Creating Update Sets that can be easily rolled out provides the organization with an easier *Back to Out-Of-Box* path should ServiceNow develop an In-House solution for the feature being deployed.  

## Update Set Naming Convention

Through my personal experiences working with ServiceNow, I've developed the following naming convention for update sets; this ensures that Update Sets are easily identifiable, and provide insight into the nature of the Change, and enables management to quickly identify the scope of the work taking place:

 ([ORGANISATION_INITIALS]) [REQUEST/INC/CHANGE NUMBER] - [SCOPE] [High Level Overview of Feature/Change] ([CURRENT SET NUMBER] of [TOTAL SET NUMBER])

#### Examples

*(ORG) REQ0000001 - [Core Platform] Implementation of Naming Standard (1 of 2)*

*(FRU) STORY0001 - [Project] Modification of Core Out-Of-Box Feature*

This provides technical/administrative staff with the capacity to quickly identify update sets of importance/relevance.

## Update Set Description Template

Update Sets within ServiceNow also contain a Description Field; This field is used to provide specific information regarding the changes applied in this Update Set.

The standard template for Descriptions within Update Sets is as below:

[FEATURE DEPLOYED HIGH LEVEL OVERVIEW]

Changes:
\- Specific Changes
\- That have been applied
\- Within this update set

[JUSTIFICATION OF DEVELOPMENT CHOICES]

#### Example

*Name: (ORG) REQ0172126 - AMS Connect 'New' Task Button Removal*

*Description:*

Adds the below roles to the 'New Button' List Control Group Condition for the Facilities_Request Table:

\- facilities_admin

\- facilities_asset_admin

\- facilities_staff

\- facilities_dispatcher

\- admin

This will allow the aforementioned Roles to use the New button; where any other Roles will not be able to.

Changes:

\- Modified the List Control Condition to include additional roles as specified by the functional team.

\- This change ensures that AMS Connect Users can't bypass the workflow for specific types of AMS Connect Queries by clicking the 'New' Button in the List View and creating their own, non-structured queries.
