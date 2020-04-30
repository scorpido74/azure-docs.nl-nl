---
title: De verbinding met Azure IoT Hub controleren en problemen oplossen
description: Meer informatie over het controleren en oplossen van veelvoorkomende fouten met connectiviteit van apparaten voor Azure IoT Hub
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: jlian
ms.custom: mqtt
ms.openlocfilehash: 82139eef9708ff8d76e1087c71aa5445ba898385
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81759605"
---
# <a name="monitor-diagnose-and-troubleshoot-disconnects-with-azure-iot-hub"></a>De verbinding met Azure IoT Hub controleren, vaststellen en problemen oplossen

Connectiviteits problemen voor IoT-apparaten kunnen lastig zijn om problemen op te lossen omdat er veel mogelijke storings punten zijn. Toepassings logica, fysieke netwerken, protocollen, hardware, IoT Hub en andere Cloud Services kunnen problemen veroorzaken. De mogelijkheid om de bron van een probleem te detecteren en te lokaliseren is essentieel. Een IoT-oplossing op schaal kan echter duizenden apparaten hebben, waardoor het niet praktisch is om afzonderlijke apparaten hand matig te controleren. Om u te helpen deze problemen op schaal te detecteren, op te sporen en op te lossen, gebruikt u de bewakings mogelijkheden IoT Hub via Azure Monitor. Deze mogelijkheden zijn beperkt tot wat IoT Hub kan observeren, dus we raden u ook aan de aanbevolen procedures voor uw apparaten en andere Azure-Services te volgen.

## <a name="get-alerts-and-error-logs"></a>Waarschuwingen en fouten logboeken ophalen

Gebruik Azure Monitor om waarschuwingen op te halen en logboeken te schrijven wanneer apparaten de verbinding verbreken.

### <a name="turn-on-diagnostic-logs"></a>Diagnostische logboeken inschakelen

Schakel diagnostische gegevens in voor IoT Hub om de gebeurtenissen en fouten van het apparaat vast te leggen. We raden u aan deze logboeken zo snel mogelijk in te scha kelen, want als de diagnostische logboeken niet zijn ingeschakeld, hebt u geen informatie om het probleem op te lossen.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

2. Blader naar uw IoT-hub.

3. Selecteer **instellingen voor diagnostische gegevens**.

4. Selecteer **Diagnostische gegevens inschakelen**.

5. De logboeken voor het verzamelen van **verbindingen** inschakelen.

6. Schakel **Send to log Analytics** ([Zie prijzen](https://azure.microsoft.com/pricing/details/log-analytics/)) in om de analyse te vergemakkelijken. Zie het voor beeld onder [connectiviteits fouten oplossen](#resolve-connectivity-errors).

   ![Aanbevolen instellingen](./media/iot-hub-troubleshoot-connectivity/diagnostic-settings-recommendation.png)

Zie [de status van Azure IOT hub controleren en problemen snel](iot-hub-monitor-resource-health.md)oplossen voor meer informatie.

### <a name="set-up-alerts-for-device-disconnect-at-scale"></a>Waarschuwingen instellen voor het verbreken van het apparaat op schaal

Als u waarschuwingen wilt ontvangen wanneer apparaten de verbinding verbreken, configureert u waarschuwingen op de metrische gegevens van de **verbonden apparaten (preview-versie)** .

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

2. Blader naar uw IoT-hub.

3. Selecteer **waarschuwingen**.

4. Selecteer **Nieuwe waarschuwingsregel**.

5. Selecteer **voor waarde toevoegen**en selecteer vervolgens verbonden apparaten (preview).

6. Stel drempelwaarde en waarschuwingen in door de volgende vragen te volgen.

Zie [Wat zijn waarschuwingen in Microsoft Azure?](../azure-monitor/platform/alerts-overview.md)voor meer informatie.

#### <a name="detecting-individual-device-disconnects"></a>De verbinding van afzonderlijke apparaten opsporen

Als u het verbreken van een *apparaat* wilt detecteren, bijvoorbeeld wanneer u wilt weten dat een fabriek offline is gegaan, configureert u de [verbrekings gebeurtenissen van het apparaat met Event grid](iot-hub-event-grid.md).

## <a name="resolve-connectivity-errors"></a>Connectiviteits fouten oplossen

Wanneer u Diagnostische logboeken en waarschuwingen voor verbonden apparaten inschakelt, ontvangt u waarschuwingen wanneer er fouten optreden. In deze sectie wordt beschreven hoe u veelvoorkomende problemen kunt controleren wanneer u een waarschuwing ontvangt. In de onderstaande stappen wordt ervan uitgegaan dat u Azure Monitor logboeken voor uw Diagnostische logboeken hebt ingesteld.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Blader naar uw IoT-hub.

1. Selecteer **Logboeken**.

1. Als u verbindings fouten logboeken wilt isoleren voor IoT Hub, voert u de volgende query in en selecteert u vervolgens **uitvoeren**:

    ```kusto
    AzureDiagnostics
    | where ( ResourceType == "IOTHUBS" and Category == "Connections" and Level == "Error")
    ```

1. Als er resultaten zijn, zoekt u `OperationName`naar `ResultType` , (fout code) en `ResultDescription` (fout bericht) om meer details te krijgen over de fout.

   ![Voor beeld van een fouten logboek](./media/iot-hub-troubleshoot-connectivity/diag-logs.png)

1. Volg de richt lijnen voor probleem oplossing voor de meest voorkomende fouten:

    - **[404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)**
    - **[401003 IoTHubUnauthorized](iot-hub-troubleshoot-error-401003-iothubunauthorized.md)**
    - **[409002 LinkCreationConflict](iot-hub-troubleshoot-error-409002-linkcreationconflict.md)**
    - **[500001 ServerError](iot-hub-troubleshoot-error-500xxx-internal-errors.md)**
    - **[500008 GenericTimeout](iot-hub-troubleshoot-error-500xxx-internal-errors.md)**

## <a name="i-tried-the-steps-but-they-didnt-work"></a>Ik heb de stappen uitgevoerd, maar deze zijn niet uitgevoerd

Als de vorige stappen niet zijn uitgevoerd, probeert u het volgende:

* Als u toegang hebt tot de problematische apparaten, fysiek of extern (zoals SSH), volgt u de [probleem Oplosser](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices) voor het oplossen van problemen door te gaan met het oplossen van het probleem.

* Controleer of uw apparaten zijn **ingeschakeld** in de Azure Portal > uw IOT-hub > IOT-apparaten.

* Als uw apparaat gebruikmaakt van het MQTT-protocol, controleert u of poort 8883 is geopend. Zie [verbinding maken met IOT hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)voor meer informatie.

* Krijg hulp van [azure IOT hub forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azureiothub), [stack overflow](https://stackoverflow.com/questions/tagged/azure-iot-hub)of [ondersteuning voor Azure](https://azure.microsoft.com/support/options/).

Als u de documentatie voor iedereen wilt verbeteren, kunt u in de sectie feedback hieronder een opmerking plaatsen als deze hand leiding u niet heeft geholpen.

## <a name="next-steps"></a>Volgende stappen

* Zie [tijdelijke fout afhandeling](/azure/architecture/best-practices/transient-faults)voor meer informatie over het oplossen van tijdelijke problemen.

* Zie [connectiviteit en betrouw bare berichten beheren met azure IOT hub apparaat-sdk's](iot-hub-reliability-features-in-sdks.md#connection-and-retry)voor meer informatie over de Azure IOT SDK en het beheren van nieuwe pogingen.
