---
title: Servicestatusmeldingen bekijken met de Azure-portal
description: Met servicestatusmeldingen u servicestatusberichten bekijken die zijn gepubliceerd door Microsoft Azure.
ms.topic: conceptual
ms.date: 6/27/2019
ms.openlocfilehash: 21416edc95d345eb183030a9b0cccb7529305d6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75748653"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Servicestatusmeldingen bekijken met de Azure-portal

Servicestatusmeldingen worden door de Azure-infrastructuur gepubliceerd in het [Azure-activiteitenlogboek](../azure-monitor/platform/platform-logs-overview.md).  De meldingen bevatten informatie over de bronnen onder uw abonnement. Gezien de mogelijk grote hoeveelheid informatie die in het activiteitenlogboek is opgeslagen, is er een aparte gebruikersinterface om het gemakkelijker te maken om waarschuwingen over servicestatusmeldingen te bekijken en in te stellen. 

Servicestatusmeldingen kunnen informatief of bruikbaar zijn, afhankelijk van de klasse.

Zie [Eigenschappen servicestatusmeldingen](service-health-notifications-properties.md)voor meer informatie over de verschillende klassen servicestatusmeldingen.

## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>Uw servicestatusmeldingen weergeven in de Azure-portal

1. Selecteer **Monitor**in de [Azure-portal](https://portal.azure.com).

    ![Schermafbeelding van het azure-portalmenu, met Monitor geselecteerd](./media/service-notifications/home-monitor.png)

    Azure Monitor brengt al uw bewakingsinstellingen en gegevens samen in één geconsolideerde weergave. Als eerste wordt de sectie **Activiteitenlogboek** geopend.

1. Selecteer **Waarschuwingen**.

    ![Schermafbeelding van het logboek Monitoractiviteit, met waarschuwingen geselecteerd](./media/service-notifications/service-health-summary.png)

1. Selecteer **+Waarschuwing voor activiteitenlogboek toevoegen**en stel een waarschuwing in om ervoor te zorgen dat u op de hoogte wordt gesteld van toekomstige servicemeldingen. Zie [Waarschuwingen voor activiteitenlogboeken maken op servicemeldingen voor](../azure-monitor/platform/alerts-activity-log-service-notifications.md)meer informatie.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over waarschuwingen voor [activiteitenlogboeken](../azure-monitor/platform/activity-log-alerts.md).
