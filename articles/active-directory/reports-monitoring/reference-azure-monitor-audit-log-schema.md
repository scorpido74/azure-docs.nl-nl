---
title: Het azure Active Directory-controlelogboekschema interpreteren in Azure Monitor | Microsoft Documenten
description: Het Azure AD-controlelogboekschema beschrijven voor gebruik in Azure Monitor
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: d9f58b213e50a021651f35112a48d8f74ae59571
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68987946"
---
# <a name="interpret-the-azure-ad-audit-logs-schema-in-azure-monitor-preview"></a>Het Azure AD-controlelogboekenschema interpreteren in Azure Monitor (voorbeeld)

In dit artikel wordt het Azure Active Directory-controlelogboekschema (Azure AD) in Azure Monitor beschreven. Elke afzonderlijke logboekvermelding wordt opgeslagen als tekst en opgemaakt als een JSON-blob, zoals in de volgende twee voorbeelden wordt weergegeven: 

```json
{ 
    "records": [ 
    { 
        "time": "2018-03-17T00:14:31.2585575Z", 
        "operationName": "Change password (self-service)",
        "operationVersion": "1.0",
        "category": "Audit", 
        "tenantId": "bf85dc9d-cb43-44a4-80c4-469e8c58249e", 
        "resultType": "Success", 
        "resultSignature": "-1", 
        "resultDescription": "None", 
        "durationMs": "-1", 
        "correlationId": "60d5e89a-b890-413f-9e25-a047734afe9f", 
        "identity": "sreens@wingtiptoysonline.com", 
        "Level": "Informational", 
        "location": "WUS", 
        "properties": { 
            "identityType": "UPN", 
            "operationType": "Update", 
            "additionalDetails": "None", 
            "additionalTargets": "", 
            "targetUpdatedProperties": "", 
            "targetResourceType": "UPN__TenantContextID__PUID__ObjectID__ObjectClass", 
            "targetResourceName": "sreens@wingtiptoysonline.com__bf85dc9d-cb43-44a4-80c4-469e8c58249e__1003BFFD9FEB17DB__7a408bdd-7d97-4574-8511-dd747b56465d__User", 
            "auditEventCategory": "UserManagement" 
        } 
    } 
    ] 
} 
```

```json
{ 
    "records": [ 
    { 
        "time": "2018-03-18T19:47:43.0368859Z", 
        "operationName": "Update service principal.", 
        "operationVersion": "1.0", 
        "category": "Audit", 
        "tenantId": "bf85dc9d-cb43-44a4-80c4-469e8c58249e", 
        "resultType": "Success", 
        "resultSignature": "-1", 
        "durationMs": "-1", 
        "callerIpAddress": "<null>", 
        "correlationId": "14916c7a-5a7d-44e8-9b06-74b49efb08ee", 
        "identity": "NA", 
        "Level": "Informational", 
        "properties": { 
            "identityType": "NA", 
            "operationType": "Update", 
            "additionalDetails": {}, 
            "additionalTargets": "", 
            "targetUpdatedProperties": [ 
            { 
                "Name": "Included Updated Properties", 
                "OldValue": null, 
                "NewValue": "" 
            }, 
            { 
                "Name": "TargetId.ServicePrincipalNames", 
                "OldValue": null, 
                "NewValue": "http://adapplicationregistry.onmicrosoft.com/salesforce.com/primary;cd3ed3de-93ee-400b-8b19-b61ef44a0f29" 
            } 
            ], 
        "targetResourceType": "Other__ObjectID__ObjectClass__Name__AppId__SPN", 
        "targetResourceName": "ServicePrincipal_ea70a262-4da3-440a-b396-9734ddfd9df2__ea70a262-4da3-440a-b396-9734ddfd9df2__ServicePrincipal__Salesforce__cd3ed3de-93ee-400b-8b19-b61ef44a0f29__http://adapplicationregistry.onmicrosoft.com/salesforce.com/primary;cd3ed3de-93ee-400b-8b19-b61ef44a0f29", 
        "auditEventCategory": "ApplicationManagement" 
      } 
    } 
    ] 
} 
```

```json
{
    "records": [
    {
        "time": "2018-12-10T00:03:46.6161822Z",
        "resourceId": "/tenants/7918d4b5-0442-4a97-be2d-36f9f9962ece/providers/Microsoft.aadiam",
        "operationName": "Update policy",
        "operationVersion": "1.0",
        "category": "AuditLogs",
        "tenantId": "7918d4b5-0442-4a97-be2d-36f9f9962ece",
        "resultSignature": "None",
        "durationMs": 0,
        "callerIpAddress": "<null>",
        "correlationId": "192298c1-0994-4dd6-b05a-a6c5984c31cb",
        "identity": "MS-PIM",
        "level": "Informational",
        "properties": {
            "id": "Directory_VNXV4_28148892",
            "category": "Policy",
            "correlationId": "192298c1-0994-4dd6-b05a-a6c5984c31cb",
            "result": 0,
            "resultReason": "",
            "activityDisplayName": "Update policy",
            "activityDateTime": "2018-12-10T00:03:46.6161822+00:00",
            "loggedByService": "Core Directory",
            "operationType": "Update",
            "initiatedBy": {},
            "targetResources": [
            {
                "id": "5e7a8ae7-165d-44a4-a4f4-6141f8c8ef40",
                "displayName": "Default Policy",
                "type": "Policy",
                "modifiedProperties": []
            }
            ],
            "additionalDetails": []
        }
    }
    ]
}

```

## <a name="field-and-property-descriptions"></a>Beschrijvingen van velden en eigendommen

| Veldnaam | Beschrijving |
|------------|-------------|
| tijd       | De datum en tijd (UTC). |
| operationName | De naam van de bewerking. |
| operationVersion | De REST API-versie die door de client wordt aangevraagd. |
| category | Momenteel is *Audit* de enige ondersteunde waarde. |
| tenantId | De tenant-GUID die is gekoppeld aan de logboeken. |
| resultType | Het resultaat van de operatie. Het resultaat kan *succes* of *mislukking*zijn. |
| resultSignature |  Dit veld is niet in kaart gebracht en u het veilig negeren. | 
| resultDescription | Een aanvullende beschrijving van het resultaat, indien beschikbaar. | 
| durationMs |  Dit veld is niet in kaart gebracht en u het veilig negeren. |
| callerIpAddress | Het IP-adres van de klant die het verzoek heeft ingediend. | 
| correlationId | Een optionele GUID die door de client wordt doorgegeven. Het kan helpen bij het correleren van client-side operations met server-side operations en het is handig wanneer u logboeken bijhoudt die services overspannen. |
| identity | De identiteit van het token dat werd gepresenteerd toen u het verzoek indiende. De identiteit kan een gebruikersaccount, systeemaccount of serviceprincipal zijn. |
| niveau | Het berichttype. Voor auditlogs is het niveau altijd *informatief.* |
| location | De locatie van het datacenter. |
| properties | Hier worden de ondersteunde eigenschappen weergegeven die gerelateerd zijn aan een controlelogboek. Zie de volgende tabel voor meer informatie. | 

<br>

| Naam van eigenschap | Beschrijving |
|---------------|-------------|
| AuditEventCategorie | Het type auditgebeurtenis. Het kan *Gebruikersbeheer,* *Toepassingsbeheer*of een ander type zijn.|
| Identiteitstype | Het type kan *toepassing* of *gebruiker*zijn. |
| Type bewerking | Het type kan *Toevoegen,* *Bijwerken*, *Verwijderen*. of *Andere*. |
| Doelbrontype | Hiermee geeft u het doelbrontype op waarop de bewerking is uitgevoerd. Het type kan *toepassing,* *gebruiker,* *rol,* *beleid* zijn | 
| Naam van doelbron | De naam van de doelbron. Het kan een toepassingsnaam, een rolnaam, een gebruikersnaam of een hoofdnaam van de service zijn. |
| aanvullende doelen | Geeft een lijst van eventuele extra eigenschappen voor specifieke bewerkingen. Voor een updatebewerking worden bijvoorbeeld de oude waarden en de nieuwe waarden weergegeven onder *targetUpdatedProperties*. | 

## <a name="next-steps"></a>Volgende stappen

* [Interpret sign-in logs schema in Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md) (Aanmeldingslogboekenschema interpreteren in Azure Monitor)
* [Azure-diagnostische logboeken](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [Veelgestelde vragen en bekende problemen](concept-activity-logs-azure-monitor.md#frequently-asked-questions)