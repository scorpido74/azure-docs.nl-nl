---
title: Diagnostische logboekregistratie voor Azure Analysis Services | Microsoft Documenten
description: Beschrijft hoe u diagnostische logboekregistratie van Azure-bronnen instellen om uw Azure Analysis Services-server te bewaken.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 0f13f297facedceb50920c0f6afca63fe1df0b48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266180"
---
# <a name="setup-diagnostic-logging"></a>Registratie in diagnoselogboek instellen

Een belangrijk onderdeel van elke Analysis Services-oplossing is het monitoren van de prestaties van uw servers. Met [Azure-bronlogboeken](../azure-monitor/platform/platform-logs-overview.md)u logboeken controleren en verzenden naar [Azure Storage,](https://azure.microsoft.com/services/storage/)deze streamen naar [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)en deze exporteren naar [Azure Monitor-logboeken.](../azure-monitor/azure-monitor-log-hub.md)

![Diagnostische logboekregistratie voor opslag-, gebeurtenishubs- of Azure-monitorlogboeken](./media/analysis-services-logging/aas-logging-overview.png)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="whats-logged"></a>Wat wordt er geregistreerd?

U **categorieën Engine,** **Service**en **Metrics** selecteren.

### <a name="engine"></a>Engine

**Engine logs** selecteren alle [xEvents](https://docs.microsoft.com/analysis-services/instances/monitor-analysis-services-with-sql-server-extended-events). U geen afzonderlijke gebeurtenissen selecteren. 

|XEvent-categorieën |Gebeurtenisnaam  |
|---------|---------|
|Beveiligingscontrole    |   Controle van aanmeldingen      |
|Beveiligingscontrole    |   Controle van afmeldingen      |
|Beveiligingscontrole    |   Controleserver start en stopt      |
|Voortgangsrapporten     |   Voortgangsrapport begin      |
|Voortgangsrapporten     |   Einde voortgangsrapport      |
|Voortgangsrapporten     |   Voortgangsrapport actueel      |
|Query's     |  Start van query       |
|Query's     |   Einde van query      |
|Opdrachten     |  Opdracht Begin       |
|Opdrachten     |  Opdracht einde       |
|Fouten & waarschuwingen     |   Fout      |
|Ontdekken     |   Ontdek End      |
|Melding     |    Melding     |
|Sessie     |  Initialisatie van sessies       |
|Vergrendelingen    |  Impasse       |
|Queryverwerking     |   VertiPaq SE-query begin      |
|Queryverwerking     |   VertiPaq SE-queryeinde      |
|Queryverwerking     |   Overeenkomst met de cache van VertiPaq SE-query      |
|Queryverwerking     |   Direct Query Begin      |
|Queryverwerking     |  Direct queryeinde       |

### <a name="service"></a>Service

|Naam van bewerking  |Treedt op wanneer  |
|---------|---------|
|Hervattingserver     |    Een server hervatten     |
|SuspendServer    |   Een server onderbreken      |
|DeleteServer     |    Een server verwijderen     |
|HerstartServer opnieuw opstarten    |     Gebruiker start een server opnieuw op via SSMS of PowerShell    |
|GetServerLogBestanden    |    Serverlogboek voor gebruiker exporteren via PowerShell     |
|ExportModel     |   Gebruiker exporteert een model in de portal met Openen in Visual Studio     |

### <a name="all-metrics"></a>Alle statistieken

In de categorie Statistieken worden dezelfde [serverstatistieken opgeslagen in](analysis-services-monitor.md#server-metrics) de tabel AzureMetrics. Als u [queryscale-out](analysis-services-scale-out.md) gebruikt en metrische gegevens voor elke gelezen replica moet scheiden, gebruikt u in plaats daarvan de tabel AzureDiagnostics, waarbij **OperationName** gelijk is aan **Logmetric**.

## <a name="setup-diagnostics-logging"></a>Configuratie diagnostische logboekregistratie

### <a name="azure-portal"></a>Azure Portal

1. Klik in [Azure-portal](https://portal.azure.com) > server op **Diagnostische logboeken** in de linkernavigatie en klik **vervolgens op Diagnostische gegevens inschakelen**.

    ![Diagnostische logboekregistratie voor Azure Cosmos DB inschakelen in de Azure-portal](./media/analysis-services-logging/aas-logging-turn-on-diagnostics.png)

2. Geef bij **Diagnostische instellingen** de volgende opties op: 

    * **Naam**. Voer een naam in voor de logboeken die u wilt maken.

    * **Archiveren naar een opslagaccount**. Als u deze optie wilt gebruiken, hebt u een bestaand opslagaccount nodig om verbinding mee te maken. Zie [Een opslagaccount maken](../storage/common/storage-create-storage-account.md). Volg de instructies om een Resource Manager-account voor algemene doeleinden te maken en selecteer vervolgens uw opslagaccount door terug te keren naar deze pagina in de portal. Het kan enkele minuten duren voordat nieuwe opslagaccounts worden weergegeven in de vervolgkeuzelijst.
    * **Streamen naar een gebeurtenishub**. Als u deze optie wilt gebruiken, hebt u een bestaande naamruimte voor gebeurtenishubs en gebeurtenishub nodig om verbinding mee te maken. Zie [Create an Event Hubs namespace and an event hub using the Azure portal](../event-hubs/event-hubs-create.md) (Een Event Hub-naamruimte en een Event Hub maken met behulp van de Azure-portal) voor meer informatie. Ga vervolgens terug naar deze pagina in de portal om de naamruimte en beleidsnaam van de gebeurtenishub te selecteren.
    * **Verzenden naar Azure Monitor (Werkruimte Logboekanalyse)**. Als u deze optie wilt gebruiken, gebruikt u een bestaande werkruimte of [maakt u een nieuwe werkruimtebron](../azure-monitor/learn/quick-create-workspace.md) in de portal. Zie [Logboeken weergeven in de werkruimte Loganalytics](#view-logs-in-log-analytics-workspace) in dit artikel voor meer informatie over het weergeven van uw logboeken.

    * **Motor**. Selecteer deze optie om xEvents aan te melden. Als u archiveert naar een opslagaccount, u de bewaartermijn voor de diagnostische logboeken selecteren. Logboeken worden automatisch verwijderd nadat de bewaartermijn is verstreken.
    * **Service**. Selecteer deze optie om gebeurtenissen op serviceniveau te registreren. Als u wilt archiveren in een opslagaccount, kunt u de bewaarperiode voor de diagnostische logboeken selecteren. Logboeken worden automatisch verwijderd nadat de bewaartermijn is verstreken.
    * **Statistieken**. Selecteer deze optie om uitgebreide gegevens op te slaan in [Metrische gegevens](analysis-services-monitor.md#server-metrics). Als u wilt archiveren in een opslagaccount, kunt u de bewaarperiode voor de diagnostische logboeken selecteren. Logboeken worden automatisch verwijderd nadat de bewaartermijn is verstreken.

3. Klik op **Opslaan**.

    Als u een fout ontvangt met de \<tekst 'Kan de diagnose niet worden bijgewerkt voor de naam van de werkruimte>. De \<gebruikersnaam van het abonnement> is niet geregistreerd om microsoft.insights te gebruiken." volg de instructies [voor Azure Diagnostics oplossen](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) om het account te registreren en probeer deze procedure opnieuw.

    Als u wilt wijzigen hoe uw diagnostische logboeken op elk moment in de toekomst worden opgeslagen, u terugkeren naar deze pagina om de instellingen te wijzigen.

### <a name="powershell"></a>PowerShell

Hier zijn de basiscommando's om je op weg te krijgen. Als u stapsgewijze hulp wilt bij het instellen van logboekregistratie voor een opslagaccount met PowerShell, raadpleegt u de zelfstudie later in dit artikel.

Gebruik de volgende opdrachten om metrische gegevens en logboekregistratie van diagnostische gegevens in te schakelen met behulp van PowerShell:

- Gebruik deze opdracht om de opslag van diagnostische logboeken in een opslagaccount in te schakelen:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   De opslagaccount-id is de resource-id voor het opslagaccount waarnaar u de logboeken wilt verzenden.

- Gebruik deze opdracht om het streamen van diagnostische logboeken naar een Event Hub in te schakelen:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   De id voor de Azure Service Bus-regel is een tekenreeks met deze indeling:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- Gebruik deze opdracht om het verzenden van diagnostische logboeken naar een Log Analytics-werkruimte in te schakelen:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- U kunt de resource-id van de Log Analytics-werkruimte achterhalen door de volgende opdracht te gebruiken:

   ```powershell
   (Get-AzOperationalInsightsWorkspace).ResourceId
   ```

U kunt deze parameters combineren om meerdere uitvoeropties in te schakelen.

### <a name="rest-api"></a>REST API

Leer hoe u [diagnostische instellingen kunt wijzigen met behulp van de Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/). 

### <a name="resource-manager-template"></a>Resource Manager-sjabloon

Leer hoe u [diagnostische instellingen kunt inschakelen tijdens het maken van een resource met behulp van een Resource Manager-sjabloon](../azure-monitor/platform/diagnostic-settings-template.md). 

## <a name="manage-your-logs"></a>Logboeken beheren

Logboeken zijn meestal beschikbaar binnen een paar uur na het instellen van logboekregistratie. Het is aan u om uw logboeken in uw opslagaccount te beheren:

* Gebruik standaardmethoden van Azure voor toegangsbeheer om uw logboeken te beveiligen door het aantal gebruikers te beperken dat toegang heeft tot de logboeken.
* Verwijder de logboeken die u niet meer in uw opslagaccount wilt bewaren.
* Zorg ervoor dat u een bewaartermijn instelt zodat oude logboeken worden verwijderd uit uw opslagaccount.

## <a name="view-logs-in-log-analytics-workspace"></a>Logboeken weergeven in de werkruimte Log Analytics

Statistieken en servergebeurtenissen zijn geïntegreerd met xEvents in uw Log Analytics-werkruimtebron voor side-by-side analyse. Log Analytics-werkruimte kan ook worden geconfigureerd om gebeurtenissen van andere Azure-services te ontvangen en biedt een holistische weergave van diagnostische logboekregistratiegegevens in uw architectuur.

Als u uw diagnostische gegevens wilt weergeven, opent u in de werkruimte Log Analytics **logboeken** in het linkermenu.

![Zoekopties voor logboeken in de Azure-portal](./media/analysis-services-logging/aas-logging-open-log-search.png)

Vouw in de querybuilder **LogManagement** > **AzureDiagnostics**uit . AzureDiagnostics bevat engine- en servicegebeurtenissen. Let op dat er on-the-fly een query wordt gemaakt. Het veld\_EventClass bevat xEvent-namen, die er bekend uitzien als u xEvents hebt gebruikt voor on-premises logboekregistratie. Klik **op\_EventClass s** of een van de gebeurtenisnamen en de werkruimte Log Analytics blijft een query samenstellen. Vergeet niet om uw query's op te slaan, zodat u ze later opnieuw kunt gebruiken.

### <a name="example-queries"></a>Voorbeelden van query's

#### <a name="example-1"></a>Voorbeeld 1

Met de volgende query retourneert de duur van elke queryeinde/vernieuwingsgebeurtenis voor een modeldatabase en -server. Als de resultaten zijn uitgesplitst per replica, omdat het replicanummer is opgenomen in ServerName_s. Groeperen met RootActivityId_g vermindert het aantal rijen dat is opgehaald uit de Azure Diagnostics REST API en helpt binnen de limieten te blijven zoals beschreven in [de limieten van log-analysetarief.](https://dev.loganalytics.io/documentation/Using-the-API/Limits)

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

Met de volgende query retourneert geheugen en QPU-verbruik voor een server. Als de resultaten zijn uitgesplitst per replica, omdat het replicanummer is opgenomen in ServerName_s.

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

In de volgende query worden de prestatiemeteritems voor de engine voor analysis services voor een server voor rijen gelezen/sec geretourneerd.

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

Er zijn honderden query's die u kunt gebruiken. Zie Aan de slag [met Azure Monitor-logboekquery's](../azure-monitor/log-query/get-started-queries.md)voor meer informatie over query's.


## <a name="turn-on-logging-by-using-powershell"></a>Logboekregistratie inschakelen met PowerShell

In deze snelle zelfstudie maakt u een opslagaccount in dezelfde abonnements- en brongroep als uw Analysis Service-server. Vervolgens gebruikt u Set-AzDiagnosticSetting om diagnostische logboekregistratie in te schakelen en uitvoer naar het nieuwe opslagaccount te verzenden.

### <a name="prerequisites"></a>Vereisten
Als u deze zelfstudie wilt voltooien, moet u de volgende bronnen hebben:

* Een bestaande Azure Analysis Services-server. Zie [Een server maken in Azure-portal](analysis-services-create-server.md)of Een Azure Analysis [Services-server maken met PowerShell](analysis-services-create-powershell.md)voor instructies voor het maken van een serverbron.

### <a name="aconnect-to-your-subscriptions"></a></a>Verbinding maken met uw abonnementen

Start een Azure PowerShell-sessie en gebruik de volgende opdracht om u aan te melden bij uw Azure-account:  

```powershell
Connect-AzAccount
```

Voer in het pop-upvenster in de browser uw gebruikersnaam en wachtwoord voor uw Azure-account in. Azure PowerShell haalt alle abonnementen op die zijn gekoppeld aan dit account en gebruikt standaard het eerste abonnement.

Als u meerdere abonnementen hebt, moet u wellicht specifiek opgeven welk abonnement is gebruikt voor het maken van uw Azure Sleutelkluis. Typ het volgende als u de abonnementen voor uw account wilt zien:

```powershell
Get-AzSubscription
```

Typ vervolgens het abonnement dat is gekoppeld aan het Azure Analysis Services-account dat u registreert:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Als u meerdere abonnementen aan uw account hebt gekoppeld, is het belangrijk om het abonnement op te geven.
>
>

### <a name="create-a-new-storage-account-for-your-logs"></a>Een nieuw opslagaccount voor uw logboeken maken

U een bestaand opslagaccount gebruiken voor uw logboeken, op voorwaarde dat deze zich in hetzelfde abonnement bevindt als uw server. Voor deze zelfstudie maakt u een nieuw opslagaccount dat is gewijd aan analysis services-logboeken. Om het u gemakkelijk te maken, slaat u de opslagaccountgegevens op in een variabele met de naam **sa.**

U gebruikt ook dezelfde brongroep als de groep die uw Analysis Services-server bevat. Vervang waarden `awsales_resgroup` `awsaleslogs`voor `West Central US` , en met uw eigen waarden:

```powershell
$sa = New-AzStorageAccount -ResourceGroupName awsales_resgroup `
-Name awsaleslogs -Type Standard_LRS -Location 'West Central US'
```

### <a name="identify-the-server-account-for-your-logs"></a>Het serveraccount voor uw logboeken identificeren

Stel de accountnaam in op een **account**met variabele naam , waarbij ResourceName de naam van het account is.

```powershell
$account = Get-AzResource -ResourceGroupName awsales_resgroup `
-ResourceName awsales -ResourceType "Microsoft.AnalysisServices/servers"
```

### <a name="enable-logging"></a>Logboekregistratie inschakelen

Als u logboekregistratie wilt inschakelen, gebruikt u de cmdlet Set-AzDiagnosticSetting samen met de variabelen voor het nieuwe opslagaccount, het serveraccount en de categorie. Voer de volgende opdracht uit en stel de vlag **in ingeschakeld** op **$true:**

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

Deze uitvoer bevestigt dat logboekregistratie nu is ingeschakeld voor de server en dat informatie wordt opgeslagen in het opslagaccount.

U ook het bewaarbeleid voor uw logboeken instellen, zodat oudere logboeken automatisch worden verwijderd. Stel bijvoorbeeld het bewaarbeleid in met de vlag **-RetentionEnabled** **op $true**en stel de parameter **RetentionInDays** in op **90**. Logboeken ouder dan 90 dagen worden automatisch verwijderd.

```powershell
Set-AzDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories Engine`
  -RetentionEnabled $true -RetentionInDays 90
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [diagnostische logboekregistratie voor Azure-bronnen](../azure-monitor/platform/platform-logs-overview.md).

Zie [Set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting) in PowerShell help.
