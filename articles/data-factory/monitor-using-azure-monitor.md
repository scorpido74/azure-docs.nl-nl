---
title: Gegevens fabrieken bewaken met behulp van Azure Monitor | Microsoft Docs
description: Meer informatie over het gebruik van Azure Monitor om Data Factory pijp lijnen te bewaken door Diagnostische logboeken in te scha kelen met informatie van Azure Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: shlo
ms.openlocfilehash: 6bad74d33f5d50bb7a35de69927bf97daad07798
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326849"
---
# <a name="alert-and-monitor-data-factories-using-azure-monitor"></a>Gegevens fabrieken melden en bewaken met behulp van Azure Monitor
Cloud toepassingen zijn complex met veel bewegende onderdelen. Bewaking biedt gegevens om ervoor te zorgen dat uw toepassing in een goede staat actief blijft. Het helpt u ook om mogelijke problemen op te lossen of om Stave te oplossen. Daarnaast kunt u bewakings gegevens gebruiken om grondige inzichten over uw toepassing te krijgen. Deze kennis kan u helpen bij het verbeteren van de prestaties of het onderhoud van toepassingen, of het automatiseren van acties waarvoor anders hand matige interventie nodig zou zijn.

Azure Monitor biedt metrische gegevens en logboeken voor de infra structuur op basis niveau voor de meeste services in Microsoft Azure. Zie [bewakings overzicht](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor)voor meer informatie. Diagnostische logboeken van Azure zijn logboeken die worden verzonden door een resource die uitgebreide, frequente gegevens over de werking van die resource levert. Data Factory worden Diagnostische logboeken in Azure Monitor uitgevoerd.

## <a name="persist-data-factory-data"></a>Data Factory gegevens persistent maken
Data Factory slaat alleen pijplijn run data op voor 45 dagen. Als u de gegevens van de pijplijn periode langer dan 45 dagen wilt behouden, met behulp van Azure Monitor, kunt u geen Diagnostische logboeken voor analyse naar een opslag account sturen, zodat u over de fabrieks gegevens beschikt voor de duur van uw keuze.

## <a name="diagnostic-logs"></a>Diagnostische logboeken

* Sla ze op in een **opslag account** voor controle of hand matige inspectie. U kunt de Bewaar periode (in dagen) opgeven met behulp van de diagnostische instellingen.
* Stream ze naar **Event hubs** voor opname door een service van derden of een aangepaste analyse oplossing, zoals Power bi.
* Deze analyseren met **log Analytics**

U kunt een opslag account of Event Hub naam ruimte gebruiken die zich niet in hetzelfde abonnement bevindt als de resource die logboeken verzendt. De gebruiker die de instelling configureert, moet beschikken over de juiste RBAC-toegang (op rollen gebaseerd toegangs beheer) voor beide abonnementen.

## <a name="set-up-diagnostic-logs"></a>Diagnostische logboeken instellen

### <a name="diagnostic-settings"></a>Diagnostische instellingen
Diagnostische logboeken voor niet-reken resources worden geconfigureerd met Diagnostische instellingen. Diagnostische instellingen voor een resource beheer:

* Waar Diagnostische logboeken worden verzonden (opslag account, Event Hubs of Azure Monitor-Logboeken).
* Welke logboek categorieën worden verzonden.
* Hoe lang elke logboek categorie moet worden bewaard in een opslag account.
* Een bewaarperiode van nul dagen betekent dat Logboeken altijd worden bewaard. De waarde kan anders een willekeurig aantal dagen tussen 1 en 2147483647 zijn.
* Als het Bewaar beleid is ingesteld, maar logboeken opslaat in een opslag account is uitgeschakeld (bijvoorbeeld alleen Event Hubs of Azure Monitor logboeken opties zijn geselecteerd), heeft het Bewaar beleid geen effect.
* Bewaarbeleid zijn toegepast per dag, dus aan het einde van een dag (UTC), logboeken van de dag dat nu is buiten de bewaarperiode van beleid worden verwijderd. Bijvoorbeeld, als u een beleid voor het bewaren van één dag had, worden aan het begin van de dag vandaag nog de logboeken van de dag voor gisteren vernietigd.

### <a name="enable-diagnostic-logs-via-rest-apis"></a>Diagnostische logboeken inschakelen via REST-Api's

Een diagnostische instelling in Azure Monitor maken of bijwerken REST API

**Aanvraag**
```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

**Headers**
* Vervang `{api-version}` door `2016-09-01`.
* Vervang `{resource-id}` door de resource-id van de resource waarvoor u de diagnostische instellingen wilt bewerken. Voor meer informatie over [het beheren van uw Azure-resources met behulp van resource groepen](../azure-resource-manager/manage-resource-groups-portal.md).
* Stel de `Content-Type` koptekst in `application/json`op.
* Stel de autorisatie-header in op een JSON-webtoken dat u van Azure Active Directory ontvangt. Zie [verificatie aanvragen](../active-directory/develop/authentication-scenarios.md)voor meer informatie.

**Hoofdtekst**
```json
{
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "metrics": [
        ],
        "logs": [
                {
                    "category": "PipelineRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "TriggerRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "ActivityRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                }
            ]
    },
    "location": ""
}
```

| Eigenschap | Type | Description |
| --- | --- | --- |
| storageAccountId |String | De resource-ID van het opslag account waarnaar u Diagnostische logboeken wilt verzenden |
| serviceBusRuleId |String | De service bus-regel-ID van de service bus-naam ruimte waarin u Event Hubs wilt maken voor het streamen van Diagnostische logboeken. De regel-ID heeft de volgende indeling: {Service Bus Resource ID}/authorizationrules/{key name}.|
| workspaceId | Complex type | Matrix van metrische tijd korrels en het Bewaar beleid. Deze eigenschap is momenteel leeg. |
|metrics| Parameter waarden van de pijplijn uitvoering worden door gegeven aan de aangeroepen pijp lijn| Een JSON-object parameter namen toewijzen aan argument waarden |
| logs| Complex type| Naam van een diagnostische logboek categorie voor een resource type. Als u de lijst met diagnostische logboek categorieën voor een resource wilt ophalen, moet u eerst een bewerking Diagnostische instellingen ophalen uitvoeren. |
| category | String | Matrix met logboek categorieën en het Bewaar beleid |
| timeGrain | String | De granulatie van metrische gegevens die zijn vastgelegd in de ISO 8601-duur notatie. Moet PT1M (één minuut)|
| enabled| Boolean-waarde | Hiermee wordt aangegeven of de verzameling van deze metrische waarde of logboek categorie is ingeschakeld voor deze resource|
| retentionPolicy| Complex type| Hierin wordt het Bewaar beleid voor een metrische of logboek categorie beschreven. Wordt alleen gebruikt voor de optie voor het opslag account.|
| days| Int| Aantal dagen dat de metrische gegevens of logboeken moeten worden bewaard. De waarde 0 behoudt de logboeken voor onbepaalde tijd. Wordt alleen gebruikt voor de optie voor het opslag account. |

**Antwoord**

200 OK


```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```

Informatie over de diagnostische instellingen in Azure Monitor weer geven REST API

**Aanvraag**
```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

**Headers**
* Vervang `{api-version}` door `2016-09-01`.
* Vervang `{resource-id}` door de resource-id van de resource waarvoor u de diagnostische instellingen wilt bewerken. Voor meer informatie over het beheren van uw Azure-resources met behulp van resource groepen.
* Stel de `Content-Type` koptekst in `application/json`op.
* Stel de autorisatie-header in op een JSON Web Token die u hebt verkregen via Azure Active Directory. Zie verificatie aanvragen voor meer informatie.

**Antwoord**

200 OK

```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.Storage/storageAccounts/azmonlogs",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.EventHub/namespaces/shloeventhub/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/ADF/providers/Microsoft.OperationalInsights/workspaces/mihaipie",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```
[Meer informatie hier](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)

## <a name="schema-of-logs--events"></a>Schema van Logboeken & gebeurtenissen

### <a name="azure-monitor-schema"></a>Azure Monitor schema

#### <a name="activity-run-logs-attributes"></a>Kenmerken voor het uitvoeren van activiteiten logboeken

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "activityRunId":"",
   "pipelineRunId":"",
   "resourceId":"",
   "category":"ActivityRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "activityName":"",
   "start":"",
   "end":"",
   "properties":
       {
          "Input": "{
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "BlobSink"
              }
           }",
          "Output": "{"dataRead":121,"dataWritten":121,"copyDuration":5,
               "throughput":0.0236328132,"errors":[]}",
          "Error": "{
              "errorCode": "null",
              "message": "null",
              "failureType": "null",
              "target": "CopyBlobtoBlob"
        }
    }
}
```

| Eigenschap | Type | Description | Voorbeeld |
| --- | --- | --- | --- |
| Niveau |Tekenreeks | Het niveau van de diagnostische Logboeken. Niveau 4 is altijd het geval voor het uitvoeren van activiteiten Logboeken. | `4`  |
| correlationId |Tekenreeks | Unieke ID voor het bijhouden van end-to-end van een bepaalde aanvraag | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | Tekenreeks | Tijd van de gebeurtenis in time span, UTC-notatie`YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|activityRunId| Tekenreeks| ID van de uitvoering van de activiteit | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|pipelineRunId| Tekenreeks| ID van de pijplijn uitvoering | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|resourceId| Tekenreeks | De bijbehorende resource-ID voor de data factory resource | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| Reeks | De categorie van Diagnostische logboeken. Stel deze eigenschap in op ' ActivityRuns ' | `ActivityRuns` |
|level| Reeks | Het niveau van de diagnostische Logboeken. Stel deze eigenschap in op ' informatief ' | `Informational` |
|operationName| Reeks |De naam van de activiteit met de status. Als de status de heartbeat start is, is `MyActivity -`dit. Als de status end heartbeat is, is `MyActivity - Succeeded` dit de laatste status | `MyActivity - Succeeded` |
|pipelineName| Tekenreeks | Naam van de pijp lijn | `MyPipeline` |
|activityName| Tekenreeks | Naam van de activiteit | `MyActivity` |
|start| Tekenreeks | Begin van de uitvoering van de activiteit in time span, UTC-indeling | `2017-06-26T20:55:29.5007959Z`|
|end| Tekenreeks | Eindigt op de uitvoering van de activiteit in time span, UTC-indeling. Als de activiteit nog niet is beëindigd (diagnostisch logboek voor het starten van een activiteit), is de `1601-01-01T00:00:00Z` standaard waarde van is ingesteld.  | `2017-06-26T20:55:29.5007959Z` |

#### <a name="pipeline-run-logs-attributes"></a>Kenmerken van Logboeken voor pijplijn uitvoeringen

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "runId":"",
   "resourceId":"",
   "category":"PipelineRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "start":"",
   "end":"",
   "status":"",
   "properties":
    {
      "Parameters": {
        "<parameter1Name>": "<parameter1Value>"
      },
      "SystemParameters": {
        "ExecutionStart": "",
        "TriggerId": "",
        "SubscriptionId": ""
      }
    }
}
```

| Eigenschap | Type | Description | Voorbeeld |
| --- | --- | --- | --- |
| Niveau |Tekenreeks | Het niveau van de diagnostische Logboeken. Niveau 4 is het geval voor het uitvoeren van activiteiten Logboeken. | `4`  |
| correlationId |Tekenreeks | Unieke ID voor het bijhouden van end-to-end van een bepaalde aanvraag | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | Tekenreeks | Tijd van de gebeurtenis in time span, UTC-notatie`YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|runId| Tekenreeks| ID van de pijplijn uitvoering | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|resourceId| Tekenreeks | De bijbehorende resource-ID voor de data factory resource | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| Reeks | De categorie van Diagnostische logboeken. Stel deze eigenschap in op ' PipelineRuns ' | `PipelineRuns` |
|level| Tekenreeks | Het niveau van de diagnostische Logboeken. Stel deze eigenschap in op ' informatief ' | `Informational` |
|operationName| Tekenreeks |De naam van de pijp lijn met de status. "Pijp lijn-geslaagd" met eind status wanneer de pijplijn uitvoering is voltooid| `MyPipeline - Succeeded` |
|pipelineName| Reeks | Naam van de pijp lijn | `MyPipeline` |
|start| Reeks | Begin van de uitvoering van de activiteit in time span, UTC-indeling | `2017-06-26T20:55:29.5007959Z`|
|end| Tekenreeks | Het einde van de activiteit wordt uitgevoerd in time span, UTC-indeling. Als de activiteit nog niet is beëindigd (diagnostisch logboek voor het starten van een activiteit), is de `1601-01-01T00:00:00Z` standaard waarde van is ingesteld.  | `2017-06-26T20:55:29.5007959Z` |
|status| Tekenreeks | De uiteindelijke status van de pijplijn uitvoering (geslaagd of mislukt) | `Succeeded`|

#### <a name="trigger-run-logs-attributes"></a>Kenmerken van run logs van trigger

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "triggerId":"",
   "resourceId":"",
   "category":"TriggerRuns",
   "level":"Informational",
   "operationName":"",
   "triggerName":"",
   "triggerType":"",
   "triggerEvent":"",
   "start":"",
   "status":"",
   "properties":
   {
      "Parameters": {
        "TriggerTime": "",
       "ScheduleTime": ""
      },
      "SystemParameters": {}
    }
}

```

| Eigenschap | Type | Description | Voorbeeld |
| --- | --- | --- | --- |
| Niveau |Tekenreeks | Het niveau van de diagnostische Logboeken. Ingesteld op niveau 4 voor het uitvoeren van activiteiten Logboeken. | `4`  |
| correlationId |Tekenreeks | Unieke ID voor het bijhouden van end-to-end van een bepaalde aanvraag | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | Tekenreeks | Tijd van de gebeurtenis in time span, UTC-notatie`YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|triggerId| Reeks| ID van de trigger uitvoering | `08587023010602533858661257311` |
|resourceId| Tekenreeks | De bijbehorende resource-ID voor de data factory resource | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| Reeks | De categorie van Diagnostische logboeken. Stel deze eigenschap in op ' PipelineRuns ' | `PipelineRuns` |
|level| Tekenreeks | Het niveau van de diagnostische Logboeken. Stel deze eigenschap in op ' informatief ' | `Informational` |
|operationName| Tekenreeks |De naam van de trigger met de definitieve status of deze is gestart. "MyTrigger-geslaagd" als de heartbeat is geslaagd| `MyTrigger - Succeeded` |
|triggerName| Tekenreeks | De naam van de trigger | `MyTrigger` |
|triggerType| Tekenreeks | Het type van de trigger (hand matige trigger of plannings trigger) | `ScheduleTrigger` |
|triggerEvent| Tekenreeks | Gebeurtenis van de trigger | `ScheduleTime - 2017-07-06T01:50:25Z` |
|start| Tekenreeks | Starten van trigger geactiveerd in time span, UTC-indeling | `2017-06-26T20:55:29.5007959Z`|
|status| Tekenreeks | De uiteindelijke status van het feit of de trigger is geactiveerd (geslaagd of mislukt) | `Succeeded`|

### <a name="log-analytics-schema"></a>Log Analytics schema

Log Analytics neemt het schema over van Azure Monitor met de volgende uitzonde ringen:

* De eerste letter van elke kolom naam wordt gekapitaliseerd, omdat de *correlatie* van instantie in azure monitor een *correlationid* is in log Analytics.
* Het kolom *niveau* wordt verwijderd.
* De *Eigenschappen* van de dynamische kolom blijven behouden als het onderstaande dynamische JSON-BLOB-type:

    | Azure Monitor kolom | Log Analytics kolom | type |
    | --- | --- | --- |
    | $. Properties. UserProperties | UserProperties | Dynamisch |
    | $. Properties. Aantekeningen | Aantekeningen | Dynamisch |
    | $. Properties. Ingevoerd | Invoer | Dynamisch |
    | $. Properties. Uitvoer | Output | Dynamisch |
    | $. Properties. Fout. error code | Code | int |
    | $. Properties. Fout. Message | ErrorMessage | string |
    | $. Properties. Optreedt | Fout | Dynamisch |
    | $. Properties. Voorgangers dikwijls werden | Voorgangers dikwijls werden | Dynamisch |
    | $. Properties. Instellen | Parameters | Dynamisch |
    | $. Properties. SystemParameters | SystemParameters | Dynamisch |
    | $. Properties. Koptags | Labels | Dynamisch |
    
## <a name="metrics"></a>Metrische gegevens

Met Azure Monitor kunt u telemetrie gebruiken om inzicht te krijgen in de prestaties en status van uw workloads op Azure. Het belangrijkste type Azure-telemetriegegevens is de metrische gegevens (ook wel prestatie meters genoemd) die worden verzonden door de meeste Azure-resources. Azure Monitor biedt verschillende manieren om deze metrische gegevens te configureren en te gebruiken voor bewaking en probleem oplossing.

ADFV2 verzendt de volgende metrische gegevens:

| **Gegevens**           | **Weergave naam voor metrische gegevens**         | **Teleenheid** | **Aggregatie type** | **Beschrijving**                                       |
|----------------------|---------------------------------|----------|----------------------|-------------------------------------------------------|
| PipelineSucceededRuns | Metrische uitvoerings metingen geslaagde pijp lijnen | Count    | Totaal                | Het totale aantal uitgevoerde pijp lijnen is voltooid binnen een minuut venster |
| PipelineFailedRuns   | Metrische gegevens van mislukte pijplijn uitvoeringen    | Count    | Totaal                | Het totale aantal uitgevoerde pijp lijnen is mislukt binnen een minuut venster    |
| ActivitySucceededRuns | Metrische gegevens uitvoeringen uitgevoerde activiteit | Count    | Totaal                | Het totale aantal uitvoeringen van de activiteit is voltooid binnen een minuut venster  |
| ActivityFailedRuns   | Metrische gegevens mislukte uitvoering van activiteit    | Count    | Totaal                | Het totale aantal uitvoeringen van de activiteit is mislukt binnen een minuut venster     |
| TriggerSucceededRuns | Meet waarden voor uitvoering van geslaagde triggers  | Count    | Totaal                | Totaal aantal uitvoeringen van de trigger is voltooid binnen een minuut venster   |
| TriggerFailedRuns    | Meet waarden voor uitvoering van mislukte triggers     | Count    | Totaal                | Totaal aantal uitgevoerde triggers is mislukt binnen een minuut venster      |

Volg de instructies in [Azure monitor data platform](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics)om toegang te krijgen tot de metrische gegevens.

## <a name="monitor-data-factory-metrics-with-azure-monitor"></a>Data Factory metrische gegevens bewaken met Azure Monitor

U kunt Azure Data Factory-integratie met Azure Monitor gebruiken om gegevens naar Azure Monitor te routeren. Deze integratie is handig in de volgende scenario's:

1.  U wilt complexe query's schrijven op een uitgebreide set metrische gegevens die door Data Factory naar Azure Monitor worden gepubliceerd. U kunt ook aangepaste waarschuwingen voor deze query's maken via Azure Monitor.

2.  U wilt controleren op gegevens fabrieken. U kunt gegevens van meerdere gegevens fabrieken naar een enkele Azure Monitor-werk ruimte routeren.

Bekijk de volgende video voor een inleiding en demonstratie van zeven minuten voor deze functie:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

### <a name="configure-diagnostic-settings-and-workspace"></a>Diagnostische instellingen en werk ruimte configureren

Schakel de diagnostische instellingen voor uw data factory in.

1. Navigeer in de portal naar Azure Monitor en klik op **Diagnostische instellingen** in het menu **instellingen** .

2. Selecteer de data factory waarvoor u een diagnostische instelling wilt instellen.
    
3. Als er geen instellingen bestaan op de data factory die u hebt geselecteerd, wordt u gevraagd een instelling te maken. Klik op "Diagnostische gegevens inschakelen."

   ![Diagnostische instelling - er zijn geen bestaande instellingen toevoegen](media/data-factory-monitor-oms/monitor-oms-image1.png)

   Als er bestaande instellingen zijn op de data factory, ziet u een lijst met instellingen die al zijn geconfigureerd op deze data factory. Klik op 'Diagnostische instelling toevoegen'.

   ![Diagnostische instelling - bestaande instellingen toevoegen](media/data-factory-monitor-oms/add-diagnostic-setting.png)

4. Geef een naam op voor uw instelling en schakel het selectie vakje **verzenden naar log Analytics**in en selecteer vervolgens een log Analytics werk ruimte.

    ![monitor-oms-image2.png](media/data-factory-monitor-oms/monitor-oms-image2.png)

5. Klik op **Opslaan**.

Na enkele ogen blikken wordt de nieuwe instelling weer gegeven in de lijst met instellingen voor deze data factory en worden Diagnostische logboeken gestreamd naar die werk ruimte zodra er nieuwe gebeurtenis gegevens worden gegenereerd. Er kan tot vijf tien minuten duren tussen het moment dat een gebeurtenis wordt verzonden en wanneer deze wordt weer gegeven in Log Analytics.

> [!NOTE]
> Vanwege een expliciete limiet van een bepaalde Azure-logboek tabel met meer dan 500 kolommen, **is het raadzaam om de resource-specifieke modus te gebruiken**. Zie [log Analytics bekende beperkingen](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-stream-log-store#known-limitation-column-limit-in-azurediagnostics)voor meer informatie.

### <a name="install-azure-data-factory-analytics-from-azure-marketplace"></a>Azure Data Factory Analytics installeren vanuit Azure Marketplace

![monitor-oms-image3.png](media/data-factory-monitor-oms/monitor-oms-image3.png)

![monitor-oms-image4.png](media/data-factory-monitor-oms/monitor-oms-image4.png)

Klik op **maken** en selecteer de werk ruimte-en werkruimte instellingen.

![monitor-oms-image5.png](media/data-factory-monitor-oms/monitor-oms-image5.png)

### <a name="monitor-data-factory-metrics"></a>Data Factory metrische gegevens bewaken

Als u **Azure Data Factory Analytics** installeert, wordt er een standaardset weer gaven gemaakt waarmee de volgende metrische gegevens worden ingeschakeld:

- ADF-uitvoeringen-1) pijplijn uitvoeringen door Data Factory

- ADF-uitvoeringen-2) uitvoering van activiteit door Data Factory

- ADF-uitvoeringen-3) activering wordt uitgevoerd door Data Factory

- ADF-fouten-1) Top 10 van pijplijn fouten per Data Factory

- ADF-fouten-2) de tien uitvoering van de activiteit wordt uitgevoerd door Data Factory

- ADF-fouten-3) de tien belangrijkste trigger fouten door Data Factory

- ADF-statistieken-1) uitvoering van activiteit per type

- ADF-statistieken-2) trigger uitvoeringen per type

- ADF-statistieken-3) maximale duur van pijplijn uitvoeringen

![monitor-oms-image6.png](media/data-factory-monitor-oms/monitor-oms-image6.png)

![monitor-oms-image7.png](media/data-factory-monitor-oms/monitor-oms-image7.png)

U kunt de bovenstaande metrische gegevens visualiseren, de query's achter deze metrische gegevens bekijken, de query's bewerken, waarschuwingen maken enzovoort.

![monitor-oms-image8.png](media/data-factory-monitor-oms/monitor-oms-image8.png)

## <a name="alerts"></a>Waarschuwingen

Meld u aan bij de Azure Portal en klik op**waarschuwingen** **controleren** > om waarschuwingen te maken.

![Waarschuwingen in het menu van de portal](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>Waarschuwingen maken

1.  Klik op **+ nieuwe waarschuwings regel** om een nieuwe waarschuwing te maken.

    ![Nieuwe waarschuwings regel](media/monitor-using-azure-monitor/alerts_image4.png)

2.  Definieer de **waarschuwings voorwaarde**.

    > [!NOTE]
    > Zorg ervoor dat u **alle** selecteert in het **resource type filteren op**.

    ![Waarschuwings voorwaarde, scherm 1 van 3](media/monitor-using-azure-monitor/alerts_image5.png)

    ![Waarschuwings voorwaarde, scherm 2 van 3](media/monitor-using-azure-monitor/alerts_image6.png)

    ![Waarschuwings voorwaarde, scherm 3 van 3](media/monitor-using-azure-monitor/alerts_image7.png)

3.  Definieer de **Details**van de waarschuwing.

    ![Meldingsdetails](media/monitor-using-azure-monitor/alerts_image8.png)

4.  Definieer de **actie groep**.

    ![Actie groep, scherm 1 van 4](media/monitor-using-azure-monitor/alerts_image9.png)

    ![Actie groep, scherm 2 van 4](media/monitor-using-azure-monitor/alerts_image10.png)

    ![Actie groep, scherm 3 van 4](media/monitor-using-azure-monitor/alerts_image11.png)

    ![Actie groep, scherm 4 van 4](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="next-steps"></a>Volgende stappen

Zie [pijp lijnen programmatisch bewaken en beheren](monitor-programmatically.md) voor meer informatie over het bewaken en beheren van pijp lijnen met code.
