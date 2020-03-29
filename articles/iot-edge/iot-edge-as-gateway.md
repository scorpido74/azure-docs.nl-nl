---
title: Gateways voor downstream-apparaten - Azure IoT Edge | Microsoft Documenten
description: Gebruik Azure IoT Edge om een transparant, ondoorzichtig of proxygateway-apparaat te maken dat gegevens van meerdere downstream-apparaten naar de cloud verzendt of lokaal verwerkt.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/25/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: cbca0c2509e74a7debf5ba26b361c79b9b208f08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76547112"
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway"></a>Hoe een IoT Edge-apparaat kan worden gebruikt als gateway

Gateways in IoT Edge-oplossingen bieden apparaatconnectiviteit en edge-analyses naar IoT-apparaten die anders niet over die mogelijkheden zouden beschikken. Azure IoT Edge kan worden gebruikt om te voldoen aan elke behoefte aan een IoT-gateway, of deze nu gerelateerd is aan connectiviteit, identiteit of edge analytics. Gatewaypatronen in dit artikel verwijzen alleen naar kenmerken van downstream-apparaatconnectiviteit en apparaatidentiteit, niet naar de manier waarop apparaatgegevens op de gateway worden verwerkt.

## <a name="patterns"></a>Patronen

Er zijn drie patronen voor het gebruik van een IoT Edge-apparaat als gateway: transparant, protocolvertaling en identiteitsvertaling:

* **Transparant** : apparaten die in theorie verbinding kunnen maken met IoT Hub, kunnen in plaats daarvan verbinding maken met een gateway-apparaat. De downstream-apparaten hebben hun eigen IoT Hub-identiteiten en gebruiken een van de MQTT-, AMQP- of HTTP-protocollen. De gateway passeert eenvoudig de communicatie tussen de apparaten en IoT Hub. Zowel de apparaten als de gebruikers die met hen communiceren via IoT Hub zijn zich er niet van bewust dat een gateway hun communicatie bemiddelt. Dit gebrek aan bewustzijn betekent dat de gateway wordt beschouwd als *transparant*. Raadpleeg [Een transparante gateway maken](how-to-create-transparent-gateway.md) voor details over het gebruik van een IoT Edge-apparaat als transparante gateway.
* **Protocolvertaling** – Ook wel een ondoorzichtig gatewaypatroon genoemd, kunnen apparaten die MQTT, AMQP of HTTP niet ondersteunen, een gateway-apparaat gebruiken om namens hen gegevens naar IoT Hub te verzenden. De gateway begrijpt het protocol dat door de downstream-apparaten wordt gebruikt en is het enige apparaat dat een identiteit heeft in IoT Hub. Alle informatie lijkt te komen van een apparaat, de gateway. Downstream-apparaten moeten aanvullende identificerende informatie in hun berichten insluiten als cloudtoepassingen de gegevens per apparaat willen analyseren. Bovendien zijn IoT Hub-primitieven zoals tweelingen en methoden alleen beschikbaar voor het gateway-apparaat, niet voor downstream-apparaten.
* **Identiteitsvertaling** - Apparaten die geen verbinding kunnen maken met IoT Hub, kunnen in plaats daarvan verbinding maken met een gateway-apparaat. De gateway biedt IoT Hub-identiteit en protocolvertaling namens de downstream-apparaten. De gateway is slim genoeg om het protocol te begrijpen dat door de downstream-apparaten wordt gebruikt, ze identiteit te bieden en IoT Hub-primitieven te vertalen. Downstream-apparaten worden in IoT Hub weergegeven als eersteklas apparaten met tweelingen en methoden. Een gebruiker kan communiceren met de apparaten in IoT Hub en is zich niet bewust van het tussenliggende gateway-apparaat.

![Diagram - Transparante, protocol- en identiteitsgatewaypatronen](./media/iot-edge-as-gateway/edge-as-gateway.png)

## <a name="use-cases"></a>Gebruiksvoorbeelden

Alle gatewaypatronen bieden de volgende voordelen:

* **Analytics aan de rand** : gebruik AI-services lokaal om gegevens van downstream-apparaten te verwerken zonder volledige telemetrie naar de cloud te verzenden. Vind en reageer op inzichten lokaal en stuur alleen een subset van gegevens naar IoT Hub.
* **Downstream-apparaatisolatie** – Het gateway-apparaat kan alle downstream-apparaten beschermen tegen blootstelling aan het internet. Het kan zitten tussen een OT-netwerk dat geen connectiviteit heeft en een IT-netwerk dat toegang biedt tot het web.
* **Verbindingsmultiplexing** - Alle apparaten die verbinding maken met IoT Hub via een IoT Edge-gateway gebruiken dezelfde onderliggende verbinding.
* **Verkeersvloeiendmaken** - Het IoT Edge-apparaat implementeert automatisch exponentiële back-off als IoT Hub het verkeer beperkt, terwijl de berichten lokaal worden gehandhaafd. Dit voordeel maakt uw oplossing bestand tegen pieken in het verkeer.
* **Offline ondersteuning** : het gatewayapparaat slaat berichten en twee updates op die niet aan IoT Hub kunnen worden geleverd.

Een gateway die protocolvertaling uitvoert, kan ook edge-analyses, apparaatisolatie, verkeersvloeiendheid en offlineondersteuning uitvoeren naar bestaande apparaten en nieuwe apparaten die zijn beperkt tot resources. Veel bestaande apparaten produceren gegevens die zakelijke inzichten kunnen bieden; ze zijn echter niet ontworpen met cloudconnectiviteit in het achterhoofd. Met ondoorzichtige gateways kunnen deze gegevens worden ontgrendeld en gebruikt in een IoT-oplossing.

Een gateway die identiteitsvertaling doet, biedt de voordelen van protocolvertaling en maakt bovendien volledige beheerbaarheid van downstream-apparaten vanuit de cloud mogelijk. Alle apparaten in uw IoT-oplossing worden weergegeven in IoT Hub, ongeacht het protocol dat ze gebruiken.

## <a name="cheat-sheet"></a>Cheatsheet

Hier is een snel spiekbriefje dat IoT Hub-primitieven vergelijkt bij het gebruik van transparante, ondoorzichtige (protocol) en proxygateways.

| &nbsp; | Transparante gateway | Protocolvertaling | Identiteitsvertaling |
|--------|-------------|--------|--------|
| Identiteiten die zijn opgeslagen in het identiteitsregister van de IoT Hub | Identiteiten van alle verbonden apparaten | Alleen de identiteit van het gateway-apparaat | Identiteiten van alle verbonden apparaten |
| Dubbel apparaat | Elk aangesloten apparaat heeft zijn eigen apparaat | Alleen de gateway heeft een apparaat en module tweelingen | Elk aangesloten apparaat heeft zijn eigen apparaat |
| Directe methoden en cloud-to-device-berichten | De cloud kan elk verbonden apparaat afzonderlijk aanpakken | De cloud kan alleen het gateway-apparaat aanspreken | De cloud kan elk verbonden apparaat afzonderlijk aanpakken |
| [IoT Hub-gashendels en quota](../iot-hub/iot-hub-devguide-quotas-throttling.md) | Toepassen op elk apparaat | Toepassen op het gateway-apparaat | Toepassen op elk apparaat |

Wanneer u een ondoorzichtig gatewaypatroon (protocolvertaling) gebruikt, delen alle apparaten die verbinding maken via die gateway dezelfde cloud-to-device-wachtrij, die maximaal 50 berichten kan bevatten. Hieruit volgt dat het ondoorzichtige gatewaypatroon alleen mag worden gebruikt wanneer er weinig apparaten verbinding maken via elke veldgateway en hun verkeer tussen de cloud en het apparaat laag is.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het instellen van een transparante gateway:

* [Een IoT Edge-apparaat configureren zodat deze werkt als een transparante gateway](how-to-create-transparent-gateway.md)
* [Een downstream-apparaat verifiëren voor Azure IoT Hub](how-to-authenticate-downstream-device.md)
* [Een downstreamapparaat verbinden met een Azure IoT Edge-gateway](how-to-connect-downstream-device.md)
