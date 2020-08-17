---
title: 'Zelfstudie: Een toepassing ‘Videoanalyse: object- en bewegingsdetectie’ maken in Azure IoT Central'
description: In deze zelfstudie leert u hoe u een videoanalysetoepassing maakt in IoT Central. U maakt de toepassing, past deze aan en verbindt deze met andere Azure-services.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
author: KishorIoT
ms.author: nandab
ms.date: 07/31/2020
ms.openlocfilehash: 897262dcdb8cbacd512f19823da375e2c603b97e
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/10/2020
ms.locfileid: "88037872"
---
# <a name="tutorial-create-a-video-analytics---object-and-motion-detection-application-in-azure-iot-central"></a>Zelfstudie: Een toepassing ‘Videoanalyse: object- en bewegingsdetectie’ maken in Azure IoT Central

Als ontwikkelaar van oplossingen leert u hoe u een videoanalysetoepassing maakt met de IoT Central-toepassingssjabloon *Videoanalyse: object- en bewegingsdetectie*, Azure IoT Edge-apparaten en Azure Media Services. Voor de oplossing wordt een detailhandel gebruikt om te laten zien hoe u voldoet aan de algemene bedrijfsbehoefte om beveiligingscamera’s te bewaken. De oplossing maakt gebruik van automatische objectdetectie in een videofeed om snel interessante gebeurtenissen te identificeren en zoeken.

De voorbeeldtoepassing bevat twee gesimuleerde apparaten en één IoT Edge-gateway. De volgende zelfstudies bieden twee benaderingen om te experimenteren met de mogelijkheden van de gateway en deze te begrijpen:

* Maak de IoT Edge-gateway in een Azure-VM en verbind een gesimuleerde camera.
* Maak de IoT Edge-gateway op een echt apparaat, zoals een Intel NUC, en verbind een echte camera.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * De Azure IoT Central-toepassingssjabloon voor videoanalyse gebruiken om een detailhandelstoepassing te maken
> * De toepassingsinstellingen aanpassen
> * Een apparaatsjabloon maken voor een IoT Edge-gatewayapparaat
> * Een gatewayapparaat toevoegen aan uw IoT Central-toepassing

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudiereeks hebt u het volgende nodig:

* Een Azure-abonnement. Als u geen Azure-abonnement hebt, kunt u er een maken via de [Azure-registratiepagina](https://aka.ms/createazuresubscription).
* Als u een echte camera gebruikt, moet er een verbinding zijn tussen het IoT Edge-apparaat en de camera en hebt u het kanaal **Real-Time Streaming Protocol** nodig.

## <a name="initial-setup"></a>Eerste configuratie

In deze zelfstudies gaat u een aantal configuratiebestanden bijwerken en gebruiken. Eerste versies van deze bestanden zijn beschikbaar in de GitHub-opslagplaats [LVA-gateway](https://github.com/Azure/live-video-analytics). De opslagplaats bevat ook een kladblokbestand dat u kunt downloaden en gebruiken om configuratiewaarden vast te leggen van de services die u implementeert.

Maak een map genaamd *lva-configuration* op uw lokale computer om exemplaren van deze bestanden op te slaan. Klik vervolgens met de rechtermuisknop op elk van de volgende koppelingen en kies **Opslaan als** om het bestand op te slaan in de map *lva-configuration*:

- [Scratchpad.txt](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/Scratchpad.txt)
- [deployment.amd64.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/deployment.amd64.json)
- [LvaEdgeGatewayDcm.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/LvaEdgeGatewayDcm.json)
- [state.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/state.json)

> [!NOTE]
> De GitHub-opslagplaats bevat ook de broncode voor de IoT Edge-modules **LvaEdgeGatewayModule** en **lvaYolov3**. Zie de [modules over het ontwikkelen van de LVA-gateway](tutorial-video-analytics-build-module.md) voor meer informatie over werken met de broncode.

## <a name="deploy-and-configure-azure-media-services"></a>Azure Media Services implementeren en configureren

De oplossing maakt gebruik van een Azure Media Services-account om de objectdetectievideoclips op te slaan die door het IoT Edge-gatewayapparaat worden gemaakt.

Wanneer u het Media Services-account maakt:

- U moet een accountnaam, een Azure-abonnement, een locatie, een resourcegroep en een opslagaccount opgeven. Maak een nieuw opslagaccount met behulp van de standaardinstellingen terwijl u het Media Services-account maakt.

- Kies de regio **VS - oost** als locatie.

- Maak een nieuwe resourcegroep genaamd *lva-rg* in de regio **VS - oost** voor het Media Services-account en het opslagaccount. Wanneer u klaar bent met de zelfstudies, kunt u alle resources gemakkelijk verwijderen door de resourcegroep *lva-rg* te verwijderen.

Maak het [Media Services-account in de Azure-portal](https://portal.azure.com/?r=1#create/Microsoft.MediaService).

> [!TIP]
> Deze zelfstudies maken in alle voorbeelden gebruik van de regio **VS - oost**. U kunt desgewenst uw dichtstbijzijnde regio gebruiken.

Noteer de naam van uw **Media Services**-account in het bestand *scratchpad.txt*.

Wanneer de implementatie is voltooid, navigeert u naar de pagina **Eigenschappen** voor uw **Media Services**-account. Noteer de **Resource-id** in het bestand *scratchpad.txt*. U gebruikt deze waarde later wanneer u de IoT Edge-module configureert.

Configureer vervolgens een Azure Active Directory-service-principal voor uw Media Services-resource. Selecteer **API-toegang** en vervolgens **Verificatie van service-principal**. Maak een nieuwe Azure Active Directory-app met dezelfde naam als uw Media Services-resource, en maak een geheim met de beschrijving *IoT Edge-toegang*.

:::image type="content" source="./media/tutorial-video-analytics-create-app/media-service-authentication.png" alt-text="AAD-app configureren voor AMS":::

Wanneer het geheim wordt gegenereerd, schuift u omlaag naar de sectie **Kopieer uw referenties om verbinding te maken met de service-principaltoepassing**. Selecteer vervolgens **JSON**. U kunt van hieruit alle referentiegegevens in één keer kopiëren. Noteer deze gegevens in het bestand *scratchpad.txt*. U gebruikt ze later wanneer u het IoT Edge-apparaat configureert.

> [!WARNING]
> Dit is uw enige kans om het geheim te bekijken en op te slaan. Als u het kwijtraakt, moet u een ander geheim genereren.

## <a name="create-the-azure-iot-central-application"></a>De Azure IoT Central-toepassing maken

In deze sectie maakt u een nieuwe Azure IoT Central-toepassing op basis van een sjabloon. U gebruikt deze toepassing in de rest van de zelfstudiereeks om een volledige oplossing te ontwikkelen.

Een nieuwe Azure IoT Central-toepassing maken:

1. Navigeer naar de website [Azure IoT Central-toepassingsbeheer](https://aka.ms/iotcentral).

1. Meld u aan met de referenties die u gebruikt om toegang te krijgen tot uw Azure-abonnement.

1. Als u wilt beginnen met het maken van een nieuwe Azure IoT Central-toepassing, selecteert u **Nieuwe toepassing** op de pagina **Ontwikkelen**.

1. Selecteer **Detailhandel**. De detailhandelspagina toont verschillende toepassingssjablonen voor detailhandels.

Een nieuwe toepassing voor videoanalyse maken:

1. Selecteer de toepassingssjabloon **Videoanalyse: object- en bewegingsdetectie**. Deze sjabloon bevat apparaatsjablonen voor de apparaten die in de zelfstudie worden gebruikt. De sjabloon biedt ook een operatordashboard om de video te bewaken.

1. Kies desgewenst een beschrijvende **Toepassingsnaam**. Deze toepassing is gebaseerd op een fictieve detailhandel genaamd Northwind Traders. In de zelfstudie wordt de **Toepassingsnaam** *Northwind Traders-videoanalyse* gebruikt.

    > [!NOTE]
    > Als u een beschrijvende **Toepassingsnaam** gebruikt, moet u nog steeds een unieke waarde voor de **Toepassings-URL** gebruiken.

1. Als u een Azure-abonnement hebt, selecteert u uw **Directory**, **Azure-abonnement** en **Verenigde Staten** als **Locatie**. Als u geen abonnement hebt, kunt u een **gratis proefversie van zeven dagen** inschakelen en de vereiste contactgegevens invullen. In deze zelfstudie worden drie apparaten gebruikt: twee camera’s en een IoT Edge-apparaat. Als u dus niet de gratis proefversie gebruikt, worden er kosten voor gebruik in rekening gebracht.

    Zie de [quickstart over het maken van een toepassing](../core/quick-deploy-iot-central.md) voor meer informatie over directory's, abonnementen en locaties.

1. Selecteer **Maken**.

    :::image type="content" source="./media/tutorial-video-analytics-create-app/new-application.png" alt-text="Pagina Toepassing maken van Azure IoT Central":::

### <a name="retrieve-the-configuration-data"></a>De configuratiegegevens ophalen

Later in deze zelfstudie, wanneer u de IoT Edge-gateway configureert, hebt u enkele configuratiegegevens uit de IoT Central-toepassing nodig. Het IoT Edge-apparaat heeft deze gegevens nodig om zich te registreren bij en verbinding te maken met de toepassing.

Selecteer in de sectie **Beheer** de optie **Uw toepassing** en noteer de **Toepassings-URL** en de **Toepassings-id** in het bestand *scratchpad.txt*:

:::image type="content" source="./media/tutorial-video-analytics-create-app/administration.png" alt-text="Beheer":::

Selecteer **API-tokens** en genereer een nieuw token genaamd **LVAEdgeToken** voor de rol **Operator**:

:::image type="content" source="./media/tutorial-video-analytics-create-app/token.png" alt-text="Token genereren":::

Noteer het token in het bestand *scratchpad.txt* voor later. Nadat het dialoogvenster is gesloten, kunt u het token niet meer bekijken.

Selecteer in de sectie **Beheer** de optie **Apparaatverbinding** en selecteer vervolgens **SAS-IoT-Devices**.

Noteer de **Primaire sleutel** voor apparaten in het bestand *scratchpad.txt*. U gebruikt dit *Shared Access Signature-token voor de primaire groep* later wanneer u het IoT Edge-apparaat configureert.

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

U kunt de Yolov3-module desgewenst vervangen door de hardwaregeoptimaliseerde [OpenVINO&trade; Model Server – Edge AI-uitbreidingsmodule](https://github.com/openvinotoolkit/model_server/tree/ams/extras/ams_wrapper) van Intel. U kunt een voorbeeld van een implementatiemanifest downloaden: [deployment.openvino.amd64.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/deployment.openvino.amd64.json). Dit manifest vervangt de configuratie-instellingen voor de `lvaYolov3`-module door de volgende configuratie:

```json
"OpenVINOModelServerEdgeAIExtensionModule": {
    "settings": {
        "image": "marketplace.azurecr.io/intel_corporation/open_vino",
        "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"4000/tcp\":[{\"HostPort\":\"4000\"}]}},\"Cmd\":[\"/ams_wrapper/start_ams.py\",\"--ams_port=4000\",\"--ovms_port=9000\"]}"
    },
    "type": "docker",
    "version": "1.0",
    "status": "running",
    "restartPolicy": "always"
}
```

## <a name="create-the-azure-iot-edge-gateway-device"></a>Het Azure IoT Edge-gatewayapparaat maken

De toepassing ‘Videoanalyse: object- en bewegingsdetectie’ bevat een **LVA Edge Object Detector**-apparaatsjabloon en een **LVA Edge Motion Detection**-apparaatsjabloon. In deze sectie maakt u een gatewayapparaatsjabloon met behulp van het implementatiemanifest en voegt u het gatewayapparaat aan uw IoT Central-toepassing toe.

### <a name="create-a-device-template-for-the-lva-edge-gateway"></a>Een apparaatsjabloon maken voor de LVA Edge Gateway

Het implementatiemanifest importeren en de **LVA Edge Gateway**-apparaatsjabloon maken:

1. Navigeer in uw IoT Central-toepassing naar **Apparaatsjablonen** en selecteer **+ Nieuw**.

1. Selecteer op de pagina **Sjabloontype selecteren** de tegel **Azure IoT Edge**. Selecteer vervolgens**Volgende: Aanpassen**.

1. Op de pagina **Een Azure IoT Edge-implementatiemanifest uploaden** voert u *LVA Edge Gateway* als de sjabloonnaam in en schakelt u **Gatewayapparaat met downstreamapparaten** in.

    Blader nog niet naar het implementatiemanifest. Als u dat doet, verwacht de implementatiewizard een interface voor elke module, maar u hoeft alleen de interface voor de **LvaEdgeGatewayModule** beschikbaar te stellen. U uploadt het manifest in een latere stap.

    :::image type="content" source="./media/tutorial-video-analytics-create-app/upload-deployment-manifest.png" alt-text="Implementatiemanifest niet uploaden":::

    Selecteer **Volgende: Review**.

1. Selecteer op de pagina **Beoordelen** de optie **Maken**.

### <a name="import-the-device-capability-model"></a>Het apparaatondersteuningsprofiel importeren

De apparaatsjabloon moet een apparaatondersteuningsprofiel bevatten. Selecteer op de pagina **LVA Edge Gateway** de tegel **Ondersteuningsprofiel importeren**. Navigeer naar de map *lva-configuration* die u eerder hebt gemaakt, en selecteer het bestand *LvaEdgeGatewayDcm.json*.

De apparaatsjabloon **LVA Edge Gateway** bevat nu de **LVA Edge Gateway-module** samen met drie interfaces: **Apparaatgegevens**, **LVA Edge Gateway-instellingen** en **LVA Edge Gateway-interface**.

### <a name="replace-the-manifest"></a>Het manifest vervangen

Selecteer op de pagina **LVA Edge Gateway** de optie **+ Manifest vervangen**.

:::image type="content" source="./media/tutorial-video-analytics-create-app/replace-manifest.png" alt-text="Manifest vervangen":::

Navigeer naar de map *lva-configuration* en selecteer het manifestbestand *deployment.amd64.json* dat u eerder hebt bewerkt. Selecteer **Uploaden**. Wanneer de validatie is voltooid, selecteert u **Vervangen**.

### <a name="add-relationships"></a>Relaties toevoegen

Selecteer in de apparaatsjabloon **LVA Edge Gateway** onder **Modules/LVA Edge Gateway-module** de optie **Relaties**. Selecteer **+ Relatie toevoegen** en voeg de volgende twee relaties toe:

|Weergavenaam               |Naam          |Doel |
|-------------------------- |------------- |------ |
|LVA Edge-bewegingsdetector   |Standaardwaarde gebruiken   |LVA Edge-bewegingsdetectorapparaat |
|LVA Edge-objectdetector   |Standaardwaarde gebruiken   |LVA Edge-objectdetectorapparaat |

Selecteer vervolgens **Opslaan**.

:::image type="content" source="media/tutorial-video-analytics-create-app/relationships.png" alt-text="Relaties toevoegen":::

### <a name="add-views"></a>Weergaven toevoegen

De apparaatsjabloon **LVA Edge Gateway** bevat geen weergavedefinities.

Een weergave toevoegen aan de sjabloon voor het apparaat:

1. Navigeer in de apparaatsjabloon **LVA Edge Gateway** naar **Weergaven** en selecteer de tegel **Het apparaat visualiseren**.

1. Voer *LVA Edge Gateway-apparaat* als de weergavenaam in.

1. Voeg de volgende tegels aan de weergave toe:

    * Een tegel met de **Apparaatgegevens**-eigenschappen: **Apparaatmodel**, **Fabrikant**, **Besturingssysteem**, **Processorarchitectuur**, **Softwareversie**, **Totaal geheugen** en **Totale opslagruimte**.
    * Een tegel Lijndiagram met de telemetriewaarden **Beschikbaar geheugen** en **Systeem-heartbeat**.
    * Een tegel Gebeurtenissengeschiedenis met de volgende gebeurtenissen: **Camera maken**, **Camera verwijderen**, **Module opnieuw opstarten**, **Module gestart**, **Module gestopt**.
    * Een 2x1 tegel Laatst bekende waarde met de telemetrie **IoT Central-clientstatus**.
    * Een 2x1 tegel Laatst bekende waarde met de telemetrie **Modulestatus**.
    * Een 1x1 tegel Laatst bekende waarde met de telemetrie **Systeem-heartbeat**.
    * Een 1x1 tegel Laatst bekende waarde met de telemetrie **Verbonden camera’s**.

    :::image type="content" source="media/tutorial-video-analytics-create-app/gateway-dashboard.png" alt-text="Dashboard":::

1. Selecteer **Opslaan**.

### <a name="publish-the-device-template"></a>De apparaatsjabloon publiceren

Voordat u een apparaat aan de toepassing kunt toevoegen, moet u de apparaatsjabloon publiceren:

1. Selecteer in de apparaatsjabloon **LVA Edge Gateway** de optie **Publiceren**.

1. Selecteer op de pagina **Deze apparaatsjabloon publiceren naar de toepassing** de optie **Publiceren**.

**LVA Edge Gateway** is nu beschikbaar als apparaattype voor gebruik op de pagina **Apparaten** in de toepassing.

## <a name="add-a-gateway-device"></a>Een gatewayapparaat toevoegen

Een **LVA Edge Gateway**-apparaat aan de toepassing toevoegen:

1. Navigeer naar de pagina **Apparaten** en selecteer de apparaatsjabloon **LVA Edge Gateway**.

1. Selecteer **+ Nieuw**.

1. In het dialoogvenster **Een nieuw apparaat maken** wijzigt u de apparaatnaam in *LVA Gateway 001* en wijzigt u de apparaat-id in *lva-gateway-001*.

    > [!NOTE]
    > De apparaat-id moet uniek zijn in de toepassing.

1. Selecteer **Maken**.

De apparaatstatus is **Geregistreerd**.

### <a name="get-the-device-credentials"></a>De apparaatreferenties ophalen

U hebt de referenties nodig waarmee het apparaat verbinding kan maken met uw IoT Central-toepassing. De apparaatreferenties ophalen:

1. Selecteer op de pagina **Apparaten** het apparaat **lva-gateway-001** dat u hebt gemaakt.

1. Selecteer **Verbinding maken**.

1. Op de pagina **Apparaatverbinding** noteert u in het bestand *scratchpad.txt* het **Id-bereik**, de **Apparaat-id** en de **Primaire sleutel** van het apparaat. U gebruikt deze waarden later.

1. Zorg dat de verbindingsmethode is ingesteld op **Shared Access Signature**.

1. Selecteer **Sluiten**.

## <a name="next-steps"></a>Volgende stappen

U hebt nu een IoT Central-toepassing gemaakt met behulp van de toepassingssjabloon **Videoanalyse: object- en bewegingsdetectie**, een apparaatsjabloon voor het gatewayapparaat gemaakt en een gatewayapparaat aan de toepassing toegevoegd.

Als u de toepassing ‘Videoanalyse: object- en bewegingsdetectie’ wilt uitproberen met behulp van IoT Edge-modules die een cloud-VM uitvoeren met gesimuleerde videostreams:

> [!div class="nextstepaction"]
> [Een IoT Edge-exemplaar voor videoanalyse maken (Linux-VM)](./tutorial-video-analytics-iot-edge-vm.md)

Als u de toepassing ‘Videoanalyse: object- en bewegingsdetectie’ wilt uitproberen met behulp van IoT Edge-modules die een echt apparaat uitvoeren met een echte **ONVIF**-camera:

> [!div class="nextstepaction"]
> [Een IoT Edge-exemplaar voor videoanalyse maken (Intel NUC)](./tutorial-video-analytics-iot-edge-nuc.md)
