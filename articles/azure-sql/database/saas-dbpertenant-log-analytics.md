---
title: Azure Monitor logboeken met een multi tenant-app
description: Azure Monitor-logboeken instellen en gebruiken met een SaaS-app met meerdere tenants Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 65513b3cd5813d7d127ca9cbabdcd038f11beee9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84042867"
---
# <a name="set-up-and-use-azure-monitor-logs-with-a-multitenant-azure-sql-database-saas-app"></a>Azure Monitor-logboeken instellen en gebruiken met een SaaS-app met meerdere tenants Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In deze zelf studie kunt u [Azure monitor logboeken](/azure/log-analytics/log-analytics-overview) instellen en gebruiken om elastische Pools en data bases te bewaken. Deze zelf studie is gebaseerd op de [zelf studie voor prestatie controle en-beheer](saas-dbpertenant-performance-monitoring.md). U ziet hoe u Azure Monitor-Logboeken kunt gebruiken om de bewakings-en waarschuwings functies van de Azure Portal te verbeteren. Azure Monitor-logboeken bieden ondersteuning voor het bewaken van duizenden elastische Pools en honderd duizenden data bases. Azure Monitor-logboeken bieden één bewakings oplossing, waarmee u de bewaking van verschillende toepassingen en Azure-Services in meerdere Azure-abonnementen kunt integreren.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Azure Monitor-logboeken installeren en configureren.
> * Gebruik Azure Monitor Logboeken om Pools en data bases te bewaken.

U kunt deze zelfstudie alleen voltooien als aan de volgende vereisten wordt voldaan:

* De Wingtip tickets SaaS data base-per-Tenant-app wordt geïmplementeerd. Zie [de toepassing Wingtip tickets SaaS data base-per-Tenant implementeren en verkennen](../../sql-database/saas-dbpertenant-get-started-deploy.md)voor meer informatie over de implementatie in minder dan vijf minuten.
* Azure PowerShell is geïnstalleerd. Zie [Aan de slag met Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps) voor meer informatie.

Raadpleeg de [zelf studie over prestatie bewaking en-beheer](saas-dbpertenant-performance-monitoring.md) voor een bespreking van SaaS-scenario's en-patronen en hoe deze van invloed zijn op de vereisten voor een bewakings oplossing.

## <a name="monitor-and-manage-database-and-elastic-pool-performance-with-azure-monitor-logs"></a>Prestaties van data bases en elastische Pools bewaken en beheren met Azure Monitor-logboeken

Voor Azure SQL Database zijn bewaking en waarschuwingen beschikbaar in data bases en Pools in de Azure Portal. Deze ingebouwde bewaking en waarschuwingen zijn handig, maar het is ook een specifieke resource. Dit betekent dat het minder goed geschikt is voor het bewaken van grote installaties of voor een uniforme weer gave van resources en abonnementen.

Voor scenario's met grote volumes kunt u Azure Monitor Logboeken gebruiken voor bewaking en waarschuwingen. Azure Monitor is een afzonderlijke Azure-service waarmee u analyses kunt uitvoeren via Logboeken die zijn verzameld in een werk ruimte van mogelijk veel services. Azure Monitor-logboeken bieden een ingebouwde query taal en hulpprogram ma's voor gegevens visualisatie die de analyse van operationele gegevens mogelijk maken. De oplossing SQL Analytics biedt verschillende vooraf gedefinieerde weer gaven en query's voor Elastic pool-en database bewaking en waarschuwingen. Azure Monitor Logboeken biedt ook een aangepaste weergave ontwerper.

OMS-werkruimten worden nu aangeduid als Log Analytics-werkruimten. Log Analytics-werk ruimten en analyse oplossingen worden geopend in de Azure Portal. De Azure Portal is het nieuwere toegangs punt, maar het is mogelijk dat de portal van Operations Management Suite op sommige gebieden zich achter het bevindt.

### <a name="create-performance-diagnostic-data-by-simulating-a-workload-on-your-tenants"></a>Diagnostische gegevens over prestaties maken door een werk belasting op uw tenants te simuleren 

1. Open in de Power shell ISE *. \\ WingtipTicketsSaaS-MultiTenantDb-master \\ learning modules \\ prestaties bewaken en beheren \\Demo-PerformanceMonitoringAndManagement.ps1*. Laat dit script open omdat u tijdens deze zelf studie mogelijk verschillende scenario's voor het genereren van de belasting wilt uitvoeren.
1. Als u dit nog niet hebt gedaan, moet u een batch tenants inrichten om de bewakings context interessanter te maken. Dit proces duurt enkele minuten.

   a. Stel **$DemoScenario = 1** _in om een batch met tenants in te richten_.

   b. Druk op F5 om het script uit te voeren en een extra 17 tenants te implementeren.

1. Start nu de load generator om een gesimuleerde belasting uit te voeren voor alle tenants.

    a. Stel **$DemoScenario = 2**in, _Genereer normale intensiteit (circa 30 DTU)_.

    b. Druk op F5 om het script uit te voeren.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>De scripts van de Wingtip tickets SaaS-data base-per-Tenant toepassing ophalen

De Wingtip tickets SaaS multi tenant-database scripts en toepassings bron code zijn beschikbaar in de [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) github opslag plaats. Raadpleeg de [algemene richt lijnen](saas-tenancy-wingtip-app-guidance-tips.md)voor de stappen voor het downloaden en deblokkeren van de Power shell-scripts voor Wingtip tickets.

## <a name="install-and-configure-log-analytics-workspace-and-the-azure-sql-analytics-solution"></a>Log Analytics-werk ruimte en de Azure SQL-analyse-oplossing installeren en configureren

Azure Monitor is een afzonderlijke service die moet worden geconfigureerd. Met Azure Monitor logboeken worden logboek gegevens, telemetrie en metrieken in een Log Analytics-werk ruimte verzameld. Net als andere resources in azure moet er een Log Analytics-werk ruimte worden gemaakt. De werk ruimte hoeft niet te worden gemaakt in dezelfde resource groep als de toepassingen die worden bewaakt. Dit is vaak het meest zinvol. Voor de Wingtip tickets-app gebruikt u één resource groep om ervoor te zorgen dat de werk ruimte met de toepassing wordt verwijderd.

1. Open in de Power shell ISE *. \\ WingtipTicketsSaaS-MultiTenantDb-master \\ learning modules \\ prestaties bewaken en beheren \\ log Analytics \\Demo-LogAnalytics.ps1*.
1. Druk op F5 om het script uit te voeren.

U kunt nu Azure Monitor-Logboeken openen in de Azure Portal. Het duurt enkele minuten om telemetrie te verzamelen in de Log Analytics-werk ruimte en om het zichtbaar te maken. Hoe langer u de systeem verzameling diagnostische gegevens verlaat, hoe interessanter de ervaring is. 

## <a name="use-log-analytics-workspace-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Gebruik Log Analytics werk ruimte en de oplossing SQL Analytics voor het bewaken van groepen en data bases


In deze oefening opent u Log Analytics werk ruimte in de Azure Portal om de telemetrie te bekijken die is verzameld voor de data bases en groepen.

1. Blader naar [Azure Portal](https://portal.azure.com). Selecteer **alle services** om log Analytics-werk ruimte te openen. Zoek vervolgens naar Log Analytics.

   ![Log Analytics-werk ruimte openen](./media/saas-dbpertenant-log-analytics/log-analytics-open.png)

1. Selecteer de werk ruimte _met de naam wtploganalytics- &lt; &gt; gebruiker_.

1. Selecteer **overzicht** om de oplossing log Analytics te openen in de Azure Portal.

   ![Overzicht](./media/saas-dbpertenant-log-analytics/click-overview.png)

    > [!IMPORTANT]
    > Het kan een paar minuten duren voordat de oplossing actief is. 

1. Selecteer de tegel **Azure SQL-analyse** om deze te openen.

    ![Overzichts tegel](./media/saas-dbpertenant-log-analytics/overview.png)

1. De weer gaven in de oplossing schuiven zijwaarts, met hun eigen interne schuif balk aan de onderkant. Vernieuw de pagina als dat nodig is.

1. Als u de pagina samen vatting wilt verkennen, selecteert u de tegels of afzonderlijke data bases om een inzoomen Verkenner te openen.

    ![Log Analytics-dash board](./media/saas-dbpertenant-log-analytics/log-analytics-overview.png)

1. Wijzig de filter instelling om het tijds bereik aan te passen. Voor deze zelf studie selecteert u **laatste 1 uur**.

    ![Tijd filter](./media/saas-dbpertenant-log-analytics/log-analytics-time-filter.png)

1. Selecteer een afzonderlijke Data Base om het query gebruik en de metrische gegevens voor die data base te verkennen.

    ![Database analyse](./media/saas-dbpertenant-log-analytics/log-analytics-database.png)

1. Als u metrische gegevens over gebruik wilt weer geven, schuift u de pagina Analytics naar rechts.
 
     ![Metrische database gegevens](./media/saas-dbpertenant-log-analytics/log-analytics-database-metrics.png)

1. Schuif de pagina Analytics naar links en selecteer de server tegel in de lijst **resource-info** .  

    ![Lijst met resource gegevens](./media/saas-dbpertenant-log-analytics/log-analytics-resource-info.png)

    Er wordt een pagina geopend waarin de Pools en data bases op de server worden weer gegeven.

    ![Server met Pools en data bases](./media/saas-dbpertenant-log-analytics/log-analytics-server.png)

1. Selecteer een groep. Blader op de pagina groep die wordt geopend naar rechts om de metrische gegevens van de groep weer te geven. 

    ![Metrische gegevens van groep](./media/saas-dbpertenant-log-analytics/log-analytics-pool-metrics.png)


1. Ga terug naar de werk ruimte Log Analytics en selecteer **OMS-Portal** om de werk ruimte daar te openen.

    ![Log Analytics-werkruimte](./media/saas-dbpertenant-log-analytics/log-analytics-workspace-oms-portal.png)

In de werk ruimte Log Analytics kunt u het logboek en de metrische gegevens verder verkennen. 

Bewaking en waarschuwingen in Azure Monitor logboeken zijn gebaseerd op query's voor de gegevens in de werk ruimte, in tegens telling tot de waarschuwingen die zijn gedefinieerd voor elke resource in de Azure Portal. Door waarschuwingen op query's te baseren, kunt u één waarschuwing definiëren die alle data bases doorzoekt, in plaats van één per Data Base te definiëren. Query's worden alleen beperkt door de gegevens die beschikbaar zijn in de werk ruimte.

Voor meer informatie over het gebruik van Azure Monitor-Logboeken om waarschuwingen op te vragen en in te stellen, Zie [werken met waarschuwings regels in azure monitor logboeken](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts-creating).

Azure Monitor logboeken voor SQL Database kosten op basis van het gegevens volume in de werk ruimte. In deze zelf studie hebt u een gratis werk ruimte gemaakt. deze is beperkt tot 500 MB per dag. Wanneer deze limiet is bereikt, worden er geen gegevens meer toegevoegd aan de werk ruimte.


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Azure Monitor-logboeken installeren en configureren.
> * Gebruik Azure Monitor Logboeken om Pools en data bases te bewaken.

Probeer de [zelf studie voor Tenant-analyses](saas-dbpertenant-log-analytics.md).

## <a name="additional-resources"></a>Aanvullende bronnen

* [Aanvullende zelf studies die voortbouwen op de initiële Wingtip tickets SaaS data base-per-Tenant toepassings implementatie](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Azure Monitor logboeken](../../azure-monitor/insights/azure-sql.md)
