---
title: "Migration performance guide for ISVs"
ms.reviewer: 
ms.author: jhendr
author: JoanneHendrickson
manager: pamgreen
audience: ITPro
ms.topic: article
ms.prod: sharepoint-server-itpro
localization_priority: Priority
ms.collection: 
- IT_Sharepoint_Server_Top
- SPMigration
- M365-collaboration
search.appverid: MET150
ms.custom: 
ms.assetid: 
---
# Migration performance guidance for ISVs

The top performance request we receive from both customers and ISVs is how to improve migration speed and avoid throttling. This document provides guidance for ISVs on how to improve migration performance and reliability. It has the latest Microsoft migration performance guidance including authentication, high level coding guidance, how to handle throttling, and more.


## Use app-based authentication
There are different usage patterns between end user traffic and an application doing background activities such as migration. It is important to identify user traffic versus application traffic. 

To provide a stable platform and more reliable service, Microsoft is requesting that ISVs transition from using a user-based authentication to app-based authentication to provide greater reliability to our end users and partners. 

Migration is a background task application and should **not** be run in user mode. By transitioning to app-based authentication, you will benefit from the elastic capability of off-peak time to have more resources.  

> [!Note]
>Microsoft will soon begin enforcing the proper usage roles. Vendors who continue to run migration in user roles can expect to experience increasing throttling and poor performance.

To learn more on how to register an app ID and how to implement app-based authentication see:

- [How to register an app ID](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Factive-directory%2Fdevelop%2Factive-directory-v2-registration-portal&data=04%7C01%7CWan.Wu%40microsoft.com%7C7c98484b20de4fc80fb308d6da3e3509%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636936358039977299%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C-1&sdata=L%2BObRVyCBKPwvvY7MUUsWX%2B8yEIbzqaTkBjcmNjc1vk%3D&reserved=0)
- [Microsoft Graph Auth guidance](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fgraph%2Fauth%2F&data=04%7C01%7CWan.Wu%40microsoft.com%7C7c98484b20de4fc80fb308d6da3e3509%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636936358039977299%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C-1&sdata=ZrFqXsLT3BtT8ynnlLQH9w7JZIOw07zu2X3EYbBmfD4%3D&reserved=0):   Includes an informative video, basics, how to register your app and getting access scenarios
- [Don’t get throttled! SharePoint and OneDrive guide to staying below the limits](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fmyignite.techcommunity.microsoft.com%2Fsessions%2F65661&data=04%7C01%7CWan.Wu%40microsoft.com%7C7c98484b20de4fc80fb308d6da3e3509%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636936358039987303%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C-1&sdata=%2FVCt7P794Lwn0hvpaa4bQicVeqHIPuOM8Vg58nkL16A%3D&reserved=0)

### App-based authentication migration guidance

#### Permission settings
Azure Active Directory (AAD) provides two type of permission : delegated permission and application permissions. For official AAD guidance please see:

- [Permissions and consent in the Azure Active Directory v1.0 endpoint](https://docs.microsoft.com/en-us/azure/active-directory/develop/v1-permissions-and-consent). 

For Sharepoint and Onedrive migration scenarios, the guidance is to follow the AAD permission specification. 

For migration tool that relies on end user signed in and presence, delegated permission is recommended. 

For service-based migration tool that run without a signed-in user present such as app that runs as background service, application permission is recommended.

#### Number of App IDs

Questions have been raised by ISV on whether to have a single App ID covering all migration offering products or having multiple App ID per software offering. There is no hard guidance provided the ISVs can identify all of their App IDs. Please contact Microsoft for any corner case scenarios. 


## Use the Migration API 
For migration jobs, the first guidance is to use existing published migration API, [SharePoint Migration API](https://docs.microsoft.com/en-us/sharepoint/dev/apis/migration-api-overview), such as the *CreateMigrationJob*. There is also a new migration API, [Asynchronous Metadata Read API](https://docs.microsoft.com/en-us/sharepointmigration/asynchronous-metadata-read-api) that will be available August 2019. Once the new API becomes available, we will recommend that you transition to the new API for migration to avoid throttling.

## Switch to the Microsoft Graph API 
If a feature is not supported by the migration API, we recommend that you use the Graph API.  If the Graph API does not support the needed migration feature, then use CSOM. However, using CSOM increases the likelihood of being throttled. 

- [Graph Guidance: Best practices for discovering files and detecting changes at scale](https://docs.microsoft.com/en-us/onedrive/developer/rest-api/concepts/scan-guidance?view=odsp-graph-online)

### CSOM Guidance (fallback only)

The following provides guidance on specific CSOM implementation scenarios to help improve migration performance with Sharepoint and OneDrive.

#### Enumeration Query Ordering guidance 
There are two kinds of enumeration queries, assuming the client intends to read every item with no server-side filtering.

To query for every item in the list, recursively – in other words, the order does not depend on which folder(s) the items are contained in – the query should sort by ID.

    <OrderBy Override="TRUE"><FieldRef name="ID"/></OrderBy>
 
To query for every item in a specific folder, the query should sort by the filename, **FileLeafRef**.

    <OrderBy Override="TRUE"><FieldRef name="FileLeafRef"/></OrderBy>


## For migrations over 100TB 

For customers migrating greater than 100TB of data, please follow the instructions on how to create a support ticket to help the product team to prepare the backend for the customers. 

• [Best practices for improving SharePoint and OneDrive migration performance](https://docs.microsoft.com/en-us/sharepointmigration/sharepoint-online-and-onedrive-migration-speed). 
 
## Escalation and throttling
 
The primary reason speed is impacted, and throttling occurs, is due to the load that gets generated by calling CSOM and REST APIs. As a result of this load, throttling rules fire that impact the speed, reliability and predictability of the migration. Throttling is used to protect the database and to ensure a good user experience for our customers.
 
To read the official throttling guidance, see:

- [Avoid getting throttled or blocked in SharePoint Online](https://myignite.techcommunity.microsoft.com/sessions/65661)

We continue to work to identify issues and improve the API. The asynchronous metadata read API is a direct result of ISV feedback. As an ISV/partner, we value your feedback. Please contact Microsoft if you have further questions. 

