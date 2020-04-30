---
title: Transparant gateway apparaat maken-Azure IoT Edge | Microsoft Docs
description: Een Azure IoT Edge apparaat gebruiken als een transparante gateway die informatie kan verwerken vanaf downstream-apparaten
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81687168"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>Een IoT Edge-apparaat configureren zodat deze werkt als een transparante gateway

In dit artikel vindt u gedetailleerde instructies voor het configureren van een IoT Edge apparaat om te werken als een transparante gateway zodat andere apparaten kunnen communiceren met IoT Hub. In dit artikel wordt de term *IOT Edge gateway* gebruikt om te verwijzen naar een IOT edge apparaat dat is geconfigureerd als een transparante gateway. Zie [How a IOT edge-apparaten kunnen worden gebruikt als een gateway](./iot-edge-as-gateway.md)voor meer informatie.

>[!NOTE]
>Dat
>
> * Apparaten met rand mogelijkheden kunnen geen verbinding maken met IoT Edge gateways.
> * Downstream-apparaten kunnen het uploaden van bestanden niet gebruiken.

Er zijn drie algemene stappen voor het instellen van een geslaagde transparante gateway verbinding. In dit artikel wordt de eerste stap behandeld:

1. **Het gateway apparaat moet in staat zijn om veilig verbinding te maken met downstream-apparaten, communicaties te ontvangen van downstream-apparaten en berichten te routeren naar de juiste bestemming.**
2. Het downstream-apparaat moet een apparaat-id hebben om te kunnen worden geverifieerd met IoT Hub en u moet communiceren via het gateway apparaat. Zie [een downstream-apparaat verifiëren voor Azure IOT hub](how-to-authenticate-downstream-device.md)voor meer informatie.
3. Het downstream-apparaat moet een beveiligde verbinding maken met het gateway apparaat. Zie [verbinding maken tussen een downstream-apparaat en een Azure IOT Edge-gateway](how-to-connect-downstream-device.md)voor meer informatie.

Een apparaat kan alleen als gateway functioneren als het op een veilige manier verbinding kan maken met de downstream-apparaten. Met Azure IoT Edge kunt u een open bare-sleutel infrastructuur (PKI) gebruiken om beveiligde verbindingen tussen apparaten in te stellen. In dit geval kunnen we een downstream-apparaat verbinding laten maken met een IoT Edge apparaat dat als transparante gateway fungeert. Om redelijke beveiliging te behouden, moet het downstream-apparaat de identiteit van het gateway-apparaat bevestigen. Met deze identiteits controle voor komt u dat uw apparaten verbinding maken met mogelijk schadelijke gateways.

Een downstream-apparaat in een transparant Gateway scenario kan elke toepassing of elk platform zijn met een identiteit die is gemaakt met de [Azure IOT hub](https://docs.microsoft.com/azure/iot-hub) -Cloud service. In veel gevallen gebruiken deze toepassingen de [Azure IOT Device SDK](../iot-hub/iot-hub-devguide-sdks.md). Voor alle praktische doel einden kan een downstream-apparaat zelfs een toepassing zijn die wordt uitgevoerd op het IoT Edge gateway apparaat zelf. Een IoT Edge apparaat kan echter niet worden downstream van een IoT Edge gateway.

U kunt een certificaat infrastructuur maken waarmee de vertrouwens relatie die is vereist voor de gateway topologie van uw apparaat wordt ingeschakeld. In dit artikel wordt ervan uitgegaan dat u dezelfde certificaat instelling gebruikt voor het inschakelen van [x. 509 ca-beveiliging](../iot-hub/iot-hub-x509ca-overview.md) in IOT hub, waarbij een x. 509 CA-certificaat is gekoppeld aan een specifieke IOT-hub (de basis-CA van de IOT-hub), een reeks certificaten die zijn ondertekend met deze certificerings instantie en een certificerings instantie voor het IOT edge apparaat.

![Installatie van Gateway certificaat](./media/how-to-create-transparent-gateway/gateway-setup.png)

>[!NOTE]
>De term ' basis-CA ' die in dit artikel wordt gebruikt, verwijst naar het open bare certificaat van de bovenste certificerings instantie van de PKI-certificaat keten, en niet noodzakelijkerwijs de hoofdmap van het certificaat van een extern gepubliceerde certificerings instantie. In veel gevallen is het eigenlijk een openbaar certificaat van een tussenliggend CA.

De IoT Edge Security daemon maakt gebruik van het CA-certificaat van IoT Edge apparaat om een CA-certificaat van een werk belasting te ondertekenen, dat op zijn beurt een server certificaat voor IoT Edge hub ondertekent. De gateway presenteert het server certificaat voor het downstream-apparaat tijdens de start van de verbinding. Het downstream-apparaat controleert of het server certificaat deel uitmaakt van een certificaat keten die naar het basis-CA-certificaat rolt. Met dit proces kan het downstream-apparaat controleren of de gateway afkomstig is van een vertrouwde bron. Zie [begrijpen hoe Azure IOT Edge certificaten gebruikt](iot-edge-certs.md)voor meer informatie.

De volgende stappen leiden u door het proces van het maken van de certificaten en het installeren ervan op de juiste plaatsen op de gateway. U kunt elke computer gebruiken om de certificaten te genereren en deze vervolgens naar uw IoT Edge apparaat kopiëren.

## <a name="prerequisites"></a>Vereisten

Een Azure IoT Edge apparaat, geconfigureerd met [productie certificaten](how-to-manage-device-certificates.md).

## <a name="deploy-edgehub-to-the-gateway"></a>EdgeHub implementeren op de gateway

Wanneer u IoT Edge voor het eerst op een apparaat installeert, wordt slechts één systeem module automatisch gestart: de IoT Edge agent. Wanneer u de eerste implementatie meer een apparaat maakt, wordt de tweede systeem module, de IoT Edge hub, ook gestart.

De IoT Edge hub is verantwoordelijk voor het ontvangen van inkomende berichten van downstream-apparaten en het routeren naar de volgende bestemming. Als de **edgeHub** -module niet op uw apparaat wordt uitgevoerd, maakt u een eerste implementatie voor uw apparaat. De implementatie ziet er leeg uit omdat u geen modules toevoegt, maar dit zorgt ervoor dat beide systeem modules worden uitgevoerd.

U kunt controleren welke modules worden uitgevoerd op een apparaat door de apparaatgegevens in de Azure Portal te controleren, de status van het apparaat weer te geven in Visual Studio of Visual Studio code of `iotedge list` door de opdracht op het apparaat zelf uit te voeren.

Als de **edgeAgent** -module wordt uitgevoerd zonder de **edgeHub** -module, voert u de volgende stappen uit:

1. Ga in Azure Portal naar uw IoT-hub.

2. Ga naar **IOT Edge** en selecteer het IOT edge apparaat dat u als gateway wilt gebruiken.

3. Selecteer **modules instellen**.

4. Selecteer **Next**.

5. Op de pagina **routes opgeven** moet u een standaard route hebben waarmee alle berichten van alle modules naar IOT hub worden verzonden. Voeg anders de volgende code toe en selecteer **Volgende**.

   ```JSON
   {
       "routes": {
           "route": "FROM /messages/* INTO $upstream"
       }
   }
   ```

6. Selecteer op de pagina **controle sjabloon** de optie **verzenden**.

## <a name="open-ports-on-gateway-device"></a>Poorten op gateway apparaat openen

Standard IoT Edge-apparaten hebben geen binnenkomende verbinding nodig om te kunnen werken, omdat alle communicatie met IoT Hub geschiedt via uitgaande verbindingen. Gateway apparaten wijken af omdat ze berichten van hun downstream-apparaten moeten ontvangen. Als er een firewall is tussen de downstream-apparaten en het gateway apparaat, moet communicatie ook mogelijk zijn via de firewall.

Een gateway scenario werkt alleen als ten minste een van de ondersteunde protocollen van de IoT Edge hub open is voor inkomend verkeer van downstream-apparaten. De ondersteunde protocollen zijn MQTT, AMQP, HTTPS, MQTT over websockets en AMQP over websockets.

| Poort | Protocol |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT + WS <br> AMQP + WS |

## <a name="route-messages-from-downstream-devices"></a>Berichten van downstream-apparaten routeren

De IoT Edge runtime kan berichten verzenden die zijn verzonden vanaf downstream-apparaten, net als berichten die door modules worden verzonden. Met deze functie kunt u analyses uitvoeren in een module die op de gateway wordt uitgevoerd voordat u gegevens naar de Cloud verzendt.

Op dit moment kunt u berichten die door downstream-apparaten worden verzonden, op een andere manier onderscheiden van berichten die door modules worden verzonden. Berichten die door modules worden verzonden, bevatten een systeem eigenschap met de naam **connectionModuleId** , maar berichten die door downstream-apparaten worden verzonden, worden niet uitgevoerd. U kunt de component WHERE van de route gebruiken om berichten met die systeem eigenschap uit te sluiten.

De onderstaande route is een voor beeld waarmee berichten van elk downstream-apparaat naar een module met `ai_insights`de naam worden verzonden `ai_insights` en vervolgens worden IOT hub.

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")",
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream"
    }
}
```

Zie [modules implementeren en routes instellen](./module-composition.md#declare-routes)voor meer informatie over het routeren van berichten.

## <a name="enable-extended-offline-operation"></a>Uitgebreide offline bewerking inschakelen

Vanaf de release van de [v-1.0.4](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) van de IOT Edge runtime kunnen het gateway apparaat en de downstream-apparaten waarmee verbinding wordt gemaakt, worden geconfigureerd voor een uitgebreide offline bewerking.

Met deze mogelijkheid kunnen lokale modules of downstream-apparaten opnieuw worden geverifieerd met het IoT Edge apparaat en met elkaar communiceren met behulp van berichten en methoden, zelfs wanneer de verbinding met de IoT-hub is verbroken. Zie voor meer informatie [uitgebreide offline mogelijkheden voor IOT edge apparaten, modules en onderliggende apparaten begrijpen](offline-capabilities.md).

Als u uitgebreide offline mogelijkheden wilt inschakelen, stelt u een relatie tussen een bovenliggend/onderliggend item in tussen een IoT Edge gateway apparaat en downstream-apparaten waarmee er verbinding mee wordt gemaakt. Deze stappen worden uitgebreid beschreven in [een downstream-apparaat verifiëren bij Azure IOT hub](how-to-authenticate-downstream-device.md).

## <a name="next-steps"></a>Volgende stappen

Nu u een IoT Edge apparaat werkt als een transparante gateway, moet u uw downstream-apparaten configureren om de gateway te vertrouwen en er berichten naar toe te sturen. Ga door met het [verifiëren van een downstream-apparaat bij Azure IOT hub](how-to-authenticate-downstream-device.md) voor de volgende stappen in het instellen van uw transparante Gateway scenario.
