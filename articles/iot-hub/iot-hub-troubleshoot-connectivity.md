---
title: De verbindingen met Azure IoT Hub bewaken en oplossen
description: Meer informatie over het monitoren en oplossen van veelvoorkomende fouten met apparaatconnectiviteit voor Azure IoT Hub
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: jlian
ms.custom: mqtt
ms.openlocfilehash: 82139eef9708ff8d76e1087c71aa5445ba898385
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759605"
---
# <a name="monitor-diagnose-and-troubleshoot-disconnects-with-azure-iot-hub"></a>De verbinding met Azure IoT Hub bewaken, diagnosticeren en oplossen

Verbindingsproblemen voor IoT-apparaten kunnen moeilijk op te lossen zijn omdat er veel mogelijke uitvalpunten zijn. Toepassingslogica, fysieke netwerken, protocollen, hardware, IoT Hub en andere cloudservices kunnen allemaal problemen veroorzaken. De mogelijkheid om de bron van een probleem te detecteren en lokaliseren is van cruciaal belang. Een IoT-oplossing op schaal kan echter duizenden apparaten hebben, dus het is niet praktisch om afzonderlijke apparaten handmatig te controleren. Gebruik de bewakingsmogelijkheden die IoT Hub biedt via Azure Monitor om u te helpen deze problemen op schaal op te sporen, te diagnosticeren en op te lossen. Deze mogelijkheden zijn beperkt tot wat IoT Hub kan waarnemen, dus we raden u ook aan om best practices voor uw apparaten en andere Azure-services te volgen.

## <a name="get-alerts-and-error-logs"></a>Waarschuwingen en foutlogboeken ontvangen

Gebruik Azure Monitor om waarschuwingen te ontvangen en logboeken te schrijven wanneer apparaten de verbinding verbreken.

### <a name="turn-on-diagnostic-logs"></a>Diagnostische logboeken inschakelen

Als u gebeurtenissen en fouten voor apparaatverbinding wilt registreren, schakelt u diagnostische gegevens in voor IoT Hub. We raden u aan deze logboeken zo vroeg mogelijk in te schakelen, want als diagnostische logboeken niet zijn ingeschakeld, wanneer de verbinding met het apparaat wordt verbroken, hebt u geen informatie om het probleem op te lossen.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

2. Blader naar uw IoT-hub.

3. Selecteer **Diagnostische instellingen**.

4. Selecteer **Diagnostische gegevens inschakelen**.

5. Schakel **logboeken van verbindingen** in om te worden verzameld.

6. Schakel Send to **Log Analytics** [(zie prijzen)](https://azure.microsoft.com/pricing/details/log-analytics/)in voor een eenvoudigere analyse. Zie het voorbeeld onder [Verbindingsfouten oplossen](#resolve-connectivity-errors).

   ![Aanbevolen instellingen](./media/iot-hub-troubleshoot-connectivity/diagnostic-settings-recommendation.png)

Zie [De status van Azure IoT Hub controleren en problemen snel diagnosticeren.](iot-hub-monitor-resource-health.md)

### <a name="set-up-alerts-for-device-disconnect-at-scale"></a>Waarschuwingen instellen voor verbinding met het apparaat op schaal

Als u waarschuwingen wilt ontvangen wanneer apparaten de verbinding verbreken, configureert u waarschuwingen op de statistiek **Verbonden apparaten (voorbeeld).**

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

2. Blader naar uw IoT-hub.

3. Selecteer **Waarschuwingen**.

4. Selecteer **Nieuwe waarschuwingsregel**.

5. Selecteer **Voorwaarde toevoegen**en selecteer vervolgens 'Verbonden apparaten (voorbeeld)".

6. Stel drempelwaarde in en waarschuw er door aanwijzingen te volgen.

Zie [Wat zijn waarschuwingen in Microsoft Azure?](../azure-monitor/platform/alerts-overview.md)

#### <a name="detecting-individual-device-disconnects"></a>Het detecteren van afzonderlijke apparaatverbindingen

Als u de verbinding *per apparaat* wilt detecteren, bijvoorbeeld wanneer u wilt weten dat een fabriek net offline is gegaan, [configureert u de verbinding tussen apparaten met gebeurtenisraster.](iot-hub-event-grid.md)

## <a name="resolve-connectivity-errors"></a>Verbindingsfouten oplossen

Wanneer u diagnostische logboeken en waarschuwingen voor verbonden apparaten inschakelt, krijgt u meldingen wanneer er fouten optreden. In deze sectie wordt beschreven hoe u op zoek gaan naar veelvoorkomende problemen wanneer u een waarschuwing ontvangt. In de onderstaande stappen wordt ervan uitgegaan dat u Azure Monitor-logboeken hebt ingesteld voor uw diagnostische logboeken.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Blader naar uw IoT-hub.

1. Selecteer **Logboeken**.

1. Als u de logboeken van verbindingsfouten voor IoT Hub wilt isoleren, voert u de volgende query in en selecteert u **Uitvoeren:**

    ```kusto
    AzureDiagnostics
    | where ( ResourceType == "IOTHUBS" and Category == "Connections" and Level == "Error")
    ```

1. Als er resultaten zijn, zoekt `OperationName`u naar , `ResultType` (foutcode) en `ResultDescription` (foutbericht) om meer details over de fout te krijgen.

   ![Voorbeeld van foutlogboek](./media/iot-hub-troubleshoot-connectivity/diag-logs.png)

1. Volg de handleidingen voor probleemoplossing voor de meest voorkomende fouten:

    - **[404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)**
    - **[401003 IoTHubUnauthorized](iot-hub-troubleshoot-error-401003-iothubunauthorized.md)**
    - **[409002 LinkCreationConflict](iot-hub-troubleshoot-error-409002-linkcreationconflict.md)**
    - **[500001 ServerError](iot-hub-troubleshoot-error-500xxx-internal-errors.md)**
    - **[500008 Generieke Time-out](iot-hub-troubleshoot-error-500xxx-internal-errors.md)**

## <a name="i-tried-the-steps-but-they-didnt-work"></a>Ik probeerde de stappen, maar ze werkte niet

Als de vorige stappen niet hebben geholpen, probeert u:

* Als u toegang hebt tot de problematische apparaten, fysiek of op afstand (zoals SSH), volgt u de handleiding voor probleemoplossing aan de [apparaatzijde](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices) om probleemoplossing voort te zetten.

* Controleer of uw apparaten zijn **ingeschakeld** in de Azure-portal > uw IoT-hub > IoT-apparaten.

* Als uw apparaat het MQTT-protocol gebruikt, controleert u of poort 8883 is geopend. Zie [Verbinding maken met IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)voor meer informatie.

* Hulp van [Azure IoT Hub-forum,](https://social.msdn.microsoft.com/Forums/azure/home?forum=azureiothub) [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-iot-hub)of [Azure-ondersteuning.](https://azure.microsoft.com/support/options/)

Als deze handleiding je niet heeft geholpen om de documentatie voor iedereen te verbeteren, laat je een reactie achter in de onderstaande feedbacksectie.

## <a name="next-steps"></a>Volgende stappen

* Zie [Tijdelijke foutafhandeling](/azure/architecture/best-practices/transient-faults)voor meer informatie over het oplossen van tijdelijke problemen.

* Zie Hoe u connectiviteit en betrouwbare berichten [beheert met Azure IoT Hub-apparaat SDK's](iot-hub-reliability-features-in-sdks.md#connection-and-retry)voor meer informatie over Azure IoT SDK.
