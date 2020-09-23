---
title: Servicestatusmeldingen bekijken met de Azure-portal
description: Bekijk uw service status meldingen in het Azure Portal. Service status meldingen worden door de Azure-infra structuur gepubliceerd in het Azure-activiteiten logboek.
ms.topic: conceptual
ms.date: 6/27/2019
ms.openlocfilehash: 615d08b6a04aef9e8ef2033154da8ff8caeebe04
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90967768"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Servicestatusmeldingen bekijken met de Azure-portal

Service status meldingen worden door de Azure-infra structuur gepubliceerd in het [Azure-activiteiten logboek](../azure-monitor/platform/platform-logs-overview.md).  De meldingen bevatten informatie over de resources in uw abonnement. Gezien het mogelijk grote volume aan gegevens dat in het activiteiten logboek is opgeslagen, is er een afzonderlijke gebruikers interface waarmee u waarschuwingen kunt bekijken en instellen voor service status meldingen. 

Service status meldingen kunnen informatie of actief zijn, afhankelijk van de klasse.

Zie [Eigenschappen van service status meldingen](service-health-notifications-properties.md)voor meer informatie over de verschillende klassen van service status meldingen.

## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>Bekijk uw service status meldingen in de Azure Portal

1. Selecteer in de [Azure Portal](https://portal.azure.com) **monitor**.

    ![Scherm opname van Azure Portal menu, waarbij monitor is geselecteerd](./media/service-notifications/home-monitor.png)

    Azure Monitor worden al uw bewakings instellingen en gegevens samen in één geconsolideerde weer gave. Als eerste wordt de sectie **Activiteitenlogboek** geopend.

1. Selecteer **waarschuwingen**.

    ![Scherm opname van het activiteiten logboek controleren, met geselecteerde waarschuwingen](./media/service-notifications/service-health-summary.png)

1. Selecteer **+ waarschuwing voor activiteiten logboek toevoegen**en stel een waarschuwing in om ervoor te zorgen dat u wordt gewaarschuwd voor toekomstige service meldingen. Zie [waarschuwingen voor activiteiten logboek maken op service meldingen](./alerts-activity-log-service-notifications-portal.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [waarschuwingen voor activiteiten logboeken](../azure-monitor/platform/activity-log-alerts.md).
