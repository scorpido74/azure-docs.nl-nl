---
title: Stream-gegevens als invoer in Azure Stream Analytics
description: Meer informatie over het instellen van een verbinding in Azure Stream Analytics. Invoer bevatten een gegevensstroom van gebeurtenissen en ook verwijzen naar gegevens.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/17/2020
ms.openlocfilehash: 388f43fec9242f6a4b448483d9486aa4413d2612
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78392577"
---
# <a name="stream-data-as-input-into-stream-analytics"></a>Stream-gegevens als invoer in Stream Analytics

Stream Analytics is een uitstekende integratie met Azure-gegevensstromen als invoer van drie soorten resources:

- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 
- [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) 

Deze invoer resources kunnen bevinden zich in hetzelfde Azure-abonnement als uw Stream Analytics-taak of een ander abonnement.

### <a name="compression"></a>Compressie

Stream Analytics biedt ondersteuning voor compressie voor alle stream input gegevensbronnen. Ondersteunde compressie typen zijn: geen-, GZip-en Deflate compressie. Ondersteuning voor compressie is niet beschikbaar voor referentiegegevens. Als de invoerindeling Avro-gegevens die zijn gecomprimeerd is, wordt dit transparant verwerkt. U hoeft niet te geven van compressietype met Avro-serialisatie. 

## <a name="create-edit-or-test-inputs"></a>Maken, bewerken of invoer testen

U kunt de [Azure Portal](stream-analytics-quick-create-portal.md), [Visual Studio](stream-analytics-quick-create-vs.md)en [Visual Studio code](quick-create-vs-code.md) gebruiken om bestaande invoer in uw streaming-taak toe te voegen en weer te geven of te bewerken. U kunt ook invoer verbindingen testen en [query's testen](stream-analytics-manage-job.md#test-your-query) vanuit voorbeeld gegevens uit de Azure Portal, [Visual Studio](stream-analytics-vs-tools-local-run.md)en [Visual Studio code](visual-studio-code-local-run.md). Wanneer u een query schrijft, vermeldt u de invoer in de component FROM. U kunt de lijst met beschik bare invoer gegevens ophalen van de **query** pagina in de portal. Als u meerdere invoer wilt gebruiken, kunt u deze `JOIN` of meerdere `SELECT` query's schrijven.


## <a name="stream-data-from-event-hubs"></a>Gegevens streamen vanuit Event Hubs

Azure Event Hubs biedt zeer schaalbare gebeurtenis ingestors publiceren / abonneren. Een Event Hub kan miljoenen gebeurtenissen per seconde verzamelen, zodat u de enorme hoeveel heden gegevens die worden geproduceerd door uw verbonden apparaten en toepassingen kunt verwerken en analyseren. Samen bieden Event Hubs en Stream Analytics u een end-to-end oplossing voor realtime analyses. Eventhubs kunt u gebeurtenissen feed in Azure in realtime, en deze gebeurtenissen in realtime kunnen worden verwerkt door Stream Analytics-taken. Bijvoorbeeld, kunt u web klikken, sensorwaarden, serverlogs, of online gebeurtenissen verzenden naar Event Hubs. Vervolgens kunt u Stream Analytics-taken voor het gebruik van Event Hubs als de invoer-gegevensstromen voor realtime filteren, aggregeren en correlatie.

`EventEnqueuedUtcTime` is de tijds tempel van de aankomst van een gebeurtenis in een Event Hub en is het standaard tijds tempel van gebeurtenissen die afkomstig zijn van Event Hubs naar Stream Analytics. Als u de gegevens wilt verwerken als een stroom met behulp van een tijds tempel in de nettolading van de gebeurtenis, moet u het sleutel woord [time stamp by](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) gebruiken.

### <a name="event-hubs-consumer-groups"></a>Consumenten groepen Event Hubs

Configureer elke Stream Analytics event hub-gegevens invoeren in een eigen consumentengroep hebben. Wanneer een taak bevat een self-join of is van meerdere invoergegevens, sommige invoer kan worden gelezen door meer dan één lezer downstream. Deze situatie heeft gevolgen voor het aantal lezers in een enkele consumergroep. Om te voorkomen dat de Event Hubs-limiet van vijf gebruikers per consumergroep per partitie, maar het is een aanbevolen procedure om een consumentengroep voor elke Stream Analytics-taak toe te wijzen. Er is ook een limiet van 20 consumenten groepen voor een standaardlaag Event Hub. Zie [problemen met Azure stream Analytics invoer oplossen](stream-analytics-troubleshoot-input.md)voor meer informatie.

### <a name="create-an-input-from-event-hubs"></a>Een invoer maken op basis van Event Hubs

In de volgende tabel wordt elke eigenschap in de **nieuwe invoer** pagina in het Azure portal voor het streamen van gegevens invoer vanuit een event hub uitgelegd:

| Eigenschap | Beschrijving |
| --- | --- |
| **Invoer alias** |Een beschrijvende naam die u in de query van de taak gebruiken om te verwijzen naar deze gegevens. |
| **Abonnement** | Kies het abonnement waarin de Event hub-bron bestaat. | 
| **Event hub-naam ruimte** | De Event Hub-naamruimte is een container voor een set berichtentiteiten. Wanneer u een nieuwe event hub maakt, maakt u ook de naamruimte. |
| **Event hub-naam** | De naam van de event hub te gebruiken als invoer. |
| **Naam van de Event hub-beleid** | Het beleid voor gedeelde toegang dat toegang tot de Event Hub biedt. Elk gedeeld toegangsbeleid heeft een naam, machtigingen die u instelt en toegangssleutels. Deze optie wordt automatisch ingevuld, tenzij u de optie voor de Event Hub-instellingen handmatig.|
| **Event hub-consumenten groep** (aanbevolen) | Het is raadzaam een afzonderlijke consumergroep gebruiken voor elke Stream Analytics-taak. Deze tekenreeks Hiermee geeft u de consumentengroep te gebruiken voor opname van gegevens uit de event hub. Als er geen consumentengroep is opgegeven, wordt in de Stream Analytics-taak de consumentengroep $Default gebruikt.  |
| **Partitie sleutel** | Als uw invoer is gepartitioneerd door een eigenschap, kunt u de naam van deze eigenschap toevoegen. Partitie sleutels zijn optioneel en worden gebruikt voor het verbeteren van de prestaties van uw query als deze de component PARTITION BY of GROUP BY bevat voor deze eigenschap. |
| **Serialisatie-indeling voor gebeurtenissen** | De serialisatie-indeling (JSON, CSV, AVRO of [Other (protobuf, XML, bedrijfs gegevens...)](custom-deserializer.md)) van de binnenkomende gegevens stroom.  Zorg ervoor dat de JSON-indeling worden uitgelijnd met de specificatie en toonaangevende 0 voor decimale getallen bevat geen. |
| **Encoding** | UTF-8 is momenteel de enige ondersteunde coderingsindeling. |
| **Type gebeurtenis compressie** | Het compressietype gebruikt om te lezen van de inkomende gegevensstroom, zoals None (standaard), GZip en Deflate. |

Wanneer uw gegevens afkomstig zijn uit een Event Hub-Stroominvoer, hebt u toegang tot de volgende metagegevensvelden in uw Stream Analytics-query:

| Eigenschap | Beschrijving |
| --- | --- |
| **EventProcessedUtcTime** |De datum en tijd waarop de gebeurtenis is verwerkt door Stream Analytics. |
| **EventEnqueuedUtcTime** |De datum en tijd waarop de gebeurtenis is ontvangen door Event Hubs. |
| **PartitionId** |De op nul gebaseerde partitie-ID voor de invoer-adapter. |

Bijvoorbeeld, kunt met behulp van deze velden, u een query zoals in het volgende voorbeeld:

```sql
SELECT
    EventProcessedUtcTime,
    EventEnqueuedUtcTime,
    PartitionId
FROM Input
```

> [!NOTE]
> Wanneer u Event hub gebruikt als een eind punt voor IoT Hub routes, hebt u toegang tot de IoT Hub meta gegevens met behulp van de [functie GetMetadataPropertyValue](https://docs.microsoft.com/stream-analytics-query/getmetadatapropertyvalue).
> 

## <a name="stream-data-from-iot-hub"></a>Stream-gegevens van IoT Hub

Azure IoT Hub is een uiterst schaal bare publicatie event ingestor geoptimaliseerd voor IoT-scenario's.

De standaardtime Stamp van gebeurtenissen die afkomstig zijn van een IoT Hub in Stream Analytics is de tijds tempel die de gebeurtenis heeft ontvangen in de IoT Hub, die wordt `EventEnqueuedUtcTime`. Als u de gegevens wilt verwerken als een stroom met behulp van een tijds tempel in de nettolading van de gebeurtenis, moet u het sleutel woord [time stamp by](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) gebruiken.

### <a name="iot-hub-consumer-groups"></a>IOT hub-consumenten groepen

Configureer elke Stream Analytics IoT-Hub invoeren in een eigen consumentengroep hebben. Wanneer een taak een self-join bevat of wanneer er meerdere invoergegevens, kan sommige invoer worden gelezen door meer dan één lezer downstream. Deze situatie heeft gevolgen voor het aantal lezers in een enkele consumergroep. Om te voorkomen dat de Azure IoT Hub-limiet van vijf gebruikers per consumergroep per partitie, is het een aanbevolen procedure om een consumentengroep voor elke Stream Analytics-taak toe te wijzen.

### <a name="configure-an-iot-hub-as-a-data-stream-input"></a>Een IoT-Hub configureren als een gegevensstroom invoer

In de volgende tabel wordt elke eigenschap in de **nieuwe invoer** pagina in het Azure Portal uitgelegd wanneer u een IOT hub als een stroom invoer configureert.

| Eigenschap | Beschrijving |
| --- | --- |
| **Invoer alias** | Een beschrijvende naam die u in de query van de taak gebruiken om te verwijzen naar deze gegevens.|
| **Abonnement** | Kies het abonnement waarin de IoT Hub-bron bestaat. | 
| **IoT Hub** | De naam van de IoT-Hub kunt u gebruiken als invoer. |
| **Endpoints** | Het eindpunt voor de IoT-Hub.|
| **Naam van het gedeelde toegangs beleid** | Het beleid voor gedeelde toegang dat toegang tot de IoT-Hub biedt. Elk gedeeld toegangsbeleid heeft een naam, machtigingen die u instelt en toegangssleutels. |
| **Sleutel voor gedeeld toegangs beleid** | De gedeelde toegangssleutel toegang verlenen aan de IoT-Hub.  Deze optie wordt automatisch ingevuld, tenzij u de optie voor de Iot-Hub instellingen handmatig. |
| **Consumentengroep** | Het is zeer raadzaam dat u een andere consumergroep voor elke Stream Analytics-taak gebruiken. De consumergroep wordt gebruikt voor opname van gegevens van de IoT-Hub. Stream Analytics maakt gebruik van de consumentengroep $Default, tenzij u iets anders opgeeft.  |
| **Partitie sleutel** | Als uw invoer is gepartitioneerd door een eigenschap, kunt u de naam van deze eigenschap toevoegen. Partitie sleutels zijn optioneel en worden gebruikt voor het verbeteren van de prestaties van uw query als deze de component PARTITION BY of GROUP BY bevat voor deze eigenschap. |
| **Serialisatie-indeling voor gebeurtenissen** | De serialisatie-indeling (JSON, CSV, AVRO of [Other (protobuf, XML, bedrijfs gegevens...)](custom-deserializer.md)) van de binnenkomende gegevens stroom.  Zorg ervoor dat de JSON-indeling worden uitgelijnd met de specificatie en toonaangevende 0 voor decimale getallen bevat geen. |
| **Encoding** | UTF-8 is momenteel de enige ondersteunde coderingsindeling. |
| **Type gebeurtenis compressie** | Het compressietype gebruikt om te lezen van de inkomende gegevensstroom, zoals None (standaard), GZip en Deflate. |


Wanneer u streaminggegevens vanuit een IoT-Hub, hebt u toegang tot de volgende metagegevensvelden in de Stream Analytics-query:

| Eigenschap | Beschrijving |
| --- | --- |
| **EventProcessedUtcTime** | De datum en tijd waarop de gebeurtenis is verwerkt. |
| **EventEnqueuedUtcTime** | De datum en tijd waarop de gebeurtenis is ontvangen door de IoT Hub. |
| **PartitionId** | De op nul gebaseerde partitie-ID voor de invoer-adapter. |
| **IoTHub. MessageId** | Een ID die wordt gebruikt voor het correleren van communicatie in twee richtingen in IoT Hub. |
| **IoTHub. CorrelationId** | Een ID die wordt gebruikt voor antwoorden en feedback van IoT-Hub. |
| **IoTHub. ConnectionDeviceId** | De verificatie-ID gebruikt om dit bericht te verzenden. Deze waarde wordt vermeld op servicebound berichten door de IoT Hub. |
| **IoTHub. ConnectionDeviceGenerationId** | De generatie-ID van het geverifieerde apparaat dat is gebruikt om dit bericht te verzenden. Deze waarde wordt vermeld op servicebound berichten door de IoT Hub. |
| **IoTHub. EnqueuedTime** | De tijd waarop het bericht is ontvangen door de IoT Hub. |


## <a name="stream-data-from-blob-storage"></a>Stream-gegevens uit Blob storage
Voor scenario's met grote hoeveelheden ongestructureerde gegevens opslaan in de cloud, biedt Azure Blob-opslag een kosteneffectieve en schaalbare oplossing. Gegevens in Blob storage wordt meestal beschouwd als de gegevens in rust; blob-gegevens kunnen echter worden verwerkt als een gegevensstroom door Stream Analytics. 

De verwerking van logboekbestanden is een veelgebruikte scenario voor het gebruik van Blob storage invoer met Stream Analytics. In dit scenario telemetrie-gegevensbestanden zijn vastgelegd in een systeem en moeten worden geparseerd en verwerkt om bruikbare gegevens te extraheren.

De standaardtime Stamp van Blob Storage-gebeurtenissen in Stream Analytics is de tijds tempel van de laatste wijziging van de blob, die wordt `BlobLastModifiedUtcTime`. Als een BLOB wordt geüpload naar een opslag account op 13:00 en de Azure Stream Analytics-taak is gestart met de optie *nu* op 13:01, wordt de BLOB niet opgehaald als gewijzigd tijd buiten de uitvoerings periode van de taak valt.

Als een BLOB wordt geüpload naar een container voor opslag accounts op 13:00 en de Azure Stream Analytics-taak is gestart met *aangepaste tijd* op 13:00 of eerder, wordt de BLOB opgehaald als gewijzigd tijd binnen de taak uitvoerings periode valt.

Als een Azure Stream Analytics taak *nu* wordt gestart op 13:00 en er een BLOB wordt geüpload naar de container van het opslag account op 13:01, wordt de blob door Azure stream Analytics opgehaald.

Als u de gegevens wilt verwerken als een stroom met behulp van een tijds tempel in de nettolading van de gebeurtenis, moet u het sleutel woord [time stamp by](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) gebruiken. Een Stream Analytics-taak haalt gegevens op uit Azure Blob storage invoer per seconde als de blobbestand beschikbaar is. Als de blob-bestand niet beschikbaar is, is er een exponentieel uitstel met een maximale vertraging van 90 seconden.

Voor invoer in CSV-indeling is een veldnamenrij vereist om velden voor de gegevensset te definiëren. alle velden in de veldnamenrij moeten uniek zijn.

> [!NOTE]
> Stream Analytics biedt geen ondersteuning voor inhoud toevoegen aan een bestaand blobbestand. Stream Analytics wordt elk bestand slechts één keer weergeven en eventuele wijzigingen die in het bestand plaatsvinden nadat de taak van de gegevens lezen niet worden verwerkt. Beste manier is om alle gegevens voor een blob-bestand tegelijk uploaden en vervolgens aanvullende nieuwere gebeurtenissen toevoegen aan een andere, nieuwe blob-bestand.

Het uploaden van een zeer groot aantal blobs tegelijk kan ertoe leiden dat Stream Analytics lees enkele blobs in zeldzame gevallen overs laat. Het wordt aanbevolen blobs ten minste 2 seconden van de Blob-opslag te uploaden. Als deze optie niet haalbaar is, kunt u Event Hubs gebruiken om grote hoeveel heden gebeurtenissen te streamen. 

### <a name="configure-blob-storage-as-a-stream-input"></a>Blob-opslag als een stroom invoer configureren 

In de volgende tabel wordt elke eigenschap in de **nieuwe invoer** pagina in de Azure Portal beschreven wanneer u Blob-opslag als een stroom invoer configureert.

| Eigenschap | Beschrijving |
| --- | --- |
| **Invoer alias** | Een beschrijvende naam die u in de query van de taak gebruiken om te verwijzen naar deze gegevens. |
| **Abonnement** | Kies het abonnement waarin de IoT Hub-bron bestaat. | 
| **Opslagaccount** | De naam van het opslagaccount waar de blob-bestanden zich bevinden. |
| **Sleutel van het opslag account** | De geheime sleutel die is gekoppeld aan de storage-account. Deze optie wordt automatisch ingevuld, tenzij u de optie voor de Blob-Opslaginstellingen handmatig. |
| **Verpakking** | De container voor de invoer van de blob. Containers bieden een logische groepering van blobs die zijn opgeslagen in de Microsoft Azure Blob-service. Wanneer u een blob geüpload naar de Azure Blob storage-service, moet u een container voor die blob opgeven. U kunt bestaande container **gebruiken** kiezen of **nieuwe maken** om een nieuwe container gemaakt te krijgen.|
| **Patroon van pad** (optioneel) | Het pad dat wordt gebruikt om de blobs in de opgegeven container te vinden. Als u blobs wilt lezen uit de hoofdmap van de container, moet u geen patroon voor paden instellen. Binnen het pad kunt u een of meer exemplaren van de volgende drie variabelen opgeven: `{date}`, `{time}`of `{partition}`<br/><br/>Voor beeld 1: `cluster1/logs/{date}/{time}/{partition}`<br/><br/>Voor beeld 2: `cluster1/logs/{date}`<br/><br/>Het `*`-teken is geen toegestane waarde voor het voor voegsel van het pad. Alleen geldige <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">Azure Blob-tekens</a> zijn toegestaan. Neem geen container namen of bestands namen op. |
| **Datum notatie** (optioneel) | Als u de datum-variabele in het pad voor de datumnotatie waarin de bestanden zijn ingedeeld gebruiken. Voorbeeld: `YYYY/MM/DD` |
| **Tijd notatie** (optioneel) |  Als u de variabele in het pad voor de indeling waarin de bestanden zijn ingedeeld. Momenteel wordt de enige ondersteunde waarde `HH` voor uren. |
| **Partitie sleutel** | Als uw invoer is gepartitioneerd door een eigenschap, kunt u de naam van deze eigenschap toevoegen. Partitie sleutels zijn optioneel en worden gebruikt voor het verbeteren van de prestaties van uw query als deze de component PARTITION BY of GROUP BY bevat voor deze eigenschap. |
| **Serialisatie-indeling voor gebeurtenissen** | De serialisatie-indeling (JSON, CSV, AVRO of [Other (protobuf, XML, bedrijfs gegevens...)](custom-deserializer.md)) van de binnenkomende gegevens stroom.  Zorg ervoor dat de JSON-indeling worden uitgelijnd met de specificatie en toonaangevende 0 voor decimale getallen bevat geen. |
| **Encoding** | Voor CSV en JSON is UTF-8 momenteel de enige ondersteunde coderingsindeling. |
| **Compressie** | Het compressietype gebruikt om te lezen van de inkomende gegevensstroom, zoals None (standaard), GZip en Deflate. |

Wanneer uw gegevens afkomstig zijn uit een Blob storage-bron, hebt u toegang tot de volgende metagegevensvelden in uw Stream Analytics-query:

| Eigenschap | Beschrijving |
| --- | --- |
| **BlobName** |De naam van de blob die de gebeurtenis afkomstig zijn uit. |
| **EventProcessedUtcTime** |De datum en tijd waarop de gebeurtenis is verwerkt door Stream Analytics. |
| **BlobLastModifiedUtcTime** |De datum en tijd waarop de blob voor het laatst is gewijzigd. |
| **PartitionId** |De op nul gebaseerde partitie-ID voor de invoer-adapter. |

Bijvoorbeeld, kunt met behulp van deze velden, u een query zoals in het volgende voorbeeld:

```sql
SELECT
    BlobName,
    EventProcessedUtcTime,
    BlobLastModifiedUtcTime
FROM Input
```

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Snelstartgids: een Stream Analytics-taak maken met behulp van de Azure Portal](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
