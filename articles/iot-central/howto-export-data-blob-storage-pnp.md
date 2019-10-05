---
title: Uw gegevens exporteren naar Azure Blob Storage | Microsoft Docs
description: Gegevens exporteren uit uw Azure IoT Central-toepassing naar Azure Blob Storage
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 09/26/2019
ms.topic: conceptual
ms.service: iot-central
manager: corywink
ms.openlocfilehash: 9ac650273a53da715b89e3233b30cf50710cfcfd
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973230"
---
# <a name="export-your-data-to-azure-blob-storage-preview-features"></a>Uw gegevens exporteren naar Azure Blob Storage (preview-functies)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

*Dit onderwerp is van toepassing op beheerders.*

In dit artikel wordt beschreven hoe u de functie continue gegevens export in azure IoT Central gebruikt om regel matig gegevens te exporteren naar uw **Azure Blob-opslag account** of **Azure data Lake Storage Gen2-opslag account**. U kunt **telemetrie**, **apparaten**en **APPARAATINSTELLINGEN** exporteren naar bestanden in JSON-indeling. De geëxporteerde gegevens kunnen worden gebruikt voor koude-pad analyses, zoals trainings modellen in de analyse van Azure Machine Learning of lange termijn in micro soft Power BI.

> [!Note]
> Wanneer u continue gegevens export inschakelt, worden er vanaf dat moment alleen gegevens opgehaald. Op dit moment kunnen geen gegevens worden opgehaald voor een tijd dat de continue gegevens export is uitgeschakeld. Als u meer historische gegevens wilt behouden, moet u de continue gegevens export voor tijdig inschakelen.


## <a name="prerequisites"></a>Vereisten

- U moet een beheerder zijn in uw IoT Central-toepassing

## <a name="create-storage-account"></a>Een opslagaccount maken
Als u geen bestaande opslag hebt om naar te exporteren, voert u de volgende stappen uit:

1. Maak een [Nieuw opslag account in de Azure Portal](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Meer informatie over het maken van nieuwe [Azure Blob Storage-accounts](https://aka.ms/blobdocscreatestorageaccount) of [Azure data Lake Storage v2-opslag accounts](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-account).

    > [!Note] 
    > Als u ervoor kiest om gegevens te exporteren naar een ADLS v2-opslag account, moet u **account type** kiezen als **BlobStorage**. 

    > [!Note] 
    > U kunt gegevens exporteren naar opslag accounts in andere abonnementen dan die voor uw betalen per gebruik-IoT Central-toepassing. In dit geval kunt u verbinding maken met behulp van een connection string.

2. Maak een container in uw opslag account. Ga naar uw opslagaccount. Selecteer onder **BLOB**-service **Bladeren door blobs**. Selecteer **+ container** aan de bovenkant om een nieuwe container te maken.


## <a name="set-up-continuous-data-export"></a>Continue gegevens export instellen

Nu u een opslag bestemming hebt waarnaar u gegevens kunt exporteren, voert u de volgende stappen uit om continue gegevens export in te stellen. 

1. Meld u aan bij uw IoT Central-toepassing.

2. Selecteer in het menu links de optie **gegevens exporteren**.

    > [!Note]
    > Als het exporteren van gegevens niet in het linkermenu wordt weer gegeven, bent u geen beheerder in uw app. Neem contact op met een beheerder om het exporteren van gegevens in te stellen.

3. Selecteer de knop **+ Nieuw** in de rechter bovenhoek. Kies **Azure Blob Storage** als doel van uw export. 

    > [!NOTE] 
    > Het maximum aantal exports per app is vijf. 

    ![Nieuwe continue gegevens export maken](media/howto-export-data-pnp/export-new2.png)

4. Selecteer in de vervolg keuzelijst de naam ruimte van uw **opslag account**. U kunt ook de laatste optie in de lijst kiezen die **een connection string invoert**. 

    > [!NOTE] 
    > U ziet alleen opslag accounts met naam ruimten in **hetzelfde abonnement als uw IOT Central-app**. Als u wilt exporteren naar een bestemming buiten dit abonnement, kiest u **een Connection String invoeren** en gaat u naar stap 5.

    > [!NOTE] 
    > Voor apps met een proef versie van 7 dagen is de enige manier om continue gegevens export te configureren via een connection string. De reden hiervoor is dat apps met een proef versie van zeven dagen geen bijbehorend Azure-abonnement hebben.

    ![Nieuwe export naar Blob maken](media/howto-export-data-pnp/export-create-blob2.png)

5. Beschrijving Als u **een connection string invoert**, wordt er een nieuw vak weer gegeven waarin u uw Connection String kunt plakken. Als u de connection string voor uw opslag account wilt ophalen, gaat u naar het opslag account in de Azure Portal:
    - Selecteer onder **instellingen** **toegangs sleutels**
    - Kopieer de waarde van de Key1-verbindings reeks of de Key2-verbindings reeks
 
6. Kies een container in de vervolg keuzelijst. Als u geen container hebt, gaat u naar uw opslag account in de Azure Portal:
    - Onder **BLOB service**selecteert u **blobs**. Klik op **+ container** en geef de container een naam. Kies een openbaar toegangs niveau voor uw gegevens (wille keurige gegevens worden geëxporteerd). Meer informatie over [Azure Storage docs](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container).

7.  Geef bij te **exporteren gegevens**elk type gegevens op dat moet worden geëxporteerd door het type in te stellen **op on**.
   
    > [!NOTE] 
    > De gegevens worden geëxporteerd in JSON-indeling.

8. Als u continue gegevens export wilt inschakelen, moet u ervoor zorgen dat de wissel knop voor het **exporteren van gegevens** is ingeschakeld. Selecteer **Opslaan**.

   ![Continue gegevens export configureren](media/howto-export-data-pnp/export-list-blob2.png)

9. Na een paar minuten worden uw gegevens weer gegeven in uw opslag account.


## <a name="path-structure"></a>Structuur van pad

De gegevens van telemetrie, apparaten en apparaatinstellingen worden eenmaal per minuut geëxporteerd naar uw opslag account, waarbij elk bestand met de batch wijzigingen sinds het laatste geëxporteerde bestand. Geëxporteerde gegevens worden in drie mappen in JSON-indeling geplaatst. De standaard paden in uw opslag account zijn:
- Telemetrie: {container}/{app-id}/telemetry/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}
- Apparaten: {container}/{app-id}/devices/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}
- Apparaatinstellingen: {container}/{app-id}/deviceTemplates/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}

U kunt door de geëxporteerde bestanden in de Azure Portal bladeren door naar het bestand te navigeren en op het tabblad **BLOB bewerken** te klikken.

## <a name="data-format"></a>Gegevensindeling
### <a name="telemetry"></a>Telemetrie

De geëxporteerde telemetriegegevens hebben alle nieuwe berichten die zijn ontvangen door IoT Central van alle apparaten tijdens die tijd. De geëxporteerde bestanden gebruiken dezelfde indeling als de bericht bestanden die worden geëxporteerd door [IOT hub bericht routering](https://docs.microsoft.com/azure/iot-hub/iot-hub-csharp-csharp-process-d2c) naar Blob Storage.

> [!NOTE]
> Zorg ervoor dat uw apparaten berichten verzenden met `contentType: application/JSON` en `contentEncoding:utf-8` (of `utf-16`, `utf-32`). Raadpleeg de [documentatie van IOT hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#message-routing-query-based-on-message-body) voor een voor beeld.

> [!NOTE]
> De apparaten die de telemetrie verzenden, worden vertegenwoordigd door apparaat-Id's (Zie de volgende secties). Als u de namen van de apparaten wilt ophalen, exporteert u de moment opnamen van het apparaat. Correleer elke berichten record met behulp van de **connectionDeviceId** die overeenkomt met de **deviceId** van de apparaatgegevens.

In het volgende voor beeld ziet u een record in de JSON-indeling.

```json
{ 
  "EnqueuedTimeUtc":"2019-09-26T17:46:09.8870000Z",
  "Properties":{ 

  },
  "SystemProperties":{ 
    "connectionDeviceId":"<deviceid>",
    "connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "connectionDeviceGenerationId":"637051167384630591",
    "contentType":"application/json",
    "contentEncoding":"utf-8",
    "enqueuedTime":"2019-09-26T17:46:09.8870000Z"
  },
  "Body":{ 
    "temp":49.91322758395974,
    "humid":49.61214852573155,
    "pm25":25.87332214661367
  }
}
```

### <a name="devices"></a>Apparaten

Wanneer voortdurende gegevens export is ingeschakeld, wordt één moment opname met alle apparaten geëxporteerd. Elk apparaat omvat:
- `@id` van het apparaat in IoT Central
- `name` van het apparaat
- `deviceId` van [Device Provisioning Service](https://aka.ms/iotcentraldocsdps)
- Informatie over de apparaatprofiel
- Waarden van eigenschappen

Een nieuwe moment opname wordt eenmaal per minuut geschreven. De moment opname bevat:

- Nieuwe apparaten die zijn toegevoegd sinds de laatste moment opname.
- Apparaten met gewijzigde eigenschaps waarden sinds de laatste moment opname.

> [!NOTE]
> Apparaten die zijn verwijderd sinds de laatste moment opname zijn niet geëxporteerd. Op dit moment hebben de moment opnamen geen indica toren voor verwijderde apparaten.
>
> De sjabloon van het apparaat waartoe elk apparaat behoort, wordt vertegenwoordigd door het `instanceOf`-veld. Als u de naam van de apparaatprofiel wilt ophalen, exporteert u de moment opnamen van de apparaatnaam.

Geëxporteerde bestanden bevatten één regel per record. In het volgende voor beeld ziet u een record in de JSON-indeling.

```json
{ 
  "@id":"<id-value>",
  "@type":"Device",
  "displayName":"Airbox",
  "data":{ 
    "$cloudProperties":{ 
        "Color":"blue"
    },
    "EnvironmentalSensor":{ 
      "thsensormodel":{ 
        "reported":{ 
          "value":"Neque quia et voluptatem veritatis assumenda consequuntur quod.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "pm25sensormodel":{ 
        "reported":{ 
          "value":"Aut alias odio.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    },
    "urn_azureiot_DeviceManagement_DeviceInformation":{ 
      "totalStorage":{ 
        "reported":{ 
          "value":27900.9730905171,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "totalMemory":{ 
        "reported":{ 
          "value":4667.82916715811,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    }
  },
  "instanceOf":"<template-id>",
  "deviceId":"<device-id>",
  "simulated":true
}
```

### <a name="device-templates"></a>Apparaatsjablonen

Wanneer continue gegevens export is ingeschakeld, wordt één moment opname met alle Apparaatinstellingen geëxporteerd. Elke apparaatprofiel bevat:
- `@id` van de sjabloon voor het apparaat
- `name` van de sjabloon voor het apparaat
- `version` van de sjabloon voor het apparaat
- Het apparaat `capabilityModel` met inbegrip van de `interfaces` en de definities van telemetrie, eigenschappen en opdrachten
- definities van `cloudProperties`
- Onderdrukkingen en initiële waarden, inline met de `capabilityModel`

Een nieuwe moment opname wordt eenmaal per minuut geschreven. De moment opname bevat:

- Nieuwe apparaatinstellingen zijn toegevoegd sinds de laatste moment opname. Dit omvat nieuwe versies van Apparaatinstellingen.
- Device-sjablonen met gewijzigde onderdrukkingen, initiële waarden en Cloud eigenschappen definities sinds de laatste moment opname.

> [!NOTE]
> De apparaatinstellingen zijn verwijderd sinds de laatste moment opname is niet geëxporteerd. Op dit moment hebben de moment opnamen geen indica toren voor verwijderde Apparaatinstellingen.

Geëxporteerde bestanden bevatten één regel per record. In het volgende voor beeld ziet u een record in de JSON-indeling.

```json
{ 
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
  }
}
```

## <a name="next-steps"></a>Volgende stappen

Nu u weet hoe u uw gegevens kunt exporteren, gaat u verder met de volgende stap:

> [!div class="nextstepaction"]
> [De IoT Central Device Bridge gebruiken om andere IoT-Clouds te verbinden](howto-build-iotc-device-bridge.md)
