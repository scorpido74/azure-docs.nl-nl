---
title: Interactie met een IoT Plug en Play preview-apparaat vanuit een Azure IoT-oplossing | Microsoft Docs
description: Als oplossings ontwikkelaar leert u hoe u de Service SDK kunt gebruiken om te communiceren met IoT Plug en Play-apparaten.
author: Philmea
ms.author: philmea
ms.date: 12/26/2019
ms.topic: tutorial
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 485b17ff236de32eab5388629c1bb6044ba19197
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/28/2019
ms.locfileid: "75531340"
---
# <a name="connect-to-and-interact-with-an-iot-plug-and-play-preview-device"></a>Verbinding maken met en interactie met een IoT Plug en Play preview-apparaat

In deze hand leiding wordt uitgelegd hoe u de voor beelden in de node Service-SDK kunt gebruiken om te laten zien hoe uw IoT-oplossing kan communiceren met IoT Plug en Play preview-apparaten.

Als u de Snelstartgids [voor het verbinden van een IoT Plug en Play-apparaat](quickstart-connect-pnp-device-solution-node.md) nog niet hebt voltooid, kunt u dit nu doen. In de Quick start ziet u hoe u de SDK downloadt en installeert en enkele voor beelden uitvoert.

Voordat u de service voorbeelden uitvoert, opent u een nieuwe terminal, gaat u naar de hoofdmap van de gekloonde opslag plaats, gaat u naar de map **digitaltwins/Quick starts/service** en voert u de volgende opdracht uit om de afhankelijkheden te installeren:

```cmd/sh
npm install
```

## <a name="run-the-service-samples"></a>De service voorbeelden uitvoeren

Gebruik de volgende voor beelden om de mogelijkheden van de node. js Service-SDK te verkennen. Zorg ervoor dat de omgevings variabele `IOTHUB_CONNECTION_STRING` is ingesteld in de shell die u gebruikt:

### <a name="retrieve-a-digital-twin-and-list-the-interfaces"></a>Een digitale dubbele en een lijst met de interfaces ophalen

**get_digital_twin. js** krijgt de digitale verbinding die is gekoppeld aan uw apparaat en het onderdeel wordt op de opdracht regel afgedrukt. U hoeft geen voor beeld van een actief apparaat te laten slagen.

**get_digital_twin_interface_instance. js** krijgt een enkel interface-exemplaar van digitaal, dat is gekoppeld aan uw apparaat en wordt afgedrukt op de opdracht regel. Het is niet nodig om het voor beeld van het apparaat uit te voeren.

### <a name="get-and-set-properties-using-the-node-service-sdk"></a>Eigenschappen ophalen en instellen met behulp van de node-Service-SDK

**update_digital_twin. js** werkt een Beschrijf bare eigenschap op uw apparaat digitaal, met behulp van een volledige patch. Als u wilt, kunt u meerdere eigenschappen op meerdere interfaces bijwerken. Hiervoor moet het voor beeld van het apparaat op hetzelfde moment worden uitgevoerd. Geslaagd lijkt op het voor beeld van een apparaat het bijwerken van een eigenschap. met het voor beeld van de service wordt een bijgewerkt digitaal twee in de Terminal afgedrukt.

### <a name="send-a-command-and-retrieve-the-response-using-the-node-service-sdk"></a>Een opdracht verzenden en de reactie ophalen met behulp van de node Service-SDK

**invoke_command. js** roept een synchrone opdracht op uw apparaat in digitale twee. Hiervoor moet het voor beeld van het apparaat op hetzelfde moment worden uitgevoerd. Geslaagd lijkt op het voor beeld van een apparaat om een opdracht te bevestigen en de service-client het resultaat van de opdracht in de Terminal af te drukken.

### <a name="connect-to-the-public-repository-and-retrieve-a-model-definition-using-the-node-service-sdk"></a>Verbinding maken met de open bare opslag plaats en een model definitie ophalen met behulp van de node Service SDK

Met dezelfde instructies als voor de voor beelden van de service en het apparaat, moet u de volgende omgevings variabele instellen:

* `AZURE_IOT_MODEL_REPOSITORY_CONNECTION_STRING`

U vindt deze connection string in het [Azure Certified for IOT-Portal](https://preview.catalog.azureiotsolutions.com) op het tabblad **verbindings reeksen** voor de **opslag plaats**van uw bedrijf.

Het connection string ziet eruit als in het volgende voor beeld:

```text
HostName={repo host name};RepositoryId={repo ID};SharedAccessKeyName={repo key ID};SharedAccessKey={repo key secret}
```

Nadat u deze vier omgevings variabelen hebt ingesteld, voert u het voor beeld op dezelfde manier uit als de andere voor beelden:

```cmd/sh
node model_repo.js
```

In dit voor beeld wordt de **ModelDiscovery** -interface gedownload en wordt dit model in de Terminal afgedrukt.

### <a name="run-queries-in-iot-hub-based-on-capability-models-and-interfaces"></a>Query's uitvoeren in IoT Hub op basis van mogelijkheden van modellen en interfaces

De IoT Hub query taal ondersteunt `HAS_INTERFACE` en `HAS_CAPABILITYMODEL`, zoals wordt weer gegeven in de volgende voor beelden:

```sql
select * from devices where HAS_INTERFACE('id without version', version)
```

```sql
select * from devices where HAS_CAPABILITYMODEL('id without version', version)
```

### <a name="creating-digital-twin-routes"></a>Digitale dubbele routes maken

Uw oplossing kan meldingen ontvangen van gebeurtenissen met een digitale dubbele wijziging. Als u zich wilt abonneren op deze meldingen, gebruikt u de [functie route ring van IOT hub](../iot-hub/iot-hub-devguide-endpoints.md) om de meldingen te verzenden naar een eind punt, zoals BLOB storage, Event hubs of een service bus wachtrij.

Een digitale dubbele route maken:

1. Ga in het Azure Portal naar uw IoT Hub resource.
1. Selecteer **bericht routering**.
1. Selecteer **toevoegen**op het tabblad **routes** .
1. Voer een waarde in het veld **naam** in en kies een **eind punt**. Als u een eind punt nog niet hebt geconfigureerd, selecteert u **eind punt toevoegen**.
1. Selecteer in de vervolg keuzelijst **gegevens bron** de optie **digitale dubbele wijzigings gebeurtenissen**.
1. Selecteer **Opslaan**.

In de volgende JSON ziet u een voor beeld van een digitale dubbele wijzigings gebeurtenis:

```json
{
  "interfaces": {
    "urn_azureiot_ModelDiscovery_DigitalTwin": {
      "name": "urn_azureiot_ModelDiscovery_DigitalTwin",
      "properties": {
        "modelInformation": {
          "reported": {
            "value": {
              "modelId": "urn:domain:capabilitymodel:TestCapability:1",
              "interfaces": {
                "MyInterfaceFoo": "urn:domain:interfaces:FooInterface:1",
                "urn_azureiot_ModelDiscovery_DigitalTwin": "urn:azureiot:ModelDiscovery:DigitalTwin:1"
              }
            }
          }
        }
      }
    },
    "MyInterfaceFoo": {
      "name": "MyInterfaceFoo",
      "properties": {
        "property_1": { "desired": { "value": "value_1" } },
        "property_2": {
          "desired": { "value": 20 },
          "reported": {
            "value": 10,
            "desiredState": {
              "code": 200,
              "version": 22,
              "subCode": 400,
              "description": ""
            }
          }
        },
        "property_3": { "reported": { "value": "value_3" } }
      }
    }
  },
  "version": 4
}
```

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd over service oplossingen die met uw IoT Plug en Play-apparaten communiceren, is een voorgestelde volgende stap meer informatie over [model detectie](concepts-model-discovery.md).
