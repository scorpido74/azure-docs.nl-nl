---
title: Uw gegevens exporteren naar Azure Event Hubs en Azure Service Bus | Microsoft Docs
description: Gegevens exporteren uit uw Azure IoT Central-toepassing naar Azure Event Hubs en Azure Service Bus
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/09/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: fed9c924274cb66671e233a7dc6d431d81e0dbfb
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973217"
---
# <a name="export-your-data-in-azure-iot-central-preview-features"></a>Uw gegevens exporteren in azure IoT Central (preview-functies)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

*Dit onderwerp is van toepassing op beheerders.*

In dit artikel wordt beschreven hoe u de functie continue gegevens export in azure IoT Central kunt gebruiken om uw gegevens te exporteren naar uw eigen **Azure-Event hubs**en **Azure service bus** exemplaren. U kunt **telemetrie**, **apparaten**en **Apparaatinstellingen** exporteren naar uw eigen bestemming voor inzichten en analyse van warme paden. Dit omvat het activeren van aangepaste regels in Azure Stream Analytics, het activeren van aangepaste werk stromen in Azure Logic Apps, of het door geven via Azure Functions door lopen om te worden getransformeerd.

> [!Note]
> Wanneer u de continue gegevens export inschakelt, ontvangt u de gegevens vanaf dat moment. Op dit moment kunnen geen gegevens worden opgehaald voor een tijd dat de continue gegevens export is uitgeschakeld. Als u meer historische gegevens wilt behouden, moet u de continue gegevens export voor tijdig inschakelen.

## <a name="prerequisites"></a>Vereisten

- U moet een beheerder zijn in uw IoT Central-toepassing

## <a name="set-up-export-destination"></a>Export bestemming instellen

Als u geen bestaande Event Hubs/Service Bus kunt exporteren naar, voert u de volgende stappen uit om een te maken:

### <a name="create-event-hubs-namespace"></a>Event Hubs naam ruimte maken

1. Maak een [nieuwe event hubs naam ruimte in de Azure Portal](https://ms.portal.azure.com/#create/Microsoft.EventHub). Meer informatie vindt u in [Azure Event hubs docs](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).
2. Kies een abonnement. 

    > [!Note] 
    > U kunt nu gegevens exporteren naar andere abonnementen die **niet gelijk** zijn aan die voor uw betalen naar gebruik-IOT Central toepassing. In dit geval kunt u verbinding maken met behulp van een connection string.
3. Maak een Event Hub in uw Event Hubs naam ruimte. Ga naar uw naam ruimte en selecteer **+ Event hub** aan de bovenkant om een event hub-exemplaar te maken.

### <a name="create-service-bus-namespace"></a>Service Bus naam ruimte maken

1. Maak een [nieuwe service bus naam ruimte in de Azure Portal](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5) . Meer informatie vindt u in [Azure service bus docs](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-create-namespace-portal).
2. Kies een abonnement. 

    > [!Note] 
    > U kunt nu gegevens exporteren naar andere abonnementen die **niet gelijk** zijn aan die voor uw betalen naar gebruik-IOT Central toepassing. In dit geval kunt u verbinding maken met behulp van een connection string.

3. Ga naar uw Service Bus-naam ruimte en selecteer **+ wachtrij** of **+ onderwerp** bovenaan om een wachtrij of onderwerp te maken waarnaar u wilt exporteren.


## <a name="set-up-continuous-data-export"></a>Continue gegevens export instellen

Nu u een Event Hubs/Service Bus bestemming hebt om gegevens te exporteren naar, voert u de volgende stappen uit om continue gegevens export in te stellen. 

1. Meld u aan bij uw IoT Central-toepassing.

2. Selecteer in het menu links de optie **gegevens exporteren**.

    > [!Note]
    > Als het exporteren van gegevens niet in het linkermenu wordt weer gegeven, bent u geen beheerder in uw app. Neem contact op met een beheerder om het exporteren van gegevens in te stellen.

3. Selecteer de knop **+ Nieuw** in de rechter bovenhoek. Kies een van de **Azure-Event hubs** of **Azure service bus** als bestemming voor de export. 

    > [!NOTE] 
    > Het maximum aantal exports per app is vijf. 

    ![Nieuwe continue gegevens export maken](media/howto-export-data-pnp/export-new2.png)

4. Selecteer uw **Event hubs naam ruimte/service bus naam ruimte**in de vervolg keuzelijst. U kunt ook de laatste optie in de lijst kiezen die **een connection string invoert**. 

    > [!NOTE] 
    > U ziet alleen opslag accounts/Event Hubs naam ruimten/Service Bus naam ruimten in **hetzelfde abonnement als uw IOT Central-app**. Als u wilt exporteren naar een bestemming buiten dit abonnement, kiest u **een Connection String invoeren** en gaat u naar stap 5.

    > [!NOTE] 
    > Voor apps met een proef versie van 7 dagen is de enige manier om continue gegevens export te configureren via een connection string. De reden hiervoor is dat apps met een proef versie van zeven dagen geen bijbehorend Azure-abonnement hebben.

    ![Nieuwe CDE Event hub maken](media/howto-export-data-pnp/export-eh.png)

5. Beschrijving Als u **een connection string invoert**, wordt er een nieuw vak weer gegeven waarin u uw Connection String kunt plakken. Ga als volgt te connection string:
    - Event Hubs of Service Bus gaat u naar de naam ruimte in de Azure Portal.
        - Onder **instellingen**selecteert u **beleid voor gedeelde toegang**
        - De standaard **RootManageSharedAccessKey** kiezen of een nieuwe maken
        - De primaire of secundaire connection string kopiëren

6. Kies een event hub/wachtrij of onderwerp in de vervolg keuzelijst.

7. Geef bij te **exporteren gegevens**elk type gegevens op dat moet worden geëxporteerd door het type in te stellen **op on**.

8. Als u continue gegevens export wilt inschakelen, moet u ervoor zorgen dat de wissel knop voor het **exporteren van gegevens** is **ingeschakeld**. Selecteer **Opslaan**.

9. Na enkele minuten worden uw gegevens weer gegeven in de bestemming die u hebt gekozen.


## <a name="data-format"></a>Gegevensindeling

Gegevens voor telemetrie, apparaten en apparaatinstellingen worden naar uw Event Hub of Service Bus-wachtrij of-onderwerp in bijna realtime geëxporteerd. Geëxporteerde telemetriegegevens bevatten het volledige bericht dat uw apparaten naar IoT Central zijn verzonden, niet alleen de telemetrie-waarden zelf. Gegevens van geëxporteerde apparaten bevatten wijzigingen in eigenschappen en meta gegevens van alle apparaten, en geëxporteerde Apparaatinstellingen bevatten wijzigingen in alle sjablonen voor apparaten. De geëxporteerde gegevens bevindt zich in de eigenschap Body en bevindt zich in JSON-indeling.

> [!NOTE]
> Bij het kiezen van een Service Bus als een export doel, mogen de wacht rijen en onderwerpen **geen sessies of duplicaten detectie zijn ingeschakeld**. Als een van deze opties is ingeschakeld, arriveert sommige berichten niet in uw wachtrij of onderwerp.

### <a name="telemetry"></a>Telemetrie

Er wordt snel een nieuw bericht geëxporteerd nadat IoT Central het bericht van een apparaat ontvangt. Elk geëxporteerd bericht in Event Hubs en Service Bus bevat het volledige bericht het apparaat dat is verzonden in de eigenschap Body in JSON-indeling. 

> [!NOTE]
> De apparaten die de telemetrie verzenden, worden vertegenwoordigd door apparaat-Id's (Zie de volgende secties). Als u de namen van de apparaten wilt ophalen, gegevens van apparaten wilt exporteren en elk bericht wilt correleren met behulp van de **connectionDeviceId** die overeenkomt met de **deviceId** van het apparaat.

In het volgende voor beeld wordt een bericht weer gegeven over telemetriegegevens die zijn ontvangen in Event Hub of Service Bus wachtrij of onderwerp.

```json
{ 
  "body":{ 
    "temp":67.96099945281145,
    "humid":58.51139305465015,
    "pm25":36.91162432340187
  },
  "annotations":{ 
    "iothub-connection-device-id":"<deviceId>",
    "iothub-connection-auth-method":"{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id":"<generationId>",
    "iothub-enqueuedtime":1539381029965,
    "iothub-message-source":"Telemetry",
    "x-opt-sequence-number":25325,
    "x-opt-offset":"<offset>",
    "x-opt-enqueued-time":1539381030200
  },
  "sequenceNumber":25325,
  "enqueuedTimeUtc":"2018-10-12T21:50:30.200Z",
  "offset":"<offset>",
  "properties":{ 
    "content_type":"application/json",
    "content_encoding":"utf-8"
  }
}
```

### <a name="devices"></a>Apparaten

Berichten met apparaatgegevens worden elke paar minuten verzonden naar uw Event Hub of Service Bus wachtrij of onderwerp. Dit betekent dat elke paar minuten een batch berichten ontvangt met gegevens over
- Nieuwe apparaten die zijn toegevoegd
- Apparaten met gewijzigde eigenschaps waarden

Elk bericht vertegenwoordigt een of meer wijzigingen aan een apparaat sinds het laatste geëxporteerde bericht. De informatie die in elk bericht wordt verzonden, omvat:
- `@id` van het apparaat in IoT Central
- `name` van het apparaat
- `deviceId` van [Device Provisioning Service](https://aka.ms/iotcentraldocsdps)
- Informatie over de apparaatprofiel
- Waarden van eigenschappen

> [!NOTE]
> Apparaten die zijn verwijderd sinds de laatste batch, zijn niet geëxporteerd. Er zijn momenteel geen indica toren voor het exporteren van berichten voor verwijderde apparaten.
>
> De sjabloon van het apparaat waartoe elk apparaat behoort, wordt vertegenwoordigd door een ID van een apparaat sjabloon. Als u de naam van de sjabloon wilt ophalen, moet u ook de gegevens van de apparaataccount van het apparaat exporteren.

In het volgende voor beeld ziet u een bericht over apparaatgegevens in Event Hub of Service Bus wachtrij of onderwerp:


```json
{ 
  "body":{ 
    "@id":"<id>",
    "@type":"Device",
    "displayName":"Airbox - 266d30aedn5",
    "data":{ 
      "$cloudProperties":{ 
        "Color":"blue"
      },
      "EnvironmentalSensor":{ 
        "thsensormodel":{ 
          "reported":{ 
            "value":"A1",
            "$lastUpdatedTimestamp":"2019-10-02T18:14:49.3820326Z"
          }
        },
        "pm25sensormodel":{ 
          "reported":{ 
            "value":"P1",
            "$lastUpdatedTimestamp":"2019-10-02T18:14:49.3820326Z"
          }
        }
      },
      "urn_azureiot_DeviceManagement_DeviceInformation":{ 
        "totalStorage":{ 
          "reported":{ 
            "value":3088.1959855710156,
            "$lastUpdatedTimestamp":"2019-10-02T18:14:49.3820326Z"
          }
        },
        "totalMemory":{ 
          "reported":{ 
            "value":16005.703586477555,
            "$lastUpdatedTimestamp":"2019-10-02T18:14:49.3820326Z"
          }
        }
      }
    },
    "instanceOf":"<templateId>",
    "deviceId":"<deviceId>",
    "simulated":true
  },
  "annotations":{ 
    "iotcentral-message-source":"devices",
    "x-opt-partition-key":"<partitionKey>",
    "x-opt-sequence-number":39740,
    "x-opt-offset":"<offset>",
    "x-opt-enqueued-time":1539274959654
  },
  "partitionKey":"<partitionKey>",
  "sequenceNumber":39740,
  "enqueuedTimeUtc":"2019-10-02T18:14:49.3820326Z",
  "offset":"<offset>"
}
```

### <a name="device-templates"></a>Apparaatsjablonen

Berichten met Device-sjabloon gegevens worden elke paar minuten verzonden naar uw Event Hub of Service Bus wachtrij of onderwerp. Dit betekent dat elke paar minuten een batch berichten ontvangt met gegevens over
- Nieuwe sjablonen voor apparaten die zijn toegevoegd of waarvan een versie is gemaakt
- Apparaatinstellingen met gewijzigde capabilityModels, cloudProperties, onderdrukkingen en oorspronkelijke waarden

Elk bericht vertegenwoordigt een of meer wijzigingen aan een apparaatprofiel sinds het laatste geëxporteerde bericht. De informatie die in elk bericht wordt verzonden, omvat:
- `@id` van de sjabloon voor het apparaat
- `name` van de sjabloon voor het apparaat
- `version` van de sjabloon voor het apparaat
- Het apparaat `capabilityModel` met inbegrip van de `interfaces` en de definities van telemetrie, eigenschappen en opdrachten
- definities van `cloudProperties`
- Onderdrukkingen en initiële waarden, inline met de `capabilityModel`

> [!NOTE]
> De apparaatinstellingen zijn verwijderd sinds de laatste batch is niet geëxporteerd. Er zijn momenteel geen indica toren in geëxporteerde berichten voor verwijderde Apparaatinstellingen.

In het volgende voor beeld ziet u een bericht met een apparaatprofiel in Event Hub of Service Bus wachtrij of onderwerp:

```json
{ 
  "body":{ 
    "@id":"<template-id>",
    "@type":"DeviceModelDefinition",
    "displayName":"Airbox",
    "capabilityModel":{ 
      "@id":"<id>",
      "@type":"CapabilityModel",
      "implements":[ 
        { 
          "@id":"<id>",
          "@type":"InterfaceInstance",
          "name":"EnvironmentalSensor",
          "schema":{ 
            "@id":"<id>",
            "@type":"Interface",
            "comment":"Requires temperature and humidity sensors.",
            "description":"Provides functionality to report temperature, humidity. Provides telemetry, commands and read-write properties",
            "displayName":"Environmental Sensor",
            "contents":[ 
              { 
                "@id":"<id>",
                "@type":"Telemetry",
                "description":"Current temperature on the device",
                "displayName":"Temperature",
                "name":"temp",
                "schema":"double",
                "unit":"Units/Temperature/celsius",
                "valueDetail":{ 
                  "@id":"<id>",
                  "@type":"ValueDetail/NumberValueDetail",
                  "minValue":{ 
                    "@value":"50"
                  }
                },
                "visualizationDetail":{ 
                  "@id":"<id>",
                  "@type":"VisualizationDetail"
                }
              },
              { 
                "@id":"<id>",
                "@type":"Telemetry",
                "description":"Current humidity on the device",
                "displayName":"Humidity",
                "name":"humid",
                "schema":"integer"
              },
              { 
                "@id":"<id>",
                "@type":"Telemetry",
                "description":"Current PM2.5 on the device",
                "displayName":"PM2.5",
                "name":"pm25",
                "schema":"integer"
              },
              { 
                "@id":"<id>",
                "@type":"Property",
                "description":"T&H Sensor Model Name",
                "displayName":"T&H Sensor Model",
                "name":"thsensormodel",
                "schema":"string"
              },
              { 
                "@id":"<id>",
                "@type":"Property",
                "description":"PM2.5 Sensor Model Name",
                "displayName":"PM2.5 Sensor Model",
                "name":"pm25sensormodel",
                "schema":"string"
              }
            ]
          }
        },
        { 
          "@id":"<id>",
          "@type":"InterfaceInstance",
          "name":"urn_azureiot_DeviceManagement_DeviceInformation",
          "schema":{ 
            "@id":"<id>",
            "@type":"Interface",
            "displayName":"Device information",
            "contents":[ 
              { 
                "@id":"<id>",
                "@type":"Property",
                "comment":"Total available storage on the device in kilobytes. Ex. 20480000 kilobytes.",
                "displayName":"Total storage",
                "name":"totalStorage",
                "displayUnit":"kilobytes",
                "schema":"long"
              },
              { 
                "@id":"<id>",
                "@type":"Property",
                "comment":"Total available memory on the device in kilobytes. Ex. 256000 kilobytes.",
                "displayName":"Total memory",
                "name":"totalMemory",
                "displayUnit":"kilobytes",
                "schema":"long"
              }
            ]
          }
        }
      ],
      "displayName":"AAEONAirbox52"
    },
    "solutionModel":{ 
      "@id":"<id>",
      "@type":"SolutionModel",
      "cloudProperties":[ 
        { 
          "@id":"<id>",
          "@type":"CloudProperty",
          "displayName":"Color",
          "name":"Color",
          "schema":"string",
          "valueDetail":{ 
            "@id":"<id>",
            "@type":"ValueDetail/StringValueDetail"
          },
          "visualizationDetail":{ 
            "@id":"<id>",
            "@type":"VisualizationDetail"
          }
        }
      ]
    },
    "annotations":{ 
      "iotcentral-message-source":"deviceTemplates",
      "x-opt-partition-key":"<partitionKey>",
      "x-opt-sequence-number":25315,
      "x-opt-offset":"<offset>",
      "x-opt-enqueued-time":1539274985085
    },
    "partitionKey":"<partitionKey>",
    "sequenceNumber":25315,
    "enqueuedTimeUtc":"2019-10-02T16:23:05.085Z",
    "offset":"<offset>"
  }
}
```

## <a name="next-steps"></a>Volgende stappen

Nu u weet hoe u uw gegevens naar Azure Event Hubs en Azure Service Bus kunt exporteren, gaat u verder met de volgende stap:

> [!div class="nextstepaction"]
> [Azure Functions activeren](howto-trigger-azure-functions.md)
