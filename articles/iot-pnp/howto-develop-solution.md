---
title: Interactie met een IoT Plug and Play Preview-apparaat vanuit een Azure IoT-oplossing | Microsoft Documenten
description: Als ontwikkelaar van oplossingen leest u meer over hoe u de service SDK gebruiken om te communiceren met IoT Plug and Play-apparaten.
author: Philmea
ms.author: philmea
ms.date: 12/26/2019
ms.topic: how-to
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: e349aadfd629202b1c8cdb5c53a88e0a6c2e06de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159214"
---
# <a name="connect-to-and-interact-with-an-iot-plug-and-play-preview-device"></a>Verbinding maken met en communiceren met een IoT Plug and Play Preview-apparaat

Deze handleiding laat u zien hoe u de voorbeelden in de Node-service SDK gebruiken die u laten zien hoe uw IoT-oplossing kan communiceren met IoT Plug and Play Preview-apparaten.

Als u het [Connect a IoT Plug and Play-apparaat](quickstart-connect-pnp-device-solution-node.md) nog niet snel hebt aangesloten op uw oplossing, moet u dit nu doen. De quickstart laat zien hoe je de SDK downloaden en installeren en een aantal van de samples uitvoeren.

Voordat u de servicevoorbeelden uitvoert, opent u een nieuwe terminal, gaat u naar de hoofdmap van uw gekloonde opslagplaats, navigeert u naar de **map digitaltwins/quickstarts/service** en voert u de volgende opdracht uit om de afhankelijkheden te installeren:

```cmd/sh
npm install
```

## <a name="run-the-service-samples"></a>De servicevoorbeelden uitvoeren

Gebruik de volgende voorbeelden om de mogelijkheden van de Node.js-service SDK te verkennen. Controleer of `IOTHUB_CONNECTION_STRING` de omgevingsvariabele is ingesteld in de shell die u gebruikt:

### <a name="retrieve-a-digital-twin-and-list-the-interfaces"></a>Haal een digitale tweeling op en vermeld de interfaces

**get_digital_twin.js** krijgt de digitale tweeling gekoppeld aan uw apparaat en drukt de component in de opdrachtregel af. Hiervoor hoeft geen lopend apparaatvoorbeeld te worden gebruikt.

**get_digital_twin_interface_instance.js** krijgt één interface-exemplaar van de digitale tweeling die aan uw apparaat is gekoppeld en drukt deze af in de opdrachtregel. Het apparaatmonster hoeft niet uit te voeren.

### <a name="get-and-set-properties-using-the-node-service-sdk"></a>Eigenschappen downloaden en instellen met de Node-service SDK

**update_digital_twin.js** werkt een beschrijfbare eigenschap op uw apparaat digitale twin met behulp van een volledige patch. U meerdere eigenschappen op meerdere interfaces bijwerken als u dat wilt. Om te slagen, moet het apparaatmonster tegelijkertijd worden uitgevoerd. Succes lijkt erop dat het apparaat monster is het afdrukken van iets over het bijwerken van een eigenschap van de service monster afdrukken van een bijgewerkte digitale tweeling in de terminal.

### <a name="send-a-command-and-retrieve-the-response-using-the-node-service-sdk"></a>Een opdracht verzenden en het antwoord ophalen met de Node-service SDK

**invoke_command.js** roept een synchrone opdracht op uw apparaat digitale tweeling. Om te slagen, moet het apparaatmonster tegelijkertijd worden uitgevoerd. Succes lijkt erop dat het apparaatvoorbeeld iets afdrukt over het herkennen van een opdracht en het afdrukken van het resultaat van de opdracht in de terminal.

### <a name="connect-to-the-public-repository-and-retrieve-a-model-definition-using-the-node-service-sdk"></a>Verbinding maken met de openbare opslagplaats en een modeldefinitie ophalen met de Node-service SDK

Met dezelfde instructies als voor de service- en apparaatmonsters moet u de volgende omgevingsvariabele instellen:

* `AZURE_IOT_MODEL_REPOSITORY_CONNECTION_STRING`

U vindt deze verbindingstekenreeks in de [Azure Certified for IoT-portal](https://preview.catalog.azureiotsolutions.com) op het tabblad **Verbindingstekenreeksen** voor uw **bedrijfsopslagplaats.**

De verbindingstekenreeks ziet eruit als het volgende voorbeeld:

```text
HostName={repo host name};RepositoryId={repo ID};SharedAccessKeyName={repo key ID};SharedAccessKey={repo key secret}
```

Nadat u deze vier omgevingsvariabelen hebt ingesteld, voert u het voorbeeld uit op dezelfde manier als de andere monsters:

```cmd/sh
node model_repo.js
```

In dit voorbeeld wordt de **ModelDiscovery-interface** gedownload en wordt dit model afgedrukt in de terminal.

### <a name="run-queries-in-iot-hub-based-on-capability-models-and-interfaces"></a>Query's uitvoeren in IoT Hub op basis van capaciteitsmodellen en interfaces

De IoT Hub-querytaal ondersteunt `HAS_INTERFACE` en `HAS_CAPABILITYMODEL` zoals weergegeven in de volgende voorbeelden:

```sql
select * from devices where HAS_INTERFACE('id without version', version)
```

```sql
select * from devices where HAS_CAPABILITYMODEL('id without version', version)
```

### <a name="creating-digital-twin-routes"></a>Digitale tweeroutes maken

Uw oplossing kan meldingen ontvangen van digitale twin change-gebeurtenissen. Als u zich wilt abonneren op deze meldingen, gebruikt u de [functie Iot Hub-routering](../iot-hub/iot-hub-devguide-endpoints.md) om de meldingen naar een eindpunt te verzenden, zoals blobopslag, gebeurtenishubs of een wachtrij voor servicebussen.

Ga als volgt te werk om een digitale tweepersoonsroute te maken:

1. Ga in de Azure-portal naar uw IoT Hub-bron.
1. Selecteer **Berichtroutering**.
1. Selecteer **op** het tabblad Routes de optie **Toevoegen**.
1. Voer een waarde in het veld **Naam** in en kies een **eindpunt**. Als u een eindpunt niet hebt geconfigureerd, selecteert u **Eindpunt toevoegen**.
1. Selecteer in de vervolgkeuzelijst **Gegevensbron** de optie **Digital Twin Change Events**.
1. Selecteer **Opslaan**.

De volgende JSON toont een voorbeeld van een gebeurtenis voor digitale tweelingverandering:

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

Nu u meer te weten bent gekomen over serviceoplossingen die samenwerken met uw IoT Plug and Play-apparaten, is een voorgestelde volgende stap om meer te weten te komen over [modeldetectie.](concepts-model-discovery.md)
