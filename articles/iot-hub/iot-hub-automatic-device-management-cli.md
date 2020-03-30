---
title: Automatisch apparaatbeheer op schaal met Azure IoT Hub (CLI) | Microsoft Documenten
description: Automatische configuraties van Azure IoT Hub gebruiken om meerdere IoT-apparaten of -modules te beheren
author: ChrisGMsft
manager: bruz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: chrisgre
ms.openlocfilehash: 748f3e09fd03a6f37954c8dfaf4b6ae9144384bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235605"
---
# <a name="automatic-iot-device-and-module-management-using-the-azure-cli"></a>Automatisch IoT-apparaat- en modulebeheer met de Azure CLI

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

Automatisch apparaatbeheer in Azure IoT Hub automatiseert veel van de repetitieve en complexe taken van het beheer van grote apparaatvloten. Met automatisch apparaatbeheer u een set apparaten targeten op basis van hun eigenschappen, een gewenste configuratie definiëren en vervolgens IoT Hub de apparaten laten bijwerken wanneer ze in het bereik komen. Deze update wordt uitgevoerd met behulp van een _automatische apparaatconfiguratie_ of _automatische moduleconfiguratie,_ waarmee u de voltooiing en naleving samenvatten, samenvoegen en conflicten verwerken en configuraties gefaseerd uitrollen.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Automatisch apparaatbeheer werkt door een set apparaattweelingen of moduletweelingen met de gewenste eigenschappen bij te werken en een samenvatting te melden die is gebaseerd op twee gerapporteerde eigenschappen.  Het introduceert een nieuwe klasse en JSON document genaamd een *configuratie* die drie delen heeft:

* De **doelvoorwaarde** definieert de reikwijdte van apparaattweelingen of moduletweelingen die moeten worden bijgewerkt. De doelvoorwaarde wordt opgegeven als query op dubbele tags van het apparaat en/of gerapporteerde eigenschappen.

* De **doelinhoud** definieert de gewenste eigenschappen die moeten worden toegevoegd of bijgewerkt in de beoogde apparaattweeling of moduletweeling. De inhoud bevat een pad naar het gedeelte van de gewenste eigenschappen dat moet worden gewijzigd.

* De **statistieken** definiëren de samenvattingstellingen van verschillende configuratiestatussen, zoals **Succes**, **In uitvoering**en **Fout**. Aangepaste statistieken worden opgegeven als query's op twee gerapporteerde eigenschappen.  Systeemstatistieken zijn de standaardstatistieken die de status van twee updates meten, zoals het aantal tweelingen dat wordt getarget en het aantal tweelingen dat met succes is bijgewerkt.

Automatische configuraties worden voor het eerst uitgevoerd kort nadat de configuratie is gemaakt en vervolgens met intervallen van vijf minuten. Metrische query's worden uitgevoerd telkens wanneer de automatische configuratie wordt uitgevoerd.

## <a name="cli-prerequisites"></a>CLI-vereisten

* Een [IoT-hub](../iot-hub/iot-hub-create-using-cli.md) in uw Azure-abonnement. 

* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) in uw omgeving. Uw Azure CLI-versie moet minimaal 2.0.70 of hoger zijn. Gebruik `az –-version` om de versie te valideren. In deze versie worden az-extensie-opdrachten ondersteund en is voor het eerst het Knack-opdrachtframework opgenomen. 

* De [IoT-extensie voor Azure CLI](https://github.com/Azure/azure-cli).

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="implement-twins"></a>Implementeren tweelingen

Automatische apparaatconfiguraties vereisen het gebruik van apparaattweelingen om de status tussen de cloud en apparaten te synchroniseren.  Zie [Apparaattweelingen begrijpen en gebruiken in IoT Hub](iot-hub-devguide-device-twins.md)voor meer informatie.

Automatische moduleconfiguraties vereisen het gebruik van moduletweelingen om de status tussen de cloud en modules te synchroniseren. Zie [Moduletwins begrijpen en gebruiken in IoT Hub](iot-hub-devguide-module-twins.md)voor meer informatie.

## <a name="use-tags-to-target-twins"></a>Tags gebruiken om tweelingen te targeten

Voordat u een configuratie maakt, moet u opgeven welke apparaten of modules u wilt beïnvloeden. Azure IoT Hub identificeert apparaten en gebruikt tags in de apparaattweeling en identificeert modules met behulp van tags in de moduletwee. Elk apparaat of modules kan meerdere tags hebben, en u ze definiëren op welke manier dan ook zinvol is voor uw oplossing. Als u apparaten bijvoorbeeld op verschillende locaties beheert, voegt u de volgende tags toe aan een apparaattweeling:

```json
"tags": {
    "location": {
        "state": "Washington",
        "city": "Tacoma"
    }
},
```

## <a name="define-the-target-content-and-metrics"></a>De doelinhoud en -statistieken definiëren

De doelinhoud en metrische query's worden opgegeven als JSON-documenten die de gewenste eigenschappen van de apparaattweeling of module beschrijven die moeten worden ingesteld en gerapporteerde eigenschappen op maat.  Als u een automatische configuratie wilt maken met Azure CLI, slaat u de doelinhoud en -statistieken lokaal op als .txt-bestanden. U gebruikt de bestandspaden in een later gedeelte wanneer u de opdracht uitvoert om de configuratie op uw apparaat toe te passen.

Hier is een basisvoorbeeld van doelinhoud voor een automatische apparaatconfiguratie:

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

Automatische moduleconfiguraties gedragen zich zeer op `moduleContent` dezelfde `deviceContent`manier, maar u richt zich in plaats van .

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

Hier volgen voorbeelden van metrische query's:

```json
{
  "queries": {
    "Compliant": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='current'",
    "Error": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='error'",
    "Pending": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='pending'"
  }
}
```

Metrische query's voor modules zijn ook vergelijkbaar met `moduleId` query's voor apparaten, maar u selecteert uit `devices.modules`. Bijvoorbeeld: 

```json
{
  "queries": {
    "Compliant": "select deviceId, moduleId from devices.module where configurations.[[chillermodulesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='current'"
  }
}
```

## <a name="create-a-configuration"></a>Een configuratie maken

U configureert doelapparaten door een configuratie te maken die bestaat uit de doelinhoud en -statistieken. 

Gebruik de volgende opdracht om een configuratie te maken:

```azurecli
   az iot hub configuration create --config-id [configuration id] \
     --labels [labels] --content [file path] --hub-name [hub name] \
     --target-condition [target query] --priority [int] \
     --metrics [metric queries]
```

* --**config-id** - De naam van de configuratie die wordt gemaakt in de IoT-hub. Geef uw configuratie een unieke naam die maximaal 128 kleine letters bedraagt. Vermijd spaties en de `& ^ [ ] { } \ | " < > /`volgende ongeldige tekens: .

* --**labels** - Labels toevoegen om uw configuratie bij te houden. Labels zijn Naam, Waardeparen die uw implementatie beschrijven. Bijvoorbeeld `HostPlatform, Linux` of `Version, 3.0.1`

* --**inhoud** - Inline JSON of bestandspad naar de doelinhoud die moet worden ingesteld als dubbele gewenste eigenschappen. 

* --**hubnaam** - Naam van de IoT-hub waarin de configuratie wordt gemaakt. De hub moet zich in het huidige abonnement bevinden. Overschakelen naar het gewenste abonnement met de opdracht`az account set -s [subscription name]`

* --**doelvoorwaarde** - Voer een doelvoorwaarde in om te bepalen welke apparaten of modules met deze configuratie worden getarget.Voor automatische apparaatconfiguratie is de voorwaarde gebaseerd op dubbele tags van het apparaat of de dubbele gewenste eigenschappen van het apparaat en moet deze overeenkomen met de expressie-indeling.Bijvoorbeeld `tags.environment='test'` of `properties.desired.devicemodel='4000x'`.Voor automatische module configuratie, de voorwaarde is gebaseerd op module twin tags of module twin gewenste eigenschappen.. Bijvoorbeeld `from devices.modules where tags.environment='test'` of `from devices.modules where properties.reported.chillerProperties.model='4000x'`.

* --**prioriteit** - Een positief geheel getal. In het geval dat twee of meer configuraties zijn gericht op hetzelfde apparaat of module, is de configuratie met de hoogste numerieke waarde voor Prioriteit van toepassing.

* --**statistieken** - Filepath naar de metrische query's. Statistieken bieden overzichtstellingen van de verschillende statussen die een apparaat of module kan rapporteren na het toepassen van configuratie-inhoud. U bijvoorbeeld een statistiek maken voor wijzigingen in in behandeling in behandeling, een statistiek voor fouten en een statistiek voor wijzigingen in succesvolle instellingen. 

## <a name="monitor-a-configuration"></a>Een configuratie controleren

Gebruik de volgende opdracht om de inhoud van een configuratie weer te geven:

```azurecli
az iot hub configuration show --config-id [configuration id] \
  --hub-name [hub name]
```

* --**config-id** - De naam van de configuratie die bestaat in de IoT-hub.

* --**hubnaam** - Naam van de IoT-hub waarin de configuratie bestaat. De hub moet zich in het huidige abonnement bevinden. Overschakelen naar het gewenste abonnement met de opdracht`az account set -s [subscription name]`

Controleer de configuratie in het opdrachtvenster.De eigenschap **metrics** bevat een telling voor elke statistiek die door elke hub wordt geëvalueerd:

* **targetedCount** - Een systeemstatistiek die het aantal apparaattweelingen of moduletweelingen in IoT-hub opgeeft dat overeenkomt met de targetingvoorwaarde.

* **appliedCount** - Een systeemstatistiek geeft het aantal apparaten of modules op waarop de doelinhoud is toegepast.

* **Uw aangepaste statistiek** : alle statistieken die u hebt gedefinieerd, zijn gebruikersstatistieken.

U een lijst met apparaat-geïdentificeerde gegevens, module--geïdentificeerde gegevens of objecten voor elk van de statistieken weergeven met behulp van de volgende opdracht:

```azurecli
az iot hub configuration show-metric --config-id [configuration id] \
   --metric-id [metric id] --hub-name [hub name] --metric-type [type] 
```

* --**config-id** - De naam van de implementatie die bestaat in de IoT-hub.

* --**metrische id** - De naam van de statistiek waarvoor u bijvoorbeeld de lijst met apparaat-id's of module-id's wilt `appliedCount`zien.

* --**hubnaam** - Naam van de IoT-hub waarin de implementatie bestaat. De hub moet zich in het huidige abonnement bevinden. Schakel met de opdracht `az account set -s [subscription name]`over naar het gewenste abonnement.

* --**metriektype** - Metrische `system` tekst `user`kan zijn of .  Systeemstatistieken `targetedCount` zijn `appliedCount`en . Alle andere statistieken zijn gebruikersstatistieken.

## <a name="modify-a-configuration"></a>Een configuratie wijzigen

Wanneer u een configuratie wijzigt, worden de wijzigingen onmiddellijk gerepliceerd naar alle beoogde apparaten. 

Als u de doelvoorwaarde bijwerkt, vinden de volgende updates plaats:

* Als een tweeling niet aan de oude doelvoorwaarde voldoet, maar voldoet aan de nieuwe doelvoorwaarde en deze configuratie de hoogste prioriteit heeft voor die tweeling, wordt deze configuratie toegepast. 

* Als een tweeling die deze configuratie uitvoert niet meer voldoet aan de doelvoorwaarde, worden de instellingen van de configuratie verwijderd en wordt de tweeling gewijzigd door de volgende configuratie met de hoogste prioriteit. 

* Als een tweeling die momenteel deze configuratie uitvoert niet meer voldoet aan de doelvoorwaarde en niet voldoet aan de doelvoorwaarde van andere configuraties, worden de instellingen van de configuratie verwijderd en worden er geen andere wijzigingen aangebracht op de tweeling. 

Gebruik de volgende opdracht om een configuratie bij te werken:

```azurecli
az iot hub configuration update --config-id [configuration id] \
   --hub-name [hub name] --set [property1.property2='value']
```

* --**config-id** - De naam van de configuratie die bestaat in de IoT-hub.

* --**hubnaam** - Naam van de IoT-hub waarin de configuratie bestaat. De hub moet zich in het huidige abonnement bevinden. Schakel met de opdracht `az account set -s [subscription name]`over naar het gewenste abonnement.

* --**instellen** - Een eigenschap in de configuratie bijwerken. U de volgende eigenschappen bijwerken:

    * targetCondition - bijvoorbeeld`targetCondition=tags.location.state='Oregon'`

    * labels 

    * priority

## <a name="delete-a-configuration"></a>Een configuratie verwijderen

Wanneer u een configuratie verwijdert, nemen alle apparaattweelingen of moduletweelingen hun volgende configuratie met de hoogste prioriteit over. Als tweelingen niet voldoen aan de doelvoorwaarde van een andere configuratie, worden er geen andere instellingen toegepast. 

Gebruik de volgende opdracht om een configuratie te verwijderen:

```azurecli
az iot hub configuration delete --config-id [configuration id] \
   --hub-name [hub name] 
```

* --**config-id** - De naam van de configuratie die bestaat in de IoT-hub.

* --**hubnaam** - Naam van de IoT-hub waarin de configuratie bestaat. De hub moet zich in het huidige abonnement bevinden. Schakel met de opdracht `az account set -s [subscription name]`over naar het gewenste abonnement.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u IoT-apparaten op schaal configureren en controleren. Volg deze koppelingen voor meer informatie over het beheer van Azure IoT Hub:

* [De identiteiten van uw IoT Hub-apparaten bulksgewijs beheren](iot-hub-bulk-identity-mgmt.md)
* [IoT Hub-statistieken](iot-hub-metrics.md)
* [Controle van bewerkingen](iot-hub-operations-monitoring.md)

Zie:

* [Handleiding voor IoT Hub-ontwikkelaars](iot-hub-devguide.md)
* [AI implementeren op Edge-apparaten met Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)

Zie: 

* [Azure IoT Hub Device Provisioning Service](/azure/iot-dps)
