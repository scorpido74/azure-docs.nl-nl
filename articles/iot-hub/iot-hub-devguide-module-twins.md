---
title: Informatie over Azure IoT Hub-moduletweelingen | Microsoft Documenten
description: Ontwikkelaarshandleiding - moduletweelingen gebruiken om status- en configuratiegegevens tussen IoT Hub en uw apparaten te synchroniseren
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/01/2020
ms.author: menchi
ms.openlocfilehash: 5ef6c4de288a764abbe434c5d84fc99e154f7492
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303593"
---
# <a name="understand-and-use-module-twins-in-iot-hub"></a>Moduletwins begrijpen en gebruiken in IoT Hub

In dit artikel wordt ervan uitgegaan dat u [apparaattweelingen begrijpen en gebruiken in IoT Hub.](iot-hub-devguide-device-twins.md) In IoT Hub u onder elke apparaatidentiteit maximaal 20 moduleidentiteiten maken. Elke module-identiteit genereert impliciet een moduletwin. Net als bij apparaattweelingen zijn moduletweelingen JSON-documenten die informatie over de status van de module opslaan, waaronder metagegevens, configuraties en voorwaarden. Azure IoT Hub onderhoudt een moduletwin voor elke module die u verbinding maakt met IoT Hub. 

Aan de apparaatzijde u met de SDK's van het IoT Hub-apparaat modules maken waarbij elk een onafhankelijke verbinding met IoT Hub opent. Met deze functionaliteit u afzonderlijke naamruimten gebruiken voor verschillende componenten op uw apparaat. Je hebt bijvoorbeeld een automaat met drie verschillende sensoren. Elke sensor wordt aangestuurd door verschillende afdelingen in uw bedrijf. U voor elke sensor een module maken. Op deze manier kan elke afdeling alleen taken of directe methoden naar de sensor sturen die ze controleren, waardoor conflicten en gebruikersfouten worden voorkomen.

 Module identiteit en module twin bieden dezelfde mogelijkheden als apparaat identiteit en apparaat twin, maar op een fijnere granulariteit. Deze fijnere granulariteit stelt geschikte apparaten, zoals apparaten op basis van het besturingssysteem of firmware-apparaten die meerdere componenten beheren, in staat om configuratie en voorwaarden voor elk van deze componenten te isoleren. Module identiteit en module tweelingen zorgen voor een beheer scheiding van zorgen bij het werken met IoT-apparaten die modulaire software componenten hebben. Wij streven ernaar om alle apparaat twin functionaliteit op module twin niveau per module twin algemene beschikbaarheid te ondersteunen. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

In dit artikel wordt beschreven:

* De structuur van de module twin: *tags,* *gewenste* en *gerapporteerde eigenschappen*.
* De bewerkingen die de modules en back-ends kunnen uitvoeren op module tweelingen.

Raadpleeg [communicatierichtlijnen voor apparaat tot cloud](iot-hub-devguide-d2c-guidance.md) voor richtlijnen voor het gebruik van gerapporteerde eigenschappen, device-to-cloud-berichten of het uploaden van bestanden.

Raadpleeg [communicatierichtlijnen voor cloud-to-device-communicatie](iot-hub-devguide-c2d-guidance.md) voor richtlijnen voor het gebruik van de gewenste eigenschappen, directe methoden of cloud-to-device-berichten.

## <a name="module-twins"></a>De tweelingen van de module

Module twins slaan module-gerelateerde informatie op die:

* Modules op het apparaat en IoT Hub kunnen worden gebruikt om de omstandigheden en configuratie van de module te synchroniseren.

* De back-end van de oplossing kan worden gebruikt om langlopende bewerkingen op te vragen en te targeten.

De levenscyclus van een moduletwin is gekoppeld aan de bijbehorende [module-identiteit.](iot-hub-devguide-identity-registry.md) Modules tweelingen worden impliciet gemaakt en verwijderd wanneer een module identiteit wordt gemaakt of verwijderd in IoT Hub.

Een moduletwin is een JSON-document met:

* **Tags**. Een gedeelte van het JSON-document waar de back-end van de oplossing van kan lezen en naar kan schrijven. Tags zijn niet zichtbaar voor modules op het apparaat. Tags zijn ingesteld voor het opvragen van doeleinden.

* **Gewenste eigenschappen.** Wordt samen met gerapporteerde eigenschappen gebruikt om de configuratie of voorwaarden van de module te synchroniseren. De back-end van de oplossing kan de gewenste eigenschappen instellen en de module-app kan ze lezen. De module-app kan ook meldingen ontvangen van wijzigingen in de gewenste eigenschappen.

* **Gerapporteerde eigenschappen**. Gebruikt samen met de gewenste eigenschappen om moduleconfiguratie of -omstandigheden te synchroniseren. De module-app kan gerapporteerde eigenschappen instellen en de back-end van de oplossing kan deze lezen en opvragen.

* **Eigenschappen van moduleidentiteit**. De hoofdmap van het twee JSON-document van de module bevat de alleen-lezen eigenschappen van de bijbehorende module-identiteit die is opgeslagen in het [identiteitsregister.](iot-hub-devguide-identity-registry.md)

![Architecturale vertegenwoordiging van apparaattweeling](./media/iot-hub-devguide-device-twins/module-twin.jpg)

In het volgende voorbeeld ziet u een twee JSON-document voor modules:

```json
{
    "deviceId": "devA",
    "moduleId": "moduleA",
    "etag": "AAAAAAAAAAc=", 
    "status": "enabled",
    "statusReason": "provisioned",
    "statusUpdateTime": "0001-01-01T00:00:00",
    "connectionState": "connected",
    "lastActivityTime": "2015-02-30T16:24:48.789Z",
    "cloudToDeviceMessageCount": 0, 
    "authenticationType": "sas",
    "x509Thumbprint": {     
        "primaryThumbprint": null, 
        "secondaryThumbprint": null 
    }, 
    "version": 2, 
    "tags": {
        "$etag": "123",
        "deploymentLocation": {
            "building": "43",
            "floor": "1"
        }
    },
    "properties": {
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            "$metadata" : {...},
            "$version": 1
        },
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            },
            "batteryLevel": 55,
            "$metadata" : {...},
            "$version": 4
        }
    }
}
```

In het hoofdobject zijn de eigenschappen van `tags` de `reported` `desired` module-identiteit en containerobjecten voor en beide en eigenschappen. De `properties` container bevat een aantal`$metadata` `$etag`alleen-lezen elementen ( , en `$version`) beschreven in de [module dubbele metadata](iot-hub-devguide-module-twins.md#module-twin-metadata) en optimistische [gelijktijdigheid](iot-hub-devguide-device-twins.md#optimistic-concurrency) secties.

### <a name="reported-property-example"></a>Voorbeeld van gerapporteerde eigenschap

In het vorige voorbeeld bevat `batteryLevel` de moduletwee een eigenschap die wordt gerapporteerd door de module-app. Deze eigenschap maakt het mogelijk om modules te bevragen en te bedienen op basis van het laatst gerapporteerde batterijniveau. Andere voorbeelden zijn de mogelijkheden voor modulerapportagemodule voor modules voor modules of connectiviteitsopties.

> [!NOTE]
> Gerapporteerde eigenschappen vereenvoudigen scenario's waarbij de back-end van de oplossing geïnteresseerd is in de laatst bekende waarde van een eigenschap. Gebruik [device-to-cloudberichten](iot-hub-devguide-messages-d2c.md) als de back-end van de oplossing telemetrie van de module moet verwerken in de vorm van sequenties van tijdstempelgebeurtenissen, zoals tijdreeksen.

### <a name="desired-property-example"></a>Voorbeeld van gewenste eigenschap

In het vorige `telemetryConfig` voorbeeld worden de gewenste en gerapporteerde eigenschappen van de module gebruikt door de back-end van de oplossing en de module-app om de telemetrieconfiguratie voor deze module te synchroniseren. Bijvoorbeeld:

1. De back-end van de oplossing stelt de gewenste eigenschap in met de gewenste configuratiewaarde. Hier is het gedeelte van het document met de gewenste eigenschapsset:

    ```json
    ...
    "desired": {
        "telemetryConfig": {
            "sendFrequency": "5m"
        },
        ...
    },
    ...
    ```

2. De module-app wordt onmiddellijk op de hoogte gebracht van de wijziging als deze is verbonden of bij de eerste verbinding. De module-app rapporteert vervolgens de bijgewerkte configuratie `status` (of een foutvoorwaarde met behulp van de eigenschap). Hier is het gedeelte van de gerapporteerde eigenschappen:

    ```json
    "reported": {
        "telemetryConfig": {
            "sendFrequency": "5m",
            "status": "success"
        }
        ...
    }
    ```

3. De back-end van de oplossing kan de resultaten van de configuratiebewerking in veel modules bijhouden door [moduletweelingen op te](iot-hub-devguide-query-language.md) vragen.

> [!NOTE]
> De voorgaande fragmenten zijn voorbeelden, geoptimaliseerd voor leesbaarheid, van een manier om een moduleconfiguratie en de status ervan te coderen. IoT Hub legt geen specifiek schema op voor de gewenste en gerapporteerde eigenschappen van de moduletweeling.
> 
> 

## <a name="back-end-operations"></a>Back-endbewerkingen
De back-end van de oplossing werkt op de moduletwin met behulp van de volgende atomaire bewerkingen, die worden weergegeven via HTTPS:

* **Module twin op ID ophalen**. Met deze bewerking retourneert het dubbele document van de module, inclusief tags en gewenste en gerapporteerde systeemeigenschappen.

* **Moduletwin gedeeltelijk bijwerken**. Deze bewerking stelt de oplossing back-end in staat om de tags of gewenste eigenschappen in een moduletwin gedeeltelijk bij te werken. De gedeeltelijke update wordt uitgedrukt als een JSON-document dat een eigenschap toevoegt of bijwerkt. Eigenschappen die `null` zijn ingesteld op worden verwijderd. In het volgende voorbeeld wordt `{"newProperty": "newValue"}`een nieuwe gewenste eigenschap `existingProperty` `"otherNewValue"`met waarde `otherOldProperty`gemaakt, wordt de bestaande waarde van met , overschrijft en verwijdert . Er worden geen andere wijzigingen aangebracht in bestaande gewenste eigenschappen of tags:

    ```json
    {
        "properties": {
            "desired": {
                "newProperty": {
                    "nestedProperty": "newValue"
                },
                "existingProperty": "otherNewValue",
                "otherOldProperty": null
            }
        }
    }
    ```

* **Vervang de gewenste eigenschappen**. Deze bewerking stelt de oplossing back-end in staat om alle bestaande `properties/desired`gewenste eigenschappen volledig te overschrijven en een nieuw JSON-document te vervangen door .

* **Tags vervangen**. Deze bewerking stelt de oplossing back-end in staat om alle `tags`bestaande tags volledig te overschrijven en een nieuw JSON-document te vervangen door .

* **Ontvang dubbele meldingen**. Met deze bewerking kan de back-end van de oplossing worden gemeld wanneer de tweeling wordt gewijzigd. Om dit te doen, moet uw IoT-oplossing een route maken en de gegevensbron gelijk stellen aan *twinChangeEvents.* Standaard worden er geen dubbele meldingen verzonden, dat wil zeggen dat dergelijke routes niet bestaan. Als de wijzigingssnelheid te hoog is of om andere redenen, zoals interne fouten, kan de IoT Hub slechts één melding verzenden die alle wijzigingen bevat. Als uw toepassing daarom betrouwbare controle en logboekregistratie van alle tussenliggende statussen nodig heeft, moet u device-to-cloud-berichten gebruiken. Het dubbele meldingsbericht bevat eigenschappen en lichaam.

  - Eigenschappen

    | Name | Waarde |
    | --- | --- |
    $content type | application/json |
    $iothub in de wachtrij |  Het tijdstip waarop de melding is verzonden |
    $iothub-berichtbron | twinChangeEvents |
    $content-codering | utf-8 |
    deviceId | ID van het apparaat |
    moduleId | ID van de module |
    hubNaam | Naam van IoT-hub |
    operationTimestamp | [ISO8601-tijdstempel](https://en.wikipedia.org/wiki/ISO_8601) van de werking |
    iothub-bericht-schema | twinChangeNotification |
    opType | "replaceTwin" of "updateTwin" |

    Eigenschappen van het berichtensysteem `$` zijn vooraf bevestigd met het symbool.

  - Hoofdtekst
        
    Deze sectie bevat alle dubbele wijzigingen in een JSON-indeling. Het maakt gebruik van hetzelfde formaat als een patch, met het verschil dat het alle dubbele secties kan bevatten: tags, properties.reported, properties.desired, en dat het de "$metadata" elementen bevat. Bijvoorbeeld:

    ```json
    {
      "properties": {
          "desired": {
              "$metadata": {
                  "$lastUpdated": "2016-02-30T16:24:48.789Z"
              },
              "$version": 1
          },
          "reported": {
              "$metadata": {
                  "$lastUpdated": "2016-02-30T16:24:48.789Z"
              },
              "$version": 1
          }
      }
    }
    ```

Alle voorgaande bewerkingen ondersteunen [optimistische gelijktijdigheid](iot-hub-devguide-device-twins.md#optimistic-concurrency) en vereisen de **ServiceConnect-toestemming,** zoals gedefinieerd in het artikel [Toegang tot IoT-hub beheren.](iot-hub-devguide-security.md)

Naast deze bewerkingen kan de back-end van de oplossing de moduletweeling opvragen met de SQL-achtige [IoT Hub-querytaal.](iot-hub-devguide-query-language.md)

## <a name="module-operations"></a>Modulebewerkingen

De module-app werkt op de moduletwin met behulp van de volgende atomaire bewerkingen:

* **Moduletwin ophalen**. Met deze bewerking retourneert het dubbele document van de module (inclusief tags en gewenste en gerapporteerde systeemeigenschappen) voor de momenteel verbonden module.

* **Gerapporteerde eigenschappen gedeeltelijk bijwerken**. Met deze bewerking kunnen de gerapporteerde eigenschappen van de momenteel verbonden module gedeeltelijk worden bijgewerkt. Deze bewerking maakt gebruik van dezelfde JSON-updateindeling die de back-end van de oplossing gebruikt voor een gedeeltelijke update van de gewenste eigenschappen.

* **Let op de gewenste eigenschappen.** De momenteel verbonden module kan ervoor kiezen om op de hoogte te worden gesteld van updates van de gewenste eigenschappen wanneer deze zich voordoen. De module ontvangt dezelfde vorm van update (gedeeltelijke of volledige vervanging) uitgevoerd door de oplossing back-end.

Alle voorgaande bewerkingen vereisen de **moduleconnect-machtiging,** zoals gedefinieerd in het artikel [Toegang tot IoT-hub beheren.](iot-hub-devguide-security.md)

Met de [Azure IoT-apparaat-SDK's](iot-hub-devguide-sdks.md) u de voorgaande bewerkingen van vele talen en platforms eenvoudig gebruiken.

## <a name="tags-and-properties-format"></a>Indeling voor tags en eigenschappen

Tags, gewenste eigenschappen en gerapporteerde eigenschappen zijn JSON-objecten met de volgende beperkingen:

* **Toetsen**: Alle toetsen in JSON-objecten zijn casegevoelige 64-bytes UTF-8 UNICODE-tekenreeksen. Toegestane tekens sluiten UNICODE-besturingselementtekens uit (segmenten `.`C0 `$`en C1), en , SP en .

* **Waarden:** Alle waarden in JSON-objecten kunnen van de volgende JSON-typen zijn: booleaan, getal, tekenreeks, object. Arrays zijn niet toegestaan.

    * Gehele getallen kunnen een minimumwaarde hebben van -4503599627370496 en een maximale waarde van 4503599627370495.

    * Tekenreekswaarden zijn UTF-8 gecodeerd en kunnen een maximale lengte van 512 bytes hebben.

* **Diepte:** Alle JSON-objecten in tags, gewenste en gerapporteerde eigenschappen kunnen een maximale diepte van 5 hebben. Het volgende object is bijvoorbeeld geldig:

    ```json
    {
        ...
        "tags": {
            "one": {
                "two": {
                    "three": {
                        "four": {
                            "five": {
                                "property": "value"
                            }
                        }
                    }
                }
            }
        },
        ...
    }
    ```

## <a name="module-twin-size"></a>Module dubbele grootte

IoT Hub dwingt een 8 KB-limiet af op de waarde van , en een groottelimiet van `tags`32 KB op de waarde van `properties/desired` en `properties/reported`. Deze totalen zijn exclusief alleen-lezen `$version`elementen `$metadata/$lastUpdated`zoals `$etag`, en .

Dubbele grootte wordt als volgt berekend:

* Voor elke eigenschap in het JSON-document berekent en voegt IoT Hub cumulatief de lengte van de sleutel en waarde van de eigenschap toe.

* Eigenschappensleutels worden beschouwd als utf8-gecodeerde tekenreeksen.

* Eenvoudige eigenschapswaarden worden beschouwd als utf8-gecodeerde tekenreeksen, numerieke waarden (8 bytes) of Booleaanse waarden (4 bytes).

* De grootte van utf8-gecodeerde tekenreeksen wordt berekend door alle tekens te tellen, met uitzondering van UNICODE-controletekens (segmenten C0 en C1).

* Complexe eigenschapswaarden (geneste objecten) worden berekend op basis van de totale grootte van de eigenschapssleutels en eigenschapswaarden die ze bevatten.

IoT Hub verwerpt met een fout alle bewerkingen die de grootte van die documenten boven de limiet zouden vergroten.

## <a name="module-twin-metadata"></a>Dubbele metagegevens module

IoT Hub behoudt de tijdstempel van de laatste update voor elk JSON-object in gewenste en gerapporteerde eigenschappen van moduletwee. De tijdstempels zijn in UTC en gecodeerd in de `YYYY-MM-DDTHH:MM:SS.mmmZ` [ISO8601-indeling](https://en.wikipedia.org/wiki/ISO_8601) .
Bijvoorbeeld:

```json
{
    ...
    "properties": {
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            "$metadata": {
                "telemetryConfig": {
                    "sendFrequency": {
                        "$lastUpdated": "2016-03-30T16:24:48.789Z"
                    },
                    "$lastUpdated": "2016-03-30T16:24:48.789Z"
                },
                "$lastUpdated": "2016-03-30T16:24:48.789Z"
            },
            "$version": 23
        },
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            },
            "batteryLevel": "55%",
            "$metadata": {
                "telemetryConfig": {
                    "sendFrequency": "5m",
                    "status": {
                        "$lastUpdated": "2016-03-31T16:35:48.789Z"
                    },
                    "$lastUpdated": "2016-03-31T16:35:48.789Z"
                },
                "batteryLevel": {
                    "$lastUpdated": "2016-04-01T16:35:48.789Z"
                },
                "$lastUpdated": "2016-04-01T16:24:48.789Z"
            },
            "$version": 123
        }
    }
    ...
}
```

Deze informatie wordt op elk niveau bewaard (niet alleen de bladeren van de JSON-structuur) om updates te behouden die objectsleutels verwijderen.

## <a name="optimistic-concurrency"></a>Optimistische gelijktijdigheid

Tags, gewenste en gerapporteerde eigenschappen ondersteunen allemaal optimistische gelijktijdigheid.
Tags hebben een ETag, volgens [RFC7232,](https://tools.ietf.org/html/rfc7232)die de JSON-weergave van de tag vertegenwoordigt. U ETags gebruiken in voorwaardelijke updatebewerkingen van de back-end van de oplossing om consistentie te garanderen.

Module twin gewenste en gerapporteerde eigenschappen hebben `$version` geen ETags, maar hebben een waarde die gegarandeerd incrementeel is. Net als bij een ETag kan de versie door de updatepartij worden gebruikt om de consistentie van updates af te dwingen. Bijvoorbeeld een module-app voor een gerapporteerde eigenschap of de oplossing back-end voor een gewenste eigenschap.

Versies zijn ook handig wanneer een observatie-agent (zoals de module-app die de gewenste eigenschappen observeert) races moet verzoenen tussen het resultaat van een ophaalbewerking en een updatemelding. De sectie [Apparaatreconnectiestroom](iot-hub-devguide-device-twins.md#device-reconnection-flow) biedt meer informatie. 

## <a name="next-steps"></a>Volgende stappen

Zie de volgende IoT Hub-zelfstudies om een aantal concepten uit te proberen die in dit artikel worden beschreven:

* [Aan de slag met de identiteit van de IoT Hub-module en moduletwin met .NET back-end en .NET-apparaat](iot-hub-csharp-csharp-module-twin-getstarted.md)
