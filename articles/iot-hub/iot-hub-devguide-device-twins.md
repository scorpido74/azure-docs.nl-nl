---
title: Informatie over Azure IoT Hub-apparaattweelingen | Microsoft Documenten
description: Handleiding voor ontwikkelaars - gebruik apparaattweelingen om status- en configuratiegegevens tussen IoT Hub en uw apparaten te synchroniseren
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/01/2020
ms.openlocfilehash: 251c9c9717bae1728bffa48827a45d4535d66c15
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81482076"
---
# <a name="understand-and-use-device-twins-in-iot-hub"></a>Apparaattweelingen begrijpen en gebruiken in IoT Hub

*Apparaattweelingen* zijn JSON-documenten die apparaatstatusgegevens opslaan, waaronder metagegevens, configuraties en voorwaarden. Azure IoT Hub onderhoudt een apparaatdubbel voor elk apparaat dat u verbindt met IoT Hub. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

In dit artikel wordt beschreven:

* De structuur van het apparaat twin: *tags,* *gewenste* en *gerapporteerde eigenschappen*.
* De bewerkingen die apparaat-apps en back-ends kunnen uitvoeren op apparaattweelingen.

Gebruik apparaattweelingen om:

* Bewaar apparaatspecifieke metagegevens in de cloud. Bijvoorbeeld de inzetlocatie van een automaat.

* Rapporteer actuele statusgegevens, zoals beschikbare mogelijkheden en voorwaarden, vanuit uw apparaat-app. Een apparaat is bijvoorbeeld verbonden met uw IoT-hub via mobiele apparaten of WiFi.

* Synchroniseer de status van langlopende werkstromen tussen apparaat-app en back-end-app. Wanneer de back-end van de oplossing bijvoorbeeld de nieuwe firmwareversie opgeeft die moet worden geïnstalleerd, rapporteert de apparaat-app de verschillende fasen van het updateproces.

* Query uw apparaat metagegevens, configuratie of status.

Raadpleeg [communicatierichtlijnen voor apparaat tot cloud](iot-hub-devguide-d2c-guidance.md) voor richtlijnen voor het gebruik van gerapporteerde eigenschappen, device-to-cloud-berichten of het uploaden van bestanden.

Raadpleeg [communicatierichtlijnen voor cloud-to-device-communicatie](iot-hub-devguide-c2d-guidance.md) voor richtlijnen voor het gebruik van de gewenste eigenschappen, directe methoden of cloud-to-device-berichten.

## <a name="device-twins"></a>De tweelingen van het apparaat

Apparaattweelingen slaan apparaatgerelateerde informatie op die:

* Apparaat en back-ends kunnen worden gebruikt om apparaatomstandigheden en configuratie te synchroniseren.

* De back-end van de oplossing kan worden gebruikt om langlopende bewerkingen op te vragen en te targeten.

De levenscyclus van een apparaattweeling is gekoppeld aan de bijbehorende [apparaatidentiteit.](iot-hub-devguide-identity-registry.md) Apparaattweelingen worden impliciet gemaakt en verwijderd wanneer een apparaatidentiteit wordt gemaakt of verwijderd in IoT Hub.

Een apparaattweeling is een JSON-document met:

* **Tags**. Een gedeelte van het JSON-document waar de back-end van de oplossing van kan lezen en naar kan schrijven. Tags zijn niet zichtbaar voor apparaat-apps.

* **Gewenste eigenschappen.** Wordt samen met gerapporteerde eigenschappen gebruikt om de configuratie of omstandigheden van het apparaat te synchroniseren. De back-end van de oplossing kan de gewenste eigenschappen instellen en de apparaat-app kan ze lezen. De apparaat-app kan ook meldingen ontvangen van wijzigingen in de gewenste eigenschappen.

* **Gerapporteerde eigenschappen**. Gebruikt samen met de gewenste eigenschappen om apparaatconfiguratie of -omstandigheden te synchroniseren. De apparaat-app kan gerapporteerde eigenschappen instellen en de back-end van de oplossing kan deze lezen en opvragen.

* **Eigenschappen van apparaatidentiteit**. De hoofdmap van het apparaat twin JSON-document bevat de alleen-lezen eigenschappen van de bijbehorende apparaatidentiteit die is opgeslagen in het [identiteitsregister.](iot-hub-devguide-identity-registry.md) Eigenschappen `connectionStateUpdatedTime` `generationId` en worden niet opgenomen.

![Schermafbeelding van de dubbele eigenschappen van het apparaat](./media/iot-hub-devguide-device-twins/twin.png)

In het volgende voorbeeld ziet u een apparaatdubbel JSON-document:

```json
{
    "deviceId": "devA",
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

In het hoofdobject zijn de eigenschappen van `tags` de `reported` `desired` apparaatidentiteit en containerobjecten voor en beide en eigenschappen. De `properties` container bevat een aantal`$metadata` `$etag`alleen-lezen elementen ( , en `$version`) beschreven in het [apparaat dubbele metadata](iot-hub-devguide-device-twins.md#device-twin-metadata) en optimistische [gelijktijdigheid](iot-hub-devguide-device-twins.md#optimistic-concurrency) secties.

### <a name="reported-property-example"></a>Voorbeeld van gerapporteerde eigenschap

In het vorige voorbeeld bevat `batteryLevel` de apparaattweeling een eigenschap die wordt gerapporteerd door de apparaat-app. Deze eigenschap maakt het mogelijk om te zoeken en te werken op apparaten op basis van het laatst gerapporteerde batterijniveau. Andere voorbeelden zijn de mogelijkheden voor apparaatrapportage van de apparaatapp of connectiviteitsopties.

> [!NOTE]
> Gerapporteerde eigenschappen vereenvoudigen scenario's waarbij de back-end van de oplossing geïnteresseerd is in de laatst bekende waarde van een eigenschap. Gebruik [device-to-cloudberichten](iot-hub-devguide-messages-d2c.md) als de back-end van de oplossing telemetrie van het apparaat moet verwerken in de vorm van sequenties van tijdstempelgebeurtenissen, zoals tijdreeksen.

### <a name="desired-property-example"></a>Voorbeeld van gewenste eigenschap

In het vorige `telemetryConfig` voorbeeld worden de gewenste en gerapporteerde eigenschappen van het apparaat gebruikt door de back-end van de oplossing en de apparaat-app om de telemetrieconfiguratie voor dit apparaat te synchroniseren. Bijvoorbeeld:

1. De back-end van de oplossing stelt de gewenste eigenschap in met de gewenste configuratiewaarde. Hier is het gedeelte van het document met de gewenste eigenschapsset:

   ```json
   "desired": {
       "telemetryConfig": {
           "sendFrequency": "5m"
       },
       ...
   },
   ```

2. De apparaat-app wordt onmiddellijk op de hoogte gebracht van de wijziging als deze is verbonden of bij de eerste verbinding. De apparaat-app rapporteert vervolgens de bijgewerkte configuratie `status` (of een foutvoorwaarde met behulp van de eigenschap). Hier is het gedeelte van de gerapporteerde eigenschappen:

   ```json
   "reported": {
       "telemetryConfig": {
           "sendFrequency": "5m",
           "status": "success"
       }
       ...
   }
   ```

3. De back-end van de oplossing kan de resultaten van de configuratiebewerking op veel apparaten bijhouden door [apparaattweelingen op te vragen.](iot-hub-devguide-query-language.md)

> [!NOTE]
> De voorgaande fragmenten zijn voorbeelden, geoptimaliseerd voor leesbaarheid, van een manier om een apparaatconfiguratie en de status ervan te coderen. IoT Hub legt geen specifiek schema op voor de gewenste en gerapporteerde eigenschappen van het apparaat in de apparaattweeling.
> 

U tweelingen gebruiken om langlopende bewerkingen te synchroniseren, zoals firmware-updates. Zie De [gewenste eigenschappen gebruiken om apparaten te configureren voor](tutorial-device-twins.md)meer informatie over het gebruik van eigenschappen om een langdurige bewerking op verschillende apparaten te synchroniseren en bij te houden.

## <a name="back-end-operations"></a>Back-endbewerkingen

De back-end van de oplossing werkt op de apparaattweeling met behulp van de volgende atoombewerkingen, die worden weergegeven via HTTPS:

* **Apparaattwin ophalen op id**. Met deze bewerking retourneert het dubbele document van het apparaat, inclusief tags en gewenste en gerapporteerde systeemeigenschappen.

* **Apparaattweeling gedeeltelijk bijwerken**. Met deze bewerking kan de back-end van de oplossing de tags of de gewenste eigenschappen in een apparaattweeling gedeeltelijk bijwerken. De gedeeltelijke update wordt uitgedrukt als een JSON-document dat een eigenschap toevoegt of bijwerkt. Eigenschappen die `null` zijn ingesteld op worden verwijderd. In het volgende voorbeeld wordt `{"newProperty": "newValue"}`een nieuwe gewenste eigenschap `existingProperty` `"otherNewValue"`met waarde `otherOldProperty`gemaakt, wordt de bestaande waarde van met , overschrijft en verwijdert . Er worden geen andere wijzigingen aangebracht in bestaande gewenste eigenschappen of tags:

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

* **Ontvang dubbele meldingen**. Met deze bewerking kan de back-end van de oplossing worden gemeld wanneer de tweeling wordt gewijzigd. Om dit te doen, moet uw IoT-oplossing een route maken en de gegevensbron gelijk stellen aan *twinChangeEvents.* Standaard bestaan dergelijke routes niet, dus er worden geen dubbele meldingen verzonden. Als de wijzigingssnelheid te hoog is of om andere redenen, zoals interne fouten, kan de IoT Hub slechts één melding verzenden die alle wijzigingen bevat. Als uw toepassing daarom betrouwbare controle en logboekregistratie van alle tussenliggende statussen nodig heeft, moet u device-to-cloud-berichten gebruiken. Het dubbele meldingsbericht bevat eigenschappen en lichaam.

  - Eigenschappen

    | Naam | Waarde |
    | --- | --- |
    $content type | application/json |
    $iothub in de wachtrij |  Het tijdstip waarop de melding is verzonden |
    $iothub-berichtbron | twinChangeEvents |
    $content-codering | utf-8 |
    deviceId | ID van het apparaat |
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

Alle voorgaande bewerkingen ondersteunen [optimistische gelijktijdigheid](iot-hub-devguide-device-twins.md#optimistic-concurrency) en vereisen de **ServiceConnect-toestemming,** zoals gedefinieerd in [Controletoegang tot IoT Hub](iot-hub-devguide-security.md).

Naast deze bewerkingen kan de back-end van de oplossing:

* Vraag de apparaattweeling op met de SQL-achtige [IoT Hub-querytaal](iot-hub-devguide-query-language.md).

* Voer bewerkingen uit op grote sets apparaattweelingen met [taken.](iot-hub-devguide-jobs.md)

## <a name="device-operations"></a>Apparaatbewerkingen

De apparaat-app werkt op de apparaattweeling met behulp van de volgende atoombewerkingen:

* **Apparaattweeling ophalen**. Met deze bewerking retourneert het apparaattweedocument (inclusief gewenste en gerapporteerde systeemeigenschappen) voor het momenteel verbonden apparaat. (Tags zijn niet zichtbaar voor apparaat-apps.)

* **Gerapporteerde eigenschappen gedeeltelijk bijwerken**. Met deze bewerking u de gerapporteerde eigenschappen van het apparaat gedeeltelijk bijwerken. Deze bewerking maakt gebruik van dezelfde JSON-updateindeling die de back-end van de oplossing gebruikt voor een gedeeltelijke update van de gewenste eigenschappen.

* **Let op de gewenste eigenschappen.** Het momenteel verbonden apparaat kan ervoor kiezen om op de hoogte te worden gesteld van updates van de gewenste eigenschappen wanneer deze zich voordoen. Het apparaat ontvangt dezelfde vorm van update (gedeeltelijke of volledige vervanging) uitgevoerd door de oplossing back-end.

Alle voorgaande bewerkingen vereisen de **Toestemming van DeviceConnect,** zoals gedefinieerd in [Controletoegang tot IoT Hub.](iot-hub-devguide-security.md)

Met de [Azure IoT-apparaat-SDK's](iot-hub-devguide-sdks.md) u de voorgaande bewerkingen van vele talen en platforms eenvoudig gebruiken. Zie [Apparaatreconnectiestroom](iot-hub-devguide-device-twins.md#device-reconnection-flow)voor meer informatie over de details van IoT Hub-primitieven voor de gewenste eigenschappensynchronisatie.

## <a name="tags-and-properties-format"></a>Indeling voor tags en eigenschappen

Tags, gewenste eigenschappen en gerapporteerde eigenschappen zijn JSON-objecten met de volgende beperkingen:

* **Toetsen:** Alle toetsen in JSON-objecten zijn UTF-8 gecodeerd, hoofdlettergevoelig en maximaal 1 KB lang. Toegestane tekens sluiten UNICODE-besturingselementtekens uit (segmenten `.` `$`C0 en C1) en , en SP.

* **Waarden:** Alle waarden in JSON-objecten kunnen van de volgende JSON-typen zijn: booleaan, getal, tekenreeks, object. Arrays zijn niet toegestaan.

    * Gehele getallen kunnen een minimumwaarde hebben van -4503599627370496 en een maximale waarde van 4503599627370495.

    * Tekenreekswaarden zijn UTF-8 gecodeerd en kunnen een maximale lengte van 4 KB hebben.

* **Diepte:** De maximale diepte van JSON-objecten in tags, gewenste eigenschappen en gerapporteerde eigenschappen is 10. Het volgende object is bijvoorbeeld geldig:

   ```json
   {
       ...
       "tags": {
           "one": {
               "two": {
                   "three": {
                       "four": {
                           "five": {
                               "six": {
                                   "seven": {
                                       "eight": {
                                           "nine": {
                                               "ten": {
                                                   "property": "value"
                                               }
                                           }
                                       }
                                   }
                               }
                           }
                       }
                   }
               }
           }
       },
       ...
   }
   ```

## <a name="device-twin-size"></a>Apparaat dubbele grootte

IoT Hub dwingt een 8 KB-limiet af op de waarde van , en een groottelimiet van `tags`32 KB op de waarde van `properties/desired` en `properties/reported`. Deze totalen zijn exclusief alleen-lezen `$version`elementen `$metadata/$lastUpdated`zoals `$etag`, en .

Dubbele grootte wordt als volgt berekend:

* Voor elke eigenschap in het JSON-document berekent en voegt IoT Hub cumulatief de lengte van de sleutel en waarde van de eigenschap toe.

* Eigenschappensleutels worden beschouwd als utf8-gecodeerde tekenreeksen.

* Eenvoudige eigenschapswaarden worden beschouwd als utf8-gecodeerde tekenreeksen, numerieke waarden (8 bytes) of Booleaanse waarden (4 bytes).

* De grootte van utf8-gecodeerde tekenreeksen wordt berekend door alle tekens te tellen, met uitzondering van UNICODE-controletekens (segmenten C0 en C1).

* Complexe eigenschapswaarden (geneste objecten) worden berekend op basis van de totale grootte van de eigenschapssleutels en eigenschapswaarden die ze bevatten.

IoT Hub verwerpt met een fout alle `tags`bewerkingen die de grootte van de , `properties/desired`of `properties/reported` documenten boven de limiet zouden vergroten.

## <a name="device-twin-metadata"></a>Dubbele metagegevens van het apparaat

IoT Hub behoudt de tijdstempel van de laatste update voor elk JSON-object in gewenste en gerapporteerde eigenschappen van apparaattweeling. De tijdstempels zijn in UTC en gecodeerd in de `YYYY-MM-DDTHH:MM:SS.mmmZ` [ISO8601-indeling](https://en.wikipedia.org/wiki/ISO_8601) .

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

Apparaat tweeling gewenste en gerapporteerde eigenschappen hebben `$version` geen ETags, maar hebben een waarde die gegarandeerd incrementeel is. Net als bij een ETag kan de versie door de updatepartij worden gebruikt om de consistentie van updates af te dwingen. Bijvoorbeeld een apparaat-app voor een gerapporteerde eigenschap of de oplossing back-end voor een gewenste eigenschap.

Versies zijn ook handig wanneer een observatie-agent (zoals de apparaat-app die de gewenste eigenschappen observeert) races moet verzoenen tussen het resultaat van een ophaalbewerking en een updatemelding. De [sectie Apparaatkoppelingsstroom](iot-hub-devguide-device-twins.md#device-reconnection-flow) biedt meer informatie.

## <a name="device-reconnection-flow"></a>Apparaatkoppelingsstroom

IoT Hub behoudt de gewenste meldingen voor het bijwerken van de gewenste eigenschappen niet voor losgekoppelde apparaten. Hieruit volgt dat een apparaat dat verbinding maakt, het volledige gewenste eigenschappendocument moet ophalen, naast een abonnement op updatemeldingen. Gezien de mogelijkheid van races tussen updatemeldingen en volledige terugwinning, moet de volgende stroom worden gewaarborgd:

1. Apparaat-app maakt verbinding met een IoT-hub.
2. Apparaat-app abonneert zich op de gewenste meldingen voor het bijwerken van eigenschappen.
3. Apparaat-app haalt het volledige document op voor de gewenste eigenschappen.

De apparaat-app kan `$version` alle meldingen negeren die minder of gelijk zijn aan de versie van het volledige opgehaalde document. Deze aanpak is mogelijk omdat IoT Hub garandeert dat versies altijd toenemen.

> [!NOTE]
> Deze logica is al geïmplementeerd in de [Azure IoT-apparaat SDKs](iot-hub-devguide-sdks.md). Deze beschrijving is alleen handig als de apparaat-app geen Azure IoT-apparaat SDK's kan gebruiken en de MQTT-interface rechtstreeks moet programmeren.
> 

## <a name="additional-reference-material"></a>Aanvullend referentiemateriaal

Andere referentieonderwerpen in de IoT Hub-ontwikkelaarshandleiding zijn:

* In het artikel [Voor eindpunten van IoT Hub](iot-hub-devguide-endpoints.md) worden de verschillende eindpunten beschreven die elke IoT-hub blootlegt voor run-time- en beheerbewerkingen.

* In het artikel [Beperking en quota](iot-hub-devguide-quotas-throttling.md) worden de quota beschreven die van toepassing zijn op de IoT Hub-service en het beperkingsgedrag dat u verwachten wanneer u de service gebruikt.

* In het artikel [Azure IoT-apparaat en service-SDKs](iot-hub-devguide-sdks.md) worden de verschillende taal-SDK's weergegeven die u gebruiken wanneer u zowel apparaat- als service-apps ontwikkelt die met IoT Hub werken.

* De [IoT Hub-querytaal voor apparaattweelingen, taken en berichtrouteringsartikel](iot-hub-devguide-query-language.md) beschrijft de IoT Hub-querytaal die u gebruiken om informatie uit IoT Hub op te halen over uw apparaattweeling en -taken.

* Het [IoT Hub MQTT-ondersteuningsartikel](iot-hub-mqtt-support.md) biedt meer informatie over IoT Hub-ondersteuning voor het MQTT-protocol.

## <a name="next-steps"></a>Volgende stappen

Nu u meer te weten bent gekomen over apparaattweelingen, bent u mogelijk geïnteresseerd in de volgende onderwerpen voor IoT Hub-ontwikkelaars:

* [Moduletwins begrijpen en gebruiken in IoT Hub](iot-hub-devguide-module-twins.md)
* [Een directe methode op een apparaat aanroepen](iot-hub-devguide-direct-methods.md)
* [Taken op meerdere apparaten plannen](iot-hub-devguide-jobs.md)

Zie de volgende IoT Hub-zelfstudies om een aantal concepten uit te proberen die in dit artikel worden beschreven:

* [Hoe het apparaat twin te gebruiken](iot-hub-node-node-twin-getstarted.md)
* [Dubbele eigenschappen van het apparaat gebruiken](tutorial-device-twins.md)
* [Apparaatbeheer met Azure IoT Tools voor VS Code](iot-hub-device-management-iot-toolkit.md)
