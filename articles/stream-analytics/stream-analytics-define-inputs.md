---
title: Gegevens streamen als invoer in Azure Stream Analytics
description: Meer informatie over het instellen van een gegevensverbinding in Azure Stream Analytics. Invoer bevat een gegevensstroom van gebeurtenissen en ook referentiegegevens.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/17/2020
ms.openlocfilehash: 388f43fec9242f6a4b448483d9486aa4413d2612
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254467"
---
# <a name="stream-data-as-input-into-stream-analytics"></a>Gegevens streamen als invoer in Stream Analytics

Stream Analytics heeft eersteklas integratie met Azure-gegevensstromen als invoer van drie soorten resources:

- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT-hub](https://azure.microsoft.com/services/iot-hub/) 
- [Azure Blob-opslag](https://azure.microsoft.com/services/storage/blobs/) 

Deze invoerbronnen kunnen in hetzelfde Azure-abonnement worden gebruikt als uw Stream Analytics-taak of een ander abonnement.

### <a name="compression"></a>Compressie

Stream Analytics ondersteunt compressie voor alle gegevensstroominvoerbronnen. Ondersteunde compressietypen zijn: Geen, GZip en Leeglopencompressie. Ondersteuning voor compressie is niet beschikbaar voor referentiegegevens. Als de invoerindeling Avro-gegevens is die worden gecomprimeerd, wordt deze transparant verwerkt. U hoeft geen compressietype met Avro-serialisatie op te geven. 

## <a name="create-edit-or-test-inputs"></a>Ingangen maken, bewerken of testen

U de [Azure-portal,](stream-analytics-quick-create-portal.md) [Visual Studio](stream-analytics-quick-create-vs.md)en [Visual Studio Code](quick-create-vs-code.md) gebruiken om bestaande ingangen in uw streamingtaak toe te voegen en weer te geven of te bewerken. U ook invoerverbindingen testen en [query's testen](stream-analytics-manage-job.md#test-your-query) op voorbeeldgegevens van de Azure-portal, [Visual Studio](stream-analytics-vs-tools-local-run.md)en Visual [Studio Code.](visual-studio-code-local-run.md) Wanneer u een query schrijft, vermeldt u de invoer in de FROM-component. U de lijst met beschikbare invoer ophalen op de pagina **Query** in de portal. Als u meerdere ingangen wilt `JOIN` gebruiken, `SELECT` kunt u deze gebruiken of meerdere query's schrijven.


## <a name="stream-data-from-event-hubs"></a>Gegevens streamen vanuit Event Hubs

Azure Event Hubs biedt zeer schaalbare gebeurtenis-inname van publiceren. Een gebeurtenishub kan miljoenen gebeurtenissen per seconde verzamelen, zodat u de enorme hoeveelheden gegevens die door uw verbonden apparaten en toepassingen worden geproduceerd, verwerken en analyseren. Event Hubs en Stream Analytics bieden samen een end-to-end oplossing voor real-time analytics. Met Gebeurtenishubs u gebeurtenissen in realtime in Azure uitvoeren en kunnen Stream Analytics-taken deze gebeurtenissen in realtime verwerken. U bijvoorbeeld webklikken, sensormetingen of onlinelogboekgebeurtenissen naar gebeurtenishubs verzenden. U vervolgens Stream Analytics-taken maken om Gebeurtenishubs te gebruiken als invoergegevensstromen voor realtime filteren, aggregeren en correlatie.

`EventEnqueuedUtcTime`is de tijdstempel van de aankomst van een gebeurtenis in een gebeurtenishub en is de standaardtijdstempel van gebeurtenissen die afkomstig zijn van Gebeurtenishubs naar Stream Analytics. Als u de gegevens als een stream wilt verwerken met een tijdstempel in de gebeurtenispayload, moet u het trefwoord [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) gebruiken.

### <a name="event-hubs-consumer-groups"></a>Event Hubs Consumentengroepen

U moet elke Stream Analytics-gebeurtenishub-ingang configureren om een eigen consumentengroep te hebben. Wanneer een taak een zelf-join bevat of meerdere ingangen heeft, kunnen sommige ingangen door meer dan één lezer stroomafwaarts worden gelezen. Deze situatie heeft gevolgen voor het aantal lezers in één consumentengroep. Om te voorkomen dat de limiet van gebeurtenishubs van vijf lezers per consumentengroep per partitie wordt overschreden, is het een goede gewoonte om een consumentengroep aan te wijzen voor elke Stream Analytics-taak. Er is ook een limiet van 20 consumentengroepen voor een standaardgebeurtenishub. Zie [Problemen met Azure Stream Analytics-invoer oplossen](stream-analytics-troubleshoot-input.md)voor meer informatie.

### <a name="create-an-input-from-event-hubs"></a>Een invoer maken vanuit gebeurtenishubs

In de volgende tabel wordt elke eigenschap op de **nieuwe invoerpagina** in de Azure-portal uitgelegd om gegevensinvoer van een gebeurtenishub te streamen:

| Eigenschap | Beschrijving |
| --- | --- |
| **Invoeralias** |Een vriendelijke naam die u in de query van de taak gebruikt om naar deze invoer te verwijzen. |
| **Abonnement** | Kies het abonnement waarin de gebeurtenishubbron bestaat. | 
| **Event hub-naamruimte** | De naamruimte van de gebeurtenishub is een container voor een reeks berichtenentiteiten. Wanneer u een nieuwe gebeurtenishub maakt, maakt u ook de naamruimte. |
| **Naam gebeurtenishub** | De naam van de gebeurtenishub die u als invoer wilt gebruiken. |
| **Naam van het Event Hub-beleid** | Het beleid voor gedeelde toegang dat toegang biedt tot de gebeurtenishub. Elk beleid voor gedeelde toegang heeft een naam, machtigingen die u instelt en toegangssleutels. Deze optie wordt automatisch ingevuld, tenzij u de optie selecteert om de instellingen van de gebeurtenishub handmatig op te geven.|
| **Event Hub consumentengroep** (aanbevolen) | Het wordt ten zeerste aanbevolen om voor elke Stream Analytics-taak een afzonderlijke consumentengroep te gebruiken. Met deze tekenreeks identificeert de consumentengroep die moet worden gebruikt om gegevens van de gebeurtenishub in te nemen. Als er geen consumentengroep is opgegeven, wordt in de taak Stream Analytics de $Default consumentengroep gebruikt.  |
| **Partitiesleutel** | Als uw invoer wordt verdeeld door een eigenschap, u de naam van deze eigenschap toevoegen. Partitiesleutels zijn optioneel en worden gebruikt om de prestaties van uw query te verbeteren als deze een component PARTITION BY of GROUP BY op deze eigenschap bevat. |
| **Serialisatie-indeling voor gebeurtenissen** | De serialisatie-indeling (JSON, CSV, Avro of [Andere (Protobuf, XML, eigen...)](custom-deserializer.md)van de binnenkomende gegevensstroom.  Zorg ervoor dat de JSON-indeling overeenkomt met de specificatie en geen voorloop0 voor decimale getallen bevat. |
| **Encoding** | UTF-8 is momenteel het enige ondersteunde coderingsformaat. |
| **Gebeurteniscompressietype** | Het compressietype dat wordt gebruikt om de binnenkomende gegevensstroom te lezen, zoals Geen (standaard), GZip of Leeglopen. |

Wanneer uw gegevens afkomstig zijn van een gebeurtenishubstreaminvoer, hebt u toegang tot de volgende metagegevensvelden in uw Stream Analytics-query:

| Eigenschap | Beschrijving |
| --- | --- |
| **EventProcessedUtcTime** |De datum en tijd waarop de gebeurtenis is verwerkt door Stream Analytics. |
| **EventEnqueuedUtcTime** |De datum en tijd waarop de gebeurtenis is ontvangen door Gebeurtenishubs. |
| **PartitionId** |De op nul gebaseerde partitie-id voor de invoeradapter. |

Met deze velden u bijvoorbeeld een query schrijven zoals het volgende voorbeeld:

```sql
SELECT
    EventProcessedUtcTime,
    EventEnqueuedUtcTime,
    PartitionId
FROM Input
```

> [!NOTE]
> Wanneer u Event Hub gebruikt als eindpunt voor IoT Hub-routes, hebt u toegang tot de metagegevens van de IoT Hub met de [functie GetMetadataPropertyValue.](https://docs.microsoft.com/stream-analytics-query/getmetadatapropertyvalue)
> 

## <a name="stream-data-from-iot-hub"></a>Gegevens streamen vanuit IoT Hub

Azure IoT Hub is een zeer schaalbare gebeurtenis die is geoptimaliseerd voor IoT-scenario's.

De standaardtijdstempel van gebeurtenissen afkomstig van een IoT-hub in Stream Analytics is de `EventEnqueuedUtcTime`tijdstempel die de gebeurtenis heeft bereikt in de IoT-hub, wat is . Als u de gegevens als een stream wilt verwerken met een tijdstempel in de gebeurtenispayload, moet u het trefwoord [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) gebruiken.

### <a name="iot-hub-consumer-groups"></a>Iot Hub Consumentengroepen

U moet elke Stream Analytics IoT Hub-ingang configureren om een eigen consumentengroep te hebben. Wanneer een taak een zelf-join bevat of wanneer deze meerdere ingangen heeft, kan sommige invoer door meer dan één lezer stroomafwaarts worden gelezen. Deze situatie heeft gevolgen voor het aantal lezers in één consumentengroep. Om te voorkomen dat de Azure IoT Hub-limiet van vijf lezers per consumentengroep per partitie wordt overschreden, is het een goede gewoonte om een consumentengroep aan te wijzen voor elke Stream Analytics-taak.

### <a name="configure-an-iot-hub-as-a-data-stream-input"></a>Een IoT-hub configureren als gegevensstroominvoer

In de volgende tabel wordt elke eigenschap in de **nieuwe invoerpagina** in de Azure-portal uitgelegd wanneer u een IoT-hub configureert als streaminvoer.

| Eigenschap | Beschrijving |
| --- | --- |
| **Invoeralias** | Een vriendelijke naam die u in de query van de taak gebruikt om naar deze invoer te verwijzen.|
| **Abonnement** | Kies het abonnement waarin de IoT Hub-bron bestaat. | 
| **IoT-hub** | De naam van de IoT-hub om te gebruiken als invoer. |
| **Eindpunt** | Het eindpunt voor de IoT Hub.|
| **Naam van beleid voor gedeelde toegang** | Het beleid voor gedeelde toegang dat toegang biedt tot de IoT-hub. Elk beleid voor gedeelde toegang heeft een naam, machtigingen die u instelt en toegangssleutels. |
| **Beleidssleutel voor gedeelde toegang** | De gedeelde toegangssleutel die wordt gebruikt om de toegang tot de IoT-hub te autoriseren.  Deze optie wordt automatisch ingevuld, tenzij u de optie selecteert om de iot-hub-instellingen handmatig op te geven. |
| **Consumentengroep** | Het wordt ten zeerste aanbevolen dat u voor elke Stream Analytics-taak een andere consumentengroep gebruikt. De consumentengroep wordt gebruikt om gegevens van de IoT Hub in te nemen. Stream Analytics gebruikt de $Default consumentengroep, tenzij u anders opgeeft.  |
| **Partitiesleutel** | Als uw invoer wordt verdeeld door een eigenschap, u de naam van deze eigenschap toevoegen. Partitiesleutels zijn optioneel en worden gebruikt om de prestaties van uw query te verbeteren als deze een component PARTITION BY of GROUP BY op deze eigenschap bevat. |
| **Serialisatie-indeling voor gebeurtenissen** | De serialisatie-indeling (JSON, CSV, Avro of [Andere (Protobuf, XML, eigen...)](custom-deserializer.md)van de binnenkomende gegevensstroom.  Zorg ervoor dat de JSON-indeling overeenkomt met de specificatie en geen voorloop0 voor decimale getallen bevat. |
| **Encoding** | UTF-8 is momenteel het enige ondersteunde coderingsformaat. |
| **Gebeurteniscompressietype** | Het compressietype dat wordt gebruikt om de binnenkomende gegevensstroom te lezen, zoals Geen (standaard), GZip of Leeglopen. |


Wanneer u streamgegevens van een IoT-hub gebruikt, hebt u toegang tot de volgende metagegevensvelden in uw Stream Analytics-query:

| Eigenschap | Beschrijving |
| --- | --- |
| **EventProcessedUtcTime** | De datum en het tijdstip waarop de gebeurtenis is verwerkt. |
| **EventEnqueuedUtcTime** | De datum en tijd waarop de gebeurtenis is ontvangen door de IoT Hub. |
| **PartitionId** | De op nul gebaseerde partitie-id voor de invoeradapter. |
| **IotHub.MessageId** | Een ID die wordt gebruikt om tweerichtingscommunicatie in IoT Hub te correleren. |
| **IotHub.CorrelationId** | Een id die wordt gebruikt in berichtreacties en feedback in IoT Hub. |
| **IotHub.ConnectionDeviceId** | De verificatie-id die wordt gebruikt om dit bericht te verzenden. Deze waarde wordt gestempeld op servicegebonden berichten door de IoT Hub. |
| **Iothub.ConnectionDeviceGenerationId** | De generatie-ID van het geverifieerde apparaat dat is gebruikt om dit bericht te verzenden. Deze waarde wordt gestempeld op servicegebonden berichten door de IoT Hub. |
| **IoTHub.EnqueuedTime** | Het tijdstip waarop het bericht is ontvangen door de IoT-hub. |


## <a name="stream-data-from-blob-storage"></a>Gegevens streamen vanuit Blob-opslag
Voor scenario's met grote hoeveelheden ongestructureerde gegevens die in de cloud moeten worden opgeslagen, biedt Azure Blob-opslag een kosteneffectieve en schaalbare oplossing. Gegevens in Blob-opslag worden meestal beschouwd als gegevens in rust; Blob-gegevens kunnen echter worden verwerkt als een gegevensstroom door Stream Analytics. 

Logboekverwerking is een veelgebruikt scenario voor het gebruik van Blob-opslagingangen met Stream Analytics. In dit scenario zijn telemetriegegevensbestanden uit een systeem vastgelegd en moeten ze worden ontleed en verwerkt om zinvolle gegevens te extraheren.

De standaardtijdstempel van Blob-opslaggebeurtenissen in Stream Analytics is de tijdstempel die de blob voor het laatst is gewijzigd, namelijk `BlobLastModifiedUtcTime`. Als een blob om 13:00 uur wordt geüpload naar een opslagaccount en de Azure Stream Analytics-taak wordt gestart met de optie *Nu* om 13:01, wordt de blob niet opgehaald omdat de gewijzigde tijd buiten de taakperiode valt.

Als een blob om 13:00 uur wordt geüpload naar een opslagaccountcontainer en de Azure Stream Analytics-taak wordt gestart met *aangepaste tijd* om 13:00 of eerder, wordt de blob opgehaald als de gewijzigde tijd binnen de taakperiode valt.

Als een Azure Stream Analytics-taak wordt gestart met *Nu* om 13:00 uur en een blob om 13:01 wordt geüpload naar de opslagaccountcontainer, wordt de blob opgehaald met Nu.

Als u de gegevens als een stream wilt verwerken met een tijdstempel in de gebeurtenispayload, moet u het trefwoord [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) gebruiken. Een taak Stream Analytics haalt elke seconde gegevens uit Azure Blob-opslaginvoer als het blobbestand beschikbaar is. Als het blobbestand niet beschikbaar is, is er een exponentiële back-off met een maximale vertraging van 90 seconden.

Voor csv-opgemaakte invoer is een koptekstrij vereist om velden voor de gegevensset te definiëren en moeten alle velden met koptekstrij uniek zijn.

> [!NOTE]
> Stream Analytics biedt geen ondersteuning voor het toevoegen van inhoud aan een bestaand blobbestand. Stream Analytics bekijkt elk bestand slechts één keer en eventuele wijzigingen die in het bestand optreden nadat de taak de gegevens heeft gelezen, worden niet verwerkt. Aanbevolen is om alle gegevens voor een blobbestand in één keer te uploaden en vervolgens extra nieuwere gebeurtenissen toe te voegen aan een ander, nieuw blobbestand.

Het uploaden van een zeer groot aantal blobs tegelijk kan ertoe leiden dat Stream Analytics het lezen van enkele blobs in zeldzame gevallen overslaat. Het wordt aanbevolen om blobs ten minste 2 seconden uit elkaar te uploaden naar Blob-opslag. Als deze optie niet haalbaar is, u Gebeurtenishubs gebruiken om grote hoeveelheden gebeurtenissen te streamen. 

### <a name="configure-blob-storage-as-a-stream-input"></a>Blob-opslag configureren als streaminvoer 

In de volgende tabel wordt elke eigenschap in de **nieuwe invoerpagina** in de Azure-portal uitgelegd wanneer u Blob-opslag configureert als een streaminvoer.

| Eigenschap | Beschrijving |
| --- | --- |
| **Invoeralias** | Een vriendelijke naam die u in de query van de taak gebruikt om naar deze invoer te verwijzen. |
| **Abonnement** | Kies het abonnement waarin de IoT Hub-bron bestaat. | 
| **Opslagaccount** | De naam van het opslagaccount waar de blobbestanden zich bevinden. |
| **Opslagaccountsleutel** | De geheime sleutel die is gekoppeld aan het opslagaccount. Deze optie wordt automatisch ingevuld, tenzij u de optie selecteert om de blob-opslaginstellingen handmatig op te geven. |
| **Container** | De container voor de blob-invoer. Containers bieden een logische groepering voor blobs die zijn opgeslagen in de Microsoft Azure Blob-service. Wanneer u een blob uploadt naar de Azure Blob-opslagservice, moet u een container voor die blob opgeven. U bestaande container **gebruiken** of **Nieuw maken gebruiken** om een nieuwe container te maken.|
| **Padpatroon** (optioneel) | Het bestandspad dat wordt gebruikt om de blobs in de opgegeven container te lokaliseren. Als u blobs wilt lezen vanaf de wortel van de container, stelt u geen padpatroon in. Binnen het pad u een of meer exemplaren `{date}`van `{time}`de volgende drie variabelen opgeven:, of`{partition}`<br/><br/>Voorbeeld 1:`cluster1/logs/{date}/{time}/{partition}`<br/><br/>Voorbeeld 2:`cluster1/logs/{date}`<br/><br/>Het `*` teken is geen toegestane waarde voor het padvoorvoegsel. Alleen geldige <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">Azure blob-tekens</a> zijn toegestaan. Neem geen containernamen of bestandsnamen op. |
| **Datumnotatie** (optioneel) | Als u de datumvariabele in het pad gebruikt, wordt de datumnotatie waarin de bestanden zijn ingedeeld. Voorbeeld: `YYYY/MM/DD` |
| **Tijdnotatie** (optioneel) |  Als u de tijdvariabele in het pad gebruikt, wordt de tijdindeling waarin de bestanden zijn ingedeeld. Momenteel is `HH` de enige ondersteunde waarde voor uren. |
| **Partitiesleutel** | Als uw invoer wordt verdeeld door een eigenschap, u de naam van deze eigenschap toevoegen. Partitiesleutels zijn optioneel en worden gebruikt om de prestaties van uw query te verbeteren als deze een component PARTITION BY of GROUP BY op deze eigenschap bevat. |
| **Serialisatie-indeling voor gebeurtenissen** | De serialisatie-indeling (JSON, CSV, Avro of [Andere (Protobuf, XML, eigen...)](custom-deserializer.md)van de binnenkomende gegevensstroom.  Zorg ervoor dat de JSON-indeling overeenkomt met de specificatie en geen voorloop0 voor decimale getallen bevat. |
| **Encoding** | Voor CSV en JSON is UTF-8 momenteel het enige ondersteunde coderingsformaat. |
| **Compressie** | Het compressietype dat wordt gebruikt om de binnenkomende gegevensstroom te lezen, zoals Geen (standaard), GZip of Leeglopen. |

Wanneer uw gegevens afkomstig zijn van een Blob-opslagbron, hebt u toegang tot de volgende metagegevensvelden in uw Stream Analytics-query:

| Eigenschap | Beschrijving |
| --- | --- |
| **BlobName** |De naam van de invoerblob waar de gebeurtenis vandaan kwam. |
| **EventProcessedUtcTime** |De datum en tijd waarop de gebeurtenis is verwerkt door Stream Analytics. |
| **BlobLastModifiedutcTime** |De datum en tijd waarop de blob voor het laatst is gewijzigd. |
| **PartitionId** |De op nul gebaseerde partitie-id voor de invoeradapter. |

Met deze velden u bijvoorbeeld een query schrijven zoals het volgende voorbeeld:

```sql
SELECT
    BlobName,
    EventProcessedUtcTime,
    BlobLastModifiedUtcTime
FROM Input
```

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Snelstart: Een Stream Analytics-taak maken via Azure Portal](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
