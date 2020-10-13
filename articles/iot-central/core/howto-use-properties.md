---
title: Eigenschappen in een Azure IoT Central-oplossing gebruiken
description: Meer informatie over het gebruik van alleen-lezen en beschrijf bare eigenschappen in een Azure IoT Central-oplossing.
author: v-krghan
ms.author: v-krghan
ms.date: 08/12/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: eb949f6f0895743250ead0276692497432bfeed5
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/11/2020
ms.locfileid: "91940562"
---
# <a name="use-properties-in-an-azure-iot-central-solution"></a>Eigenschappen in een Azure IoT Central-oplossing gebruiken

In dit artikel leest u hoe u apparaateigenschappen kunt gebruiken die zijn gedefinieerd in een sjabloon in uw Azure IoT Central-toepassing.

Eigenschappen vertegenwoordigen waarden van het tijdstip. Een apparaat kan bijvoorbeeld een eigenschap gebruiken om de doel temperatuur te rapporteren die het probeert te bereiken. Met eigenschappen kunt u ook de status synchroniseren tussen uw apparaat en uw Azure IoT Central-toepassing. U kunt Beschrijf bare eigenschappen instellen vanuit Azure IoT Central.

U kunt ook Cloud eigenschappen definiëren in een Azure IoT Central-toepassing. Waarden van Cloud-eigenschappen worden nooit uitgewisseld met een apparaat en vallen buiten het bereik van dit artikel.

## <a name="define-your-properties"></a>Uw eigenschappen definiëren

Eigenschappen zijn gegevens velden die de status van uw apparaat vertegenwoordigen. Eigenschappen gebruiken om de duurzame status van het apparaat weer te geven, zoals de aan/uit-status van een apparaat. Eigenschappen kunnen ook basis eigenschappen van apparaten vertegenwoordigen, zoals de software versie van het apparaat. U kunt eigenschappen declareren als alleen-lezen of beschrijfbaar.

De volgende scherm afbeelding toont een eigenschaps definitie in een Azure IoT Central-toepassing.

![Scherm afbeelding met een eigenschaps definitie in een Azure IoT Central-toepassing.](./media/howto-use-properties/property-definition.png)

De volgende tabel bevat de configuratie-instellingen voor een eigenschaps mogelijkheid.

| Veld           | Beschrijving                                                                                                                                                                                                                        |
|-----------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Weergavenaam    | De weergave naam voor de waarde van de eigenschap die wordt gebruikt in dash boards en formulieren.                                                                                                                                                              |
| Naam            | De naam van de eigenschap. Azure IoT Central genereert een waarde voor dit veld van de weergave naam, maar u kunt indien nodig uw eigen waarde kiezen. Dit veld moet alfanumeriek zijn.                                                 |
| Type capaciteit | Eigenschap.                                                                                                                                                                                                                          |
| Semantisch type   | Het semantische type van de eigenschap, zoals de Tempe ratuur, de status of de gebeurtenis. De keuze van semantisch type bepaalt welke van de volgende velden beschikbaar zijn.                                                                       |
| Schema          | Het gegevens type van de eigenschap, zoals double, String of vector. Welke opties beschikbaar zijn, wordt bepaald door het semantische type. Schema is niet beschikbaar voor de semantische typen gebeurtenis en status.                                               |
| Beschrijfbaar       | Als de eigenschap niet schrijfbaar is, kan het apparaat eigenschaps waarden rapporteren aan Azure IoT Central. Als de eigenschap schrijfbaar is, kan het apparaat eigenschaps waarden rapporteren aan Azure IoT Central. Vervolgens kan Azure IoT Central bijgewerkte eigenschappen verzenden naar het apparaat. |
| Ernst        | Alleen beschikbaar voor het semantische gebeurtenis type. De ernst is **fout**, **informatie**of **waarschuwing**.                                                                                                                         |
| Status waarden    | Alleen beschikbaar voor het semantische type status. Definieer de mogelijke status waarden, die elk een weergave naam, naam, opsommings type en waarde hebben.                                                                                   |
| Eenheid            | Een eenheid voor de waarde van de eigenschap, zoals **mph**, **%** of ** &deg; C**.                                                                                                                                                              |
| Eenheid weer geven    | Een weergave-eenheid voor gebruik in dash boards en formulieren.                                                                                                                                                                                    |
| Opmerking         | Eventuele opmerkingen over de eigenschaps mogelijkheid.                                                                                                                                                                                        |
| Beschrijving     | Een beschrijving van de eigenschaps mogelijkheid.                                                                                                                                                                                          |

De eigenschappen kunnen ook worden gedefinieerd in een interface in een sjabloon, zoals hier wordt weer gegeven:

``` json
{
  "@type": "Property",
  "displayName": "Device State",
  "description": "The state of the device. Two states online/offline are available.",
  "name": "state",
  "schema": "boolean"
},
{
  "@type": "Property",
  "displayName": "Customer Name",
  "description": "The name of the customer currently operating the device.",
  "name": "name",
  "schema": "string",
  "writable": true
},
{
 "@type": "Property",
 "displayName": "Date ",
 "description": "The date on which the device is currently operating",
 "name": "date",
 "writable": true,
 "schema": "date"
},
{ 
 "@type": "Property",
 "displayName": "Location",
 "description": "The current location of the device",
 "name": "location",
 "writable": true,
 "schema": "geopoint"
},
{
 "@type": "Property",
 "displayName": "Vector Level",
 "description": "The Vector level of the device",
 "name": "vector",
 "writable": true,
 "schema": "vector"
}
```

In dit voor beeld worden vijf eigenschappen weer gegeven. Deze eigenschappen kunnen worden gerelateerd aan de eigenschaps definitie in de gebruikers interface, zoals hier wordt weer gegeven:

* `@type` het type mogelijkheid opgeven: `Property`
* `name` voor de waarde van de eigenschap.
* `schema` Geef het gegevens type voor de eigenschap op. Deze waarde kan een primitief type zijn, zoals double, integer, Boolean of string. Complexe object typen, matrices en Maps worden ook ondersteund.
* `writable` Eigenschappen zijn standaard alleen-lezen. U kunt een eigenschap markeren als beschrijfbaar met behulp van dit veld.

Met optionele velden, zoals weergave naam en-beschrijving, kunt u meer details toevoegen aan de interface en de mogelijkheden.

Wanneer u een eigenschap maakt, kunt u de complexe **schema** typen als object en Enum opgeven.

![Scherm afbeelding die laat zien hoe u een mogelijkheid kunt toevoegen.](./media/howto-use-properties/property.png)

Wanneer u het complexe **schema**selecteert, zoals **object**, moet u het object ook definiëren.

![Scherm afbeelding die laat zien hoe u een object definieert.](./media/howto-use-properties/object.png)

De volgende code toont de definitie van een eigenschaps type van een object. Dit object heeft twee velden met typen teken reeks en geheel getal.

``` json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "ObjectProperty"
  },
  "name": "ObjectProperty",
  "schema": {
    "@id": "<element id>",
    "@type": "Object",
    "displayName": {
      "en": "Object"
    },
    "fields": [
      {
        "@id": "<element id>",
        "@type": "SchemaField",
        "displayName": {
          "en": "Field1"
        },
        "name": "Field1",
        "schema": "integer"
      },
      {
        "@id": "<element id>",
        "@type": "SchemaField",
        "displayName": {
          "en": "Field2"
        },
        "name": "Field2",
        "schema": "string"
      }
    ]
  }
}
```

## <a name="implement-read-only-properties"></a>Alleen-lezen eigenschappen implementeren

Eigenschappen zijn standaard alleen-lezen. Alleen-lezen eigenschappen betekenen dat het apparaat de waarde van de eigenschapwaarde bijwerkt naar uw Azure IoT Central-toepassing. Uw Azure IoT Central-toepassing kan de waarde van een alleen-lezen eigenschap niet instellen.

Azure IoT Central maakt gebruik van apparaatdubbels om eigenschaps waarden te synchroniseren tussen het apparaat en de Azure IoT Central-toepassing. Waarden van eigenschappen van een apparaat gebruiken door apparaatdubbels gerapporteerde eigenschappen. Zie [device apparaatdubbels](https://docs.microsoft.com/azure/iot-hub/tutorial-device-twins)voor meer informatie.

Het volgende code fragment van een hulp model voor apparaten toont de definitie van een alleen-lezen eigenschaps type:

``` json
{
  "@type": "Property",
  "name": "model",
  "displayName": "Device model",
  "schema": "string",
  "comment": "Device model name or ID. Ex. Surface Book 2."
}
```

Alleen-lezen eigenschappen worden door het apparaat naar Azure IoT Central verzonden. De eigenschappen worden verzonden als JSON-nettolading. Zie [payloads](./concepts-telemetry-properties-commands.md)voor meer informatie.

U kunt de Azure IoT Device SDK gebruiken om een eigenschaps update naar uw Azure IoT Central-toepassing te verzenden.

Dubbele eigenschappen van het apparaat kunnen worden verzonden naar uw Azure IoT Central-toepassing met behulp van de volgende functie:

``` javascript
hubClient.getTwin((err, twin) => {
    const properties = {
        model: 'environmentalSensor1.0'
    };
    twin.properties.reported.update(properties, (err) => {
        console.log(err ? `error: ${err.toString()}` : `status: success` )
    });
});
```

In dit artikel wordt Node.js gebruikt voor eenvoud. Zie de volgende zelf studies voor meer informatie over de voor beelden van Device Application:

* [Een client toepassing maken en verbinden met uw Azure IoT Central-toepassing (Node.js)](tutorial-connect-device-nodejs.md)
* [Een client toepassing maken en verbinden met uw Azure IoT Central-toepassing (python)](tutorial-connect-device-python.md)

In de volgende weer gave in azure IoT Central-toepassing worden de eigenschappen weer gegeven die u kunt zien. De weer gave maakt de eigenschap **apparaat model** automatisch een _eigenschap met het kenmerk alleen-lezen_.

![Scherm opname van de weer gave van een alleen-lezen eigenschap.](./media/howto-use-properties/read-only.png)

## <a name="implement-writable-properties"></a>Beschrijf bare eigenschappen implementeren

Beschrijf bare eigenschappen worden ingesteld door een operator in de Azure IoT Central-toepassing op een formulier. De eigenschap wordt door Azure IoT Central naar het apparaat verzonden. Azure IoT Central verwacht een bevestiging van het apparaat.

In het volgende code fragment van een hulp model voor apparaten wordt de definitie van een beschrijfbaar eigenschaps type weer gegeven:

``` json
{
  "@type": "Property",
  "displayName": "Brightness Level",
  "description": "The brightness level for the light on the device. Can be specified as 1 (high), 2 (medium), 3 (low)",
  "name": "brightness",
  "writable": true,
  "schema": "long"
}
```

Een apparaatclient moet een JSON-nettolading verzenden die lijkt op het volgende voor beeld als een gerapporteerde eigenschap in het apparaat dubbele:

``` json
{ "Brightness Level": 2 }
```

Als u de Beschrijf bare eigenschappen wilt definiëren en verwerken waarop uw apparaat reageert, kunt u de volgende code gebruiken:

``` javascript
hubClient.getTwin((err, twin) => {
    twin.on('properties.desired.brightness', function(desiredBrightness) {
        console.log( `Received setting: ${desiredBrightness.value}` );
        var patch = {
            brightness: {
                value: desiredBrightness.value,
                ad: 'success',
                ac: 200,
                av: desiredBrightness.$version
            }
        }
        twin.properties.reported.update(patch, (err) => {
            console.log(err ? `error: ${err.toString()}` : `status: success` )
        });
    });
});
```

Het antwoord bericht moet de `ac` velden en bevatten `av` . Het veld `ad` is optioneel. Raadpleeg de volgende code fragmenten voor voor beelden:

* `ac` is een numeriek veld dat gebruikmaakt van de waarden in de volgende tabel.
* `av` is het versie nummer dat naar het apparaat wordt verzonden.
* `ad` is een beschrijving van een optie teken reeks.

| Waarde | Label | Beschrijving |
| ----- | ----- | ----------- |
| `'ac': 200` | Voltooid | De bewerking voor het wijzigen van de eigenschap is voltooid. |
| `'ac': 202` of `'ac': 201` | In behandeling | De bewerking voor het wijzigen van de eigenschap is in behandeling of wordt uitgevoerd. |
| `'ac': 4xx` | Fout | De aangevraagde eigenschaps wijziging is ongeldig of er is een fout opgetreden. |
| `'ac': 5xx` | Fout | Het apparaat heeft een onverwachte fout aangetroffen bij het verwerken van de aangevraagde wijziging. |


Zie [uw apparaten configureren vanuit een back-end-service](https://docs.microsoft.com/azure/iot-hub/tutorial-device-twins)voor meer informatie over apparaatdubbels.

Wanneer de operator een Beschrijf bare eigenschap in de Azure IoT Central-toepassing instelt, gebruikt de toepassing een door het apparaat dubbele gewenste eigenschap om de waarde naar het apparaat te verzenden. Het apparaat reageert vervolgens met behulp van een dubbele, gerapporteerde eigenschap van het apparaat. Wanneer Azure IoT Central de gerapporteerde eigenschaps waarde ontvangt, wordt de eigenschappen weergave met de status **geaccepteerd**bijgewerkt.

In de volgende weer gave worden de Beschrijf bare eigenschappen weer gegeven. Wanneer u de waarde invoert en **Opslaan**selecteert, is de initiële status **in behandeling**. Wanneer het apparaat de wijziging accepteert, wordt de status gewijzigd in **geaccepteerd**.

![Scherm opname waarin de status in behandeling wordt weer gegeven.](./media/howto-use-properties/status-pending.png)

![Scherm opname van de geaccepteerde eigenschap.](./media/howto-use-properties/accepted.png)

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u eigenschappen in uw Azure IoT Central-toepassing kunt gebruiken, raadpleegt u:

* [Nettoladingen](concepts-telemetry-properties-commands.md)
* [Een client toepassing maken en verbinden met uw Azure IoT Central-toepassing (Node.js)](tutorial-connect-device-nodejs.md)
