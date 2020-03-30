---
title: Streaming exporteren van statistieken en bronlogboeken configureren
description: Meer informatie over het configureren van streaming-export van statistieken en bronlogboeken, inclusief intelligente diagnostische analyse van Azure SQL Database naar de bestemming van uw keuze om informatie op te slaan over het gebruik van resources en queryuitvoeringsstatistieken.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: 880072c9865e38e181869506e35968767fa95e8a
ms.sourcegitcommit: d0fd35f4f0f3ec71159e9fb43fcd8e89d653f3f2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80387900"
---
# <a name="configure-streaming-export-of-azure-sql-database-diagnostic-telemetry"></a>Streaming-export van diagnostische telemetrie van Azure SQL Database configureren

In dit artikel vindt u meer informatie over de prestatiestatistieken en bronlogboeken voor Azure SQL Database die u exporteren naar een van de verschillende bestemmingen voor analyse. U leert hoe u de streaming-export van deze diagnostische telemetrie configureert via de Azure-portal, PowerShell, Azure CLI, de REST API en Azure Resource Manager-sjablonen.

Je leert ook over de bestemmingen waarnaar je deze diagnostische telemetrie streamen en hoe je kiezen tussen deze keuzes. Uw bestemmingsopties zijn:

- [Log Analytics en SQL Analytics](#stream-into-sql-analytics)
- [Gebeurtenishubs](#stream-into-event-hubs)
- [Azure Storage](#stream-into-azure-storage)

## <a name="diagnostic-telemetry-for-export-for-azure-sql-database"></a>Diagnostische telemetrie voor exporteren voor Azure SQL Database

Het belangrijkste onder de diagnostische telemetrie die u exporteren is het Intelligent Insights (SQLInsights) logboek. [Intelligent Insights](sql-database-intelligent-insights.md) maakt gebruik van ingebouwde intelligentie om het gebruik van gegevens continu te monitoren door middel van kunstmatige intelligentie en storende gebeurtenissen te detecteren die slechte prestaties veroorzaken. Eenmaal gedetecteerd, wordt een gedetailleerde analyse uitgevoerd die een Intelligent Insights-logboek genereert met een intelligente beoordeling van het probleem. Deze beoordeling bestaat uit een hoofdoorzaakanalyse van het probleem van de databaseprestaties en, waar mogelijk, aanbevelingen voor prestatieverbeteringen. U moet de streaming-export van dit logboek configureren om de inhoud ervan te bekijken.

Naast het streamen van de export van het Intelligent Insights-logboek, u ook verschillende prestatiestatistieken en aanvullende SQL Database-logboeken exporteren. In de volgende tabel worden de prestatiestatistieken en resourceslogs beschreven die u configureren voor streaming exporteren naar een van de verschillende bestemmingen. Deze diagnostische telemetrie kan worden geconfigureerd voor afzonderlijke databases, elastische pools en gepoolde databases en beheerde instanties en instantiedatabases.

| Diagnostische telemetrie voor databases | Ondersteuning voor één database en gepoolde database | Ondersteuning voor beheerde instantiedatabase |
| :------------------- | ----- | ----- |
| [Basisstatistieken](#basic-metrics): Bevat DTU/CPU-percentage, DTU/CPU-limiet, leespercentage fysieke gegevens, schrijfpercentage van logboeken, Geslaagd/Mislukt/Geblokkeerd door firewallverbindingen, sessiespercentage, werknemerspercentage, opslag-, opslagpercentage en XTP-opslagpercentage. | Ja | Nee |
| [Instance and App Advanced](#advanced-metrics): Bevat tempdb-systeemdatabasegegevens en logbestandsgrootte en tempdb-percentlogbestand dat wordt gebruikt. | Ja | Nee |
| [QueryStoreRuntimeStatistics:](#query-store-runtime-statistics)Bevat informatie over de queryruntimestatistieken, zoals CPU-gebruik en queryduurstatistieken. | Ja | Ja |
| [QueryStoreWaitStatistics:](#query-store-wait-statistics)Bevat informatie over de query wachtstatistieken (waar uw query's op hebben gewacht) zoals CPU, LOG en VERGRENDELING. | Ja | Ja |
| [Fouten:](#errors-dataset)Bevat informatie over SQL-fouten in een database. | Ja | Ja |
| [DatabaseWaitStatistics](#database-wait-statistics-dataset): Bevat informatie over hoeveel tijd een database heeft besteed aan het wachten op verschillende wachttypen. | Ja | Nee |
| [Time-outs](#time-outs-dataset): Bevat informatie over time-outs in een database. | Ja | Nee |
| [Blokken:](#blockings-dataset)Bevat informatie over het blokkeren van gebeurtenissen in een database. | Ja | Nee |
| [Impasses:](#deadlocks-dataset)Bevat informatie over impassegebeurtenissen in een database. | Ja | Nee |
| [Automatisch afstemmen:](#automatic-tuning-dataset)bevat informatie over automatische tuningaanbevelingen voor een database. | Ja | Nee |
| [SQLInsights](#intelligent-insights-dataset): Bevat intelligente inzichten in prestaties voor een database. Zie [Intelligent Insights](sql-database-intelligent-insights.md)voor meer informatie. | Ja | Ja |

> [!NOTE]
> Diagnostische instellingen kunnen niet worden geconfigureerd voor de **systeemdatabases,** zoals master-, msdb-, model-, resource- en tempdb-databases.

## <a name="streaming-export-destinations"></a>Streaming exportbestemmingen

Deze diagnostische telemetrie kan worden gestreamd naar een van de volgende Azure-bronnen voor analyse.

- **[Log Analytics-werkruimte:](#stream-into-sql-analytics)**

  Gegevens die naar een [Log Analytics-werkruimte](../azure-monitor/platform/resource-logs-collect-workspace.md) worden gestreamd, kunnen worden verbruikt door [SQL Analytics.](../azure-monitor/insights/azure-sql.md) SQL Analytics is een cloud-only monitoringoplossing die intelligente monitoring van uw databases biedt met prestatierapporten, waarschuwingen en aanbevelingen voor mitigatie. Gegevens die naar een Log Analytics-werkruimte worden gestreamd, kunnen worden geanalyseerd met andere monitoringgegevens die worden verzameld en stelt u ook in staat andere Azure Monitor-functies te gebruiken, zoals waarschuwingen en visualisaties
- **[Azure-gebeurtenishubs:](#stream-into-event-hubs)**

  Gegevens die naar een Azure Event Hub worden [gestreamd,](../azure-monitor/platform/resource-logs-stream-event-hubs.md)bieden de volgende functionaliteit:

  - **Logboekregistratie van derden streamen naar logboekregistratie- en telemetriesystemen**van derden: Stream al uw statistieken en bronlogboeken naar één gebeurtenishub om logboekgegevens te pipeteren naar een SIEM- of loganalysetool van derden.
  - **Bouw een aangepast telemetrie- en logboekplatform:** met het zeer schaalbare publicatie-abonneerbare karakter van gebeurtenishubs u flexibel statistieken en bronlogboeken innemen op een aangepast telemetrieplatform. Zie [Het ontwerpen en dimensioneren van een telemetrieplatform op globale schaal op Azure-gebeurtenishubs](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/) voor meer informatie.
  - **Bekijk de status van de service door gegevens te streamen naar Power BI:** gebruik Gebeurtenishubs, Stream Analytics en Power BI om uw diagnostische gegevens om te zetten in bijna realtime inzichten over uw Azure-services. Zie [Stream Analytics en Power BI: een realtime analysedashboard voor het streamen van gegevens](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-power-bi-dashboard) voor meer informatie over deze oplossing.
- **[Azure Storage:](#stream-into-azure-storage)**

  Met gegevens die naar [Azure Storage](../azure-monitor/platform/resource-logs-collect-storage.md) worden gestreamd, u enorme hoeveelheden diagnostische telemetrie archiveren voor een fractie van de kosten van de vorige twee streamingopties.

Deze diagnostische telemetrie die naar een van deze bestemmingen wordt gestreamd, kan worden gebruikt om resourcegebruik en queryuitvoeringsstatistieken te meten voor eenvoudigere prestatiebewaking.

![Architectuur](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="enable-and-configure-the-streaming-export-of-diagnostic-telemetry"></a>De streaming-export van diagnostische telemetrie inschakelen en configureren

U metrische gegevens en diagnostische telemetrielogboekregistratie inschakelen en beheren met behulp van een van de volgende methoden:

- Azure Portal
- PowerShell
- Azure-CLI
- Azure Monitor REST API
- Azure Resource Manager-sjabloon

> [!NOTE]
> Zie [Controle instellen voor uw database-](sql-database-auditing.md#setup-auditing) en [controlelogboeken instellen in Azure Monitor-logboeken en Azure Event Hubs](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/SQL-Audit-logs-in-Azure-Log-Analytics-and-Azure-Event-Hubs/ba-p/386242)als u het streamen van beveiligingslogboeken voor controle wilt inschakelen.

## <a name="configure-the-streaming-export-of-diagnostic-telemetry"></a>De streaming-export van diagnostische telemetrie configureren

U het menu **Diagnostische instellingen** in de Azure-portal gebruiken om streaming van diagnostische telemetrie in te schakelen en te configureren. Daarnaast u PowerShell, de Azure CLI, de [REST API](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)en [Resource Manager-sjablonen](../azure-monitor/platform/diagnostic-settings-template.md) gebruiken om streaming van diagnostische telemetrie te configureren. U instellen dat de volgende bestemmingen de diagnostische telemetrie streamen: Azure Storage, Azure Event Hubs en Azure Monitor-logboeken.

> [!IMPORTANT]
> Het streamen van diagnostische telemetrie is standaard niet ingeschakeld.

Selecteer een van de volgende tabbladen voor stapsgewijze richtlijnen voor het configureren van de streaming-export van diagnostische telemetrie in de Azure-portal en voor scripts voor het uitvoeren van hetzelfde met PowerShell en de Azure CLI.

# <a name="azure-portal"></a>[Azure-portal](#tab/azure-portal)

### <a name="elastic-pools"></a>Pools voor Elastic Database

U een elastische poolbron instellen om de volgende diagnostische telemetrie te verzamelen:

| Resource | Telemetrie controleren |
| :------------------- | ------------------- |
| **Elastische pool** | [Basisstatistieken](sql-database-metrics-diag-logging.md#basic-metrics) bevatten eDTU/CPU-percentage, eDTU/CPU-limiet, het leespercentage van fysieke gegevens, het schrijfpercentage van logboeken, het percentage sessies, het percentage werknemers, de opslag, het opslagpercentage, de opslaglimiet en het XTP-opslagpercentage. |

Als u het streamen van diagnostische telemetrie voor elastische groepen en samengevoegde databases wilt configureren, moet u elk afzonderlijk configureren:

- Streaming van diagnostische telemetrie inschakelen voor een elastische groep
- Streaming van diagnostische telemetrie voor elke database in elastische groep inschakelen

De elastische poolcontainer heeft zijn eigen telemetrie, gescheiden van de telemetrie van elke afzonderlijke samengevoegde database.

Voer de volgende stappen uit om het streamen van diagnostische telemetrie voor een elastische poolbron in te schakelen:

1. Ga naar de **elastische poolbron** in Azure-portal.
2. Selecteer **Diagnostische instellingen**.
3. Selecteer **Diagnostische gegevens inschakelen** als er geen eerdere instellingen bestaan of selecteer De instelling **Bewerken** selecteren om een vorige instelling te bewerken.

   ![Diagnostische gegevens inschakelen voor elastische pools](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-enable.png)

4. Voer een instellingsnaam in voor uw eigen referentie.
5. Selecteer een doelbron voor de gegevens over streamingdiagnostiek: **Archiveren naar opslagaccount,** **Streamen naar een gebeurtenishub**of **Verzenden naar logboekanalyse**.
6. Selecteer voor logboekanalyses Een nieuwe werkruimte **configureren** en maken door **+Nieuwe werkruimte maken**te selecteren of een bestaande werkruimte te selecteren.
7. Schakel het selectievakje in voor diagnostische telemetrie voor elastische poolgegevens: **Basisstatistieken.**
   ![Diagnostische gegevens configureren voor elastische pools](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-selection.png)

8. Selecteer **Opslaan**.
9. Configureer bovendien het streamen van diagnostische telemetrie voor elke database binnen de elastische groep die u wilt controleren door de volgende stappen te volgen die in de volgende sectie zijn beschreven.

> [!IMPORTANT]
> Naast het configureren van diagnostische telemetrie voor een elastische pool, moet u ook diagnostische telemetrie configureren voor elke database in de elastische groep.

### <a name="single-or-pooled-database"></a>Enkele of samengevoegde database

U één of samengevoegde databasebron instellen om de volgende diagnostische telemetrie te verzamelen:

| Resource | Telemetrie controleren |
| :------------------- | ------------------- |
| **Enkele of samengevoegde database** | [Basisstatistieken](sql-database-metrics-diag-logging.md#basic-metrics) bevatten DTU-percentage, DTU-limiet, DTU-limiet, CPU-percentage, leespercentage voor fysieke gegevens, schrijfpercentage voor logboeken, Geslaagd/Mislukt/Geblokkeerd door firewallverbindingen, sessiespercentage, werknemerspercentage, opslag, opslagpercentage, XTP-opslagpercentage en impasses. |

Voer de volgende stappen uit om het streamen van diagnostische telemetrie voor één of een samengevoegde database in te schakelen:

1. Ga naar Azure **SQL-databasebron.**
2. Selecteer **Diagnostische instellingen**.
3. Selecteer **Diagnostische gegevens inschakelen** als er geen eerdere instellingen bestaan of selecteer De instelling **Bewerken** selecteren om een vorige instelling te bewerken. U maximaal drie parallelle verbindingen maken om diagnostische telemetrie te streamen.
4. Selecteer **Diagnostische instelling toevoegen** om parallelle streaming van diagnostische gegevens naar meerdere bronnen te configureren.

   ![Diagnostische gegevens inschakelen voor afzonderlijke en samengevoegde databases](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-enable.png)

5. Voer een instellingsnaam in voor uw eigen referentie.
6. Selecteer een doelbron voor de gegevens over streamingdiagnostiek: **Archiveren naar opslagaccount,** **Streamen naar een gebeurtenishub**of **Verzenden naar logboekanalyse**.
7. Schakel voor de standaard, op gebeurtenissen gebaseerde monitoringervaring de volgende selectievakjes in voor telemetrie van databasediagnoselogboeken: **SQLInsights**, **AutomaticTuning**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics**, **Errors**, **DatabaseWaitStatistics**, **Timeouts**, **Blocks**en **Deadlocks**.
8. Schakel het selectievakje voor **basisstatistieken** van één minuut in voor een geavanceerde, op één minuut gebaseerde bewakingservaring.

   ![Diagnostische gegevens configureren voor afzonderlijke, samengevoegde of instantiedatabases](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-selection.png)
9. Selecteer **Opslaan**.
10. Herhaal deze stappen voor elke database die u wilt controleren.

> [!TIP]
> Herhaal deze stappen voor elke afzonderlijke en samengevoegde database die u wilt controleren.

### <a name="managed-instance"></a>Beheerd exemplaar

U een beheerde instantiebron instellen om de volgende diagnostische telemetrie te verzamelen:

| Resource | Telemetrie controleren |
| :------------------- | ------------------- |
| **Beheerd exemplaar** | [ResourceUsageStats](#resource-usage-stats-for-managed-instances) bevat vCores, gemiddeld CPU-percentage, IO-aanvragen, gelezen/geschreven bytes, gereserveerde opslagruimte en gebruikte opslagruimte. |

Als u het streamen van diagnostische telemetrie voor beheerde instantie- en instantiedatabases wilt configureren, moet u elk afzonderlijk configureren:

- Streaming van diagnostische telemetrie inschakelen voor beheerde instantie
- Streaming van diagnostische telemetrie voor elke instantiedatabase inschakelen

De beheerde instantiecontainer heeft zijn eigen telemetrie, gescheiden van de telemetrie van elke instantiedatabase.

Voer de volgende stappen uit om streaming van diagnostische telemetrie voor een beheerde instantiebron in te schakelen:

1. Ga naar de **beheerde instantiebron** in Azure-portal.
2. Selecteer **Diagnostische instellingen**.
3. Selecteer **Diagnostische gegevens inschakelen** als er geen eerdere instellingen bestaan of selecteer De instelling **Bewerken** selecteren om een vorige instelling te bewerken.

   ![Diagnostische gegevens inschakelen voor beheerde instantie](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-enable.png)

4. Voer een instellingsnaam in voor uw eigen referentie.
5. Selecteer een doelbron voor de gegevens over streamingdiagnostiek: **Archiveren naar opslagaccount,** **Streamen naar een gebeurtenishub**of **Verzenden naar logboekanalyse**.
6. Selecteer voor logboekanalyses De optie Een nieuwe werkruimte **configureren** en maken door **+Nieuwe werkruimte maken**te selecteren of een bestaande werkruimte te gebruiken.
7. Schakel het selectievakje in voor bijvoorbeeld diagnostische telemetrie: **ResourceUsageStats**.

   ![Diagnostische gegevens configureren voor beheerde instantie](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-selection.png)

8. Selecteer **Opslaan**.
9. Configureer bovendien het streamen van diagnostische telemetrie voor elke instantiedatabase in de beheerde instantie die u wilt controleren door de stappen te volgen die in de volgende sectie zijn beschreven.

> [!IMPORTANT]
> Naast het configureren van diagnostische telemetrie voor een beheerde instantie, moet u ook diagnostische telemetrie configureren voor elke instantiedatabase.

### <a name="instance-database"></a>Instantiedatabase

U een instantiedatabasebron instellen om de volgende diagnostische telemetrie te verzamelen:

| Resource | Telemetrie controleren |
| :------------------- | ------------------- |
| **Instantiedatabase** | [ResourceUsageStats](#resource-usage-stats-for-managed-instances) bevat vCores, gemiddeld CPU-percentage, IO-aanvragen, gelezen/geschreven bytes, gereserveerde opslagruimte en gebruikte opslagruimte. |

Voer de volgende stappen uit om het streamen van diagnostische telemetrie voor een instantiedatabase in te schakelen:

1. Ga naar **instantiedatabasebron** binnen beheerde instantie.
2. Selecteer **Diagnostische instellingen**.
3. Selecteer **Diagnostische gegevens inschakelen** als er geen eerdere instellingen bestaan of selecteer De instelling **Bewerken** selecteren om een vorige instelling te bewerken.
   - U maximaal drie (3) parallelle verbindingen maken om diagnostische telemetrie te streamen.
   - Selecteer **+Diagnostische instelling toevoegen** om parallelle streaming van diagnostische gegevens naar meerdere bronnen te configureren.

   ![Diagnostische gegevens inschakelen voor bijvoorbeeld databases](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-enable.png)

4. Voer een instellingsnaam in voor uw eigen referentie.
5. Selecteer een doelbron voor de gegevens over streamingdiagnostiek: **Archiveren naar opslagaccount,** **Streamen naar een gebeurtenishub**of **Verzenden naar logboekanalyse**.
6. Schakel de selectievakjes in voor diagnostische telemetrie voor database: **SQLInsights**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics**en **Fouten**.
   ![Diagnostische gegevens configureren voor bijvoorbeeld databases](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-selection.png)
7. Selecteer **Opslaan**.
8. Herhaal deze stappen voor elke instantiedatabase die u wilt controleren.

> [!TIP]
> Herhaal deze stappen voor elke instantiedatabase die u wilt controleren.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> De PowerShell Azure Resource Manager-module wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkelingen zijn voor de Az.Sql-module. Zie [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)voor deze cmdlets. De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn nagenoeg identiek.

U logboekregistratie en logboekregistratie inschakelen met PowerShell.

- Als u de opslag van statistieken en bronlogboeken in een opslagaccount wilt inschakelen, gebruikt u deze opdracht:

  ```powershell
  Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
  ```

  De opslagaccount-id is de bron-id voor het doelopslagaccount.

- Als u het streamen van statistieken en bronlogboeken naar een gebeurtenishub wilt inschakelen, gebruikt u deze opdracht:

  ```powershell
  Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
  ```

  De id voor de Azure Service Bus-regel is een tekenreeks met deze indeling:

  ```powershell
  {service bus resource ID}/authorizationrules/{key name}
  ```

- Als u het verzenden van statistieken en bronlogboeken naar een log-analysewerkruimte wilt inschakelen, gebruikt u deze opdracht:

  ```powershell
  Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
  ```

- U kunt de resource-id van de Log Analytics-werkruimte achterhalen door de volgende opdracht te gebruiken:

  ```powershell
  (Get-AzOperationalInsightsWorkspace).ResourceId
  ```

U kunt deze parameters combineren om meerdere uitvoeropties in te schakelen.

**Meerdere Azure-bronnen configureren**

Als u meerdere abonnementen wilt ondersteunen, gebruikt u het PowerShell-script van [Azure-bronstatistieken inschakelen met PowerShell](https://blogs.technet.microsoft.com/msoms/20../../enable-azure-resource-metrics-logging-using-powershell/).

Geef de werkruimtebron-id \<$WSID\> als parameter `Enable-AzureRMDiagnostics.ps1` bij het uitvoeren van het script om diagnostische gegevens van meerdere bronnen naar de werkruimte te verzenden.

- Als u de \<\> werkruimte-id $WSID van de bestemming voor uw diagnostische gegevens wilt krijgen, gebruikt u het volgende script:

  ```powershell
  $WSID = "/subscriptions/<subID>/resourcegroups/<RG_NAME>/providers/microsoft.operationalinsights/workspaces/<WS_NAME>"
  .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
  ```

  Vervang \<sub-ID\> door \<de\> abonnements-ID, RG_NAME \<\> met de naam van de brongroep en WS_NAME door de naam van de werkruimte.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

U logboekregistratie met statistieken en diagnostische gegevens inschakelen met behulp van de Azure CLI.

> [!IMPORTANT]
> Scripts om diagnostische logboekregistratie in te schakelen, worden ondersteund voor Azure CLI v1.0. Azure CLI v2.0 wordt op dit moment niet ondersteund.

- Als u de opslag van statistieken en bronlogboeken in een opslagaccount wilt inschakelen, gebruikt u deze opdracht:

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
  ```

  De opslagaccount-id is de bron-id voor het doelopslagaccount.

- Als u het streamen van statistieken en bronlogboeken naar een gebeurtenishub wilt inschakelen, gebruikt u deze opdracht:

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
  ```

  De regel-ID van de servicebus is een tekenreeks met deze indeling:

  ```azurecli-interactive
  {service bus resource ID}/authorizationrules/{key name}
  ```

- Als u het verzenden van statistieken en bronlogboeken naar een log-analysewerkruimte wilt inschakelen, gebruikt u de opdracht:

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
  ```

U kunt deze parameters combineren om meerdere uitvoeropties in te schakelen.

---

## <a name="stream-into-sql-analytics"></a>Streamen naar SQL Analytics

SQL Database-statistieken en bronlogboeken die worden gestreamd naar een Log Analytics-werkruimte kunnen worden verbruikt door Azure SQL Analytics. Azure SQL Analytics is een cloudoplossing die de prestaties van afzonderlijke databases, elastische pools en gepoolde databases bewaakt en beheerde instanties en instantiedatabases op schaal en in meerdere abonnementen. Het kan u helpen bij het verzamelen en visualiseren van Azure SQL Database prestatiestatistieken en het heeft ingebouwde informatie voor het oplossen van prestaties.

![Overzicht van Azure SQL Analytics](../azure-monitor/insights/media/azure-sql/azure-sql-sol-overview.png)

### <a name="installation-overview"></a>Overzicht van de installatie

U een verzameling Azure SQL-databases met Azure SQL Analytics controleren door de volgende stappen uit te voeren:

1. Maak een Azure SQL Analytics-oplossing vanuit de Azure Marketplace.
2. Maak een Log Analytics-werkruimte in de oplossing.
3. Configureer databases om diagnostische telemetrie naar de werkruimte te streamen.

U de streaming-export van deze diagnostische telemetrie configureren met de ingebouwde optie **Verzenden naar logboekanalyse** op het tabblad Diagnostische instellingen in de Azure-portal. U ook streaming naar een Log Analytics-werkruimte inschakelen met behulp van diagnostische instellingen via [PowerShell-cmdlets,](sql-database-metrics-diag-logging.md?tabs=azure-powershell#configure-the-streaming-export-of-diagnostic-telemetry)de [Azure CLI,](sql-database-metrics-diag-logging.md?tabs=azure-cli#configure-the-streaming-export-of-diagnostic-telemetry)de [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)of Resource [Manager-sjablonen.](../azure-monitor/platform/diagnostic-settings-template.md)

### <a name="create-an-azure-sql-analytics-resource"></a>Een Azure SQL Analytics-bron maken

1. Zoek naar Azure SQL Analytics in Azure Marketplace en selecteer deze.

   ![Zoeken naar Azure SQL Analytics in portal](./media/sql-database-metrics-diag-logging/sql-analytics-in-marketplace.png)

2. Selecteer **Maken** op het overzichtsscherm van de oplossing.

3. Vul het Azure SQL Analytics-formulier in met de aanvullende informatie die nodig is: naam van de werkruimte, abonnement, brongroep, locatie en prijscategorie.

   ![Azure SQL Analytics configureren in portal](./media/sql-database-metrics-diag-logging/sql-analytics-configuration-blade.png)

4. Selecteer **OK** om te bevestigen en selecteer **Vervolgens Maken**.

### <a name="configure-the-resource-to-record-metrics-and-resource-logs"></a>De bron configureren om metrische gegevens en bronlogboeken op te nemen

U moet diagnostische telemetriestreaming afzonderlijk configureren voor afzonderlijke en samengevoegde databases, elastische pools, beheerde instanties en instantiedatabases. De eenvoudigste manier om te configureren waar een resource records metrics is met behulp van de Azure-portal. Zie [De streamingexport van diagnostische telemetrie configureren](sql-database-metrics-diag-logging.md?tabs=azure-portal#configure-the-streaming-export-of-diagnostic-telemetry)voor gedetailleerde stappen.

### <a name="use-azure-sql-analytics-for-monitoring-and-alerting"></a>Azure SQL Analytics gebruiken voor bewaking en waarschuwingen

U SQL Analytics gebruiken als een hiërarchisch dashboard om uw SQL-databasebronnen weer te geven.

- Zie SQL Database controleren met [SQL Analytics](../log-analytics/log-analytics-azure-sql.md)voor meer informatie over het gebruik van Azure SQL Analytics.
- Zie [Waarschuwingen maken voor database, elastische pools en beheerde instanties](../azure-monitor/insights/azure-sql.md#analyze-data-and-create-alerts)voor meer informatie over het instellen van waarschuwingen voor SQL Analytics.

## <a name="stream-into-event-hubs"></a>Streamen in Event Hubs

U SQL Database-statistieken en bronlogboeken streamen naar gebeurtenishubs met behulp van de ingebouwde **stream naar een gebeurtenishuboptie** in de Azure-portal. U de regel-ID servicebus ook inschakelen met behulp van diagnostische instellingen via PowerShell-cmdlets, de Azure CLI of de Azure Monitor REST API.

### <a name="what-to-do-with-metrics-and-resource-logs-in-event-hubs"></a>Wat te doen met statistieken en bronlogboeken in gebeurtenishubs

Nadat de geselecteerde gegevens zijn gestreamd naar gebeurtenishubs, bent u een stap dichter bij geavanceerde bewakingsscenario's. Event Hubs fungeert als de voordeur voor een gebeurtenispijplijn. Nadat gegevens zijn verzameld in een gebeurtenishub, kan deze worden getransformeerd en opgeslagen met behulp van een realtime analyseprovider of een opslagadapter. Event Hubs ontkoppelt de productie van een stroom van evenementen van het verbruik van deze evenementen. Op deze manier hebben eventconsumenten toegang tot de evenementen op hun eigen schema. Zie voor meer informatie over eventhubs:

- [Wat zijn Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Aan de slag met Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Je gestreamde statistieken in gebeurtenishubs gebruiken om:

- **De status van de service weergeven door hot-path-gegevens naar Power BI te streamen**

  Door Event Hubs, Stream Analytics en Power BI te gebruiken, u uw statistieken en diagnostische gegevens eenvoudig omzetten in bijna realtime inzichten over uw Azure-services. Zie [Stream Analytics en Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)voor een overzicht van het instellen van een gebeurtenishub, het verwerken van gegevens met Stream Analytics en het gebruik van Power BI als uitvoer.

- **Logboeken streamen naar logboekregistratie- en telemetriestreams van derden**

  Door het streamen van Event Hubs te gebruiken, u uw statistieken en bronlogboeken in verschillende oplossingen voor monitoring en logboekanalyse van derden plaatsen.

- **Een aangepast telemetrie- en logboekplatform bouwen**

  Heeft u al een op maat gemaakt telemetrieplatform of overweegt u er een te bouwen? Het zeer schaalbare publicatie-abonneerkarakter van Event Hubs stelt u in staat om flexibel metrics en resource logs in te nemen. Bekijk [dan Rosanova's gids voor het gebruik van Event Hubs in een telemetrieplatform op wereldwijde schaal.](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)

## <a name="stream-into-azure-storage"></a>Streamen naar Azure Storage

U metrische gegevens en bronlogboeken opslaan in Azure Storage met behulp van het ingebouwde **archief naar een opslagaccountoptie** in de Azure-portal. U opslag ook inschakelen door diagnostische instellingen te gebruiken via PowerShell-cmdlets, de Azure CLI of de Azure Monitor REST API.

### <a name="schema-of-metrics-and-resource-logs-in-the-storage-account"></a>Schema van statistieken en bronlogboeken in het opslagaccount

Nadat u statistieken en verzameling van bronlogboeken hebt ingesteld, wordt een opslagcontainer gemaakt in het opslagaccount dat u hebt geselecteerd wanneer de eerste rijen gegevens beschikbaar zijn. De structuur van de blobs is:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Of, eenvoudiger:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Een blobnaam voor basisstatistieken kan bijvoorbeeld zijn:

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Een blobnaam voor het opslaan van gegevens uit een elastische groep ziet eruit als:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

## <a name="data-retention-policy-and-pricing"></a>Beleid voor gegevensbewaring en -prijzen

Als u Gebeurtenishubs of een opslagaccount selecteert, u een bewaarbeleid opgeven. Met dit beleid worden gegevens verwijderd die ouder zijn dan een geselecteerde periode. Als u Log Analytics opgeeft, is het bewaarbeleid afhankelijk van de geselecteerde prijscategorie. In dit geval kunnen de geleverde gratis eenheden van gegevensopname elke maand gratis bewaking van verschillende databases mogelijk maken. Elk verbruik van diagnostische telemetrie boven de vrije eenheden kan kosten met zich meebrengen.

> [!IMPORTANT]
> Actieve databases met zwaardere workloads nemen meer gegevens in dan inactieve databases. Zie [Prijzen voor logboekanalyses](https://azure.microsoft.com/pricing/details/monitor/)voor meer informatie.

Als u Azure SQL Analytics gebruikt, u het verbruik van gegevensinname controleren door **OMS Workspace** te selecteren in het navigatiemenu van Azure SQL Analytics en vervolgens **Gebruiks-** en **geschatte kosten te**selecteren.

## <a name="metrics-and-logs-available"></a>Beschikbare statistieken en logboeken

Het bewaken van telemetrie beschikbaar voor afzonderlijke databases, gepoolde databases, elastische pools, beheerde instantie en instantie databases is gedocumenteerd in dit gedeelte van het artikel. Verzamelde bewaking telemetrie in SQL Analytics kan worden gebruikt voor uw eigen aangepaste analyse en toepassingsontwikkeling met behulp van [Azure Monitor-logboekquery'staal.](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries)

### <a name="basic-metrics"></a>Basisstatistieken

Raadpleeg de volgende tabellen voor meer informatie over basisstatistieken per resource.

> [!NOTE]
> Basic metrics optie was voorheen bekend als Alle metrische gegevens. De aangebrachte wijziging was alleen in de naamgeving en er was geen wijziging in de metrische gegevens gecontroleerd. Deze wijziging is geïnitieerd om de invoering van aanvullende metrische categorieën in de toekomst mogelijk te maken.

#### <a name="basic-metrics-for-elastic-pools"></a>Basisstatistieken voor elastische pools

|**Resource**|**Statistieken**|
|---|---|
|Elastische pool|eDTU-percentage, eDTU-limiet, eDTU-limiet, CPU-percentage, leespercentage voor fysieke gegevens, percentage logboekschrijfpunten, sessiespercentage, werknemerspercentage, opslag, opslagpercentage, opslaglimiet, XTP-opslagpercentage |

#### <a name="basic-metrics-for-single-and-pooled-databases"></a>Basisstatistieken voor afzonderlijke en samengevoegde databases

|**Resource**|**Statistieken**|
|---|---|
|Eenenpooldatabase|DTU-percentage, DTU-limiet, DTU-limiet, CPU-percentage, fysiek leespercentage voor gegevens, schrijfpercentage van logboeken, Geslaagd/mislukt/geblokkeerd door firewallverbindingen, sessiespercentage, werknemerspercentage, opslag, opslagpercentage, XTP-opslagpercentage en impasses |

### <a name="advanced-metrics"></a>Geavanceerde statistieken

Raadpleeg de volgende tabel voor meer informatie over geavanceerde statistieken.

|**Gegevens**|**Metrische weergavenaam**|**Beschrijving**|
|---|---|---|
|tempdb_data_size| Tempdb-gegevensbestandsgrootte Kilobytes |Tempdb Data File Size Kilobytes. Niet van toepassing op datawarehouses. Deze statistiek zal beschikbaar zijn voor databases met behulp van de vCore inkoopmodel met 2 vCores en hoger, of 200 DTU en hoger voor DTU-gebaseerde inkoopmodellen. Deze statistiek is momenteel niet beschikbaar voor Hyperscale-databases.|
|tempdb_log_size| Tempdb Log File Size Kilobytes |Tempdb Log File Size Kilobytes. Niet van toepassing op datawarehouses. Deze statistiek zal beschikbaar zijn voor databases met behulp van de vCore inkoopmodel met 2 vCores en hoger, of 200 DTU en hoger voor DTU-gebaseerde inkoopmodellen. Deze statistiek is momenteel niet beschikbaar voor Hyperscale-databases.|
|tempdb_log_used_percent| Tempdb Procent Log gebruikt |Tempdb Procent Log gebruikt. Niet van toepassing op datawarehouses. Deze statistiek zal beschikbaar zijn voor databases met behulp van de vCore inkoopmodel met 2 vCores en hoger, of 200 DTU en hoger voor DTU-gebaseerde inkoopmodellen. Deze statistiek is momenteel niet beschikbaar voor Hyperscale-databases.|

### <a name="basic-logs"></a>Basislogboeken

Details van telemetrie beschikbaar voor alle logboeken zijn gedocumenteerd in de volgende tabellen. Zie [ondersteunde diagnostische telemetrie](#diagnostic-telemetry-for-export-for-azure-sql-database) om te begrijpen welke logboeken worden ondersteund voor een bepaalde databasesmaak - Azure SQL-single-, pooled- of instance-database.

#### <a name="resource-usage-stats-for-managed-instances"></a>Statistieken voor resourcegebruik voor beheerde instanties

|Eigenschap|Beschrijving|
|---|---|
|TenantId|Uw tenant-id |
|SourceSystem|Altijd: Azure|
|TimeGenerated [UTC]|Tijdstempel toen het logboek werd geregistreerd |
|Type|Altijd: AzureDiagnostics |
|ResourceProvider|Naam van de resourceprovider. Altijd: MICROSOFT. Sql |
|Categorie|Naam van de categorie. Altijd: ResourceUsageStats |
|Resource|Naam van de resource |
|ResourceType|Naam van het resourcetype. Altijd: MANAGEDINSTANCES |
|SubscriptionId|GuiD voor abonnement voor de database |
|ResourceGroup|Naam van de resourcegroep voor de database |
|LogicalServerName_s|Naam van de beheerde instantie |
|ResourceId|Resource URI |
|SKU_s|Beheerde instantie product SKU |
|virtual_core_count_s|Aantal beschikbare vCores |
|avg_cpu_percent_s|Gemiddeld CPU-percentage |
|reserved_storage_mb_s|Gereserveerde opslagcapaciteit op de beheerde instantie |
|storage_space_used_mb_s|Gebruikte opslag op de beheerde instantie |
|io_requests_s|IOPS tellen |
|io_bytes_read_s|Gelezen IOPS-bytes |
|io_bytes_written_s|IOPS-bytes geschreven |

#### <a name="query-store-runtime-statistics"></a>Runtime-statistieken voor queryopslag

|Eigenschap|Beschrijving|
|---|---|
|TenantId|Uw tenant-id |
|SourceSystem|Altijd: Azure |
|TimeGenerated [UTC]|Tijdstempel toen het logboek werd geregistreerd |
|Type|Altijd: AzureDiagnostics |
|ResourceProvider|Naam van de resourceprovider. Altijd: MICROSOFT. Sql |
|Categorie|Naam van de categorie. Altijd: QueryStoreRuntimeStatistics |
|OperationName|Naam van de bewerking. Altijd: QueryStoreRuntimeStatisticsEvent |
|Resource|Naam van de resource |
|ResourceType|Naam van het resourcetype. Altijd: SERVERS/DATABASES |
|SubscriptionId|GuiD voor abonnement voor de database |
|ResourceGroup|Naam van de resourcegroep voor de database |
|LogicalServerName_s|Naam van de server voor de database |
|ElasticPoolName_s|Naam van de elastische pool voor de database, indien aanwezig |
|DatabaseName_s|Naam van de database |
|ResourceId|Resource URI |
|query_hash_s|Queryhash |
|query_plan_hash_s|Hash van queryplan |
|statement_sql_handle_s|Sql-handle van instructie |
|interval_start_time_d|Begindatumverschuiving van het interval in aantal teken van 1900-1-1 |
|interval_end_time_d|Einddatum van het interval in aantal teken uit 1900-1-1 |
|logical_io_writes_d|Totaal aantal logische IO-schrijfbewerkingen |
|max_logical_io_writes_d|Maximaal aantal logische IO-schrijfbewerkingen per uitvoering |
|physical_io_reads_d|Totaal aantal fysieke IO-reads |
|max_physical_io_reads_d|Maximaal aantal logische IO-reads per uitvoering |
|logical_io_reads_d|Totaal aantal logische IO-reads |
|max_logical_io_reads_d|Maximaal aantal logische IO-reads per uitvoering |
|execution_type_d|Uitvoeringstype |
|count_executions_d|Aantal uitvoeringen van de query |
|cpu_time_d|Totale CPU-tijd die in microseconden door de query wordt verbruikt |
|max_cpu_time_d|Max CPU tijd consument door een enkele uitvoering in microseconden |
|dop_d|Som van de mate van parallellisme |
|max_dop_d|Maximale mate van parallellisme die wordt gebruikt voor éénuitvoering |
|rowcount_d|Totaal aantal geretourneerde rijen |
|max_rowcount_d|Maximum aantal rijen dat is geretourneerd in één uitvoering |
|query_max_used_memory_d|Totale hoeveelheid geheugen die wordt gebruikt in KB |
|max_query_max_used_memory_d|Maximale hoeveelheid geheugen die wordt gebruikt door één uitvoering in KB |
|duration_d|Totale uitvoeringstijd in microseconden |
|max_duration_d|Maximale uitvoeringstijd van één uitvoering |
|num_physical_io_reads_d|Totaal aantal fysieke reads |
|max_num_physical_io_reads_d|Maximum aantal fysieke reads per uitvoering |
|log_bytes_used_d|Totaal aantal gebruikte logboekbytes |
|max_log_bytes_used_d|Maximumaantal logboekbytes dat per uitvoering wordt gebruikt |
|query_id_d|ID van de query in queryarchief |
|plan_id_d|ID van het abonnement in queryarchief |

Meer informatie over gegevens over [runtime-statistieken in queryarchief](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql).

#### <a name="query-store-wait-statistics"></a>Wachtstatistieken van queryopslag

|Eigenschap|Beschrijving|
|---|---|
|TenantId|Uw tenant-id |
|SourceSystem|Altijd: Azure |
|TimeGenerated [UTC]|Tijdstempel toen het logboek werd geregistreerd |
|Type|Altijd: AzureDiagnostics |
|ResourceProvider|Naam van de resourceprovider. Altijd: MICROSOFT. Sql |
|Categorie|Naam van de categorie. Altijd: QueryStoreWaitStatistieken |
|OperationName|Naam van de bewerking. Altijd: QueryStoreWaitStatisticsEvent |
|Resource|Naam van de resource |
|ResourceType|Naam van het resourcetype. Altijd: SERVERS/DATABASES |
|SubscriptionId|GuiD voor abonnement voor de database |
|ResourceGroup|Naam van de resourcegroep voor de database |
|LogicalServerName_s|Naam van de server voor de database |
|ElasticPoolName_s|Naam van de elastische pool voor de database, indien aanwezig |
|DatabaseName_s|Naam van de database |
|ResourceId|Resource URI |
|wait_category_s|Categorie van het wachten |
|is_parameterizable_s|Is de query parameterizable |
|statement_type_s|Type van de instructie |
|statement_key_hash_s|Hash van de instructiesleutel |
|exec_type_d|Type uitvoering |
|total_query_wait_time_ms_d|Totale wachttijd van de query in de specifieke wachtcategorie |
|max_query_wait_time_ms_d|Maximale wachttijd van de query in afzonderlijke uitvoering op de specifieke wachtcategorie |
|query_param_type_d|0 |
|query_hash_s|Queryhash in queryarchief |
|query_plan_hash_s|Hash van queryplanin queryarchief |
|statement_sql_handle_s|Instructiegreep in queryarchief |
|interval_start_time_d|Begindatumverschuiving van het interval in aantal teken van 1900-1-1 |
|interval_end_time_d|Einddatum van het interval in aantal teken uit 1900-1-1 |
|count_executions_d|Aantal uitvoeringen van de query |
|query_id_d|ID van de query in queryarchief |
|plan_id_d|ID van het abonnement in queryarchief |

Meer informatie over [de gegevens van wachtstatistieken van queryarchief](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql).

#### <a name="errors-dataset"></a>Gegevensset Fouten

|Eigenschap|Beschrijving|
|---|---|
|TenantId|Uw tenant-id |
|SourceSystem|Altijd: Azure |
|TimeGenerated [UTC]|Tijdstempel toen het logboek werd geregistreerd |
|Type|Altijd: AzureDiagnostics |
|ResourceProvider|Naam van de resourceprovider. Altijd: MICROSOFT. Sql |
|Categorie|Naam van de categorie. Altijd: Fouten |
|OperationName|Naam van de bewerking. Altijd: ErrorEvent |
|Resource|Naam van de resource |
|ResourceType|Naam van het resourcetype. Altijd: SERVERS/DATABASES |
|SubscriptionId|GuiD voor abonnement voor de database |
|ResourceGroup|Naam van de resourcegroep voor de database |
|LogicalServerName_s|Naam van de server voor de database |
|ElasticPoolName_s|Naam van de elastische pool voor de database, indien aanwezig |
|DatabaseName_s|Naam van de database |
|ResourceId|Resource URI |
|Bericht|Foutbericht in platte tekst |
|user_defined_b|Is de foutgebruiker gedefinieerd bit |
|error_number_d|Foutcode |
|Severity|Ernst van de fout |
|state_d|Staat van de fout |
|query_hash_s|Queryhash van de mislukte query, indien beschikbaar |
|query_plan_hash_s|Queryplanhash van de mislukte query, indien beschikbaar |

Meer informatie over [SQL Server-foutberichten](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors?view=sql-server-ver15).

#### <a name="database-wait-statistics-dataset"></a>Gegevensgegevensgegevens databasewachtstatistieken

|Eigenschap|Beschrijving|
|---|---|
|TenantId|Uw tenant-id |
|SourceSystem|Altijd: Azure |
|TimeGenerated [UTC]|Tijdstempel toen het logboek werd geregistreerd |
|Type|Altijd: AzureDiagnostics |
|ResourceProvider|Naam van de resourceprovider. Altijd: MICROSOFT. Sql |
|Categorie|Naam van de categorie. Altijd: DatabaseWaitStatistics |
|OperationName|Naam van de bewerking. Altijd: DatabaseWaitStatisticsEvent |
|Resource|Naam van de resource |
|ResourceType|Naam van het resourcetype. Altijd: SERVERS/DATABASES |
|SubscriptionId|GuiD voor abonnement voor de database |
|ResourceGroup|Naam van de resourcegroep voor de database |
|LogicalServerName_s|Naam van de server voor de database |
|ElasticPoolName_s|Naam van de elastische pool voor de database, indien aanwezig |
|DatabaseName_s|Naam van de database |
|ResourceId|Resource URI |
|wait_type_s|Naam van het wachttype |
|start_utc_date_t [UTC]|Gemeten beginperiode |
|end_utc_date_t [UTC]|Gemeten eindtijd van de periode |
|delta_max_wait_time_ms_d|Max wachtte tijd per uitvoering |
|delta_signal_wait_time_ms_d|Totale signalen wachttijd |
|delta_wait_time_ms_d|Totale wachttijd in de periode |
|delta_waiting_tasks_count_d|Aantal wachttaken |

Meer informatie over [databasewachtstatistieken](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql).

#### <a name="time-outs-dataset"></a>Gegevensset Time-outs

|Eigenschap|Beschrijving|
|---|---|
|TenantId|Uw tenant-id |
|SourceSystem|Altijd: Azure |
|TimeGenerated [UTC]|Tijdstempel toen het logboek werd geregistreerd |
|Type|Altijd: AzureDiagnostics |
|ResourceProvider|Naam van de resourceprovider. Altijd: MICROSOFT. Sql |
|Categorie|Naam van de categorie. Altijd: Time-outs |
|OperationName|Naam van de bewerking. Altijd: Time-outEvent |
|Resource|Naam van de resource |
|ResourceType|Naam van het resourcetype. Altijd: SERVERS/DATABASES |
|SubscriptionId|GuiD voor abonnement voor de database |
|ResourceGroup|Naam van de resourcegroep voor de database |
|LogicalServerName_s|Naam van de server voor de database |
|ElasticPoolName_s|Naam van de elastische pool voor de database, indien aanwezig |
|DatabaseName_s|Naam van de database |
|ResourceId|Resource URI |
|error_state_d|Foutstatuscode |
|query_hash_s|Queryhash, indien beschikbaar |
|query_plan_hash_s|Hash van queryplan, indien beschikbaar |

#### <a name="blockings-dataset"></a>Gegevensset Blokkeringen

|Eigenschap|Beschrijving|
|---|---|
|TenantId|Uw tenant-id |
|SourceSystem|Altijd: Azure |
|TimeGenerated [UTC]|Tijdstempel toen het logboek werd geregistreerd |
|Type|Altijd: AzureDiagnostics |
|ResourceProvider|Naam van de resourceprovider. Altijd: MICROSOFT. Sql |
|Categorie|Naam van de categorie. Altijd: Blokken |
|OperationName|Naam van de bewerking. Altijd: BlockEvent |
|Resource|Naam van de resource |
|ResourceType|Naam van het resourcetype. Altijd: SERVERS/DATABASES |
|SubscriptionId|GuiD voor abonnement voor de database |
|ResourceGroup|Naam van de resourcegroep voor de database |
|LogicalServerName_s|Naam van de server voor de database |
|ElasticPoolName_s|Naam van de elastische pool voor de database, indien aanwezig |
|DatabaseName_s|Naam van de database |
|ResourceId|Resource URI |
|lock_mode_s|Vergrendelingsmodus die door de query wordt gebruikt |
|resource_owner_type_s|Eigenaar van het slot |
|blocked_process_filtered_s|XML voor geblokkeerd procesrapport |
|duration_d|Duur van het slot in microseconden |

#### <a name="deadlocks-dataset"></a>Gegevensset Impasses

|Eigenschap|Beschrijving|
|---|---|
|TenantId|Uw tenant-id |
|SourceSystem|Altijd: Azure |
|TimeGenerated [UTC] |Tijdstempel toen het logboek werd geregistreerd |
|Type|Altijd: AzureDiagnostics |
|ResourceProvider|Naam van de resourceprovider. Altijd: MICROSOFT. Sql |
|Categorie|Naam van de categorie. Altijd: Impasses |
|OperationName|Naam van de bewerking. Altijd: DeadlockEvent |
|Resource|Naam van de resource |
|ResourceType|Naam van het resourcetype. Altijd: SERVERS/DATABASES |
|SubscriptionId|GuiD voor abonnement voor de database |
|ResourceGroup|Naam van de resourcegroep voor de database |
|LogicalServerName_s|Naam van de server voor de database |
|ElasticPoolName_s|Naam van de elastische pool voor de database, indien aanwezig |
|DatabaseName_s|Naam van de database |
|ResourceId|Resource URI |
|deadlock_xml_s|XML-impasserapport |

#### <a name="automatic-tuning-dataset"></a>Automatische tuninggegevensset

|Eigenschap|Beschrijving|
|---|---|
|TenantId|Uw tenant-id |
|SourceSystem|Altijd: Azure |
|TimeGenerated [UTC]|Tijdstempel toen het logboek werd geregistreerd |
|Type|Altijd: AzureDiagnostics |
|ResourceProvider|Naam van de resourceprovider. Altijd: MICROSOFT. Sql |
|Categorie|Naam van de categorie. Altijd: Automatisch afstemmen |
|Resource|Naam van de resource |
|ResourceType|Naam van het resourcetype. Altijd: SERVERS/DATABASES |
|SubscriptionId|GuiD voor abonnement voor de database |
|ResourceGroup|Naam van de resourcegroep voor de database |
|LogicalServerName_s|Naam van de server voor de database |
|LogicalDatabaseName_s|Naam van de database |
|ElasticPoolName_s|Naam van de elastische pool voor de database, indien aanwezig |
|DatabaseName_s|Naam van de database |
|ResourceId|Resource URI |
|RecommendationHash_s|Unieke hash van automatische tuning aanbeveling |
|OptionName_s|Automatische afstemmingsbewerking |
|Schema_s|Databaseschema |
|Table_s|Tabel beïnvloed |
|IndexName_s|Indexnaam |
|IndexColumns_s|Kolomnaam |
|IncludedColumns_s|Kolommen inbegrepen |
|EstimatedImpact_s|Geschatte impact van automatische tuning aanbeveling JSON |
|Event_s|Type automatische tuninggebeurtenis |
|Timestamp_t|Laatst bijgewerkte tijdstempel |

#### <a name="intelligent-insights-dataset"></a>Intelligent Insights-gegevensset

Meer informatie over de [logboekindeling Intelligent Insights](sql-database-intelligent-insights-use-diagnostics-log.md).

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over het inschakelen van logboekregistratie en het begrijpen van de statistieken en logboekcategorieën die worden ondersteund door de verschillende Azure-services:

- [Overzicht van statistieken in Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
- [Overzicht van Azure-platformlogboeken](../azure-monitor/platform/platform-logs-overview.md)

Lees voor meer informatie over gebeurtenishubs:

- [Wat is Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Aan de slag met Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Zie voor meer informatie over het instellen van waarschuwingen op basis van telemetrie van logboekanalyses:

- [Waarschuwingen maken voor SQL Database en beheerde instantie](../azure-monitor/insights/azure-sql.md#analyze-data-and-create-alerts)
