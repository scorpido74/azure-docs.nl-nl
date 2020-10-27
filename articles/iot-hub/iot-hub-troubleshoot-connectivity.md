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
ms.custom:
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: Technical Support'
ms.openlocfilehash: b194812ef68820a0c310d0bac3b055360c5b5e4a
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92538422"
---
# <a name="monitor-diagnose-and-troubleshoot-disconnects-with-azure-iot-hub"></a>De verbinding met Azure IoT Hub controleren, vaststellen en problemen oplossen

Connectiviteits problemen voor IoT-apparaten kunnen lastig zijn om problemen op te lossen omdat er veel mogelijke storings punten zijn. Toepassings logica, fysieke netwerken, protocollen, hardware, IoT Hub en andere Cloud Services kunnen problemen veroorzaken. De mogelijkheid om de bron van een probleem te detecteren en te lokaliseren is essentieel. Een IoT-oplossing op schaal kan echter duizenden apparaten hebben, waardoor het niet praktisch is om afzonderlijke apparaten hand matig te controleren. Om u te helpen deze problemen op schaal te detecteren, op te sporen en op te lossen, gebruikt u de bewakings mogelijkheden IoT Hub via Azure Monitor. Deze mogelijkheden zijn beperkt tot wat IoT Hub kan observeren, dus we raden u ook aan de aanbevolen procedures voor uw apparaten en andere Azure-Services te volgen.

## <a name="get-alerts-and-error-logs"></a>Waarschuwingen en fouten logboeken ophalen

Gebruik Azure Monitor om waarschuwingen op te halen en logboeken te schrijven wanneer apparaten de verbinding verbreken.

### <a name="turn-on-logs"></a>Logboeken inschakelen

Als u de gebeurtenissen en fouten van een apparaat wilt vastleggen, maakt u een diagnostische instelling voor de [bron logboeken van IOT hub verbindingen](monitor-iot-hub-reference.md#connections). We raden u aan deze instelling zo snel mogelijk te maken, omdat deze logboeken niet standaard worden verzameld en zonder deze worden geen informatie over het oplossen van problemen met het apparaat verbroken wanneer deze zich voordoen.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Blader naar uw IoT-hub.

1. Selecteer **instellingen voor diagnostische gegevens** .

1. Selecteer **Diagnostische instelling toevoegen** .

1. Selecteer **verbindingen** Logboeken.

1. Voor een eenvoudige analyse selecteert **u verzenden naar log Analytics** ( [Zie prijzen](https://azure.microsoft.com/pricing/details/log-analytics/)). Zie het voor beeld onder [connectiviteits fouten oplossen](#resolve-connectivity-errors).

   ![Aanbevolen instellingen](./media/iot-hub-troubleshoot-connectivity/diagnostic-settings-recommendation.png)

Zie [IOT hub bewaken](monitor-iot-hub.md)voor meer informatie.

### <a name="set-up-alerts-for-device-disconnect-at-scale"></a>Waarschuwingen instellen voor het verbreken van het apparaat op schaal

Als u waarschuwingen wilt ontvangen wanneer apparaten de verbinding verbreken, configureert u waarschuwingen op de metrische gegevens van de **verbonden apparaten (preview-versie)** .

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Blader naar uw IoT-hub.

3. Selecteer **waarschuwingen** .

4. Selecteer **Nieuwe waarschuwingsregel** .

5. Selecteer **voor waarde toevoegen** en selecteer vervolgens verbonden apparaten (preview).

6. Stel drempelwaarde en waarschuwingen in door de volgende vragen te volgen.

Zie [Wat zijn waarschuwingen in Microsoft Azure?](../azure-monitor/platform/alerts-overview.md)voor meer informatie.

#### <a name="detecting-individual-device-disconnects"></a>De verbinding van afzonderlijke apparaten opsporen

Als u het verbreken van een *apparaat* wilt detecteren, bijvoorbeeld wanneer u wilt weten dat een fabriek offline is gegaan, configureert u de [verbrekings gebeurtenissen van het apparaat met Event grid](iot-hub-event-grid.md).

## <a name="resolve-connectivity-errors"></a>Connectiviteits fouten oplossen

Wanneer u Logboeken en waarschuwingen inschakelt voor verbonden apparaten, ontvangt u waarschuwingen wanneer er fouten optreden. In deze sectie wordt beschreven hoe u veelvoorkomende problemen kunt controleren wanneer u een waarschuwing ontvangt. In de onderstaande stappen wordt ervan uitgegaan dat u al een diagnostische instelling hebt gemaakt voor het verzenden van Logboeken voor IoT Hub verbindingen naar een Log Analytics-werk ruimte.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Blader naar uw IoT-hub.

1. Selecteer **Logboeken** .

1. Als u verbindings fouten logboeken wilt isoleren voor IoT Hub, voert u de volgende query in en selecteert u vervolgens **uitvoeren** :

    ```kusto
    AzureDiagnostics
    | where ( ResourceType == "IOTHUBS" and Category == "Connections" and Level == "Error")
    ```

1. Als er resultaten zijn, zoekt `OperationName` u naar, `ResultType` (fout code) en `ResultDescription` (fout bericht) om meer details te krijgen over de fout.

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

* Krijg hulp van [micro soft Q&een vraag pagina voor azure IOT hub](/answers/topics/azure-iot-hub.html), [stack overflow](https://stackoverflow.com/questions/tagged/azure-iot-hub)of [ondersteuning voor Azure](https://azure.microsoft.com/support/options/).

Als u de documentatie voor iedereen wilt verbeteren, kunt u in de sectie feedback hieronder een opmerking plaatsen als deze hand leiding u niet heeft geholpen.

## <a name="next-steps"></a>Volgende stappen

* Zie [tijdelijke fout afhandeling](/azure/architecture/best-practices/transient-faults)voor meer informatie over het oplossen van tijdelijke problemen.

* Zie [connectiviteit en betrouw bare berichten beheren met azure IOT hub apparaat-sdk's](iot-hub-reliability-features-in-sdks.md#connection-and-retry)voor meer informatie over de Azure IOT SDK en het beheren van nieuwe pogingen.