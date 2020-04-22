---
title: Inleiding tot IoT Plug and Play Preview | Microsoft Documenten
description: Meer informatie over IoT Plug and Play Preview. IoT Plug and Play is gebaseerd op een open modelleringstaal waarmee IoT-apparaten hun mogelijkheden kunnen declareren. IoT-apparaten presenteren die verklaring, een apparaatcapaciteitsmodel genoemd, wanneer ze verbinding maken met cloudoplossingen zoals Azure IoT Central of partnertoepassingen. De cloudoplossing kan het apparaat vervolgens automatisch begrijpen en ermee communiceren , allemaal zonder code te schrijven.
author: Philmea
ms.author: philmea
ms.date: 12/23/2019
ms.topic: overview
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 12f3febac2c5c8ed01b9b156a64dc77f6ed0704f
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770439"
---
# <a name="what-is-iot-plug-and-play-preview"></a>Wat is IoT Plug and Play Preview?

IoT Plug and Play Preview stelt ontwikkelaars van oplossingen in staat om apparaten te integreren met hun oplossingen zonder ingesloten code te schrijven. De kern van IoT Plug and Play is een modelschema voor _apparaatmogelijkheden_ dat apparaatmogelijkheden beschrijft. Dit schema is een JSON-document dat is gestructureerd als een set interfaces met definities van:

- _Eigenschappen_ die de alleen-lezen en lees-/schrijfstatus van een apparaat of andere entiteit vertegenwoordigen. Een apparaatserienummer kan bijvoorbeeld een alleen-lezen eigenschap zijn en een doeltemperatuur op een thermostaat kan een eigenschap lezen/schrijven zijn.
- _Telemetrie_ dat is de gegevens die door een apparaat worden uitgezonden, of de gegevens nu een regelmatige stroom van sensormetingen, een incidentele fout of informatiebericht zijn.
- _Opdrachten_ die een functie of bewerking beschrijven die op een apparaat kan worden uitgevoerd. Een opdracht kan bijvoorbeeld een gateway opnieuw opstarten of een foto maken met een externe camera.

U interfaces opnieuw gebruiken op verschillende apparaatcapaciteitsmodellen om samenwerking eenvoudiger te maken en de ontwikkeling te versnellen.

Om IoT Plug and Play naadloos te laten werken met [Azure Digital Twins,](../digital-twins/about-digital-twins.md)wordt het IoT Plug and Play-schema gedefinieerd met behulp van de [Digital Twin Definition Language (DTDL).](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL) IoT Plug and Play en de DTDL staan open voor de community en Microsoft verwelkomt samenwerking met klanten, partners en de industrie. Beide zijn gebaseerd op open W3C-standaarden zoals JSON-LD en RDF, die het mogelijk maken om de adoptie tussen services en gereedschappen te vergemakkelijken. Bovendien zijn er geen extra kosten voor het gebruik van IoT Plug and Play en DTDL. Standaardsnelheden voor [Azure IoT Hub,](../iot-hub/about-iot-hub.md) [Azure IoT Central](../iot-central/core/overview-iot-central.md)en andere Azure-services blijven hetzelfde.

Oplossingen die zijn gebouwd op IoT Hub of IoT Central kunnen profiteren van IoT Plug and Play.

In dit artikel wordt beschreven:

- De typische rollen die zijn gekoppeld aan een project dat IoT Plug and Play gebruikt.
- IoT Plug and Play-apparaten gebruiken in uw toepassing.
- Hoe ontwikkel je een IoT-apparaatapplicatie die IoT Plug and Play ondersteunt.
- Een IoT Plug and Play-apparaat certificeren en publiceren in de [catalogus van certified for IoT-apparaten.](https://catalog.azureiotsolutions.com/)

## <a name="user-roles"></a>Gebruikersrollen

IoT Plug and Play is handig voor twee soorten ontwikkelaars:

- Een _oplossingsontwikkelaar_ is verantwoordelijk voor het ontwikkelen van een IoT-oplossing met Azure IoT en andere Azure-bronnen en voor het identificeren van IoT-apparaten om te integreren.
- Een _apparaatontwikkelaar_ maakt de code die wordt uitgevoerd op een apparaat dat is verbonden met uw oplossing.

## <a name="use-iot-plug-and-play-devices"></a>IoT-stekker- en afspeelapparaten gebruiken

Als ontwikkelaar van oplossingen u een door de cloud gehoste IoT-oplossing ontwikkelen die gebruikmaakt van IoT Plug and Play-apparaten. U een van de volgende Azure-services gebruiken:

- [IoT Central](../iot-central/core/overview-iot-central.md) - een volledig beheerde IoT-software-as-a-service-oplossing die het eenvoudig maakt om producten te maken die de fysieke en digitale werelden verbinden.
- [IoT Hub](../iot-hub/about-iot-hub.md) - een beheerde cloudservice, die fungeert als een berichtenhub voor veilige, bidirectionele communicatie tussen uw IoT-toepassing en uw apparaten.

U IoT Plug and Play-apparaten vinden via de Azure Certified for IoT-apparaatcatalogus. Elk IoT Plug and Play-apparaat in de catalogus is gevalideerd en heeft een apparaatcapaciteitsmodel. Bekijk het apparaatcapaciteitsmodel om de functionaliteit van het apparaat te begrijpen of om het apparaat in Azure IoT Central te simuleren.

Wanneer u een IoT Plug and Play-apparaat aansluit, u het apparaatcapaciteitsmodel, de interfaces in het model en de telemetrie, eigenschappen en opdrachten die in die interfaces zijn gedefinieerd, bekijken.

## <a name="develop-an-iot-device-application"></a>Een IoT-apparaattoepassing ontwikkelen

Als apparaatontwikkelaar u een IoT-hardwareproduct ontwikkelen dat IoT Plug and Play ondersteunt. Het proces omvat twee belangrijke stappen:

1. Definieer het apparaatcapaciteitsmodel en interfaces. U maakt een set JSON-bestanden die de mogelijkheden van uw apparaat aangeven met de [DTDL.](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL) Een apparaatcapaciteitsmodel beschrijft een volledige entiteit, zoals een fysiek product, en definieert de set interfaces die door die entiteit zijn geïmplementeerd. Interfaces zijn gedeelde contracten die op unieke wijze de telemetrie, eigenschappen en opdrachten identificeren die door een apparaat worden ondersteund. Interfaces kunnen worden hergebruikt voor verschillende apparaatcapaciteitsmodellen.

1. Auteur van de apparaatsoftware of -firmware die de mogelijkheden implementeert die zijn aangegeven in het apparaatcapaciteitsmodel en -interfaces. De Azure IoT SDK bevat API's om apparaatcapaciteitsmodellen te implementeren.

Het [Azure IoT Tools for VS Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) extension pack biedt veel functies om u te helpen. Als apparaatontwikkelaar u bijvoorbeeld een extensie gebruiken om een skelet C-project te genereren vanuit een capaciteitsmodel. U echter elke IDE gebruiken om apparaatcapaciteitsmodellen te maken en te implementeren.

## <a name="certify-an-iot-plug-and-play-device"></a>Een IoT Plug and Play-apparaat certificeren

Als apparaatontwikkelaar u IoT-hardwareproducten indienen voor certificering. U een gecertificeerd apparaat publiceren in de catalogus van Certified for IoT-apparaten. De certificeringsprocesstappen omvatten:

- Word lid van het [Microsoft Partner Network](https://partner.microsoft.com).
- Aan boord van de Certified for Azure IoT-portal.
- Dien een IoT Plug and Play-apparaatmodel en marketinginformatie in om een nieuwe apparaatrecord te maken.
- Geef geautomatiseerde set validatietests voor het apparaat door.
- Publiceren naar de catalogus van Certified for IoT-apparaten.

## <a name="regional-availability"></a>Regionale beschikbaarheid

Tijdens de openbare preview is IoT Plug and Play beschikbaar in alle regio's.

## <a name="message-quotas-in-iot-hub"></a>Berichtquota in IoT-hub
Tijdens openbare preview verzenden IoT Plug and Play-apparaten afzonderlijke berichten per interface, waardoor het aantal berichten dat wordt geteld voor uw [berichtquotum](../iot-hub/iot-hub-devguide-quotas-throttling.md)kan toenemen.

## <a name="next-steps"></a>Volgende stappen

Nu je een overzicht hebt van IoT Plug and Play, is de voorgestelde volgende stap om een van de quickstarts uit te proberen:

- [Een apparaatmogelijkheidsmodel gebruiken om een IoT Plug and Play-apparaat te maken](./quickstart-create-pnp-device-windows.md)
- [Een apparaat verbinden met IoT Hub](./quickstart-connect-pnp-device-c-windows.md)
- [Verbinding maken met een apparaat in uw oplossing](./quickstart-connect-pnp-device-solution-node.md)
