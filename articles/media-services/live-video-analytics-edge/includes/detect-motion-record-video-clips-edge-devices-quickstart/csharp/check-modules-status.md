---
ms.openlocfilehash: e46a56742ab8b98c53c1cd05e840e8ad4b8a73da
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88682116"
---
Vouw bij de stap [Het IoT Edge-implementatiemanifest genereren en implementeren](../../../detect-motion-emit-events-quickstart.md#generate-and-deploy-the-deployment-manifest) in Visual Studio Code het knooppunt **lva-sample-device** onder **AZURE IOT HUB** uit (in de sectie linksonder). U ziet dat de volgende modules zijn geïmplementeerd:

* De Live Video Analytics-module met de naam `lvaEdge`
* De `rtspsim`-module, die een RTSP-server simuleert en fungeert als de bron van een live-videofeed

  ![Modules](../../../media/quickstarts/lva-sample-device-node.png)

> [!NOTE]
> Als u uw eigen Edge-apparaat gebruikt in plaats van het apparaat dat door ons installatiescript is ingericht, gaat u naar uw Edge-apparaat en voert u de volgende opdrachten uit met **beheerdersrechten** om het voorbeeldvideobestand dat voor deze quickstart wordt gebruikt, op te halen en op te slaan:  

```
mkdir /home/lvaadmin/samples
mkdir /home/lvaadmin/samples/input    
curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaadmin/samples/input/camera-300s.mkv  
chown -R lvaadmin /home/lvaadmin/samples/  
```
