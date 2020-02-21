---
title: Metrische gegevens en diagnoselogboeken
description: Meer informatie over het inschakelen van diagnostische gegevens in Azure SQL Database voor het opslaan van informatie over resource gebruik en statistieken over het uitvoeren van query's.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 11/16/2019
ms.openlocfilehash: f5ed3ee9b0e7e7218a519baa56cda443fddab105
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/21/2020
ms.locfileid: "77522614"
---
# <a name="azure-sql-database-metrics-and-diagnostics-logging"></a>Azure SQL Database metrische gegevens en logboek registratie van diagnostische gegevens

In dit onderwerp leert u hoe u logboek registratie van diagnostische gegevens voor Azure SQL Database kunt configureren via de Azure Portal, Power shell, Azure CLI, Azure Monitor REST API en Azure Resource Manager sjabloon. Deze diagnostische gegevens kunnen worden gebruikt om het resource gebruik en de statistieken voor query uitvoering te meten.

Afzonderlijke data bases, gepoolde data bases in elastische Pools en instantie-data bases in een beheerd exemplaar kunnen metrische gegevens en Diagnostische logboeken streamen voor eenvoudiger prestatie bewaking. U kunt een Data Base configureren voor het verzenden van resource gebruik, werk nemers en sessies, en connectiviteit met een van de volgende Azure-resources:

- **Azure SQL-analyse**: om intelligente controle te krijgen over uw Azure SQL-data bases, waaronder prestatie rapporten, waarschuwingen en aanbevelingen voor risico beperking.
- **Azure Event hubs**: om SQL database telemetrie te integreren met uw aangepaste bewakings oplossingen of dynamische pijp lijnen.
- **Azure Storage**: om grote hoeveel heden telemetrie te archiveren voor een fractie van de prijs.

    ![Architectuur](./media/sql-database-metrics-diag-logging/architecture.png)

Zie voor meer informatie over de metrische gegevens en logboek categorieën die worden ondersteund door de verschillende Azure-Services:

- [Overzicht van metrische gegevens in Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
- [Overzicht van Azure Diagnostics-logboeken](../azure-monitor/platform/platform-logs-overview.md)

Dit artikel bevat richt lijnen voor het inschakelen van diagnostische telemetrie voor Azure SQL-data bases, elastische Pools en beheerde exemplaren. Daarnaast kunt u beter begrijpen hoe u Azure SQL-analyse configureert als een bewakings programma voor het weer geven van de telemetrie van database diagnostiek.

## <a name="enable-logging-of-diagnostics-telemetry"></a>Logboek registratie van diagnostische gegevens inschakelen

U kunt metrische gegevens en diagnostische gegevens over telemetrie inschakelen en beheren met een van de volgende methoden:

- Azure-portal
- PowerShell
- Azure CLI
- Azure Monitor REST API
- Azure Resource Manager-sjabloon

Wanneer u metrische gegevens en logboek registratie van diagnostische gegevens inschakelt, moet u de Azure-resource bestemming opgeven voor het verzamelen van de diagnostische gegevens over de telemetrie. Beschik bare opties zijn:

- Azure SQL-analyse
- Azure Event Hubs
- Azure Storage

U kunt een nieuwe Azure-resource inrichten of een bestaande resource selecteren. Nadat u een resource hebt gekozen met de optie **Diagnostische instellingen** , geeft u op welke gegevens u wilt verzamelen.

## <a name="supported-diagnostic-logging-for-azure-sql-databases-and-instance-databases"></a>Ondersteunde diagnostische logboek registratie voor Azure SQL-data bases en instantie-data bases

Schakel de metrische gegevens en logboek registratie van diagnostische gegevens in SQL-data bases in. deze worden niet standaard ingeschakeld.

U kunt Azure SQL-data bases en exemplaar databases instellen om de volgende diagnostische telemetrie te verzamelen:

| Telemetrie voor data bases bewaken | Ondersteuning voor één data base en gepoolde data base | Ondersteuning voor instance data base |
| :------------------- | ----- | ----- |
| [Basis metrieken](#basic-metrics): bevat DTU/CPU-percentage, DTU/CPU-limiet, fysiek gegevens Lees percentage, logboek schrijf percentage, geslaagd/mislukt/geblokkeerd door Firewall verbindingen, percentages van werk nemers, percentage van de opslag, opslag percentage en XTP opslag percentage. | Ja | Nee |
| [Exemplaar-en app-Geavanceerd](#advanced-metrics): bevat TempDB systeem database gegevens en Logboek bestands grootte en het logboek bestand TempDB-percentage gebruikt. | Ja | Nee |
| [QueryStoreRuntimeStatistics](#query-store-runtime-statistics): bevat informatie over de query runtime-statistieken, zoals CPU-gebruik en statistieken voor de duur van query's. | Ja | Ja |
| [QueryStoreWaitStatistics](#query-store-wait-statistics): bevat informatie over de query wacht statistieken (waarvoor uw query's zijn gewacht), zoals CPU, logboek en vergren deling. | Ja | Ja |
| [Fouten](#errors-dataset): bevat informatie over SQL-fouten in een Data Base. | Ja | Ja |
| [DatabaseWaitStatistics](#database-wait-statistics-dataset): bevat informatie over hoeveel tijd een Data Base op verschillende wacht typen heeft gewacht. | Ja | Nee |
| [Time-outs](#time-outs-dataset): bevat informatie over time-outs voor een Data Base. | Ja | Nee |
| [Blokken](#blockings-dataset): bevat informatie over het blok keren van gebeurtenissen in een Data Base. | Ja | Nee |
| [Deadlocks](#deadlocks-dataset): bevat informatie over deadlock-gebeurtenissen voor een Data Base. | Ja | Nee |
| [AutomaticTuning](#automatic-tuning-dataset): bevat informatie over aanbevelingen voor automatisch afstemmen voor een Data Base. | Ja | Nee |
| [SQLInsights](#intelligent-insights-dataset): bevat intelligent Insights prestaties voor een Data Base. Zie [intelligent Insights](sql-database-intelligent-insights.md)voor meer informatie. | Ja | Ja |

> [!IMPORTANT]
> Elastische Pools en beheerde instanties hebben hun eigen afzonderlijke diagnostische gegevens over de telemetrie van de data bases die ze bevatten. Dit is belang rijk om aan te geven dat telemetrie van diagnostische gegevens afzonderlijk is geconfigureerd voor elk van deze resources, zoals hieronder wordt beschreven.

> [!NOTE]
> - Als u controle logboek streaming wilt inschakelen, raadpleegt [u controle instellen voor uw data base](sql-database-auditing.md#subheading-2)en controleert u [logboeken in azure monitor logboeken en Azure Event hubs](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/SQL-Audit-logs-in-Azure-Log-Analytics-and-Azure-Event-Hubs/ba-p/386242).
> - Diagnostische instellingen kunnen niet worden geconfigureerd voor de **systeem databases**, zoals Master-, msdb-, model-, resource-en tempdb-data bases.

## <a name="azure-portal"></a>Azure-portal

U kunt het menu **Diagnostische instellingen** gebruiken voor elke afzonderlijke, gepoolde of exemplaar database in azure portal voor het configureren van streaming van diagnostische gegevens over de telemetrie. Daarnaast kan diagnostische telemetrie ook afzonderlijk worden geconfigureerd voor database containers: elastische Pools en beheerde exemplaren. U kunt de volgende bestemmingen instellen voor het streamen van de telemetrie diagnostische gegevens: Azure Storage, Azure Event Hubs en Azure Monitor Logboeken.

### <a name="configure-streaming-of-diagnostics-telemetry-for-elastic-pools"></a>Streaming van diagnostische telemetrie voor elastische Pools configureren

   ![Pictogram elastische pool](./media/sql-database-metrics-diag-logging/icon-elastic-pool-text.png)

U kunt een resource voor een elastische pool instellen om de volgende diagnostische gegevens te verzamelen:

| Resource | Telemetrie controleren |
| :------------------- | ------------------- |
| **Elastische pool** | [Basis metrieken](sql-database-metrics-diag-logging.md#basic-metrics) bevatten EDTU/CPU-percentage, EDTU/CPU-limiet, fysiek gegevens Lees percentage, logboek schrijf percentage, sessie percentage, werk nemer-percentage, opslag, opslag percentage, opslag limiet en XTP opslag percentage. |

Als u streaming-telemetrie voor elastische Pools en data bases in elastische Pools wilt configureren, moet u de volgende gegevens **afzonderlijk configureren:**

- Streaming van diagnostische gegevens over de telemetrie inschakelen voor een elastische pool **en**
- Streaming van diagnostische gegevens over de telemetrie inschakelen voor elke data base in elastische pool

De reden hiervoor is dat elastische pool een database container is met een eigen telemetrie, gescheiden van een afzonderlijke data base-telemetrie.

Voer de volgende stappen uit om streaming van diagnostische gegevens over de telemetrie in te scha kelen voor een elastische pool-resource:

1. Ga naar de resource voor **elastische Pools** in azure Portal.
1. Selecteer **instellingen voor diagnostische gegevens**.
1. Schakel **Diagnostische gegevens inschakelen in** als er geen eerdere instellingen zijn of selecteer **instelling bewerken** om een vorige instelling te bewerken.

   ![Diagnostische gegevens inschakelen voor elastische Pools](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-enable.png)

1. Voer een instellings naam in voor uw eigen verwijzing.
1. Selecteer een doel resource voor de streaming diagnostische gegevens: **archiveren naar opslag account**, **streamen naar een event hub**of **verzenden naar log Analytics**.
1. Voor log Analytics selecteert **u configureren** en maakt u een nieuwe werk ruimte door te selecteren **+ nieuwe werk ruimte maken**of een bestaande werk ruimte te selecteren.
1. Schakel het selectie vakje voor telemetrie van elastische pool Diagnostics in: **basis** gegevens.
   ![diagnostische gegevens configureren voor elastische Pools](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-selection.png)

1. Selecteer **Opslaan**.
1. Daarnaast kunt u streaming configureren van de telemetrie van diagnostische gegevens voor elke data base in de elastische pool die u wilt bewaken door de stappen te volgen die in de volgende sectie worden beschreven.

> [!IMPORTANT]
> Naast het configureren van diagnostische telemetrie voor een elastische pool moet u ook diagnostische telemetrie configureren voor elke data base in een elastische pool, zoals hieronder wordt beschreven.

### <a name="configure-streaming-of-diagnostics-telemetry-for-single-database-or-database-in-elastic-pool"></a>Streaming configureren van de telemetrie van diagnostische gegevens voor één data base of data base in een elastische pool

   ![SQL Database pictogram](./media/sql-database-metrics-diag-logging/icon-sql-database-text.png)

Ga als volgt te werk om streaming van diagnostische gegevens over de telemetrie voor één of gepoolde data bases in te scha kelen:

1. Ga naar Azure **SQL database** resource.
1. Selecteer **instellingen voor diagnostische gegevens**.
1. Schakel **Diagnostische gegevens inschakelen in** als er geen eerdere instellingen zijn of selecteer **instelling bewerken** om een vorige instelling te bewerken.
   - U kunt Maxi maal drie parallelle verbindingen maken voor het streamen van de telemetrie van diagnostische gegevens.
   - Selecteer **Diagnostische instelling toevoegen** om parallelle streaming van diagnostische gegevens te configureren voor meerdere resources.

   ![Diagnostische gegevens inschakelen voor data bases met één, gegroepeerd of exemplaar](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-enable.png)

1. Voer een instellings naam in voor uw eigen verwijzing.
1. Selecteer een doel resource voor de streaming diagnostische gegevens: **archiveren naar opslag account**, **streamen naar een event hub**of **verzenden naar log Analytics**.
1. Schakel voor de standaard bewakings ervaring op basis van gebeurtenissen de volgende selectie vakjes in voor de telemetrie van het logboek voor database diagnostiek: **SQLInsights**, **AutomaticTuning**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics**, **Errors**, **DatabaseWaitStatistics**, **time-outs**, **blokken**en **deadlocks**.
1. Voor een geavanceerde bewakings ervaring op basis van één minuut selecteert u het selectie vakje voor **basis** metrische gegevens.
   ![diagnostische gegevens configureren voor een Data Base met één, gegroepeerd of exemplaar](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-selection.png)
1. Selecteer **Opslaan**.
1. Herhaal deze stappen voor elke Data Base die u wilt bewaken.

> [!NOTE]
> Als u controle logboek streaming wilt inschakelen, raadpleegt [u controle instellen voor uw data base](sql-database-auditing.md#subheading-2)en controleert u [logboeken in azure monitor logboeken en Azure Event hubs](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/SQL-Audit-logs-in-Azure-Log-Analytics-and-Azure-Event-Hubs/ba-p/386242).

> [!TIP]
> Herhaal deze stappen voor elke Azure SQL Database die u wilt bewaken.

### <a name="configure-streaming-of-diagnostics-telemetry-for-managed-instances"></a>Streaming telemetrie van diagnostische gegevens voor beheerde instanties configureren

   ![Pictogram beheerde instantie](./media/sql-database-metrics-diag-logging/icon-managed-instance-text.png)

U kunt een beheerde exemplaar bron instellen om de volgende diagnostische telemetrie te verzamelen:

| Resource | Telemetrie controleren |
| :------------------- | ------------------- |
| **Beheerd exemplaar** | [ResourceUsageStats](#resource-usage-stats-for-managed-instance) bevat vCores aantal, gemiddeld CPU-percentage, i/o-aanvragen, lees-en schrijf bewerkingen in bytes, gereserveerde opslag ruimte en gebruikte opslag ruimte. |

Voor het configureren van streaming van diagnostische gegevens over de telemetrie voor beheerde exemplaar-en exemplaar databases, moet u de volgende onderdelen **afzonderlijk configureren:**

- Streaming-telemetrie van diagnostische gegevens inschakelen voor een beheerd exemplaar **en**
- Streaming van diagnostische gegevens over de telemetrie inschakelen voor elke exemplaar database

Dit is omdat een beheerd exemplaar een database container is met een eigen telemetrie, gescheiden van een telemetrie van een afzonderlijke exemplaar van de data base.

Voer de volgende stappen uit om streaming van diagnostische gegevens over de telemetrie voor een beheerde exemplaar bron in te scha kelen:

1. Ga naar de bron van het **beheerde exemplaar** in azure Portal.
1. Selecteer **instellingen voor diagnostische gegevens**.
1. Schakel **Diagnostische gegevens inschakelen in** als er geen eerdere instellingen zijn of selecteer **instelling bewerken** om een vorige instelling te bewerken.

   ![Diagnostische gegevens inschakelen voor een beheerd exemplaar](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-enable.png)

1. Voer een instellings naam in voor uw eigen verwijzing.
1. Selecteer een doel resource voor de streaming diagnostische gegevens: **archiveren naar opslag account**, **streamen naar een event hub**of **verzenden naar log Analytics**.
1. Voor log Analytics selecteert **u configureren** en maakt u een nieuwe werk ruimte door te selecteren **+ nieuwe werk ruimte maken**of een bestaande werk ruimte te gebruiken.
1. Schakel het selectie vakje voor de telemetrie van exemplaar diagnose in: **ResourceUsageStats**.

   ![Diagnostische gegevens configureren voor een beheerd exemplaar](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-selection.png)

1. Selecteer **Opslaan**.
1. Daarnaast kunt u streaming configureren van de telemetrie van diagnostische gegevens voor elke exemplaar database binnen het beheerde exemplaar dat u wilt bewaken door de stappen te volgen die in de volgende sectie worden beschreven.

> [!IMPORTANT]
> Naast het configureren van diagnostische telemetrie voor een beheerd exemplaar, moet u ook diagnostische telemetrie configureren voor elke exemplaar database, zoals hieronder wordt beschreven.

### <a name="configure-streaming-of-diagnostics-telemetry-for-instance-databases"></a>Streaming van de telemetrie van diagnostische gegevens voor exemplaar databases configureren

   ![Pictogram data base-exemplaar in beheerd exemplaar](./media/sql-database-metrics-diag-logging/icon-mi-database-text.png)

Ga als volgt te werk om streaming van diagnostische gegevens van de telemetrie voor instantie-data bases in te scha kelen:

1. Ga naar de **Data Base** van het exemplaar van het beheerde exemplaar.
1. Selecteer **instellingen voor diagnostische gegevens**.
1. Schakel **Diagnostische gegevens inschakelen in** als er geen eerdere instellingen zijn of selecteer **instelling bewerken** om een vorige instelling te bewerken.
   - U kunt Maxi maal drie (3) parallelle verbindingen maken om de telemetrie van diagnostische gegevens te streamen.
   - Selecteer **+ Diagnostische instelling toevoegen** om parallelle streaming van diagnostische gegevens te configureren voor meerdere resources.

   ![Diagnostische gegevens inschakelen voor exemplaar databases](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-enable.png)

1. Voer een instellings naam in voor uw eigen verwijzing.
1. Selecteer een doel resource voor de streaming diagnostische gegevens: **archiveren naar opslag account**, **streamen naar een event hub**of **verzenden naar log Analytics**.
1. Schakel de selectie vakjes voor de telemetrie van database diagnostiek in: **SQLInsights**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics** en **Errors**.
   ![diagnostische gegevens voor exemplaar databases configureren](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-selection.png)
1. Selecteer **Opslaan**.
1. Herhaal deze stappen voor elke instantie database die u wilt bewaken.

> [!TIP]
> Herhaal deze stappen voor elke instantie database die u wilt bewaken.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> De Power shell-Azure Resource Manager module wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkeling is voor de module AZ. SQL. Zie [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)voor deze cmdlets. De argumenten voor de opdrachten in de module AZ en in de AzureRm-modules zijn aanzienlijk identiek.

U kunt metrische gegevens en diagnostische logboek registratie inschakelen met behulp van Power shell.

- Om in te schakelen opslag van diagnostische logboeken in een opslagaccount, gebruikt u deze opdracht:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   De ID van het opslag account is de resource-ID voor het doel-opslag account.

- Als u wilt inschakelen voor streaming van diagnostische logboeken naar een event hub, gebruikt u deze opdracht:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   De regel-ID van Azure Service Bus is een tekenreeks zijn met deze indeling:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Als u wilt inschakelen verzenden van diagnostische logboeken naar Log Analytics-werkruimte, moet u deze opdracht gebruiken:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- U vindt de resource-ID van uw Log Analytics-werkruimte met behulp van de volgende opdracht uit:

   ```powershell
   (Get-AzOperationalInsightsWorkspace).ResourceId
   ```

U kunt deze parameters voor het inschakelen van meerdere uitvoeropties combineren.

### <a name="to-configure-multiple-azure-resources"></a>Meerdere Azure-resources configureren

Als u meerdere abonnementen wilt ondersteunen, gebruikt u het Power shell-script voor het [inschakelen van logboek registratie van Azure-resource-metrieken met Power shell](https://blogs.technet.microsoft.com/msoms/20../../enable-azure-resource-metrics-logging-using-powershell/).

Geef de resource-ID van de werk ruimte \<$WSID\> als een para meter bij het uitvoeren van het script `Enable-AzureRMDiagnostics.ps1` voor het verzenden van diagnostische gegevens van meerdere resources naar de werk ruimte.

- Als u de werk ruimte-ID \<$WSID\> van de bestemming voor uw diagnostische gegevens wilt ophalen, gebruikt u het volgende script:

    ```powershell
    $WSID = "/subscriptions/<subID>/resourcegroups/<RG_NAME>/providers/microsoft.operationalinsights/workspaces/<WS_NAME>"
    .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
    ```

   Vervang \<subID\> door de abonnements-ID \<RG_NAME\> met de naam van de resource groep en \<WS_NAME\> met de naam van de werk ruimte.

### <a name="azure-cli"></a>Azure CLI

U kunt metrische gegevens en diagnostische logboek registratie inschakelen met behulp van de Azure CLI.

> [!NOTE]
> Scripts voor het inschakelen van diagnostische logboek registratie worden ondersteund voor Azure CLI v 1.0. CLI v 2.0 wordt op dit moment niet ondersteund.

- Als u de opslag van Diagnostische logboeken in een opslag account wilt inschakelen, gebruikt u deze opdracht:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   De ID van het opslag account is de resource-ID voor het doel-opslag account.

- Als u het streamen van Diagnostische logboeken naar een Event Hub wilt inschakelen, gebruikt u deze opdracht:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   De Service Bus regel-ID is een teken reeks met de volgende indeling:

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Gebruik deze opdracht om het verzenden van Diagnostische logboeken naar een Log Analytics-werk ruimte in te scha kelen:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

U kunt deze parameters voor het inschakelen van meerdere uitvoeropties combineren.

### <a name="rest-api"></a>REST-API

Meer informatie over het [wijzigen van diagnostische instellingen met behulp van de Azure Monitor rest API](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings).

### <a name="resource-manager-template"></a>Resource Manager-sjabloon

Meer informatie over het [inschakelen van diagnostische instellingen bij het maken van resources met behulp van een resource manager-sjabloon](../azure-monitor/platform/diagnostic-settings-template.md).

## <a name="stream-into-azure-sql-analytics"></a>Streamen naar Azure SQL-analyse

Azure SQL-analyse is een Cloud oplossing die de prestaties van Azure SQL-data bases, elastische Pools en beheerde exemplaren op schaal en op meerdere abonnementen bewaakt. Het helpt u bij het verzamelen en visualiseren van Azure SQL Database prestatie gegevens en heeft ingebouwde intelligentie voor het oplossen van prestaties.

![Overzicht van Azure SQL Analytics](../azure-monitor/insights/media/azure-sql/azure-sql-sol-overview.png)

SQL Database metrische gegevens en Diagnostische logboeken kunnen worden gestreamd naar Azure SQL-analyse met behulp van de ingebouwde optie **Send to log Analytics** op het tabblad Diagnostische instellingen in de portal. U kunt log Analytics ook inschakelen met behulp van een diagnostische instelling via Power shell-cmdlets, de Azure CLI of de Azure Monitor REST API.

### <a name="installation-overview"></a>Overzicht van de installatie

U kunt een SQL Database vloot bewaken met Azure SQL-analyse. Voer de volgende stappen uit:

1. Maak een Azure SQL-analyse oplossing op Azure Marketplace.
2. Maak een bewakings werkruimte in de oplossing.
3. Configureer data bases voor het streamen van de telemetrie diagnostische gegevens in de werk ruimte.

Als u gebruikmaakt van elastische Pools of beheerde instanties, moet u ook diagnostische telemetrie-streaming configureren van deze resources.

### <a name="create-azure-sql-analytics-resource"></a>Azure SQL-analyse resource maken

1. Zoek naar Azure SQL-analyse in azure Marketplace en selecteer deze.

   ![Zoeken naar Azure SQL-analyse in de portal](./media/sql-database-metrics-diag-logging/sql-analytics-in-marketplace.png)

2. Selecteer **maken** in het overzichts scherm van de oplossing.

3. Vul het Azure SQL-analyse formulier in met de vereiste aanvullende gegevens: naam van de werk ruimte, het abonnement, de resource groep, de locatie en de prijs categorie.

   ![Azure SQL-analyse configureren in de portal](./media/sql-database-metrics-diag-logging/sql-analytics-configuration-blade.png)

4. Selecteer **OK** om te bevestigen en selecteer vervolgens **maken**.

### <a name="configure-databases-to-record-metrics-and-diagnostics-logs"></a>Data bases configureren voor het vastleggen van metrische gegevens en Diagnostische logboeken

De eenvoudigste manier om te configureren waar de metrische gegevens van data bases worden vastgelegd met behulp van de Azure Portal. Zoals eerder beschreven, gaat u naar uw SQL Database-resource in de Azure Portal en selecteert u **instellingen voor diagnostische gegevens**.

Als u gebruikmaakt van elastische Pools of beheerde instanties, moet u ook diagnostische instellingen in deze bronnen configureren om de diagnostische telemetrie in te scha kelen voor het streamen van de werk ruimte.

### <a name="use-the-sql-analytics-solution-for-monitoring-and-alerting"></a>De SQL Analytics-oplossing gebruiken voor bewaking en waarschuwingen

U kunt SQL Analytics als hiërarchisch dash board gebruiken om uw SQL Database-resources weer te geven.

- Zie [SQL database bewaken met behulp van de SQL Analytics-oplossing](../log-analytics/log-analytics-azure-sql.md)voor meer informatie over het gebruik van de SQL Analytics-oplossing.
- Zie voor meer informatie over het instellen van waarschuwingen voor SQL Database en beheerde exemplaren op basis van SQL Analytics [waarschuwingen maken voor SQL database en een beheerd exemplaar](../azure-monitor/insights/azure-sql.md#analyze-data-and-create-alerts).

## <a name="stream-into-event-hubs"></a>Streamen in Event Hubs

U kunt SQL Database metrische gegevens en Diagnostische logboeken streamen naar Event Hubs met behulp van de ingebouwde **stroom naar een event hub** optie in de Azure Portal. U kunt ook de Service Bus regel-ID inschakelen met behulp van een diagnostische instelling via Power shell-cmdlets, de Azure CLI of de Azure Monitor REST API.

### <a name="what-to-do-with-metrics-and-diagnostics-logs-in-event-hubs"></a>Wat u kunt doen met metrische gegevens en Diagnostische logboeken in Event Hubs

Nadat de geselecteerde gegevens zijn gestreamd naar Event Hubs, bent u een stap dichter bij het inschakelen van geavanceerde bewakings scenario's. Event Hubs fungeert als de voor deur voor een gebeurtenis pijplijn. Nadat de gegevens in een Event Hub zijn verzameld, kunnen ze worden getransformeerd en opgeslagen met behulp van een real-time analyse provider of een opslag adapter. Event Hubs de productie van een stroom van gebeurtenissen loskoppelt van het verbruik van die gebeurtenissen. Op deze manier hebben Event consumers toegang tot de gebeurtenissen op hun eigen schema. Zie voor meer informatie over Event Hubs:

- [Wat zijn Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Aan de slag met Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

U kunt gestreamde metrische gegevens in Event Hubs gebruiken om het volgende te doen:

- **Bekijk de service status door de gegevens van het warme pad te streamen naar Power BI**

   Met behulp van Event Hubs, Stream Analytics en Power BI kunt u eenvoudig uw metrische gegevens en informatie over het diagnosticeren naar bijna realtime inzichten op uw Azure-Services transformeren. Zie [Stream Analytics en Power bi](../stream-analytics/stream-analytics-power-bi-dashboard.md)voor een overzicht van het instellen van een event hub, het verwerken van gegevens met Stream Analytics en het gebruiken van Power bi als uitvoer.

- **Stream-logboeken naar logboek registratie van derden en telemetrie-stromen**

   Door Event Hubs streaming te gebruiken, kunt u uw metrische gegevens en diagnostische gegevens vastleggen in verschillende bewakings-en log Analytics-oplossingen van derden.

- **Een aangepast telemetrie-en logboek registratie platform bouwen**

   Hebt u al een speciaal gebouwde telemetrie-platform of overweegt u er een te bouwen? Met de uiterst schaal bare functie voor publiceren en abonneren van Event Hubs kunt u Diagnostische logboeken voor flexibele opname maken. Raadpleeg [dan de hand leiding van rosanova voor het gebruik van Event hubs in een telemetrie-platform voor wereld wijd schalen](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="stream-into-storage"></a>Streamen naar opslag

U kunt SQL Database metrische gegevens en Diagnostische logboeken opslaan in Azure Storage met behulp van het ingebouwde **Archief naar een opslag account** optie in de Azure Portal. U kunt ook opslag inschakelen met behulp van een diagnostische instelling via Power shell-cmdlets, de Azure CLI of de Azure Monitor REST API.

### <a name="schema-of-metrics-and-diagnostics-logs-in-the-storage-account"></a>Schema van metrische gegevens en Diagnostische logboeken in het opslag account

Nadat u metrische gegevens en de verzameling logboeken voor diagnostische gegevens hebt ingesteld, wordt een opslag container gemaakt in het opslag account dat u hebt geselecteerd bij het beschikbaar maken van de eerste rijen. De structuur van de blobs is:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Of, alleen meer:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

De naam van een BLOB kan bijvoorbeeld de volgende basis waarden hebben:

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

De naam van een BLOB voor het opslaan van gegevens uit een elastische pool ziet er als volgt uit:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

## <a name="data-retention-policy-and-pricing"></a>Bewaar beleid voor gegevens en prijzen

Als u Event Hubs of een opslag account selecteert, kunt u een Bewaar beleid opgeven. Dit beleid verwijdert gegevens die ouder zijn dan een geselecteerde tijds periode. Als u Log Analytics opgeeft, is het Bewaar beleid afhankelijk van de geselecteerde prijs categorie. In dit geval kunnen de meegeleverde gratis eenheden van gegevens opname de gratis bewaking van verschillende data bases per maand inschakelen. Voor elk verbruik van de telemetrie van diagnostische gegevens van de gratis eenheden kunnen kosten in rekening worden gebracht. Houd er rekening mee dat actieve data bases met zwaarere workloads meer gegevens opnemen dan niet-actieve data bases. Zie [prijzen voor log Analytics](https://azure.microsoft.com/pricing/details/monitor/)voor meer informatie.

Als u Azure SQL-analyse gebruikt, kunt u uw gegevens opname bewaken in de oplossing door de **werk ruimte OMS** te selecteren in het navigatie menu van Azure SQL-analyse en vervolgens **gebruik** en **geschatte kosten**te selecteren.

## <a name="metrics-and-logs-available"></a>Metrische gegevens en logboeken beschikbaar

Het bewaken van telemetrie voor Azure SQL Database, elastische Pools en beheerde exemplaren worden hieronder beschreven. Verzamelde bewakings-telemetrie in SQL Analytics kan worden gebruikt voor uw eigen aangepaste analyse en toepassings ontwikkeling met behulp van [Azure monitor logboek](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) taal van query's.

## <a name="basic-metrics"></a>Basis gegevens

Raadpleeg de volgende tabellen voor meer informatie over basis gegevens per resource.

> [!NOTE]
> De optie basis metrieken heette voorheen alle metrische gegevens. De wijziging is doorgevoerd in de naam en er is geen wijziging aangebracht in de metrische gegevens die worden gecontroleerd. Deze wijziging is doorgevoerd om in de toekomst extra metrische categorieën te kunnen introduceren.

### <a name="basic-metrics-for-elastic-pools"></a>Basis gegevens voor elastische Pools

|**Resource**|**Metrische gegevens**|
|---|---|
|Elastische pool|eDTU-percentage, eDTU gebruikt, eDTU-limiet, CPU-percentage, fysiek gegevens Lees percentage, logboek schrijf percentage, sessies percentage, werk nemers percentage, opslag, opslag percentage, opslag limiet, opslag percentage van XTP |

### <a name="basic-metrics-for-azure-sql-databases"></a>Basis gegevens voor Azure SQL-data bases

|**Resource**|**Metrische gegevens**|
|---|---|
|Azure SQL-database|DTU-percentage, gebruikte DTU, DTU-limiet, CPU-percentage, fysiek gegevens Lees percentage, logboek schrijf percentage, geslaagd/mislukt/geblokkeerd door Firewall verbindingen, percentages van werk nemers, opslag, opslag percentage, XTP opslag percentage en deadlocks |

## <a name="advanced-metrics"></a>Geavanceerde metrische gegevens

Raadpleeg de volgende tabel voor meer informatie over geavanceerde metrische gegevens.

|**Gegevens**|**Weergave naam voor metrische gegevens**|**Beschrijving**|
|---|---|---|
|tempdb_data_size| Data File grootte van tempdb |Data File grootte van tempdb-gegevens bestanden. Niet van toepassing op data warehouses. Deze metriek is beschikbaar voor data bases met behulp van het vCore-aankoop model met 2 vCores en hoger, of 200 DTU en hoger voor op DTU gebaseerde aankoop modellen. Deze metriek is momenteel niet beschikbaar voor grootschalige-data bases.|
|tempdb_log_size| Grootte van logboek bestanden tempdb |Grootte van KB-logboek bestanden. Niet van toepassing op data warehouses. Deze metriek is beschikbaar voor data bases met behulp van het vCore-aankoop model met 2 vCores en hoger, of 200 DTU en hoger voor op DTU gebaseerde aankoop modellen. Deze metriek is momenteel niet beschikbaar voor grootschalige-data bases.|
|tempdb_log_used_percent| Percentage gebruikt TempDB-logboek |Percentage gebruikt TempDB-logboek. Niet van toepassing op data warehouses. Deze metriek is beschikbaar voor data bases met behulp van het vCore-aankoop model met 2 vCores en hoger, of 200 DTU en hoger voor op DTU gebaseerde aankoop modellen. Deze metriek is momenteel niet beschikbaar voor grootschalige-data bases.|

## <a name="basic-logs"></a>Basis logboeken

Details van de telemetrie die beschikbaar zijn voor alle logboeken, worden beschreven in de onderstaande tabellen. Zie de [ondersteunde diagnostische logboek registratie](#supported-diagnostic-logging-for-azure-sql-databases-and-instance-databases) om te begrijpen welke logboeken worden ondersteund voor een bepaalde database versie: Azure SQL single, gepoolde of instance data base.

### <a name="resource-usage-stats-for-managed-instance"></a>Resource gebruik-statistieken voor het beheerde exemplaar

|Eigenschap|Beschrijving|
|---|---|
|TenantId|Uw Tenant-ID |
|SourceSystem|Altijd: Azure|
|TimeGenerated [UTC]|Tijds tempel waarop het logboek is vastgelegd |
|Type|Altijd: AzureDiagnostics |
|ResourceProvider|De naam van de resource provider. Altijd: micro soft. SQL |
|Categorie|De naam van de categorie. Altijd: ResourceUsageStats |
|Resource|Naam van de resource |
|ResourceType|De naam van het resource type. Altijd: MANAGEDINSTANCES |
|SubscriptionId|GUID van abonnement voor de data base |
|ResourceGroup|De naam van de resource groep voor de data base |
|LogicalServerName_s|Naam van het beheerde exemplaar |
|ResourceId|Resource-URI |
|SKU_s|Product-SKU Managed instance |
|virtual_core_count_s|Aantal beschik bare vCores |
|avg_cpu_percent_s|Gemiddeld CPU-percentage |
|reserved_storage_mb_s|Gereserveerde opslag capaciteit op het beheerde exemplaar |
|storage_space_used_mb_s|Gebruikte opslag ruimte op het beheerde exemplaar |
|io_requests_s|Aantal IOPS |
|io_bytes_read_s|Bytes van IOPS gelezen |
|io_bytes_written_s|Bytes van IOPS geschreven |

### <a name="query-store-runtime-statistics"></a>Runtime statistieken voor query Store

|Eigenschap|Beschrijving|
|---|---|
|TenantId|Uw Tenant-ID |
|SourceSystem|Altijd: Azure |
|TimeGenerated [UTC]|Tijds tempel waarop het logboek is vastgelegd |
|Type|Altijd: AzureDiagnostics |
|ResourceProvider|De naam van de resource provider. Altijd: micro soft. SQL |
|Categorie|De naam van de categorie. Altijd: QueryStoreRuntimeStatistics |
|OperationName|Naam van de bewerking. Altijd: QueryStoreRuntimeStatisticsEvent |
|Resource|Naam van de resource |
|ResourceType|De naam van het resource type. Altijd: SERVERS/data BASEs |
|SubscriptionId|GUID van abonnement voor de data base |
|ResourceGroup|De naam van de resource groep voor de data base |
|LogicalServerName_s|De naam van de server voor de data base |
|ElasticPoolName_s|De naam van de elastische pool voor de data base, indien van toepassing |
|DatabaseName_s|De naam van de data base |
|ResourceId|Resource-URI |
|query_hash_s|Query-hash |
|query_plan_hash_s|Query plan-hash |
|statement_sql_handle_s|SQL-ingang voor instructie |
|interval_start_time_d|Start date time offset van het interval in aantal maten van 1900-1-1 |
|interval_end_time_d|End date time offset van het interval in aantal ticks van 1900-1-1 |
|logical_io_writes_d|Totaal aantal logische i/o-schrijf bewerkingen |
|max_logical_io_writes_d|Maximum aantal logische i/o-schrijf bewerkingen per uitvoering |
|physical_io_reads_d|Totaal aantal fysieke IO-Lees bewerkingen |
|max_physical_io_reads_d|Maximum aantal logische IO-Lees bewerkingen per uitvoering |
|logical_io_reads_d|Totaal aantal logische IO-Lees bewerkingen |
|max_logical_io_reads_d|Maximum aantal logische IO-Lees bewerkingen per uitvoering |
|execution_type_d|Type uitvoering |
|count_executions_d|Aantal uitvoeringen van de query |
|cpu_time_d|Totale CPU-tijd verbruikt door de query in micro seconden |
|max_cpu_time_d|Maximale CPU-tijd verbruiker door één uitvoering in micro seconden |
|dop_d|Som van de mate van parallelle uitvoering |
|max_dop_d|Maximale mate van parallellisme die wordt gebruikt voor eenmalige uitvoering |
|rowcount_d|Totaal aantal geretourneerde rijen |
|max_rowcount_d|Maximum aantal rijen dat wordt geretourneerd tijdens één uitvoering |
|query_max_used_memory_d|Totale hoeveelheid geheugen die wordt gebruikt in KB |
|max_query_max_used_memory_d|Maximale hoeveelheid geheugen die wordt gebruikt door één uitvoering in KB |
|duration_d|Totale uitvoerings tijd in micro seconden |
|max_duration_d|Maximale uitvoerings tijd van één uitvoering |
|num_physical_io_reads_d|Totaal aantal fysieke Lees bewerkingen |
|max_num_physical_io_reads_d|Maximum aantal fysieke Lees bewerkingen per uitvoering |
|log_bytes_used_d|Totaal aantal gebruikte logboek bytes |
|max_log_bytes_used_d|Maximum aantal logboek bytes dat per uitvoering wordt gebruikt |
|query_id_d|ID van de query in query Store |
|plan_id_d|ID van het plan in query Store |

Meer informatie over [gegevens van runtime statistieken voor query Store](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql).

### <a name="query-store-wait-statistics"></a>Wacht statistieken voor query Store

|Eigenschap|Beschrijving|
|---|---|
|TenantId|Uw Tenant-ID |
|SourceSystem|Altijd: Azure |
|TimeGenerated [UTC]|Tijds tempel waarop het logboek is vastgelegd |
|Type|Altijd: AzureDiagnostics |
|ResourceProvider|De naam van de resource provider. Altijd: micro soft. SQL |
|Categorie|De naam van de categorie. Altijd: QueryStoreWaitStatistics |
|OperationName|Naam van de bewerking. Altijd: QueryStoreWaitStatisticsEvent |
|Resource|Naam van de resource |
|ResourceType|De naam van het resource type. Altijd: SERVERS/data BASEs |
|SubscriptionId|GUID van abonnement voor de data base |
|ResourceGroup|De naam van de resource groep voor de data base |
|LogicalServerName_s|De naam van de server voor de data base |
|ElasticPoolName_s|De naam van de elastische pool voor de data base, indien van toepassing |
|DatabaseName_s|De naam van de data base |
|ResourceId|Resource-URI |
|wait_category_s|Categorie van de wacht tijd |
|is_parameterizable_s|Is de query parameteriseerbaar |
|statement_type_s|Type van de instructie |
|statement_key_hash_s|Hash van de instructie sleutel |
|exec_type_d|Type uitvoering |
|total_query_wait_time_ms_d|Totale wacht tijd van de query op de specifieke wacht categorie |
|max_query_wait_time_ms_d|Maximale wacht tijd van de query in een afzonderlijke uitvoering voor de specifieke wacht categorie |
|query_param_type_d|0 |
|query_hash_s|Query-hash in query Store |
|query_plan_hash_s|Query plan-hash in query Store |
|statement_sql_handle_s|Instructie-ingang in query Store |
|interval_start_time_d|Start date time offset van het interval in aantal maten van 1900-1-1 |
|interval_end_time_d|End date time offset van het interval in aantal ticks van 1900-1-1 |
|count_executions_d|Aantal uitvoeringen van de query |
|query_id_d|ID van de query in query Store |
|plan_id_d|ID van het plan in query Store |

Meer informatie over [query Store-wacht statistieken](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql).

### <a name="errors-dataset"></a>Gegevensset met fouten

|Eigenschap|Beschrijving|
|---|---|
|TenantId|Uw Tenant-ID |
|SourceSystem|Altijd: Azure |
|TimeGenerated [UTC]|Tijds tempel waarop het logboek is vastgelegd |
|Type|Altijd: AzureDiagnostics |
|ResourceProvider|De naam van de resource provider. Altijd: micro soft. SQL |
|Categorie|De naam van de categorie. Altijd: fouten |
|OperationName|Naam van de bewerking. Altijd: ErrorEvent |
|Resource|Naam van de resource |
|ResourceType|De naam van het resource type. Altijd: SERVERS/data BASEs |
|SubscriptionId|GUID van abonnement voor de data base |
|ResourceGroup|De naam van de resource groep voor de data base |
|LogicalServerName_s|De naam van de server voor de data base |
|ElasticPoolName_s|De naam van de elastische pool voor de data base, indien van toepassing |
|DatabaseName_s|De naam van de data base |
|ResourceId|Resource-URI |
|Bericht|Fout bericht in tekst zonder opmaak |
|user_defined_b|Is de door de gebruiker gedefinieerde fout |
|error_number_d|Foutcode |
|Severity|Ernst van de fout |
|state_d|Status van de fout |
|query_hash_s|Query-hash van de mislukte query, indien beschikbaar |
|query_plan_hash_s|Query plan-hash van de mislukte query, indien beschikbaar |

Meer informatie over [SQL Server fout berichten](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors?view=sql-server-ver15).

### <a name="database-wait-statistics-dataset"></a>Gegevensset data base wacht statistieken

|Eigenschap|Beschrijving|
|---|---|
|TenantId|Uw Tenant-ID |
|SourceSystem|Altijd: Azure |
|TimeGenerated [UTC]|Tijds tempel waarop het logboek is vastgelegd |
|Type|Altijd: AzureDiagnostics |
|ResourceProvider|De naam van de resource provider. Altijd: micro soft. SQL |
|Categorie|De naam van de categorie. Altijd: DatabaseWaitStatistics |
|OperationName|Naam van de bewerking. Altijd: DatabaseWaitStatisticsEvent |
|Resource|Naam van de resource |
|ResourceType|De naam van het resource type. Altijd: SERVERS/data BASEs |
|SubscriptionId|GUID van abonnement voor de data base |
|ResourceGroup|De naam van de resource groep voor de data base |
|LogicalServerName_s|De naam van de server voor de data base |
|ElasticPoolName_s|De naam van de elastische pool voor de data base, indien van toepassing |
|DatabaseName_s|De naam van de data base |
|ResourceId|Resource-URI |
|wait_type_s|Naam van het wacht type |
|start_utc_date_t [UTC]|Begin tijd van gemeten periode |
|end_utc_date_t [UTC]|Eind tijd van gemeten periode |
|delta_max_wait_time_ms_d|Maximale wacht tijd per uitvoering |
|delta_signal_wait_time_ms_d|Totale wacht tijd voor signalen |
|delta_wait_time_ms_d|Totale wacht tijd in de periode |
|delta_waiting_tasks_count_d|Aantal wachtende taken |

Meer informatie over [Data Base-wacht statistieken](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql).

### <a name="time-outs-dataset"></a>Gegevensset voor time-outs

|Eigenschap|Beschrijving|
|---|---|
|TenantId|Uw Tenant-ID |
|SourceSystem|Altijd: Azure |
|TimeGenerated [UTC]|Tijds tempel waarop het logboek is vastgelegd |
|Type|Altijd: AzureDiagnostics |
|ResourceProvider|De naam van de resource provider. Altijd: micro soft. SQL |
|Categorie|De naam van de categorie. Altijd: time-outs |
|OperationName|Naam van de bewerking. Altijd: TimeoutEvent |
|Resource|Naam van de resource |
|ResourceType|De naam van het resource type. Altijd: SERVERS/data BASEs |
|SubscriptionId|GUID van abonnement voor de data base |
|ResourceGroup|De naam van de resource groep voor de data base |
|LogicalServerName_s|De naam van de server voor de data base |
|ElasticPoolName_s|De naam van de elastische pool voor de data base, indien van toepassing |
|DatabaseName_s|De naam van de data base |
|ResourceId|Resource-URI |
|error_state_d|Fout status code |
|query_hash_s|Query-hash, indien beschikbaar |
|query_plan_hash_s|Query plan-hash, indien beschikbaar |

### <a name="blockings-dataset"></a>Gegevensset voor blok keringen

|Eigenschap|Beschrijving|
|---|---|
|TenantId|Uw Tenant-ID |
|SourceSystem|Altijd: Azure |
|TimeGenerated [UTC]|Tijds tempel waarop het logboek is vastgelegd |
|Type|Altijd: AzureDiagnostics |
|ResourceProvider|De naam van de resource provider. Altijd: micro soft. SQL |
|Categorie|De naam van de categorie. Altijd: blokken |
|OperationName|Naam van de bewerking. Altijd: BlockEvent |
|Resource|Naam van de resource |
|ResourceType|De naam van het resource type. Altijd: SERVERS/data BASEs |
|SubscriptionId|GUID van abonnement voor de data base |
|ResourceGroup|De naam van de resource groep voor de data base |
|LogicalServerName_s|De naam van de server voor de data base |
|ElasticPoolName_s|De naam van de elastische pool voor de data base, indien van toepassing |
|DatabaseName_s|De naam van de data base |
|ResourceId|Resource-URI |
|lock_mode_s|Vergrendelings modus die wordt gebruikt door de query |
|resource_owner_type_s|Eigenaar van de vergren deling |
|blocked_process_filtered_s|Rapport-XML geblokkeerd proces |
|duration_d|Duur van de vergren deling in micro seconden |

### <a name="deadlocks-dataset"></a>Impasses-gegevensset

|Eigenschap|Beschrijving|
|---|---|
|TenantId|Uw Tenant-ID |
|SourceSystem|Altijd: Azure |
|TimeGenerated [UTC] |Tijds tempel waarop het logboek is vastgelegd |
|Type|Altijd: AzureDiagnostics |
|ResourceProvider|De naam van de resource provider. Altijd: micro soft. SQL |
|Categorie|De naam van de categorie. Altijd: deadlocks |
|OperationName|Naam van de bewerking. Altijd: DeadlockEvent |
|Resource|Naam van de resource |
|ResourceType|De naam van het resource type. Altijd: SERVERS/data BASEs |
|SubscriptionId|GUID van abonnement voor de data base |
|ResourceGroup|De naam van de resource groep voor de data base |
|LogicalServerName_s|De naam van de server voor de data base |
|ElasticPoolName_s|De naam van de elastische pool voor de data base, indien van toepassing |
|DatabaseName_s|De naam van de data base |
|ResourceId|Resource-URI |
|deadlock_xml_s|XML van deadlock rapport |

### <a name="automatic-tuning-dataset"></a>Gegevensset voor automatisch afstemmen

|Eigenschap|Beschrijving|
|---|---|
|TenantId|Uw Tenant-ID |
|SourceSystem|Altijd: Azure |
|TimeGenerated [UTC]|Tijds tempel waarop het logboek is vastgelegd |
|Type|Altijd: AzureDiagnostics |
|ResourceProvider|De naam van de resource provider. Altijd: micro soft. SQL |
|Categorie|De naam van de categorie. Altijd: AutomaticTuning |
|Resource|Naam van de resource |
|ResourceType|De naam van het resource type. Altijd: SERVERS/data BASEs |
|SubscriptionId|GUID van abonnement voor de data base |
|ResourceGroup|De naam van de resource groep voor de data base |
|LogicalServerName_s|De naam van de server voor de data base |
|LogicalDatabaseName_s|De naam van de data base |
|ElasticPoolName_s|De naam van de elastische pool voor de data base, indien van toepassing |
|DatabaseName_s|De naam van de data base |
|ResourceId|Resource-URI |
|RecommendationHash_s|Unieke hash van aanbeveling voor automatisch afstemmen |
|OptionName_s|Automatische afstemmings bewerking |
|Schema_s|Database schema |
|Table_s|Betrokken tabel |
|IndexName_s|Index naam |
|IndexColumns_s|kolom naam |
|IncludedColumns_s|Opgenomen kolommen |
|EstimatedImpact_s|Geschatte impact van de JSON van de aanbeveling voor automatisch afstemmen |
|Event_s|Type gebeurtenis waarbij automatisch afstemmen |
|Timestamp_t|Tijds tempel laatst bijgewerkt |

### <a name="intelligent-insights-dataset"></a>Intelligent Insights gegevensset

Meer informatie over de [intelligent Insights-logboek indeling](sql-database-intelligent-insights-use-diagnostics-log.md).

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over het inschakelen van logboek registratie en het begrijpen van de metrische gegevens en logboek categorieën die worden ondersteund door de verschillende Azure-Services:

- [Overzicht van metrische gegevens in Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
- [Overzicht van Azure Diagnostics-logboeken](../azure-monitor/platform/platform-logs-overview.md)

Lees voor meer informatie over Event Hubs:

- [Wat is Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Aan de slag met Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Voor meer informatie over het instellen van waarschuwingen op basis van telemetrie in log Analytics raadpleegt u:

- [Waarschuwingen voor SQL Database en een beheerd exemplaar maken](../azure-monitor/insights/azure-sql.md#analyze-data-and-create-alerts)
