---
title: 'Zelfstudie: een toepassing Videoanalyse: object- en bewegingsdetectie maken in Azure IoT Central (YOLO v3)'
description: In deze zelfstudie leert u hoe u een videoanalysetoepassing maakt in IoT Central. U maakt de toepassing, past deze aan en verbindt deze met andere Azure-services. In deze zelfstudie wordt het YOLO v3-systeem voor objectdetectie in realtime gebruikt.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
author: KishorIoT
ms.author: nandab
ms.date: 10/06/2020
ms.openlocfilehash: 3994b05f613cbebcf6daa05cf8db3ef429b52407
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94428058"
---
# <a name="tutorial-create-a-video-analytics---object-and-motion-detection-application-in-azure-iot-central-yolo-v3"></a>Zelfstudie: Een toepassing Videoanalyse: object- en bewegingsdetectie maken in Azure IoT Central (YOLO v3)

Als ontwikkelaar van oplossingen leert u hoe u een videoanalysetoepassing maakt met de IoT Central-toepassingssjabloon *Videoanalyse: object- en bewegingsdetectie*, Azure IoT Edge-apparaten, Azure Media Services en het YOLO v3-systeem voor objectdetectie in realtime. Voor de oplossing wordt een detailhandel gebruikt om te laten zien hoe u voldoet aan de algemene bedrijfsbehoefte om beveiligingscamera’s te bewaken. De oplossing maakt gebruik van automatische objectdetectie in een videofeed om snel interessante gebeurtenissen te identificeren en zoeken.

> [!TIP]
> Als u OpenVINO&trade; wilt gebruiken in plaats van YOLO v3 voor object- en bewegingsdetectie, raadpleegt u [Zelfstudie: een toepassing Videoanalyse: object- en bewegingsdetectie maken in Azure IoT Central (OpenVINO&trade;)](tutorial-video-analytics-create-app-openvino.md).

[!INCLUDE [iot-central-video-analytics-part1](../../../includes/iot-central-video-analytics-part1.md)]

- [Scratchpad.txt](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/Scratchpad.txt)
- [deployment.amd64.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/deployment.amd64.json)
- [LvaEdgeGatewayDcm.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/LvaEdgeGatewayDcm.json)
- [state.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/state.json)

> [!NOTE]
> De GitHub-opslagplaats bevat ook de broncode voor de IoT Edge-modules **LvaEdgeGatewayModule** en **lvaYolov3**. Zie de [modules over het ontwikkelen van de LVA-gateway](tutorial-video-analytics-build-module.md) voor meer informatie over werken met de broncode.

[!INCLUDE [iot-central-video-analytics-part2](../../../includes/iot-central-video-analytics-part2.md)]

## <a name="edit-the-deployment-manifest"></a>Het implementatiemanifest bewerken

U implementeert een IoT Edge-module met behulp van een implementatiemanifest. In IoT Central kunt u het manifest als een apparaatsjabloon importeren en vervolgens IoT Central de module-implementatie laten beheren.

Het implementatiemanifest voorbereiden:

1. Open het bestand *deployment.amd64.json*, dat u in de map *lva-configuration* hebt opgeslagen, met een teksteditor.

1. Zoek de `LvaEdgeGatewayModule`-instellingen en wijzig de afbeeldingsnaam zoals weergegeven in het volgende fragment:

    ```json
    "LvaEdgeGatewayModule": {
        "settings": {
            "image": "mcr.microsoft.com/lva-utilities/lva-edge-iotc-gateway:1.0-amd64",
    ```

1. Voeg de naam van uw Media Services-account toe aan het `env`-knooppunt in de sectie `LvaEdgeGatewayModule`. U hebt deze accountnaam genoteerd in het bestand *scratchpad.txt*.

    ```json
    "env": {
        "lvaEdgeModuleId": {
            "value": "lvaEdge"
        },
        "amsAccountName": {
            "value": "<YOUR_AZURE_MEDIA_ACCOUNT_NAME>"
        }
    }
    ```

1. De sjabloon stelt deze eigenschappen niet beschikbaar in IoT Central, dus u moet de Media Services-configuratiewaarden aan het implementatiemanifest toevoegen. Zoek de `lvaEdge`-module en vervang de tijdelijke aanduidingen door de waarden die u in het bestand *scratchpad.txt* had genoteerd toen u uw Media Services-account maakte.

    De `azureMediaServicesArmId` is de **Resource-id** die u in het bestand *scratchpad.txt* had genoteerd toen u het Media Services-account maakte.

    U had de `aadTenantId`, `aadServicePrincipalAppId` en `aadServicePrincipalSecret` in het bestand *scratchpad.txt* genoteerd toen u de service-principal voor uw Media Services-account maakte:

    ```json
    {
        "lvaEdge":{
        "properties.desired": {
            "applicationDataDirectory": "/var/lib/azuremediaservices",
            "azureMediaServicesArmId": "[Resource ID from scratchpad]",
            "aadTenantId": "[AADTenantID from scratchpad]",
            "aadServicePrincipalAppId": "[AadClientId from scratchpad]",
            "aadServicePrincipalSecret": "[AadSecret from scratchpad]",
            "aadEndpoint": "https://login.microsoftonline.com",
            "aadResourceId": "https://management.core.windows.net/",
            "armEndpoint": "https://management.azure.com/",
            "diagnosticsEventsOutputName": "AmsDiagnostics",
            "operationalMetricsOutputName": "AmsOperational",
            "logCategories": "Application,Event",
            "AllowUnsecuredEndpoints": "true",
            "TelemetryOptOut": false
            }
        }
    }
    ```

1. Sla de wijzigingen op.

In deze zelfstudie wordt uw oplossing geconfigureerd voor gebruik van de YOLO v3-module voor object- en bewegingsdetectie. In het volgende codefragment wordt de configuratie van de module getoond:

```json
"lvaYolov3": {
    "settings": {
        "image": "mcr.microsoft.com/lva-utilities/yolov3-onnx:1.0"
    },
    "type": "docker",
    "status": "running",
    "restartPolicy": "always",
    "version": "1.0"
}
```

[!INCLUDE [iot-central-video-analytics-part3](../../../includes/iot-central-video-analytics-part3.md)]

### <a name="replace-the-manifest"></a>Het manifest vervangen

Selecteer op de pagina **LVA Edge Gateway v2** de optie **+ Manifest vervangen**.

:::image type="content" source="./media/tutorial-video-analytics-create-app-yolo-v3/replace-manifest.png" alt-text="Manifest vervangen":::

Navigeer naar de map *lva-configuration* en selecteer het manifestbestand *deployment.amd64.json* dat u eerder hebt bewerkt. Selecteer **Uploaden**. Wanneer de validatie is voltooid, selecteert u **Vervangen**.

[!INCLUDE [iot-central-video-analytics-part4](../../../includes/iot-central-video-analytics-part4.md)]
