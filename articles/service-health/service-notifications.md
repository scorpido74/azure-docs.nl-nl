---
title: Servicestatusmeldingen bekijken met de Azure-portal
description: Met service status meldingen kunt u service status berichten weer geven die zijn gepubliceerd door Microsoft Azure.
ms.topic: conceptual
ms.date: 6/27/2019
ms.openlocfilehash: 21416edc95d345eb183030a9b0cccb7529305d6d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "75748653"
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

1. Selecteer **+ waarschuwing voor activiteiten logboek toevoegen**en stel een waarschuwing in om ervoor te zorgen dat u wordt gewaarschuwd voor toekomstige service meldingen. Zie [waarschuwingen voor activiteiten logboek maken op service meldingen](../azure-monitor/platform/alerts-activity-log-service-notifications.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [waarschuwingen voor activiteiten logboeken](../azure-monitor/platform/activity-log-alerts.md).
