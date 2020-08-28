---
title: Gateways voor downstream-apparaten-Azure IoT Edge | Microsoft Docs
description: Gebruik Azure IoT Edge om een transparant, dekkend of proxy gateway-apparaat te maken dat gegevens van meerdere downstream-apparaten naar de Cloud verzendt of lokaal verwerkt.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/21/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 0589779de2ddb0bc75dde3b57d6444634b879f86
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2020
ms.locfileid: "89017019"
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway"></a>Hoe een IoT Edge-apparaat kan worden gebruikt als gateway

Gateways in IoT Edge oplossingen bieden connectiviteit van apparaten en Edge Analytics naar IoT-apparaten die anderszins deze mogelijkheden niet zouden hebben. Azure IoT Edge kan worden gebruikt om te voldoen aan de behoeften van een IoT-gateway, of het nu gaat om verbinding, identiteit of Edge-analyses. Gateway patronen in dit artikel verwijzen alleen naar kenmerken van de verbindings-en apparaat-id van het downstream-apparaat, niet hoe apparaatgegevens worden verwerkt op de gateway.

## <a name="patterns"></a>Patronen

Er zijn drie patronen voor het gebruik van een IoT Edge apparaat als gateway: transparant, protocol omzetting en identiteits vertalingen.

Een belang rijk verschil tussen de patronen is dat een transparante gateway berichten tussen de downstream-apparaten en IoT Hub doorgeeft zonder dat er aanvullende verwerking nodig is. Protocol omzetting en identiteits omzetting vereisen echter verwerking op de gateway om communicatie mogelijk te maken.

Elke gateway kan gebruikmaken van IoT Edge-modules voor het uitvoeren van analyses of vooraf verwerkingen aan de rand voordat berichten van downstream-apparaten worden door gegeven aan IoT Hub.

![Diagram: transparante, protocol-en identiteits gateway-patronen](./media/iot-edge-as-gateway/edge-as-gateway.png)

### <a name="transparent-pattern"></a>Transparant patroon

In een *transparant* gateway patroon kunnen apparaten die theoretisch verbinding kunnen maken met IOT hub in plaats daarvan verbinding maken met een gateway apparaat. De downstreamapparaten hebben hun eigen IoT Hub-identiteiten en maken gebruik van een van de MQTT-, AMQP- of HTTP-protocollen. Via de gateway wordt simpelweg communicatie tussen de apparaten en IoT Hub doorgegeven. Zowel de apparaten als de gebruikers die met hen communiceren via IoT Hub, zijn niet op de hoogte van de communicatie van een gateway. Dit gebrek aan bewustzijn betekent dat de gateway als *transparant*wordt beschouwd.

De IoT Edge runtime bevat transparante gateway mogelijkheden. Zie [een IOT edge apparaat configureren om te fungeren als transparante gateway](how-to-create-transparent-gateway.md)voor meer informatie.

### <a name="protocol-translation-pattern"></a>Patroon voor Protocol conversie

Een *protocol vertaling* gateway wordt ook wel een *ondoorzichtige* gateway genoemd, in tegens telling tot het transparante gateway patroon. In dit patroon kunnen apparaten die geen ondersteuning bieden voor MQTT, AMQP of HTTP een gateway apparaat gebruiken voor het verzenden van gegevens naar IoT Hub namens hun naam. De gateway begrijpt het protocol dat wordt gebruikt door de downstreamapparaten en is het enige apparaat met een identiteit in IoT Hub. Alle informatie lijkt op het ene apparaat, de gateway. Downstreamapparaten moeten extra identificatiegegevens in hun berichten insluiten als cloudtoepassingen de gegevens per apparaat willen analyseren. Daarnaast zijn IoT Hub-primitieven, zoals apparaatdubbels en methoden, alleen beschikbaar voor het gatewayapparaat, niet voor downstreamapparaten.

De IoT Edge-runtime bevat geen mogelijkheden voor protocol omzetting. Voor dit patroon zijn aangepaste of modules van derden vereist die vaak specifiek zijn voor de hardware en het gebruikte protocol. [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) bevat verschillende modules voor protocol omzetting waaruit u kunt kiezen.

### <a name="identity-translation-pattern"></a>Patroon voor identiteits conversie

In een gateway patroon voor *identiteits vertalingen* kunnen apparaten die geen verbinding kunnen maken met IOT hub verbinding maken met een gateway apparaat. De gateway verstrekt de identiteits- en protocolomzetting voor IoT Hub namens de downstreamapparaten. De gateway is slim genoeg om het protocol te begrijpen dat wordt gebruikt door de downstreamapparaten, identiteit te leveren en IoT Hub-primitieven om te zetten. Downstreamapparaten worden in IoT Hub weergegeven als eersteklasapparaten met apparaatdubbels en methoden. Een gebruiker kan communiceren met de apparaten in IoT Hub en is zich niet bewust van het tussenliggende gatewayapparaat.

De IoT Edge runtime bevat geen mogelijkheden voor het converteren van de identiteit. Voor dit patroon zijn aangepaste of modules van derden vereist die vaak specifiek zijn voor de hardware en het gebruikte protocol. Zie [Azure IOT Edge LoRaWAN Starter Kit](https://github.com/Azure/iotedge-lorawan-starterkit)voor een voor beeld dat gebruikmaakt van het Vertaal patroon voor identiteiten.

## <a name="use-cases"></a>Gebruiksvoorbeelden

Alle gateway patronen bieden de volgende voor delen:

* **Analytics aan de rand** : gebruik AI-services lokaal om gegevens te verwerken die afkomstig zijn van downstream-apparaten zonder een telemetrie met een volledige kwaliteit naar de cloud te verzenden. Zoek en reageer lokaal op inzichten en verzend alleen een subset van gegevens naar IoT Hub.
* De isolatie van het **downstream-apparaat** : het gateway apparaat kan alle downstream-apparaten afschermen tegen bloot stelling aan Internet. Het kan zich bevinden tussen een OT netwerk dat geen verbinding heeft en een IT-netwerk dat toegang biedt tot het web.
* **Verbindings multiplexing** : alle apparaten die verbinding maken met IOT hub via een IOT Edge gateway gebruiken dezelfde onderliggende verbinding.
* Uitgaand **verkeer** : het IOT edge apparaat implementeert automatisch exponentiële uitstel als IOT hub het verkeer beperkt, terwijl de berichten lokaal worden bewaard. Dit voor deel is uw oplossing robuust voor pieken in het verkeer.
* **Offline ondersteuning** : het gateway apparaat slaat berichten en dubbele updates op die niet aan IOT hub kunnen worden geleverd.

Een gateway die de protocol vertaling ondersteunt, kan bestaande apparaten en nieuwe apparaten ondersteunen die zijn beperkt. Veel bestaande apparaten produceren gegevens die zakelijke inzichten kunnen ervaren. ze zijn echter niet ontworpen met het oog op Cloud connectiviteit. Met dekkende gateways kunnen deze gegevens worden ontgrendeld en worden gebruikt in een IoT-oplossing.

Een gateway die identiteits vertalingen ondersteunt, biedt de voor delen van protocol vertalingen en biedt bovendien volledige beheer baarheid van downstream-apparaten vanuit de Cloud. Alle apparaten in uw IoT-oplossing worden weer gegeven in IoT Hub, ongeacht het protocol dat ze gebruiken.

## <a name="cheat-sheet"></a>Cheatsheet

Hier volgt een snel Cheat-blad dat IoT Hub primitieven vergelijkt wanneer transparante, dekkende (Protocol) en proxy gateways worden gebruikt.

| Primitieve | Transparante gateway | Protocol omzetting | Identiteits vertalingen |
|--------|-------------|--------|--------|
| Identiteiten die zijn opgeslagen in het IoT Hub-identiteits register | Identiteit van alle verbonden apparaten | Alleen de identiteit van het gateway apparaat | Identiteit van alle verbonden apparaten |
| Dubbel apparaat | Elk verbonden apparaat heeft een eigen apparaat, twee | Alleen de gateway heeft een apparaat-en module-apparaatdubbels | Elk verbonden apparaat heeft een eigen apparaat, twee |
| Directe methoden en Cloud-naar-apparaat-berichten | De cloud kan elk aangesloten apparaat afzonderlijk adresseren | De cloud kan alleen het gateway apparaat adresseren | De cloud kan elk aangesloten apparaat afzonderlijk adresseren |
| [Beperkings-en quota IoT Hub](../iot-hub/iot-hub-devguide-quotas-throttling.md) | Toep assen op elk apparaat | Toep assen op het gateway apparaat | Toep assen op elk apparaat |

Wanneer u een patroon voor het omzetten van een dekkende gateway (protocol Translation) gebruikt, delen alle apparaten die via die gateway verbinding maken, dezelfde Cloud-naar-apparaat-wachtrij, die Maxi maal 50 berichten kan bevatten. Het volgende is het geval dat het patroon ondoorzichtige gateway alleen moet worden gebruikt wanneer er slechts enkele apparaten zijn die verbinding maken via elke veld Gateway en dat het Cloud-naar-apparaat-verkeer laag is.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de drie stappen voor het instellen van een transparante gateway:

* [Een IoT Edge-apparaat configureren zodat deze werkt als een transparante gateway](how-to-create-transparent-gateway.md)
* [Een downstream-apparaat verifiëren voor Azure IoT Hub](how-to-authenticate-downstream-device.md)
* [Een downstreamapparaat verbinden met een Azure IoT Edge-gateway](how-to-connect-downstream-device.md)
