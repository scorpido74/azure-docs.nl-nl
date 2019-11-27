---
title: Automatische implementatie voor apparaatgroepen - Azure IoT Edge | Microsoft Docs
description: Gebruik van automatische implementaties in Azure IoT Edge voor groepen van apparaten op basis van gedeelde tags beheren
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 09/27/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: eb45f2b929c08ce77c83af450726a00dd6af458e
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456725"
---
# <a name="understand-iot-edge-automatic-deployments-for-single-devices-or-at-scale"></a>Informatie over IoT Edge-automatische implementaties voor individuele apparaten of op schaal

Azure IoT Edge apparaten volgen een [levens cyclus](../iot-hub/iot-hub-device-management-overview.md) van een apparaat die vergelijkbaar is met andere typen IOT-apparaten:

1. Richt nieuwe IoT Edge-apparaten in door een apparaat te Imaging met een besturings systeem en de [IOT Edge-runtime](iot-edge-runtime.md)te installeren.
2. Configureer de apparaten om [IOT Edge modules](iot-edge-modules.md)uit te voeren en controleer hun status. 
3. Ten slotte apparaten buiten gebruik stellen wanneer ze worden vervangen of verouderd raken.  

Azure IoT Edge biedt twee manieren om de modules te configureren voor uitvoering op IoT Edge apparaten: een voor ontwikkeling en snelle herhalingen op één apparaat (u hebt deze methode gebruikt in de Azure IoT Edge [zelf studies](tutorial-deploy-function.md)) en één voor het beheren van grote vloots van IOT edge apparaten. Beide van deze methoden zijn beschikbaar in Azure portal en programmatisch. Voor doel groepen of een groot aantal apparaten kunt u opgeven welke apparaten u wilt implementeren met behulp van [labels](../iot-edge/how-to-deploy-monitor.md#identify-devices-using-tags) in het apparaat. De volgende stappen uit praten over een implementatie op een apparaatgroep de staat Washington is geïdentificeerd door de eigenschap tags. 

In dit artikel is gericht op de configuratie en bewaking van de fasen voor vloten van apparaten, gezamenlijk aangeduid als automatische IoT Edge-implementaties. De algemene implementaties tappen zijn als volgt: 

1. Een operator definieert een implementatie die een reeks modules, evenals de doelapparaten beschrijft. Elke implementatie heeft een implementatie manifest dat deze informatie weergeeft. 
2. De IoT Hub-service communiceert met alle apparaten uit de doelgroep om deze te configureren met de gewenste modules. 
3. De IoT Hub-service wordt de status opgehaald van de IoT Edge-apparaten en maakt ze beschikbaar voor de operator.  Een operator kan bijvoorbeeld zien wanneer een edge-apparaat niet is geconfigureerd of als een module tijdens runtime mislukt. 
4. Op elk gewenst moment zijn nieuwe IoT Edge-apparaten die voldoen aan de doelitems voorwaarden geconfigureerd voor de implementatie. Zo configureert een implementatie die gericht is op alle IoT Edge apparaten in de status Washington automatisch een nieuw IoT Edge-apparaat nadat het is ingericht en toegevoegd aan de apparaatgroep status van Washington. 
 
Dit artikel wordt beschreven voor elk onderdeel is betrokken bij het configureren en controleren van een implementatie. Zie [IOT Edge modules op schaal implementeren en bewaken](how-to-deploy-monitor.md)voor een overzicht van het maken en bijwerken van een implementatie.

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

Bijvoorbeeld, hebt u een A-implementatie met een doel voorwaarde tags.environment = 'prod'. Wanneer u een vliegende start de implementatie, zijn er 10 productieapparaten. De modules zijn geïnstalleerd in deze 10-apparaten. De Status van de IoT Edge-Agent wordt weergegeven als het totaal aantal apparaten 10, 10 gelukt-antwoorden, 0 mislukte reacties en 0 in behandeling-antwoorden. Nu u bij het toevoegen van vijf meer apparaten met tags.environment = 'prod'. De service detecteert de wijziging en de Status van de IoT Edge-Agent wordt 15 totaal aantal apparaten, 10 gelukt-antwoorden, 0 mislukte reacties en 5 in behandeling-antwoorden als er wordt geprobeerd om in de vijf nieuwe apparaten te implementeren.

Gebruik een Boole-voorwaarde op device twins tags of deviceId de doelapparaten selecteren. Als u voor waarde met tags wilt gebruiken, moet u ' Tags ' toevoegen:{} sectie in het apparaat dubbele onder hetzelfde niveau als eigenschappen. [Meer informatie over tags op het apparaat, dubbele](../iot-hub/iot-hub-devguide-device-twins.md)

Voorbeelden van de doel-voorwaarden:

* apparaat-id = 'linuxprod1'
* tags.Environment = 'prod'
* tags.Environment = 'prod' AND tags.location = 'westus'
* tags.Environment = 'prod' of tags.location = 'westus'
* tags.operator = 'John' en tags.environment = 'prod' geen apparaat-id = 'linuxprod1'

Hier volgen enkele beperkingen wanneer u een doelvoorwaarde maken:

* In de apparaatdubbel, kunt u alleen een doelvoorwaarde met behulp van labels of deviceId bouwen.
* Dubbele aanhalingstekens zijn niet toegestaan in een gedeelte van de doelvoorwaarde. Gebruikt u enkele aanhalingstekens.
* Enkele aanhalingstekens vertegenwoordigen de waarden van de doelvoorwaarde. Daarom moet u de enkel aanhalingsteken met een andere enkel aanhalingsteken escape-als het deel van de naam van het apparaat uitmaakt. Als u bijvoorbeeld een apparaat met de naam `operator'sDevice`wilt richten, schrijft u `deviceId='operator''sDevice'`.
* Cijfers, letters en de volgende tekens zijn toegestaan in doel voorwaarde waarden: `-:.+%_#*?!(),=@;$`.

### <a name="priority"></a>Prioriteit

Een prioriteit bepaalt of een implementatie moet worden toegepast op een doelapparaat ten opzichte van andere implementaties. De prioriteit van een implementatie is een positief geheel getal, met grotere aantallen die aangeeft hogere prioriteit. Als een IoT Edge-apparaat door meer dan één implementatie is gericht, wordt de implementatie met de hoogste prioriteit is van toepassing.  Implementaties met lagere prioriteiten worden niet toegepast en worden niet samengevoegd.  Als een apparaat is gericht op twee of meer implementaties met een gelijke prioriteit, is de meest recent gemaakte implementatie (bepaald door de tijds tempel voor maken) van toepassing.

### <a name="labels"></a>Labels 

Labels zijn een reeks sleutel/waarde-paren die u om te filteren en groeperen van implementaties gebruiken kunt. Een implementatie kan meerdere labels hebben. Labels zijn optioneel en niet van invloed op de werkelijke configuratie van IoT Edge-apparaten uitvoeren. 

### <a name="deployment-status"></a>Implementatiestatus

Een implementatie kan worden gecontroleerd om te bepalen of deze is toegepast voor alle betreffende IoT Edge-apparaat.  Een doel apparaat wordt weer gegeven in een of meer van de volgende status Categorieën: 

* **Doel** toont de IOT edge-apparaten die overeenkomen met de voor waarde voor de implementatie van doelen.
* Met **werkelijk** worden de beoogde IOT edge-apparaten weer gegeven die niet zijn gericht op een andere implementatie van een hogere prioriteit.
* **In orde** worden de IOT edge-apparaten weer gegeven die zijn gerapporteerd aan de service dat de modules zijn geïmplementeerd. 
* Een **slechte status** geeft aan dat de IOT edge apparaten zijn gerapporteerd aan de service dat er niet met succes een of meerdere modules zijn geïmplementeerd. Voor verder onderzoek van de fout, extern verbinding maken met deze apparaten en de logboekbestanden.
* **Onbekend** toont de IOT edge apparaten die geen status hebben gerapporteerd voor deze implementatie. Verder te onderzoeken, service-gegevens en logboekbestanden bestanden weergeven

## <a name="phased-rollout"></a>Gefaseerde implementatie 

Een gefaseerde implementatie is een algemene proces waarbij wijzigingen in een operator worden geïmplementeerd op een leven set IoT Edge-apparaten. Het doel is om wijzigingen aanbrengen geleidelijk in vermindert het risico van het maken van grote schaal belangrijke wijzigingen.  

Een gefaseerde implementatie wordt uitgevoerd in de volgende fasen en stappen: 

1. Stel een test omgeving van IoT Edge apparaten in door deze in te richten en een apparaat-dubbele tag in te stellen, zoals `tag.environment='test'`. In de test omgeving moet de productie omgeving worden gespiegeld die de implementatie uiteindelijk gaat richten. 
2. Maak een implementatie met inbegrip van de gewenste modules en configuraties. De doelitems voorwaarde moet het doel de test-omgeving van IoT Edge-apparaten.   
3. Valideer de moduleconfiguratie van de nieuwe in de testomgeving.
4. De implementatie zodat een subset van de productie IoT Edge-apparaten die door een nieuwe tag toe te voegen aan de voorwaarde doelitems bijwerken. Zorg er ook voor dat de prioriteit voor de implementatie hoger dan andere implementaties op dit moment is gericht op deze apparaten is 
5. Controleer of dat de implementatie is voltooid op de betreffende IoT-apparaten door de implementatiestatus te bekijken.
6. Bijwerken van de implementatie wilt richten op alle resterende productie IoT Edge-apparaten.

## <a name="rollback"></a>Terugdraaien

Implementaties kunnen worden teruggedraaid terug als er fouten of onjuiste configuraties.  Omdat een implementatie de absolute module configuratie voor een IoT Edge apparaat definieert, moet een extra implementatie ook met een lagere prioriteit op hetzelfde apparaat worden gericht, zelfs als het doel is om alle modules te verwijderen.  

Terugdraaiacties in de volgende handelingen uitvoeren: 

1. Bevestig dat een tweede implementatie ook is gericht op dezelfde apparaatset. Als het doel van het terugdraaien is het verwijderen van alle modules, moet alle modules die niet in de tweede implementatie bevatten. 
2. Wijzigen of verwijderen van de doel-voorwaarde-expressie van de implementatie die u wilt opnieuw zodat de apparaten niet langer voldoet aan de doelitems voorwaarde samenvouwen.
3. Controleren of het terugdraaien is geslaagd door de implementatiestatus te bekijken.
   * De service-Update-back-implementatie moet status voor de apparaten die worden teruggedraaid niet meer weergeven.
   * De tweede implementatie bevatten moet nu de implementatiestatus voor de apparaten die worden teruggedraaid.


## <a name="next-steps"></a>Volgende stappen

* Door loop de stappen om een implementatie te maken, bij te werken of te verwijderen in [IOT Edge modules implementeren en bewaken op schaal](how-to-deploy-monitor.md).
* Meer informatie over andere IoT Edge concepten, zoals de modules [IOT Edge runtime](iot-edge-runtime.md) en [IOT Edge](iot-edge-modules.md).

