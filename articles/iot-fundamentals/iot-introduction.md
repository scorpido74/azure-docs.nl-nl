---
title: Inleiding tot Azure Internet of Things (IoT)
description: Inleiding met uitleg over de grondbeginselen van Azure IoT en de IoT-services, inclusief voorbeelden ter illustratie van het gebruik van IoT.
author: dominicbetts
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: overview
ms.date: 01/15/2020
ms.author: dobett
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 73eb0b3164a386bb270e42ceba56d5dc7045af1c
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81728995"
---
# <a name="what-is-azure-internet-of-things-iot"></a>Wat is Azure Internet of Things (IoT)?

Azure Internet of Things (IoT) is een verzameling van door Microsoft beheerde cloudservices die miljarden IoT-assets met elkaar verbinden, bewaken en controleren. Eenvoudiger is dat een IoT-oplossing bestaat uit een of meer IoT-apparaten die communiceren met een of meer back-endservices die in de cloud worden gehost. 

## <a name="iot-devices"></a>IoT-apparaten

Een IoT-apparaat bestaat meestal uit een printplaat met sensoren die WiFi gebruiken om verbinding te maken met het internet. Bijvoorbeeld:

* Een druksensor op een oliepomp op afstand.
* Temperatuur- en vochtigheidssensoren in een airconditioningunit.
* Een versnellingsmeter in een lift.
* Aanwezigheidssensoren in een kamer.

Er is een breed scala aan apparaten beschikbaar van verschillende fabrikanten om uw oplossing te bouwen. Zie de [azure certified for IoT-apparaatcatalogus voor](https://catalog.azureiotsolutions.com/alldevices)een lijst met apparaten die zijn gecertificeerd om met Azure IoT-hub te werken. Voor prototypen u apparaten gebruiken zoals een [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) of een [Raspberry Pi.](https://www.raspberrypi.org/) De Devkit heeft ingebouwde sensoren voor temperatuur, druk, vochtigheid en een gyroscoop, versnellingsmeter en magnetometer. Met de Raspberry Pi kun je veel verschillende soorten sensoren bevestigen. 

Microsoft biedt open-source [Device SDKs](../iot-hub/iot-hub-devguide-sdks.md) die u gebruiken om de apps te bouwen die op uw apparaten worden uitgevoerd. Deze [SDK's vereenvoudigen en versnellen](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/) de ontwikkeling van uw IoT-oplossingen.

## <a name="communication"></a>Communicatie

Doorgaans verzenden IoT-apparaten telemetrie van de sensoren naar back-endservices in de cloud. Andere vormen van communicatie zijn echter mogelijk, zoals een back-endservice die opdrachten naar uw apparaten verzendt. Hieronder volgen enkele voorbeelden van communicatie tussen apparaat en cloud en cloud tot apparaat:

* Een mobiele koeltruck stuurt elke 5 minuten de temperatuur naar een IoT Hub. 

* De back-endservice stuurt een opdracht naar een apparaat om de frequentie te wijzigen waarmee het telemetrie verzendt om een probleem te diagnosticeren. 

* Een apparaat stuurt waarschuwingen op basis van de waarden die worden gelezen op basis van de sensoren. Een apparaat dat een batchreactor in een chemische installatie controleert, stuurt bijvoorbeeld een waarschuwing wanneer de temperatuur een bepaalde waarde overschrijdt.

* Uw apparaten verzenden informatie om weer te geven op een dashboard om te bekijken door menselijke operators. Een controlekamer in een raffinaderij kan bijvoorbeeld de temperatuur-, druk- en stroomvolumes in elke leiding weergeven, zodat de operatoren de faciliteit kunnen controleren. 

De [IoT Device SDKs](../iot-hub/iot-hub-devguide-sdks.md) en IoT Hub ondersteunen algemene [communicatieprotocollen](../iot-hub/iot-hub-devguide-protocols.md) zoals HTTP, MQTT en AMQP.

IoT-apparaten hebben verschillende kenmerken in vergelijking met andere clients, zoals browsers en mobiele apps. De SDK's van het apparaat helpen u de uitdagingen aan te gaan van het veilig en betrouwbaar aansluiten van apparaten op uw back-endservice.  IoT-apparaten:

* Zijn vaak ingesloten systemen waarbij geen menselijke operator komt kijken (in tegenstelling tot een telefoon).
* Kunnen worden geïmplementeerd op verafgelegen locaties, waar fysieke toegang kostbaar is.
* Kunnen mogelijk alleen worden bereikt via de back-end van de oplossing.
* Hebben mogelijk een beperkt vermogen en een beperkt aantal verwerkingsbronnen.
* Hebben mogelijk een onstabiele, trage of dure netwerkverbinding.
* Vereisen mogelijk het gebruik van bedrijfseigen, aangepaste of branchespecifieke toepassingsprotocollen.

## <a name="back-end-services"></a>Back-endservices 

In een IoT-oplossing biedt de back-endservice functionaliteit zoals:

* Ontvangen van telemetriegegevens van uw apparaten en bepalen hoe die gegevens moeten worden verwerkt en opgeslagen.
* Analyseren van de telemetriegegevens om inzichten te krijgen, ongeacht of dat in realtime of achteraf wordt gedaan.
* Verzenden van opdrachten vanuit de cloud naar een bepaald apparaat. 
* Apparaten inrichten en bepalen welke apparaten verbinding kunnen maken met uw infrastructuur.
* De status van uw apparaten controleren en hun activiteiten controleren.
* De firmware beheren die op uw apparaten is geïnstalleerd.

Bijvoorbeeld, in een remote monitoring oplossing voor een olie pompstation, de cloud back-end maakt gebruik van telemetrie van de pompen om afwijkend gedrag te identificeren. Wanneer de back-endservice een afwijking identificeert, kan deze automatisch een opdracht terugsturen naar het apparaat om een corrigerende actie te ondernemen. Dit proces genereert een automatische feedbacklus tussen het apparaat en de cloud die de efficiëntie van de oplossing aanzienlijk verhoogt.

## <a name="azure-iot-examples"></a>Azure IoT-voorbeelden

Zie [Microsoft Technical Case Studies voor IoT voor](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured)voorbeelden uit de praktijk van hoe organisaties Azure IoT gebruiken. 

## <a name="next-steps"></a>Volgende stappen

Zie de [technische casestudy's van Microsoft Azure IoT](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured) voor enkele voorbeelden uit de praktijk en de architectuur die daarvoor is gebruikt.

Zie de [projectcatalogus van de IoT-ontwikkelkit](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) voor enkele voorbeeldprojecten die u kunt uitproberen met een IoT-ontwikkelkit. 

Zie [Azure IoT-services en -technologieën](iot-services-and-technologies.md)voor een uitgebreidere uitleg over de verschillende services en hoe ze worden gebruikt.

Zie [Microsoft Azure IoT Reference Architecture](https://aka.ms/iotrefarchitecture) (Referentiearchitectuur voor Microsoft Azure IoT) voor een uitgebreide beschrijving van de IoT-architectuur.
