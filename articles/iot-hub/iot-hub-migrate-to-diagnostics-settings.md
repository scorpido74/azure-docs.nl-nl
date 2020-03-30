---
title: Azure IoT Hub migreren naar diagnostische instellingen | Microsoft Documenten
description: Azure IoT Hub bijwerken om Azure-diagnostische instellingen te gebruiken in plaats van bedrijfscontrole om de status van bewerkingen op uw IoT-hub in realtime te controleren.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: kgremban
ms.openlocfilehash: ab07da38c01b052a4220274fb059683a22950a3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75750695"
---
# <a name="migrate-your-iot-hub-from-operations-monitoring-to-diagnostics-settings"></a>Uw IoT-hub migreren van operationsmonitoring naar diagnostische instellingen

Klanten die [bedrijfsbewaking](iot-hub-operations-monitoring.md) gebruiken om de status van bewerkingen in IoT Hub bij te houden, kunnen die werkstroom migreren naar [Azure-diagnostische instellingen](../azure-monitor/platform/platform-logs-overview.md), een functie van Azure Monitor. Diagnostische instellingen leveren diagnostische informatie op resourceniveau voor veel Azure-services.

**De operationele monitoringfunctionaliteit van IoT Hub is afgeschaft**en is verwijderd uit de portal. In dit artikel vindt u stappen om uw workloads te verplaatsen van operations monitoring naar diagnostische instellingen. Zie [Uw Azure IoT-oplossingen met Azure Monitor en Azure Resource Health controleren](https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health/)voor meer informatie over de afschrijvingstijdlijn.

## <a name="update-iot-hub"></a>IoT-hub bijwerken

Als u uw IoT-hub wilt bijwerken in de Azure-portal, schakelt u eerst diagnostische instellingen in en schakelt u de bewaking van bewerkingen uit.  

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="turn-off-operations-monitoring"></a>Bedrijfscontrole uitschakelen

> [!NOTE]
> Vanaf 11 maart 2019 wordt de functie voor bedrijfsbewaking verwijderd uit de Azure-portalinterface van IoT Hub. De onderstaande stappen zijn niet meer van toepassing. Als u wilt migreren, controleert u of de juiste categorieën zijn ingeschakeld in de diagnostische instellingen van Azure Monitor hierboven.

Zodra u de nieuwe diagnostische instellingen in uw werkstroom hebt getest, u de functie voor bedrijfscontrole uitschakelen. 

1. Selecteer in het menu Van de IoT-hub de optie **Operations-bewaking**.

2. Selecteer Onder elke controlecategorie **Geen**.

3. Sla de wijzigingen voor het bewaken van bewerkingen op.

## <a name="update-applications-that-use-operations-monitoring"></a>Toepassingen bijwerken die bedrijfsbewaking gebruiken

De schema's voor operationele monitoring en diagnostische instellingen variëren enigszins. Het is belangrijk dat u de toepassingen bijwerkt die de bewaking van bewerkingen vandaag gebruiken om het schema in kaart te brengen dat wordt gebruikt door diagnostische instellingen. 

Ook biedt diagnostische instellingen vijf nieuwe categorieën voor het bijhouden. Nadat u toepassingen voor het bestaande schema hebt bijgewerkt, voegt u ook de nieuwe categorieën toe:

* Cloud-to-device twin operations
* Dubbele bewerkingen van device-to-cloud
* Dubbele query's
* Taakbewerkingen
* Directe methoden

Zie [Het schema voor diagnostische instellingen begrijpen voor](iot-hub-monitor-resource-health.md#understand-the-logs)de specifieke schemastructuren .

## <a name="monitoring-device-connect-and-disconnect-events-with-low-latency"></a>Controleapparaat sluit gebeurtenissen aan en koppelt deze los met lage latentie

Als u de verbinding tussen apparaten wilt controleren en gebeurtenissen in productie wilt verbreken, raden we u aan u te abonneren op de [ **verbinding tussen** ](iot-hub-event-grid.md#event-types) het apparaat en gebeurtenis op Gebeurtenisraster om waarschuwingen te ontvangen en de status van de apparaatverbinding te controleren. Gebruik deze [zelfstudie](iot-hub-how-to-order-connection-state-events.md) voor meer informatie over het integreren van door apparaten verbonden en apparaatverbroken van IoT Hub in uw IoT-oplossing.

## <a name="next-steps"></a>Volgende stappen

[De status van Azure IoT Hub bewaken en snel problemen vaststellen](iot-hub-monitor-resource-health.md)
