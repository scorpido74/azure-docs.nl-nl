---
title: bestand opnemen
description: bestand opnemen
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 08/07/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 4fdb891d668d99644d8a9ed9c15d158e65d53ba5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72793139"
---
Het beveiligen van een IoT-infrastructuur (Internet of Things) vereist een rigoureuze beveiligingsstrategie. Deze strategie vereist dat u gegevens in de cloud beveiligt, de gegevensintegriteit beschermt terwijl u via het openbare internet bent, en apparaten veilig inrichten. Elke laag zorgt voor meer zekerheid in de totale infrastructuur.

## <a name="secure-an-iot-infrastructure"></a>Een IoT-infrastructuur beveiligen

Deze security-in-depth strategie kan worden ontwikkeld en uitgevoerd met actieve deelname van verschillende spelers die betrokken zijn bij de productie, ontwikkeling en implementatie van IoT-apparaten en infrastructuur. Hieronder volgt een beschrijving op hoog niveau van deze spelers.

* **IoT-hardwarefabrikant/-integrator**: Doorgaans zijn deze spelers de fabrikanten van IoT-hardware die worden geïmplementeerd, integrators die hardware assembleren van verschillende fabrikanten of leveranciers die hardware leveren voor een IoT-implementatie die door andere leveranciers wordt vervaardigd of geïntegreerd.

* **IoT-oplossingsontwikkelaar**: De ontwikkeling van een IoT-oplossing wordt meestal gedaan door een ontwikkelaar van oplossingen. Deze ontwikkelaar kan deel uitmaken van een eigen team of een system integrator (SI) gespecialiseerd in deze activiteit. De IoT-oplossingsontwikkelaar kan verschillende componenten van de IoT-oplossing vanaf nul ontwikkelen, verschillende kant-en-klare of open-source componenten integreren of oplossingsversnellers gebruiken met kleine aanpassingen.

* **IoT-oplossingsimplementeerer**: Nadat een IoT-oplossing is ontwikkeld, moet deze in het veld worden geïmplementeerd. Dit proces omvat de implementatie van hardware, interconnectie van apparaten en implementatie van oplossingen in hardwareapparaten of in de cloud.

* **IoT-oplossingsoperator**: Nadat de IoT-oplossing is geïmplementeerd, vereist het langdurige bewerkingen, bewaking, upgrades en onderhoud. Deze taken kunnen worden uitgevoerd door een eigen team dat bestaat uit informatietechnologiespecialisten, hardware-activiteiten en onderhoudsteams en domeinspecialisten die het juiste gedrag van de totale IoT-infrastructuur monitoren.

De volgende secties bieden best practices voor elk van deze spelers om te helpen bij het ontwikkelen, implementeren en beheren van een veilige IoT-infrastructuur.

## <a name="iot-hardware-manufacturerintegrator"></a>IoT-hardwarefabrikant/integrator

Hieronder volgen de aanbevolen procedures voor IoT-hardwarefabrikanten en hardwareintegratoren.

* **Scope hardware aan minimale eisen:** Het hardwareontwerp moet de minimale functies bevatten die nodig zijn voor de werking van de hardware, en niets meer. Een voorbeeld is om USB-poorten alleen op te nemen als dat nodig is voor de werking van het apparaat. Deze extra functies openen het apparaat voor ongewenste aanvalsvectoren die moeten worden vermeden.

* **Maak hardwaresabotagebestendig:** inbouwmechanismen om fysieke manipulatie te detecteren, zoals het openen van de apparaathoes of het verwijderen van een deel van het apparaat. Deze sabotagesignalen kunnen deel uitmaken van de gegevensstroom die naar de cloud is geüpload, waardoor operators op deze gebeurtenissen kunnen worden gewaarschuwd.

* **Bouw rond veilige hardware:** Als COGS dit toestaat, bouw je beveiligingsfuncties zoals veilige en versleutelde opslag of opstartfunctionaliteit op basis van Trusted Platform Module (TPM). Deze functies maken apparaten veiliger en helpen de algehele IoT-infrastructuur te beschermen.

* **Maak upgrades veilig:** Firmware-upgrades tijdens de levensduur van het apparaat zijn onvermijdelijk. Door apparaten te bouwen met veilige paden voor upgrades en cryptografische beveiliging van firmwareversies, kan het apparaat veilig zijn tijdens en na upgrades.

## <a name="iot-solution-developer"></a>IoT-oplossingsontwikkelaar

De volgende zijn de aanbevolen procedures voor iot-oplossingsontwikkelaars:

* **Volg veilige software ontwikkelingsmethodologie**: Ontwikkeling van veilige software vereist ground-up denken over beveiliging, vanaf het begin van het project helemaal tot de implementatie, testen en implementatie. De keuzes van platforms, talen en tools worden allemaal beïnvloed met deze methodologie. De Microsoft Security Development Lifecycle biedt een stapsgewijze aanpak voor het bouwen van veilige software.

* **Kies met zorg open-source software**: Open-source software biedt de mogelijkheid om snel oplossingen te ontwikkelen. Wanneer u open-sourcesoftware kiest, moet u rekening houden met het activiteitsniveau van de community voor elke open-sourcecomponent. Een actieve community zorgt ervoor dat software wordt ondersteund en dat problemen worden ontdekt en aangepakt. Als alternatief wordt een duister en inactief open-source softwareproject mogelijk niet ondersteund en worden problemen waarschijnlijk niet ontdekt.

* **Integreren met zorg**: Veel software beveiligingsfouten bestaan op de grens van bibliotheken en API's. Functionaliteit die mogelijk niet vereist is voor de huidige implementatie, is mogelijk nog steeds beschikbaar via een API-laag. Om de algehele veiligheid te garanderen, moet u alle interfaces controleren van onderdelen die zijn geïntegreerd op beveiligingsfouten.

## <a name="iot-solution-deployer"></a>IoT-oplossingsimplementatie

Hieronder volgen best practices voor IoT-oplossingsimplementaties:

* **Hardware veilig implementeren:** voor IoT-implementaties moet hardware mogelijk worden geïmplementeerd op onveilige locaties, zoals in openbare ruimten of onbewaakte locaties. Zorg er in dergelijke situaties voor dat de implementatie van hardware maximaal sabotagebestendig is. Als USB of andere poorten beschikbaar zijn op de hardware, zorg er dan voor dat ze veilig worden afgedekt. Veel aanvalsvectoren kunnen deze gebruiken als toegangspunten.

* **Verificatiesleutels veilig houden:** tijdens de implementatie vereist elk apparaat-id en bijbehorende verificatiesleutels die door de cloudservice worden gegenereerd. Houd deze sleutels fysiek veilig, zelfs na de implementatie. Elke gecompromitteerde sleutel kan worden gebruikt door een kwaadaardig apparaat om zich voor te doen als een bestaand apparaat.

## <a name="iot-solution-operator"></a>IoT-oplossingsoperator

De volgende zijn de best practices voor IoT-oplossingsoperatoren:

* **Houd het systeem up-to-date:** zorg ervoor dat besturingssystemen voor apparaten en alle apparaatstuurprogramma's worden geüpgraded naar de nieuwste versies. Als u automatische updates inSchakelt in Windows 10 (IoT of andere SKU's), houdt Microsoft deze up-to-date en biedt het een veilig besturingssysteem voor IoT-apparaten. Het up-to-date houden van andere besturingssystemen (zoals Linux) zorgt ervoor dat ze ook beschermd zijn tegen kwaadaardige aanvallen.

* **Bescherm tegen schadelijke activiteiten:** Als het besturingssysteem het toelaat, installeert u de nieuwste antivirus- en antimalwaremogelijkheden op elk besturingssysteem. Deze praktijk kan helpen de meeste externe bedreigingen te beperken. U de meeste moderne besturingssystemen beschermen tegen bedreigingen door passende stappen te ondernemen.

* **Controleer regelmatig:** Het controleren van IoT-infrastructuur voor beveiligingsgerelateerde problemen is essentieel bij het reageren op beveiligingsincidenten. De meeste besturingssystemen bieden ingebouwde logboekregistratie voor gebeurtenissen die regelmatig moeten worden gecontroleerd om ervoor te zorgen dat er geen inbreuk op de beveiliging is opgetreden. Controle-informatie kan worden verzonden als een aparte telemetriestroom naar de cloudservice waar deze kan worden geanalyseerd.

* **De IoT-infrastructuur fysiek beschermen**: De ergste beveiligingsaanvallen tegen IoT-infrastructuur worden gestart met behulp van fysieke toegang tot apparaten. Een belangrijke veiligheidspraktijk is om te beschermen tegen kwaadwillig gebruik van USB-poorten en andere fysieke toegang. Een sleutel tot het blootleggen van inbreuken die zich kunnen hebben voorgedaan, is het loggen van fysieke toegang, zoals het gebruik van usb-poorten. Nogmaals, Windows 10 (IoT en andere SKU's) maakt gedetailleerde logboekregistratie van deze gebeurtenissen mogelijk.

* **Cloudreferenties beveiligen:** Cloudauthenticatiereferenties die worden gebruikt voor het configureren en bedienen van een IoT-implementatie zijn mogelijk de eenvoudigste manier om toegang te krijgen en een IoT-systeem te compromitteren. Bescherm de referenties door het wachtwoord regelmatig te wijzigen en gebruik deze referenties niet op openbare machines.

De mogelijkheden van verschillende IoT-apparaten variëren. Sommige apparaten kunnen computers zijn met algemene desktopbesturingssystemen en sommige apparaten draaien mogelijk met zeer lichte besturingssystemen. De eerder beschreven beveiligingsaanbevolen procedures kunnen in verschillende mate op deze apparaten van toepassing zijn. Indien voorzien, moeten aanvullende beveiligings- en implementatieprocedures van de fabrikanten van deze apparaten worden gevolgd.

Sommige oudere en beperkte apparaten zijn mogelijk niet specifiek ontworpen voor IoT-implementatie. Deze apparaten hebben mogelijk niet de mogelijkheid om gegevens te versleutelen, verbinding te maken met internet of geavanceerde controle te bieden. In deze gevallen kan een moderne en beveiligde veldgateway gegevens van oudere apparaten samenvoegen en de beveiliging bieden die nodig is voor het verbinden van deze apparaten via internet. Veldgateways kunnen veilige verificatie, onderhandeling over versleutelde sessies, ontvangst van opdrachten uit de cloud en vele andere beveiligingsfuncties bieden.