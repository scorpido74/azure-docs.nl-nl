---
title: Azure Monitor-logboeken met een multitenant-app
description: Azure Monitor-logboeken instellen en gebruiken met een Azure SQL Database SaaS-app met meerdere tenant
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
ms.date: 01/25/2019
ms.openlocfilehash: 7429a9d5e9a803f0e9a6f900c5d81e77e7477a48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214489"
---
# <a name="set-up-and-use-azure-monitor-logs-with-a-multitenant-sql-database-saas-app"></a>Azure Monitor-logboeken instellen en gebruiken met een SQL Database SaaS-app met meerdere tenant

In deze zelfstudie stelt u [Azure Monitor-logboeken](/azure/log-analytics/log-analytics-overview) in en gebruikt u deze om elastische groepen en databases te controleren. Deze zelfstudie bouwt voort op de [zelfstudie voor prestatiebewaking en beheer.](saas-dbpertenant-performance-monitoring.md) Hierin wordt uitgelegd hoe u Azure Monitor-logboeken gebruiken om de bewaking en waarschuwingen in de Azure-portal uit te breiden. Azure Monitor-logboeken ondersteunen het bewaken van duizenden elastische pools en honderdduizenden databases. Azure Monitor-logboeken bieden één bewakingsoplossing, die bewaking van verschillende toepassingen en Azure-services voor meerdere Azure-abonnementen kan integreren.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Azure Monitor-logboeken installeren en configureren.
> * Gebruik Azure Monitor-logboeken om groepen en databases te controleren.

U kunt deze zelfstudie alleen voltooien als aan de volgende vereisten wordt voldaan:

* De Wingtip Tickets SaaS database-per-tenant app wordt geïmplementeerd. Zie Implementeren en verkennen van [de Wingtip Tickets SaaS-database-per-tenant-toepassing](saas-dbpertenant-get-started-deploy.md)om in minder dan vijf minuten te implementeren.
* Azure PowerShell is geïnstalleerd. Zie [Aan de slag met Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps) voor meer informatie.

Zie de [zelfstudie Prestatiebewaking en beheer](saas-dbpertenant-performance-monitoring.md) voor een bespreking van SaaS-scenario's en -patronen en hoe deze van invloed zijn op de vereisten voor een bewakingsoplossing.

## <a name="monitor-and-manage-database-and-elastic-pool-performance-with-azure-monitor-logs"></a>Database- en elastische poolprestaties bewaken en beheren met Azure Monitor-logboeken

Voor Azure SQL Database is bewaking en waarschuwing beschikbaar in databases en pools in de Azure-portal. Deze ingebouwde monitoring en waarschuwing is handig, maar het is ook resource-specifiek. Dat betekent dat het minder geschikt is om grote installaties te bewaken of een uniforme weergave te bieden tussen resources en abonnementen.

Voor scenario's met een hoog volume u Azure Monitor-logboeken gebruiken voor bewaking en waarschuwingen. Azure Monitor is een afzonderlijke Azure-service waarmee analyses over logboeken die zijn verzameld in een werkruimte van mogelijk veel services, kunnen worden gebruikt. Azure Monitor-logboeken bieden een ingebouwde querytaal- en gegevensvisualisatietools waarmee operationele gegevensanalyse mogelijk is. De SQL Analytics-oplossing biedt verschillende vooraf gedefinieerde elastische pool- en databasebewaking en waarschuwingen voor weergaven en query's. Azure Monitor-logboeken bieden ook een aangepaste weergaveontwerper.

OMS-werkruimten worden nu aangeduid als Log Analytics-werkruimten. Log Analytics-werkruimten en analyseoplossingen worden geopend in de Azure-portal. De Azure-portal is het nieuwere toegangspunt, maar het kan zijn wat er achter de Operations Management Suite-portal zit in sommige gebieden.

### <a name="create-performance-diagnostic-data-by-simulating-a-workload-on-your-tenants"></a>Diagnostische gegevens voor prestaties maken door een werkbelasting voor uw tenants te simuleren 

1. Open in de PowerShell ISE *.. WingtipTicketsSaaS-MultiTenantDb-master\\Learning\\Modules Performance\\Monitoring and Management Demo-PerformanceMonitoringAndManagement.ps1 . \\* Houd dit script open omdat u tijdens deze zelfstudie verschillende scenario's voor het genereren van belasting wilt uitvoeren.
1. Als u dit nog niet hebt gedaan, u een groep huurders inrichten om de controlecontext interessanter te maken. Dit proces duurt een paar minuten.

   a. Stel **$DemoScenario = 1**, Een partij huurders _in._

   b. Druk op F5 om het script uit te voeren en nog eens 17 tenants te implementeren.

1. Start nu de laadgenerator om een gesimuleerde belasting op alle tenants uit te voeren.

    a. Stel **$DemoScenario = 2**, _Genereer normale intensiteitbelasting (ongeveer 30 DTU)_.

    b. Als u het script wilt uitvoeren, drukt u op F5.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Download de Wingtip Tickets SaaS database-per-tenant applicatiescripts

De Wingtip Tickets SaaS multitenant database scripts en applicatie broncode zijn beschikbaar in de [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub repo. Zie de [algemene richtlijnen](saas-tenancy-wingtip-app-guidance-tips.md)voor stappen om de Wingtip Tickets PowerShell-scripts te downloaden en te deblokkeren.

## <a name="install-and-configure-log-analytics-workspace-and-the-azure-sql-analytics-solution"></a>Log Analytics-werkruimte en de Azure SQL Analytics-oplossing installeren en configureren

Azure Monitor is een aparte service die moet worden geconfigureerd. Azure Monitor-logboeken verzamelen logboekgegevens, telemetrie en statistieken in een werkruimte log Analytics. Net als andere bronnen in Azure moet er een Log Analytics-werkruimte worden gemaakt. De werkruimte hoeft niet te worden gemaakt in dezelfde resourcegroep als de toepassingen die worden bewaakt. Doen dit vaak maakt het meest zinvol hoor. Gebruik voor de app Wingtip Tickets één resourcegroep om ervoor te zorgen dat de werkruimte met de toepassing wordt verwijderd.

1. Open in de PowerShell ISE *.. WingtipTicketsSaaS-MultiTenantDb-master\\Learning\\Modules Performance\\Monitoring\\and Management Log Analytics Demo-LogAnalytics.ps1 . \\*
1. Als u het script wilt uitvoeren, drukt u op F5.

Nu u Azure Monitor-logboeken openen in de Azure-portal. Het verzamelen van telemetrie in de werkruimte Log Analytics duurt enkele minuten en maakt deze zichtbaar. Hoe langer je het systeem laat diagnostische gegevens verzamelen, hoe interessanter de ervaring is. 

## <a name="use-log-analytics-workspace-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>De werkruimte Log Analytics en de SQL Analytics-oplossing gebruiken om pools en databases te controleren


Open in deze oefening de werkruimte Log Analytics in de Azure-portal om te kijken naar de telemetrie die is verzameld voor de databases en groepen.

1. Blader naar [Azure Portal](https://portal.azure.com). Selecteer **Alle services** om de werkruimte Log Analytics te openen. Zoek vervolgens naar Log Analytics.

   ![Werkruimte Logboekanalyse openen](media/saas-dbpertenant-log-analytics/log-analytics-open.png)

1. Selecteer de werkruimte met de naam _wtploganalytics-user&lt;&gt;_.

1. Selecteer **Overzicht** om de oplossing voor logboekanalyse in de Azure-portal te openen.

   ![Overzicht](media/saas-dbpertenant-log-analytics/click-overview.png)

    > [!IMPORTANT]
    > Het kan een paar minuten duren voordat de oplossing actief is. 

1. Selecteer de tegel **Azure SQL Analytics** om deze te openen.

    ![Overzichtstegel](media/saas-dbpertenant-log-analytics/overview.png)

1. De weergaven in de oplossing scrollen zijwaarts, met hun eigen binnenschuifbalk onderaan. Vernieuw indien nodig de pagina.

1. Als u de overzichtspagina wilt verkennen, selecteert u de tegels of afzonderlijke databases om een inzoomende verkenner te openen.

    ![Dashboard logboekanalyse](media/saas-dbpertenant-log-analytics/log-analytics-overview.png)

1. Wijzig de filterinstelling om het tijdsbereik te wijzigen. Selecteer Voor deze zelfstudie **Last 1 uur**.

    ![Tijdfilter](media/saas-dbpertenant-log-analytics/log-analytics-time-filter.png)

1. Selecteer een afzonderlijke database om het querygebruik en de statistieken voor die database te verkennen.

    ![Database-analyse](media/saas-dbpertenant-log-analytics/log-analytics-database.png)

1. Als u gebruiksstatistieken wilt zien, schuift u de analysepagina naar rechts.
 
     ![Databasestatistieken](media/saas-dbpertenant-log-analytics/log-analytics-database-metrics.png)

1. Schuif de analysepagina naar links en selecteer de servertegel in de lijst **Resourceinfo.**  

    ![Lijst met brongegevens](media/saas-dbpertenant-log-analytics/log-analytics-resource-info.png)

    Er wordt een pagina geopend met de pools en databases op de server.

    ![Server met pools en databases](media/saas-dbpertenant-log-analytics/log-analytics-server.png)

1. Selecteer een pool. Schuif op de poolpagina die wordt geopend naar rechts om de poolstatistieken te bekijken. 

    ![Statistieken van de groep](media/saas-dbpertenant-log-analytics/log-analytics-pool-metrics.png)


1. Selecteer **oms-portal** om de werkruimte daar te openen in de werkruimte Log Analytics.

    ![Log Analytics-werkruimte](media/saas-dbpertenant-log-analytics/log-analytics-workspace-oms-portal.png)

In de werkruimte Log Analytics u de logboek- en metrische gegevens verder verkennen. 

Monitoring en waarschuwingen in Azure Monitor-logboeken zijn gebaseerd op query's over de gegevens in de werkruimte, in tegenstelling tot de waarschuwingen die zijn gedefinieerd voor elke bron in de Azure-portal. Door waarschuwingen te baseren op query's, u één waarschuwing definiëren die alle databases bekijkt, in plaats van er één per database te definiëren. Query's worden alleen beperkt door de gegevens die beschikbaar zijn in de werkruimte.

Zie [Werken met waarschuwingsregels in Azure Monitor-logboeken](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts-creating)voor meer informatie over het gebruik van Azure Monitor-logboeken voor het opvragen en instellen van waarschuwingen.

Azure Monitor-logboeken voor SQL-databasekosten op basis van het gegevensvolume in de werkruimte. In deze zelfstudie hebt u een gratis werkruimte gemaakt, die beperkt is tot 500 MB per dag. Nadat die limiet is bereikt, worden gegevens niet meer toegevoegd aan de werkruimte.


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Azure Monitor-logboeken installeren en configureren.
> * Gebruik Azure Monitor-logboeken om groepen en databases te controleren.

Probeer de [zelfstudie Tenant analytics](saas-dbpertenant-log-analytics.md).

## <a name="additional-resources"></a>Aanvullende bronnen

* [Aanvullende zelfstudies die voortbouwen op de eerste Wingtip Tickets SaaS-database per tenant-toepassing](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Azure Monitor-logboeken](../azure-monitor/insights/azure-sql.md)
