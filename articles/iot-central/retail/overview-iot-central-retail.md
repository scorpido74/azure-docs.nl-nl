---
title: Retailoplossingen bouwen met Azure IoT Central | Microsoft Documenten
description: Meer informatie over het gebruik van Azure IoT Central-toepassingssjablonen voor het bouwen van verbonden logistiek, digitaal distributiecentrum, in-store-analyses, conditiebewaking, kassa, slim voorraadbeheer en retailoplossingen.
author: avneet723
ms.author: avneets
ms.date: 01/10/2020
ms.topic: overview
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
ms.openlocfilehash: 9427b007bc047e2f01db2dad02e06bf0ab0f5dea
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "81000614"
---
# <a name="building-retail-solutions-with-azure-iot-central"></a>Oplossingen voor de detailhandel bouwen met Azure IoT Central



Azure IoT Central is een IoT-appplatform dat de lasten en kosten vermindert die gepaard gaan met het ontwikkelen, beheren en onderhouden van Enterprise-grade IoT-oplossingen. Als u ervoor kiest om te bouwen met Azure IoT Central, u uw tijd, geld en energie richten op het transformeren van uw bedrijf met IoT-gegevens, in plaats van alleen een complexe en voortdurend evoluerende IoT-infrastructuur te onderhouden en bij te werken.

In dit artikel worden verschillende retailspecifieke IoT Central-toepassingssjablonen beschreven. Als oplossingsbouwer u deze sjablonen gebruiken om IoT-oplossingen te bouwen die supply chains optimaliseren, in-store-ervaringen voor klanten verbeteren en voorraad efficiënter bijhouden.

> [!div class="mx-imgBorder"]
> ![Overzicht van Azure IoT Retail](./media/overview-iot-central-retail/retail-app-templates.png)

In de volgende secties worden de mogelijkheden van deze toepassingssjablonen beschreven:

## <a name="connected-logistics"></a>Verbonden logistiek

De wereldwijde logistieke uitgaven zullen naar verwachting $ 10,6 biljoen bereiken in 2020. Het vervoer van goederen is goed voor het grootste deel van deze uitgaven en verschepen staan onder grote concurrentiedruk en beperkingen.

U IoT-sensoren gebruiken om omgevingscondities te verzamelen en te controleren, zoals temperatuur, vochtigheid, kantelen, schokken, licht en de locatie van een zending. U telemetrie verzameld van IoT-sensoren en apparaten combineren met andere gegevensbronnen, zoals weers- en verkeersinformatie in cloudgebaseerde business intelligence-systemen.

De voordelen van een verbonden logistieke oplossing zijn:

* Transportmonitoring met real-time tracking en tracking. 
* Transport integriteit met real-time ambient condition monitoring.
* Beveiliging van diefstal, verlies of schade van zendingen.
* Geo-fencing, routeoptimalisatie, fleetmanagement en voertuiganalyse.
* Prognoses voor voorspelbaar vertrek en aankomst van zendingen.

De volgende schermafbeeldingen tonen het out-of-the-box dashboard in de toepassingssjabloon. Het dashboard is volledig aanpasbaar om te voldoen aan uw specifieke oplossingsvereisten:

> [!div class="mx-imgBorder"]
> ![Verbonden logistiek dashboard](./media/overview-iot-central-retail/connected-logistics-dashboard1.png)

> [!div class="mx-imgBorder"]
> ![Verbonden logistiek dashboard](./media/overview-iot-central-retail/connected-logistics-dashboard2.png)

Zie voor meer informatie de [zelfstudie implementeren en doorlopen door een sjabloonvoor verbonden logistieke toepassingen.](./tutorial-iot-central-connected-logistics.md)

## <a name="digital-distribution-center"></a>Digitaal distributiecentrum

Naarmate fabrikanten en retailers wereldwijd aanwezig zijn, vertakken hun toeleveringsketens en worden ze complexer. Consumenten verwachten nu dat er grote selecties van producten beschikbaar zijn, en dat deze goederen binnen een of twee dagen na aankoop aankomen. Distributiecentra moeten zich aanpassen aan deze trends en tegelijkertijd bestaande inefficiënties overwinnen. 

Vandaag, een afhankelijkheid van handenarbeid betekent dat plukken en verpakken goed is voor 55-65% van de distributiecentrum kosten. Handmatig kiezen en verpakken zijn meestal ook trager dan geautomatiseerde systemen, en snel fluctuerende personeelsbehoeften maken het nog moeilijker om aan de verzendvolumes te voldoen. Deze seizoensfluctuatie leidt tot een hoog personeelsverloop en verhoogt de kans op kostbare fouten.

Oplossingen op basis van IoT-camera's kunnen transformationele voordelen opleveren door een digitale feedbacklus mogelijk te maken. Gegevens uit het hele distributiecentrum leiden tot bruikbare inzichten die op hun beurt betere data opleveren.

De voordelen van een digitaal distributiecentrum zijn:

* Camera's controleren goederen als ze aankomen en bewegen door het transportsysteem.
* Automatische identificatie van defecte goederen.
* Efficiënte ordertracking.
* Lagere kosten, verbeterde productiviteit en geoptimaliseerd gebruik.

De volgende schermafbeelding toont het out-of-the-box dashboard in de toepassingssjabloon. Het dashboard is volledig aanpasbaar om te voldoen aan uw specifieke oplossingsvereisten: 

> [!div class="mx-imgBorder"]
> ![Dashboard voor digitaal distributiecentrum](./media/overview-iot-central-retail/digital-distribution-center-dashboard.png)

Zie voor meer informatie de zelfstudie voor een sjabloon [voor een digitaal distributiecentrum.](./tutorial-iot-central-digital-distribution-center.md)

## <a name="in-store-analytics---condition-monitoring"></a>In-store analytics - conditiebewaking

Voor veel detailhandelaren zijn de milieuomstandigheden in hun winkels een belangrijke differentiator van hun concurrenten. Retailers willen aangename omstandigheden in hun winkels te handhaven ten behoeve van hun klanten.  

Als oplossingsbouwer u de toepassingsjabloon voor het monitoren van de iot-centrale-analyseconditie gebruiken om een end-to-end-oplossing te bouwen. Met de toepassingssjabloon u digitaal verbinding maken met en een winkelomgeving bewaken met behulp van verschillende soorten sensorapparaten. Deze sensorapparaten genereren telemetrie die u omzetten in bedrijfsinzichten die de retailer helpen om de bedrijfskosten te verlagen en een geweldige ervaring voor hun klanten te creëren.

Gebruik de toepassingssjabloon om:

* Sluit verschillende IoT-sensoren aan op een IoT Central-toepassingsexemplaar.
* De status van het sensornetwerk en alle gateway-apparaten in de omgeving bewaken en beheren.
* Maak aangepaste regels rond de omgevingsomstandigheden in een winkel om waarschuwingen voor winkelmanagers te activeren.
* Transformeer de omgevingsomstandigheden in uw winkel in inzichten die het winkelteam kan gebruiken om de klantervaring te verbeteren.
* Exporteer de geaggregeerde inzichten naar bestaande of nieuwe bedrijfstoepassingen om nuttige en tijdige informatie te verstrekken aan winkelpersoneel.

De toepassingssjabloon wordt geleverd met een set apparaatsjablonen en gebruikt een set gesimuleerde apparaten om het dashboard te vullen. 

De volgende schermafbeelding toont het out-of-the-box dashboard in de toepassingssjabloon. Het dashboard is volledig aanpasbaar om te voldoen aan uw specifieke oplossingsvereisten: 

> [!div class="mx-imgBorder"]
> ![Conditiebewaking in de store Analytics](./media/overview-iot-central-retail/in-store-analytics-condition-dashboard.png)

Zie de [analysetoepassing Een analyse maken in Azure IoT Central](./tutorial-in-store-analytics-create-app.md) voor meer informatie.

## <a name="in-store-analytics---checkout"></a>Analyse in de winkel - afrekenen

Voor sommige retailers is de kassa-ervaring in hun winkels een belangrijke differentiator van hun concurrenten. Retailers willen een soepele betaalervaring in hun winkels bieden om klanten aan te moedigen terug te keren.  

Als oplossingsbouwer u de iot central-in-store analytics checkout-toepassingssjabloon gebruiken om een oplossing te bouwen die inzichten biedt vanuit de kassazone van een winkel aan winkelpersoneel. Sensoren kunnen bijvoorbeeld informatie geven over wachtrijlengtes en gemiddelde wachttijden voor elke kassarijstrook.

Gebruik de toepassingssjabloon om:

* Sluit verschillende IoT-sensoren aan op een IoT Central-toepassingsexemplaar.
* De status van het sensornetwerk en alle gateway-apparaten in de omgeving bewaken en beheren.
* Maak aangepaste regels rond de betalingsvoorwaarde in een winkel om waarschuwingen voor winkelpersoneel te activeren.
* Transformeer de kassaomstandigheden in de winkel in inzichten die het winkelteam kan gebruiken om de klantervaring te verbeteren.
* Exporteer de geaggregeerde inzichten naar bestaande of nieuwe bedrijfstoepassingen om nuttige en tijdige informatie te verstrekken aan winkelpersoneel.

De toepassingssjabloon wordt geleverd met een set apparaatsjablonen en gebruikt een set gesimuleerde apparaten om het dashboard te vullen met gegevens over de bezetting van de rijstrook. 

De volgende schermafbeelding toont het out-of-the-box dashboard in de toepassingssjabloon. Het dashboard is volledig aanpasbaar om te voldoen aan uw specifieke oplossingsvereisten: 

> [!div class="mx-imgBorder"]
> ![Afhandeling van in-store-analyses](./media/overview-iot-central-retail/In-Store-Analytics-Checkout-Dashboard.png)

Zie de [analysetoepassing Een analyse maken in Azure IoT Central](./tutorial-in-store-analytics-create-app.md) voor meer informatie.

## <a name="smart-inventory-management"></a>Slim voorraadbeheer

Voorraad is de voorraad van goederen die een detailhandelaar heeft. Voorraadbeheer is essentieel om ervoor te zorgen dat het juiste product op het juiste moment op de juiste plaats is. Een detailhandelaar moet de kosten van het opslaan van te veel voorraad afhouden van de kosten van het niet op voorraad hebben van voldoende artikelen om aan de vraag te voldoen.

IoT-gegevens die worden gegenereerd met RFID-tags (Radio Frequency Identification), beacons en camera's bieden mogelijkheden om voorraadbeheerprocessen te verbeteren. U telemetrie verzameld van IoT-sensoren en apparaten combineren met andere gegevensbronnen, zoals weers- en verkeersinformatie in cloudgebaseerde business intelligence-systemen.

De voordelen van slim voorraadbeheer zijn:

* Het verminderen van het risico van artikelen die niet op voorraad en zorgen voor de gewenste klantenservice niveau. 
* Diepgaande analyse en inzichten in de nauwkeurigheid van de voorraad in bijna real-time.
* Tools om te beslissen over de juiste hoeveelheid voorraad te houden om te voldoen aan orders van klanten.

Deze toepassingssjabloon richt zich op apparaatconnectiviteit en de configuratie en het beheer van RFID- en Bluetooth-low energy (BLE) lezerapparaten.

De volgende schermafbeelding toont het out-of-the-box dashboard in de toepassingssjabloon. Het dashboard is volledig aanpasbaar om te voldoen aan uw specifieke oplossingsvereisten:

> [!div class="mx-imgBorder"]
> ![Dashboard voor slim voorraadbeheer](./media/overview-iot-central-retail/smart-inventory-management-dashboard.png)

Zie voor meer informatie de [zelfstudie voor een slimme sjabloon voor voorraadbeheertoepassingen implementeren en doorlopen.](./tutorial-iot-central-smart-inventory-management.md)

## <a name="micro-fulfillment-center"></a>Micro-uitvoeringscentrum

In het steeds concurrerender wordende retaillandschap staan retailers voortdurend onder druk om de kloof tussen vraag en vervulling te dichten. Een nieuwe trend die is ontstaan om de groeiende vraag van de consument aan te pakken is om de inventaris huis in de buurt van de eindklanten en de winkels die ze bezoeken.

De IoT Central micro-fulfillment center applicatie sjabloon stelt solution builders in staat om alle aspecten van hun volledig geautomatiseerde fulfillment centers te monitoren en te beheren. De sjabloon bevat een set gesimuleerde conditiebewakingssensoren en robotdragers om het proces voor het ontwikkelen van oplossingen te versnellen. Deze sensorapparaten vangen zinvolle signalen op die kunnen worden omgezet in bedrijfsinzichten, waardoor retailers hun bedrijfskosten kunnen verlagen en ervaringen voor hun klanten kunnen creëren.

Met de toepassingssjabloon u: 

- Sluit naadloos verschillende soorten IoT-sensoren aan, zoals robots of conditiebewakingssensoren, naadloos aan op een IoT Central-toepassingsexemplaar.
- De status van het sensornetwerk en alle gateway-apparaten in de omgeving bewaken en beheren.
- Maak aangepaste regels rond de omgevingsomstandigheden in een fulfillmentcenter om de juiste waarschuwingen te activeren.
- Transformeer de omgevingsomstandigheden binnen uw fulfillment center in inzichten die kunnen worden benut door het retail warehouse team.
- Exporteer de geaggregeerde inzichten naar bestaande of nieuwe bedrijfsapplicaties ten behoeve van de retailmedewerkers.

De volgende schermafbeelding toont het out-of-the-box dashboard in de toepassingssjabloon. Het dashboard is volledig aanpasbaar om te voldoen aan uw specifieke oplossingsvereisten:

> [!div class="mx-imgBorder"]
> ![Micro-fulfilment Centrum](./media/overview-iot-central-retail/MFC-Dashboard.png)

Zie de zelfstudie voor de toepassing Implementatie en loop door de sjabloonzelfstudie van het micro-fulfillmentcenter.For learn more, see the [Deploy and walk through the micro-fulfillment center application template tutorial.](./tutorial-micro-fulfillment-center.md)

## <a name="next-steps"></a>Volgende stappen

Om aan de slag te gaan met het bouwen van een retailoplossing:

* Ga aan de slag met de [analysetoepassing In-store maken in Azure IoT Central,](./tutorial-in-store-analytics-create-app.md) waarin u een oplossing bouwen met een van de in-store analytics-toepassingssjablonen.
* [Implementeren en doorlopen van een sjabloon voor verbonden logistieke toepassingen.](./tutorial-iot-central-connected-logistics.md)
* [Implementeren en doorlopen van een sjabloon voor de toepassing van een digitaal distributiecentrum.](./tutorial-iot-central-digital-distribution-center.md)
* [Implementeren en doorlopen van een slimme voorraadbeheertoepassingssjabloon.](./tutorial-iot-central-smart-inventory-management.md)
* [Implementeren en doorlopen van de toepassingssjabloon voor microafhandelingscentrum.](./tutorial-micro-fulfillment-center.md)
* Meer informatie over IoT Central vindt u in het [IoT Central-overzicht](../preview/overview-iot-central.md).
