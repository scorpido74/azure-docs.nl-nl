---
title: Schema's voor de Azure Security Center waarschuwingen
description: In dit artikel worden de verschillende schema's beschreven die worden gebruikt door Azure Security Center voor beveiligings waarschuwingen.
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
ms.openlocfilehash: 9b8d7f241f79b087f318c9c416dcadf92838b084
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85126209"
---
# <a name="security-alerts-schemas"></a>Schema's voor beveiligings waarschuwingen

Gebruikers van de Standard-laag van Azure Security Center ontvangen beveiligings waarschuwingen wanneer Security Center bedreigingen voor hun resources detecteert.

U kunt deze beveiligings waarschuwingen bekijken op de pagina's van Azure Security Center **Threat Protection** of via externe hulpprogram ma's, zoals:

- [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/) : de Cloud-native Siem van micro soft. De Sentinel-connector ontvangt waarschuwingen van Azure Security Center en verzendt deze naar de [log Analytics-werk ruimte](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) voor Azure Sentinel.
- Siem's van derden: gebruik de [continue export](continuous-export.md) hulpprogramma's van Security Center om gegevens te verzenden naar [Azure Event hubs](https://docs.microsoft.com/azure/event-hubs/). Integreer vervolgens uw event hub-gegevens met een SIEM van derden.
- [De rest API](https://docs.microsoft.com/rest/api/securitycenter/) : als u de rest API gebruikt voor toegang tot waarschuwingen, raadpleegt u de [online Alerts API-documentatie](https://docs.microsoft.com/rest/api/securitycenter/alerts).

Als u een programmatische methode gebruikt om de waarschuwingen te verbruiken, hebt u het juiste schema nodig om de velden te vinden die relevant zijn voor u. Als u exporteert naar een event hub of als u werk stroom automatisering wilt activeren met algemene HTTP-connectors, gebruikt u de schema's om de JSON-objecten correct te parseren.

>[!IMPORTANT]
> Het schema wijkt enigszins af voor elk van deze scenario's. Zorg er dus voor dat u het relevante tabblad hieronder selecteert.


## <a name="the-schemas"></a>De schema's 


### <a name="workflow-automation-and-continuous-export-to-event-hub"></a>[Werk stroom automatisering en doorlopend exporteren naar Event hub](#tab/schema-continuousexport)

### <a name="sample-json-for-alerts-sent-to-logic-apps-event-hub-and-third-party-siems"></a>Voor beeld-JSON voor waarschuwingen die worden verzonden naar Logic Apps, Event hub en Siem's van derden

Hieronder vindt u het schema van de waarschuwings gebeurtenissen die worden door gegeven aan:

- Azure Logic app-exemplaren die in de werk stroom automatisering van Security Center zijn geconfigureerd
- Azure Event hub met behulp van de continue export functie van Security Center

Zie [reacties op waarschuwingen en aanbevelingen automatiseren](workflow-automation.md)voor meer informatie over de functie voor het automatiseren van werk stromen.
Zie [waarschuwingen en aanbevelingen exporteren](continuous-export.md)voor meer informatie over continue export.

[!INCLUDE [Workflow schema](../../includes/security-center-alerts-schema-workflow-automation.md)]




### <a name="azure-sentinel-and-log-analytics-workspaces"></a>[Azure Sentinel-en Log Analytics-werk ruimten](#tab/schema-sentinel)

De Sentinel-connector ontvangt waarschuwingen van Azure Security Center en verzendt deze naar de Log Analytics-werk ruimte voor Azure Sentinel. 

Als u een verklikker Case of incident wilt maken met behulp van Security Center waarschuwingen, hebt u het schema nodig voor de hieronder weer gegeven waarschuwingen. 

Raadpleeg [de documentatie](https://docs.microsoft.com/azure/sentinel/)voor meer informatie over Azure Sentinel.

[!INCLUDE [Sentinel and workspace schema](../../includes/security-center-alerts-schema-log-analytics-workspace.md)]




### <a name="azure-activity-log"></a>[Azure-activiteitenlogboek](#tab/schema-activitylog)

Azure Security Center controles gegenereerd beveiligings waarschuwingen als gebeurtenissen in azure-activiteiten logboek.

U kunt de gebeurtenissen voor beveiligings waarschuwingen in het activiteiten logboek weer geven door te zoeken naar de gebeurtenis waarschuwing activeren, zoals wordt weer gegeven:

[![Zoeken in het activiteiten logboek voor de gebeurtenis waarschuwing activeren](media/alerts-schemas/sample-activity-log-alert.png)](media/alerts-schemas/sample-activity-log-alert.png#lightbox)


### <a name="sample-json-for-alerts-sent-to-azure-activity-log"></a>Voor beeld-JSON voor waarschuwingen die zijn verzonden naar Azure-activiteiten logboek

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

### <a name="the-data-model-of-the-schema"></a>Het gegevens model van het schema

|Veld|Beschrijving|
|----|----|
|**detailhandelkanalen**|Constante, ' bewerking '|
|**correlationId**|De meldings-ID Azure Security Center|
|**beschrijvingen**|Beschrijving van de waarschuwing|
|**eventDataId**|Zie correlationId|
|**eventName**|De subvelden value en localizedValue bevatten de weergave naam van de waarschuwing|
|**category**|De subvelden value en localizedValue zijn constant-' Security '|
|**eventTimestamp**|UTC-tijds tempel voor het moment waarop de waarschuwing is gegenereerd|
|**id**|De volledig gekwalificeerde waarschuwings-ID|
|**niveau**|Constante, ' informatief '|
|**operationId**|Zie correlationId|
|**operationName**|Het veld waarde is constant: ' micro soft. Security/locations/Alerts/Activate/Action ' en de gelokaliseerde waarde is ' waarschuwing activeren ' (de gebruiker kan mogelijk worden gelokaliseerd pari)|
|**resourceGroupName**|Bevat de naam van de resource groep|
|**resourceProviderName**|De subvelden value en localizedValue zijn constant-"micro soft. Security"|
|**Resource**|De subvelden value en localizedValue zijn constant-"micro soft. Security/locations/Alerts"|
|**resourceId**|De volledig gekwalificeerde Azure-Resource-ID|
|**hebben**|De subvelden value en localizedValue zijn constant-"actief"|
|**subStatus**|De subvelden value en localizedValue zijn leeg|
|**submissionTimestamp**|De UTC-tijds tempel van het verzenden van gebeurtenissen naar het activiteiten logboek|
|**Abonnements**|De abonnements-ID van de aangetaste resource|
|**properties**|Een JSON-verzameling extra eigenschappen die betrekking hebben op de waarschuwing. Deze kunnen worden gewijzigd van de ene waarschuwing naar de andere, maar de volgende velden worden in alle waarschuwingen weer gegeven:<br>-Ernst: de ernst van de aanval<br>-compromisedEntity: de naam van de aangetaste bron<br>-remediationSteps: matrix van herstels tappen die moeten worden genomen<br>-intentie: de bedoeling van de Kill-keten van de waarschuwing. Mogelijke intenties worden gedocumenteerd in de [tabel met bedoelingen](alerts-reference.md#intentions)|
|**relatedEvents**|Constante-lege matrix|
|||





### <a name="ms-graph-api"></a>[MS Graph API](#tab/schema-graphapi)

Microsoft Graph is de gateway naar gegevens en intelligentie in Microsoft 365. Het biedt een uniform programmeerbaar model dat u kunt gebruiken voor toegang tot de enorme hoeveelheid gegevens in Microsoft 365, Windows 10 en Enterprise Mobility + Security. Gebruik de schat aan gegevens in Microsoft Graph om apps te bouwen voor organisaties en consumenten die met miljoenen gebruikers communiceren.

Het schema en een JSON-weer gave voor beveiligings waarschuwingen die naar MS Graph worden verzonden, zijn beschikbaar in [de Microsoft Graph documentatie](https://docs.microsoft.com/graph/api/resources/alert?view=graph-rest-1.0).

---


## <a name="next-steps"></a>Volgende stappen

In dit artikel worden de schema's beschreven die worden gebruikt bij het verzenden van informatie over beveiligings waarschuwingen Azure Security Center de hulpprogram ma's voor beveiliging tegen bedreigingen.

Zie de volgende pagina's voor meer informatie over de manieren om toegang te krijgen tot beveiligings waarschuwingen van buiten Security Center:

- [Azure-Sentinel](https://docs.microsoft.com/azure/sentinel/) : de Cloud-native Siem van micro soft
- [Azure-Event hubs](https://docs.microsoft.com/azure/event-hubs/) -de volledig beheerde, realtime Service voor gegevens opname van micro soft
- De [functie continue export](continuous-export.md) van Security Center
- [Log Analytics werk ruimten](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) : Azure monitor slaat logboek gegevens op in een log Analytics-werk ruimte, een container die gegevens en configuratie gegevens bevat
