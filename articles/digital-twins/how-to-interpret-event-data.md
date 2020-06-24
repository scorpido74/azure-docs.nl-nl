---
title: Gebeurtenisgegevens interpreteren
titleSuffix: Azure Digital Twins
description: Zie verschillende gebeurtenis typen en de verschillende meldings berichten interpreteren.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: how-to
ms.service: digital-twins
ROBOTS: NOINDEX, NOFOLLOW
ms.openlocfilehash: e194c046cde623e0fcdd4c73ac24f2bf0755945c
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/24/2020
ms.locfileid: "85299429"
---
# <a name="understand-event-data"></a>Informatie over gebeurtenis gegevens

[!INCLUDE [Azure Digital Twins current preview status](../../includes/digital-twins-preview-status.md)]

Verschillende gebeurtenissen in azure Digital Apparaatdubbels maken **meldingen**, waardoor de back-end van de oplossing op de hoogte kan worden wanneer er verschillende acties plaatsvinden. Deze worden vervolgens [doorgestuurd](concepts-route-events.md) naar verschillende locaties binnen en buiten Azure Digital apparaatdubbels die deze informatie kunnen gebruiken om actie te ondernemen.

Er zijn verschillende typen meldingen die kunnen worden gegenereerd en meldings berichten kunnen er anders uitzien, afhankelijk van het type gebeurtenis dat ze heeft gegenereerd. In dit artikel vindt u meer informatie over verschillende typen berichten en hoe deze eruit kunnen zien.

Dit diagram toont de verschillende meldings typen:

[!INCLUDE [digital-twins-notifications.md](../../includes/digital-twins-notifications.md)]

Over het algemeen bestaan er meldingen uit twee delen: de koptekst en de hoofd tekst. 

### <a name="event-notification-headers"></a>Bericht headers voor gebeurtenis

Kopteksten van meldings berichten worden weer gegeven met sleutel-waardeparen. Afhankelijk van het protocol dat wordt gebruikt (MQTT, AMQP, of HTTP), worden bericht headers op een andere manier geserialiseerd. In deze sectie worden algemene header-informatie voor meldings berichten besproken, ongeacht het gekozen protocol en de geselecteerde serialisatie.

Sommige meldingen voldoen aan de CloudEvents-standaard. CloudEvents-conformiteit is als volgt.
* Meldingen die worden verzonden vanaf apparaten blijven de bestaande specificaties voor meldingen volgen
* De meldingen die worden verwerkt en verzonden door IoT Hub blijven de bestaande specificaties voor de melding volgen, behalve wanneer IoT Hub kiest voor de ondersteuning van CloudEvents, zoals via Event Grid
* Meldingen die worden verzonden vanuit [digitale apparaatdubbels](concepts-twins-graph.md) met een [model](concepts-models.md) dat overeenkomt met CloudEvents
* Meldingen die worden verwerkt en verzonden door Azure Digital Apparaatdubbels voldoen aan CloudEvents

Services moeten een Volg nummer toevoegen aan alle meldingen om de volg orde aan te geven of hun eigen ordening op een andere manier te onderhouden. Meldingen die worden verzonden door Azure Digital Apparaatdubbels naar Event Grid zijn ingedeeld in het Event Grid schema, totdat Event Grid CloudEvents op invoer ondersteunt. Extensie kenmerken voor headers worden toegevoegd als eigenschappen van het Event Grid schema in de payload. 

### <a name="event-notification-bodies"></a>Gebeurtenis meldings teksten

De hoofd tekst van meldings berichten wordt hier in JSON beschreven. Afhankelijk van de gewenste serialisatie voor de hoofd tekst van het bericht (bijvoorbeeld met JSON, CBOR, protobuf enzovoort), kan de bericht tekst op een andere manier worden geserialiseerd.

De set velden die de hoofd tekst bevat, verschilt per meldings type. Hier volgen twee voorbeeld bericht teksten om een idee te krijgen van wat ze in het algemeen eruitzien en kunnen bevatten.

Telemetrie-bericht:

```json
{ 
    "specversion": "1.0", 
    "type": "microsoft.iot.telemetry", 
    "source": "myhub.westus2.azuredigitaltwins.net", 
    "subject": "thermostat.vav-123", 
    "id": "c1b53246-19f2-40c6-bc9e-4666fa590d1a",
    "dataschema": "dtmi:com:contoso:DigitalTwins:VAV;1",
    "time": "2018-04-05T17:31:00Z", 
    "datacontenttype" : "application/json", 
    "data":  
      {
          "temp": 70,
          "humidity": 40 
      }
}
```

Bericht over levens cyclus melding:

```json
{ 
    "specversion": "1.0", 
    "type": "microsoft.digitaltwins.twin.create", 
    "source": "mydigitaltwins.westus2.azuredigitaltwins.net", 
    "subject": "device-123", 
    "id": "c1b53246-19f2-40c6-bc9e-4666fa590d1a", 
    "time": "2018-04-05T17:31:00Z", 
    "datacontenttype" : "application/json", 
    "dataschema": "dtmi:com:contoso:DigitalTwins:Device;1",           
    "data":  
      { 
        "$dtId": "room-123", 
        "property": "value",
        "$metadata": { 
                //...
        } 
      } 
}
```

## <a name="message-format-detail-for-different-event-types"></a>Details over het bericht formaat voor verschillende gebeurtenis typen

In deze sectie vindt u meer informatie over de verschillende typen meldingen die worden verzonden door IoT Hub en Azure Digital Apparaatdubbels (of andere Azure IoT-Services). U leest informatie over de dingen die elk meldings type activeren en de set velden die zijn opgenomen in elk type meldings hoofdtekst.

### <a name="digital-twin-life-cycle-notifications"></a>Digitale dubbele levens cyclus meldingen

Alle [digitale apparaatdubbels](concepts-twins-graph.md) verzenden meldingen, ongeacht of ze [IOT Hub apparaten vertegenwoordigen in azure Digital apparaatdubbels](how-to-ingest-iot-hub-data.md) of niet. Dit komt door meldingen over de **levens cyclus**, die betrekking hebben op de digitale twee berichten.

Meldingen over de levens cyclus worden geactiveerd wanneer:
* Er is een digitaal, twee, gemaakt
* Er is een digitale-dubbele verdubbeling verwijderd

#### <a name="properties"></a>Eigenschappen

Dit zijn de velden in de hoofd tekst van een melding voor levens cyclus.

| Name | Waarde |
| --- | --- |
| `id` | Id van de melding, zoals een UUID of een item dat door de service wordt onderhouden. `source` + `id`is uniek voor elke afzonderlijke gebeurtenis. |
| `source` | Naam van het IoT hub-of Azure Digital Apparaatdubbels-exemplaar, zoals *myhub.Azure-devices.net* of *mydigitaltwins.westus2.azuredigitaltwins.net* |
| `specversion` | 1.0 |
| `type` | `Microsoft.DigitalTwins.Twin.Create`<br>`Microsoft.DigitalTwins.Twin.Delete`<br>`Microsoft.DigitalTwins.TwinProxy.Create`<br>`Microsoft.DigitalTwins.TwinProxy.Delete`<br>`Microsoft.DigitalTwins.TwinProxy.Attach`<br>`Microsoft.DigitalTwins.TwinProxy.Detach` |
| `datacontenttype` | application/json |
| `subject` | ID van de digitale dubbele |
| `time` | Tijds tempel voor wanneer de bewerking is opgetreden op de dubbele |
| `sequence` | Waarde waarmee de positie van de gebeurtenis wordt uitgedrukt in een grotere geordende reeks gebeurtenissen. Services moeten een Volg nummer toevoegen aan alle meldingen om de volg orde aan te geven of hun eigen ordening op een andere manier te onderhouden. Het Volg nummer wordt met elk bericht verhoogd. De waarde wordt ingesteld op 1 als het object wordt verwijderd en opnieuw wordt gemaakt met dezelfde ID. |
| `sequencetype` | Meer informatie over hoe het volgorde veld wordt gebruikt. Met deze eigenschap kan bijvoorbeeld worden opgegeven dat de waarde een ondertekend 32-bits geheel getal moet zijn, dat begint bij 1 en telkens 1 keer wordt verhoogd. |

#### <a name="body-details"></a>Details van hoofd tekst

De hoofd tekst is de betrokken digitale dubbele, weer gegeven in JSON-indeling. Het schema hiervoor is *Digital Apparaatdubbels Resource 7,1*.

Bij het maken van gebeurtenissen weerspiegelt de payload de status van de dubbele nadat de resource is gemaakt, zodat deze alle door het systeem gegenereerde elementen zou moeten bevatten, net als bij een `GET` aanroep.

Hier volgt een voor beeld van een hoofd tekst voor een [IOT-Plug en Play (PnP)](../iot-pnp/overview-iot-plug-and-play.md) -apparaat, met onderdelen en geen eigenschappen op het hoogste niveau. Eigenschappen die niet zinvol zijn voor apparaten (zoals gerapporteerde eigenschappen), moeten worden wegge laten.

```json
{
  "$dtId": "device-digitaltwin-01",
  "thermostat": {
    "temperature": 80,
    "humidity": 45,
    "$metadata": {
      "$model": "dtmi:com:contoso:Thermostat;1",
      "temperature": {
        "desiredValue": 85,
        "desiredVersion": 3,
        "ackVersion": 2,
        "ackCode": 200,
        "ackDescription": "OK"
      },
      "humidity": {
        "desiredValue": 40,
        "desiredVersion": 1,
        "ackVersion": 1,
        "ackCode": 200,
        "ackDescription": "OK"
      }
    }
  },
  "$metadata": {
    "$model": "dtmi:com:contoso:Thermostat_X500;1",
  }
}
```

Hier volgt nog een voor beeld van een digitale dubbele. Deze is gebaseerd op een [model](concepts-models.md)en ondersteunt geen onderdelen:

```json
{
  "$dtId": "logical-digitaltwin-01",
  "avgTemperature": 70,
  "comfortIndex": 85,
  "$metadata": {
    "$model": "dtmi:com:contoso:Building;1",
    "$kind": "DigitalTwin",
    "avgTemperature": {
      "desiredValue": 72,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "comfortIndex": {
      "desiredValue": 90,
      "desiredVersion": 1,
      "ackVersion": 3,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

### <a name="digital-twin-relationship-change-notifications"></a>Meldingen over wijziging van digitale dubbele relatie

**Relatie wijzigings meldingen** worden geactiveerd wanneer een relatie van een digitale-dubbele verbinding wordt gemaakt, bijgewerkt of verwijderd. 

#### <a name="properties"></a>Eigenschappen

Dit zijn de velden in de hoofd tekst van een melding voor een wijziging van de rand.

| Name    | Waarde |
| --- | --- |
| `id` | Id van de melding, zoals een UUID of een item dat door de service wordt onderhouden. `source` + `id`is uniek voor elke afzonderlijke gebeurtenis |
| `source` | De naam van het Azure Digital Apparaatdubbels-exemplaar, zoals *mydigitaltwins.westus2.azuredigitaltwins.net* |
| `specversion` | 1.0 |
| `type` | `Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br>`Microsoft.DigitalTwins.Relationship.Delete`<br>`datacontenttype    application/json for Relationship.Create`<br>`application/json-patch+json for Relationship.Update` |
| `subject` | ID van de relatie, zoals`<twinID>/relationships/<relationshipName>/<edgeID>` |
| `time` | Tijds tempel voor wanneer de bewerking is opgetreden op de relatie |
| `sequence` | Waarde waarmee de positie van de gebeurtenis wordt uitgedrukt in een grotere geordende reeks gebeurtenissen. Services moeten een Volg nummer toevoegen aan alle meldingen om de volg orde aan te geven of hun eigen ordening op een andere manier te onderhouden. Het Volg nummer wordt met elk bericht verhoogd. De waarde wordt ingesteld op 1 als het object wordt verwijderd en opnieuw wordt gemaakt met dezelfde ID. |
| `sequencetype` | Meer informatie over hoe het volgorde veld wordt gebruikt. Met deze eigenschap kan bijvoorbeeld worden opgegeven dat de waarde een ondertekend 32-bits geheel getal moet zijn, dat begint bij 1 en telkens 1 keer wordt verhoogd. |

#### <a name="body-details"></a>Details van hoofd tekst

De hoofd tekst is de payload van een relatie, ook in JSON-indeling. Er wordt gebruikgemaakt van dezelfde indeling als een `GET` aanvraag voor een relatie via de [DIGITALTWINS-API](how-to-use-apis-sdks.md). 

Als u een relatie bijwerkt, worden de eigenschappen van de relatie gewijzigd. 

Hier volgt een voor beeld van een melding voor update relaties om een eigenschap bij te werken:

```json
[
  {
    "op": "replace",
    "path": "ownershipUser",
    "value": "user3"
  }
]
```

Voor `Relationship.Delete` is de hoofd tekst hetzelfde als de `GET` aanvraag en krijgt deze de meest recente status voordat deze wordt verwijderd.

Hier volgt een voor beeld van een melding voor het maken of verwijderen van een relatie:

```json
{
    "$relationshipId": "EdgeId1",
    "$sourceId": "building11",
    "$relationshipName": "Contains",
    "$targetId": "floor11",
    "ownershipUser": "user1",
    "ownershipDepartment": "Operations"
}
```

### <a name="digital-twin-change-notifications"></a>Digitale dubbele wijzigings meldingen

Er worden **digitale dubbele wijzigings meldingen** geactiveerd wanneer er een digitale twee worden bijgewerkt, zoals:
* Wanneer eigenschaps waarden of meta gegevens worden gewijzigd.
* Wanneer meta gegevens van een digitaal element of onderdeel worden gewijzigd. Een voor beeld van dit scenario is het wijzigen van het model van een digitale dubbele.

#### <a name="properties"></a>Eigenschappen

Dit zijn de velden in de hoofd tekst van een digitale, dubbele wijzigings melding.

| Name    | Waarde |
| --- | --- |
| `id` | Id van de melding, zoals een UUID of een item dat door de service wordt onderhouden. `source` + `id`is uniek voor elke afzonderlijke gebeurtenis |
| `source` | Naam van het IoT hub-of Azure Digital Apparaatdubbels-exemplaar, zoals *myhub.Azure-devices.net* of *mydigitaltwins.westus2.azuredigitaltwins.net*
| `specversion` | 1.0 |
| `type` | `Microsoft.DigitalTwins.Twin.Update` |
| `datacontenttype` | application/json-patch + JSON |
| `subject` | ID van de digitale dubbele |
| `time` | Tijds tempel voor wanneer de bewerking is opgetreden op de digitale twee |
| `sequence` | Waarde waarmee de positie van de gebeurtenis wordt uitgedrukt in een grotere geordende reeks gebeurtenissen. Services moeten een Volg nummer toevoegen aan alle meldingen om de volg orde aan te geven of hun eigen ordening op een andere manier te onderhouden. Het Volg nummer wordt met elk bericht verhoogd. De waarde wordt ingesteld op 1 als het object wordt verwijderd en opnieuw wordt gemaakt met dezelfde ID. |
| `sequencetype` | Meer informatie over hoe het volgorde veld wordt gebruikt. Met deze eigenschap kan bijvoorbeeld worden opgegeven dat de waarde een ondertekend 32-bits geheel getal moet zijn, dat begint bij 1 en telkens 1 keer wordt verhoogd. |

#### <a name="body-details"></a>Details van hoofd tekst

De hoofd tekst van de `Twin.Update` melding is een JSON-patch document met de update voor de digitale twee.

Stel bijvoorbeeld dat een digitaal is bijgewerkt met behulp van de volgende patch.

```json
[
  {
    "op": "replace",
    "path": "/mycomp/prop1",
    "value": {"a":3}
  }
]
```

De bijbehorende melding (als synchroon door de service wordt uitgevoerd, zoals Azure Digital Apparaatdubbels het bijwerken van een digitale dubbele) zou een hoofd tekst hebben als:

```json
[
    { "op": "replace", "path": "/myComp/prop1", "value": {"a": 3}},
    { "op": "replace", "path": "/myComp/$metadata/prop1",
        "value": {
            "desiredValue": { "a": 3 },
            "desiredVersion": 2,
                "ackCode": 200,
            "ackVersion": 2 
        }
    }
]
```

## <a name="next-steps"></a>Volgende stappen

Zie eind punten en routes maken voor het leveren van gebeurtenissen:
* [Instructies: eind punten en routes beheren](how-to-manage-routes.md)

Of lees meer over de Azure Digital Apparaatdubbels Api's en SDK-opties:
* [Instructies: de Azure Digital Apparaatdubbels-Api's en Sdk's gebruiken](how-to-use-apis-sdks.md)