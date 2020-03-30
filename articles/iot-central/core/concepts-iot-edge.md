---
title: Azure IoT Edge en Azure IoT Central | Microsoft Documenten
description: Meer informatie over het gebruik van Azure IoT Edge met een IoT Central-toepassing.
author: dominicbetts
ms.author: dobett
ms.date: 12/12/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 69660152458de26e9dbcbf1f50db6ce6824351d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77027067"
---
# <a name="connect-azure-iot-edge-devices-to-an-azure-iot-central-application"></a>Azure IoT Edge-apparaten verbinden met een Azure IoT Central-toepassing

IoT Edge bestaat uit drie componenten:

* **IoT Edge-modules** zijn containers waarop Azure-services, partnerservices of uw eigen code worden uitgevoerd. Modules worden geïmplementeerd op IoT Edge-apparaten en lokaal worden uitgevoerd op die apparaten.
* De **IoT Edge-runtime** wordt uitgevoerd op elk IoT Edge-apparaat en beheert de modules die op elk apparaat zijn geïmplementeerd.
* Met een cloudinterface u op afstand IoT **Edge-apparaten** monitoren en beheren. IoT Central is de cloudinterface.

Een **Azure IoT Edge-apparaat** kan een gateway-apparaat zijn, waarbij downstream-apparaten verbinding maken met het IoT Edge-apparaat. In dit artikel vindt u meer informatie over downstream-apparaatconnectiviteitspatronen.

Een **apparaatsjabloon** definieert de mogelijkheden van uw apparaat en IoT Edge-modules. Mogelijkheden omvatten telemetrie die de module verzendt, module-eigenschappen en de opdrachten waar een module op reageert.

## <a name="downstream-device-relationships-with-a-gateway-and-modules"></a>Downstream-apparaatrelaties met een gateway en modules

Downstream-apparaten kunnen via de `$edgeHub` module verbinding maken met een IoT Edge-gatewayapparaat. Dit IoT Edge-apparaat wordt in dit scenario een transparante gateway.

![Diagram van transparante gateway](./media/concepts-iot-edge/gateway-transparent.png)

Downstream-apparaten kunnen ook verbinding maken met een IoT Edge-gatewayapparaat via een aangepaste module. In het volgende scenario maken downstream-apparaten verbinding via een aangepaste Modbus-module.

![Diagram van aangepaste moduleverbinding](./media/concepts-iot-edge/gateway-module.png)

In het volgende diagram wordt de verbinding met een IoT `$edgeHub`Edge-gatewayapparaat weergegeven via beide typen modules (aangepast en ).  

![Diagram van verbinding via beide verbindingsmodules](./media/concepts-iot-edge/gateway-module-transparent.png)

Ten slotte kunnen downstream-apparaten verbinding maken met een IoT Edge-gateway-apparaat via meerdere aangepaste modules. Het volgende diagram toont downstream-apparaten die verbinding maken via een `$edgeHub` aangepaste Modbus-module, een BLE-aangepaste module en de module. 

![Diagram van verbinding via meerdere aangepaste modules](./media/concepts-iot-edge/gateway-module2-transparent.png)

## <a name="deployment-manifests-and-device-templates"></a>Implementatiemanifesten en apparaatsjablonen

In IoT Edge u bedrijfslogica implementeren en beheren in de vorm van modules. IoT Edge-modules zijn de kleinste rekeneenheid die wordt beheerd door IoT Edge en kunnen Azure-services (zoals Azure Stream Analytics) of uw eigen oplossingsspecifieke code bevatten. Zie [IoT Edge-modules](../../iot-edge/iot-edge-modules.md)om te begrijpen hoe modules worden ontwikkeld, geïmplementeerd en onderhouden.

Op een hoog niveau is een implementatiemanifest een lijst van moduletweelingen die zijn geconfigureerd met de gewenste eigenschappen. Een implementatiemanifest vertelt een IoT Edge-apparaat (of een groep apparaten) welke modules moeten worden geïnstalleerd en hoe u deze configureren. Implementatiemanifesten bevatten de gewenste eigenschappen voor elke moduletwin. IoT Edge-apparaten rapporteren de gerapporteerde eigenschappen voor elke module.

Gebruik Visual Studio Code om een implementatiemanifest te maken. Zie [Azure IoT Edge voor Visual Studio Code voor](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)meer informatie.

In Azure IoT Central u een implementatiemanifest importeren om een apparaatsjabloon te maken. Het volgende stroomdiagram toont een implementatiemanifestlevenscyclus in IoT Central.

![Stroomdiagram van de levenscyclus van implementatiemanifest](./media/concepts-iot-edge/dmflow.png)

IoT Plug and Play (preview) modelleert een IoT Edge-apparaat als volgt:

* Elke IoT Edge-apparaatsjabloon heeft een apparaatcapaciteitsmodel.
* Voor elke aangepaste module die in het implementatiemanifest wordt vermeld, wordt een modulecapaciteitsmodel gegenereerd.
* Er wordt een relatie tot stand gebracht tussen elk modulecapaciteitsmodel en een apparaatcapaciteitsmodel.
* Een modulecapaciteitsmodel implementeert module-interfaces.
* Elke module-interface bevat telemetrie, eigenschappen en opdrachten.

![Diagram met IoT Edge-modellering](./media/concepts-iot-edge/edgemodelling.png)

## <a name="iot-edge-gateway-devices"></a>IoT Edge-gatewayapparaten

Als u een IoT Edge-apparaat hebt geselecteerd als gatewayapparaat, u downstreamrelaties toevoegen aan apparaatcapaciteitsmodellen voor apparaten die u met het gateway-apparaat wilt verbinden.

## <a name="next-steps"></a>Volgende stappen

Nu u weet wat IoT Central-toepassingssjablonen zijn, gaat u aan de slag met [het maken van een IoT Central Application.](quick-deploy-iot-central.md)