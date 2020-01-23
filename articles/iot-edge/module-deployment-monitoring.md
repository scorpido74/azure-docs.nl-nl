---
title: Automatische implementatie voor apparaatgroepen - Azure IoT Edge | Microsoft Docs
description: Gebruik van automatische implementaties in Azure IoT Edge voor groepen van apparaten op basis van gedeelde tags beheren
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/12/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 406830add1891a058e9b43fccb8435aa4d339ed0
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548676"
---
# <a name="understand-iot-edge-automatic-deployments-for-single-devices-or-at-scale"></a>Informatie over IoT Edge-automatische implementaties voor individuele apparaten of op schaal

Automatische implementaties en gelaagde implementaties helpen u bij het beheren en configureren van modules op grote aantallen IoT Edge apparaten.

Azure IoT Edge biedt twee manieren om de modules te configureren die op IoT Edge apparaten kunnen worden uitgevoerd. De eerste methode is om modules per apparaat te implementeren. U maakt een implementatie manifest en past dit vervolgens op naam toe op een bepaald apparaat. Bij de tweede methode worden modules automatisch geïmplementeerd op alle geregistreerde apparaten die voldoen aan een set gedefinieerde voor waarden. U maakt een implementatie manifest en definieert vervolgens op basis van [Tags](../iot-edge/how-to-deploy-monitor.md#identify-devices-using-tags) op het apparaat dubbele.

Dit artikel richt zich op het configureren en bewaken van wagentjes van apparaten, gezamenlijk aangeduid als *IOT Edge automatische implementaties*. De eenvoudige implementaties tappen zijn als volgt:

1. Een operator definieert een implementatie die een set modules en de doel apparaten beschrijft. Elke implementatie heeft een implementatie manifest dat deze informatie weergeeft.
2. De IoT Hub-service communiceert met alle doel apparaten om ze te configureren met de gedeclareerde modules.
3. De IoT Hub-service wordt de status opgehaald van de IoT Edge-apparaten en maakt ze beschikbaar voor de operator.  Een operator kan bijvoorbeeld zien wanneer een edge-apparaat niet is geconfigureerd of dat een module tijdens runtime mislukt.
4. Op elk gewenst moment zijn nieuwe IoT Edge-apparaten die voldoen aan de doelitems voorwaarden geconfigureerd voor de implementatie.

Dit artikel wordt beschreven voor elk onderdeel is betrokken bij het configureren en controleren van een implementatie. Zie voor een overzicht van het maken en bijwerken van een implementatie [implementeren en controleren van IoT Edge-modules op schaal](how-to-deploy-monitor.md).

## <a name="deployment"></a>Implementatie

De automatische implementatie van een IoT Edge toegewezen IoT Edge module installatiekopieën uit te voeren als de exemplaren in een bepaalde set van IoT Edge-apparaten. Dit gebeurt door het configureren van een manifest van de IoT Edge-implementatie om op te nemen van een lijst met modules met de bijbehorende initialisatieparameters. Een implementatie kan worden toegewezen aan één apparaat (op basis van apparaat-ID) of aan een groep apparaten (op basis van labels). Nadat een IoT Edge-apparaat een implementatie manifest heeft ontvangen, worden de container installatie kopieën uit de respectieve container opslagplaatsen gedownload en geïnstalleerd en worden ze dienovereenkomstig geconfigureerd. Zodra een implementatie is gemaakt, kan een operator de implementatie status controleren om te zien of de doel apparaten correct zijn geconfigureerd.

IoT Edge-apparaten kunnen worden geconfigureerd met een implementatie. De volgende vereisten moeten zijn op het apparaat voordat de implementatie kan ontvangen:

* Het basisbesturingssysteem
* Een container management-systeem, zoals Moby of Docker
* Het inrichten van IoT Edge-runtime

### <a name="deployment-manifest"></a>Distributiemanifest

Het manifest voor een implementatie is een JSON-document met een beschrijving van de modules worden geconfigureerd op de betreffende IoT Edge-apparaten. Het bevat de metagegevens van de configuratie voor alle modules, met inbegrip van de modules voor het systeem verplicht (specifiek de IoT Edge-agent en IoT Edge hub).  

De metagegevens van de configuratie voor elke module bevat:

* Versie
* Type
* Status (bijvoorbeeld wordt uitgevoerd of gestopt)
* Beleid voor opnieuw opstarten
* Afbeeldings- en container registry
* Routes voor invoer en uitvoer

Als de module-installatiekopie is opgeslagen in een privécontainerregister, bevat de IoT Edge-agent de registerreferenties.

### <a name="target-condition"></a>Doelvoorwaarde

De doel voorwaarde wordt continu geëvalueerd gedurende de levens duur van de implementatie. Nieuwe apparaten die voldoen aan de vereisten zijn opgenomen en alle bestaande apparaten die niet langer worden verwijderd. De implementatie opnieuw wordt geactiveerd als de service wordt gedetecteerd door elke wijziging van de voorwaarde doel.

U hebt bijvoorbeeld een implementatie met een doel voorwaarde label. Environment = ' Prod '. Wanneer u een vliegende start de implementatie, zijn er 10 productieapparaten. De modules zijn geïnstalleerd in deze 10-apparaten. In de status van de IoT Edge-agent worden 10 apparaten weer gegeven, 10 geslaagde reacties, 0 mislukte antwoorden en 0 Reacties in behandeling. Nu u bij het toevoegen van vijf meer apparaten met tags.environment = 'prod'. De service detecteert de wijziging en de status van de IoT Edge-agent wordt 15 totaal aantal apparaten, 10 geslaagde reacties, 0 mislukte antwoorden en vijf in behandeling zijnde antwoorden tijdens de implementatie van de vijf nieuwe apparaten.

Gebruik een Boole-voorwaarde op device twins tags of deviceId de doelapparaten selecteren. Als u wilt de voorwaarde met tags gebruiken, moet u 'tags' toevoegen:{} sectie in de apparaatdubbel onder hetzelfde niveau als eigenschappen. [Meer informatie over tags in de apparaatdubbel](../iot-hub/iot-hub-devguide-device-twins.md)

Voor beelden van doel voorwaarden:

* apparaat-id = 'linuxprod1'
* tags.Environment = 'prod'
* tags.Environment = 'prod' AND tags.location = 'westus'
* tags.Environment = 'prod' of tags.location = 'westus'
* tags.operator = 'John' en tags.environment = 'prod' geen apparaat-id = 'linuxprod1'

Hier volgen enkele beperkingen wanneer u een doelvoorwaarde maken:

* In de apparaatdubbel, kunt u alleen een doelvoorwaarde met behulp van labels of deviceId bouwen.
* Dubbele aanhalingstekens zijn niet toegestaan in een gedeelte van de doelvoorwaarde. Gebruikt u enkele aanhalingstekens.
* Enkele aanhalingstekens vertegenwoordigen de waarden van de doelvoorwaarde. Daarom moet u de enkel aanhalingsteken met een andere enkel aanhalingsteken escape-als het deel van de naam van het apparaat uitmaakt. Bijvoorbeeld: op een apparaat met de naam `operator'sDevice`, schrijven `deviceId='operator''sDevice'`.
* Cijfers, letters en de volgende tekens zijn toegestaan in de voorwaarde doelwaarden: `-:.+%_#*?!(),=@;$`.

### <a name="priority"></a>Prioriteit

Een prioriteit bepaalt of een implementatie moet worden toegepast op een doelapparaat ten opzichte van andere implementaties. De prioriteit van een implementatie is een positief geheel getal, met grotere aantallen die aangeeft hogere prioriteit. Als een IoT Edge-apparaat door meer dan één implementatie is gericht, wordt de implementatie met de hoogste prioriteit is van toepassing.  Implementaties met lagere prioriteiten worden niet toegepast en worden niet samengevoegd.  Als een apparaat is gericht op twee of meer implementaties met een gelijke prioriteit, is de meest recent gemaakte implementatie (bepaald door de tijds tempel voor maken) van toepassing.

### <a name="labels"></a>Labels

Labels zijn teken reeks sleutel-waardeparen die u kunt gebruiken voor het filteren en groeperen van implementaties. Een implementatie kan meerdere labels hebben. Labels zijn optioneel en zijn niet van invloed op de werkelijke configuratie van IoT Edge apparaten.

### <a name="metrics"></a>Metrische gegevens

Standaard worden alle implementaties gerapporteerd op basis van vier metrische gegevens:

* **Doel** geeft de IOT edge apparaten weer die overeenkomen met de voor waarde voor de implementatie doelstelling.
* **Toegepast** toont de doel IOT edge apparaten die niet zijn gericht op een andere implementatie van een hogere prioriteit.
* Bij **geslaagde rapportage** worden de IOT edge-apparaten weer gegeven die zijn gerapporteerd aan de service dat de modules zijn geïmplementeerd.
* **Fout bij het rapporteren** geeft de IOT edge-apparaten weer die zijn gerapporteerd aan de service dat een of meer modules niet met succes zijn geïmplementeerd. Voor verder onderzoek van de fout, extern verbinding maken met deze apparaten en de logboekbestanden.

Daarnaast kunt u uw eigen aangepaste metrische gegevens definiëren om de implementatie te bewaken en beheren.

Metrische gegevens geven samen vattingen van de verschillende statussen die apparaten kunnen terugsturen als gevolg van het Toep assen van een implementatie configuratie. Met metrische gegevens kunt u een query uitvoeren op [dubbele gerapporteerde eigenschappen van edgeHub-module](module-edgeagent-edgehub.md#edgehub-reported-properties), zoals *lastDesiredStatus* of *lastConnectTime*. Bijvoorbeeld:

```sql
SELECT deviceId FROM devices
  WHERE properties.reported.lastDesiredStatus.code = 200
```

Het toevoegen van uw eigen metrische gegevens is optioneel en heeft geen invloed op de werkelijke configuratie van IoT Edge apparaten.

## <a name="layered-deployment"></a>Gelaagde implementatie

Gelaagde implementaties zijn automatische implementaties die samen kunnen worden gecombineerd om het aantal unieke implementaties te verminderen dat moet worden gemaakt. Gelaagde implementaties zijn handig in scenario's waarin dezelfde modules opnieuw worden gebruikt in verschillende combi Naties in veel automatische implementaties.

Gelaagde implementaties hebben dezelfde basis onderdelen als automatische implementatie. Ze zijn gericht op apparaten op basis van tags in de apparaatdubbels van het apparaat en bieden dezelfde functionaliteit voor labels, metrische gegevens en status rapportage. Aan gelaagde implementaties zijn ook prioriteiten toegewezen, maar in plaats van de prioriteit te gebruiken om te bepalen welke implementatie op een apparaat wordt toegepast, bepaalt de prioriteit hoe meerdere implementaties op een apparaat worden gerangschikt. Als twee gelaagde implementaties bijvoorbeeld een module of een route met dezelfde naam hebben, wordt de gelaagde implementatie met de hogere prioriteit toegepast terwijl de lagere prioriteit wordt overschreven.

De System runtime modules, edgeAgent en edgeHub, zijn niet geconfigureerd als onderdeel van een gelaagde implementatie. Op alle IoT Edge apparaten waarop een gelaagde implementatie is gericht, moet eerst een standaard automatische implementatie worden toegepast om de basis op te geven waarop gelaagde implementaties kunnen worden toegevoegd.

Een IoT Edge apparaat kan één en slechts één standaard automatische implementatie Toep assen, maar kan meerdere gelaagde automatische implementaties Toep assen. Gelaagde implementaties die zijn gericht op een apparaat, moeten een hogere prioriteit hebben dan de automatische implementatie voor dat apparaat.

Denk bijvoorbeeld aan het volgende scenario van een bedrijf dat gebouwen beheert. Ze hebben IoT Edge modules ontwikkeld voor het verzamelen van gegevens uit beveiligings camera's, bewegings Sens oren en liften. Niet al hun gebouwen kunnen echter alle drie de modules gebruiken. Met standaard automatische implementaties moet het bedrijf afzonderlijke implementaties maken voor alle module combinaties die hun gebouwen nodig hebben.

![Standaard automatische implementaties moeten elke module combinatie bevatten](./media/module-deployment-monitoring/standard-deployment.png)

Zodra het bedrijf overschakelt naar gelaagde automatische implementaties, kunnen ze echter dezelfde module combinaties voor hun gebouwen maken met minder implementaties om te beheren. Elke module heeft een eigen gelaagde implementatie en de labels van apparaten bepalen welke modules aan elk gebouw worden toegevoegd.

![Gelaagde automatische implementaties vereenvoudigen scenario's waarbij dezelfde modules op verschillende manieren worden gecombineerd](./media/module-deployment-monitoring/layered-deployment.png)

### <a name="module-twin-configuration"></a>Dubbele configuratie van module

Wanneer u werkt met gelaagde implementaties, kunt u op een of andere manier twee implementaties met dezelfde module richten als een apparaat. In dergelijke gevallen kunt u beslissen of de implementatie met een hogere prioriteit de module moet overschrijven of toevoegen. U hebt bijvoorbeeld een implementatie met dezelfde module op 100 verschillende apparaten. 10 van die apparaten bevinden zich echter in beveiligde faciliteiten en hebben extra configuratie nodig om te communiceren via proxy servers. U kunt een gelaagde implementatie gebruiken om module dubbele eigenschappen toe te voegen die ervoor zorgen dat deze 10 apparaten veilig kunnen communiceren zonder de bestaande module dubbele informatie van de basis implementatie te overschrijven.

U kunt module dubbele gewenste eigenschappen toevoegen in het implementatie manifest. Wanneer u in een standaard implementatie eigenschappen zou toevoegen in de **Eigenschappen. gewenste** sectie van de module dubbele, in een gelaagde implementatie kunt u een nieuwe subset van gewenste eigenschappen declareren.

In een standaard implementatie kunt u bijvoorbeeld de gesimuleerde temperatuur sensor module toevoegen met de volgende gewenste eigenschappen die aangeven dat de gegevens binnen vijf seconden moeten worden verzonden:

```json
"SimulatedTemperatureSensor": {
  "properties.desired": {
    "SendData": true,
    "SendInterval": 5
  }
}
```

In een gelaagde implementatie die gericht is op dezelfde apparaten of een subset van dezelfde apparaten, wilt u mogelijk een extra eigenschap toevoegen die aangeeft dat de gesimuleerde sensor 1000 berichten verzendt en vervolgens stopt. U wilt de bestaande eigenschappen niet overschrijven, dus maakt u een nieuwe sectie in de gewenste eigenschappen met de naam `layeredProperties`, die de nieuwe eigenschap bevat:

```json
"SimulatedTemperatureSensor": {
  "properties.desired.layeredProperties": {
    "StopAfterCount": 1000
  }
}
```

Op een apparaat waarop zowel implementaties zijn toegepast, worden de volgende eigenschappen weer gegeven in de module twee voor de gesimuleerde temperatuur sensor:

```json
"properties": {
  "desired": {
    "SendData": true,
    "SendInterval": 5,
    "layeredProperties": {
      "StopAfterCount": 1000
    }
  }
}
```

Als u het `properties.desired` veld van de module dubbele in een gelaagde implementatie instelt, worden de gewenste eigenschappen voor die module overschreven in een implementatie met een lagere prioriteit.

## <a name="phased-rollout"></a>Gefaseerde implementatie

Een gefaseerde implementatie is een algemene proces waarbij wijzigingen in een operator worden geïmplementeerd op een leven set IoT Edge-apparaten. Het doel is om wijzigingen aanbrengen geleidelijk in vermindert het risico van het maken van grote schaal belangrijke wijzigingen. Automatische implementaties helpen gefaseerde implementaties te beheren in een vloot van IoT Edge apparaten.

Een gefaseerde implementatie wordt uitgevoerd in de volgende fasen en stappen:

1. Een testomgeving van IoT Edge-apparaten tot stand brengen door ze inrichting en het instellen van een label van het dubbele apparaat, zoals `tag.environment='test'`. In de test omgeving moet de productie omgeving worden gespiegeld die de implementatie uiteindelijk gaat richten.
2. Maak een implementatie met inbegrip van de gewenste modules en configuraties. De doelitems voorwaarde moet het doel de test-omgeving van IoT Edge-apparaten.
3. Valideer de moduleconfiguratie van de nieuwe in de testomgeving.
4. De implementatie zodat een subset van de productie IoT Edge-apparaten die door een nieuwe tag toe te voegen aan de voorwaarde doelitems bijwerken. Zorg er ook voor dat de prioriteit voor de implementatie hoger dan andere implementaties op dit moment is gericht op deze apparaten is
5. Controleer of dat de implementatie is voltooid op de betreffende IoT-apparaten door de implementatiestatus te bekijken.
6. Bijwerken van de implementatie wilt richten op alle resterende productie IoT Edge-apparaten.

## <a name="rollback"></a>Terugdraaien

Implementaties kunnen worden teruggedraaid terug als er fouten of onjuiste configuraties. Omdat een implementatie de absolute module configuratie voor een IoT Edge apparaat definieert, moet een extra implementatie ook met een lagere prioriteit op hetzelfde apparaat worden gericht, zelfs als het doel is om alle modules te verwijderen.  

Als u een implementatie verwijdert, worden de modules niet verwijderd van de doel apparaten. Er moet een andere implementatie zijn die een nieuwe configuratie voor de apparaten definieert, zelfs als het een lege implementatie is.

Terugdraaiacties in de volgende handelingen uitvoeren:

1. Bevestig dat een tweede implementatie ook is gericht op dezelfde apparaatset. Als het doel van het terugdraaien is het verwijderen van alle modules, moet alle modules die niet in de tweede implementatie bevatten.
2. Wijzigen of verwijderen van de doel-voorwaarde-expressie van de implementatie die u wilt opnieuw zodat de apparaten niet langer voldoet aan de doelitems voorwaarde samenvouwen.
3. Controleren of het terugdraaien is geslaagd door de implementatiestatus te bekijken.
   * De service-Update-back-implementatie moet status voor de apparaten die worden teruggedraaid niet meer weergeven.
   * De tweede implementatie bevatten moet nu de implementatiestatus voor de apparaten die worden teruggedraaid.

## <a name="next-steps"></a>Volgende stappen

* Doorloop de stappen voor het maken, bijwerken of verwijderen van een implementatie in [implementeren en controleren van IoT Edge-modules op schaal](how-to-deploy-monitor.md).
* Meer informatie over andere IoT Edge-concepten, zoals de [IoT Edge-runtime](iot-edge-runtime.md) en [IoT Edge-modules](iot-edge-modules.md).
