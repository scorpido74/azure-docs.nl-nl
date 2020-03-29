---
title: Schema's voor de Azure Security Center-waarschuwingen
description: In dit artikel worden de verschillende schema's beschreven die worden gebruikt door Azure Security Center voor beveiligingswaarschuwingen.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2020
ms.author: memildin
ms.openlocfilehash: 19ca17f66f6818ed4c3ef532e2030cc03f0e73ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062956"
---
# <a name="security-alerts-schemas"></a>Beveiligingswaarschuwingen schema's

Gebruikers van de standaardlaag van Azure Security Center ontvangen beveiligingswaarschuwingen wanneer Security Center bedreigingen voor hun resources detecteert.

U deze beveiligingswaarschuwingen bekijken op **de pagina's bedreigingsbeveiliging van** Azure Security Center of via externe hulpprogramma's zoals:

- [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/) - Microsoft's cloud-native SIEM. De Sentinel Connector krijgt waarschuwingen van Azure Security Center en stuurt deze naar de [Log Analytics-werkruimte](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) voor Azure Sentinel.
- SPM's van derden - Gebruik de [tools voor continue export van](continuous-export.md) beveiligingscentrum om gegevens naar Azure Event [Hubs](https://docs.microsoft.com/azure/event-hubs/)te verzenden. Integreer vervolgens uw Event Hub-gegevens met een SIEM van derden.
- [De REST API](https://docs.microsoft.com/rest/api/securitycenter/) - Als u de REST API gebruikt om toegang te krijgen tot waarschuwingen, raadpleegt u de [api-documentatie voor online waarschuwingen.](https://docs.microsoft.com/rest/api/securitycenter/alerts)

Als u programmatische methoden gebruikt om de waarschuwingen te gebruiken, hebt u het juiste schema nodig om de velden te vinden die voor u relevant zijn. Als u exporteert naar een gebeurtenishub of workflowautomatisering probeert te activeren met algemene HTTP-connectors, gebruikt u de schema's om de JSON-objecten goed te ontleden.

>[!IMPORTANT]
> Het schema is iets anders voor elk van deze scenario's, dus zorg ervoor dat u het relevante tabblad hieronder selecteert.


## <a name="the-schemas"></a>De schema's 


### <a name="workflow-automation-and-continuous-export-to-event-hub"></a>[Workflowautomatisering en continue export naar eventhub](#tab/schema-continuousexport)

### <a name="sample-json-for-alerts-sent-to-logic-apps-event-hub-and-third-party-siems"></a>Voorbeeld van JSON voor waarschuwingen die worden verzonden naar Logic Apps, Event Hub en SPM's van derden

Hieronder vindt u het schema van de waarschuwingsgebeurtenissen die zijn doorgegeven aan:

- Azure Logic App-exemplaren die zijn geconfigureerd in de werkstroomautomatisering van Security Center
- Azure Event Hub met de functie continue export van beveiligingscentrum

Zie [Reacties op waarschuwingen en aanbevelingen automatiseren](workflow-automation.md)voor meer informatie over de functie voor workflowautomatisering.
Zie [Waarschuwingen en aanbevelingen exporteren](continuous-export.md)voor meer informatie over continue export.

[!INCLUDE [Workflow schema](../../includes/security-center-alerts-schema-workflow-automation.md)]




### <a name="azure-sentinel-and-log-analytics-workspaces"></a>[Azure Sentinel- en Log Analytics-werkruimten](#tab/schema-sentinel)

De Sentinel Connector krijgt waarschuwingen van Azure Security Center en stuurt deze naar de Log Analytics Workspace voor Azure Sentinel. 

Als u een Sentinel-aanvraag of -incident wilt maken met waarschuwingen voor beveiligingscentrum, hebt u het schema nodig voor de onderstaande waarschuwingen. 

Zie [de documentatie](https://docs.microsoft.com/azure/sentinel/)voor meer informatie over Azure Sentinel.

[!INCLUDE [Sentinel and workspace schema](../../includes/security-center-alerts-schema-log-analytics-workspace.md)]




### <a name="azure-activity-log"></a>[Azure-activiteitenlogboek](#tab/schema-activitylog)

Azure Security Center controleert gegenereerde beveiligingswaarschuwingen als gebeurtenissen in Azure Activity Log.

U de gebeurtenissen met beveiligingswaarschuwingen in het activiteitenlogboek bekijken door te zoeken naar de gebeurtenis Waarschuwing activeren zoals weergegeven:

[![Zoeken in het activiteitenlogboek voor de gebeurtenis Waarschuwing activeren](media/alerts-schemas/sample-activity-log-alert.png)](media/alerts-schemas/sample-activity-log-alert.png#lightbox)


### <a name="sample-json-for-alerts-sent-to-azure-activity-log"></a>Voorbeeld van JSON voor waarschuwingen die naar Azure-activiteitenlogboek worden verzonden

```json
{
    "channels": "Operation",
    "correlationId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "description": "PREVIEW - Role binding to the cluster-admin role detected. Kubernetes audit log analysis detected a new binding to the cluster-admin role which gives administrator privileges.\r\nUnnecessary administrator privileges might cause privilege escalation in the cluster.",
    "eventDataId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "eventName": {
        "value": "PREVIEW - Role binding to the cluster-admin role detected",
        "localizedValue": "PREVIEW - Role binding to the cluster-admin role detected"
    },
    "category": {
        "value": "Security",
        "localizedValue": "Security"
    },
    "eventTimestamp": "2019-12-25T18:52:36.801035Z",
    "id": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RESOURCE_GROUP_NAME/providers/Microsoft.Security/locations/centralus/alerts/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff/events/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff/ticks/637128967568010350",
    "level": "Informational",
    "operationId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "operationName": {
        "value": "Microsoft.Security/locations/alerts/activate/action",
        "localizedValue": "Activate Alert"
    },
    "resourceGroupName": "RESOURCE_GROUP_NAME",
    "resourceProviderName": {
        "value": "Microsoft.Security",
        "localizedValue": "Microsoft.Security"
    },
    "resourceType": {
        "value": "Microsoft.Security/locations/alerts",
        "localizedValue": "Microsoft.Security/locations/alerts"
    },
    "resourceId": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RESOURCE_GROUP_NAME/providers/Microsoft.Security/locations/centralus/alerts/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-12-25T19:14:03.5507487Z",
    "subscriptionId": "SUBSCRIPTION_ID",
    "properties": {
        "clusterRoleBindingName": "cluster-admin-binding",
        "subjectName": "for-binding-test",
        "subjectKind": "ServiceAccount",
        "username": "masterclient",
        "actionTaken": "Detected",
        "resourceType": "Kubernetes Service",
        "severity": "Low",
        "intent": "[\"Persistence\"]",
        "compromisedEntity": "ASC-IGNITE-DEMO",
        "remediationSteps": "[\"Review the user in the alert details. If cluster-admin is unnecessary for this user, consider granting lower privileges to the user.\"]",
        "attackedResourceType": "Kubernetes Service"
    },
    "relatedEvents": []
}
```

### <a name="the-data-model-of-the-schema"></a>Het gegevensmodel van het schema

|Veld|Beschrijving|
|----|----|
|**Kanalen**|Constant, "Operatie"|
|**correlationId**|De waarschuwings-id van Azure Security Center|
|**Beschrijving**|Beschrijving van de waarschuwing|
|**eventDataId**|Zie correlationId|
|**gebeurtenisNaam**|De subvelden waarde en gelokaliseerde waardewaarde bevatten de naam van de waarschuwingsweergave|
|**Categorie**|De waarde en gelokaliseerde subvelden Waarde zijn constant - 'Beveiliging'|
|**eventTimestamp**|UTC-tijdstempel voor wanneer de waarschuwing is gegenereerd|
|**id**|De volledig gekwalificeerde waarschuwings-ID|
|**Niveau**|Constant, "Informatief"|
|**operationId (operatieId)**|Zie correlationId|
|**operationName**|Het waardeveld is constant - "Microsoft.Security/locaties/waarschuwingen/activeren/actie", en de gelokaliseerde waarde is 'Waarschuwing activeren' (kan mogelijk gelokaliseerd worden bij de gebruikerslocale)|
|**resourceGroupName**|Bevat de naam van de resourcegroep|
|**resourceProviderName**|De subvelden waarde en gelokaliseerde waardewaarde zijn constant - 'Microsoft.Security'|
|**Resourcetype**|De subvelden waarde en gelokaliseerde waardewaarde zijn constant - 'Microsoft.Security/locaties/waarschuwingen'|
|**Resourceid**|De volledig gekwalificeerde Azure-bron-id|
|**status**|De waarde en gelokaliseerde subvelden Waarde zijn constant - 'Actief'|
|**subStatus**|De waarde en gelokaliseerde subvelden Waarde zijn leeg|
|**indieningTijdstempel**|De UTC-tijdstempel van het indienen van gebeurtenissen in het activiteitenlogboek|
|**abonnementId**|De abonnements-ID van de gecompromitteerde bron|
|**Eigenschappen**|Een JSON zak met extra eigenschappen met betrekking tot de waarschuwing. Deze kunnen van de ene waarschuwing naar de andere veranderen, maar de volgende velden worden in alle waarschuwingen weergegeven:<br>- ernst: De ernst van de aanval<br>- gecompromitteerde Entiteit: De naam van de gecompromitteerde bron<br>- saneringSstappen: Reeks herstelstappen die moeten worden genomen<br>- intentie: De kill-chain intentie van de waarschuwing. Mogelijke intenties worden gedocumenteerd in de [tabel Intenties](alerts-reference.md#intentions)|
|**gerelateerdeEvenementen**|Constant - lege array|
|||





### <a name="ms-graph-api"></a>[MS Graph API](#tab/schema-graphapi)

Microsoft Graph is de toegangspoort tot gegevens en intelligentie in Microsoft 365. Het biedt een uniform programmeerbaarheidsmodel dat u gebruiken om toegang te krijgen tot de enorme hoeveelheid gegevens in Office 365, Windows 10 en Enterprise Mobility + Security. Gebruik de schat aan gegevens in Microsoft Graph om apps te bouwen voor organisaties en consumenten die met miljoenen gebruikers communiceren.

Het schema en een JSON-weergave voor beveiligingswaarschuwingen die naar MS Graph worden verzonden, zijn beschikbaar in [de documentatie van Microsoft Graph.](https://docs.microsoft.com/graph/api/resources/alert?view=graph-rest-1.0)

---


## <a name="next-steps"></a>Volgende stappen

In dit artikel worden de schema's beschreven die de hulpprogramma's voor bedreigingsbeveiliging van Azure Security Center gebruiken bij het verzenden van beveiligingsbeveiligingsgegevens.

Zie de volgende pagina's voor meer informatie over de manieren om toegang te krijgen tot beveiligingswaarschuwingen van buiten het beveiligingscentrum:

- [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/) - Microsoft's cloud-native SIEM
- [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/) - De volledig beheerde, realtime gegevensopnameservice van Microsoft
- De continue [exportfunctie](continuous-export.md) van Security Center
- [Log Analytics-werkruimten](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) - Azure Monitor slaat logboekgegevens op in een Log Analytics-werkruimte, een container met gegevens- en configuratiegegevens
