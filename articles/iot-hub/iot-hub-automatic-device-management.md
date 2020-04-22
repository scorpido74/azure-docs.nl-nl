---
title: Automatisch apparaatbeheer op schaal met Azure IoT Hub | Microsoft Documenten
description: Automatische configuraties van Azure IoT Hub gebruiken om meerdere IoT-apparaten en -modules te beheren
author: Philmea
manager: bruz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: philmea
ms.openlocfilehash: 1de7c34d8f8cbfa139212f29ca6be5d4eca64655
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767502"
---
# <a name="automatic-iot-device-and-module-management-using-the-azure-portal"></a>Automatisch IoT-apparaat- en modulebeheer met behulp van de Azure-portal

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

Automatisch apparaatbeheer in Azure IoT Hub automatiseert veel van de repetitieve en complexe taken van het beheer van grote apparaatvloten. Met automatisch apparaatbeheer u een set apparaten targeten op basis van hun eigenschappen, een gewenste configuratie definiëren en vervolgens IoT Hub de apparaten laten bijwerken wanneer ze in het bereik komen. Deze update wordt uitgevoerd met behulp van een _automatische apparaatconfiguratie_ of _automatische moduleconfiguratie,_ waarmee u de voltooiing en naleving samenvatten, samenvoegen en conflicten verwerken en configuraties gefaseerd uitrollen.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Automatisch apparaatbeheer werkt door een set apparaattweelingen of moduletweelingen met de gewenste eigenschappen bij te werken en een samenvatting te melden die is gebaseerd op twee gerapporteerde eigenschappen.  Het introduceert een nieuwe klasse en JSON document genaamd een *configuratie* die drie delen heeft:

* De **doelvoorwaarde** definieert de reikwijdte van apparaattweelingen of moduletweelingen die moeten worden bijgewerkt. De doelvoorwaarde wordt opgegeven als query op dubbele tags en/of gerapporteerde eigenschappen.

* De **doelinhoud** definieert de gewenste eigenschappen die moeten worden toegevoegd of bijgewerkt in de beoogde apparaattweeling of moduletweeling. De inhoud bevat een pad naar het gedeelte van de gewenste eigenschappen dat moet worden gewijzigd.

* De **statistieken** definiëren de samenvattingstellingen van verschillende configuratiestatussen, zoals **Succes**, **In uitvoering**en **Fout**. Aangepaste statistieken worden opgegeven als query's op twee gerapporteerde eigenschappen.  Systeemstatistieken zijn de standaardstatistieken die de status van twee updates meten, zoals het aantal tweelingen dat wordt getarget en het aantal tweelingen dat met succes is bijgewerkt.

Automatische configuraties worden voor het eerst uitgevoerd kort nadat de configuratie is gemaakt en vervolgens met intervallen van vijf minuten. Metrische query's worden uitgevoerd telkens wanneer de automatische configuratie wordt uitgevoerd.

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

## <a name="create-a-configuration"></a>Een configuratie maken

1. Ga in de [Azure-portal](https://portal.azure.com)naar uw IoT-hub. 

2. Selecteer **De configuratie van IoT-apparaten**.

3. Selecteer **Apparaatconfiguratie toevoegen** of **moduleconfiguratie toevoegen**.

   ![Apparaatconfiguratie of moduleconfiguratie toevoegen](./media/iot-hub-automatic-device-management/create-automatic-configuration.png)

Er zijn vijf stappen om een configuratie te maken. De volgende secties lopen door elk. 

### <a name="name-and-label"></a>Naam en label

1. Geef uw configuratie een unieke naam die maximaal 128 kleine letters bedraagt. Vermijd spaties en de `& ^ [ ] { } \ | " < > /`volgende ongeldige tekens: .

2. Voeg labels toe om uw configuraties bij te houden. Labels zijn **Naam,** **Waardeparen** die uw configuratie beschrijven. Bijvoorbeeld `HostPlatform, Linux` of `Version, 3.0.1`.

3. Selecteer **Volgende** om naar de volgende stap te gaan. 

### <a name="specify-settings"></a>Instellingen opgeven

In deze sectie wordt de inhoud gedefinieerd die moet worden ingesteld in gerichte apparaat- of moduletweelingen. Er zijn twee ingangen voor elke set instellingen. De eerste is de twee pad, dat is het pad naar de JSON sectie binnen de dubbele gewenste eigenschappen die zal worden ingesteld.  De tweede is de JSON-inhoud die in die sectie moet worden ingevoegd. 

U bijvoorbeeld het dubbele `properties.desired.chiller-water` pad instellen en vervolgens de volgende JSON-inhoud leveren: 

```json
{
  "temperature": 66,
  "pressure": 28
}
```

![Het dubbele pad en de inhoud instellen](./media/iot-hub-automatic-device-management/module-config-twin-settings.png)


U ook afzonderlijke instellingen instellen door het volledige dubbele pad op te geven en de waarde zonder haakjes te verstrekken. Stel bijvoorbeeld met het `properties.desired.chiller-water.temperature`dubbele pad `66`de inhoud in op . Maak vervolgens een nieuwe dubbele instelling voor de drukeigenschap. 

Als twee of meer configuraties op hetzelfde dubbele pad zijn gericht, is de inhoud van de configuratie met de hoogste prioriteit van toepassing (prioriteit wordt gedefinieerd in stap 4).

Als u een bestaande eigenschap wilt verwijderen, `null`geeft u de waarde van de eigenschap op aan .

U extra instellingen toevoegen door **De tweeinstelling voor apparaten toevoegen** of De dubbele instelling van de module toevoegen te **selecteren.**

### <a name="specify-metrics-optional"></a>Statistieken opgeven (optioneel)

Statistieken bieden overzichtstellingen van de verschillende statussen die een apparaat of module kan rapporteren na het toepassen van configuratie-inhoud. U bijvoorbeeld een statistiek maken voor wijzigingen in in behandeling in behandeling, een statistiek voor fouten en een statistiek voor wijzigingen in succesvolle instellingen.

Elke configuratie kan maximaal vijf aangepaste statistieken bevatten. 

1. Voer een naam in voor **metrische naam**.

2. Voer een query in voor **metrische criteria**.  De query is gebaseerd op de gerapporteerde eigenschappen van apparaattweeling.  De statistiek vertegenwoordigt het aantal rijen dat door de query wordt geretourneerd.

Bijvoorbeeld:

```sql
SELECT deviceId FROM devices 
  WHERE properties.reported.chillerWaterSettings.status='pending'
```

U een clausule opnemen dat de configuratie is toegepast, bijvoorbeeld: 

```sql
/* Include the double brackets. */
SELECT deviceId FROM devices 
  WHERE configurations.[[yourconfigname]].status='Applied'
```

Als u een statistiek bouwt om te rapporteren `moduleId` `devices.modules`over geconfigureerde modules, selecteert u uit . Bijvoorbeeld:

```sql
SELECT deviceId, moduleId FROM devices.modules
  WHERE properties.reported.lastDesiredStatus.code = 200
```

### <a name="target-devices"></a>Doelapparaten

Gebruik de eigenschap tags van uw tweeling om zich te richten op de specifieke apparaten of modules die deze configuratie moeten ontvangen. U zich ook richten op dubbele gerapporteerde eigenschappen.

Automatische apparaatconfiguraties kunnen alleen op dubbele tags van het apparaat worden gericht en automatische moduleconfiguraties kunnen alleen op dubbele tags van de module worden gericht. 

Aangezien meerdere configuraties zich op hetzelfde apparaat of dezelfde module kunnen richten, heeft elke configuratie een prioriteitsnummer nodig. Als er ooit een conflict is, wint de configuratie met de hoogste prioriteit. 

1. Voer een positief geheel getal in voor de **configuratiePrioriteit**. De hoogste numerieke waarde wordt beschouwd als de hoogste prioriteit. Als twee configuraties hetzelfde prioriteitsnummer hebben, wint degene die onlangs is gemaakt. 

2. Voer een **doelvoorwaarde** in om te bepalen welke apparaten of modules met deze configuratie worden getarget. De voorwaarde is gebaseerd op dubbele tags of dubbele gerapporteerde eigenschappen en moet overeenkomen met de expressie-indeling. 

   Voor automatische apparaatconfiguratie u alleen de tag of gerapporteerde eigenschap opgeven die u wilt targeten. Bijvoorbeeld `tags.environment='test'` of `properties.reported.chillerProperties.model='4000x'`. U kunt `*` opgeven om alle apparaten te targeten. 
   
   Gebruik voor automatische moduleconfiguratie een query om tags of gerapporteerde eigenschappen op te geven van de modules die zijn geregistreerd op de IoT-hub. Bijvoorbeeld `from devices.modules where tags.environment='test'` of `from devices.modules where properties.reported.chillerProperties.model='4000x'`. De wildcard kan niet worden gebruikt om alle modules te targeten. 

3. Selecteer **Volgende** om door te gaan naar de laatste stap.

### <a name="review-configuration"></a>Configuratie controleren

Controleer uw configuratiegegevens en selecteer **Verzenden**.

## <a name="monitor-a-configuration"></a>Een configuratie controleren

Als u de details van een configuratie wilt weergeven en de apparaten wilt controleren waarop deze wordt uitgevoerd, voert u de volgende stappen uit:

1. Ga in de [Azure-portal](https://portal.azure.com)naar uw IoT-hub. 

2. Selecteer **De configuratie van IoT-apparaten**.

3. Controleer de configuratielijst. Voor elke configuratie u de volgende details bekijken:

   * **ID** - de naam van de configuratie.

   * **Doelvoorwaarde** - de query die wordt gebruikt om gerichte apparaten of modules te definiëren.

   * **Prioriteit** - het prioriteitsnummer dat aan de configuratie is toegewezen.

   * **Aanmaaktijd** - de tijdstempel van toen de configuratie is gemaakt. Deze tijdstempel wordt gebruikt om banden te verbreken wanneer twee configuraties dezelfde prioriteit hebben. 

   * **Systeemstatistieken** - statistieken die worden berekend door IoT Hub en niet kunnen worden aangepast door ontwikkelaars. Targeted geeft het aantal apparaattweelingen op dat overeenkomt met de doelconditie. Past het aantal apparaattweelingen toe dat door de configuratie is gewijzigd, wat gedeeltelijke wijzigingen kan omvatten in het geval dat een afzonderlijke configuratie met een hogere prioriteit ook wijzigingen heeft aangebracht. 

   * **Aangepaste statistieken** - statistieken die door de ontwikkelaar zijn opgegeven als query's tegen twee gerapporteerde eigenschappen.  Per configuratie kunnen maximaal vijf aangepaste statistieken worden gedefinieerd. 
   
4. Selecteer de configuratie die u wilt controleren.  

5. Controleer de configuratiegegevens. U tabbladen gebruiken om specifieke details te bekijken over de apparaten die de configuratie hebben ontvangen.

   * **Doelconditie** - de apparaten of modules die overeenkomen met de doelconditie. 

   * **Statistieken** - een lijst met systeemstatistieken en aangepaste statistieken.  U een lijst met apparaten of modules weergeven die voor elke statistiek worden geteld door de statistiek in de vervolgkeuzelijst te selecteren en vervolgens **Apparaten** of **Weergavemodules te**selecteren.

   * **Apparaat Twin Instellingen** of **Module Twin Instellingen** - de dubbele instellingen die zijn ingesteld door de configuratie. 

   * **Configuratielabels** - sleutelwaardeparen die worden gebruikt om een configuratie te beschrijven.  Labels hebben geen invloed op de functionaliteit. 

## <a name="modify-a-configuration"></a>Een configuratie wijzigen

Wanneer u een configuratie wijzigt, worden de wijzigingen onmiddellijk gerepliceerd naar alle beoogde apparaten of modules. 

Als u de doelvoorwaarde bijwerkt, vinden de volgende updates plaats:

* Als een tweeling niet aan de oude doelvoorwaarde voldoet, maar voldoet aan de nieuwe doelvoorwaarde en deze configuratie de hoogste prioriteit heeft voor die tweeling, wordt deze configuratie toegepast. 

* Als een tweeling die deze configuratie uitvoert niet meer voldoet aan de doelvoorwaarde, worden de instellingen van de configuratie verwijderd en wordt de tweeling gewijzigd door de volgende configuratie met de hoogste prioriteit. 

* Als een tweeling die momenteel deze configuratie uitvoert niet meer voldoet aan de doelvoorwaarde en niet voldoet aan de doelvoorwaarde van andere configuraties, worden de instellingen van de configuratie verwijderd en worden er geen andere wijzigingen aangebracht op de tweeling. 

Als u een configuratie wilt wijzigen, gebruikt u de volgende stappen: 

1. Ga in de [Azure-portal](https://portal.azure.com)naar uw IoT-hub. 

2. Selecteer **De configuratie van IoT-apparaten**. 

3. Selecteer de configuratie die u wilt wijzigen. 

4. Breng de volgende velden bij: 

   * Doelvoorwaarde 
   * Labels 
   * Prioriteit 
   * Metrische gegevens

4. Selecteer **Opslaan**.

5. Volg de stappen in [Monitor een configuratie](#monitor-a-configuration) om de wijzigingen uit te rollen. 

## <a name="delete-a-configuration"></a>Een configuratie verwijderen

Wanneer u een configuratie verwijdert, nemen alle apparaattweelingen hun volgende configuratie met de hoogste prioriteit over. Als apparaattweelingen niet voldoen aan de doelvoorwaarde van een andere configuratie, worden er geen andere instellingen toegepast. 

1. Ga in de [Azure-portal](https://portal.azure.com)naar uw IoT-hub. 

2. Selecteer **De configuratie van IoT-apparaten**. 

3. Gebruik het selectievakje om de configuratie in te schakelen die u wilt verwijderen. 

4. Selecteer **Verwijderen**.

5. Een prompt zal u vragen om te bevestigen.

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
