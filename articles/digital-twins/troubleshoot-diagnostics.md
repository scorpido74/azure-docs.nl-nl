---
title: Diagnostische gegevens instellen
titleSuffix: Azure Digital Twins
description: Zie logboek registratie met Diagnostische instellingen inschakelen.
author: baanders
ms.author: baanders
ms.date: 7/28/2020
ms.topic: troubleshooting
ms.service: digital-twins
ms.openlocfilehash: fc397b6d6beb719e11dc3959bbcf4d75c08a8dda
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88723925"
---
# <a name="troubleshooting-azure-digital-twins-diagnostics-logging"></a>Problemen oplossen met Azure Digital Apparaatdubbels: logboek registratie van diagnostische gegevens

Azure Digital Apparaatdubbels verzamelt [metrische](troubleshoot-metrics.md) gegevens voor uw service-exemplaar die informatie geven over de status van uw resources. U kunt deze metrische gegevens gebruiken om de algemene status van de Azure Digital Apparaatdubbels-service en de gekoppelde resources te beoordelen. Met deze gebruikers gerichte statistieken kunt u zien wat er gebeurt met uw Azure Digital Apparaatdubbels en kunt u de hoofd oorzaak van problemen analyseren zonder dat u contact hoeft op te nemen met de ondersteuning van Azure.

In dit artikel wordt beschreven hoe u **Diagnostische logboek registratie** inschakelt voor uw metrische gegevens uit uw Azure Digital apparaatdubbels-exemplaar. U kunt deze logboeken gebruiken om problemen met de service op te lossen en diagnostische instellingen configureren voor het verzenden van Azure Digital Apparaatdubbels-metrische gegevens naar verschillende bestemmingen. Meer informatie over deze instellingen vindt u in [*Diagnostische instellingen maken om platform logboeken en-metrische gegevens naar verschillende bestemmingen te verzenden*](../azure-monitor/platform/diagnostic-settings.md).

## <a name="turn-on-diagnostic-settings-with-the-azure-portal"></a>Diagnostische instellingen inschakelen met de Azure Portal

Hier vindt u informatie over het inschakelen van diagnostische instellingen voor uw Azure Digital Apparaatdubbels-exemplaar:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en navigeer naar uw Azure Digital apparaatdubbels-exemplaar. U kunt het vinden door de naam ervan in de zoek balk van de portal te typen. 

2. Selecteer **Diagnostische instellingen** in het menu en vervolgens **Diagnostische instelling toevoegen**.

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="Scherm opname van de pagina met Diagnostische instellingen en de knop die u wilt toevoegen":::

3. Vul op de onderstaande pagina de volgende waarden in:
     * **Naam van de diagnostische instelling**: Geef de diagnostische instellingen een naam.
     * **Categorie Details**: Kies welke bewerkingen u wilt bewaken en schakel de selectie vakjes in om diagnostische gegevens voor deze bewerkingen in te scha kelen. De bewerkingen waarvan de diagnostische instellingen kunnen rapporteren, zijn:
        - DigitalTwinsOperation
        - EventRoutesOperation
        - ModelsOperation
        - QueryOperation
        - AllMetrics
        
        Meer informatie over deze opties kunt u vinden in de sectie [*categorie gegevens*](#category-details) hieronder.
     * **Doel Details**: Kies waar u de logboeken wilt verzenden. U kunt een combi natie van de drie opties selecteren:
        - Verzenden naar Log Analytics
        - Archiveren naar een opslagaccount
        - Streamen naar een Event Hub

        U wordt mogelijk gevraagd aanvullende gegevens in te vullen als deze nodig zijn voor uw doel selectie.  
    
4. Sla de nieuwe instellingen op. 

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings-details.png" alt-text="Scherm opname van de pagina met Diagnostische instellingen en de knop die u wilt toevoegen":::

Nieuwe instellingen worden in ongeveer 10 minuten van kracht. Daarna worden logboeken weer gegeven in de geconfigureerde doel weer op de pagina **Diagnostische instellingen** voor uw exemplaar. 

## <a name="category-details"></a>Categorie Details

Hier vindt u meer informatie over de logboek categorieën die kunnen worden geselecteerd onder **categorie Details** bij het instellen van diagnostische instellingen.

| Logboekcategorie | Beschrijving |
| --- | --- |
| ADTModelsOperation | Alle API-aanroepen voor modellen vastleggen |
| ADTQueryOperation | Alle API-aanroepen met betrekking tot Query's vastleggen |
| ADTEventRoutesOperation | Registreer alle API-aanroepen die betrekking hebben op gebeurtenis routes en voor het uitvoeren van gebeurtenissen van Azure Digital Apparaatdubbels naar een eindpunt service, zoals Event Grid, Event Hubs en Service Bus |
| ADTDigitalTwinsOperation | Alle API-aanroepen vastleggen die betrekking hebben op Azure Digital Apparaatdubbels |

Elke logboek categorie bestaat uit bewerkingen van schrijven, lezen, verwijderen en actie.  Deze toewijzing REST API aanroepen als volgt:

| Gebeurtenistype | REST API bewerkingen |
| --- | --- |
| Schrijven | PUT and PATCH |
| Lezen | GET |
| Verwijderen | DELETE |
| Bewerking | POST |

Hier volgt een uitgebreide lijst met de bewerkingen en bijbehorende [Azure Digital apparaatdubbels rest API-aanroepen](https://docs.microsoft.com/rest/api/azure-digitaltwins/) die in elke categorie zijn geregistreerd. 

>[!NOTE]
> Elke logboek categorie bevat verschillende bewerkingen/REST API-aanroepen. In de onderstaande tabel wordt elke logboek categorie toegewezen aan alle bewerkingen/REST API-aanroepen, totdat de volgende logboek categorie wordt weer gegeven. 

| Logboekcategorie | Bewerking | REST API-aanroepen en andere gebeurtenissen |
| --- | --- | --- |
| ADTModelsOperation | Micro soft. DigitalTwins/modellen/schrijven | Update-API voor digitale dubbele modellen |
|  | Micro soft. DigitalTwins/modellen/lezen | Digitale twee modellen ontvangen op ID en lijst-Api's |
|  | Micro soft. DigitalTwins/modellen/verwijderen | Digitale dubbele modellen API verwijderen |
|  | Micro soft. DigitalTwins/modellen/actie | Digitale dubbele modellen API toevoegen |
| ADTQueryOperation | Micro soft. DigitalTwins/query/actie | Query Apparaatdubbels-API |
| ADTEventRoutesOperation | Micro soft. DigitalTwins/eventroutes/schrijven | Gebeurtenis routes toevoegen API |
|  | Micro soft. DigitalTwins/eventroutes/lezen | Gebeurtenis routes ophalen op basis van ID en lijst-Api's |
|  | Micro soft. DigitalTwins/eventroutes/verwijderen | Gebeurtenis routes verwijderen API |
|  | Micro soft. DigitalTwins/eventroutes/actie | Fout bij het publiceren van gebeurtenissen naar een eindpunt service (geen API-aanroep) |
| ADTDigitalTwinsOperation | Micro soft. DigitalTwins/DigitalTwins/schrijven | Digitale Apparaatdubbels toevoegen, relatie toevoegen, bijwerken, onderdeel bijwerken |
|  | Micro soft. DigitalTwins/DigitalTwins/lezen | Digital Apparaatdubbels get by ID, component ophalen, relatie ophalen op ID, binnenkomende relaties weer geven, lijst relaties |
|  | Micro soft. DigitalTwins/DigitalTwins/verwijderen | Digitale Apparaatdubbels verwijderen, relatie verwijderen |
|  | Micro soft. DigitalTwins/DigitalTwins/actie | Digital Apparaatdubbels, telemetrie van het onderdeel verzenden, telemetrie verzenden |

## <a name="log-schemas"></a>Logboek schema's 

Elke logboek categorie heeft een schema dat definieert hoe gebeurtenissen in die categorie worden gerapporteerd. Elke afzonderlijke logboek vermelding wordt als tekst opgeslagen en opgemaakt als een JSON-blob. De velden in de logboeken en voor beeld-JSON-instanties worden aangegeven voor elk logboek type hieronder. 

`ADTDigitalTwinsOperation`, `ADTModelsOperation` en `ADTQueryOperation` gebruik een consistent API-logboek schema; `ADTEventRoutesOperation` heeft een eigen afzonderlijk schema.

### <a name="api-log-schemas"></a>API-logboek schema's

Dit logboek schema is consistent voor `ADTDigitalTwinsOperation` , `ADTModelsOperation` en `ADTQueryOperation` . Het bevat informatie die betrekking heeft op API-aanroepen naar een Azure Digital Apparaatdubbels-exemplaar.

Dit zijn de omschrijvingen van velden en eigenschappen voor API-Logboeken.

| Veldnaam | Gegevenstype | Beschrijving |
|-----|------|-------------|
| `Time` | DateTime | De datum en tijd waarop deze gebeurtenis is opgetreden, in UTC |
| `ResourceID` | Tekenreeks | De Azure Resource Manager Resource-ID voor de resource waar het evenement plaatsvond |
| `OperationName` | Tekenreeks  | Het type actie dat wordt uitgevoerd tijdens de gebeurtenis |
| `OperationVersion` | Tekenreeks | De API-versie die wordt gebruikt tijdens de gebeurtenis |
| `Category` | Tekenreeks | Het type bron dat wordt verzonden |
| `ResultType` | Tekenreeks | Resultaat van de gebeurtenis |
| `ResultSignature` | Tekenreeks | Http-status code voor de gebeurtenis |
| `ResultDescription` | Tekenreeks | Aanvullende informatie over de gebeurtenis |
| `DurationMs` | Tekenreeks | Hoe lang het duurt om de gebeurtenis in milliseconden uit te voeren |
| `CallerIpAddress` | Tekenreeks | Een gemaskeerde IP-adres van de bron voor de gebeurtenis |
| `CorrelationId` | Guid | Door de klant ingevoerde unieke id voor de gebeurtenis |
| `Level` | Tekenreeks | De ernst van de logboek registratie van de gebeurtenis |
| `Location` | Tekenreeks | De regio waar het evenement plaatsvond |
| `RequestUri` | URI | Het eind punt dat wordt gebruikt tijdens de gebeurtenis |

Hieronder ziet u voor beelden van JSON-instanties voor deze typen logboeken.

#### <a name="adtdigitaltwinsoperation"></a>ADTDigitalTwinsOperation

```json
{
  "time": "2020-03-14T21:11:14.9918922Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/digitaltwins/write",
  "operationVersion": "2020-05-31-preview",
  "category": "DigitalTwinOperation",
  "resultType": "Success",
  "resultSignature": "200",
  "resultDescription": "",
  "durationMs": "314",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "2f6a8e64-94aa-492a-bc31-16b9f0b16ab3",
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/digitaltwins/factory-58d81613-2e54-4faa-a930-d980e6e2a884?api-version=2020-05-31-preview"
}
```

#### <a name="adtmodelsoperation"></a>ADTModelsOperation

```json
{
  "time": "2020-10-29T21:12:24.2337302Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/models/write",
  "operationVersion": "2020-05-31-preview",
  "category": "ModelsOperation",
  "resultType": "Success",
  "resultSignature": "201",
  "resultDescription": "",
  "durationMs": "935",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "9dcb71ea-bb6f-46f2-ab70-78b80db76882",
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/Models?api-version=2020-05-31-preview",
}
```

#### <a name="adtqueryoperation"></a>ADTQueryOperation

```json
{
  "time": "2020-12-04T21:11:44.1690031Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/query/action",
  "operationVersion": "2020-05-31-preview",
  "category": "QueryOperation",
  "resultType": "Success",
  "resultSignature": "200",
  "resultDescription": "",
  "durationMs": "255",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "1ee2b6e9-3af4-4873-8c7c-1a698b9ac334",
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/query?api-version=2020-05-31-preview",
}
```

### <a name="egress-log-schemas"></a>Uitgangs logboek schema's

Dit is het schema voor `ADTEventRoutesOperation` Logboeken. Deze bevatten informatie over uitzonde ringen en de API-bewerkingen rond eind punten die zijn verbonden met een Azure Digital Apparaatdubbels-exemplaar.

|Veldnaam | Gegevenstype | Beschrijving |
|-----|------|-------------|
| `Time` | DateTime | De datum en tijd waarop deze gebeurtenis is opgetreden, in UTC |
| `ResourceId` | Tekenreeks | De Azure Resource Manager Resource-ID voor de resource waar het evenement plaatsvond |
| `OperationName` | Tekenreeks  | Het type actie dat wordt uitgevoerd tijdens de gebeurtenis |
| `Category` | Tekenreeks | Het type bron dat wordt verzonden |
| `ResultDescription` | Tekenreeks | Aanvullende informatie over de gebeurtenis |
| `Level` | Tekenreeks | De ernst van de logboek registratie van de gebeurtenis |
| `Location` | Tekenreeks | De regio waar het evenement plaatsvond |
| `EndpointName` | Tekenreeks | De naam van het uitgangs eindpunt dat is gemaakt in azure Digital Apparaatdubbels |

Hieronder ziet u voor beelden van JSON-instanties voor deze typen logboeken.

#### <a name="adteventroutesoperation"></a>ADTEventRoutesOperation

```json
{
  "time": "2020-11-05T22:18:38.0708705Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/eventroutes/action",
  "category": "EventRoutesOperation",
  "resultDescription": "Unable to send EventGrid message to [my-event-grid.westus-1.eventgrid.azure.net] for event Id [f6f45831-55d0-408b-8366-058e81ca6089].",
  "correlationId": "7f73ab45-14c0-491f-a834-0827dbbf7f8e",
  "level": "3",
  "location": "southcentralus",
  "properties": {
    "endpointName": "endpointEventGridInvalidKey"
  }
}
```

## <a name="next-steps"></a>Volgende stappen

* Zie [*logboek gegevens verzamelen en gebruiken van uw Azure-resources*](../azure-monitor/platform/platform-logs-overview.md)voor meer informatie over het configureren van diagnostiek.
* Zie voor meer informatie over de metrische gegevens van de Azure Digital Apparaatdubbels [*problemen oplossen: metrische gegevens weer geven met Azure monitor*](troubleshoot-metrics.md).
* Zie [*probleem oplossing: waarschuwingen instellen*](troubleshoot-alerts.md)voor meer informatie over het inschakelen van waarschuwingen voor uw metrische gegevens.
