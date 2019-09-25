---
title: Azure IoT Hub migreren naar Diagnostische instellingen | Microsoft Docs
description: Azure IoT Hub bijwerken om de diagnostische instellingen van Azure te gebruiken in plaats van bewerkingen voor bewaking om de status van bewerkingen op uw IoT-hub in realtime te controleren.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: kgremban
ms.openlocfilehash: 0feca8b477a1d76eae9e3f41de0026a33cc3249d
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71259342"
---
# <a name="migrate-your-iot-hub-from-operations-monitoring-to-diagnostics-settings"></a>Migreer uw IoT Hub van operations monitoring naar Diagnostische instellingen

Klanten die bewerkings [bewaking](iot-hub-operations-monitoring.md) gebruiken om de status van bewerkingen in IOT hub bij te houden, kunnen die werk stroom migreren naar de [Diagnostische instellingen van Azure](../azure-monitor/platform/resource-logs-overview.md), een functie van Azure monitor. Diagnostische instellingen geven diagnostische gegevens op resource niveau voor veel Azure-Services.

**De functionaliteit voor de bewaking van bewerkingen van IOT hub is afgeschaft**en is verwijderd uit de portal. In dit artikel worden de stappen beschreven voor het verplaatsen van de workloads van bewerkingen die worden bewaakt naar Diagnostische instellingen. Zie [uw Azure IOT-oplossingen bewaken met Azure monitor en Azure resource Health](https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health/)voor meer informatie over de tijd lijn van de afschaffing.

## <a name="update-iot-hub"></a>IoT Hub bijwerken

Als u uw IoT Hub in de Azure Portal wilt bijwerken, schakelt u eerst de instellingen voor diagnostische gegevens in en schakelt u de bewaking van bewerkingen uit.  

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="turn-off-operations-monitoring"></a>Bewaking van bewerkingen uitschakelen

> [!NOTE]
> Vanaf 11 maart 2019 wordt de functie voor het controleren van bewerkingen verwijderd uit de Azure Portal-interface van IoT Hub. De onderstaande stappen zijn niet meer van toepassing. Als u wilt migreren, moet u ervoor zorgen dat de juiste categorieën zijn ingeschakeld in Azure Monitor Diagnostische instellingen hierboven.

Wanneer u de nieuwe diagnostische instellingen in uw werk stroom hebt getest, kunt u de functie voor het controleren van bewerkingen uitschakelen. 

1. Selecteer in het menu IoT Hub de optie **bewerkingen bewaken**.

2. Selecteer onder elke bewakings categorie de optie **geen**.

3. Sla de wijzigingen van de operations-bewaking op.

## <a name="update-applications-that-use-operations-monitoring"></a>Toepassingen bijwerken die gebruikmaken van operations monitoring

De schema's voor de instellingen voor het bewaken van bewerkingen en diagnostische gegevens variëren enigszins. Het is belang rijk dat u de toepassingen die vandaag gebruikmaken van operations monitoring, bijwerkt om toe te wijzen aan het schema dat wordt gebruikt door de diagnostische instellingen. 

Daarnaast biedt de diagnostische instellingen vijf nieuwe categorieën voor het bijhouden van gegevens. Nadat u toepassingen voor het bestaande schema hebt bijgewerkt, voegt u ook de nieuwe categorieën toe:

* Dubbele bewerkingen van het Cloud naar het apparaat
* Dubbele bewerkingen van het apparaat naar de Cloud
* Dubbele query's
* Taakbewerkingen
* Directe methoden

Zie [inzicht in het schema voor Diagnostische instellingen](iot-hub-monitor-resource-health.md#understand-the-logs)voor de specifieke schema structuren.

## <a name="monitoring-device-connect-and-disconnect-events-with-low-latency"></a>Bewaking van het apparaat verbinding maken en verbreken van gebeurtenissen met een lage latentie

Als u verbinding met het apparaat wilt bewaken en de gebeurtenissen in de productie verbinding wilt verbreken, kunt u het beste abonneren op de [ **verbroken** gebeurtenis](iot-hub-event-grid.md#event-types) van het apparaat op Event grid om waarschuwingen op te halen en de verbindings status van het apparaat te controleren. Gebruik deze [zelf studie](iot-hub-how-to-order-connection-state-events.md) voor meer informatie over het integreren van met het apparaat verbonden en verbroken gebeurtenissen van het apparaat van IOT hub in uw IOT-oplossing.

## <a name="next-steps"></a>Volgende stappen

[De status van Azure IoT Hub bewaken en snel problemen vaststellen](iot-hub-monitor-resource-health.md)
