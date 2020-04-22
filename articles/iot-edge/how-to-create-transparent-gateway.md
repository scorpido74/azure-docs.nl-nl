---
title: Transparant gatewayapparaat maken - Azure IoT Edge | Microsoft Documenten
description: Een Azure IoT Edge-apparaat gebruiken als een transparante gateway die informatie van downstream-apparaten kan verwerken
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/03/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: e563e67b5e951b43e5782f8c845c8ec46ff3e9bb
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687168"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>Een IoT Edge-apparaat configureren zodat deze werkt als een transparante gateway

In dit artikel vindt u gedetailleerde instructies voor het configureren van een IoT Edge-apparaat om te functioneren als een transparante gateway voor andere apparaten om te communiceren met IoT Hub. In dit artikel wordt de term *IoT Edge-gateway* gebruikt om te verwijzen naar een IoT Edge-apparaat dat is geconfigureerd als een transparante gateway. Zie [Hoe een IoT Edge-apparaat als gateway kan worden gebruikt voor](./iot-edge-as-gateway.md)meer informatie.

>[!NOTE]
>Momenteel:
>
> * Apparaten met randfunctionaliteit kunnen geen verbinding maken met IoT Edge-gateways.
> * Downstream-apparaten kunnen het uploaden van bestanden niet gebruiken.

Er zijn drie algemene stappen om een succesvolle transparante gatewayverbinding tot doel te stellen. Dit artikel behandelt de eerste stap:

1. **Het gateway-apparaat moet veilig verbinding kunnen maken met downstream-apparaten, communicatie van downstream-apparaten kunnen ontvangen en berichten naar de juiste bestemming kunnen leiden.**
2. Het downstream-apparaat moet een apparaatidentiteit hebben om te kunnen verifiëren met IoT Hub en te weten dat het moet communiceren via het gateway-apparaat. Zie [Een downstream-apparaat verifiëren naar Azure IoT Hub](how-to-authenticate-downstream-device.md)voor meer informatie.
3. Het downstream-apparaat moet veilig verbinding maken met het gateway-apparaat. Zie [Een downstream-apparaat verbinden met een Azure IoT Edge-gateway](how-to-connect-downstream-device.md)voor meer informatie.

Om een apparaat als gateway te laten functioneren, moet het veilig verbinding kunnen maken met zijn downstream-apparaten. Met Azure IoT Edge u een openbare sleutelinfrastructuur (PKI) gebruiken om beveiligde verbindingen tussen apparaten in te stellen. In dit geval staan we een downstream-apparaat toe om verbinding te maken met een IoT Edge-apparaat dat fungeert als een transparante gateway. Om een redelijke beveiliging te behouden, moet het downstream-apparaat de identiteit van het gateway-apparaat bevestigen. Deze identiteitscontrole voorkomt dat uw apparaten verbinding maken met mogelijk schadelijke gateways.

Een downstream-apparaat in een transparant gatewayscenario kan elke toepassing of platform zijn met een identiteit die is gemaakt met de [Azure IoT Hub-cloudservice.](https://docs.microsoft.com/azure/iot-hub) In veel gevallen maken deze toepassingen gebruik van de [Azure IoT-apparaat SDK.](../iot-hub/iot-hub-devguide-sdks.md) Voor alle praktische doeleinden kan een downstream-apparaat zelfs een toepassing zijn die wordt uitgevoerd op het IoT Edge-gateway-apparaat zelf. Een IoT Edge-apparaat kan echter niet stroomafwaarts van een IoT Edge-gateway zijn.

U elke certificaatinfrastructuur maken waarmee de vertrouwensrelatie die vereist is voor de topologie van uw apparaatgateway mogelijk is. In dit artikel gaan we uit van dezelfde certificaatinstelling die u zou gebruiken om [X.509 CA-beveiliging](../iot-hub/iot-hub-x509ca-overview.md) in Te schakelen in IoT Hub, waarbij een X.509 CA-certificaat is gekoppeld aan een specifieke IoT-hub (de IoT-hub root CA), een reeks certificaten die met deze CA zijn ondertekend en een CA voor het IoT Edge-apparaat.

![Gatewaycertificaat-instelling](./media/how-to-create-transparent-gateway/gateway-setup.png)

>[!NOTE]
>De term "root CA" gebruikt in dit artikel verwijst naar de bovenste autoriteit openbare certificaat van de PKI certificaat keten, en niet noodzakelijkerwijs het certificaat wortel van een gesyndiceerde certificaat autoriteit. In veel gevallen is het eigenlijk een tussentijds ca-openbaar certificaat.

De IoT Edge security daemon gebruikt het IoT Edge device CA-certificaat om een CA-workload-certificaat te ondertekenen, dat op zijn beurt een servercertificaat voor IoT Edge-hub ondertekent. De gateway presenteert zijn servercertificaat aan het downstream-apparaat tijdens het starten van de verbinding. Het downstream-apparaat controleert of het servercertificaat deel uitmaakt van een certificaatketen die wordt gerolld naar het basis-CA-certificaat. Met dit proces kan het downstream-apparaat bevestigen dat de gateway afkomstig is van een vertrouwde bron. Zie [Begrijpen hoe Azure IoT Edge certificaten gebruikt](iot-edge-certs.md)voor meer informatie.

De volgende stappen leiden u door het proces van het maken van de certificaten en het installeren van hen op de juiste plaatsen op de gateway. U elke machine gebruiken om de certificaten te genereren en deze vervolgens naar uw IoT Edge-apparaat kopiëren.

## <a name="prerequisites"></a>Vereisten

Een Azure IoT Edge-apparaat, geconfigureerd met [productiecertificaten.](how-to-manage-device-certificates.md)

## <a name="deploy-edgehub-to-the-gateway"></a>EdgeHub implementeren op de gateway

Wanneer u IoT Edge voor het eerst op een apparaat installeert, wordt slechts één systeemmodule automatisch gestart: de IoT Edge-agent. Zodra u de eerste implementatie meer een apparaat, de tweede systeemmodule, de IoT Edge hub, wordt gestart.

De IoT Edge-hub is verantwoordelijk voor het ontvangen van binnenkomende berichten van downstream-apparaten en het routeren ervan naar de volgende bestemming. Als de **edgeHub-module** niet op uw apparaat wordt uitgevoerd, maakt u een eerste implementatie voor uw apparaat. De implementatie ziet er leeg uit omdat u geen modules toevoegt, maar zorgt ervoor dat beide systeemmodules worden uitgevoerd.

U controleren welke modules op een apparaat worden uitgevoerd door de apparaatgegevens in de Azure-portal te `iotedge list` controleren, de apparaatstatus in Visual Studio of Visual Studio Code te bekijken of door de opdracht op het apparaat zelf uit te voeren.

Als de **edgeAgent-module** zonder **edgeHub-module** wordt uitgevoerd, voert u de volgende stappen uit:

1. Ga in Azure Portal naar uw IoT-hub.

2. Ga naar **IoT Edge** en selecteer uw IoT Edge-apparaat dat u als gateway wilt gebruiken.

3. Selecteer **Modules instellen**.

4. Selecteer **Next**.

5. Op de pagina **Routes opgeven** moet u een standaardroute hebben die alle berichten van alle modules naar IoT Hub verzendt. Voeg anders de volgende code toe en selecteer **Volgende**.

   ```JSON
   {
       "routes": {
           "route": "FROM /messages/* INTO $upstream"
       }
   }
   ```

6. Selecteer op de pagina **Sjabloon controleren** de optie **Verzenden**.

## <a name="open-ports-on-gateway-device"></a>Poorten openen op gatewayapparaat

Standaard IoT Edge-apparaten hebben geen inkomende connectiviteit nodig om te kunnen functioneren, omdat alle communicatie met IoT Hub wordt uitgevoerd via uitgaande verbindingen. Gateway-apparaten zijn anders omdat ze berichten van hun downstream-apparaten moeten ontvangen. Als een firewall zich tussen de downstream-apparaten en het gateway-apparaat bevindt, moet communicatie ook mogelijk zijn via de firewall.

Om een gatewayscenario te laten werken, moet ten minste één van de ondersteunde protocollen van de IoT Edge-hub open staan voor binnenkomend verkeer vanaf downstream-apparaten. De ondersteunde protocollen zijn MQTT, AMQP, HTTPS, MQTT via WebSockets en AMQP via WebSockets.

| Poort | Protocol |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT+WS <br> AMQP+WS |

## <a name="route-messages-from-downstream-devices"></a>Berichten van downstream-apparaten routeren

De IoT Edge-runtime kan berichten routeren die vanaf downstream-apparaten worden verzonden, net als berichten die door modules worden verzonden. Met deze functie u analyses uitvoeren in een module die op de gateway wordt uitgevoerd voordat u gegevens naar de cloud verzendt.

Momenteel is de manier waarop u berichten die door downstream-apparaten worden verzonden, door ze te onderscheiden van berichten die door modules worden verzonden. Berichten die door modules worden verzonden, bevatten allemaal een systeemeigenschap genaamd **connectionModuleId,** maar berichten die door downstream-apparaten worden verzonden, niet. U de WHERE-clausule van de route gebruiken om berichten die die eigenschap van het systeem bevatten uit te sluiten.

De onderstaande route is een voorbeeld dat berichten van `ai_insights`elk downstream-apparaat naar een module met de naam en vervolgens van `ai_insights` naar IoT Hub zou verzenden.

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")",
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream"
    }
}
```

Zie Modules implementeren en [routes instellen](./module-composition.md#declare-routes)voor meer informatie over berichtroutering.

## <a name="enable-extended-offline-operation"></a>Uitgebreide offlinebewerking inschakelen

Vanaf de [v1.0.4-release](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) van de IoT Edge-runtime kunnen het gatewayapparaat en de downstream-apparaten die ermee verbinding maken, worden geconfigureerd voor uitgebreide offline bewerking.

Met deze mogelijkheid kunnen lokale modules of downstream-apparaten desnoodzaak opnieuw verifiëren met het IoT Edge-apparaat en met elkaar communiceren met behulp van berichten en methoden, zelfs wanneer deze zijn losgekoppeld van de IoT-hub. Zie [Uitgebreide offlinemogelijkheden voor IoT Edge-apparaten, -modules en onderliggende apparaten begrijpen voor](offline-capabilities.md)meer informatie.

Als u uitgebreide offlinemogelijkheden wilt inschakelen, maakt u een bovenliggende relatie tussen een IoT Edge-gatewayapparaat en downstream-apparaten die er verbinding mee maken. Deze stappen worden nader toegelicht in [Authenticeren van een downstream-apparaat naar Azure IoT Hub.](how-to-authenticate-downstream-device.md)

## <a name="next-steps"></a>Volgende stappen

Nu u een IoT Edge-apparaat hebt dat werkt als een transparante gateway, moet u uw downstream-apparaten configureren om de gateway te vertrouwen en er berichten naar te verzenden. Ga verder [met Het verifiëren van een downstream-apparaat naar Azure IoT Hub](how-to-authenticate-downstream-device.md) voor de volgende stappen bij het instellen van uw transparante gatewayscenario.
