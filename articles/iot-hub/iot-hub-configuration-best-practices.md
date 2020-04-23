---
title: Aanbevolen procedures voor apparaatconfiguratie voor Azure IoT Hub | Microsoft Documenten
description: Meer informatie over aanbevolen procedures voor het gebruik van automatisch apparaatbeheer om repetitieve en complexe taken die betrokken zijn bij het beheren van IoT-apparaten op schaal te minimaliseren.
author: robinsh
ms.author: robinsh
ms.date: 06/28/2019
ms.topic: conceptual
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: 67f0d9eb1fdac603ee82d568644e8ad8550d1c80
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024775"
---
# <a name="best-practices-for-device-configuration-within-an-iot-solution"></a>Aanbevolen procedures voor apparaatconfiguratie binnen een IoT-oplossing

Automatisch apparaatbeheer in Azure IoT Hub automatiseert veel repetitieve en complexe taken van het beheer van grote apparaatvloten gedurende het gehele gehele levenscyclus. In dit artikel worden veel van de best practices gedefinieerd voor de verschillende rollen die betrokken zijn bij het ontwikkelen en bedienen van een IoT-oplossing.

* **IoT-hardwarefabrikant/-integrator:** Fabrikanten van IoT-hardware, integrators die hardware assembleren van verschillende fabrikanten of leveranciers die hardware leveren voor een IoT-implementatie die door andere leveranciers wordt vervaardigd of geïntegreerd. Betrokken bij de ontwikkeling en integratie van firmware, embedded besturingssystemen en embedded software.

* **IoT-oplossingsontwikkelaar:** De ontwikkeling van een IoT-oplossing wordt meestal gedaan door een ontwikkelaar van oplossingen. Deze ontwikkelaar kan deel uitmaken van een eigen team of een systeemintegrator die gespecialiseerd is in deze activiteit. De IoT-oplossingsontwikkelaar kan verschillende componenten van de IoT-oplossing vanaf nul ontwikkelen, verschillende standaard- of opensourcecomponenten integreren of een [IoT-oplossingsversneller](/azure/iot-accelerators/)aanpassen.

* **Operator van ioT-oplossingen:** Nadat de IoT-oplossing is geïmplementeerd, zijn langdurige bewerkingen, bewaking, upgrades en onderhoud vereist. Deze taken kunnen worden uitgevoerd door een eigen team dat bestaat uit informatietechnologiespecialisten, hardware-activiteiten en onderhoudsteams en domeinspecialisten die het juiste gedrag van de totale IoT-infrastructuur monitoren.

## <a name="understand-automatic-device-management-for-configuring-iot-devices-at-scale"></a>Inzicht in automatisch apparaatbeheer voor het configureren van IoT-apparaten op schaal

Automatisch apparaatbeheer omvat de vele voordelen van [apparaattweelingen](iot-hub-devguide-device-twins.md) en [moduletweelingen](iot-hub-devguide-module-twins.md) om gewenste en gerapporteerde toestanden tussen de cloud en apparaten te synchroniseren. [Automatische apparaatconfiguraties](iot-hub-auto-device-config.md) werken automatisch grote sets tweelingen bij en vatten de voortgang en naleving samen. In de volgende stappen op hoog niveau wordt beschreven hoe automatisch apparaatbeheer wordt ontwikkeld en gebruikt:

* De **IoT-hardwarefabrikant/integrator** implementeert apparaatbeheerfuncties in een ingesloten toepassing met behulp van [apparaattweelingen.](iot-hub-devguide-device-twins.md) Deze functies kunnen firmware-updates, software-installatie en -update en instellingenbeheer omvatten.

* De **IoT-oplossingsontwikkelaar** implementeert de beheerlaag van apparaatbeheerbewerkingen met behulp van [apparaattweelingen](iot-hub-devguide-device-twins.md) en [automatische apparaatconfiguraties.](iot-hub-auto-device-config.md) De oplossing moet het definiëren van een operatorinterface omvatten om apparaatbeheertaken uit te voeren.

* De **IoT-oplossingsoperator** gebruikt de IoT-oplossing om apparaatbeheertaken uit te voeren, met name om apparaten samen te groeperen, configuratiewijzigingen zoals firmware-updates te initiëren, voortgang te controleren en problemen op te lossen die zich voordoen.

## <a name="iot-hardware-manufacturerintegrator"></a>IoT-hardwarefabrikant/integrator

De volgende zijn best practices voor hardwarefabrikanten en integrators die zich bezighouden met embedded software ontwikkeling:

* **Implementeren [apparaat tweelingen:](iot-hub-devguide-device-twins.md)** Apparaattweelingen maken het synchroniseren van de gewenste configuratie vanuit de cloud mogelijk en voor het rapporteren van huidige configuratie- en apparaateigenschappen. De beste manier om apparaattweelingen te implementeren in embedded toepassingen is via de [Azure IoT SDKs.](https://github.com/Azure/azure-iot-sdks) Apparaattweelingen zijn het meest geschikt voor configuratie omdat ze:

    * Ondersteuning voor bidirectionele communicatie.
    * Sta zowel verbonden als losgekoppelde apparaatstatussen toe.
    * Volg het principe van uiteindelijke consistentie.
    * Zijn volledig queriable in de cloud.

* **Structureer de apparaattweeling voor apparaatbeheer:** De apparaattweeling moet zodanig zijn gestructureerd dat apparaatbeheereigenschappen logisch zijn gegroepeerd in secties. Hierdoor kunnen configuratiewijzigingen worden geïsoleerd zonder dat dit gevolgen heeft voor andere delen van de tweeling. Maak bijvoorbeeld een sectie binnen de gewenste eigenschappen voor firmware, een andere sectie voor software en een derde sectie voor netwerkinstellingen. 

* **Apparaatkenmerken rapporteren die nuttig zijn voor apparaatbeheer:** Kenmerken zoals het merk en model van fysieke apparaten, firmware, besturingssysteem, serienummer en andere id's zijn handig voor rapportage en als parameters voor het targeten van configuratiewijzigingen.

* **Definieer de belangrijkste statussen voor rapportagestatus en voortgang:** Statussen op het hoogste niveau moeten worden opgesomd, zodat ze aan de operator kunnen worden gerapporteerd. Een firmware-update rapporteert bijvoorbeeld status als Huidig, Downloaden, Toepassen, In uitvoering en Fout. Definieer extra velden voor meer informatie over elke status.

## <a name="iot-solution-developer"></a>IoT-oplossingsontwikkelaar

Hieronder volgen aanbevolen procedures voor IoT-oplossingsontwikkelaars die systemen bouwen die zijn gebaseerd in Azure:

* **Implementeren [apparaat tweelingen:](iot-hub-devguide-device-twins.md)** Apparaattweelingen maken het synchroniseren van de gewenste configuratie vanuit de cloud mogelijk en voor het rapporteren van huidige configuratie- en apparaateigenschappen. De beste manier om device twins te implementeren in cloudoplossingen toepassingen is via de [Azure IoT SDKs.](https://github.com/Azure/azure-iot-sdks) Apparaattweelingen zijn het meest geschikt voor configuratie omdat ze:

    * Ondersteuning voor bidirectionele communicatie.
    * Sta zowel verbonden als losgekoppelde apparaatstatussen toe.
    * Volg het principe van uiteindelijke consistentie.
    * Zijn volledig queriable in de cloud.

* **Apparaten ordenen met dubbele tags van het apparaat:** De oplossing moet de operator in staat stellen om kwaliteitsringen of andere sets apparaten te definiëren op basis van verschillende implementatiestrategieën, zoals kanarie. Apparaatorganisatie kan binnen uw oplossing worden geïmplementeerd met behulp van dubbele apparaattags en [query's.](iot-hub-devguide-query-language.md) Apparaatorganisatie is nodig om configuratie-uitrol veilig en nauwkeurig mogelijk te maken.

* **Automatische [apparaatconfiguraties](iot-hub-auto-device-config.md)implementeren:** Automatische apparaatconfiguraties implementeren en bewaken configuratiewijzigingen in grote sets IoT-apparaten via apparaattweelingen.

   Automatische apparaatconfiguraties richten sets van apparaattweelingen via de **doelvoorwaarde,** wat een query is op dubbele tags van het apparaat of gerapporteerde eigenschappen. De **doelinhoud** is de set van gewenste eigenschappen die worden ingesteld binnen het beoogde apparaat tweelingen. De doelinhoud moet overeenkomen met de dubbele structuur van het apparaat die is gedefinieerd door de IoT-hardwarefabrikant/-integrator. De **statistieken** zijn query's op apparaat twee gerapporteerde eigenschappen en moet ook in overeenstemming zijn met het apparaat twin structuur gedefinieerd door de IoT hardware fabrikant / integrator.

   Automatische apparaatconfiguraties worden voor het eerst uitgevoerd kort nadat de configuratie is gemaakt en vervolgens met intervallen van vijf minuten. Ze profiteren ook van de IoT Hub die apparaattweelingbewerkingen uitvoert met een snelheid die nooit de [beperkingslimieten](iot-hub-devguide-quotas-throttling.md) voor apparaattweelingleest en -updates zal overschrijden.

* **Gebruik de [Service Apparaatinrichting:](../iot-dps/how-to-manage-enrollments.md)** Ontwikkelaars van oplossingen moeten de Apparaatinrichtingsservice gebruiken om dubbele apparaattags toe te wijzen aan nieuwe apparaten, zodat ze automatisch worden geconfigureerd door **automatische apparaatconfiguraties** die zijn gericht op tweelingen met die tag. 

## <a name="iot-solution-operator"></a>IoT-oplossingsoperator

Hieronder volgen aanbevolen procedures voor IoT-oplossingsoperatoren die een IoT-oplossing gebruiken die is gebaseerd op Azure:

* **Apparaten ordenen voor beheer:** De IoT-oplossing moet het maken van kwaliteitsringen of andere sets apparaten definiëren of mogelijk maken op basis van verschillende implementatiestrategieën, zoals kanarie. De sets apparaten worden gebruikt om configuratiewijzigingen uit te rollen en andere apparaatbeheerbewerkingen op schaal uit te voeren.

* **Configuratiewijzigingen uitvoeren met een gefaseerde uitrol:**  Een gefaseerde uitrol is een algemeen proces waarbij een operator wijzigingen implementeert in een bredere set IoT-apparaten. Het doel is om geleidelijk wijzigingen aan te brengen om het risico op grootschalige veranderingen te verminderen.De operator moet de interface van de oplossing gebruiken om een [automatische apparaatconfiguratie](iot-hub-auto-device-config.md) te maken en de targetingvoorwaarde moet zich richten op een eerste set apparaten (zoals een kanariegroep). De operator moet vervolgens de configuratiewijziging in de eerste set apparaten valideren.

   Zodra de validatie is voltooid, werkt de operator de automatische apparaatconfiguratie bij met een grotere set apparaten. De operator moet ook de prioriteit instellen dat de configuratie hoger is dan andere configuraties die momenteel op die apparaten zijn gericht. De uitrol kan worden gecontroleerd met behulp van de statistieken gerapporteerd door de automatische apparaatconfiguratie.

* **Rollbacks uitvoeren in geval van fouten of verkeerde configuraties:**  Een automatische apparaatconfiguratie die fouten of verkeerde configuraties veroorzaakt, kan worden teruggedraaid door de **targetingvoorwaarde te** wijzigen, zodat de apparaten niet langer voldoen aan de targetingvoorwaarde. Zorg ervoor dat een andere automatische apparaatconfiguratie met een lagere prioriteit nog steeds is gericht op deze apparaten. Controleer of de terugdraaiing is geslaagd door de statistieken te bekijken: de teruggerolde configuratie mag de status voor niet-gerichte apparaten niet meer weergeven en de statistieken van de tweede configuratie moeten nu tellingen bevatten voor de apparaten die nog steeds zijn getarget.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het implementeren van apparaattweelingen in [Apparaattweelingen begrijpen en gebruiken in IoT Hub](iot-hub-devguide-device-twins.md).

* Loop door de stappen om een automatische apparaatconfiguratie te maken, bij te werken of te verwijderen in [IoT-apparaten op schaal configureren en bewaken.](iot-hub-auto-device-config.md)

* Implementeer een firmware-updatepatroon met apparaattweelingen en automatische apparaatconfiguraties in [Zelfstudie: Implementeer een proces voor het bijwerken van de apparaatfirmware.](tutorial-firmware-update.md)
