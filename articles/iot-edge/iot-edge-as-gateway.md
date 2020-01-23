---
title: 'Gateways voor downstream-apparaten: Azure IoT Edge | Microsoft Docs'
description: Gebruik Azure IoT Edge te maken van een transparante, ondoorzichtig of proxy gateway-apparaat die gegevens uit meerdere downstream apparaten verzendt naar de cloud of lokaal verwerkt.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/25/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: cbca0c2509e74a7debf5ba26b361c79b9b208f08
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76547112"
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway"></a>Hoe een IoT Edge-apparaat kan worden gebruikt als gateway

Gateways in IoT Edge oplossingen bieden connectiviteit van apparaten en Edge Analytics naar IoT-apparaten die anderszins deze mogelijkheden niet zouden hebben. Azure IoT Edge kan worden gebruikt om te voldoen aan de behoeften van een IoT-gateway, of het nu gaat om verbinding, identiteit of Edge-analyses. Gateway-patronen in dit artikel alleen verwijzen naar eigenschappen van downstream apparaatconnectiviteit en apparaat-id niet hoe apparaatgegevens worden verwerkt op de gateway.

## <a name="patterns"></a>Patronen

Er zijn drie patronen voor het gebruik van een IoT Edge-apparaat als gateway: transparant protocol vertaling en identiteit vertaling:

* **Transparante** -apparaten die in theorie verbinding met IoT Hub maken kunnen kunnen verbinding maken met een gateway-apparaat in plaats daarvan. De downstream-apparaten hebben hun eigen IoT Hub-identiteiten en gebruikmaakt van de protocollen MQTT-, AMQP- of HTTP. De gateway wordt gewoon doorgegeven voor communicatie tussen de apparaten en IoT-Hub. Zowel de apparaten als de gebruikers die met hen communiceren via IoT Hub, zijn niet op de hoogte van de communicatie van een gateway. Dit gebrek aan bewustzijn betekent dat de gateway als *transparant*wordt beschouwd. Raadpleeg [een transparante gateway maken](how-to-create-transparent-gateway.md) voor meer informatie over het gebruik van een IoT Edge-apparaat als een transparante gateway.
* **NAT-protocol** – ook wel bekend als een ondoorzichtige gateway-patroon, kunnen apparaten die geen ondersteuning voor MQTT-, AMQP- of HTTP een gateway-apparaat om gegevens te verzenden naar IoT Hub namens hen gebruiken. De gateway begrijpt het protocol dat wordt gebruikt door de downstream-apparaten en is het enige apparaat met een identiteit in IoT Hub. Alle informatie lijkt erop dat het afkomstig is van een apparaat, de gateway. Downstream-apparaten moeten extra identificatiegegevens insluiten in hun berichten als cloud-toepassingen wilt analyseren van de gegevens op basis van per apparaat. Bovendien IoT-Hub primitieven zoals dubbels en methoden zijn alleen beschikbaar voor de gateway-apparaat, niet downstream-apparaten.
* **Identiteit vertaling** -apparaten die geen verbinding met IoT Hub maken kunnen verbinding maken met een gateway-apparaat, in plaats daarvan. De gateway biedt IoT Hub identiteits- en protocol vertaling namens de downstream-apparaten. De gateway is slimme begrijpt het protocol dat wordt gebruikt door de downstream-apparaten en vertalen van IoT-Hub primitieven identiteit bieden. Downstream-apparaten worden weergegeven in IoT Hub als eersteklas-apparaten met dubbels en methoden. Een gebruiker kan communiceren met de apparaten in IoT Hub en is geen informatie over het tussenliggende gateway-apparaat.

![Diagram - transparante, het protocol en patronen voor identiteit-gateway](./media/iot-edge-as-gateway/edge-as-gateway.png)

## <a name="use-cases"></a>Use cases

Alle gateway patronen bieden de volgende voordelen:

* **Analytics aan de rand** : gebruik AI-services lokaal om gegevens te verwerken die afkomstig zijn van downstream-apparaten zonder een telemetrie met een volledige kwaliteit naar de cloud te verzenden. Zoek en reageren op inzichten lokaal en alleen een subset van gegevens verzenden naar IoT Hub.
* **Downstream apparaat isolatie** – het gateway-apparaat kunt beschermen alle downstream-apparaten van blootstelling aan internet. Deze kan blijven tussen een OT netwerk die niet is verbonden en een IT-netwerk dat toegang tot het web biedt.
* **Verbinding multiplexing** - alle apparaten die verbinding maken met IoT Hub via een IoT Edge-gateway gebruiken de onderliggende verbinding.
* **Aanpassing van verkeer** -de IoT Edge-apparaat wordt automatisch exponentieel uitstel implementeren als IoT Hub-verkeer, tijdens het opslaan van de berichten lokaal beperkt. Dit voordeel is uw oplossing flexibele om pieken in verkeer.
* **Offline ondersteuning** : het gateway apparaat slaat berichten en dubbele updates op die niet aan IOT hub kunnen worden geleverd.

Een gateway die NAT-protocol kan ook uitvoeren randanalyses, apparaat isolatie, verkeer vloeiend maken en offline ondersteuning aan bestaande apparaten en nieuwe apparaten die beperkte resource zijn. Veel bestaande apparaten produceren gegevens die bedrijfsinzichten aansturen kunnen; ze zijn echter niet ontworpen met cloud-connectiviteit in gedachten. Met dekkende gateways kunnen deze gegevens worden ontgrendeld en worden gebruikt in een IoT-oplossing.

Een gateway die identiteit vertaling biedt de voordelen van protocollen vertalen en Daarnaast kunt u volledige beheermogelijkheden van downstream apparaten vanuit de cloud. Alle apparaten in uw IoT-oplossing weergegeven in IoT Hub, ongeacht het protocol dat ze gebruiken.

## <a name="cheat-sheet"></a>Overzichtskaart

Hier volgt een snel referentieoverzicht waarmee IoT-Hub primitieven vergeleken bij het gebruik van transparant ondoorzichtig (protocol) en proxy-gateways.

| &nbsp; | Transparante gateway | Protocollen vertalen | Identiteit vertaling |
|--------|-------------|--------|--------|
| Identiteiten die zijn opgeslagen in het id-register van IoT Hub | Identiteiten voor alle verbonden apparaten | Alleen de identiteit van de gateway-apparaat | Identiteiten voor alle verbonden apparaten |
| Dubbel apparaat | Elk verbonden apparaat heeft een eigen apparaatdubbel | Alleen de gateway is een dubbele apparaat- en -module | Elk verbonden apparaat heeft een eigen apparaatdubbel |
| Directe methoden en cloud-naar-apparaat-berichten | De cloud kan voorzien in elk verbonden apparaat afzonderlijk | Het gateway-apparaat kan alleen voorzien in de cloud | De cloud kan voorzien in elk verbonden apparaat afzonderlijk |
| [Vertragingen in IoT Hub en quota](../iot-hub/iot-hub-devguide-quotas-throttling.md) | Van toepassing op elk apparaat | Van toepassing op de gateway-apparaat | Van toepassing op elk apparaat |

Wanneer u een patroon ondoorzichtige gateway (protocollen vertalen) gebruikt, delen alle apparaten die verbinding te maken via de gateway die de dezelfde wachtrij van het cloud-naar-apparaat, die maximaal 50 berichten kan bevatten. Betekent dit dat het patroon ondoorzichtige gateway moet worden gebruikt wanneer enkele apparaten zijn verbonden via de veldgateway voor elk en hun cloud-naar-apparaat verkeer laag is.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het instellen van een transparante gateway:

* [Een IoT Edge-apparaat configureren zodat deze werkt als een transparante gateway](how-to-create-transparent-gateway.md)
* [Een downstreamapparaat verifiëren voor Azure IoT Hub](how-to-authenticate-downstream-device.md)
* [Een downstreamapparaat verbinden met een Azure IoT Edge-gateway](how-to-connect-downstream-device.md)
