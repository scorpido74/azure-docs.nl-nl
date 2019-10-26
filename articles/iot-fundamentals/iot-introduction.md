---
title: Inleiding tot Azure Internet of Things (IoT)
description: Inleiding met uitleg over de grondbeginselen van Azure IoT en de IoT-services, inclusief voorbeelden ter illustratie van het gebruik van IoT.
author: robinsh
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: overview
ms.date: 10/11/2018
ms.author: robinsh
ms.openlocfilehash: 2f46ebcabd98c7a8c3376157c72da9ec5ed424a4
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72935229"
---
# <a name="what-is-azure-internet-of-things-iot"></a>Wat is Azure Internet of Things (IoT)?

Azure Internet of Things (IoT) is een verzameling van door Microsoft beheerde cloudservices die miljarden IoT-assets met elkaar verbinden, bewaken en controleren. Eenvoudiger gezegd, een IoT-oplossing bestaat uit een of meer IoT-apparaten en een of meer back-endservices in de cloud die met elkaar communiceren. 

In dit artikel vindt u een beschrijving van de grondbeginselen van IoT, gebruiksvoorbeelden en een overzicht van de acht verschillende beschikbare services. Als u weet wat er beschikbaar is, kunt u bepalen welke mogelijkheden u nader wilt onderzoeken voor het ontwerp van uw scenario.

## <a name="introduction"></a>Inleiding

De belangrijkste onderdelen van een IoT-oplossing zijn: apparaten, back-endservices en de communicatie tussen beide. 

### <a name="iot-devices"></a>IoT-apparaten

Apparaten bestaan meestal uit een printplaat met sensoren die verbinding maken met internet. Veel apparaten maken voor de communicatie gebruik van een Wi-Fi-chip. Hier volgen enkele voorbeelden van IoT-apparaten:

* druksensoren op een externe oliepomp
* temperatuur- en luchtvochtigheidssensoren in een airco-eenheid
* versnellingsmeters in een lift
* aanwezigheidssensoren in een ruimte

Twee apparaten die vaak worden gebruikt voor het ontwikkelen van prototypen zijn de MX Chip IoT-ontwikkelkit van Microsoft en Raspberry PI-apparaten. De MX Chip-ontwikkelkit heeft ingebouwde sensoren voor de temperatuur, druk en luchtvochtigheid, plus een gyroscoop en een versnellingsmeter, een magnetometer en een Wi-Fi-chip. Raspberry PI is een IoT-apparaat waaraan u veel verschillende soorten sensoren kunt koppelen, zodat u precies kunt selecteren wat u nodig hebt voor uw scenario. 

Bekijk voor meer informatie over beschikbare IoT-apparaten de grootste [catalogus van voor IoT gecertificeerde apparaten](https://catalog.azureiotsolutions.com/alldevices) in de bedrijfstak.

Met de [SDK's voor IoT-apparaten](../iot-hub/iot-hub-devguide-sdks.md) kunt u apps bouwen die worden uitgevoerd op uw apparaten, zodat ze de gewenste taken kunnen uitvoeren. Met de SDK's kunt u telemetriegegevens naar uw IoT-hub verzenden, berichten en updates van de IoT-hub ontvangen, enzovoort.

### <a name="communication"></a>Communicatie

Uw apparaat kan communiceren met back-endservices in beide richtingen. Hier volgen enkele voorbeelden van manieren waarop het apparaat met de back-end-oplossing kan communiceren.

#### <a name="examples"></a>Voorbeelden 

* Uw apparaat kan de temperatuur van de koelinstallatie van een vrachtwagen elke 5 minuten naar een IoT-hub verzenden. 

* De back-endservice kan het apparaat vragen om vaker telemetriegegevens te verzenden om een probleem te diagnosticeren. 

* Uw apparaat kan waarschuwingen verzenden op basis van de uitgelezen waarden van de sensoren. Om een koelreactor in een chemische fabriek te bewaken, wilt u wellicht een waarschuwing verzenden wanneer de temperatuur een bepaalde waarde overschrijdt.

* Uw apparaat kan informatie verzenden naar een dashboard voor weergave aan menselijke operators. In een controleruimte in een raffinaderij kunnen bijvoorbeeld de temperatuur en druk van elke pijpleiding worden weergegeven, evenals het volume dat door de pijpleiding stroomt, zodat de operators deze kunnen bewaken. 

Deze (en nog veel meer) taken kunnen worden geïmplementeerd met behulp van de [SDK's voor IoT-apparaten](../iot-hub/iot-hub-devguide-sdks.md).

#### <a name="connection-considerations"></a>Overwegingen voor de verbinding

De grootste uitdaging voor IoT-oplossingen is vaak om apparaten op een veilige en betrouwbare manier verbinding te laten maken. Vergeleken met andere clients, zoals browsers en mobiele apps, hebben IoT-apparaten namelijk andere kenmerken. IoT-apparaten:

* Zijn vaak ingesloten systemen waarbij geen menselijke operator komt kijken (in tegenstelling tot een telefoon).

* Kunnen worden geïmplementeerd op verafgelegen locaties, waar fysieke toegang kostbaar is.

* Kunnen mogelijk alleen worden bereikt via de back-end van de oplossing. Er is geen andere manier om te communiceren met het apparaat.

* Hebben mogelijk een beperkt vermogen en een beperkt aantal verwerkingsbronnen.

* Hebben mogelijk een onstabiele, trage of dure netwerkverbinding.

* Vereisen mogelijk het gebruik van bedrijfseigen, aangepaste of branchespecifieke toepassingsprotocollen.

### <a name="back-end-services"></a>Back-endservices 

Hier volgen enkele voorbeelden van functies van back-endservices.

* Ontvangen van telemetriegegevens van uw apparaten en bepalen hoe die gegevens moeten worden verwerkt en opgeslagen.

* Analyseren van de telemetriegegevens om inzichten te krijgen, ongeacht of dat in realtime of achteraf wordt gedaan.

* Verzenden van opdrachten vanuit de cloud naar een bepaald apparaat. 

* Apparaten inrichten en bepalen welke apparaten verbinding mogen maken met uw infrastructuur.

* Bijhouden van de status van uw apparaten en hun activiteiten volgen.

In een scenario voor voorspeld onderhoud slaat de back-end van de cloud bijvoorbeeld historische telemetriegegevens op. De oplossing gebruikt deze gegevens om mogelijk afwijkend gedrag van specifieke pompen te identificeren voordat er een echt probleem ontstaat. Met behulp van gegevensanalyses kan worden bepaald dat de preventieve oplossing een opdracht moet verzenden naar het apparaat om een corrigerende actie te ondernemen. Dit proces genereert een automatische feedbacklus tussen het apparaat en de cloud die de efficiëntie van de oplossing aanzienlijk verhoogt.

## <a name="an-iot-example"></a>Een IoT-voorbeeld

Hier volgt een voorbeeld van hoe een bedrijf IoT heeft gebruikt om miljoenen dollars te besparen. 

Een grote veehouderij heeft honderden tot duizenden koeien. Het is een hels karwei om zo veel koeien tegelijk in de gaten te houden en te weten hoe het met ze gaat. Daarvoor wordt heel wat afgereden. Daarom hebben ze elke koe voorzien van een sensor die informatie zoals de GPS-coördinaten en temperatuur doorgeeft aan een back-endservice. Vervolgens worden die gegevens naar een database geschreven.

Met behulp van een analytische service worden de binnenkomende gegevens gescand en per koe geanalyseerd op vragen als:

* Heeft de koe koorts? Hoe lang heeft de koe al koorts? Als dit langer dan een dag is, halen ze de GPS-coördinaten op, zoeken de koe en behandelen deze zo nodig met antibiotica. 

* Is de koe langer dan een dag op dezelfde plek? Zo ja, dan worden de GPS-coördinaten opgehaald en gaat men de koe zoeken. Is de koe van een rots gevallen? Is het dier gewond? Heeft de koe hulp nodig? 

Door deze IoT-oplossing te implementeren werd het mogelijk voor het bedrijf om de koeien snel te controleren en behandelen. Zo hoeft men minder vaak naar de dieren te rijden om ze te controleren, en dat bespaart het bedrijf veel geld. Zie [Microsoft Technical Case Studies for IoT](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured) (technische casestudy's voor IoT van Microsoft) voor meer praktijkvoorbeelden van het gebruik van IoT door bedrijven. 

## <a name="iot-services"></a>IoT Services

Er zijn meerdere IoT-services in Azure en het kan moeilijk zijn om te bepalen welke u wilt gebruiken. Sommige services, zoals IoT Central en de IoT-oplossingsverbeteringen, bevatten sjablonen waarmee u uw eigen oplossing kunt maken zodat u snel aan de slag kunt met IoT. U kunt uw eigen oplossingen ook helemaal zelf ontwikkelen met behulp van andere services. Het hangt er maar net vanaf hoeveel hulp en controle u wilt. Hier volgt een lijst met de beschikbare services en waarvoor u ze kunt gebruiken.

1. [**IOT Central**](../iot-central/core/overview-iot-central.md): dit is een SaaS-oplossing waarmee u uw IOT-apparaten kunt verbinden, bewaken en beheren. Selecteer om te beginnen een sjabloon voor uw type apparaat, en maak en test een IoT Central-basistoepassing die de operators van het apparaat gaan gebruiken. Met de IoT Central-toepassing kunt u de apparaten ook bewaken en nieuwe apparaten inrichten. Deze service is geschikt voor eenvoudige IoT-oplossingen waarvoor geen uitgebreide serviceaanpassing is vereist. 

2. [**IoT-oplossingsverbeteringen**](/azure/iot-suite): dit is een verzameling van PaaS-oplossingen waarmee u de ontwikkeling van uw IoT-oplossing kunt versnellen. U begint met een vooraf gedefinieerde IoT-oplossing en past die vervolgens volledig naar wens aan. U moet beschikken over Java- of .NET-vaardigheden om de back-end aan te passen, en over JavaScript-vaardigheden om de visualisatie aan te passen. 

3. [**IoT Hub**](/azure/iot-hub/): met deze service kunt u uw apparaten verbinden met een IoT-hub en miljarden IoT-apparaten beheren. Dit is vooral handig als bidirectionele communicatie tussen uw IoT-apparaten en uw back-end vereist is. Dit is de onderliggende service voor IoT Central en IoT-oplossingsverbeteringen. 

4. [**IoT Hub Device Provisioning Service**](/azure/iot-dps/): dit is een hulpservice voor IoT Hub waarmee u apparaten veilig kunt inrichten voor uw IoT-hub. Met deze service kunt u snel en eenvoudig miljoenen apparaten tegelijk inrichten in plaats van ze één voor één in te richten. 

5. [**IoT Edge**](/azure/iot-edge/): deze service bouwt voort op IoT Hub. Deze service kan worden gebruikt voor het analyseren van gegevens op de IoT-apparaten in plaats van in de cloud. Door uw werkbelasting gedeeltelijk te verplaatsen naar de randapparaten, hoeven er minder berichten te worden verzonden naar de cloud. 

6. [**Azure Digital Twins**](../digital-twins/index.yml): met deze service kunt u uitgebreide modellen van de fysieke omgeving maken. Zo kunt u de relaties en interacties tussen personen, ruimten en apparaten modelleren. U kunt bijvoorbeeld de onderhoudsbehoeften voor een fabriek voorspellen, het energieverbruik voor een elektriciteitsnet in realtime analyseren of het gebruik van de beschikbare kantoorruimte optimaliseren.

7. [**Time Series Insights**](/azure/time-series-insights): met deze service kunt u grote volumes gegenereerde tijdreeksgegevens van IoT-apparaten opslaan en visualiseren of er query's op uitvoeren. U kunt deze gebruiken in combinatie met IoT Hub. 

8. [**Azure Maps**](/azure/azure-maps): deze service biedt geografische gegevens voor web- en mobiele toepassingen. Er is een volledige set met REST-API's en een webgebaseerd JavaScript-besturingselement waarmee u flexibele toepassingen kunt maken voor gebruik in bureaublad- of mobiele toepassingen op Apple- en Windows-apparaten.

## <a name="next-steps"></a>Volgende stappen

Zie de [technische casestudy's van Microsoft Azure IoT](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured) voor enkele voorbeelden uit de praktijk en de architectuur die daarvoor is gebruikt.

Zie de [projectcatalogus van de IoT-ontwikkelkit](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) voor enkele voorbeeldprojecten die u kunt uitproberen met een IoT-ontwikkelkit. 

Zie [Azure IoT-services en technologieën](iot-services-and-technologies.md) voor uitgebreide informatie over de verschillende services en hoe ze worden gebruikt.

Zie [Microsoft Azure IoT Reference Architecture](https://aka.ms/iotrefarchitecture) (Referentiearchitectuur voor Microsoft Azure IoT) voor een uitgebreide beschrijving van de IoT-architectuur.
