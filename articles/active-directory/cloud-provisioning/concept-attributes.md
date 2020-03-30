---
title: Het Azure AD-schema en aangepaste expressies begrijpen
description: In dit artikel worden het Azure AD-schema, de kenmerken die de inrichtingsagent stroomt en aangepaste expressies beschreven.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/18/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ac09fb3faf55be6c07a1e0a88b6e2032c9ab8ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299326"
---
# <a name="understand-the-azure-ad-schema"></a>Het Azure AD-schema begrijpen
Een object in Azure Active Directory (Azure AD), zoals elke map, is een programmatische gegevensconstructie op hoog niveau die zaken als gebruikers, groepen en contactpersonen vertegenwoordigt. Wanneer u een nieuwe gebruiker of contactpersoon maakt in Azure AD, maakt u een nieuw exemplaar van dat object. Deze exemplaren kunnen worden gedifferentieerd op basis van hun eigenschappen.

Eigenschappen in Azure AD zijn de elementen die verantwoordelijk zijn voor het opslaan van informatie over een instantie van een object in Azure AD.

Het Azure AD-schema definieert de regels waarvoor eigenschappen in een item kunnen worden gebruikt, welke waarden deze eigenschappen kunnen hebben en hoe gebruikers met die waarden kunnen communiceren. 

Azure AD heeft twee typen eigenschappen:
- **Ingebouwde eigenschappen:** eigenschappen die vooraf zijn gedefinieerd door het Azure AD-schema. Deze eigenschappen bieden verschillende toepassingen en zijn mogelijk wel of niet toegankelijk.
- **Directory-extensies:** eigenschappen die worden geleverd, zodat u Azure AD voor eigen gebruik aanpassen. Als u bijvoorbeeld uw on-premises Active Directory hebt uitgebreid met een bepaald kenmerk en dat kenmerk wilt doorstromen, u een van de aangepaste eigenschappen gebruiken die zijn opgegeven. 

## <a name="attributes-and-expressions"></a>Kenmerken en expressies
Wanneer een object zoals een gebruiker is ingericht in Azure AD, wordt een nieuw exemplaar van het gebruikersobject gemaakt. Deze creatie omvat de eigenschappen van dat object, die ook bekend staan als kenmerken. In eerste instantie heeft het nieuw gemaakte object zijn kenmerken ingesteld op waarden die worden bepaald door de synchronisatieregels. Deze kenmerken worden vervolgens up-to-date gehouden via de cloud provisioning agent.

![Objectinrichting](media/concept-attributes/attribute1.png)

Een gebruiker kan bijvoorbeeld deel uitmaken van een marketingafdeling. Het kenmerk azure AD-afdeling wordt in eerste instantie gemaakt wanneer ze zijn ingericht en de waarde is ingesteld op Marketing. Zes maanden later, als ze overstappen op Verkoop, wordt het kenmerk van de on-premises Active Directory-afdeling gewijzigd in Verkoop. Deze wijziging synchroniseert met Azure AD en wordt weergegeven in het Azure AD-gebruikersobject.

Kenmerksynchronisatie kan direct zijn, waarbij de waarde in Azure AD rechtstreeks is ingesteld op de waarde van het on-premises kenmerk. Een programmatische expressie kan de synchronisatie ook verwerken. Een programmatische expressie is nodig in gevallen waarin enige logica of een bepaling moet worden gemaakt om de waarde te vullen.

Als u bijvoorbeeld het e-mailattribuut "john.smith@contoso.com" had@contoso.comen het gedeelte " " moest uitkleden en alleen de waarde "john.smith" moest doorstromen, zou u zoiets als dit gebruiken:

`Replace([mail], "@contoso.com", , ,"", ,)`  

**Voorbeeldinvoer/-uitvoer:** <br>

* **INPUT** (e-mail): "john.smith@contoso.com
* **OUTPUT**: "John.smith"

Zie [Expressies schrijven voor kenmerktoewijzingen in Azure Active Directory voor](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data)meer informatie over het schrijven van aangepaste expressies en de syntaxis.

In de volgende tabel worden veelvoorkomende kenmerken weergegeven en hoe ze zijn gesynchroniseerd met Azure AD.


|On-premises Active Directory|Toewijzingstype|Azure AD|
|-----|-----|-----|
|Cn|Direct|commonName
|landCode|Direct|landCode|
|displayName|Direct|displayName|
|givenName|Expressie|givenName|
|objectGUID|Direct|sourceAnchorBinary|  
|userprincipalName|Direct|userPrincipalName|
|ProxyAdress ProxyAdress|Direct|ProxyAdres|

## <a name="view-the-schema"></a>Het schema weergeven
> [!WARNING]
> De configuratie voor het inrichten van de cloud maakt een serviceprincipal. De serviceprincipal is zichtbaar in de Azure-portal. U moet de kenmerktoewijzingen niet wijzigen met behulp van de hoofdervaring van de service in de Azure-portal.  Dit wordt niet ondersteund.

Voer de volgende stappen uit om het schema te bekijken en te verifiëren.

1.  Ga naar [Grafiekverkenner](https://developer.microsoft.com/graph/graph-explorer).
1.  Meld u aan met uw wereldwijde beheerdersaccount.
1.  Selecteer aan de linkerkant **machtigingen wijzigen** en zorg ervoor dat **Directory.ReadWrite.All** is *goedgekeurd*.
1.  Voer de `https://graph.microsoft.com/beta/serviceprincipals/?$filter=startswith(Displayname,'Active')`query uit . Met deze query wordt een gefilterde lijst met serviceprincipals geretourneerd.
1.  Zoek `"appDisplayName": "Active Directory to Azure Active Directory Provisioning"` en noteer `"id"`de waarde voor .
    ```
    "value": [
            {
                "id": "00d41b14-7958-45ad-9d75-d52fa29e02a1",
                "deletedDateTime": null,
                "accountEnabled": true,
                "appDisplayName": "Active Directory to Azure Active Directory Provisioning",
                "appId": "1a4721b3-e57f-4451-ae87-ef078703ec94",
                "applicationTemplateId": null,
                "appOwnerOrganizationId": "47df5bb7-e6bc-4256-afb0-dd8c8e3c1ce8",
                "appRoleAssignmentRequired": false,
                "displayName": "Active Directory to Azure Active Directory Provisioning",
                "errorUrl": null,
                "homepage": "https://account.activedirectory.windowsazure.com:444/applications/default.aspx?metadata=AD2AADProvisioning|ISV9.1|primary|z",
                "loginUrl": null,
                "logoutUrl": null,
                "notificationEmailAddresses": [],
                "preferredSingleSignOnMode": null,
                "preferredTokenSigningKeyEndDateTime": null,
                "preferredTokenSigningKeyThumbprint": null,
                "publisherName": "Active Directory Application Registry",
                "replyUrls": [],
                "samlMetadataUrl": null,
                "samlSingleSignOnSettings": null,
                "servicePrincipalNames": [
                    "http://adapplicationregistry.onmicrosoft.com/adprovisioningtoaad/primary",
                    "1a4721b3-e57f-4451-ae87-ef078703ec94"
                ],
                "signInAudience": "AzureADMultipleOrgs",
                "tags": [
                    "WindowsAzureActiveDirectoryIntegratedApp"
                ],
                "addIns": [],
                "api": {
                    "resourceSpecificApplicationPermissions": []
                },
                "appRoles": [
                    {
                        "allowedMemberTypes": [
                            "User"
                        ],
                        "description": "msiam_access",
                        "displayName": "msiam_access",
                        "id": "a0326856-1f51-4311-8ae7-a034d168eedf",
                        "isEnabled": true,
                        "origin": "Application",
                        "value": null
                    }
                ],
                "info": {
                    "termsOfServiceUrl": null,
                    "supportUrl": null,
                    "privacyStatementUrl": null,
                    "marketingUrl": null,
                    "logoUrl": null
                },
                "keyCredentials": [],
                "publishedPermissionScopes": [
                    {
                        "adminConsentDescription": "Allow the application to access Active Directory to Azure Active Directory Provisioning on behalf of the signed-in user.",
                        "adminConsentDisplayName": "Access Active Directory to Azure Active Directory Provisioning",
                        "id": "d40ed463-646c-4efe-bb3e-3fa7d0006688",
                        "isEnabled": true,
                        "type": "User",
                        "userConsentDescription": "Allow the application to access Active Directory to Azure Active Directory Provisioning on your behalf.",
                        "userConsentDisplayName": "Access Active Directory to Azure Active Directory Provisioning",
                        "value": "user_impersonation"
                    }
                ],
                "passwordCredentials": []
            },
    ```
1. Vervang `{Service Principal id}` de waarde en voer `https://graph.microsoft.com/beta/serviceprincipals/{Service Principal id}/synchronization/jobs/`de query uit .
1. Zoek `"id": "AD2AADProvisioning.fd1c9b9e8077402c8bc03a7186c8f976"` en noteer `"id"`de waarde voor .
    ```
    {
                "id": "AD2AADProvisioning.fd1c9b9e8077402c8bc03a7186c8f976",
                "templateId": "AD2AADProvisioning",
                "schedule": {
                    "expiration": null,
                    "interval": "PT2M",
                    "state": "Active"
                },
                "status": {
                    "countSuccessiveCompleteFailures": 0,
                    "escrowsPruned": false,
                    "code": "Active",
                    "lastSuccessfulExecutionWithExports": null,
                    "quarantine": null,
                    "steadyStateFirstAchievedTime": "2019-11-08T15:48:05.7360238Z",
                    "steadyStateLastAchievedTime": "2019-11-20T16:17:24.7957721Z",
                    "troubleshootingUrl": "",
                    "lastExecution": {
                        "activityIdentifier": "2dea06a7-2960-420d-931e-f6c807ebda24",
                        "countEntitled": 0,
                        "countEntitledForProvisioning": 0,
                        "countEscrowed": 15,
                        "countEscrowedRaw": 15,
                        "countExported": 0,
                        "countExports": 0,
                        "countImported": 0,
                        "countImportedDeltas": 0,
                        "countImportedReferenceDeltas": 0,
                        "state": "Succeeded",
                        "error": null,
                        "timeBegan": "2019-11-20T16:15:21.116098Z",
                        "timeEnded": "2019-11-20T16:17:24.7488681Z"
                    },
                    "lastSuccessfulExecution": {
                        "activityIdentifier": null,
                        "countEntitled": 0,
                        "countEntitledForProvisioning": 0,
                        "countEscrowed": 0,
                        "countEscrowedRaw": 0,
                        "countExported": 5,
                        "countExports": 0,
                        "countImported": 0,
                        "countImportedDeltas": 0,
                        "countImportedReferenceDeltas": 0,
                        "state": "Succeeded",
                        "error": null,
                        "timeBegan": "0001-01-01T00:00:00Z",
                        "timeEnded": "2019-11-20T14:09:46.8855027Z"
                    },
                    "progress": [],
                    "synchronizedEntryCountByType": [
                        {
                            "key": "group to Group",
                            "value": 33
                        },
                        {
                            "key": "user to User",
                            "value": 3
                        }
                    ]
                },
                "synchronizationJobSettings": [
                    {
                        "name": "Domain",
                        "value": "{\"DomainFQDN\":\"contoso.com\",\"DomainNetBios\":\"CONTOSO\",\"ForestFQDN\":\"contoso.com\",\"ForestNetBios\":\"CONTOSO\"}"
                    },
                    {
                        "name": "DomainFQDN",
                        "value": "contoso.com"
                    },
                    {
                        "name": "DomainNetBios",
                        "value": "CONTOSO"
                    },
                    {
                        "name": "ForestFQDN",
                        "value": "contoso.com"
                    },
                    {
                        "name": "ForestNetBios",
                        "value": "CONTOSO"
                    },
                    {
                        "name": "QuarantineTooManyDeletesThreshold",
                        "value": "500"
                    }
                ]
            }
    ```
1. Voer nu `https://graph.microsoft.com/beta/serviceprincipals/{Service Principal Id}/synchronization/jobs/{AD2AAD Provisioning id}/schema`de query uit .
 
    Voorbeeld: https://graph.microsoft.com/beta/serviceprincipals/653c0018-51f4-4736-a3a3-94da5dcb6862/synchronization/jobs/AD2AADProvisioning.e9287a7367e444c88dc67a531c36d8ec/schema

   Vervang `{Service Principal Id}` `{AD2ADD Provisioning Id}` en met uw waarden.

1. Met deze query wordt het schema geretourneerd.

   ![Geretourneerd schema](media/concept-attributes/schema1.png)
 
## <a name="next-steps"></a>Volgende stappen

- [Wat is inrichting?](what-is-provisioning.md)
- [Wat is Azure AD Connect-cloudinrichting?](what-is-cloud-provisioning.md)
