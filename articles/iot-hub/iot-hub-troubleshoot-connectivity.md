---
title: Problemen met Azure IoT Hub vaststellen en oplossen
description: Meer informatie over het vaststellen en oplossen van veelvoorkomende fouten met connectiviteit van apparaten voor Azure IoT Hub
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/19/2018
ms.author: jlian
ms.openlocfilehash: 3904c6390cfe8de197bae470c4ae32d22605ae6a
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70801428"
---
# <a name="detect-and-troubleshoot-disconnects-with-azure-iot-hub"></a>Problemen met Azure IoT Hub detecteren en oplossen

Connectiviteits problemen voor IoT-apparaten kunnen lastig zijn om problemen op te lossen omdat er veel mogelijke storings punten zijn. Toepassings logica van apparaten, fysieke netwerken, protocollen, hardware en Azure-IoT Hub kunnen alle problemen veroorzaken. In dit artikel vindt u aanbevelingen voor het detecteren en oplossen van problemen met connectiviteit vanuit de Cloud kant (in plaats van aan de zijkant van het apparaat).

## <a name="get-alerts-and-error-logs"></a>Waarschuwingen en fouten logboeken ophalen

Gebruik Azure Monitor om waarschuwingen op te halen en logboeken te schrijven wanneer de verbinding met het apparaat wordt verwijderd.

### <a name="turn-on-diagnostic-logs"></a>Logboeken met diagnostische gegevens inschakelen

Schakel diagnostische gegevens in voor IoT Hub om de gebeurtenissen en fouten van het apparaat vast te leggen.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Blader naar uw IoT-hub.

3. Selecteer **instellingen voor diagnostische gegevens**.

4. Selecteer **diagnostische gegevens inschakelen**.

5. De logboeken voor het verzamelen van **verbindingen** inschakelen.

6. Schakel **Send to log Analytics** ([Zie prijzen](https://azure.microsoft.com/pricing/details/log-analytics/)) in om de analyse te vergemakkelijken. Zie het voor beeld onder [connectiviteits fouten oplossen](#resolve-connectivity-errors).

   ![Aanbevolen instellingen](./media/iot-hub-troubleshoot-connectivity/diagnostic-settings-recommendation.png)

Zie [de status van Azure IOT hub controleren en problemen snel](iot-hub-monitor-resource-health.md)oplossen voor meer informatie.

### <a name="set-up-alerts-for-the-_connected-devices_-count-metric"></a>Waarschuwingen instellen voor de metriek van het aantal _verbonden apparaten_

Als u waarschuwingen wilt ontvangen wanneer apparaten de verbinding verbreken, configureert u waarschuwingen op de metrische gegevens van de **verbonden apparaten (preview-versie)** .

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Blader naar uw IoT-hub.

3. Selecteer **waarschuwingen**.

4. Selecteer **nieuwe waarschuwings regel**.

5. Selecteer **voor waarde toevoegen**en selecteer vervolgens verbonden apparaten (preview).

6. Voltooi de instelling van de gewenste drempel waarden en waarschuwings opties door de volgende vragen te volgen.

Zie [Wat zijn klassieke waarschuwingen in Microsoft Azure?](../azure-monitor/platform/alerts-overview.md)voor meer informatie.

## <a name="resolve-connectivity-errors"></a>Connectiviteits fouten oplossen

Wanneer u Diagnostische logboeken en waarschuwingen voor verbonden apparaten inschakelt, ontvangt u waarschuwingen wanneer er fouten optreden. In deze sectie wordt beschreven hoe u veelvoorkomende problemen kunt oplossen wanneer u een waarschuwing ontvangt. In de onderstaande stappen wordt ervan uitgegaan dat u Azure Monitor logboeken voor uw Diagnostische logboeken hebt ingesteld.

1. Ga naar uw werk ruimte voor **log Analytics** in het Azure Portal.

2. Selecteer **logboek zoeken**.

3. Als u verbindings fouten logboeken wilt isoleren voor IoT Hub, voert u de volgende query in en selecteert u vervolgens **uitvoeren**:

    ```kusto
    search *
    | where ( Type == "AzureDiagnostics" and ResourceType == "IOTHUBS")
    | where ( Category == "Connections" and Level == "Error")
    ```

1. Als er resultaten zijn, zoekt `OperationName`u naar, `ResultType` (fout code) en `ResultDescription` (fout bericht) om meer details te krijgen over de fout.

   ![Voor beeld van een fouten logboek](./media/iot-hub-troubleshoot-connectivity/diag-logs.png)

2. Gebruik deze tabel om veelvoorkomende fouten te begrijpen en op te lossen.

    | Fout | Hoofdoorzaak | Oplossing |
    |-------|------------|------------|
    | 404104 DeviceConnectionClosedRemotely | De verbinding is gesloten door het apparaat, maar IoT Hub weet niet waarom. Veelvoorkomende oorzaken zijn MQTT/AMQP time-out en Internet connectiviteits verlies. | Controleer of het apparaat verbinding kan maken met IoT Hub door [de verbinding te testen](tutorial-connectivity.md). Als de verbinding nauw keurig is, maar het apparaat wordt met tussen tijd verbroken, moet u ervoor zorgen dat u de juiste Keep Alive-apparaat-logica implementeert voor uw keuze van Protocol (MQTT/AMPQ). |
    | 401003 IoTHubUnauthorized | IoT Hub kan de verbinding niet verifiëren. | Zorg ervoor dat de SAS of het beveiligings token dat u gebruikt, niet is verlopen. [Azure IOT sdk's](iot-hub-devguide-sdks.md) genereren automatisch tokens zonder dat hiervoor speciale configuratie is vereist. |
    | 409002 LinkCreationConflict | Een apparaat heeft meer dan een verbinding. Wanneer een nieuwe verbindings aanvraag voor een apparaat wordt geleverd, wordt het vorige door IoT Hub met deze fout gesloten. | In het meest voorkomende geval detecteert een apparaat een verbreking en probeert de verbinding opnieuw tot stand te brengen, maar IoT Hub wordt het apparaat nog steeds beschouwd als een verbinding. IoT Hub sluit de vorige verbinding en wordt deze fout vastgelegd. Deze fout wordt doorgaans weer gegeven als een neven effect van een ander, tijdelijk probleem, dus zoek naar andere fouten in de logboeken om de problemen verder op te lossen. Anders moet u ervoor zorgen dat u alleen een nieuwe verbindings aanvraag uitgeeft als de verbinding wordt verbroken. |
    | 500001 ServerError | Er is IoT Hub een probleem aan de server zijde opgetreden. Waarschijnlijk is het probleem tijdelijk. Hoewel het IoT Hub-team moeilijk werkt om [de sla](https://azure.microsoft.com/support/legal/sla/iot-hub/)te onderhouden, kunnen kleine subsets van IOT hub knoop punten af en toe tijdelijke fouten ondervinden. Wanneer het apparaat probeert verbinding te maken met een knoop punt dat problemen ondervindt, wordt deze fout weer gegeven. | Als u de tijdelijke fout wilt verhelpen, geeft u een nieuwe poging vanaf het apparaat. Als u [nieuwe pogingen automatisch wilt beheren](iot-hub-reliability-features-in-sdks.md#connection-and-retry), moet u ervoor zorgen dat u de meest recente versie van de [Azure IOT sdk's](iot-hub-devguide-sdks.md)gebruikt.<br><br>Zie [tijdelijke fout afhandeling](/azure/architecture/best-practices/transient-faults)voor Best Practice van tijdelijke fout afhandeling en nieuwe pogingen.  <br><br>Als het probleem zich na nieuwe pogingen blijft voordoen, controleert u [resource Health](iot-hub-monitor-resource-health.md#use-azure-resource-health) en de [status van Azure](https://azure.microsoft.com/status/history/) om na te gaan of er een bekend probleem is met IOT hub. Als er geen bekende problemen zijn en het probleem blijft bestaan, [neemt u contact op met de ondersteuning](https://azure.microsoft.com/support/options/) voor verdere onderzoek. |
    | 500008 GenericTimeout | IoT Hub kan de verbindings aanvraag niet volt ooien voordat er een time-out optreedt. Net als een 500001 server error is deze fout waarschijnlijk tijdelijk. | Volg de stappen voor probleem oplossing voor een 500001-server error naar de hoofd oorzaak en los deze fout op.|

## <a name="other-steps-to-try"></a>Andere stappen om te proberen

Als de vorige stappen niet kunnen worden uitgevoerd, kunt u het volgende proberen:

* Als u toegang hebt tot de problematische apparaten, fysiek of extern (zoals SSH), volgt u de [probleem Oplosser](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices) voor het oplossen van problemen door te gaan met het oplossen van het probleem.

* Controleer of uw apparaten zijn **ingeschakeld** in de Azure Portal > uw IOT-hub > IOT-apparaten.

* Krijg hulp van [azure IOT hub forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azureiothub), [stack overflow](https://stackoverflow.com/questions/tagged/azure-iot-hub)of [ondersteuning voor Azure](https://azure.microsoft.com/support/options/).

Als u de documentatie voor iedereen wilt verbeteren, kunt u in de sectie feedback hieronder een opmerking plaatsen als deze hand leiding u niet heeft geholpen.

## <a name="next-steps"></a>Volgende stappen

* Zie [tijdelijke fout afhandeling](/azure/architecture/best-practices/transient-faults)voor meer informatie over het oplossen van tijdelijke problemen.

* Zie [connectiviteit en betrouw bare berichten beheren met azure IOT hub apparaat-sdk's](iot-hub-reliability-features-in-sdks.md#connection-and-retry)voor meer informatie over de Azure IOT SDK en het beheren van nieuwe pogingen.
