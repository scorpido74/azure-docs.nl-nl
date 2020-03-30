---
title: Azure-activiteitslogboek verzamelen in de werkruimte Log Analytics
description: Verzamel het Azure Activity Log in Azure Monitor Logs en gebruik de bewakingsoplossing om het Azure-activiteitenlogboek voor al uw Azure-abonnementen te analyseren en te doorzoeken.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/30/2019
ms.openlocfilehash: 407bff10e2480c5210d3057bcccd6c60e591c165
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80055311"
---
# <a name="collect-and-analyze-azure-activity-logs-in-log-analytics-workspace-in-azure-monitor"></a>Azure-activiteitslogboeken verzamelen en analyseren in de werkruimte Log Analytics in Azure Monitor

> [!WARNING]
> U nu het logboek activiteit verzamelen in een werkruimte van Log Analytics met behulp van een diagnostische instelling die vergelijkbaar is met hoe u bronlogboeken verzamelt. Zie [Azure-activiteitslogboeken verzamelen en analyseren in de werkruimte Log Analytics in Azure Monitor](diagnostic-settings-legacy.md).

Het [Azure-activiteitenlogboek](platform-logs-overview.md) biedt inzicht in gebeurtenissen op abonnementsniveau die zich in uw Azure-abonnement hebben voorgedaan. In dit artikel wordt beschreven hoe u het activiteitenlogboek verzamelt in een werkruimte van Log Analytics en hoe u de [monitoringoplossing](../insights/solutions.md)Activity Log Analytics gebruiken, die logboekquery's en -weergaven biedt voor het analyseren van deze gegevens. 

Het activiteitenlogboek verbinden met een loganalytics-werkruimte biedt de volgende voordelen:

- Het activiteitenlogboek van meerdere Azure-abonnementen consolideren tot één locatie voor analyse.
- Winkel activiteitlogboekvermeldingen langer dan 90 dagen.
- Correleer activiteitslogboekgegevens met andere bewakingsgegevens die door Azure Monitor worden verzameld.
- Gebruik [logboekquery's](../log-query/log-query-overview.md) om complexe analyses uit te voeren en diepgaande inzichten te krijgen over items in het activiteitenlogboek.

## <a name="connect-to-log-analytics-workspace"></a>Verbinding maken met de werkruimte Log Analytics
Eén werkruimte kan worden verbonden met het activiteitenlogboek voor meerdere abonnementen in dezelfde Azure-tenant. Zie [Azure Activity Logs verzamelen in een Log Analytics-werkruimte voor verschillende Azure Active Directory-tenants](activity-log-collect-tenants.md)voor verzameling en meerdere tenants.

> [!IMPORTANT]
> Mogelijk ontvangt u een fout met de volgende procedure als de microsoft.operationalinsights- en Microsoft.OperationsManagement-resourceproviders niet zijn geregistreerd voor uw abonnement. Zie [Azure-bronproviders en -typen](../../azure-resource-manager/management/resource-providers-and-types.md) om deze providers te registreren.

Gebruik de volgende procedure om het activiteitenlogboek te koppelen aan uw log analytics-werkruimte:

1. Selecteer in het menu **Logboekanalyse-werkruimten** in de Azure-portal de werkruimte om het activiteitenlogboek te verzamelen.
1. Selecteer **azure-activiteitenlogboek**in de sectie **Werkruimtegegevensbronnen in** het menu van de werkruimte .
1. Klik op het abonnement dat u wilt aansluiten.

    ![Workspaces](media/activity-log-export/workspaces.png)

1. Klik **op Verbinding maken** om het activiteitslogboek in het abonnement te verbinden met de geselecteerde werkruimte. Als het abonnement al is verbonden met een andere werkruimte, klikt u eerst op **Verbinding verbreken** om de verbinding te verbreken.

    ![Werkruimten verbinden](media/activity-log-export/connect-workspace.png)

## <a name="analyze-in-log-analytics-workspace"></a>Analyseren in de werkruimte Log Analytics
Wanneer u een activiteitenlogboek koppelt aan een werkruimte loganalytics, worden items naar de werkruimte geschreven in een tabel met de naam **AzureActivity** die u ophalen met een [logboekquery](../log-query/log-query-overview.md). De structuur van deze tabel is afhankelijk van de [categorie loginvoer.](activity-log-view.md#categories-in-the-activity-log) Zie [gebeurtenisschema azure activity log](activity-log-schema.md) voor een beschrijving van elke categorie.

## <a name="activity-logs-analytics-monitoring-solution"></a>Monitoringoplossing activity logs Analytics
De bewakingsoplossing azure log Analytics bevat meerdere logboekquery's en -weergaven voor het analyseren van de activiteitenlogboekrecords in uw logboekanalysewerkruimte.

### <a name="install-the-solution"></a>De oplossing installeren
Gebruik de procedure in [Een bewakingsoplossing installeren](../insights/solutions.md#install-a-monitoring-solution) om de **Activity Log Analytics-oplossing** te installeren. Er is geen extra configuratie vereist.

### <a name="use-the-solution"></a>Gebruik de oplossing
Bewakingsoplossingen zijn toegankelijk via het menu **Monitor** in de Azure-portal. Selecteer **Meer** in de sectie **Inzichten** om de **pagina Overzicht** met de oplossingstegels te openen. Op de tegel **Azure Activity Logs** wordt een aantal **AzureActivity-records** in uw werkruimte weergegeven.

![Tegel Azure Activity Logs](media/collect-activity-logs/azure-activity-logs-tile.png)


Klik op de tegel **Azure Activity Logs** om de weergave Azure Activity **Logs** te openen. De weergave bevat de visualisatie-onderdelen in de volgende tabel. Elk onderdeel bevat maximaal 10 items die overeenkomen met de criteria van die onderdelen voor het opgegeven tijdsbereik. U een logboekquery uitvoeren die alle overeenkomende records retourneert door onder aan het onderdeel op **Alles weergeven** te klikken.

![Azure-activiteitslogboekendashboard](media/collect-activity-logs/activity-log-dash.png)

| Visualisatieonderdeel | Beschrijving |
| --- | --- |
| Azure-activiteitslogboekvermeldingen | Toont een staafdiagram van de hoogste totalen van de azure activity log-vermelding voor het datumbereik dat u hebt geselecteerd en toont een lijst met de top 10-activiteitsbellers. Klik op het staafdiagram om `AzureActivity`een logboekzoekopdracht uit te voeren naar . Klik op een belleritem om een logboekzoekopdracht uit te voeren en alle activiteitslogboekvermeldingen voor dat item terug te sturen. |
| Activiteitslogboeken op status | Toont een ringdiagram voor de status azure-activiteitslogboek voor het geselecteerde datumbereik en een lijst met de tien belangrijkste statusrecords. Klik op de grafiek om `AzureActivity | summarize AggregatedValue = count() by ActivityStatus`een logboekquery uit te voeren voor . Klik op een statusitem om een logboekzoekopdracht uit te voeren en alle activiteitslogboekvermeldingen voor die statusrecord terug te sturen. |
| Activiteitslogboeken per resource | Toont het totale aantal resources met activiteitslogboeken en geeft een overzicht van de tien belangrijkste resources met recordtellingen voor elke resource. Klik op het totale gebied `AzureActivity | summarize AggregatedValue = count() by Resource`om een logboekzoekopdracht uit te voeren, waarin alle Azure-bronnen worden weergegeven die beschikbaar zijn voor de oplossing. Klik op een resource om een logboekquery uit te voeren die alle activiteitsrecords voor die resource retoureert. |
| Activiteitslogboeken per resourceprovider | Toont het totale aantal resourceproviders dat activiteitslogboeken produceert en de top tien weergeeft. Klik op het totale gebied `AzureActivity | summarize AggregatedValue = count() by ResourceProvider`waarvoor u een logboekquery wilt uitvoeren, waarin alle Azure-bronproviders worden weergegeven. Klik op een resourceprovider om een logboekquery uit te voeren die alle activiteitsrecords voor de provider retoureert. |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [activiteitenlogboek](platform-logs-overview.md).
- Meer informatie over het [Azure Monitor-gegevensplatform](data-platform.md).
- Gebruik [logboekquery's](../log-query/log-query-overview.md) om gedetailleerde informatie uit uw activiteitenlogboek weer te geven.
