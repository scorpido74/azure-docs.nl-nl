---
title: Retail oplossingen bouwen met Azure IoT Central | Microsoft Docs
description: Meer informatie over het gebruik van Azure IoT Central-toepassings sjablonen voor het bouwen van verbonden logistiek, digitaal distributie centrum, in-Store-analyses, controle op de voor waarde, afhandeling, Smart Inventory Management en handels oplossingen.
author: avneet723
ms.author: avneets
ms.date: 01/10/2020
ms.topic: overview
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
ms.openlocfilehash: 171f06ad238f862adbd7071ee10d81133d7a6855
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77022134"
---
# <a name="building-retail-solutions-with-azure-iot-central"></a>Oplossingen voor de detailhandel bouwen met Azure IoT Central



Azure IoT Central is een IoT app-platform waarmee de belasting en de kosten voor het ontwikkelen, beheren en onderhouden van IoT-oplossingen op bedrijfs niveau worden verminderd. Als u ervoor kiest om met Azure IoT Central te bouwen, kunt u zich richten op uw tijd, geld en energie over het trans formatie van uw bedrijf met IoT-gegevens, in plaats van alleen maar een complexe en voortdurend veranderende IoT-infra structuur te onderhouden en bij te werken.

In dit artikel worden verschillende branchespecifieke IoT Central toepassings sjablonen beschreven. Als oplossings bouwer kunt u deze sjablonen gebruiken om IoT-oplossingen te bouwen die de toeleverings ketens optimaliseren, de in-Store-ervaring voor klanten te verbeteren en de voor Raad efficiënter te volgen.

> [!div class="mx-imgBorder"]
> ![Azure IoT Retail Overview](./media/overview-iot-central-retail/retail-app-templates.png)

In de volgende secties worden de mogelijkheden van deze toepassings sjablonen beschreven:

## <a name="connected-logistics"></a>Verbonden logistiek

De kosten voor wereld wijde logistiek worden verwacht $10.600.000.000.000 te bereiken in 2020. Het Trans Port van goederen accounts voor het meren deel van deze uitgaven-en verzend providers ligt onder intensieve concurrentie druk en beperkingen.

U kunt IoT Sens oren gebruiken om omgevings voorwaarden te verzamelen en te bewaken, zoals Tempe ratuur, vochtigheid, kanteling, schokken, licht en de locatie van een verzen ding. U kunt telemetrie combi neren die is verzameld van IoT-Sens oren en apparaten met andere gegevens bronnen, zoals weer en verkeers gegevens in business intelligence systemen in de Cloud.

De voor delen van een verbonden logistiek-oplossing zijn:

* Verzendings controle met realtime tracering en tracering. 
* Leverings integriteit met realtime bewaking van omgevings condities.
* Beveiliging tegen dief stal, verlies of beschadiging van verzen dingen.
* Geoomheining, route optimalisatie, vloot beheer en voertuig analyses.
* Prognoses voor voorspel bare vertrek en ontvangst van zendingen.

In de volgende scherm afbeeldingen ziet u het out-of-the-box-dash board in de toepassings sjabloon. Het dash board is volledig aanpasbaar om te voldoen aan uw specifieke oplossings vereisten:

> [!div class="mx-imgBorder"]
> ![verbonden logistiek-dash board](./media/overview-iot-central-retail/connected-logistics-dashboard1.png)

> [!div class="mx-imgBorder"]
> ![verbonden logistiek-dash board](./media/overview-iot-central-retail/connected-logistics-dashboard2.png)

Zie de zelf studie [een verbonden logistiek-toepassings sjabloon implementeren en door lopen](./tutorial-iot-central-connected-logistics-pnp.md) voor meer informatie.

## <a name="digital-distribution-center"></a>Digitaal distributiecentrum

Naarmate fabrikanten en detail handelaren wereld wijde aanwezigheids vormen, zijn de toeleverings ketens vertakking uit en worden ze complexer. Consumenten verwachten nu dat er grote selecties van producten beschikbaar zijn en dat deze goederen binnen een of twee dagen na aankoop kunnen aankomen. Distributie centra moeten worden aangepast aan deze trends terwijl bestaande inefficiënties worden overtreden. 

Vandaag de dag is afhankelijk van een beroep op hand matige arbeid dat het picken en inpakken van accounts voor 55-65% van de kosten van het distributie centrum. Hand matig orderverzamelen en verpakken zijn meestal trager dan geautomatiseerde systemen, en het snel wisselen van personeels behoeften maakt het zelfs moeilijker om te voldoen aan de verzend volumes. Deze fluctuatie schommelt de omzet van hoge mede werkers en verg root de kans op dure fouten.

Oplossingen op basis van IoT ingeschakelde camera's kunnen transformationele-voor delen bieden door een digitale feedback-lus in te scha kelen. Gegevens van in het distributie centrum leiden tot inzicht in inzichten die op hun beurt resulteren in betere gegevens.

De voor delen van een digitaal distributie centrum zijn:

* Camera's bewaken de goederen wanneer ze binnenkomen en door het transport systeem bladeren.
* Automatische identificatie van defecte goederen.
* Efficiënte order tracering.
* Lagere kosten, verbeterde productiviteit en geoptimaliseerd gebruik.

In de volgende scherm afbeelding ziet u het out-of-the-box-dash board in de toepassings sjabloon. Het dash board is volledig aanpasbaar om te voldoen aan uw specifieke oplossings vereisten: 

> [!div class="mx-imgBorder"]
> ![Digital Distribution Center-dash board](./media/overview-iot-central-retail/digital-distribution-center-dashboard.png)

Meer informatie vindt u in de zelf studie [een digitale distributie centrum-toepassings sjabloon implementeren en door lopen](./tutorial-iot-central-digital-distribution-center-pnp.md) .

## <a name="in-store-analytics---condition-monitoring"></a>Bewaking in de Store-voor waarden

Voor veel detail handelaren zijn omgevings voorwaarden in hun winkels een belang rijke onderscheid van hun concurrenten. Detail handelaren willen voor deel van hun klanten onaangename voor waarden in hun winkels onderhouden.  

Als oplossings bouwer kunt u de toepassings sjabloon IoT Central in-Store analyse voorwaarde bewaking gebruiken om een end-to-end oplossing te bouwen. Met de toepassings sjabloon kunt u een Retail Store-omgeving digitaal verbinden en bewaken met verschillende typen sensor apparaten. Deze sensor apparaten genereren telemetrie die u kunt omzetten in Business Insights om de bedrijfs kosten te verlagen en een geweldige ervaring voor hun klanten te creëren.

Gebruik de toepassings sjabloon voor het volgende:

* Verbind een aantal IoT-Sens oren met een IoT Central toepassings exemplaar.
* Bewaak en beheer de status van het sensor netwerk en alle gateway apparaten in de omgeving.
* Maak aangepaste regels rondom de omgevings voorwaarden in een Store om waarschuwingen voor winkel managers te activeren.
* Transformeer de omgevings voorwaarden in uw winkel in inzichten die het Retail Store-team kan gebruiken om de gebruikers ervaring te verbeteren.
* De geaggregeerde inzichten exporteren naar bestaande of nieuwe zakelijke toepassingen om nuttige en tijdige informatie te verstrekken aan retail personeel.

De toepassings sjabloon wordt geleverd met een set met Apparaatbeheer en maakt gebruik van een set gesimuleerde apparaten om het dash board in te vullen. 

In de volgende scherm afbeelding ziet u het out-of-the-box-dash board in de toepassings sjabloon. Het dash board is volledig aanpasbaar om te voldoen aan uw specifieke oplossings vereisten: 

> [!div class="mx-imgBorder"]
> Bewaking van de ![in de Store-voor waarde](./media/overview-iot-central-retail/in-store-analytics-condition-dashboard.png)

Zie de zelf studie [een in-Store Analytics-toepassing maken in Azure IOT Central](./tutorial-in-store-analytics-create-app-pnp.md) voor meer informatie.

## <a name="in-store-analytics---checkout"></a>In-Store-analyses-uitchecken

Voor sommige detail handelaren is de uitcheck ervaring binnen hun winkels een belang rijke onderscheid van hun concurrenten. Detail handelaren willen in hun winkels een soepele ervaring voor de afhandeling bieden om klanten te stimuleren om te retour neren.  

Als oplossings bouwer kunt u de IoT Central in-Store Analytics-toepassings sjabloon gebruiken om een oplossing te bouwen die inzicht biedt in de afhandelings zone van een Store naar Retail personeel. Sens oren kunnen bijvoorbeeld informatie geven over de lengte van de wachtrij en gemiddelde wacht tijden voor elke afhandelings baan.

Gebruik de toepassings sjabloon voor het volgende:

* Verbind een aantal IoT-Sens oren met een IoT Central toepassings exemplaar.
* Bewaak en beheer de status van het sensor netwerk en alle gateway apparaten in de omgeving.
* Maak aangepaste regels rondom de uitcheck voorwaarde binnen een Store om waarschuwingen voor retail personeel te activeren.
* Transformeer de afhandelings voorwaarden in de Store in inzichten die het Retail Store-team kan gebruiken om de gebruikers ervaring te verbeteren.
* De geaggregeerde inzichten exporteren naar bestaande of nieuwe zakelijke toepassingen om nuttige en tijdige informatie te verstrekken aan retail personeel.

De toepassings sjabloon wordt geleverd met een set met Apparaatbeheer en maakt gebruik van een set gesimuleerde apparaten voor het vullen van het dash board met Lane-bezettings gegevens. 

In de volgende scherm afbeelding ziet u het out-of-the-box-dash board in de toepassings sjabloon. Het dash board is volledig aanpasbaar om te voldoen aan uw specifieke oplossings vereisten: 

> [!div class="mx-imgBorder"]
> ![in-Store Analytics-uitchecken](./media/overview-iot-central-retail/In-Store-Analytics-Checkout-Dashboard.png)

Zie de zelf studie [een in-Store Analytics-toepassing maken in Azure IOT Central](./tutorial-in-store-analytics-create-app-pnp.md) voor meer informatie.

## <a name="smart-inventory-management"></a>Slim voorraadbeheer

Inventaris is het aandeel van de goederen die door een winkel worden bewaard. Voorraad beheer is van cruciaal belang om ervoor te zorgen dat het juiste product zich op het juiste moment op de juiste plaats bevindt. Een detail handelaar moet rekening houden met de kosten voor het opslaan van te veel inventaris op basis van de kosten van het niet voldoende voorradige artikelen om aan de vraag te voldoen.

IoT-gegevens die zijn gegenereerd op basis van RFID-tags (Radio-Frequency Identification), bakens en camera's bieden mogelijkheden voor het verbeteren van voorraad beheer processen. U kunt telemetrie combi neren die is verzameld van IoT-Sens oren en apparaten met andere gegevens bronnen, zoals weer en verkeers gegevens in business intelligence systemen in de Cloud.

De voor delen van het beheer van Smart Inventory zijn:

* Het risico van het afnemen van de voor Raad en het gewenste service niveau van de klant te verminderen. 
* Diep gaande analyse en inzicht in de nauw keurigheid van de inventaris in bijna realtime.
* Hulp middelen om te helpen beslissen over het juiste aantal inventarisatie dat kan worden afgestemd op klant orders.

Deze toepassings sjabloon is gericht op de connectiviteit van apparaten en de configuratie en het beheer van RFID-en Bluetooth-lezer (Low Energy).

In de volgende scherm afbeelding ziet u het out-of-the-box-dash board in de toepassings sjabloon. Het dash board is volledig aanpasbaar om te voldoen aan uw specifieke oplossings vereisten:

> [!div class="mx-imgBorder"]
> ![dash board voor Smart Inventory Management](./media/overview-iot-central-retail/smart-inventory-management-dashboard.png)

Zie voor meer informatie de zelf studie [een slimme inventarisatie-sjabloon implementeren en door lopen](./tutorial-iot-central-smart-inventory-management-pnp.md) .

## <a name="micro-fulfillment-center"></a>Micro fulfillment Center

In de steeds concurrerende detail handel zijn de detail handelaren voortdurend druk om de kloof tussen vraag en uitvoering te dichten. Een nieuwe trend die is opgetreden om de groeiende vraag van de consument te verhelpen, is in de huis inventaris bij de eind klanten en de winkels die ze bezoeken.

Met de IoT Central micro fulfillment Center-toepassings sjabloon kunnen bouwers van oplossingen alle aspecten van hun volledig geautomatiseerde fulfillment-centrums bewaken en beheren. De sjabloon bevat een aantal gesimuleerde Sens oren en geautomatiseerde luchtvaart maatschappijen om het ontwikkelings proces voor oplossingen te versnellen. Deze sensor apparaten nemen zinvolle signalen op die kunnen worden omgezet in zakelijke inzichten, waardoor de bedrijfs kosten worden verminderd en ervaringen voor hun klanten worden gemaakt.

Met de toepassings sjabloon kunt u het volgende doen: 

- Verbind verschillende soorten IoT-Sens oren, zoals robots of condition bewaking Sens oren, naadloos met een IoT Central toepassings exemplaar.
- Bewaak en beheer de status van het sensor netwerk en alle gateway apparaten in de omgeving.
- Maak aangepaste regels rondom de omgevings voorwaarden binnen een fulfillment-centrum om de juiste waarschuwingen te activeren.
- Transformeer de omgevings voorwaarden binnen uw fulfillment Center in inzichten die kunnen worden gebruikt door het retail warehouse-team.
- Exporteer de geaggregeerde inzichten in bestaande of nieuwe zakelijke toepassingen voor het voor deel van de leden van de retail medewerker.

In de volgende scherm afbeelding ziet u het out-of-the-box-dash board in de toepassings sjabloon. Het dash board is volledig aanpasbaar om te voldoen aan uw specifieke oplossings vereisten:

> [!div class="mx-imgBorder"]
> ![micro fulfillment Center](./media/overview-iot-central-retail/MFC-Dashboard.png)

Raadpleeg voor meer informatie de zelf studie over [het implementeren en door lopen van de toepassing van de micro-fulfillment Center-toepassings sjabloon](./tutorial-iot-central-smart-inventory-management-pnp.md) .

## <a name="next-steps"></a>Volgende stappen

Om aan de slag te gaan met het bouwen van een handels oplossing:

* Ga aan de slag met de zelf studie [een in-Store Analytics-toepassing maken in Azure IOT Central](./tutorial-in-store-analytics-create-app-pnp.md) om u stapsgewijs te helpen bij het bouwen van een oplossing met een van de in-Store Analytics-toepassings sjablonen.
* [Een verbonden logistiek-toepassings sjabloon implementeren en door lopen](./tutorial-iot-central-connected-logistics-pnp.md).
* [Een Digital Distribution Center-toepassings sjabloon implementeren en door lopen](./tutorial-iot-central-digital-distribution-center-pnp.md).
* [Een sjabloon voor slimme inventaris beheer toepassing implementeren en door lopen](./tutorial-iot-central-smart-inventory-management-pnp.md).
* [Implementeer en volg de toepassings sjabloon van het micro uitvoerings centrum](./tutorial-iot-central-smart-inventory-management-pnp.md).
* Meer informatie over IoT Central vindt u in het [overzicht IOT Central](../preview/overview-iot-central.md).
