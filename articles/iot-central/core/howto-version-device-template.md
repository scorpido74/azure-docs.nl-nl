---
title: Informatie over het versie beheer van de app voor uw Azure IoT Central-apps | Microsoft Docs
description: Herhaal uw Apparaatinstellingen door nieuwe versies te maken en zonder uw live verbonden apparaten te beïnvloeden
author: sarahhubbard
ms.author: sahubbar
ms.date: 04/24/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.custom: device-developer
ms.openlocfilehash: 2025b127a428afa478cfe839c7619df2d7d688d3
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2020
ms.locfileid: "90015956"
---
# <a name="create-a-new-device-template-version"></a>Een nieuwe sjabloon versie voor een apparaat maken

*Dit artikel is van toepassing op oplossingenbouwers en apparaatontwikkelaars.*

Een sjabloon voor een apparaat bevat een schema dat beschrijft hoe een apparaat samenwerkt met IoT Central. Deze interacties zijn onder andere telemetrie, eigenschappen en opdrachten. Zowel het apparaat als de IoT Central-toepassing zijn afhankelijk van een gemeen schappelijke uitleg van dit schema om informatie uit te wisselen. U kunt alleen beperkte wijzigingen aanbrengen in het schema zonder het contract te verbreken. Daarom is het belang rijk dat de meeste schema wijzigingen een nieuwe versie van de sjabloon voor het apparaat vereisen. Door de versie van de sjabloon te bepalen, kunnen oudere apparaten door gaan met de schema versie die ze begrijpen, terwijl nieuwere of bijgewerkte apparaten een latere schema versie gebruiken.

Het schema in een apparaatprofiel wordt gedefinieerd in het apparaat Capability model (DCM) en de bijbehorende interfaces. Device-sjablonen bevatten andere informatie, zoals Cloud eigenschappen, weer gave-aanpassingen en weer gaven. Als u wijzigingen aanbrengt in de onderdelen van de sjabloon die niet bepalen hoe het apparaat gegevens uitwisselt met IoT Central, hoeft u geen versie van de sjabloon te maken.

U moet wijzigingen in de sjabloon van een apparaat publiceren, ongeacht of er een versie-update nodig is, voordat een operator deze kan gebruiken. IoT Central stopt met het publiceren van belang rijke wijzigingen in een apparaatprofiel zonder de sjabloon eerst te hoeven indelen.

> [!NOTE]
> Zie [een sjabloon instellen en beheren](howto-set-up-template.md) voor meer informatie over het maken van een sjabloon voor een apparaat.

## <a name="versioning-rules"></a>Versie beheer regels

In deze sectie vindt u een overzicht van de versie beheer regels die van toepassing zijn op Apparaatinstellingen. Zowel DCMs als-interfaces hebben versie nummers. Het volgende code fragment toont de DCM voor een omgevings sensor apparaat. DCM heeft twee interfaces: **DeviceInformation** en **EnvironmentalSensor**. U ziet de versie nummers aan het einde van de `@id` velden. Als u deze informatie wilt weer geven in de IoT Central-gebruikers interface, selecteert u **identiteit weer geven** in de editor voor Apparaatbeheer.

```json
{
  "@id": "urn:contoso:sample_device:1",
  "@type": "CapabilityModel",
  "implements": [
    {
      "@id": "urn:contoso:sample_device:deviceinfo:1",
      "@type": "InterfaceInstance",
      "name": "deviceinfo",
      "schema": {
        "@id": "urn:azureiot:DeviceManagement:DeviceInformation:1",
        "@type": "Interface",
        "displayName": {
          "en": "Device Information"
        },
        "contents": [...
        ]
      }
    },
    {
      "@id": "urn:contoso:sample_device:sensor:1",
      "@type": "InterfaceInstance",
      "name": "sensor",
      "schema": {
        "@id": "urn:contoso:EnvironmentalSensor:2",
        "@type": "Interface",
        "displayName": {
          "en": "Environmental Sensor"
        },
        "contents": [...
        ]
      }
    }
  ],
  "displayName": {
    "en": "Environment Sensor Capability Model"
  },
  "@context": [
    "http://azureiot.com/v1/contexts/IoTModel.json"
  ]
}
```

* Nadat een DCM is gepubliceerd, kunt u geen interfaces verwijderen, zelfs niet in een nieuwe versie van de sjabloon.
* Nadat een DCM is gepubliceerd, kunt u een interface toevoegen als u een nieuwe versie van de sjabloon voor het apparaat maakt.
* Nadat een DCM is gepubliceerd, kunt u een interface met een nieuwere versie vervangen als u een nieuwe versie van de sjabloon voor het apparaat maakt. Als de sensor v1-Device-sjabloon bijvoorbeeld gebruikmaakt van de EnvironmentalSensor v1-interface, kunt u een sensor v2-apparaatprofiel maken die gebruikmaakt van de EnvironmentalSensor v2-interface.
* Nadat een interface is gepubliceerd, kunt u de inhoud van de interface niet meer verwijderen, zelfs in een nieuwe versie van de sjabloon.
* Nadat een interface is gepubliceerd, kunt u items toevoegen aan de inhoud van een interface als u een nieuwe versie van de interface en de apparaatprofiel maakt. Items die u aan de interface kunt toevoegen, zijn onder andere telemetrie, eigenschappen en opdrachten.
* Nadat een interface is gepubliceerd, kunt u niet-schema wijzigingen aanbrengen in bestaande items in de interface als u een nieuwe versie van de interface en de apparaatprofiel maakt. Niet-schema onderdelen van een interface-item bevatten de weergave naam en het semantische type. De schema onderdelen van een interface-item dat u niet kunt wijzigen, zijn naam, type capaciteit en schema.

In de volgende secties worden enkele voor beelden gegeven van het wijzigen van apparaatinstellingen in IoT Central.

## <a name="customize-the-device-template-without-versioning"></a>De sjabloon voor het apparaat aanpassen zonder versie beheer

Bepaalde elementen van de mogelijkheden van uw apparaat kunnen worden bewerkt zonder dat ze de sjabloon en interfaces van uw apparaat hoeven te maken. Enkele van deze velden bevatten bijvoorbeeld weergave naam, semantisch type, minimum waarde, maximum waarde, decimaal posities, kleur, eenheid, weergave-eenheid, opmerking en beschrijving. Een van deze aanpassingen toevoegen:

1. Ga naar de pagina met **Apparaatinstellingen** .
1. Selecteer de sjabloon die u wilt aanpassen.
1. Kies het tabblad **aanpassen** .
1. Alle mogelijkheden die zijn gedefinieerd in het mogelijkheidsprofiel, worden hier weer gegeven. U kunt al deze velden bewerken, opslaan en gebruiken zonder de nood zaak van de sjabloon voor uw apparaat. Als er velden zijn die u wilt bewerken die alleen-lezen zijn, moet u een versie van uw apparaatprofiel maken om deze te wijzigen. Selecteer een veld dat u wilt bewerken en voer een nieuwe waarde in.
1. Klik op **Opslaan**. Deze waarden overschrijven nu alles dat in de sjabloon voor het eerst is opgeslagen en die in de toepassing worden gebruikt.

## <a name="version-a-device-template"></a>Een sjabloon voor een apparaat versie

Als u een nieuwe versie van uw apparaatprofiel maakt, maakt u een concept versie van de sjabloon waarin het mogelijkheidsprofiel kan worden bewerkt. Alle gepubliceerde interfaces blijven gepubliceerd totdat ze afzonderlijk zijn geversied. Als u een gepubliceerde interface wilt wijzigen, maakt u eerst een nieuwe sjabloon versie voor een apparaat.

Alleen de sjabloon van het apparaat wordt gebruikt wanneer u probeert een deel van het mogelijkheidsprofiel te bewerken dat u niet kunt bewerken in de sectie aanpassingen.

Een sjabloon voor een apparaat versie:

1. Ga naar de pagina met **Apparaatinstellingen** .
1. Selecteer de sjabloon voor het apparaat dat u wilt versieren.
1. Klik boven aan de pagina op de knop **versie** en geef de sjabloon een nieuwe naam. IoT Central stelt een nieuwe naam voor die u kunt bewerken.
1. Klik op **Create**.
1. Uw sjabloon voor het apparaat bevindt zich nu in de concept modus. U kunt zien dat uw interfaces nog steeds zijn vergrendeld. De versie van de interfaces die u wilt wijzigen.

## <a name="version-an-interface"></a>Een interface versie

Met versie beheer van een interface kunt u de mogelijkheden in de interface die u al hebt gemaakt, toevoegen, bijwerken en verwijderen.

Een interface versie:

1. Ga naar de pagina met **Apparaatinstellingen** .
1. Selecteer de Device-sjabloon die u in een concept modus hebt.
1. Selecteer de interface in de gepubliceerde modus die u wilt versie en bewerken.
1. Klik boven aan de interface pagina op de knop **versie** .
1. Klik op **Create**.
1. Uw interface bevindt zich nu in de concept modus. U kunt mogelijkheden aan uw interface toevoegen of deze bewerken zonder bestaande aanpassingen en weer gaven te verbreken.

## <a name="migrate-a-device-across-versions"></a>Een apparaat migreren over versies

U kunt meerdere versies van de sjabloon voor het apparaat maken. In de loop van de tijd hebt u meerdere apparaten die zijn verbonden met behulp van deze Apparaatinstellingen. U kunt apparaten van de ene versie van uw apparaatprofiel naar de andere migreren. In de volgende stappen wordt beschreven hoe u een apparaat migreert:

1. Ga naar de pagina **device Explorer** .
1. Selecteer het apparaat dat u naar een andere versie wilt migreren.
1. Kies **migreren**.
1. Selecteer de sjabloon van het-apparaat met het versie nummer waarnaar u het apparaat wilt migreren en kies **migreren**.

![Een apparaat migreren](media/howto-version-device-template/pick-version.png)

## <a name="next-steps"></a>Volgende stappen

Als u een operator of oplossings bouwer bent, is een voorgestelde volgende stap het [beheren van uw apparaten](./howto-manage-devices.md).

Als u een ontwikkelaar van apparaten bent, is een voorgestelde volgende stap meer informatie over [Azure IOT edge apparaten en Azure IOT Central](./concepts-iot-edge.md).
