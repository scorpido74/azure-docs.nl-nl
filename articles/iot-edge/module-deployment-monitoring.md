---
title: Automatische implementatie voor apparaatgroepen - Azure IoT Edge | Microsoft Documenten
description: Automatische implementaties gebruiken in Azure IoT Edge om groepen apparaten te beheren op basis van gedeelde tags
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/30/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 8aaac6100ba980301ff3e85a3ac3959bfee89b49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76895966"
---
# <a name="understand-iot-edge-automatic-deployments-for-single-devices-or-at-scale"></a>IoT Edge-automatische implementaties voor afzonderlijke apparaten of op schaal begrijpen

Automatische implementaties en gelaagde implementaties helpen u bij het beheren en configureren van modules op grote aantallen IoT Edge-apparaten.

Azure IoT Edge biedt twee manieren om de modules te configureren die op IoT Edge-apparaten worden uitgevoerd. De eerste methode is het implementeren van modules per apparaat. U maakt een implementatiemanifest en past het vervolgens op naam van een bepaald apparaat toe. De tweede methode is om modules automatisch te implementeren op elk geregistreerd apparaat dat voldoet aan een reeks gedefinieerde voorwaarden. U maakt een implementatiemanifest en definieert vervolgens op welke apparaten het is op basis van [tags](../iot-edge/how-to-deploy-monitor.md#identify-devices-using-tags) in de apparaattweeling.

Dit artikel richt zich op het configureren en bewaken van vloten van apparaten, gezamenlijk aangeduid als *IoT Edge automatische implementaties.*De basisimplementatiestappen zijn als volgt:

1. Een operator definieert een implementatie die een set modules en de doelapparaten beschrijft.Elke implementatie heeft een implementatiemanifest dat deze informatie weergeeft.
2. De IoT Hub-service communiceert met alle beoogde apparaten om ze te configureren met de gedeclareerde modules.
3. De IoT Hub-service haalt de status op van de IoT Edge-apparaten en maakt deze beschikbaar voor de operator.Een operator kan bijvoorbeeld zien wanneer een Edge-apparaat niet is geconfigureerd of als een module tijdens runtime uitvalt.
4. Op elk gewenst moment worden nieuwe IoT Edge-apparaten die voldoen aan de targetingvoorwaarden geconfigureerd voor de implementatie.

In dit artikel wordt elke component beschreven die betrokken is bij het configureren en bewaken van een implementatie. Zie [IoT Edge-modules implementeren en controleren op schaal voor](how-to-deploy-monitor.md)een walkthrough van het maken en bijwerken van een implementatie.

## <a name="deployment"></a>Implementatie

Met een automatische implementatie van IoT Edge worden IoT Edge-moduleafbeeldingen als instanties uitgevoerd op een gerichte set IoT Edge-apparaten. Het werkt door het configureren van een IoT Edge implementatie manifest om een lijst van modules met de bijbehorende initialisatie parameters op te nemen.Een implementatie kan worden toegewezen aan één apparaat (op basis van apparaat-id) of aan een groep apparaten (op basis van tags).Zodra een IoT Edge-apparaat een implementatiemanifest ontvangt, worden de containerafbeeldingen van de desbetreffende containeropslagplaatsen gedownload en geïnstalleerd en worden deze dienovereenkomstig geconfigureerd.Zodra een implementatie is gemaakt, kan een operator de implementatiestatus controleren om te zien of gerichte apparaten correct zijn geconfigureerd.

Alleen IoT Edge-apparaten kunnen worden geconfigureerd met een implementatie. De volgende vereisten moeten op het apparaat staan voordat het de implementatie kan ontvangen:

* Het basisbesturingssysteem
* Een containerbeheersysteem, zoals Moby of Docker
* Inrichten van de Runtime van IoT Edge

### <a name="deployment-manifest"></a>Distributiemanifest

Een implementatiemanifest is een JSON-document waarin de modules worden beschreven die moeten worden geconfigureerd op de beoogde IoT Edge-apparaten. Het bevat de configuratiemetadata voor alle modules, inclusief de vereiste systeemmodules (met name de IoT Edge-agent en IoT Edge-hub).  

De configuratiemetagegevens voor elke module bevatten:

* Versie
* Type
* Status (bijvoorbeeld uitvoeren of gestopt)
* Beleid voor opnieuw starten
* Afbeeldings- en containerregister
* Routes voor gegevensinvoer en -uitvoer

Als de moduleafbeelding is opgeslagen in een register van privécontainers, bevat de IoT Edge-agent de registerreferenties.

### <a name="target-condition"></a>Doelvoorwaarde

De doelvoorwaarde wordt gedurende de gehele levensduur van de implementatie continu geëvalueerd. Alle nieuwe apparaten die aan de vereisten voldoen, zijn inbegrepen en alle bestaande apparaten die dat niet meer doen, worden verwijderd. De implementatie wordt opnieuw geactiveerd als de service een doelvoorwaardewijziging detecteert.

U hebt bijvoorbeeld een implementatie met een doelvoorwaardetags.environment = 'prod'. Wanneer u de implementatie start, zijn er 10 productieapparaten. De modules zijn met succes geïnstalleerd in deze 10 apparaten. De iot edge-agentstatus toont 10 apparaten in totaal, 10 succesvolle reacties, 0 foutreacties en 0 in behandeling zijnde reacties. Nu voegt u nog vijf apparaten toe met tags.environment = 'prod'. De service detecteert de wijziging en de IoT Edge-agentstatus wordt 15 apparaten in totaal, 10 succesvolle reacties, 0 foutreacties en 5 in behandeling zijnde reacties terwijl deze wordt geïmplementeerd op de vijf nieuwe apparaten.

Gebruik een Booleaanse toestand op dubbele tags van het apparaat, dubbele gerapporteerde eigenschappen van het apparaat of deviceId om de doelapparaten te selecteren. Als u de voorwaarde met tags wilt gebruiken,{} moet u 'tags' toevoegen: sectie in de apparaattweeling onder hetzelfde niveau als eigenschappen. [Meer informatie over tags in apparaattweeling](../iot-hub/iot-hub-devguide-device-twins.md)

Voorbeelden van doelvoorwaarden:

* deviceId ='linuxprod1'
* tags.environment ='prod'
* tags.environment = 'prod' EN tags.location = 'westus'
* tags.environment = 'prod' OR tags.location = 'westus'
* tags.operator = 'John' AND tags.environment = 'prod' NOT deviceId = 'linuxprod1'
* properties.reported.devicemodel = '4000x'

Houd rekening met deze beperkingen wanneer u een doelvoorwaarde maakt:

* In apparaattweeling u alleen een doelvoorwaarde bouwen met behulp van tags, gerapporteerde eigenschappen of deviceId.
* Dubbele aanhalingstekens zijn niet toegestaan in een deel van de doelvoorwaarde. Gebruik enkele aanhalingstekens.
* Enkele aanhalingstekens vertegenwoordigen de waarden van de doelvoorwaarde. Daarom moet u ontsnappen aan de enkele offerte met een andere enkele offerte als het deel uitmaakt van de naam van het apparaat. Bijvoorbeeld, om een apparaat `operator'sDevice`genaamd `deviceId='operator''sDevice'`richten , schrijven .
* Cijfers, letters en de volgende tekens zijn toegestaan `-:.+%_#*?!(),=@;$`in doelconditiewaarden: .

### <a name="priority"></a>Prioriteit

Een prioriteit bepaalt of een implementatie moet worden toegepast op een doelapparaat ten opzichte van andere implementaties. Een implementatieprioriteit is een positief geheel getal, waarbij grotere getallen een hogere prioriteit aangeven. Als een IoT Edge-apparaat is gericht op meer dan één implementatie, is de implementatie met de hoogste prioriteit van toepassing.Implementaties met lagere prioriteiten worden niet toegepast en worden ook niet samengevoegd.Als een apparaat is gericht met twee of meer implementaties met gelijke prioriteit, is de meest recent gemaakte implementatie (bepaald door de tijdstempel voor maken) van toepassing.

### <a name="labels"></a>Labels

Labels zijn tekenreekssleutel-/waardeparen die u gebruiken om implementaties te filteren en te groeperen.Een implementatie kan meerdere labels hebben. Labels zijn optioneel en hebben geen invloed op de werkelijke configuratie van IoT Edge-apparaten.

### <a name="metrics"></a>Metrische gegevens

Standaard rapporteren alle implementaties over vier statistieken:

* **Targeted** toont de IoT Edge-apparaten die overeenkomen met de voorwaarde implementatietargeting.
* **Applied** toont de beoogde IoT Edge-apparaten die niet zijn gericht op een andere implementatie met een hogere prioriteit.
* **Rapportage succes** toont de IoT Edge-apparaten die hebben gemeld dat de modules zijn geïmplementeerd met succes.
* **Rapportagefout** toont de IoT Edge-apparaten die hebben gemeld dat een of meer modules niet zijn geïmplementeerd. Als u de fout verder wilt onderzoeken, maakt u op afstand verbinding met deze apparaten en bekijkt u de logboekbestanden.

Daarnaast u uw eigen aangepaste statistieken definiëren om de implementatie te controleren en te beheren.

Statistieken bieden overzichtstellingen van de verschillende statussen die apparaten kunnen rapporteren als gevolg van het toepassen van een implementatieconfiguratie. Metrics kunnen de dubbele gerapporteerde eigenschappen van [de EdgeHub-module](module-edgeagent-edgehub.md#edgehub-reported-properties)opvragen, zoals *lastDesiredStatus* of *lastConnectTime*. Bijvoorbeeld:

```sql
SELECT deviceId FROM devices
  WHERE properties.reported.lastDesiredStatus.code = 200
```

Het toevoegen van uw eigen statistieken is optioneel en heeft geen invloed op de werkelijke configuratie van IoT Edge-apparaten.

## <a name="layered-deployment"></a>Gelaagde implementatie

Gelaagde implementaties zijn automatische implementaties die kunnen worden gecombineerd om het aantal unieke implementaties te verminderen dat moet worden gemaakt. Gelaagde implementaties zijn handig in scenario's waarin dezelfde modules in verschillende combinaties worden hergebruikt in veel automatische implementaties.

Gelaagde implementaties hebben dezelfde basiscomponenten als elke automatische implementatie. Ze richten zich op apparaten op basis van tags in de apparaattweeling en bieden dezelfde functionaliteit rond labels, statistieken en statusrapportage. Gelaagde implementaties hebben ook prioriteiten toegewezen aan hen, maar in plaats van de prioriteit te gebruiken om te bepalen welke implementatie wordt toegepast op een apparaat, bepaalt de prioriteit hoe meerdere implementaties op een apparaat worden gerangschikt. Als bijvoorbeeld twee gelaagde implementaties een module of een route met dezelfde naam hebben, wordt de gelaagde implementatie met de hogere prioriteit toegepast terwijl de lagere prioriteit wordt overschreven.

De runtimemodules van het systeem, edgeAgent en edgeHub, zijn niet geconfigureerd als onderdeel van een gelaagde implementatie. Elk IoT Edge-apparaat dat is gericht op een gelaagde implementatie, heeft eerst een standaard automatische implementatie nodig. De automatische implementatie biedt de basis waarop gelaagde implementaties kunnen worden toegevoegd.

Een IoT Edge-apparaat kan één en slechts één standaard automatische implementatie toepassen, maar het kan meerdere gelaagde automatische implementaties toepassen. Alle gelaagde implementaties die op een apparaat zijn gericht, moeten een hogere prioriteit hebben dan de automatische implementatie voor dat apparaat.

Denk bijvoorbeeld aan het volgende scenario van een bedrijf dat gebouwen beheert. Ze ontwikkelden IoT Edge-modules voor het verzamelen van gegevens van beveiligingscamera's, bewegingssensoren en liften. Echter, niet al hun gebouwen kunnen gebruik maken van alle drie de modules. Met standaard automatische implementaties moet het bedrijf afzonderlijke implementaties maken voor alle modulecombinaties die hun gebouwen nodig hebben.

![Standaard automatische implementaties moeten geschikt zijn voor elke modulecombinatie](./media/module-deployment-monitoring/standard-deployment.png)

Zodra het bedrijf echter overschakelt naar gelaagde automatische implementaties, vinden ze dat ze dezelfde modulecombinaties kunnen maken voor hun gebouwen met minder implementaties om te beheren. Elke module heeft zijn eigen gelaagde implementatie en de apparaattags bepalen welke modules aan elk gebouw worden toegevoegd.

![Gelaagde automatische implementaties vereenvoudigen scenario's waarbij dezelfde modules op verschillende manieren worden gecombineerd](./media/module-deployment-monitoring/layered-deployment.png)

### <a name="module-twin-configuration"></a>Dubbele moduleconfiguratie

Wanneer u met gelaagde implementaties werkt, u, al dan niet opzettelijk, twee implementaties hebben met dezelfde module die op een apparaat is gericht. In die gevallen u beslissen of de implementatie met een hogere prioriteit de moduletweeling moet overschrijven of eraan moet toevoegen. U bijvoorbeeld een implementatie hebben die dezelfde module toepast op 100 verschillende apparaten. Echter, 10 van deze apparaten zijn in beveiligde faciliteiten en hebben extra configuratie nodig om te communiceren via proxy servers. U een gelaagde implementatie gebruiken om dubbele eigenschappen van modules toe te voegen waarmee deze 10 apparaten veilig kunnen communiceren zonder de bestaande moduledubbele informatie van de basisimplementatie te overschrijven.

U de gewenste eigenschappen van de module toevoegen in het implementatiemanifest. Waar u in een standaardimplementatie eigenschappen zou toevoegen in het gewenste gedeelte **properties.desired** van de moduletweeling, u in een gelaagde implementatie een nieuwe subset van gewenste eigenschappen declareren.

Bij een standaardimplementatie u bijvoorbeeld de gesimuleerde temperatuursensormodule toevoegen met de volgende gewenste eigenschappen die ervoor zorgen dat deze gegevens in intervallen van 5 seconden moet verzenden:

```json
"SimulatedTemperatureSensor": {
  "properties.desired": {
    "SendData": true,
    "SendInterval": 5
  }
}
```

In een gelaagde implementatie die zich richt op sommige of alle dezelfde apparaten, u een eigenschap toevoegen die de gesimuleerde sensor vertelt om 1000 berichten te verzenden en vervolgens te stoppen. U wilt de bestaande eigenschappen niet overschrijven, dus maakt u een `layeredProperties`nieuwe sectie binnen de gewenste eigenschappen die de nieuwe eigenschap worden genoemd:

```json
"SimulatedTemperatureSensor": {
  "properties.desired.layeredProperties": {
    "StopAfterCount": 1000
  }
}
```

Een apparaat dat beide implementaties heeft toegepast, geeft de volgende eigenschappen weer in de moduletwin voor de gesimuleerde temperatuursensor:

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

Als u het `properties.desired` veld van de moduletwee in een gelaagde implementatie instelt, worden de gewenste eigenschappen voor die module overschreven in implementaties met een lagere prioriteit.

## <a name="phased-rollout"></a>Gefaseerde uitrol

Een gefaseerde implementatie is een algemeen proces waarbij een operator wijzigingen implementeert in een bredere set IoT Edge-apparaten. Het doel is om geleidelijk wijzigingen aan te brengen om het risico op grootschalige veranderingen te verminderen. Automatische implementaties helpen bij het beheren van gefaseerde implementaties in een vloot van IoT Edge-apparaten.

Een gefaseerde implementatie wordt uitgevoerd in de volgende fasen en stappen:

1. Stel een testomgeving van IoT Edge-apparaten in door `tag.environment='test'`ze in te richten en een apparaattwintag in te stellen zoals .De testomgeving moet een afspiegeling zijn van de productieomgeving die de implementatie uiteindelijk zal targeten.
2. Maak een implementatie inclusief de gewenste modules en configuraties. De targetingvoorwaarde moet gericht zijn op de omgeving van het IoT Edge-apparaat van de test.
3. Valideer de nieuwe moduleconfiguratie in de testomgeving.
4. Werk de implementatie bij om een subset van iot-edge-apparaten voor productie op te nemen door een nieuwe tag toe te voegen aan de targetingvoorwaarde. Zorg er ook voor dat de prioriteit voor de implementatie hoger is dan andere implementaties die momenteel op deze apparaten zijn gericht
5. Controleer of de implementatie is geslaagd op de beoogde IoT-apparaten door de implementatiestatus te bekijken.
6. Werk de implementatie bij om alle resterende IoT Edge-apparaten voor productie te targeten.

## <a name="rollback"></a>Terugdraaien

Implementaties kunnen worden teruggedraaid als u fouten of verkeerde configuraties ontvangt.Omdat een implementatie de absolute moduleconfiguratie voor een IoT Edge-apparaat definieert, moet een extra implementatie ook worden gericht op hetzelfde apparaat met een lagere prioriteit, zelfs als het doel is om alle modules te verwijderen.  

Als u een implementatie verwijdert, worden de modules niet verwijderd van gerichte apparaten. Er moet een andere implementatie zijn die een nieuwe configuratie voor de apparaten definieert, zelfs als het een lege implementatie is.

Rollbacks uitvoeren in de volgende reeks:

1. Controleer of een tweede implementatie ook is gericht op dezelfde apparaatset. Als het doel van de terugdraaiing is om alle modules te verwijderen, mag de tweede implementatie geen modules bevatten.
2. Wijzig of verwijder de doelvoorwaardeexpressie van de implementatie die u wilt terugdraaien, zodat de apparaten niet meer voldoen aan de targetingvoorwaarde.
3. Controleer of de terugdraaiing is gelukt door de implementatiestatus weer te geven.
   * De opgerolde implementatie mag de status niet meer weergeven voor de apparaten die zijn teruggedraaid.
   * De tweede implementatie moet nu de implementatiestatus bevatten voor de apparaten die zijn teruggedraaid.

## <a name="next-steps"></a>Volgende stappen

* Loop door de stappen om een implementatie te maken, bij te werken of te verwijderen in [IoT Edge-modules implementeren en bewaken op schaal.](how-to-deploy-monitor.md)
* Meer informatie over andere IoT Edge-concepten, zoals de [IoT Edge-runtime-](iot-edge-runtime.md) en [IoT Edge-modules.](iot-edge-modules.md)
