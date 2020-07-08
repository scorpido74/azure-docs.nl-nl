---
title: Gateways voor downstream-apparaten-Azure IoT Edge | Microsoft Docs
description: Gebruik Azure IoT Edge om een transparant, dekkend of proxy gateway-apparaat te maken dat gegevens van meerdere downstream-apparaten naar de Cloud verzendt of lokaal verwerkt.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/25/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 916eeaa60bc054301af039164ce1c14e77ceb91a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "81733515"
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway"></a>Hoe een IoT Edge-apparaat kan worden gebruikt als gateway

Gateways in IoT Edge oplossingen bieden connectiviteit van apparaten en Edge Analytics naar IoT-apparaten die anderszins deze mogelijkheden niet zouden hebben. Azure IoT Edge kan worden gebruikt om te voldoen aan de behoeften van een IoT-gateway, of het nu gaat om verbinding, identiteit of Edge-analyses. Gateway patronen in dit artikel verwijzen alleen naar kenmerken van de verbindings-en apparaat-id van het downstream-apparaat, niet hoe apparaatgegevens worden verwerkt op de gateway.

## <a name="patterns"></a>Patronen

Er zijn drie patronen voor het gebruik van een IoT Edge apparaat als gateway: transparant, protocol omzetting en identiteits omzetting:

* **Transparant** : apparaten die theoretisch verbinding kunnen maken met IOT hub kunnen in plaats daarvan verbinding maken met een gateway apparaat. De downstream-apparaten hebben hun eigen IoT Hub identiteiten en maken gebruik van een van de MQTT-, AMQP-of HTTP-protocollen. De gateway geeft simpelweg communicatie tussen de apparaten en IoT Hub door. Zowel de apparaten als de gebruikers die met hen communiceren via IoT Hub, zijn niet op de hoogte van de communicatie van een gateway. Dit gebrek aan bewustzijn betekent dat de gateway als *transparant*wordt beschouwd. Raadpleeg [een transparante gateway maken](how-to-create-transparent-gateway.md) voor specifieke informatie over het gebruik van een IOT edge apparaat als transparante gateway.
* **Protocol vertaling** : ook wel bekend als een dekkend gateway patroon, apparaten die geen ondersteuning bieden voor MQTT, AMQP of http, kunnen een gateway apparaat gebruiken om gegevens te verzenden naar IOT hub namens hun naam. De gateway begrijpt het protocol dat wordt gebruikt door de downstream-apparaten en is het enige apparaat met een identiteit in IoT Hub. Alle informatie lijkt op het ene apparaat, de gateway. Downstream-apparaten moeten extra identificerende informatie in hun berichten insluiten als Cloud toepassingen de gegevens per apparaat willen analyseren. Daarnaast zijn IoT Hub primitieven, zoals apparaatdubbels en methoden, alleen beschikbaar voor het gateway apparaat, niet voor downstream-apparaten.
* **Identiteits vertalingen** : apparaten die geen verbinding kunnen maken met IOT hub kunnen in plaats daarvan verbinding maken met een gateway apparaat. De gateway biedt IoT Hub identiteits-en protocol omzetting namens de downstream-apparaten. De gateway is slim genoeg om inzicht te krijgen in het protocol dat wordt gebruikt door de downstream-apparaten, de identiteit ervan te leveren en IoT Hub primitieven te vertalen. Downstream-apparaten worden in IoT Hub weer gegeven als apparaten van de eerste klasse met apparaatdubbels en-methoden. Een gebruiker kan communiceren met de apparaten in IoT Hub en is niet op de hoogte van het tussenliggende gateway apparaat.

![Diagram: transparante, protocol-en identiteits gateway-patronen](./media/iot-edge-as-gateway/edge-as-gateway.png)

## <a name="use-cases"></a>Gebruiksvoorbeelden

Alle gateway patronen bieden de volgende voor delen:

* **Analytics aan de rand** : gebruik AI-services lokaal om gegevens te verwerken die afkomstig zijn van downstream-apparaten zonder een telemetrie met een volledige kwaliteit naar de cloud te verzenden. Zoek en reageer lokaal op inzichten en verzend alleen een subset van gegevens naar IoT Hub.
* De isolatie van het **downstream-apparaat** : het gateway apparaat kan alle downstream-apparaten afschermen tegen bloot stelling aan Internet. Het kan zich bevinden tussen een OT netwerk dat geen verbinding heeft en een IT-netwerk dat toegang biedt tot het web.
* **Verbindings multiplexing** : alle apparaten die verbinding maken met IOT hub via een IOT Edge gateway gebruiken dezelfde onderliggende verbinding.
* Uitgaand **verkeer** : het IOT edge apparaat implementeert automatisch exponentiële uitstel als IOT hub het verkeer beperkt, terwijl de berichten lokaal worden bewaard. Dit voor deel is uw oplossing robuust voor pieken in het verkeer.
* **Offline ondersteuning** : het gateway apparaat slaat berichten en dubbele updates op die niet aan IOT hub kunnen worden geleverd.

Een gateway die protocol vertaling ondersteunt, kan ook Edge Analytics, isolatie van apparaten, probleemloze verkeer en offline ondersteuning bieden voor bestaande apparaten en nieuwe apparaten die zijn beperkt. Veel bestaande apparaten produceren gegevens die zakelijke inzichten kunnen ervaren. ze zijn echter niet ontworpen met het oog op Cloud connectiviteit. Met dekkende gateways kunnen deze gegevens worden ontgrendeld en worden gebruikt in een IoT-oplossing.

Een gateway die identiteits vertalingen ondersteunt, biedt de voor delen van protocol vertalingen en biedt bovendien volledige beheer baarheid van downstream-apparaten vanuit de Cloud. Alle apparaten in uw IoT-oplossing worden weer gegeven in IoT Hub, ongeacht het protocol dat ze gebruiken.

## <a name="cheat-sheet"></a>Cheatsheet

Hier volgt een snel Cheat-blad dat IoT Hub primitieven vergelijkt wanneer transparante, dekkende (Protocol) en proxy gateways worden gebruikt.

| &nbsp; | Transparante gateway | Protocol omzetting | Identiteits vertalingen |
|--------|-------------|--------|--------|
| Identiteiten die zijn opgeslagen in het IoT Hub-identiteits register | Identiteit van alle verbonden apparaten | Alleen de identiteit van het gateway apparaat | Identiteit van alle verbonden apparaten |
| Dubbel apparaat | Elk verbonden apparaat heeft een eigen apparaat, twee | Alleen de gateway heeft een apparaat-en module-apparaatdubbels | Elk verbonden apparaat heeft een eigen apparaat, twee |
| Directe methoden en Cloud-naar-apparaat-berichten | De cloud kan elk aangesloten apparaat afzonderlijk adresseren | De cloud kan alleen het gateway apparaat adresseren | De cloud kan elk aangesloten apparaat afzonderlijk adresseren |
| [Beperkings-en quota IoT Hub](../iot-hub/iot-hub-devguide-quotas-throttling.md) | Toep assen op elk apparaat | Toep assen op het gateway apparaat | Toep assen op elk apparaat |

Wanneer u een patroon voor het omzetten van een dekkende gateway (protocol Translation) gebruikt, delen alle apparaten die via die gateway verbinding maken, dezelfde Cloud-naar-apparaat-wachtrij, die Maxi maal 50 berichten kan bevatten. Het volgende is het geval dat het patroon ondoorzichtige gateway alleen moet worden gebruikt wanneer er slechts enkele apparaten zijn die verbinding maken via elke veld Gateway en dat het Cloud-naar-apparaat-verkeer laag is.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het instellen van een transparante gateway:

* [Een IoT Edge-apparaat configureren zodat deze werkt als een transparante gateway](how-to-create-transparent-gateway.md)
* [Een downstream-apparaat verifiëren voor Azure IoT Hub](how-to-authenticate-downstream-device.md)
* [Een downstreamapparaat verbinden met een Azure IoT Edge-gateway](how-to-connect-downstream-device.md)
