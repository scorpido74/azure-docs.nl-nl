---
title: Informatie over het Azure AD-schema en de aangepaste expressies
description: Dit onderwerp bevat een beschrijving van het Azure AD-schema, de kenmerken die de agent voor het inrichten van het inrichtings verkeer en de aangepaste expressies.
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
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: eae594bcc20e3c4ed1c6fbd0333699de8c9f4452
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794495"
---
# <a name="understanding-the-azure-ad-schema"></a>Informatie over het Azure AD-schema
Een object in azure AD, zoals een wille keurige Directory, is een programmatische gegevens constructie op hoog niveau waarmee items worden aangeduid als gebruikers, groepen en contact personen.  Wanneer u een nieuwe gebruiker of contact persoon in azure AD maakt, maakt u een nieuw exemplaar van dat object.  Deze instanties kunnen worden gedifferentieerd op basis van hun eigenschappen.

Eigenschappen, in azure AD, zijn de elementen die verantwoordelijk zijn voor het opslaan van informatie over een exemplaar van een object in azure AD.  

Het Azure AD-schema definieert de regels waarvoor eigenschappen kunnen worden gebruikt in een item, de typen waarden die deze eigenschappen kunnen hebben en hoe gebruikers met deze waarden kunnen werken. 

Azure AD heeft twee soorten eigenschappen.  De eigenschappen zijn:
- **Ingebouwde eigenschappen** : eigenschappen die vooraf zijn gedefinieerd door het Azure AD-schema.  Deze eigenschappen bieden verschillende toepassingen en zijn mogelijk niet toegankelijk.
- **Directory-extensies** : eigenschappen die worden gegeven, zodat u Azure AD kunt aanpassen voor eigen gebruik.  Als u bijvoorbeeld uw on-premises Active Directory hebt uitgebreid met een bepaald kenmerk en u dat kenmerk wilt debiet, kunt u een van de aangepaste eigenschappen gebruiken die worden gegeven. 

## <a name="attributes-and-expressions"></a>Kenmerken en expressies
Wanneer een object, zoals een gebruiker, is ingericht voor Azure AD, wordt er een nieuw exemplaar van het gebruikers object gemaakt.  Deze maken bevat de eigenschappen van dat object, ook wel bekend als kenmerken.  Het nieuw gemaakte object heeft in eerste instantie de kenmerken die zijn ingesteld op waarden die worden bepaald door de synchronisatie regels.  Deze kenmerken worden vervolgens up-to-date gehouden via de Cloud-inrichtings agent.

![](media/concept-attributes/attribute1.png)

Als een gebruiker bijvoorbeeld deel uitmaakt van de marketing afdeling, wordt het kenmerk van de Azure AD-afdeling eerst gemaakt wanneer deze worden ingericht en vervolgens wordt de waarde ingesteld op marketing.  Maar zes maanden later veranderen ze in verkoop.  Het kenmerk van de on-premises AD-afdeling is gewijzigd in verkoop.  Deze wijziging wordt vervolgens gesynchroniseerd met Azure AD en wordt doorgevoerd in hun Azure AD-gebruikers object.

Kenmerk synchronisatie kan direct zijn, waarbij de waarde in azure AD rechtstreeks wordt ingesteld op de waarde van het on-premises kenmerk.  Het is ook mogelijk dat er een programmatische expressie is waarmee deze synchronisatie wordt afgehandeld.  Een programmatische expressie zou nodig moeten zijn in gevallen waarin bepaalde logica of een bepaling moet worden toegepast om de waarde te kunnen vullen.

Als ik bijvoorbeeld mijn e-mail kenmerk ('john.smith@contoso.com' had) en ik het gedeelte '@contoso.com' had moeten verwijderen en alleen de waarde ' John. Smit ' zou gebruiken, zou dit er ongeveer als volgt moeten uitzien:

`Replace([mail], "@contoso.com", , ,"", ,)`  

**Voor beeld van invoer/uitvoer:** <br>

* **Invoer** (mail): "john.smith@contoso.com"
* **Uitvoer**: "Jan. Smit"

Voor aanvullende informatie over het schrijven van aangepaste expressies en de syntaxis Zie [expressies schrijven voor kenmerk toewijzingen in azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data).

De volgende lijst heeft algemene kenmerken en hoe deze worden gesynchroniseerd met Azure AD.


|On-premises Active Directory|Toewijzings type|Azure AD|
|-----|-----|-----|
|genoemd|Direct|commonName
|countryCode|Direct|countryCode|
|displayName|Direct|displayName|
|givenName|Expressie|givenName|
|GUID|Direct|sourceAnchorBinary|  
|userprincipalName|Direct|userPrincipalName|
|ProxyAdress|Direct|ProxyAddress attribuut|

## <a name="viewing-the-schema"></a>Het schema weer geven
Voer de volgende stappen uit om het schema weer te geven en te verifiëren:

1.  Navigeer naar [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).
2.  Meld u aan met het account van uw globale beheerder
3.  Klik aan de linkerkant op **machtigingen wijzigen** en controleer of **map. readwrite. all** is ingestemd.
4.  Voer de volgende query uit: https://graph.microsoft.com/beta/serviceprincipals/.  Met deze query wordt een lijst met Service-principals geretourneerd.
5.  Zoek "appDisplayName": "Active Directory naar Azure Active Directory inrichten" en noteer de waarde "id:".
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
6. Vervang de {Service Principal id} door de waarde en voer de volgende query uit: `https://graph.microsoft.com/beta/serviceprincipals/{Service Principal id}/synchronization/jobs/`
7. Zoek de sectie ' id ': ' AD2AADProvisioning. fd1c9b9e8077402c8bc03a7186c8f976 ' en noteer de ' id: '.
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
8. Voer nu de volgende query uit: `https://graph.microsoft.com/beta/serviceprincipals/{Service Principal Id}/synchronization/jobs/{AD2AAD Provisioning id}/schema`
 
    Voor beeld: https://graph.microsoft.com/beta/serviceprincipals/653c0018-51f4-4736-a3a3-94da5dcb6862/synchronization/jobs/AD2AADProvisioning.e9287a7367e444c88dc67a531c36d8ec/schema

 Vervang {Service Principal id} en {AD2ADD Provisioning id} door uw waarden.

9. Met deze query wordt het schema geretourneerd.
  ![](media/concept-attributes/schema1.png)
 
## <a name="next-steps"></a>Volgende stappen 

- [Wat is inrichten?](what-is-provisioning.md)
- [Wat is Azure AD Connect Cloud inrichting?](what-is-cloud-provisioning.md)
