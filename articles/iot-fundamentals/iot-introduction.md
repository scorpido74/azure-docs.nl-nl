---
title: Inleiding tot Azure Internet of Things (IoT)
description: Inleiding met uitleg over de grondbeginselen van Azure IoT en de IoT-services, inclusief voorbeelden ter illustratie van het gebruik van IoT.
author: dominicbetts
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: overview
ms.date: 01/15/2020
ms.author: dobett
ms.openlocfilehash: c79f18669e1b13f79491e98658107221b43f3ff5
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/06/2020
ms.locfileid: "77046179"
---
# <a name="what-is-azure-internet-of-things-iot"></a>Wat is Azure Internet of Things (IoT)?

Azure Internet of Things (IoT) is een verzameling van door Microsoft beheerde cloudservices die miljarden IoT-assets met elkaar verbinden, bewaken en controleren. In het eenvoudigere voor waarden bestaat een IoT-oplossing uit een of meer IoT-apparaten die communiceren met een of meer back-end-services die in de cloud worden gehost. 

## <a name="iot-devices"></a>IoT-apparaten

Een IoT-apparaat bestaat doorgaans uit een print bord met Sens oren die WiFi gebruiken om verbinding te maken met internet. Bijvoorbeeld:

* Een druk sensor op een externe olie pomp.
* Tempe ratuur-en vochtigheids sensoren in een airconditioning eenheid.
* Een acceleratie meter in een lift.
* Aanwezigheids sensors in een ruimte.

Er is een groot aantal apparaten beschikbaar van verschillende fabrikanten om uw oplossing te bouwen. Zie [Azure Certified for IOT Device Catalog](https://catalog.azureiotsolutions.com/alldevices)(Engelstalig) voor een lijst met apparaten die zijn gecertificeerd voor gebruik met Azure IOT hub. Voor het prototypen kunt u apparaten gebruiken zoals een [MXChip IOT-DevKit](https://microsoft.github.io/azure-iot-developer-kit/) of een [Raspberry Pi](https://www.raspberrypi.org/). De Devkit heeft ingebouwde Sens oren voor de Tempe ratuur, druk, vochtigheid en een gyroscope, acceleratie meter en magnetometer. Met de Raspberry Pi kunt u veel verschillende typen sensors aansluiten. 

Micro soft biedt een open-source [apparaat-sdk's](../iot-hub/iot-hub-devguide-sdks.md) die u kunt gebruiken om de apps te bouwen die op uw apparaten worden uitgevoerd. Deze [sdk's vereenvoudigen en versnellen](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/) de ontwikkeling van uw IOT-oplossingen.

## <a name="communication"></a>Communicatie

IoT-apparaten verzenden doorgaans telemetrie van de Sens oren naar back-end-services in de Cloud. Andere soorten communicatie zijn echter mogelijk, zoals een back-end-service die opdrachten naar uw apparaten verzendt. Hier volgen enkele voor beelden van apparaat-naar-Cloud-en Cloud-naar-apparaat-communicatie:

* Een mobiele koel wagen stuurt elke 5 minuten een Tempe ratuur naar een IoT Hub. 

* De back-end-service stuurt een opdracht naar een apparaat om de frequentie te wijzigen waarmee telemetrie wordt verzonden om te helpen bij het vaststellen van een probleem. 

* Een apparaat verzendt waarschuwingen op basis van de waarden die zijn gelezen van de Sens oren. Een apparaat dat bijvoorbeeld een batch-reactor in een chemisch bedrijf bewaken, verzendt een waarschuwing wanneer de Tempe ratuur een bepaalde waarde overschrijdt.

* Uw apparaten verzenden gegevens om weer te geven in een dash board voor weer gave door personeel. Een controle kamer in een verfijning kan bijvoorbeeld de Tempe ratuur, druk en flow volumes in elke pipe tonen, waardoor Opera tors de faciliteit kunnen bewaken. 

De [sdk's van IOT-apparaten](../iot-hub/iot-hub-devguide-sdks.md) en IOT hub ondersteunen algemene [communicatie protocollen](../iot-hub/iot-hub-devguide-protocols.md) zoals http, MQTT en AMQP.

IoT-apparaten hebben verschillende kenmerken ten opzichte van andere clients, zoals browsers en mobiele apps. De apparaat-Sdk's helpen u bij het oplossen van de uitdagingen van het veilig en betrouwbaar zijn van apparaten met uw back-end-service.  IoT-apparaten:

* Zijn vaak ingesloten systemen waarbij geen menselijke operator komt kijken (in tegenstelling tot een telefoon).
* Kunnen worden geïmplementeerd op verafgelegen locaties, waar fysieke toegang kostbaar is.
* Kunnen mogelijk alleen worden bereikt via de back-end van de oplossing.
* Hebben mogelijk een beperkt vermogen en een beperkt aantal verwerkingsbronnen.
* Hebben mogelijk een onstabiele, trage of dure netwerkverbinding.
* Vereisen mogelijk het gebruik van bedrijfseigen, aangepaste of branchespecifieke toepassingsprotocollen.

## <a name="back-end-services"></a>Back-endservices 

In een IoT-oplossing biedt de back-end-service functionaliteit zoals:

* Ontvangen van telemetriegegevens van uw apparaten en bepalen hoe die gegevens moeten worden verwerkt en opgeslagen.
* Analyseren van de telemetriegegevens om inzichten te krijgen, ongeacht of dat in realtime of achteraf wordt gedaan.
* Verzenden van opdrachten vanuit de cloud naar een bepaald apparaat. 
* Apparaten inrichten en bepalen welke apparaten verbinding kunnen maken met uw infra structuur.
* Het beheren van de status van uw apparaten en het controleren van hun activiteiten.
* De geïnstalleerde firmware op uw apparaten beheren.

In een oplossing voor controle op afstand voor een olie pomp, gebruikt de back-end van de Cloud bijvoorbeeld telemetrie van de pompen om afwijkend gedrag te identificeren. Wanneer de back-end-service een anomalie identificeert, kan deze automatisch een opdracht naar het apparaat verzenden om een corrigerende actie te ondernemen. Dit proces genereert een automatische feedbacklus tussen het apparaat en de cloud die de efficiëntie van de oplossing aanzienlijk verhoogt.

## <a name="azure-iot-examples"></a>Azure IoT-voor beelden

Zie technische casestudy's van [micro soft voor IOT](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured)voor praktijk voorbeelden van de manier waarop organisaties Azure IOT gebruiken. 

## <a name="next-steps"></a>Volgende stappen

Zie de [technische casestudy's van Microsoft Azure IoT](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured) voor enkele voorbeelden uit de praktijk en de architectuur die daarvoor is gebruikt.

Zie de [projectcatalogus van de IoT-ontwikkelkit](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) voor enkele voorbeeldprojecten die u kunt uitproberen met een IoT-ontwikkelkit. 

Zie [Azure IOT-Services en-technologieën](iot-services-and-technologies.md)voor een uitgebreidere uitleg van de verschillende services en hoe ze worden gebruikt.

Zie [Microsoft Azure IoT Reference Architecture](https://aka.ms/iotrefarchitecture) (Referentiearchitectuur voor Microsoft Azure IoT) voor een uitgebreide beschrijving van de IoT-architectuur.
