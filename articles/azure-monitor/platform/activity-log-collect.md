---
title: Azure-activiteiten Logboeken in Log Analytics werk ruimte verzamelen en analyseren | Microsoft Docs
description: Verzamel het Azure-activiteiten logboek in Azure Monitor logboeken en gebruik de bewakings oplossing om het Azure-activiteiten logboek te analyseren en doorzoeken op al uw Azure-abonnementen.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/30/2019
ms.openlocfilehash: 07062ea9ce10b99e0f03a66247bb97795b45aedc
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74212594"
---
# <a name="collect-and-analyze-azure-activity-logs-in-log-analytics-workspace-in-azure-monitor"></a>Azure-activiteiten Logboeken in Log Analytics werk ruimte in Azure Monitor verzamelen en analyseren

> [!NOTE]
> U kunt het activiteiten logboek nu verzamelen in een Log Analytics-werk ruimte met behulp van een diagnostische instelling, vergelijkbaar met de manier waarop u bron logboeken verzamelt. Zie [Azure-activiteiten logboeken verzamelen en analyseren in log Analytics werk ruimte in azure monitor](diagnostic-settings-subscription.md).

Het [Azure-activiteiten logboek](activity-logs-overview.md) biedt inzicht in gebeurtenissen op abonnements niveau die in uw Azure-abonnement zijn opgetreden. In dit artikel wordt beschreven hoe u het activiteiten logboek in een Log Analytics-werk ruimte verzamelt en hoe u de Analyse van activiteitenlogboek [bewakings oplossing](../insights/solutions.md)gebruikt, waarmee logboek query's en-weer gaven worden geboden voor het analyseren van deze gegevens. 

Het verbinden van het activiteiten logboek met een Log Analytics-werk ruimte biedt de volgende voor delen:

- Het activiteiten logboek van meerdere Azure-abonnementen samen voegen tot één locatie voor analyse.
- Vermeldingen in het activiteiten logboek worden langer dan 90 dagen bewaard.
- Correleer activiteiten logboek gegevens met andere bewakings gegevens die zijn verzameld door Azure Monitor.
- Gebruik [logboek query's](../log-query/log-query-overview.md) om complexe analyses uit te voeren en uitgebreide inzichten te verkrijgen over activiteiten logboek vermeldingen.

## <a name="connect-to-log-analytics-workspace"></a>Verbinding maken met Log Analytics werk ruimte
Eén werk ruimte kan worden gekoppeld aan het activiteiten logboek voor meerdere abonnementen in dezelfde Azure-Tenant. Zie [Azure-activiteiten logboeken verzamelen in een log Analytics-werk ruimte voor het verzamelen van gegevens over meerdere tenants in verschillende Azure Active Directory tenants](activity-log-collect-tenants.md).

> [!IMPORTANT]
> Er kan een fout met de volgende procedure worden weer gegeven als de resource providers micro soft. OperationalInsights en micro soft. OperationsManagement niet zijn geregistreerd voor uw abonnement. Zie [Azure-resource providers en-typen](../../azure-resource-manager/resource-manager-supported-services.md) om deze providers te registreren.

Gebruik de volgende procedure om het activiteiten logboek te verbinden met uw Log Analytics-werk ruimte:

1. Selecteer in het menu **log Analytics werk ruimten** in de Azure Portal de werk ruimte om het activiteiten logboek te verzamelen.
1. Selecteer in de sectie **werkruimte gegevens bronnen** van het menu van de werk ruimte **Azure-activiteiten logboek**.
1. Klik op het abonnement dat u wilt verbinden.

    ![Workspaces](media/activity-log-export/workspaces.png)

1. Klik op **verbinden** om het activiteiten logboek in het abonnement te verbinden met de geselecteerde werk ruimte. Als het abonnement al is verbonden met een andere werk ruimte, klikt u eerst op **verbinding verbreken** om de verbinding te verbreken.

    ![Werk ruimten verbinden](media/activity-log-export/connect-workspace.png)

## <a name="analyze-in-log-analytics-workspace"></a>In Log Analytics werk ruimte analyseren
Wanneer u een activiteiten logboek verbindt met een Log Analytics-werk ruimte, worden er items naar de werk ruimte geschreven in een tabel met de naam **AzureActivity** die u met een [logboek query](../log-query/log-query-overview.md)kunt ophalen. De structuur van deze tabel is afhankelijk van de [categorie logboek vermelding](activity-logs-overview.md#categories-in-the-activity-log). Zie [Azure activiteiten logboek gebeurtenis schema](activity-log-schema.md) voor een beschrijving van elke categorie.

## <a name="activity-logs-analytics-monitoring-solution"></a>Bewakings oplossing voor activiteiten logboeken
De Azure Log Analytics-bewakings oplossing bevat meerdere logboek query's en-weer gaven voor het analyseren van de records in het activiteiten logboek in uw Log Analytics-werk ruimte.

### <a name="install-the-solution"></a>De oplossing installeren
Gebruik de procedure in [een bewakings oplossing installeren](../insights/solutions.md#install-a-monitoring-solution) om de **analyse van activiteitenlogboek** -oplossing te installeren. Er is geen aanvullende configuratie vereist.

### <a name="use-the-solution"></a>De oplossing gebruiken
Bewakings oplossingen zijn toegankelijk via het menu **monitor** in de Azure Portal. Selecteer **meer** in het gedeelte **inzichten** om de **overzichts** pagina te openen met de oplossings tegels. De tegel **activiteiten logboeken van Azure** bevat een telling van het aantal **AzureActivity** -records in uw werk ruimte.

![Azure-activiteitenlogboeken tegel](media/collect-activity-logs/azure-activity-logs-tile.png)


Klik op de tegel **Azure-activiteiten logboeken** om de weer gave **Azure-activiteiten logboeken** te openen. De weer gave bevat de visualisatie onderdelen in de volgende tabel. Elk onderdeel bevat Maxi maal 10 items die overeenkomen met de criteria van die onderdelen voor het opgegeven tijds bereik. U kunt een logboek query uitvoeren waarmee alle overeenkomende records worden geretourneerd door te klikken op **Alles bekijken** onder aan het onderdeel.

![Azure-activiteitenlogboeken-dashboard](media/collect-activity-logs/activity-log-dash.png)

| Visualisatie onderdeel | Beschrijving |
| --- | --- |
| Vermeldingen in het Azure-activiteit | Toont een staaf diagram van de bovenste Azure-activiteiten logboek vermeldingen records voor het datum bereik dat u hebt geselecteerd, en toont een lijst met de Top 10 van aanroepen van de activiteit. Klik op het staaf diagram om een logboek zoekopdracht voor `AzureActivity`uit te voeren. Klik op een beller-item om een zoek opdracht in Logboeken uit te voeren waarin alle activiteiten logboek vermeldingen voor dat item worden geretourneerd. |
| Activiteitenlogboeken op Status | Toont een ring diagram voor de Azure-activiteiten logboek status voor het geselecteerde datum bereik en een lijst met de top tien status records. Klik op de grafiek om een logboek query voor `AzureActivity | summarize AggregatedValue = count() by ActivityStatus`uit te voeren. Klik op een status item om een logboek zoekopdracht uit te voeren waarin alle activiteiten logboek vermeldingen voor die status record worden geretourneerd. |
| Activiteitenlogboeken per Resource | Toont het totale aantal resources met activiteiten logboeken en een lijst met de bovenste tien resources met record aantallen voor elke resource. Klik op het gedeelte totaal om een zoek opdracht van het logboek uit te voeren voor `AzureActivity | summarize AggregatedValue = count() by Resource`, waarin alle Azure-resources worden weer gegeven die beschikbaar zijn voor de oplossing. Klik op een resource om een logboek query uit te voeren voor het retour neren van alle activiteiten records voor die bron. |
| Activiteitenlogboeken door de Resourceprovider | Toont het totale aantal resource providers dat activiteiten logboeken produceert en een lijst met de top tien. Klik op het gedeelte totaal om een logboek query uit te voeren voor `AzureActivity | summarize AggregatedValue = count() by ResourceProvider`, waarin alle Azure-resource providers worden weer gegeven. Klik op een resource provider om een logboek query uit te voeren voor het retour neren van alle activiteiten records voor de provider. |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [activiteiten logboek](activity-logs-overview.md).
- Meer informatie over het [Azure monitor-gegevens platform](data-platform.md).
- Gebruik [logboek query's](../log-query/log-query-overview.md) om gedetailleerde informatie uit uw activiteiten logboek weer te geven.
