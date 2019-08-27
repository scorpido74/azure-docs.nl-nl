---
title: Inleiding tot IoT Plug en Play preview | Microsoft Docs
description: Meer informatie over de preview-versie van IoT Plug en Play. IoT Plug en Play is gebaseerd op een open model taal waarmee IoT-apparaten hun mogelijkheden kunnen declareren. IoT-apparaten presen teren een functionaliteits model voor apparaten, wanneer ze verbinding maken met cloud oplossingen zoals Azure IoT Central of partner toepassingen. De Cloud oplossing kan vervolgens automatisch inzicht krijgen in het apparaat en ermee werken, zonder dat u code hoeft te schrijven.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 06/21/2019
ms.topic: overview
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 56a73449c69010bc97122023f7dcbc9e3a9df154
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2019
ms.locfileid: "70048001"
---
# <a name="what-is-iot-plug-and-play-preview"></a>Wat is IoT Plug en Play preview?

Met IoT Plug en Play Preview kunnen ontwikkel aars van oplossingen apparaten met hun oplossingen integreren zonder Inge sloten code te hoeven schrijven. De kern van IoT Plug en Play is een schema voor het _capaciteits model_ van het apparaat dat de mogelijkheden van apparaten beschrijft. Dit schema is een JSON-document dat is gestructureerd als een reeks interfaces die definities bevatten van:

- _Eigenschappen_ die de status alleen-lezen en lezen/schrijven van een apparaat of andere entiteit vertegenwoordigen. Een serie nummer van een apparaat kan bijvoorbeeld een alleen-lezen eigenschap zijn en een doel temperatuur op een thermo staat kan een lezen/schrijven-eigenschap zijn.
- Telemetrie die de gegevens is die door een apparaat worden verzonden, ongeacht of de gegevens een gewone stroom van sensor leesingen, incidentele fout of informatie bericht zijn.
- _Opdrachten_ die een functie of bewerking beschrijven die op een apparaat kan worden uitgevoerd. Een opdracht kan bijvoorbeeld een gateway opnieuw opstarten of een foto nemen met een externe camera.

U kunt interfaces hergebruiken over hulp modellen voor apparaten om samen werking eenvoudiger te maken en de ontwikkeling te versnellen.

Om IoT Plug en Play naadloos te laten werken met [Azure Digital apparaatdubbels](../digital-twins/about-digital-twins.md), wordt het IOT Plug en Play-schema gedefinieerd met behulp van de [Digital-taal voor dubbele definitie (DTDL)](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL). IoT Plug en Play en de DTDL zijn geopend voor de community en micro soft is van de samen werking met klanten, partners en de branche. Beide zijn gebaseerd op open W3C-standaarden, zoals JSON-LD en RDF, waarmee u eenvoudiger kunt aannemen tussen services en hulp middelen. Daarnaast zijn er geen extra kosten verbonden aan het gebruik van IoT Plug en Play en DTDL. De standaard tarieven voor [azure IOT hub](../iot-hub/about-iot-hub.md), [Azure IOT Central](../iot-central/overview-iot-central.md)en andere Azure-Services blijven hetzelfde.

Oplossingen op basis van IoT Hub of IoT Central kunnen profiteren van IoT Plug en Play.

Dit artikel geeft een overzicht van:

- De typische rollen die zijn gekoppeld aan een project dat IoT-Plug en Play gebruikt.
- IoT Plug en Play-apparaten gebruiken in uw toepassing.
- Het ontwikkelen van een IoT-Device-toepassing die ondersteuning biedt voor IoT Plug en Play.
- Een IoT Plug en Play-apparaat certificeren en publiceren naar de certificerings instantie [voor IOT-apparaten](https://catalog.azureiotsolutions.com/).

## <a name="user-roles"></a>Gebruikersrollen

IoT Plug en Play is handig voor twee typen ontwikkel aars:

- Een _oplossings ontwikkelaar_ is verantwoordelijk voor het ontwikkelen van een IOT-oplossing met Azure IOT en andere Azure-resources en voor het identificeren van IOT-apparaten om te integreren.
- Een _ontwikkelaar_ van het apparaat maakt de code die wordt uitgevoerd op een apparaat dat is verbonden met uw oplossing.

## <a name="use-iot-plug-and-play-devices"></a>IoT Plug en Play-apparaten gebruiken

Als oplossings ontwikkelaar kunt u een IoT-oplossing in de Cloud ontwikkelen die gebruikmaakt van IoT Plug en Play-apparaten. U kunt een van de volgende Azure-Services gebruiken:

- [IOT Central](../iot-central/overview-iot-central.md) -een volledig beheerde IOT-software-as-a-service-oplossing waarmee u eenvoudig producten kunt maken die de fysieke en digitale werelden verbinden.
- [IOT hub](../iot-hub/about-iot-hub.md) : een beheerde Cloud service die fungeert als een Message hub voor beveiligde, bidirectionele communicatie tussen uw IOT-toepassing en uw apparaten.

U vindt IoT Plug en Play-apparaten met behulp van de Azure Certified voor IoT-apparaat Catalog. Elk IoT Plug en Play-apparaat in de catalogus is gevalideerd en heeft een functionaliteits model voor apparaten. Bekijk het mogelijkheidsprofiel om inzicht te krijgen in de functionaliteit van het apparaat of gebruik het voor het simuleren van het apparaat in azure IoT Central.

Wanneer u verbinding maakt met een IoT Plug en Play-apparaat, kunt u het hulp model van het apparaat weer geven, de interfaces die zijn opgenomen in het model en de telemetrie, eigenschappen en opdrachten die in deze interfaces zijn gedefinieerd.

## <a name="develop-an-iot-device-application"></a>Een IoT Device-toepassing ontwikkelen

Als ontwikkelaar van apparaten kunt u een IoT-hardware-product ontwikkelen dat IoT-Plug en Play ondersteunt. Het proces bestaat uit twee belang rijke stappen:

1. Definieer het functionaliteits model en de interfaces van het apparaat. U maakt een set JSON-bestanden die de mogelijkheden van uw apparaat declareren met de [DTDL](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL). Een mogelijkheidsprofiel beschrijft een volledige entiteit, zoals een fysiek product, en definieert de set interfaces die door die entiteit worden geïmplementeerd. Interfaces zijn gedeelde contracten die een unieke identificatie vormen van de telemetrie, eigenschappen en opdrachten die door een apparaat worden ondersteund. Interfaces kunnen opnieuw worden gebruikt voor verschillende hulp modellen van apparaten.

1. De software of firmware van het apparaat ontwerpen die de mogelijkheden implementeert die zijn gedeclareerd in het mogelijkheidsprofiel en de interfaces van het apparaat. De Azure IoT SDK bevat Api's voor het implementeren van Capability-modellen.

De [IOT Device Workbench voor VS code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) -extensie biedt veel functies om u te helpen. Als ontwikkel aars van apparaten kunt u bijvoorbeeld de uitbrei ding gebruiken om een skelet C-project te genereren op basis van een bekwaamheids model. U kunt echter een IDE gebruiken om hulp modellen voor apparaten te ontwerpen en implementeren.

## <a name="certify-an-iot-plug-and-play-device"></a>Een IoT Plug en Play-apparaat certificeren

Als ontwikkelaar van apparaten kunt u IoT-hardwareproducten verzenden voor certificering. U kunt een gecertificeerd apparaat publiceren in de catalogus met gecertificeerde voor IoT-apparaten. De stappen voor het certificerings proces zijn:

- Word lid van de [Microsoft Partner Network](https://partner.microsoft.com).
- Onboarding naar de Certified voor Azure IoT-Portal.
- Een IoT Plug en Play-mogelijkheidsprofiel en marketing gegevens verzenden om een nieuwe apparaat-record te maken.
- Automatische set validatie tests voor het apparaat door geven.
- Publiceer naar de catalogus met gecertificeerde voor IoT-apparaten.

## <a name="regional-availability"></a>Regionale beschikbaarheid
Tijdens de open bare preview is IoT Plug en Play beschikbaar in de regio's Europa-noord, centraal, VS en Japan Oost. Zorg ervoor dat u uw hub in een van deze regio's maakt.

## <a name="message-quotas-in-iot-hub"></a>Bericht quota's in IoT Hub
Tijdens de open bare preview verzenden IoT Plug en Play-apparaten afzonderlijke berichten per interface, waardoor het aantal berichten dat kan worden meegeteld voor uw [bericht quotum](../iot-hub/iot-hub-devguide-quotas-throttling.md), kan toenemen.

## <a name="next-steps"></a>Volgende stappen

Nu u een overzicht van IoT Plug en Play hebt, is de voorgestelde volgende stap het uitproberen van een van de Quick starts:

- [Een mogelijkheidsprofiel gebruiken om een IoT Plug en Play-apparaat te maken](./quickstart-create-pnp-device.md)
- [Een apparaat verbinden met IoT Hub](./quickstart-connect-pnp-device.md)
- [Verbinding maken met een apparaat in uw oplossing](./quickstart-connect-pnp-device-solution.md)
