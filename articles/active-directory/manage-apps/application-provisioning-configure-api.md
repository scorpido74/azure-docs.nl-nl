---
title: Gebruik MS Graph Api's om inrichting te configureren-Azure Active Directory | Microsoft Docs
description: Moet u inrichten instellen voor meerdere exemplaren van een toepassing? Meer informatie over hoe u tijd kunt besparen door gebruik te maken van MS Graph Api's om de configuratie van automatische inrichting te automatiseren.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00b3e6f84051aec97691d2bedda6665d3c9c0ceb
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74125072"
---
# <a name="configure-provisioning-using-microsoft-graph-apis"></a>Inrichting configureren met behulp van Microsoft Graph-Api's

De Azure Portal is een handige manier om de inrichting van afzonderlijke apps per keer te configureren. Maar als u meerdere of zelfs honderden exemplaren van een toepassing maakt, is het eenvoudiger om het maken en configureren van apps met Microsoft Graph Api's te automatiseren. In dit artikel wordt beschreven hoe u de inrichtings configuratie kunt automatiseren via Api's. Deze methode wordt doorgaans gebruikt voor toepassingen als [Amazon Web Services](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial#configure-azure-ad-single-sign-on).

**Overzicht van de stappen voor het gebruik van Microsoft Graph-Api's voor het automatiseren van de inrichtings configuratie**


|Stap  |Details  |
|---------|---------|
|[Stap 1. De galerie toepassing maken](#step-1-create-the-gallery-application)     |Meld u aan bij de API-client <br> De sjabloon voor de galerie toepassing ophalen <br> De galerie toepassing maken         |
|[Stap 2. Inrichtings taak maken op basis van sjabloon](#step-2-create-the-provisioning-job-based-on-the-template)     |De sjabloon voor de inrichtings connector ophalen <br> De inrichtings taak maken         |
|[Stap 3. Toegang toestaan](#step-3-authorize-access)     |De verbinding met de toepassing testen <br> De referenties opslaan         |
|[Stap 4. De inrichtings taak starten](#step-4-start-the-provisioning-job)     |Taak starten         |
|[Stap 5. Inrichting controleren](#step-5-monitor-provisioning)     |De status van de inrichtings taak controleren <br> De inrichtings logboeken ophalen         |

> [!NOTE]
> De antwoord objecten die in dit artikel worden weer gegeven, kunnen worden inge kort op Lees baarheid. Alle eigenschappen worden geretourneerd op basis van een daad werkelijke aanroep.

## <a name="step-1-create-the-gallery-application"></a>Stap 1: de galerie toepassing maken

### <a name="sign-in-to-microsoft-graph-explorer-recommended-postman-or-any-other-api-client-you-use"></a>Meld u aan bij Microsoft Graph Explorer (aanbevolen), Postman of een andere API-client die u gebruikt

1. [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) starten
1. Selecteer de knop aanmelden bij micro soft en meld u aan met behulp van de Azure AD Global-beheerder of de referenties van de app-beheerder.

    ![Graph-aanmelding](./media/export-import-provisioning-mappings/wd_export_02.png)

1. Wanneer de aanmelding is geslaagd, worden de gegevens van het gebruikers account in het linkerdeel venster weer gegeven.

### <a name="retrieve-the-gallery-application-template-identifier"></a>De id van de galerie toepassings sjabloon ophalen
Toepassingen in de Azure AD-toepassings galerie hebben elk een [toepassings sjabloon](https://docs.microsoft.com/graph/api/applicationtemplate-list?view=graph-rest-beta&tabs=http) waarmee de meta gegevens voor die toepassing worden beschreven. Met deze sjabloon kunt u een exemplaar van de toepassing en Service-Principal in uw Tenant maken voor beheer.

#### <a name="request"></a>*Aanvraag*

<!-- {
  "blockType": "request",
  "name": "get_applicationtemplates"
}-->

```msgraph-interactive
GET https://graph.microsoft.com/beta/applicationTemplates
```

#### <a name="response"></a>*Antwoord*

<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.applicationTemplate",
  "isCollection": true
} -->

```http
HTTP/1.1 200 OK
Content-type: application/json

{
  "value": [
  {
    "id": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "displayName": "Amazon Web Services (AWS)",
        "homePageUrl": "http://aws.amazon.com/",
        "supportedSingleSignOnModes": [
             "password",
             "saml",
             "external"
         ],
         "supportedProvisioningTypes": [
             "sync"
         ],
         "logoUrl": "https://az495088.vo.msecnd.net/app-logo/aws_215.png",
         "categories": [
             "developerServices"
         ],
         "publisher": "Amazon",
         "description": null    
  
}
```

### <a name="create-the-gallery-application"></a>De galerie toepassing maken

Gebruik de sjabloon-ID die is opgehaald voor uw toepassing in de laatste stap om [een exemplaar](https://docs.microsoft.com/graph/api/applicationtemplate-instantiate?view=graph-rest-beta&tabs=http) van de toepassing en Service-Principal in uw Tenant te maken.

#### <a name="request"></a>*Aanvraag*

<!-- {
  "blockType": "request",
  "name": "applicationtemplate_instantiate"
}-->

```msgraph-interactive
POST https://graph.microsoft.com/beta/applicationTemplates/{id}/instantiate
Content-type: application/json

{
  "displayName": "AWS Contoso"
}
```

#### <a name="response"></a>*Antwoord*


<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.applicationServicePrincipal"
} -->

```http
HTTP/1.1 201 OK
Content-type: application/json


{
    "application": {
        "objectId": "cbc071a6-0fa5-4859-8g55-e983ef63df63",
        "appId": "92653dd4-aa3a-3323-80cf-e8cfefcc8d5d",
        "applicationTemplateId": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "displayName": "AWS Contoso",
        "homepage": "https://signin.aws.amazon.com/saml?metadata=aws|ISV9.1|primary|z",
        "replyUrls": [
            "https://signin.aws.amazon.com/saml"
        ],
        "logoutUrl": null,
        "samlMetadataUrl": null,
    },
    "servicePrincipal": {
        "objectId": "f47a6776-bca7-4f2e-bc6c-eec59d058e3e",
        "appDisplayName": "AWS Contoso",
        "applicationTemplateId": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "appRoleAssignmentRequired": true,
        "displayName": "My custom name",
        "homepage": "https://signin.aws.amazon.com/saml?metadata=aws|ISV9.1|primary|z",
        "replyUrls": [
            "https://signin.aws.amazon.com/saml"
        ],
        "servicePrincipalNames": [
            "93653dd4-aa3a-4323-80cf-e8cfefcc8d7d"
        ],
        "tags": [
            "WindowsAzureActiveDirectoryIntegratedApp"
        ],
    }
}
```

## <a name="step-2-create-the-provisioning-job-based-on-the-template"></a>Stap 2: de inrichtings taak maken op basis van de sjabloon

### <a name="retrieve-the-template-for-the-provisioning-connector"></a>De sjabloon voor de inrichtings connector ophalen

Toepassingen in de galerie die zijn ingeschakeld voor inrichting, hebben sjablonen voor het stroom lijnen van de configuratie. Gebruik de onderstaande aanvraag om [de sjabloon voor de inrichtings configuratie](https://docs.microsoft.com/graph/api/synchronization-synchronizationtemplate-list?view=graph-rest-beta&tabs=http)op te halen.

#### <a name="request"></a>*Aanvraag*

<!-- {
  "blockType": "request",
  "name": "get_synchronizationtemplate"
}-->
```msgraph-interactive
GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/templates
```


#### <a name="response"></a>*Antwoord*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.synchronizationTemplate",
  "isCollection": true
} -->
```http
HTTP/1.1 200 OK

{
    "value": [
        {
            "id": "aws",
            "factoryTag": "aws",
            "schema": {
                    "directories": [],
                    "synchronizationRules": []
                    }
        }
    ]
}
```

### <a name="create-the-provisioning-job"></a>De inrichtings taak maken
Als u het inrichten wilt inschakelen, moet u eerst [een taak maken](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-post?view=graph-rest-beta&tabs=http). Gebruik de onderstaande aanvraag om een inrichtings taak te maken. Gebruik de ontbrekende templateid uit de vorige stap wanneer u de sjabloon opgeeft die voor de taak moet worden gebruikt.

#### <a name="request"></a>*Aanvraag*
<!-- {
  "blockType": "request",
  "name": "create_synchronizationjob_from_synchronization"
}-->
```msgraph-interactive
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs
Content-type: application/json

{ 
    "templateId": "aws"
}
```

#### <a name="response"></a>*Antwoord*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.synchronizationJob"
} -->
```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "id": "{jobId}",
    "templateId": "aws",
    "schedule": {
        "expiration": null,
        "interval": "P10675199DT2H48M5.4775807S",
        "state": "Disabled"
    },
    "status": {
        "countSuccessiveCompleteFailures": 0,
        "escrowsPruned": false,
        "synchronizedEntryCountByType": [],
        "code": "NotConfigured",
        "lastExecution": null,
        "lastSuccessfulExecution": null,
        "lastSuccessfulExecutionWithExports": null,
        "steadyStateFirstAchievedTime": "0001-01-01T00:00:00Z",
        "steadyStateLastAchievedTime": "0001-01-01T00:00:00Z",
        "quarantine": null,
        "troubleshootingUrl": null
    }
}
```

## <a name="step-3-authorize-access"></a>Stap 3: toegang autoriseren

### <a name="test-the-connection-to-the-application"></a>De verbinding met de toepassing testen

Test de verbinding met de toepassing van derden. Het onderstaande voor beeld is voor een toepassing waarvoor clientSecret en secretToken is vereist. Voor elke toepassing gelden de vereisten. Raadpleeg de [API-documentatie](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-validatecredentials?view=graph-rest-beta&tabs=http) voor een overzicht van de beschik bare opties. 

#### <a name="request"></a>*Aanvraag*
```http
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{id}/validateCredentials
{ 
    credentials: [ 
        { key: "ClientSecret", value: "xxxxxxxxxxxxxxxxxxxxx" },
        { key: "SecretToken", value: "xxxxxxxxxxxxxxxxxxxxx" }
    ]
}
```
#### <a name="response"></a>*Antwoord*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.None"
} -->
```http
HTTP/1.1 204 No Content
```

### <a name="save-your-credentials"></a>Uw referenties opslaan

Voor het configureren van de inrichting moet u een vertrouwens relatie tussen Azure AD en de toepassing tot stand brengen. Toegang verlenen tot de toepassing van derden. Het onderstaande voor beeld is voor een toepassing waarvoor clientSecret en secretToken is vereist. Voor elke toepassing gelden de vereisten. Raadpleeg de [API-documentatie](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-validatecredentials?view=graph-rest-beta&tabs=http) voor een overzicht van de beschik bare opties. 

#### <a name="request"></a>*Aanvraag*
```json
PUT https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/secrets 
 
{ 
    value: [ 
        { key: "ClientSecret", value: "xxxxxxxxxxxxxxxxxxxxx" },
        { key: "SecretToken", value: "xxxxxxxxxxxxxxxxxxxxx" }
    ]
}
```

#### <a name="response"></a>*Antwoord*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.None"
} -->
```http
HTTP/1.1 204 No Content
```

## <a name="step-4-start-the-provisioning-job"></a>Stap 4: de inrichtings taak starten
Nu de inrichtings taak is geconfigureerd, gebruikt u de volgende opdracht om [de taak te starten](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-start?view=graph-rest-beta&tabs=http). 


#### <a name="request"></a>*Aanvraag*
<!-- {
  "blockType": "request",
  "name": "synchronizationjob_start"
}-->
```http
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/start
```

#### <a name="response"></a>*Antwoord*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.None"
} -->
```http
HTTP/1.1 204 No Content
```


## <a name="step-5-monitor-provisioning"></a>Stap 5: inrichting controleren

### <a name="monitor-the-provisioning-job-status"></a>De status van de inrichtings taak controleren

Nu de inrichtings taak wordt uitgevoerd, gebruikt u de volgende opdracht om de voortgang van de huidige inrichtings cyclus te volgen, evenals statistieken tot datum, zoals het aantal gebruikers en groepen dat in het doel systeem is gemaakt. 

#### <a name="request"></a>*Aanvraag*
<!-- {
  "blockType": "request",
  "name": "get_synchronizationjob"
}-->
```msgraph-interactive
GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/
```

#### <a name="response"></a>*Antwoord*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.synchronizationJob"
} -->
```http
HTTP/1.1 200 OK
Content-type: application/json
Content-length: 2577

{
    "id": "{jobId}",
    "templateId": "aws",
    "schedule": {
        "expiration": null,
        "interval": "P10675199DT2H48M5.4775807S",
        "state": "Disabled"
    },
    "status": {
        "countSuccessiveCompleteFailures": 0,
        "escrowsPruned": false,
        "synchronizedEntryCountByType": [],
        "code": "Paused",
        "lastExecution": null,
        "lastSuccessfulExecution": null,
        "progress": [],
        "lastSuccessfulExecutionWithExports": null,
        "steadyStateFirstAchievedTime": "0001-01-01T00:00:00Z",
        "steadyStateLastAchievedTime": "0001-01-01T00:00:00Z",
        "quarantine": null,
        "troubleshootingUrl": null
    },
    "synchronizationJobSettings": [
      {
          "name": "QuarantineTooManyDeletesThreshold",
          "value": "500"
      }
    ]
}
```


### <a name="monitor-provisioning-events-using-the-provisioning-logs"></a>Inrichtings gebeurtenissen bewaken met behulp van de inrichtings logboeken
Naast het controleren van de status van de inrichtings taak, kunt u de [inrichtings logboeken](https://docs.microsoft.com/graph/api/provisioningobjectsummary-list?view=graph-rest-beta&tabs=http) gebruiken om een query uit te zoeken naar alle gebeurtenissen die plaatsvinden (bijvoorbeeld een query voor een bepaalde gebruiker en te bepalen of ze zijn ingericht).

#### <a name="request"></a>*Aanvraag*
```msgraph-interactive
GET https://graph.microsoft.com/beta/auditLogs/provisioning
```
#### <a name="response"></a>*Antwoord*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.provisioningObjectSummary",
  "name": "list_provisioningobjectsummary_error"
} -->

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#auditLogs/provisioning",
    "value": [
        {
            "id": "gc532ff9-r265-ec76-861e-42e2970a8218",
            "activityDateTime": "2019-06-24T20:53:08Z",
            "tenantId": "7928d5b5-7442-4a97-ne2d-66f9j9972ecn",
            "jobId": "BoxOutDelta.7928d5b574424a97ne2d66f9j9972ecn",
            "cycleId": "44576n58-v14b-70fj-8404-3d22tt46ed93",
            "changeId": "eaad2f8b-e6e3-409b-83bd-e4e2e57177d5",
            "action": "Create",
            "durationInMilliseconds": 2785,
            "sourceSystem": {
                "id": "0404601d-a9c0-4ec7-bbcd-02660120d8c9",
                "displayName": "Azure Active Directory",
                "details": {}
            },
            "targetSystem": {
                "id": "cd22f60b-5f2d-1adg-adb4-76ef31db996b",
                "displayName": "Box",
                "details": {
                    "ApplicationId": "f2764360-e0ec-5676-711e-cd6fc0d4dd61",
                    "ServicePrincipalId": "chc46a42-966b-47d7-9774-576b1c8bd0b8",
                    "ServicePrincipalDisplayName": "Box"
                }
            },
            "initiatedBy": {
                "id": "",
                "displayName": "Azure AD Provisioning Service",
                "initiatorType": "system"
            },
            "sourceIdentity": {
                "id": "5e6c9rae-ab4d-5239-8ad0-174391d110eb",
                "displayName": "Self-service Pilot",
                "identityType": "Group",
                "details": {}
            },
            "targetIdentity": {
                "id": "",
                "displayName": "",
                "identityType": "Group",
                "details": {}
            },
            "statusInfo": {
                "@odata.type": "#microsoft.graph.statusDetails",
                "status": "failure",
                "errorCode": "BoxEntryConflict",
                "reason": "Message: Box returned an error response with the HTTP status code 409.  This response indicates that a user or a group already exisits with the same name. This can be avoided by identifying and removing the conflicting user from Box via the Box administrative user interface, or removing the current user from the scope of provisioning either by removing their assignment to the Box application in Azure Active Directory or adding a scoping filter to exclude the user.",
                "additionalDetails": null,
                "errorCategory": "NonServiceFailure",
                "recommendedAction": null
            },
            "provisioningSteps": [
                {
                    "name": "EntryImportAdd",
                    "provisioningStepType": "import",
                    "status": "success",
                    "description": "Received Group 'Self-service Pilot' change of type (Add) from Azure Active Directory",
                    "details": {}
                },
                {
                    "name": "EntrySynchronizationAdd",
                    "provisioningStepType": "matching",
                    "status": "success",
                    "description": "Group 'Self-service Pilot' will be created in Box (Group is active and assigned in Azure Active Directory, but no matching Group was found in Box)",
                    "details": {}
                },
                {
                    "name": "EntryExportAdd",
                    "provisioningStepType": "export",
                    "status": "failure",
                    "description": "Failed to create Group 'Self-service Pilot' in Box",
                    "details": {
                        "ReportableIdentifier": "Self-service Pilot"
                    }
                }
            ],
            "modifiedProperties": [
                {
                    "displayName": "objectId",
                    "oldValue": null,
                    "newValue": "5e0c9eae-ad3d-4139-5ad0-174391d110eb"
                },
                {
                    "displayName": "displayName",
                    "oldValue": null,
                    "newValue": "Self-service Pilot"
                },
                {
                    "displayName": "mailEnabled",
                    "oldValue": null,
                    "newValue": "False"
                },
                {
                    "displayName": "mailNickname",
                    "oldValue": null,
                    "newValue": "5ce25n9a-4c5f-45c9-8362-ef3da29c66c5"
                },
                {
                    "displayName": "securityEnabled",
                    "oldValue": null,
                    "newValue": "True"
                },
                {
                    "displayName": "Name",
                    "oldValue": null,
                    "newValue": "Self-service Pilot"
                }
            ]
       }
    ]
}

```
## <a name="related-articles"></a>Verwante artikelen:

- [Raadpleeg de documentatie voor synchronisatie Microsoft Graph](https://docs.microsoft.com/graph/api/resources/synchronization-overview?view=graph-rest-beta)
- [Een aangepaste SCIM-app integreren met Azure AD](use-scim-to-provision-users-and-groups.md)
