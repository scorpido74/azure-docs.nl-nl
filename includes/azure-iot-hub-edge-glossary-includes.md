---
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 11/09/2018
ms.author: dobett
ms.openlocfilehash: c95bca125ea70cf32acad0d5ea67c3ad195ed704
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67176154"
---
## <a name="automatic-device-management"></a>Automatisch apparaatbeheer
Automatic Device Management in Azure IoT Hub automatiseert veel van de repetitieve en complexe taken van het beheer van grote apparaatvloten gedurende het gehele levenscyclus. Met Automatisch apparaatbeheer u een set apparaten targeten op basis van hun eigenschappen, een gewenste configuratie definiëren en IoT Hub-apparaten laten bijwerken wanneer ze in het bereik komen.  Bestaat uit [automatische apparaatconfiguraties](../articles/iot-hub/iot-hub-auto-device-config.md) en automatische implementaties van [IoT Edge.](../articles/iot-edge/how-to-deploy-monitor.md)

## <a name="iot-edge"></a>IoT Edge
Azure IoT Edge maakt cloudgestuurde implementatie van Azure-services en oplossingsspecifieke code naar on-premises apparaten mogelijk. IoT Edge-apparaten kunnen gegevens van andere apparaten samenvoegen om computers en analyses uit te voeren voordat de gegevens naar de cloud worden verzonden. Zie [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/)voor meer informatie.

## <a name="iot-edge-agent"></a>IoT Edge-agent
Het deel van de IoT Edge runtime dat verantwoordelijk is voor het implementeren en monitoren van modules.

## <a name="iot-edge-device"></a>IoT Edge-apparaat
IoT Edge-apparaten hebben de 3T Edge-runtime geïnstalleerd en worden gemarkeerd als **IoT Edge-apparaat** in de apparaatgegevens. Meer informatie over het [implementeren van Azure IoT Edge op een gesimuleerd apparaat in Linux - preview](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux).

## <a name="iot-edge-automatic-deployment"></a>Automatische implementatie van IoT Edge
Een automatische implementatie van IoT Edge configureert een doelset IoT Edge-apparaten om een set IoT Edge-modules uit te voeren. Elke implementatie zorgt er continu voor dat alle apparaten die overeenkomen met de doelconditie de opgegeven set modules uitvoeren, zelfs wanneer nieuwe apparaten worden gemaakt of worden aangepast aan de doelvoorwaarde. Elk IoT Edge-apparaat ontvangt alleen de implementatie met de hoogste prioriteit aan de doelvoorwaarde. Meer informatie over [automatische implementatie van IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).

## <a name="iot-edge-deployment-manifest"></a>IoT Edge-implementatiemanifest
Een Json-document met de informatie die moet worden gekopieerd in de moduletwin(s) van een of meer IoT Edge-apparaten om een reeks modules, routes en bijbehorende gewenste eigenschappen van de module te implementeren.

## <a name="iot-edge-gateway-device"></a>IoT Edge-gatewayapparaat
Een IoT Edge-apparaat met downstream-apparaat. Het downstream-apparaat kan een IoT Edge zijn of niet het IoT Edge-apparaat.

## <a name="iot-edge-hub"></a>IoT Edge-hub
Het deel van de IoT Edge runtime dat verantwoordelijk is voor module-communicatie naar modulecommunicatie, upstream (richting IoT Hub) en downstream (weg van IoT Hub) communicatie. 

## <a name="iot-edge-leaf-device"></a>IoT Edge leaf-apparaat
Een IoT Edge-apparaat zonder downstream-apparaat. 

## <a name="iot-edge-module"></a>IoT Edge-module
Een IoT Edge-module is een Docker-container die u implementeren op IoT Edge-apparaten. Het voert een specifieke taak uit, zoals het innemen van een bericht vanaf een apparaat, het transformeren van een bericht of het verzenden van een bericht naar een IoT-hub. Het communiceert met andere modules en stuurt gegevens naar de IoT Edge runtime. [Begrijp de vereisten en tools voor het ontwikkelen van IoT Edge-modules.](https://docs.microsoft.com/azure/iot-edge/module-development)

## <a name="iot-edge-module-identity"></a>Identiteit van IoT Edge-module
Een record in het identiteitsregister van de IoT Hub-module waarin het bestaan en de beveiligingsreferenties worden beschreven die door een module moeten worden gebruikt om te verifiëren met een edge hub of IoT Hub.

## <a name="iot-edge-module-image"></a>Afbeelding van de IoT Edge-module
De dockerafbeelding die wordt gebruikt door de runtime van IoT Edge om module-exemplaren te instantiëren.

## <a name="iot-edge-module-twin"></a>IoT Edge module twin
Er is een Json-document blijven staan in de IoT-hub waarmee de statusgegevens voor een module-exemplaar worden opgeslagen.

## <a name="iot-edge-priority"></a>IoT Edge-prioriteit
Wanneer twee IoT Edge-implementaties zich op hetzelfde apparaat richten, wordt de implementatie met hogere prioriteit toegepast. Als twee implementaties dezelfde prioriteit hebben, wordt de implementatie met de latere aanmaakdatum toegepast. Meer informatie over [prioriteit](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring#priority).

## <a name="iot-edge-runtime"></a>IoT Edge-runtime
IoT Edge-runtime bevat alles wat Microsoft distribueert om te worden geïnstalleerd op een IoT Edge-apparaat. Het bevat Edge-agent, Edge-hub en de IoT Edge-beveiligingsdaemon.

## <a name="iot-edge-set-modules-to-a-single-device"></a>IoT Edge-setmodules op één apparaat
Een bewerking die de inhoud van een IoT Edge-manifest kopieert op de modulekoppeling van één apparaat. De onderliggende API is een generieke 'apply configuration', die gewoon een IoT Edge manifest als input neemt.

## <a name="iot-edge-target-condition"></a>IoT Edge-doelconditie
In een IoT Edge-implementatie is doelvoorwaarde een Booleaanse voorwaarde op de tags van apparaattweelingen om de doelapparaten van de implementatie te selecteren, bijvoorbeeld **tag.environment = prod**. De doelvoorwaarde wordt voortdurend geëvalueerd om nieuwe apparaten op te nemen die aan de vereisten voldoen of apparaten te verwijderen die dit niet meer doen. Meer informatie over [doelconditie](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring#target-condition)