---
title: Gegevensfabrieken bewaken met Azure Monitor
description: Meer informatie over het gebruik van Azure Monitor om /Azure Data Factory-pijplijnen te monitoren door diagnostische logboeken in te schakelen met informatie uit Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/11/2018
ms.openlocfilehash: 5753336eeef115038de4eb0b5ade0651b1fa293e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419456"
---
# <a name="alert-and-monitor-data-factories-by-using-azure-monitor"></a>Gegevensfabrieken waarschuwen en bewaken met Azure Monitor

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Cloudapplicaties zijn complex en hebben veel bewegende onderdelen. Monitoren bieden gegevens om ervoor te zorgen dat uw toepassingen in een gezonde staat aan de gang blijven. Monitoren helpen u ook potentiële problemen te voorkomen en problemen op te lossen in het verleden.

U monitoringgegevens gebruiken om diepgaande inzichten te krijgen over uw toepassingen. Deze kennis helpt u bij het verbeteren van applicatieprestaties en onderhoudbaarheid. Het helpt u ook acties te automatiseren waarvoor anders handmatige interventie vereist is.

Azure Monitor biedt infrastructuurstatistieken op basisniveau en logboeken voor de meeste Azure-services. Azure diagnostische logboeken worden uitgestoten door een resource en bieden uitgebreide, frequente gegevens over de werking van die bron. En Azure Data Factory schrijft diagnostische logboeken in Monitor.

Zie overzicht [van Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor)voor meer informatie.

## <a name="keeping-azure-data-factory-data"></a>Azure Data Factory-gegevens behouden

Data Factory slaat gegevens voor pijplijnuitvoering op voor slechts 45 dagen. Gebruik Monitor als u die gegevens langer wilt bewaren. Met Monitor u diagnostische logboeken routeren voor analyse. U ze ook in een opslagaccount bewaren, zodat u fabrieksinformatie hebt voor de door u gekozen duur.

## <a name="diagnostic-logs"></a>Diagnostische logboeken

* Sla uw diagnostische logboeken op in een opslagaccount voor controle of handmatige inspectie. U de diagnostische instellingen gebruiken om de bewaartijd in dagen op te geven.
* Stream de logboeken naar Azure Event Hubs. De logboeken worden input voor een partnerservice of voor een aangepaste analyseoplossing zoals Power BI.
* Analyseer de logboeken met Log Analytics.

U een opslagaccount of naamruimte voor gebeurtenis-hub gebruiken die niet in het abonnement van de bron zit die logboeken uitzendt. De gebruiker die de instelling configureert, moet toegang hebben tot beide abonnementen (RBAC) en moet toegang hebben tot het juiste op rollen gebaseerde toegangscontrole(RBAC).

## <a name="set-up-diagnostic-logs"></a>Diagnostische logboeken instellen

### <a name="diagnostic-settings"></a>Diagnostische instellingen

Gebruik diagnostische instellingen om diagnostische logboeken te configureren voor niet-berekende resources. De instellingen voor een resourcebesturingselement hebben de volgende functies:

* Ze geven aan waar diagnostische logboeken worden verzonden. Voorbeelden hiervan zijn een Azure-opslagaccount, een Azure-gebeurtenishub of Monitorlogboeken.
* Zij geven aan welke logcategorieën worden verzonden.
* Ze geven aan hoe lang elke logboekcategorie in een opslagaccount moet worden bewaard.
* Een waarde van nul voor de optie Retentie houdt in dat logboeken permanent worden bewaard. Anders kan de waarde een willekeurig aantal dagen zijn van 1 tot en met 2.147.483.647.
* Als bewaarbeleid is ingesteld, maar het opslaan van logboeken in een opslagaccount is uitgeschakeld, heeft het bewaarbeleid geen effect. Deze voorwaarde kan bijvoorbeeld optreden wanneer alleen gebeurtenishubs of monitorlogboekenzijn geselecteerd.
* Bewaarbeleid wordt per dag toegepast. De grens tussen de dagen vindt plaats om middernacht Gecoördineerde Universele Tijd (UTC). Aan het einde van een dag worden logboeken van dagen die verder gaan dan het bewaarbeleid verwijderd. Als u bijvoorbeeld een bewaarbeleid van één dag hebt, worden aan het begin van vandaag de logboeken van voor gisteren verwijderd.

### <a name="enable-diagnostic-logs-via-the-azure-monitor-rest-api"></a>Diagnostische logboeken inschakelen via de Azure Monitor REST API

#### <a name="create-or-update-a-diagnostics-setting-in-the-monitor-rest-api"></a>Een diagnostische instelling maken of bijwerken in de Monitor REST API

##### <a name="request"></a>Aanvraag

```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>Headers

* Vervang `{api-version}` door `2016-09-01`.
* Vervang `{resource-id}` de id van de resource waarvoor u diagnostische instellingen wilt bewerken. Zie [Resourcegroepen gebruiken om uw Azure-bronnen te beheren voor](../azure-resource-manager/management/manage-resource-groups-portal.md)meer informatie.
* Stel `Content-Type` de `application/json`koptekst in op .
* Stel de autorisatiekop in op het JSON-webtoken dat u hebt gekregen van Azure Active Directory (Azure AD). Zie [Aanvragen verifiëren voor](../active-directory/develop/authentication-scenarios.md)meer informatie.

##### <a name="body"></a>Hoofdtekst

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

| Eigenschap | Type | Beschrijving |
| --- | --- | --- |
| **storageAccountId** |Tekenreeks | De bron-id van het opslagaccount waarnaar u diagnostische logboeken wilt verzenden. |
| **serviceBusRuleId** |Tekenreeks | De service-busregel-ID van de service-busnaamruimte waarin u gebeurtenishubs wilt laten maken voor het streamen van diagnostische logboeken. De regel-ID `{service bus resource ID}/authorizationrules/{key name}`heeft de indeling .|
| **workspaceId** | Complex Type | Een reeks metrische tijdkorrels en hun bewaarbeleid. De waarde van deze accommodatie is leeg. |
|**Statistieken**| Parameterwaarden van de pijplijnrun die moeten worden doorgegeven aan de aangeroepen pijplijn| Een JSON-object dat parameternamen inkaart aan argumentwaarden. |
| **Logs**| Complex Type| De naam van een categorie diagnostische logboeken voor een resourcetype. Als u de lijst met diagnostische logboekcategorieën voor een resource wilt krijgen, voert u een bewerking voor diagnostische instellingen van GET uit. |
| **Categorie**| Tekenreeks| Een reeks logboekcategorieën en hun bewaarbeleid. |
| **timeGrain** | Tekenreeks | De granulariteit van metrische gegevens, die worden vastgelegd in de duurnotatie ISO 8601. De eigenschapswaarde `PT1M`moet zijn , die één minuut specificeert. |
| **Ingeschakeld**| Booleaans | Hiermee geeft u op of verzameling van de metrische of logboekcategorie is ingeschakeld voor deze resource. |
| **retentieBeleid**| Complex Type| Beschrijft het bewaarbeleid voor een metrische of logboekcategorie. Deze eigenschap wordt alleen gebruikt voor opslagaccounts. |
|**Dagen**| Int| Het aantal dagen om de statistieken of logboeken bij te houden. Als de eigenschapswaarde 0 is, worden de logboeken voor altijd bewaard. Deze eigenschap wordt alleen gebruikt voor opslagaccounts. |

##### <a name="response"></a>Antwoord

200 oké.


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

#### <a name="get-information-about-diagnostics-settings-in-the-monitor-rest-api"></a>Informatie over diagnostische instellingen in de Monitor REST API

##### <a name="request"></a>Aanvraag

```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>Headers

* Vervang `{api-version}` door `2016-09-01`.
* Vervang `{resource-id}` de id van de resource waarvoor u diagnostische instellingen wilt bewerken. Zie [Resourcegroepen gebruiken om uw Azure-bronnen te beheren voor](../azure-resource-manager/management/manage-resource-groups-portal.md)meer informatie.
* Stel `Content-Type` de `application/json`koptekst in op .
* Stel de autorisatiekop in op een JSON-webtoken dat u hebt gekregen van Azure AD. Zie [Aanvragen verifiëren voor](../active-directory/develop/authentication-scenarios.md)meer informatie.

##### <a name="response"></a>Antwoord

200 oké.

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
Zie [Diagnostische instellingen voor](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)meer informatie .

## <a name="schema-of-logs-and-events"></a>Schema van logboeken en gebeurtenissen

### <a name="monitor-schema"></a>Monitorschema

#### <a name="activity-run-log-attributes"></a>Logboekkenmerken voor activiteitsuitvoeren

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

| Eigenschap | Type | Beschrijving | Voorbeeld |
| --- | --- | --- | --- |
| **Niveau** |Tekenreeks | Het niveau van de diagnostische logboeken. Stel de eigenschapswaarde in voor logboeken die worden uitgevoerd op 4. | `4` |
| **correlationId** |Tekenreeks | De unieke ID voor het bijhouden van een bepaald verzoek. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **Tijd** | Tekenreeks | De tijd van de gebeurtenis in `YYYY-MM-DDTHH:MM:SS.00000Z`de timespan UTC-indeling . | `2017-06-28T21:00:27.3534352Z` |
|**activiteitRunId**| Tekenreeks| De id van de activiteit wordt uitgevoerd. | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|**pipelineRunId**| Tekenreeks| De ID van de pijplijn wordt uitgevoerd. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**Resourceid**| Tekenreeks | De id die is gekoppeld aan de bron gegevensfabriek. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**Categorie**| Tekenreeks | De categorie van de diagnostische logboeken. Stel de waarde `ActivityRuns`van de eigenschap in op . | `ActivityRuns` |
|**Niveau**| Tekenreeks | Het niveau van de diagnostische logboeken. Stel de waarde `Informational`van de eigenschap in op . | `Informational` |
|**operationName**| Tekenreeks | De naam van de activiteit met zijn status. Als de activiteit de startheartbeat is, is `MyActivity -`de eigenschapswaarde . Als de activiteit de eindhartslag is, is `MyActivity - Succeeded`de eigenschapswaarde . | `MyActivity - Succeeded` |
|**pipelineName**| Tekenreeks | De naam van de pijpleiding. | `MyPipeline` |
|**activiteitNaam**| Tekenreeks | De naam van de activiteit. | `MyActivity` |
|**Start**| Tekenreeks | De begintijd van de activiteit wordt uitgevoerd in de UTC-indeling tijdspanne. | `2017-06-26T20:55:29.5007959Z`|
|**Einde**| Tekenreeks | De eindtijd van de activiteit wordt uitgevoerd in de UTC-indeling tijdspanne. Als uit het diagnoselogboek blijkt dat een activiteit is `1601-01-01T00:00:00Z`gestart maar nog niet is beëindigd, is de eigenschapswaarde . | `2017-06-26T20:55:29.5007959Z` |

#### <a name="pipeline-run-log-attributes"></a>Logboekkenmerken voor pijplijnuitvoering

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

| Eigenschap | Type | Beschrijving | Voorbeeld |
| --- | --- | --- | --- |
| **Niveau** |Tekenreeks | Het niveau van de diagnostische logboeken. Stel de eigenschapswaarde in voor logboeken die worden uitgevoerd op 4. | `4` |
| **correlationId** |Tekenreeks | De unieke ID voor het bijhouden van een bepaald verzoek. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **Tijd** | Tekenreeks | De tijd van de gebeurtenis in `YYYY-MM-DDTHH:MM:SS.00000Z`de timespan UTC-indeling . | `2017-06-28T21:00:27.3534352Z` |
|**runId**| Tekenreeks| De ID van de pijplijn wordt uitgevoerd. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**Resourceid**| Tekenreeks | De id die is gekoppeld aan de bron gegevensfabriek. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**Categorie**| Tekenreeks | De categorie van de diagnostische logboeken. Stel de waarde `PipelineRuns`van de eigenschap in op . | `PipelineRuns` |
|**Niveau**| Tekenreeks | Het niveau van de diagnostische logboeken. Stel de waarde `Informational`van de eigenschap in op . | `Informational` |
|**operationName**| Tekenreeks | De naam van de pijplijn samen met de status ervan. Nadat de pijplijnrun is voltooid, `Pipeline - Succeeded`is de eigenschapswaarde . | `MyPipeline - Succeeded`. |
|**pipelineName**| Tekenreeks | De naam van de pijpleiding. | `MyPipeline` |
|**Start**| Tekenreeks | De begintijd van de activiteit wordt uitgevoerd in de UTC-indeling tijdspanne. | `2017-06-26T20:55:29.5007959Z`. |
|**Einde**| Tekenreeks | De eindtijd van de activiteit wordt uitgevoerd in de UTC-indeling tijdspanne. Als in het diagnoselogboek wordt weergegeven dat een activiteit `1601-01-01T00:00:00Z`is gestart maar nog niet is beëindigd, is de eigenschapswaarde .  | `2017-06-26T20:55:29.5007959Z` |
|**Status**| Tekenreeks | De uiteindelijke status van de pijplijn wordt uitgevoerd. Mogelijke eigenschapswaarden zijn `Succeeded` en `Failed`. | `Succeeded`|

#### <a name="trigger-run-log-attributes"></a>Logboekkenmerken voor trigger-run

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

| Eigenschap | Type | Beschrijving | Voorbeeld |
| --- | --- | --- | --- |
| **Niveau** |Tekenreeks | Het niveau van de diagnostische logboeken. Stel de eigenschapswaarde in voor logboeken die worden uitgevoerd op 4. | `4` |
| **correlationId** |Tekenreeks | De unieke ID voor het bijhouden van een bepaald verzoek. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **Tijd** | Tekenreeks | De tijd van de gebeurtenis in `YYYY-MM-DDTHH:MM:SS.00000Z`de timespan UTC-indeling . | `2017-06-28T21:00:27.3534352Z` |
|**triggerId**| Tekenreeks| De ID van de trekker loopt. | `08587023010602533858661257311` |
|**Resourceid**| Tekenreeks | De id die is gekoppeld aan de bron gegevensfabriek. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**Categorie**| Tekenreeks | De categorie van de diagnostische logboeken. Stel de waarde `PipelineRuns`van de eigenschap in op . | `PipelineRuns` |
|**Niveau**| Tekenreeks | Het niveau van de diagnostische logboeken. Stel de waarde `Informational`van de eigenschap in op . | `Informational` |
|**operationName**| Tekenreeks | De naam van de trigger met de uiteindelijke status, die aangeeft of de trigger met succes is afgevuurd. Als de hartslag is geslaagd, `MyTrigger - Succeeded`is de waarde van de eigenschap . | `MyTrigger - Succeeded` |
|**triggerName**| Tekenreeks | De naam van de trekker. | `MyTrigger` |
|**triggerType**| Tekenreeks | Het type van de trekker. Mogelijke eigenschapswaarden zijn `Manual Trigger` en `Schedule Trigger`. | `ScheduleTrigger` |
|**triggerEvent**| Tekenreeks | Het geval van de trekker. | `ScheduleTime - 2017-07-06T01:50:25Z` |
|**Start**| Tekenreeks | De begintijd van de trigger wordt geactiveerd in de UTC-indeling tijdspanne. | `2017-06-26T20:55:29.5007959Z`|
|**Status**| Tekenreeks | De uiteindelijke status die aangeeft of de trigger is geactiveerd. Mogelijke eigenschapswaarden zijn `Succeeded` en `Failed`. | `Succeeded`|

### <a name="log-analytics-schema"></a>Log Analytics-schema

Log Analytics neemt het schema over van Monitor met de volgende uitzonderingen:

* De eerste letter in elke kolomnaam wordt gekapitaliseerd. De kolomnaam 'correlationId' in Monitor is bijvoorbeeld 'CorrelationId' in Log Analytics.
* Er is geen kolom 'Niveau'.
* De dynamische kolom 'eigenschappen' blijft behouden als het volgende dynamische JSON-blobtype.

    | Kolom Azure Monitor | Kolom Log Analytics | Type |
    | --- | --- | --- |
    | $.eigenschappen. Userproperties | Userproperties | Dynamisch |
    | $.eigenschappen. Aantekeningen | Aantekeningen | Dynamisch |
    | $.eigenschappen. Input | Invoer | Dynamisch |
    | $.eigenschappen. Output | Uitvoer | Dynamisch |
    | $.eigenschappen. Error.errorCode | ErrorCode | int |
    | $.eigenschappen. Fout.bericht | Errormessage | tekenreeks |
    | $.eigenschappen. Fout | Fout | Dynamisch |
    | $.eigenschappen. Voorgangers | Voorgangers | Dynamisch |
    | $.eigenschappen. Parameters | Parameters | Dynamisch |
    | $.eigenschappen. Systeemparameters | Systeemparameters | Dynamisch |
    | $.eigenschappen. Tags | Tags | Dynamisch |
    
## <a name="metrics"></a>Metrische gegevens

Met Monitor krijgt u inzicht in de prestaties en status van uw Azure-workloads. Het belangrijkste type monitorgegevens is de statistiek, die ook wel het prestatiemeterteller wordt genoemd. Statistieken worden door de meeste Azure-bronnen uitgestoten. Monitor biedt verschillende manieren om deze statistieken te configureren en te gebruiken voor monitoring en probleemoplossing.

Azure Data Factory versie 2 zendt de volgende statistieken uit.

| **Gegevens**           | **Metrische weergavenaam**         | **Eenheid** | **Aggregatietype** | **Beschrijving**                                       |
|----------------------|---------------------------------|----------|----------------------|-------------------------------------------------------|
| PipelineSucceededRuns | Opgevolgd pijplijnvoert statistieken uit | Count    | Totaal                | Het totale aantal pijplijnuitvoeringen dat binnen een minuut is uitgevoerd. |
| Pijplijnmisluktloopt   | Mislukte pijplijn voert statistieken uit    | Count    | Totaal                | Het totale aantal pijplijnruns dat binnen een minuut is mislukt.    |
| ActivitySucceededRuns ActivitySucceededRuns ActivitySucceededRuns ActivitySucceed | Op geslaagde activiteit voert statistieken uit | Count    | Totaal                | Het totale aantal activiteitsruns dat binnen een minuut is gelukt.  |
| Activiteitmisluktloopt   | Mislukte activiteit voert statistieken uit    | Count    | Totaal                | Het totale aantal activiteitsruns dat binnen een minuut is mislukt.     |
| TriggerSucceededRuns | Geslaagde trigger voert statistieken uit  | Count    | Totaal                | Het totale aantal triggerruns dat binnen een minuut is gelukt.   |
| Triggerfailedruns    | Mislukte trigger voert statistieken uit     | Count    | Totaal                | Het totale aantal triggerruns dat binnen een minuut is mislukt.      |

Voer de instructies in [Azure Monitor-gegevensplatform](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics)in om toegang te krijgen tot de statistieken.

> [!NOTE]
> Alleen voltooide, geactiveerde activiteit en pijplijnuitvoeringen worden uitgestoten. In uitvoering en sandbox/debug-uitvoeringen worden **niet** uitgezonden. 

## <a name="monitor-data-factory-metrics-with-azure-monitor"></a>Gegevensfabrieksstatistieken bewaken met Azure Monitor

U de integratie van Data Factory met Monitor gebruiken om gegevens naar Monitor te leiden. Deze integratie is nuttig in de volgende scenario's:

* U wilt complexe query's schrijven op een uitgebreide set statistieken die door Data Factory wordt gepubliceerd om te controleren. U aangepaste waarschuwingen voor deze query's maken via Monitor.

* U wilt toezicht houden op alle datafabrieken. U gegevens van meerdere gegevensfabrieken doorsturen naar één monitorwerkruimte.

Voor een zeven minuten durende introductie en demonstratie van deze functie, bekijk de volgende video:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

### <a name="configure-diagnostic-settings-and-workspace"></a>Diagnostische instellingen en werkruimte configureren

Diagnostische instellingen voor uw gegevensfabriek maken of toevoegen.

1. Ga in het portaal naar Monitor. Selecteer **Diagnostische instellingen** > **instellingen**.

1. Selecteer de gegevensfabriek waarvoor u een diagnostische instelling wilt instellen.

1. Als er geen instellingen bestaan in de geselecteerde gegevensfabriek, wordt u gevraagd een instelling te maken. Selecteer **Diagnostische gegevens inschakelen**.

   ![Een diagnostische instelling maken als er geen instellingen bestaan](media/data-factory-monitor-oms/monitor-oms-image1.png)

   Als er bestaande instellingen zijn in de gegevensfabriek, ziet u een lijst met instellingen die al zijn geconfigureerd in de gegevensfabriek. Selecteer **Diagnostische instelling toevoegen**.

   ![Een diagnostische instelling toevoegen als er instellingen bestaan](media/data-factory-monitor-oms/add-diagnostic-setting.png)

1. Geef uw instelling een naam, selecteer **Verzenden naar logboekanalyses**en selecteer vervolgens een werkruimte in **Log Analytics Workspace**.

    ![Uw instellingen een naam geven en een werkruimte voor logboekanalyse selecteren](media/data-factory-monitor-oms/monitor-oms-image2.png)

1. Selecteer **Opslaan**.

Na enkele ogenblikken wordt de nieuwe instelling weergegeven in uw lijst met instellingen voor deze gegevensfabriek. Diagnostische logboeken worden naar die werkruimte gestreamd zodra nieuwe gebeurtenisgegevens worden gegenereerd. Er kunnen maximaal 15 minuten verstrijken tussen het moment waarop een gebeurtenis wordt uitgezonden en wanneer deze wordt weergegeven in Log Analytics.

* In de modus _Resourcespecifieke_ worden diagnostische logboeken van Azure Data Factory uitgevoerd in _ADFPipelineRun,_ _ADFTriggerRun_en _ADFActivityRun-tabellen_
* In de _diagnostische modus van Azure_ worden diagnostische logboeken geplaatst in de tabel _AzureDiagnostics_

> [!NOTE]
> Omdat een Azure-logboektabel niet meer dan 500 kolommen kan bevatten, raden we u ten zeerste aan de modus Resourcespecifiek te selecteren. Zie [Log Analytics Known Limitations](../azure-monitor/platform/resource-logs-collect-workspace.md#column-limit-in-azurediagnostics)voor meer informatie.

### <a name="install-azure-data-factory-analytics-from-azure-marketplace"></a>Azure Data Factory Analytics installeren vanuit Azure Marketplace

![Ga naar 'Azure Marketplace', typ 'Analytics-filter' en selecteer 'Azure Data Factory Analytics (Preview')](media/data-factory-monitor-oms/monitor-oms-image3.png)

![Details over "Azure Data Factory Analytics (Preview)"](media/data-factory-monitor-oms/monitor-oms-image4.png)

Selecteer **Maken** en selecteer **vervolgens OMS Workspace-** en **OMS-werkruimteinstellingen**.

![Een nieuwe oplossing creëren](media/data-factory-monitor-oms/monitor-oms-image5.png)

### <a name="monitor-data-factory-metrics"></a>Gegevensfabriekstatistieken controleren

Als u Azure Data Factory Analytics installeert, wordt een standaardset weergaven gemaakt, zodat de volgende statistieken worden ingeschakeld:

- ADF-uitvoeringen - 1) Pijplijnuitvoeringen door gegevensfabriek
 
- ADF-runs- 2) Activiteit wordt uitgevoerd door gegevensfabriek

- ADF-uitvoeringen - 3) Trigger Runs by Data Factory

- ADF-fouten - 1) Top 10 pijplijnfouten per gegevensfabriek

- ADF-fouten - 2) Top 10 activiteitsruns per gegevensfabriek

- ADF-fouten - 3) Top 10 Trigger fouten per gegevensfabriek

- ADF-statistieken - 1) Activiteit sert op type

- ADF-statistieken - 2) Trigger Runs op type

- ADF-statistieken - 3) Maximale pijplijnuitvoeringen

![Venster met 'Werkmappen (voorbeeld)' en 'AzureDataFactoryAnalytics' gemarkeerd](media/data-factory-monitor-oms/monitor-oms-image6.png)

U de voorgaande statistieken visualiseren, de query's achter deze statistieken bekijken, de query's bewerken, waarschuwingen maken en andere acties uitvoeren.

![Grafische weergave van pijplijnwordt uitgevoerd door gegevensfabriek"](media/data-factory-monitor-oms/monitor-oms-image8.png)

> [!NOTE]
> Azure Data Factory Analytics (Preview) verzendt diagnostische logboeken naar _resourcespecifieke_ doeltabellen. U query's schrijven op de volgende tabellen: _ADFPipelineRun,_ _ADFTriggerRun_en _ADFActivityRun_.

## <a name="alerts"></a>Waarschuwingen

Meld u aan bij de Azure-portal en selecteer > **Monitorwaarschuwingen** om waarschuwingen te maken. **Monitor**

![Waarschuwingen in het portalmenu](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>Waarschuwingen maken

1. Selecteer **+ Nieuwe waarschuwingsregel** om een nieuwe waarschuwing te maken.

    ![Nieuwe waarschuwingsregel](media/monitor-using-azure-monitor/alerts_image4.png)

1. Definieer de waarschuwingsvoorwaarde.

    > [!NOTE]
    > Zorg ervoor dat u **Alles** selecteert in de vervolgkeuzelijst **Filter op resourcetype.**

    !['Waarschuwingsvoorwaarde definiëren' > 'Doel selecteren', waarmee het deelvenster 'Een resource selecteren' wordt geopend ](media/monitor-using-azure-monitor/alerts_image5.png)

    !['Waarschuwingsvoorwaarde definiëren' > criteria toevoegen, waarmee het deelvenster 'Signaallogica configureren' wordt geopend](media/monitor-using-azure-monitor/alerts_image6.png)

    ![Deelvenster 'Signaaltype configureren'](media/monitor-using-azure-monitor/alerts_image7.png)

1. Definieer de waarschuwingsgegevens.

    ![Meldingsdetails](media/monitor-using-azure-monitor/alerts_image8.png)

1. Definieer de actiegroep.

    ![Een regel maken met de nadruk op 'Nieuwe actiegroep'.](media/monitor-using-azure-monitor/alerts_image9.png)

    ![Een nieuwe actiegroep maken](media/monitor-using-azure-monitor/alerts_image10.png)

    ![E-mail, sms, push en spraak configureren](media/monitor-using-azure-monitor/alerts_image11.png)

    ![Een actiegroep definiëren](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="next-steps"></a>Volgende stappen
[Pijplijnen programmatisch bewaken en beheren](monitor-programmatically.md)
