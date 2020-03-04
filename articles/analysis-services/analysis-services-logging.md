---
title: Diagnostische logboek registratie voor Azure Analysis Services | Microsoft Docs
description: Hierin wordt beschreven hoe u Azure resource Diagnostic-logboek registratie instelt om uw Azure Analysis Services-server te bewaken.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 0f13f297facedceb50920c0f6afca63fe1df0b48
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2020
ms.locfileid: "78248053"
---
# <a name="setup-diagnostic-logging"></a>Registratie in diagnoselogboek instellen

Een belangrijk onderdeel van een Analysis Services-oplossing wordt bewaakt door het uitvoeren van uw servers. Met [Azure-resource logboeken](../azure-monitor/platform/platform-logs-overview.md)kunt u logboeken controleren en verzenden naar [Azure Storage](https://azure.microsoft.com/services/storage/), ze streamen naar [Azure Event hubs](https://azure.microsoft.com/services/event-hubs/)en exporteren naar [Azure monitor logboeken](../azure-monitor/azure-monitor-log-hub.md).

![Diagnostische logboek registratie voor opslag, Event Hubs of Azure Monitor-logboeken](./media/analysis-services-logging/aas-logging-overview.png)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="whats-logged"></a>Wat aangemeld?

U kunt categorieën voor **engine**, **service**en **metrische gegevens** selecteren.

### <a name="engine"></a>Engine

Selecteren **engine** Logboeken alle [xEvents](https://docs.microsoft.com/analysis-services/instances/monitor-analysis-services-with-sql-server-extended-events). U kunt geen afzonderlijke gebeurtenissen selecteren. 

|XEvent-categorieën |Gebeurtenisnaam  |
|---------|---------|
|Beveiligingscontrole    |   Audit-aanmelding      |
|Beveiligingscontrole    |   Afmelden controleren      |
|Beveiligingscontrole    |   Audit-Server wordt gestart en gestopt      |
|Van voortgangsrapporten     |   Voortgang rapport Begin      |
|Van voortgangsrapporten     |   Einde van het voortgangsrapport      |
|Van voortgangsrapporten     |   Voortgang rapport huidige      |
|Query's     |  Begin van de query       |
|Query's     |   Einde van de query      |
|Opdrachten     |  Begin van de opdracht       |
|Opdrachten     |  Einde van de opdracht       |
|Fouten en waarschuwingen     |   Fout      |
|Ontdekken     |   Einde detecteren      |
|Melding     |    Melding     |
|Sessie     |  Sessie initialiseren       |
|Vergrendelingen    |  Impasse       |
|Verwerking van query 's     |   VertiPaq SE-Query Begin      |
|Verwerking van query 's     |   Einde van VertiPaq SE-Query      |
|Verwerking van query 's     |   VertiPaq SE-Query Cache overeenkomst      |
|Verwerking van query 's     |   Directe Query Begin      |
|Verwerking van query 's     |  Directe Query End       |

### <a name="service"></a>Service

|Naam van bewerking  |Treedt op wanneer  |
|---------|---------|
|ResumeServer     |    Hervatten van een server     |
|SuspendServer    |   Onderbreken van een server      |
|DeleteServer     |    Een server verwijderen     |
|RestartServer    |     Gebruiker opnieuw wordt opgestart een server via SSMS of PowerShell    |
|GetServerLogFiles    |    Gebruiker exporteert serverlogboek via PowerShell     |
|ExportModel     |   Gebruiker Hiermee exporteert u een model in de portal met behulp van Open in Visual Studio     |

### <a name="all-metrics"></a>Alle metrische gegevens

De categorie metrische gegevens registreert dezelfde [Server metrieken](analysis-services-monitor.md#server-metrics) in de tabel AzureMetrics. Als u query [scale-out](analysis-services-scale-out.md) gebruikt en metrische gegevens voor elke Lees replica moet scheiden, gebruikt u de AzureDiagnostics-tabel in plaats daarvan, waarbij **operationname** gelijk is aan **LogMetric**.

## <a name="setup-diagnostics-logging"></a>Configuratie diagnostische logboekregistratie

### <a name="azure-portal"></a>Azure-portal

1. Klik in [Azure Portal](https://portal.azure.com) > server op **Diagnostische logboeken** in het linkernavigatievenster en klik vervolgens op **Diagnostische gegevens inschakelen**.

    ![Schakel registratie in diagnoselogboek voor Azure Cosmos DB in Azure portal](./media/analysis-services-logging/aas-logging-turn-on-diagnostics.png)

2. Geef in **Diagnostische instellingen**de volgende opties op: 

    * **Naam**. Voer een naam voor de logboeken om te maken.

    * **Archiveren naar een opslag account**. Als u wilt deze optie gebruikt, moet u een bestaand opslagaccount verbinden. Zie [een opslag account maken](../storage/common/storage-create-storage-account.md). Volg de instructies voor het maken van een Resource Manager, account voor algemeen gebruik, en selecteer vervolgens uw storage-account door te retourneren aan deze pagina in de portal. Het duurt een paar minuten voor de zojuist gemaakte storage-accounts worden weergegeven in de vervolgkeuzelijst.
    * **Streamen naar een event hub**. Als u wilt deze optie gebruikt, moet u een bestaande Event Hub-naamruimte en event hub te verbinden. Zie [een event hubs naam ruimte maken en een event hub met behulp van de Azure Portal](../event-hubs/event-hubs-create.md)voor meer informatie. Ga vervolgens terug naar deze pagina in de portal voor het selecteren van de naam van de Event Hub-naamruimte en het beleid.
    * **Send to Azure monitor (log Analytics-werk ruimte)** . Als u deze optie wilt gebruiken, moet u een bestaande werk ruimte gebruiken of [een nieuwe werkruimte resource maken](../azure-monitor/learn/quick-create-workspace.md) in de portal. Zie [Logboeken weer geven in log Analytics werk ruimte](#view-logs-in-log-analytics-workspace) in dit artikel voor meer informatie over het weer geven van uw logboeken.

    * **Engine**. Selecteer deze optie om aan te melden xEvents. Als u naar een opslagaccount archiveren bent, kunt u de bewaarperiode voor de diagnostische logboeken. Logboeken zijn autodeleted nadat de bewaarperiode is verlopen.
    * **Service**. Selecteer deze optie om aan te melden op gebeurtenissen van de service. Als u naar een opslagaccount archiveren wordt, kunt u de bewaarperiode voor de diagnostische logboeken. Logboeken zijn autodeleted nadat de bewaarperiode is verlopen.
    * **Metrische gegevens**. Selecteer deze optie om uitgebreide gegevens op te slaan [.](analysis-services-monitor.md#server-metrics) Als u naar een opslagaccount archiveren wordt, kunt u de bewaarperiode voor de diagnostische logboeken. Logboeken zijn autodeleted nadat de bewaarperiode is verlopen.

3. Klik op **Opslaan**.

    Als er een fout bericht wordt weer gegeven met de melding dat de diagnostische gegevens voor \<werkruimte naam niet kunnen worden bijgewerkt >. De abonnements-id van het abonnement \<> niet is geregistreerd voor gebruik van micro soft. Insights. Volg de instructies voor het [oplossen van problemen Azure Diagnostics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) om het account te registreren en voer deze procedure opnieuw uit.

    Als u wijzigen hoe uw logboeken met diagnostische gegevens in de toekomst op elk gewenst moment worden opgeslagen wilt, kunt u terugkeren naar deze pagina om instellingen te wijzigen.

### <a name="powershell"></a>PowerShell

Hier volgen de basisopdrachten voor u aan de slag. Als u wilt dat Stapsgewijze instructies over het instellen van logboekregistratie naar een opslagaccount met behulp van PowerShell, raadpleegt u de zelfstudie verderop in dit artikel.

Om in te schakelen metrische en diagnostische gegevens logboekregistratie met behulp van PowerShell, gebruikt u de volgende opdrachten:

- Om in te schakelen opslag van diagnostische logboeken in een opslagaccount, gebruikt u deze opdracht:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   Het opslagaccount-ID is de resource-ID voor het opslagaccount waar u om de logboeken te verzenden.

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

### <a name="rest-api"></a>REST-API

Meer informatie over [het wijzigen van diagnostische instellingen met behulp van de Azure Monitor rest API](https://docs.microsoft.com/rest/api/monitor/). 

### <a name="resource-manager-template"></a>Resource Manager-sjabloon

Meer informatie over het [inschakelen van diagnostische instellingen bij het maken van resources met behulp van een resource manager-sjabloon](../azure-monitor/platform/diagnostic-settings-template.md). 

## <a name="manage-your-logs"></a>Uw logboeken beheren

Logboeken zijn doorgaans beschikbaar binnen een paar uur na het instellen van logboekregistratie. Het is aan u om uw logboeken in uw opslagaccount te beheren:

* Gebruik standaardmethoden van Azure voor toegangsbeheer om uw logboeken te beveiligen door het aantal gebruikers te beperken dat toegang heeft tot de logboeken.
* Verwijder de logboeken die u niet meer in uw opslagaccount wilt bewaren.
* Zorg ervoor dat een bewaarperiode voor instellen, zodat de oude logboeken worden verwijderd uit uw storage-account.

## <a name="view-logs-in-log-analytics-workspace"></a>Logboeken in Log Analytics werk ruimte weer geven

Metrische gegevens en server gebeurtenissen zijn geïntegreerd met xEvents in uw Log Analytics werkruimte resource voor de analyse van de side-by-side. Log Analytics-werk ruimte kan ook worden geconfigureerd voor het ontvangen van gebeurtenissen van andere Azure-Services met een holistische weer gave van gegevens van diagnostische logboek registratie in uw architectuur.

Als u uw diagnostische gegevens wilt weer geven, opent u in Log Analytics werk ruimte **Logboeken** vanuit het linkermenu.

![Opties voor het doorzoeken van logboekbestanden in de Azure-portal](./media/analysis-services-logging/aas-logging-open-log-search.png)

Vouw in de opbouw functie voor query's **LogManagement** > **AzureDiagnostics**uit. AzureDiagnostics bevat-Engine en servicegebeurtenissen. U ziet dat er aan de vlucht een query wordt gemaakt. Het veld EventClass\_s bevat xEvent-namen. Dit kan er als volgt uitzien als u xEvents hebt gebruikt voor on-premises logboek registratie. Klik op **EventClass\_s** of een van de gebeurtenis namen en log Analytics werk ruimte blijft een query maken. Zorg ervoor dat u uw query's voor hergebruik.

### <a name="example-queries"></a>Voorbeelden van query's

#### <a name="example-1"></a>Voorbeeld 1

Met de volgende query wordt de duur van elke query end/Refresh end gebeurtenis voor een model database en-server geretourneerd. Als de uitbreiden worden uitgeschaald, worden de resultaten uitgesplitst op replica omdat het replica nummer is opgenomen in ServerName_s. Groeperen op RootActivityId_g vermindert het aantal rijen dat is opgehaald uit de Azure Diagnostics REST API en helpt de grenzen te blijven zien, zoals beschreven in [log Analytics frequentie limieten](https://dev.loganalytics.io/documentation/Using-the-API/Limits).

```Kusto
let window = AzureDiagnostics
   | where ResourceProvider == "MICROSOFT.ANALYSISSERVICES" and Resource =~ "MyServerName" and DatabaseName_s =~ "MyDatabaseName" ;
window
| where OperationName has "QueryEnd" or (OperationName has "CommandEnd" and EventSubclass_s == 38)
| where extract(@"([^,]*)", 1,Duration_s, typeof(long)) > 0
| extend DurationMs=extract(@"([^,]*)", 1,Duration_s, typeof(long))
| project  StartTime_t,EndTime_t,ServerName_s,OperationName,RootActivityId_g,TextData_s,DatabaseName_s,ApplicationName_s,Duration_s,EffectiveUsername_s,User_s,EventSubclass_s,DurationMs
| order by StartTime_t asc
```

#### <a name="example-2"></a>Voorbeeld 2

Met de volgende query wordt het geheugen-en QPU verbruik voor een server geretourneerd. Als de uitbreiden worden uitgeschaald, worden de resultaten uitgesplitst op replica omdat het replica nummer is opgenomen in ServerName_s.

```Kusto
let window = AzureDiagnostics
   | where ResourceProvider == "MICROSOFT.ANALYSISSERVICES" and Resource =~ "MyServerName";
window
| where OperationName == "LogMetric" 
| where name_s == "memory_metric" or name_s == "qpu_metric"
| project ServerName_s, TimeGenerated, name_s, value_s
| summarize avg(todecimal(value_s)) by ServerName_s, name_s, bin(TimeGenerated, 1m)
| order by TimeGenerated asc 
```

#### <a name="example-3"></a>Voorbeeld 3

Met de volgende query worden de Analysis Services engine-prestatie meter items van de rijen voor een server gelezen per seconde.

```Kusto
let window =  AzureDiagnostics
   | where ResourceProvider == "MICROSOFT.ANALYSISSERVICES" and Resource =~ "MyServerName";
window
| where OperationName == "LogMetric" 
| where parse_json(tostring(parse_json(perfobject_s).counters))[0].name == "Rows read/sec" 
| extend Value = tostring(parse_json(tostring(parse_json(perfobject_s).counters))[0].value) 
| project ServerName_s, TimeGenerated, Value
| summarize avg(todecimal(Value)) by ServerName_s, bin(TimeGenerated, 1m)
| order by TimeGenerated asc 
```

Er zijn honderden query's die u kunt gebruiken. Zie aan de [slag met Azure monitor-logboek query's](../azure-monitor/log-query/get-started-queries.md)voor meer informatie over query's.


## <a name="turn-on-logging-by-using-powershell"></a>Logboekregistratie inschakelen met behulp van PowerShell

In deze korte zelfstudie maakt u een opslagaccount in hetzelfde abonnement en dezelfde resourcegroep als uw Analysis Services-server. Vervolgens gebruikt u set-AzDiagnosticSetting om diagnostische logboek registratie in te scha kelen en de uitvoer naar het nieuwe opslag account te verzenden.

### <a name="prerequisites"></a>Vereisten
Voor deze zelfstudie hebt u de volgende bronnen:

* Een bestaande Azure Analysis Services-server. Zie [een server maken in azure Portal](analysis-services-create-server.md)of [een Azure Analysis Services-server maken met behulp van Power shell](analysis-services-create-powershell.md)voor instructies voor het maken van een server bron.

### <a name="aconnect-to-your-subscriptions"></a></a>verbinding maken met uw abonnementen

Start een Azure PowerShell-sessie en meld u aan bij uw Azure-account met de volgende opdracht:  

```powershell
Connect-AzAccount
```

Voer in het pop-upvenster in de browser uw gebruikersnaam en wachtwoord voor uw Azure-account in. Azure PowerShell haalt alle abonnementen op die zijn gekoppeld aan dit account en gebruikt standaard het eerste abonnement.

Als u meerdere abonnementen hebt, moet u wellicht specifiek opgeven welk abonnement is gebruikt voor het maken van uw Azure Sleutelkluis. Typ het volgende als u de abonnementen voor uw account wilt zien:

```powershell
Get-AzSubscription
```

Klik, als u het abonnement dat is gekoppeld aan het Azure Analysis Services-account dat u zich aanmeldt, typt u:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Als u meerdere abonnementen die zijn gekoppeld aan uw account hebt, is het belangrijk om op te geven van het abonnement.
>
>

### <a name="create-a-new-storage-account-for-your-logs"></a>Een nieuw opslagaccount voor uw logboeken maken

U kunt een bestaand opslagaccount gebruiken voor uw Logboeken, indien deze zich in hetzelfde abonnement als uw server. Voor deze zelf studie maakt u een nieuw opslag account dat is toegewezen aan Analysis Services Logboeken. Om het eenvoudig te maken, slaat u de details van het opslag account op in een variabele met de naam **sa**.

U kunt ook dezelfde resourcegroep gebruiken als het account dat met uw Analysis Services-server. Vervang waarden voor `awsales_resgroup`, `awsaleslogs`en `West Central US` met uw eigen waarden:

```powershell
$sa = New-AzStorageAccount -ResourceGroupName awsales_resgroup `
-Name awsaleslogs -Type Standard_LRS -Location 'West Central US'
```

### <a name="identify-the-server-account-for-your-logs"></a>De server-serviceaccount voor uw logboeken identificeren

Stel de account naam in op een variabele met de naam **account**, waarbij ResourceName de naam van het account is.

```powershell
$account = Get-AzResource -ResourceGroupName awsales_resgroup `
-ResourceName awsales -ResourceType "Microsoft.AnalysisServices/servers"
```

### <a name="enable-logging"></a>Logboekregistratie inschakelen

Als u logboek registratie wilt inschakelen, gebruikt u de cmdlet Set-AzDiagnosticSetting in combi natie met de variabelen voor het nieuwe opslag account, het Server account en de categorie. Voer de volgende opdracht uit en stel de vlag **-enabled** in op **$True**:

```powershell
Set-AzDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories Engine
```

De uitvoer zou er ongeveer uit moeten zien zoals in dit voorbeeld:

```powershell
StorageAccountId            : 
/subscriptions/a23279b5-xxxx-xxxx-xxxx-47b7c6d423ea/resourceGroups/awsales_resgroup/providers/Microsoft.Storage/storageAccounts/awsaleslogs
ServiceBusRuleId            :
EventHubAuthorizationRuleId :
Metrics                    
    TimeGrain       : PT1M
    Enabled         : False
    RetentionPolicy
    Enabled : False
    Days    : 0


Logs                       
    Category        : Engine
    Enabled         : True
    RetentionPolicy
    Enabled : False
    Days    : 0


    Category        : Service
    Enabled         : False
    RetentionPolicy
    Enabled : False
    Days    : 0


WorkspaceId                 :
Id                          : /subscriptions/a23279b5-xxxx-xxxx-xxxx-47b7c6d423ea/resourcegroups/awsales_resgroup/providers/microsoft.analysisservic
es/servers/awsales/providers/microsoft.insights/diagnosticSettings/service
Name                        : service
Type                        :
Location                    :
Tags                        :
```

Met deze uitvoer wordt bevestigd dat logboek registratie nu is ingeschakeld voor de server, waardoor gegevens worden opgeslagen in het opslag account.

U kunt ook bewaarbeleid instellen voor uw Logboeken, zodat oudere logboeken automatisch worden verwijderd. Stel bijvoorbeeld het Bewaar beleid met de vlag **-RetentionEnabled** in op **$True**en stel de para meter **-RetentionInDays** in op **90**. Logboeken die ouder zijn dan 90 dagen worden automatisch verwijderd.

```powershell
Set-AzDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories Engine`
  -RetentionEnabled $true -RetentionInDays 90
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [Azure resource Diagnostic-logboek registratie](../azure-monitor/platform/platform-logs-overview.md).

Zie [set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting) in Power shell Help.
