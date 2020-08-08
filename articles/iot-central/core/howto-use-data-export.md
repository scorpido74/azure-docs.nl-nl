---
title: Gegevens exporteren uit IoT Central | Microsoft Docs
description: De nieuwe gegevens export gebruiken om uw IoT-gegevens te exporteren naar Azure en aangepaste Cloud bestemmingen.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 08/04/2020
ms.topic: how-to
ms.service: iot-central
manager: corywink
ms.openlocfilehash: 737fe4b334e60f1b51e8f60f39e8821588a6841c
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/07/2020
ms.locfileid: "88010289"
---
# <a name="export-iot-data-to-cloud-destinations-using-data-export-preview"></a>IoT-gegevens exporteren naar Cloud bestemmingen met behulp van gegevens export (preview-versie)

> [!Note]
> Zoekt u de verouderde gegevens export? U vindt [hier](./howto-export-data.md)informatie over het exporteren van gegevens. Zie voor meer informatie over de verschillen tussen de nieuwe gegevens export en verouderde gegevens export de [vergelijkings tabel](#comparison-of-legacy-data-export-and-new-data-export).

In dit artikel wordt beschreven hoe u de nieuwe preview-functies voor het exporteren van gegevens kunt gebruiken in azure IoT Central. U kunt deze functie gebruiken om uw gefilterde en verrijkte IoT-gegevens voortdurend te exporteren naar uw Cloud Services. U kunt gegevens export gebruiken om wijzigingen in bijna realtime te pushen naar andere onderdelen van uw Cloud oplossing voor inzichten, analyses en opslag van warme paden. 

 U kunt bijvoorbeeld:
-   Doorlopend telemetrie-gegevens en eigenschaps wijzigingen in de JSON-indeling in vrijwel realtime exporteren
-   Filter deze gegevens stromen om specifieke mogelijkheden te exporteren die overeenkomen met aangepaste voor waarden
-   De gegevens stromen verrijken met aangepaste waarden en eigenschaps waarden van het apparaat
-   Deze gegevens verzenden naar bestemmingen zoals Azure Event Hubs, Azure Service Bus, Azure Blob Storage en webhook-eind punten

> [!Note]
> Wanneer u het exporteren van gegevens inschakelt, worden er vanaf dat moment alleen gegevens opgehaald. Op dit moment kunnen geen gegevens worden opgehaald voor een tijdstip waarop het exporteren van gegevens is uitgeschakeld. Als u meer historische gegevens wilt behouden, schakelt u de optie gegevens export voor tijdig in.

## <a name="prerequisites"></a>Vereisten

U moet een beheerder zijn in uw IoT Central-toepassing of machtigingen voor het exporteren van gegevens hebben.

## <a name="set-up-export-destination"></a>Export bestemming instellen

Het export doel moet bestaan voordat u uw gegevens export kunt configureren. Dit zijn de beschik bare typen doelen:
  - Azure Event Hubs
  - Azure Service Bus-wachtrij
  - Azure Service Bus-onderwerp
  - Azure Blob Storage
  - Webhook

### <a name="create-an-event-hubs-destination"></a>Een Event Hubs bestemming maken

Als u geen bestaande Event Hubs naam ruimte hebt om naar te exporteren, voert u de volgende stappen uit:

1. Maak een [nieuwe event hubs naam ruimte in de Azure Portal](https://ms.portal.azure.com/#create/Microsoft.EventHub). Meer informatie vindt u in [Azure Event hubs docs](../../event-hubs/event-hubs-create.md).

2. Maak een Event Hub in uw Event Hubs naam ruimte. Ga naar uw naam ruimte en selecteer **+ Event hub** aan de bovenkant om een event hub-exemplaar te maken.

3. Genereer een sleutel die u in IoT Central gaat gebruiken om uw gegevens export in te stellen. 
    - Klik op het Event Hub exemplaar dat u hebt gemaakt. 
    - Klik op **instellingen/beleid voor gedeelde toegang**. 
    - Maak een nieuwe sleutel of kies een bestaande sleutel die machtigingen voor **verzenden** heeft. 
    - Kopieer de primaire of secundaire connection string. U gebruikt deze voor het instellen van een nieuwe bestemming in IoT Central.

### <a name="create-a-service-bus-queue-or-topic-destination"></a>Een Service Bus wachtrij of onderwerp bestemming maken

Als u geen bestaande Service Bus naam ruimte hebt om naar te exporteren, voert u de volgende stappen uit:

1. Maak een [nieuwe service bus naam ruimte in de Azure Portal](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5). Meer informatie vindt u in [Azure service bus docs](../../service-bus-messaging/service-bus-create-namespace-portal.md).

2. Als u een wachtrij of onderwerp wilt maken om naar te exporteren, gaat u naar uw Service Bus-naam ruimte en selecteert u **+ wachtrij** of **+ onderwerp**.

3. Genereer een sleutel die u in IoT Central gaat gebruiken om uw gegevens export in te stellen. 
    - Klik op de wachtrij of het onderwerp dat u hebt gemaakt. 
    - Klik op **instellingen/beleid voor gedeelde toegang**. 
    - Maak een nieuwe sleutel of kies een bestaande sleutel die machtigingen voor **verzenden** heeft. 
    - Kopieer de primaire of secundaire connection string. U gebruikt deze voor het instellen van een nieuwe bestemming in IoT Central.

### <a name="create-an-azure-blob-storage-destination"></a>Een Azure Blob Storage-bestemming maken

Als u geen bestaand Azure Storage-account hebt om naar te exporteren, voert u de volgende stappen uit:

1. Maak een [Nieuw opslag account in de Azure Portal](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Meer informatie over het maken van nieuwe [Azure Blob Storage-accounts](https://aka.ms/blobdocscreatestorageaccount) of [Azure data Lake Storage v2-opslag accounts](../../storage/blobs/data-lake-storage-quickstart-create-account.md). Gegevens export kan alleen gegevens schrijven naar opslag accounts die ondersteuning bieden voor blok-blobs. De volgende lijst bevat de bekende compatibele opslag account typen:

    |Prestatie niveau|Accounttype|
    |-|-|
    |Standard|Algemeen v2|
    |Standard|Algemeen v1|
    |Standard|Blob Storage|
    |Premium|Blob-opslag blok keren|

2. Maak een container in uw opslag account. Ga naar uw opslagaccount. Selecteer onder **BLOB**-service **Bladeren door blobs**. Selecteer **+ container** aan de bovenkant om een nieuwe container te maken.

3. Genereer een connection string voor uw opslag account door naar **instellingen/toegangs sleutels**te gaan. Kopieer een van de twee verbindings reeksen.

### <a name="create-a-webhook-endpoint"></a>Een webhook-eind punt maken
U kunt een openbaar beschikbaar HTTP-eind punt opgeven voor de gegevens die u wilt exporteren. U kunt een test webhook-eind punt maken met behulp van RequestBin. Houd er wel voor dat RequestBin een aanvraag limiet heeft ingesteld voordat aanvragen worden beperkt.

1. Open [RequestBin](https://requestbin.net/).
2. Maak een nieuwe RequestBin en kopieer de URL van de opslag locatie.

## <a name="set-up-data-export"></a>Gegevens export instellen

Nu u een bestemming hebt voor het exporteren van gegevens, voert u de volgende stappen uit om het exporteren van gegevens in te stellen.

1. Meld u aan bij uw IoT Central-toepassing.

2. Selecteer in het linkerdeel venster **gegevens export**.

    > [!Tip]
    > Als het exporteren van **gegevens** niet in het linkerdeel venster wordt weer gegeven, bent u niet gemachtigd om de gegevens export in uw app te configureren. Neem contact op met een beheerder om het exporteren van gegevens in te stellen.

3. Selecteer de knop **+ Nieuw exporteren** . 

4. Voer een weergave naam in voor de nieuwe export en zorg ervoor dat de **ingeschakelde** wissel knop is ingeschakeld voor de gegevens stroom.

## <a name="1-choose-the-type-of-data-to-export"></a>1. Kies het type gegevens dat u wilt exporteren
U kunt ervoor kiezen om voortdurend verschillende typen gegevens te exporteren. Dit zijn de ondersteunde gegevens typen:

| Gegevenstype | Beschrijving | Gegevensindeling |
| :------------- | :---------- | :----------- |
|  Telemetrie | Telemetrie-berichten van apparaten in bijna realtime exporteren. Elk geëxporteerd bericht bevat de volledige inhoud van het oorspronkelijke apparaat bericht, genormaliseerd.   |  [Indeling voor telemetrie-berichten](#telemetry-format)   |
| Eigenschaps wijzigingen | Wijzigingen in de eigenschappen van het apparaat en de cloud in bijna realtime exporteren. Wijzigingen in de gerapporteerde waarden worden geëxporteerd voor eigenschappen van alleen-lezen apparaten. Voor eigenschappen voor lezen/schrijven worden zowel gerapporteerde als de gewenste waarden geëxporteerd. | [Bericht indeling voor wijzigen van eigenschap](#property-changes-format) |

## <a name="2-optional-add-filters"></a>2. (optioneel) filters toevoegen
Voeg filters toe om de hoeveelheid geëxporteerde gegevens op basis van filter voorwaarden te verminderen. Er zijn verschillende typen filters beschikbaar voor elk type gegevens dat moet worden geëxporteerd.

### <a name="telemetry-filters"></a>Telemetrie filters
  - **Mogelijkheidsprofiel**: als u een telemetrie-item in de vervolg keuzelijst kiest, bevat de geëxporteerde stroom alleen telemetriegegevens die voldoen aan de filter voorwaarde. Als u een apparaat-of Cloud eigenschaps item in de vervolg keuzelijst kiest, bevat de geëxporteerde stroom alleen telemetrie van apparaten met eigenschappen die overeenkomen met de filter voorwaarde.
  - **Filter voor bericht eigenschappen**: apparaten die gebruikmaken van de apparaat-sdk's, kunnen *bericht eigenschappen* of *toepassings eigenschappen* verzenden voor elk telemetrie-bericht. Dit is een verzameling sleutel-waardeparen die doorgaans wordt gebruikt om het bericht met aangepaste id's te labelen. U kunt een filter voor bericht eigenschappen maken door te typen in de sleutel van de bericht eigenschap die u zoekt en een voor waarde op te geven. Alleen telemetrie-berichten met bericht eigenschappen die overeenkomen met de opgegeven filter voorwaarde, worden geëxporteerd. Alleen vergelijkings operatoren voor teken reeksen worden ondersteund (is gelijk aan, is niet gelijk aan, bevat, niet bevat, bestaat niet). Meer [informatie over toepassings eigenschappen van IOT hub docs](../../iot-hub/iot-hub-devguide-messages-construct.md).

### <a name="property-changes-filters"></a>Filters voor eigenschaps wijzigingen
**Eigenschappen filter**: Kies een eigenschaps item in de vervolg keuzelijst en de geëxporteerde stroom bevat alleen wijzigingen in de geselecteerde eigenschap die voldoet aan de filter voorwaarde.

## <a name="3-optional-add-enrichments"></a>3. (optioneel) verrijkingen toevoegen
Voeg verrijkingen toe aan verrijkte geëxporteerde berichten met aanvullende meta gegevens in sleutel-waardeparen. Dit zijn de beschik bare verrijkingen voor de gegevens typen telemetrie en eigenschappen wijzigingen:
  - **Aangepaste teken reeks**: Hiermee voegt u een aangepaste statische teken reeks aan elk bericht toe. Voer een sleutel in en voer een wille keurige teken reeks waarde in.
  - **Eigenschap**: Hiermee wordt de huidige waarde van de eigenschap voor het apparaat of de eigenschap van de cloud aan elk bericht toegevoegd. Voer een wille keurige sleutel in en kies een apparaat-of Cloud eigenschap. Als het geëxporteerde bericht afkomstig is van een apparaat dat niet de opgegeven eigenschap apparaat of Cloud heeft, heeft het geëxporteerde bericht deze verrijking niet.

## <a name="4-add-destinations"></a>4. bestemmingen toevoegen
Maak een nieuwe bestemming of Voeg een bestemming toe die u al hebt gemaakt. 
  
1. Klik op de koppeling **een nieuwe bestemming maken** . Vul de volgende informatie in:
    - **Doel naam**: de weergave naam van de bestemming in IOT Central
    - **Doel type**: Kies het type bestemming. Als u dat nog niet hebt gedaan, [moet u uw export bestemming instellen](#set-up-export-destination)
    - Plak voor Azure Event Hubs, Azure Service Bus wachtrij of onderwerp, de connection string voor uw resource. 
    - Plak voor Azure Blob Storage de connection string voor uw resource en voer de naam van de container in (hoofdletter gevoelig).
    - Plak voor webhook de call back-URL voor het webhook-eind punt. 
    - Klik op **Maken**

2. Klik op **+ bestemming** en kies een bestemming in de vervolg keuzelijst. U kunt Maxi maal 5 bestemmingen aan één export toevoegen.

3. Klik op **Opslaan**als u klaar bent met het instellen van het exporteren. Na een paar minuten worden uw gegevens weer gegeven in uw doelen.

## <a name="export-contents-and-format"></a>Inhoud en indeling exporteren

Voor Event Hubs en Service Bus doelen worden gegevens in bijna realtime geëxporteerd. De gegevens bevindt zich in de hoofd tekst van het bericht en bevindt zich in JSON-indeling als UTF-8. Hieronder vindt u voor beelden.

Bij Blob Storage worden gegevens eenmaal per minuut geëxporteerd, waarbij elk bestand met de batch wijzigingen sinds het laatste geëxporteerde bestand is. Geëxporteerde gegevens worden in drie mappen in JSON-indeling geplaatst. De standaard paden in uw opslag account zijn:

- Telemetrie: _{container}/{app-id}/{partition_id}/{yyyy}/{mm}/{dd}/{hh}/{mm}/{filename}_
- Eigenschaps wijzigingen: _{container}/{app-id}/{partition_id}/{yyyy}/{mm}/{dd}/{hh}/{mm}/{filename}_

Als u door de geëxporteerde bestanden in de Azure Portal wilt bladeren, gaat u naar het bestand en selecteert u het tabblad **BLOB bewerken** .

Voor webhooks-bestemmingen worden gegevens ook bijna in realtime geëxporteerd. De gegevens in de hoofd tekst van het bericht bevindt zich in dezelfde indeling als voor Event Hubs en Service Bus.


## <a name="telemetry-format"></a>Telemetrie-indeling
Elk geëxporteerd bericht bevat een genormaliseerde vorm van het volledige bericht het apparaat dat is verzonden in de hoofd tekst van het bericht in JSON-indeling gecodeerd als UTF-8. Meer informatie in elk bericht bevat:

- `applicationId`van de IoT Central-app
- `messageSource`Wat is *telemetrie* voor het exporteren van telemetriegegevens
- `deviceId`van het apparaat dat het telemetrie-bericht heeft verzonden
- `schema`is de naam en versie van het payload-schema
- `templateId`is de id van de apparaat sjabloon gekoppeld aan het apparaat
- `enrichments`zijn er verrijkingen die zijn ingesteld voor de export
- `messageProperties`zijn de extra gegevens die het apparaat naast het bericht heeft verzonden. Dit wordt ook wel *toepassings eigenschappen*genoemd. [meer informatie vindt u in IOT hub docs](../../iot-hub/iot-hub-devguide-messages-construct.md).

Voor Event Hubs en Service Bus, IoT Central een nieuw bericht snel exporteren nadat het bericht van een apparaat is ontvangen.

Voor Blob-opslag worden berichten per minuut in batch verzonden en geëxporteerd.

In het volgende voor beeld ziet u een geëxporteerd telemetrie-bericht:

```json

{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@preview",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:26:55.455Z",
    "telemetry": {
        "Activity": "running",
        "BloodPressure": {
            "Diastolic": 7,
            "Systolic": 71
        },
        "BodyTemperature": 98.73447010562934,
        "HeartRate": 88,
        "HeartRateVariability": 17,
        "RespiratoryRate": 13
    },
    "enrichments": {
      "userSpecifiedKey": "sampleValue"
    },
    "messageProperties": {
      "messageProp": "value"
    }
}
```

## <a name="property-changes-format"></a>Opmaak van eigenschaps wijzigingen

Elk bericht of record vertegenwoordigt een wijziging van een apparaat of Cloud eigenschap. Voor apparaateigenschappen worden alleen wijzigingen in de gerapporteerde waarde geëxporteerd als een afzonderlijk bericht. In het geëxporteerde bericht zijn de volgende aanvullende gegevens opgenomen:

- `applicationId`van de IoT Central-app
- `messageSource`Wat zijn *Eigenschappen* voor het exporteren van eigenschaps gegevens wijzigingen
- `messageType`Dit is *cloudPropertyChange* of *devicePropertyDesiredChange*, *devicePropertyReportedChange*
- `deviceId`van het apparaat waarvan de eigenschappen zijn gewijzigd
- `schema`is de naam en versie van het payload-schema
- `templateId`is de id van de apparaat sjabloon gekoppeld aan het apparaat
- `enrichments`zijn er verrijkingen die zijn ingesteld voor de export

IoT Central exporteert voor Event Hubs en Service Bus nieuwe bericht gegevens naar uw Event Hub-of Service Bus-wachtrij of-onderwerp in bijna realtime.

Voor Blob-opslag worden berichten per minuut in batch verzonden en geëxporteerd.

In het volgende voor beeld ziet u het wijzigings bericht voor een geëxporteerde eigenschap dat is ontvangen in Azure Blob Storage.

```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "properties",
    "messageType": "cloudPropertyChange",
    "deviceId": "18a985g1fta",
    "schema": "default@preview",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:37:32.942Z",
    "properties": [{
        "fieldName": "MachineSerialNumber",
        "value": "abc"
    }],
    "enrichments": {
        "userSpecifiedKey" : "sampleValue"
    }
}
```

## <a name="comparison-of-legacy-data-export-and-new-data-export"></a>Vergelijking van verouderde gegevens export en nieuwe gegevens export
Dit is een tabel waarin de verschillen tussen de verouderde gegevens export en de nieuwe gegevens export worden gemarkeerd. Meer informatie over de oude gegevens export [vindt u hier](howto-export-data.md).

| Functies  | Verouderde gegevens export | Nieuwe gegevens export |
| :------------- | :---------- | :----------- |
| Beschik bare gegevens typen | Telemetrie, apparaten, apparaatprofielen | Telemetrie, eigenschaps wijzigingen |
| Filteren | Geen | Is afhankelijk van het geëxporteerde gegevens type. Filteren op telemetrie, bericht eigenschappen, eigenschaps waarden |
| Verrijken | Geen | Verrijken met een aangepaste teken reeks of een eigenschaps waarde op het apparaat |
| Bestemmingen | Azure Event Hubs, Azure Service Bus wacht rijen en onderwerpen, Azure Blob Storage | Hetzelfde als voor verouderde gegevens export en webhooks| 
| Opvallende limieten | 5 uitvoer per app, 1 doel per export | 10 export-doel verbindingen per app | 

## <a name="next-steps"></a>Volgende stappen

Nu u weet hoe u de nieuwe gegevens export kunt gebruiken, gaat u verder met de volgende stap:

> [!div class="nextstepaction"]
> [Analytics gebruiken in IoT Central](./howto-create-analytics.md)
