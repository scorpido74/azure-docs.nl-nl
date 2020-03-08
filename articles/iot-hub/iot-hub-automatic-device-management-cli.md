---
title: Automatische Apparaatbeheer op schaal met Azure IoT Hub (CLI) | Microsoft Docs
description: Automatische configuraties van Azure IoT Hub gebruiken om meerdere IoT-apparaten of-modules te beheren
author: ChrisGMsft
manager: bruz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: chrisgre
ms.openlocfilehash: 381f550f6d64dee3c7649a040c1e24b7c9d42f2c
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78669442"
---
# <a name="automatic-iot-device-and-module-management-using-the-azure-cli"></a>Automatische IoT-apparaat-en module beheer met Azure CLI

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

Automatische Apparaatbeheer in azure IoT Hub automatiseert veel van de herhaalde en complexe taken voor het beheren van grote apparaat vloots. Met automatisch Apparaatbeheer kunt u een set apparaten op basis van hun eigenschappen richten, een gewenste configuratie definiëren en de apparaten vervolgens IoT Hub bijwerken wanneer ze binnen het bereik komen. Deze update wordt uitgevoerd met behulp van een _automatische apparaatconfiguratie_ of _automatische configuratie_van een module, waarmee u de voltooiing en naleving kunt samen vatten, samen voegen en conflicten kunt afhandelen en configuraties in een gefaseerde benadering implementeert.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Automatische Apparaatbeheer werkt door het bijwerken van een set apparaatdubbels of module apparaatdubbels met gewenste eigenschappen en het rapporteren van een samen vatting dat is gebaseerd op twee gerapporteerde eigenschappen.  Er wordt een nieuwe klasse en een JSON-document geïntroduceerd, een *configuratie* met drie onderdelen:

* De **doel voorwaarde** definieert het bereik van de apparaatdubbels of module apparaatdubbels die moet worden bijgewerkt. De doel voorwaarde wordt opgegeven als een query op dubbele Tags en/of gerapporteerde eigenschappen van het apparaat.

* De **doel inhoud** definieert de gewenste eigenschappen die moeten worden toegevoegd of bijgewerkt in de apparaatdubbels of module apparaatdubbels van het doel apparaat. De inhoud bevat een pad naar de sectie met gewenste eigenschappen die moeten worden gewijzigd.

* De **metrische gegevens** bepalen het aantal samen vattingen van de verschillende configuratie statussen, zoals **geslaagd**, **in uitvoering**en **fout**. Aangepaste metrische gegevens worden opgegeven als query's op dubbele gerapporteerde eigenschappen.  Systeem metrieken zijn de standaard waarden die de dubbele update status meten, zoals het aantal apparaatdubbels dat is gericht en het aantal apparaatdubbels dat is bijgewerkt.

Automatische configuraties worden voor de eerste keer uitgevoerd, kort nadat de configuratie is gemaakt en vervolgens vijf minuten. Metrische query's worden uitgevoerd telkens wanneer de automatische configuratie wordt uitgevoerd.

## <a name="cli-prerequisites"></a>CLI-vereisten

* Een [IOT-hub](../iot-hub/iot-hub-create-using-cli.md) in uw Azure-abonnement. 

* [Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli) in uw omgeving. Uw Azure CLI-versie moet mini maal 2.0.70 of hoger zijn. Gebruik `az –-version` om de versie te valideren. In deze versie worden az-extensie-opdrachten ondersteund en is voor het eerst het Knack-opdrachtframework opgenomen. 

* De [IOT-extensie voor Azure cli](https://github.com/Azure/azure-cli).

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="implement-twins"></a>Apparaatdubbels implementeren

Voor automatische apparaatconfiguratie is het gebruik van apparaatdubbels vereist om de status van de Cloud en de apparaten te synchroniseren.  Zie [Apparaatdubbels begrijpen en gebruiken in IoT Hub](iot-hub-devguide-device-twins.md) voor meer informatie.

Voor automatische module configuraties moet module apparaatdubbels worden gebruikt om de status van de Cloud en modules te synchroniseren. Zie voor meer informatie de [module Apparaatdubbels begrijpen en gebruiken in IOT hub](iot-hub-devguide-module-twins.md).

## <a name="use-tags-to-target-twins"></a>Tags gebruiken om apparaatdubbels te richten

Voordat u een configuratie maakt, moet u opgeven welke apparaten of modules u wilt beïnvloeden. Met Azure IoT Hub worden apparaten geïdentificeerd en tags op het apparaat dubbele en worden modules geïdentificeerd met behulp van labels in de module. Elk apparaat of elke module kan meerdere labels hebben, en u kunt ze op elke manier definiëren die zinvol is voor uw oplossing. Als u bijvoorbeeld apparaten op verschillende locaties beheert, voegt u de volgende tags toe aan een apparaat:

```json
"tags": {
    "location": {
        "state": "Washington",
        "city": "Tacoma"
    }
},
```

## <a name="define-the-target-content-and-metrics"></a>De doel inhoud en-metrische gegevens definiëren

De doel inhoud en metrische query's worden opgegeven als JSON-documenten waarin de eigenschappen van het apparaat dubbel of de gewenste module worden beschreven voor het instellen en gerapporteerde eigenschappen die moeten worden gemeten.  Als u een automatische configuratie wilt maken met behulp van Azure CLI, slaat u de doel inhoud en de metrische gegevens lokaal op als txt-bestanden. U gebruikt de bestands paden in een latere sectie wanneer u de opdracht uitvoert om de configuratie toe te passen op uw apparaat.

Hier volgt een voor beeld van een basis doel inhoud voor een automatische apparaatconfiguratie:

```json
{
  "content": {
    "deviceContent": {
      "properties.desired.chillerWaterSettings": {
        "temperature": 38,
        "pressure": 78
      }
    }
}
```

Automatische module configuraties gedragen zich op vergelijk bare wijze, maar u bereikt `moduleContent` in plaats van `deviceContent`.

```json
{
  "content": {
    "moduleContent": {
      "properties.desired.chillerWaterSettings": {
        "temperature": 38,
        "pressure": 78
      }
    }
}
```

Hier volgen enkele voor beelden van metrische query's:

```json
{
  "queries": {
    "Compliant": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='current'",
    "Error": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='error'",
    "Pending": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='pending'"
  }
}
```

Metrische query's voor modules zijn ook vergelijkbaar met query's voor apparaten, maar u selecteert voor `moduleId` van `devices.modules`. Bijvoorbeeld: 

```json
{
  "queries": {
    "Compliant": "select deviceId, moduleId from devices.module where configurations.[[chillermodulesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='current'"
  }
}
```

## <a name="create-a-configuration"></a>Een configuratie maken

U kunt doel apparaten configureren door een configuratie te maken die bestaat uit de doel inhoud en-metrische gegevens. 

Gebruik de volgende opdracht om een configuratie te maken:

```cli
   az iot hub configuration create --config-id [configuration id] \
     --labels [labels] --content [file path] --hub-name [hub name] \
     --target-condition [target query] --priority [int] \
     --metrics [metric queries]
```

* --**config-id** : de naam van de configuratie die wordt gemaakt in de IOT-hub. Geef uw configuratie een unieke naam van Maxi maal 128 kleine letters. Vermijd spaties en de volgende ongeldige tekens: `& ^ [ ] { } \ | " < > /`.

* --**labels** : Labels toevoegen om uw configuratie bij te houden. Labels zijn naam, waarde-paren die uw implementatie te beschrijven. Bijvoorbeeld `HostPlatform, Linux` of `Version, 3.0.1`

* --**inhoud** inline JSON of bestandspad naar de doel inhoud moet worden ingesteld als twee gewenste eigenschappen. 

* --**hub-naam** -naam van de IOT-hub waarin de configuratie wordt gemaakt. De hub moet zich in het huidige abonnement. Schakel over naar het gewenste abonnement met de opdracht `az account set -s [subscription name]`

* --**doel-condition** : Voer een doel voorwaarde in om te bepalen welke apparaten of modules met deze configuratie worden benaderd. Voor automatische apparaatconfiguratie is de voor waarde gebaseerd op apparaatonafhankelijke Tags of de dubbele gewenste eigenschappen van het apparaat en moet overeenkomen met de indeling van de expressie. Bijvoorbeeld `tags.environment='test'` of `properties.desired.devicemodel='4000x'`. Voor de automatische module configuratie is de voor waarde gebaseerd op een module dubbele Tags of module dubbele gewenste eigenschappen.. Bijvoorbeeld `from devices.modules where tags.environment='test'` of `from devices.modules where properties.reported.chillerProperties.model='4000x'`.

* --**prioriteit** : een positief geheel getal. Als twee of meer configuraties zijn gericht op hetzelfde apparaat of dezelfde module, is de configuratie met de hoogste numerieke waarde voor prioriteit van toepassing.

* --**metrische gegevens** : filepath naar de metrische query's. Metrische gegevens bieden een overzicht van de verschillende statussen die een apparaat of module kan melden na het Toep assen van configuratie-inhoud. U kunt bijvoorbeeld een metriek maken voor wijzigingen in de instellingen in behandeling, een metrische waarde voor fouten en een metrische waarde voor geslaagde instellingen wijzigen. 

## <a name="monitor-a-configuration"></a>Een configuratie controleren

Gebruik de volgende opdracht om de inhoud van een configuratie weer te geven:

```cli
az iot hub configuration show --config-id [configuration id] \
  --hub-name [hub name]
```

* --**config-id** : de naam van de configuratie die in de IOT-hub bestaat.

* --**hub-naam** -naam van de IOT-hub waarin de configuratie bestaat. De hub moet zich in het huidige abonnement. Schakel over naar het gewenste abonnement met de opdracht `az account set -s [subscription name]`

Controleer de configuratie in het opdracht venster. De eigenschap **Metrics** bevat een aantal voor elke metrische waarde die door elke hub wordt geëvalueerd:

* **targetedCount** : een systeem metriek waarmee het aantal apparaatdubbels of module apparaatdubbels in IOT hub wordt opgegeven dat overeenkomt met de doel voorwaarde.

* **appliedCount** : met een systeem metriek geeft u het aantal apparaten of modules op waarop de doel inhoud is toegepast.

* **Uw aangepaste metrische gegevens** : alle metrische gegevens die u hebt gedefinieerd, zijn metrische gegevens van de gebruiker.

U kunt een lijst met apparaat-Id's, module-Id's of objecten voor elk van de metrische gegevens weer geven met behulp van de volgende opdracht:

```cli
az iot hub configuration show-metric --config-id [configuration id] \
   --metric-id [metric id] --hub-name [hub name] --metric-type [type] 
```

* --**config-id** : de naam van de implementatie die in de IOT-hub bestaat.

* --**metrische id** : de naam van de metriek waarvoor u de lijst met apparaat-id's of module-id's wilt zien, bijvoorbeeld `appliedCount`.

* --**hub-naam** -naam van de IOT-hub waarin de implementatie bestaat. De hub moet zich in het huidige abonnement. Schakel over naar het gewenste abonnement met de opdracht `az account set -s [subscription name]`.

* --type metrische waarde **-type** metriek kan `system` of `user`zijn.  Systeem metrieken zijn `targetedCount` en `appliedCount`. Alle andere metrische gegevens zijn metrische gegevens van de gebruiker.

## <a name="modify-a-configuration"></a>Een configuratie wijzigen

Wanneer u een configuratie wijzigt, worden de wijzigingen onmiddellijk gerepliceerd naar alle doel apparaten. 

Als u de doelvoorwaarde bijwerkt, gebeuren de volgende updates:

* Als een dubbele niet voldoet aan de oude doel voorwaarde, maar voldoet aan de nieuwe doel voorwaarde en deze configuratie de hoogste prioriteit voor die dubbele is, wordt deze configuratie toegepast. 

* Als een dubbele uitvoering van deze configuratie niet meer voldoet aan de doel voorwaarde, worden de instellingen van de configuratie verwijderd en wordt de dubbele wijziging door de configuratie van de volgende hoogste prioriteit gewijzigd. 

* Als een dubbele uitvoering van deze configuratie niet meer voldoet aan de doel voorwaarde en niet voldoet aan de doel voorwaarde van andere configuraties, worden de instellingen van de configuratie verwijderd en worden er geen andere wijzigingen doorgevoerd op de dubbele. 

Gebruik de volgende opdracht om een configuratie bij te werken:

```cli
az iot hub configuration update --config-id [configuration id] \
   --hub-name [hub name] --set [property1.property2='value']
```

* --**config-id** : de naam van de configuratie die in de IOT-hub bestaat.

* --**hub-naam** -naam van de IOT-hub waarin de configuratie bestaat. De hub moet zich in het huidige abonnement. Schakel over naar het gewenste abonnement met de opdracht `az account set -s [subscription name]`.

* --**set** : een eigenschap in de configuratie bijwerken. U kunt de volgende eigenschappen bijwerken:

    * targetCondition-bijvoorbeeld `targetCondition=tags.location.state='Oregon'`

    * labels 

    * priority

## <a name="delete-a-configuration"></a>Een configuratie verwijderen

Wanneer u een configuratie verwijdert, nemen alle apparaatdubbels of module apparaatdubbels de volgende configuratie voor de hoogste prioriteit. Als apparaatdubbels niet voldoet aan de doel voorwaarde van een andere configuratie, worden er geen andere instellingen toegepast. 

Gebruik de volgende opdracht om een configuratie te verwijderen:

```cli
az iot hub configuration delete --config-id [configuration id] \
   --hub-name [hub name] 
```
* --**config-id** : de naam van de configuratie die in de IOT-hub bestaat.

* --**hub-naam** -naam van de IOT-hub waarin de configuratie bestaat. De hub moet zich in het huidige abonnement. Schakel over naar het gewenste abonnement met de opdracht `az account set -s [subscription name]`.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u IoT-apparaten op schaal kunt configureren en controleren. Volg deze koppelingen voor meer informatie over het beheren van Azure IoT Hub:

* [De identiteiten van uw IoT Hub-apparaten bulksgewijs beheren](iot-hub-bulk-identity-mgmt.md)
* [IoT Hub metrische gegevens](iot-hub-metrics.md)
* [Controle van bewerkingen](iot-hub-operations-monitoring.md)

Zie voor meer informatie over de mogelijkheden van IoT Hub:

* [Ontwikkelaars handleiding IoT Hub](iot-hub-devguide.md)
* [AI implementeren op edge-apparaten met Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)

Zie voor meer informatie over het gebruik van de IoT Hub Device Provisioning Service om Zero-Touch, just-in-time inrichten in te scha kelen: 

* [Azure IoT Hub Device Provisioning Service](/azure/iot-dps)
