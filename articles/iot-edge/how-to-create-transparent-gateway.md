---
title: Transparante gateway-apparaat - Azure IoT Edge maken | Microsoft Docs
description: Een Azure IoT Edge-apparaat gebruiken als een transparante gateway waarmee gegevens uit de downstream-apparaten kan worden verwerkt.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/30/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1deeb17e4d55c81d6161855ee2e6dc4766bdcdca
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75772462"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>Een IoT Edge-apparaat om te fungeren als een transparante gateway configureren

In dit artikel vindt u gedetailleerde instructies voor het configureren van een IoT Edge apparaat om te werken als een transparante gateway zodat andere apparaten kunnen communiceren met IoT Hub. In dit artikel wordt de term *IOT Edge gateway* gebruikt om te verwijzen naar een IOT edge apparaat dat is geconfigureerd als een transparante gateway. Zie [How a IOT edge-apparaten kunnen worden gebruikt als een gateway](./iot-edge-as-gateway.md)voor meer informatie.

>[!NOTE]
>Op dit moment:
> * Edge-apparaten kunnen geen verbinding maken met IoT Edge-gateways. 
> * Aangesloten apparaten niet uploaden van bestanden gebruiken.

Er zijn drie algemene stappen voor het instellen van een geslaagde transparante gateway verbinding. In dit artikel wordt de eerste stap behandeld:

1. **Het gateway apparaat moet in staat zijn om veilig verbinding te maken met downstream-apparaten, communicaties te ontvangen van downstream-apparaten en berichten te routeren naar de juiste bestemming.**
2. Het downstream-apparaat moet een apparaat-id hebben om te kunnen worden geverifieerd met IoT Hub en u moet communiceren via het gateway apparaat. Zie [een downstream-apparaat verifiëren voor Azure IOT hub](how-to-authenticate-downstream-device.md)voor meer informatie.
3. Het downstream-apparaat moet een beveiligde verbinding maken met het gateway apparaat. Zie voor meer informatie, [een downstream-apparaat verbinden met Azure IoT Edge-gateway](how-to-connect-downstream-device.md).


Een apparaat kan alleen als gateway functioneren als het op een veilige manier verbinding kan maken met de downstream-apparaten. Azure IoT Edge kunt u een openbare-sleutelinfrastructuur (PKI) gebruiken voor het instellen van beveiligde verbindingen tussen apparaten. In dit geval toestemming we een downstream apparaat verbinding maakt met een IoT Edge-apparaat als een transparante gateway fungeert. Om redelijke beveiliging te behouden, moet het downstream-apparaat de identiteit van het gateway-apparaat bevestigen. Met deze identiteits controle voor komt u dat uw apparaten verbinding maken met mogelijk schadelijke gateways.

Een downstream-apparaat in een transparant Gateway scenario kan elke toepassing of elk platform zijn met een identiteit die is gemaakt met de [Azure IOT hub](https://docs.microsoft.com/azure/iot-hub) -Cloud service. In veel gevallen gebruikt u deze toepassingen de [Azure IoT device-SDK](../iot-hub/iot-hub-devguide-sdks.md). Praktische overwegingen, een downstream apparaat mogelijk ook een toepassing die wordt uitgevoerd op het IoT Edge-gateway-apparaat zelf. Een IoT Edge apparaat kan echter niet worden downstream van een IoT Edge gateway. 

U kunt een certificaatinfrastructuur waarmee de vertrouwensrelatie die vereist zijn voor de topologie van uw apparaat-gateway maken. In dit artikel wordt ervan uitgegaan dat u dezelfde certificaat instelling gebruikt voor het inschakelen van [x. 509 ca-beveiliging](../iot-hub/iot-hub-x509ca-overview.md) in IOT hub, waarbij een x. 509 CA-certificaat is gekoppeld aan een specifieke IOT-hub (de basis-CA van de IOT-hub), een reeks certificaten die zijn ondertekend met deze certificerings instantie en een certificerings instantie voor het IOT edge apparaat.

![Certificaat-gatewayupgrade](./media/how-to-create-transparent-gateway/gateway-setup.png)

>[!NOTE]
>De term ' basis-CA ' die in dit artikel wordt gebruikt, verwijst naar het open bare certificaat van de bovenste certificerings instantie van de PKI-certificaat keten, en niet noodzakelijkerwijs de hoofdmap van het certificaat van een extern gepubliceerde certificerings instantie. In veel gevallen is het eigenlijk een openbaar certificaat van een tussenliggend CA. 

De gateway geeft het CA-certificaat van IoT Edge apparaat aan het downstream-apparaat tijdens de start van de verbinding. Het downstream-apparaat controleert of het CA-certificaat van IoT Edge apparaat is ondertekend door het basis-CA-certificaat. Met dit proces kan het downstream-apparaat controleren of de gateway afkomstig is van een vertrouwde bron.

De volgende stappen leiden u door het proces van het maken van de certificaten en het installeren ervan op de juiste plaatsen op de gateway. U kunt elke virtuele machine gebruiken voor het genereren van de certificaten, en kopieer deze vervolgens naar uw IoT Edge-apparaat. 

## <a name="prerequisites"></a>Vereisten

Een Azure IoT Edge apparaat, geconfigureerd met [productie certificaten](how-to-install-production-certificates.md).

## <a name="deploy-edgehub-to-the-gateway"></a>EdgeHub implementeren op de gateway

Wanneer u IoT Edge voor het eerst op een apparaat installeert, wordt slechts één systeem module automatisch gestart: de IoT Edge agent. Wanneer u de eerste implementatie meer een apparaat maakt, wordt de tweede systeem module, de IoT Edge hub, ook gestart. 

De IoT Edge hub is verantwoordelijk voor het ontvangen van inkomende berichten van downstream-apparaten en het routeren naar de volgende bestemming. Als de **edgeHub** -module niet op uw apparaat wordt uitgevoerd, maakt u een eerste implementatie voor uw apparaat. De implementatie ziet er leeg uit omdat u geen modules toevoegt, maar dit zorgt ervoor dat beide systeem modules worden uitgevoerd. 

U kunt controleren welke modules worden uitgevoerd op een apparaat door de apparaatgegevens in de Azure Portal te controleren, de status van het apparaat weer te geven in Visual Studio of Visual Studio code of door de opdracht `iotedge list` op het apparaat zelf uit te voeren. 

Als de **edgeAgent** -module wordt uitgevoerd zonder de **edgeHub** -module, voert u de volgende stappen uit:

1. Ga in Azure Portal naar uw IoT-hub.

2. Ga naar **IoT Edge** en selecteert u uw IoT Edge-apparaat dat u wilt gebruiken als een gateway.

3. Selecteer **Modules instellen**.

4. Selecteer **Next**.

5. In de **routes opgeven** pagina, moet u hebt een standaardroute dat alle berichten van alle modules naar IoT Hub verzendt. Voeg anders de volgende code toe en selecteer **Volgende**.

   ```JSON
   {
       "routes": {
           "route": "FROM /messages/* INTO $upstream"
       }
   }
   ```

6. In de **sjabloon controleren** weergeeft, schakelt **indienen**.

## <a name="open-ports-on-gateway-device"></a>Poorten op gateway apparaat openen

Standard IoT Edge-apparaten hebben geen binnenkomende verbinding nodig om te kunnen werken, omdat alle communicatie met IoT Hub geschiedt via uitgaande verbindingen. Gateway apparaten wijken af omdat ze berichten van hun downstream-apparaten moeten ontvangen. Als er een firewall is tussen de downstream-apparaten en het gateway apparaat, moet communicatie ook mogelijk zijn via de firewall.

Een gateway scenario werkt alleen als ten minste een van de ondersteunde protocollen van de IoT Edge hub open is voor inkomend verkeer van downstream-apparaten. De ondersteunde protocollen zijn MQTT, AMQP, HTTPS, MQTT over websockets en AMQP over websockets. 

| Port | Protocol |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT + WS <br> AMQP+WS | 

## <a name="route-messages-from-downstream-devices"></a>Routeren van berichten van downstream-apparaten
IoT Edge-runtime kunt versturen berichten worden verzonden van downstream apparaten net als bij berichten die worden verzonden door modules. Met deze functie kunt u analyses uitvoeren in een module die op de gateway wordt uitgevoerd voordat u gegevens naar de Cloud verzendt. 

Op dit moment is de manier waarop u berichten die worden verzonden door downstream-apparaten versturen door ze verschillen van berichten die worden verzonden door modules. Berichten van alle modules bevatten een systeemeigenschap, genaamd **connectionModuleId** maar niet voor berichten die worden verzonden door downstream-apparaten. De WHERE-component van de route kunt u alle berichten met die systeemeigenschap uitsluiten. 

De onderstaande route is een voor beeld waarmee berichten van een wille keurig downstream-apparaat naar een module met de naam `ai_insights`worden verzonden en vervolgens van `ai_insights` naar IoT Hub.

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", 
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" 
    } 
}
```

Zie voor meer informatie over het routeren van berichten, [implementeren modules en routes tot stand brengen](./module-composition.md#declare-routes).


## <a name="enable-extended-offline-operation"></a>Uitgebreide offline bewerking inschakelen

Vanaf de release van de [v-1.0.4](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) van de IOT Edge runtime kunnen het gateway apparaat en de downstream-apparaten waarmee verbinding wordt gemaakt, worden geconfigureerd voor een uitgebreide offline bewerking. 

Met deze mogelijkheid kunnen lokale modules of downstream-apparaten opnieuw worden geverifieerd met het IoT Edge apparaat en met elkaar communiceren met behulp van berichten en methoden, zelfs wanneer de verbinding met de IoT-hub is verbroken. Zie voor meer informatie, [begrijpen uitgebreid offline-mogelijkheden voor IoT Edge-apparaten, modules en onderliggende apparaten](offline-capabilities.md).

Als u uitgebreide offline mogelijkheden wilt inschakelen, stelt u een relatie tussen een bovenliggend/onderliggend item in tussen een IoT Edge gateway apparaat en downstream-apparaten waarmee er verbinding mee wordt gemaakt. Deze stappen worden uitgebreid beschreven in [een downstream-apparaat verifiëren bij Azure IOT hub](how-to-authenticate-downstream-device.md).

## <a name="next-steps"></a>Volgende stappen

Nu u een IoT Edge-apparaat werkt als een transparante gateway hebt, moet u uw downstream-apparaten voor het vertrouwen van de gateway en berichten te verzenden. Ga door met het [verifiëren van een downstream-apparaat bij Azure IOT hub](how-to-authenticate-downstream-device.md) voor de volgende stappen in het instellen van uw transparante Gateway scenario. 
