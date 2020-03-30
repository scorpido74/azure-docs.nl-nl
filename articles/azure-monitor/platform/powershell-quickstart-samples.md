---
title: Voorbeelden om snel aan de slag te gaan met Azure Monitor PowerShell
description: Gebruik PowerShell om toegang te krijgen tot Azure Monitor-functies zoals automatisch schalen, waarschuwingen, webhooks en zoeken naar activiteitslogboeken.
ms.subservice: ''
ms.topic: conceptual
ms.date: 2/14/2018
ms.openlocfilehash: 9f039f71954998ef561d1efd1e559318740c86ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274318"
---
# <a name="azure-monitor-powershell-quick-start-samples"></a>Voorbeelden om snel aan de slag te gaan met Azure Monitor PowerShell
In dit artikel ziet u voorbeeldopdrachten van PowerShell om u te helpen toegang te krijgen tot Azure Monitor-functies.

> [!NOTE]
> Azure Monitor is de nieuwe naam voor wat "Azure Insights" werd genoemd tot 25 september 2016. De naamruimten en dus de volgende opdrachten bevatten echter nog steeds het woord 'inzichten'.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="set-up-powershell"></a>PowerShell instellen
Als u dit nog niet hebt gedaan, stelt u PowerShell in om op uw computer te worden uitgevoerd. Zie [PowerShell installeren en configureren](/powershell/azure/overview)voor meer informatie.

## <a name="examples-in-this-article"></a>Voorbeelden in dit artikel
De voorbeelden in het artikel illustreren hoe u Azure Monitor-cmdlets gebruiken. U ook de volledige lijst met Azure Monitor PowerShell-cmdlets bekijken op [Azure Monitor (Insights) Cmdlets.](https://docs.microsoft.com/powershell/module/az.applicationinsights)

## <a name="sign-in-and-use-subscriptions"></a>Inloggen en abonnementen gebruiken
Log eerst in bij uw Azure-abonnement.

```powershell
Connect-AzAccount
```

U ziet een inlogscherm. Zodra u zich aanmeldt voor uw account, worden TenantID en standaard abonnements-id weergegeven. Alle Azure-cmdlets werken in de context van uw standaardabonnement. Als u de lijst met abonnementen wilt bekijken waartoe u toegang hebt, gebruikt u de volgende opdracht:

```powershell
Get-AzSubscription
```

Als u uw werkcontext wilt zien (tegen welk abonnement uw opdrachten worden uitgevoerd), gebruikt u de volgende opdracht:

```powershell
Get-AzContext
```
Als u uw werkcontext wilt wijzigen in een ander abonnement, gebruikt u de volgende opdracht:

```powershell
Set-AzContext -SubscriptionId <subscriptionid>
```


## <a name="retrieve-activity-log-for-a-subscription"></a>Activiteitenlogboek ophalen voor een abonnement
Gebruik de [Get-AzLog](https://docs.microsoft.com/powershell/module/az.monitor/get-azlog) cmdlet.  Hieronder volgen enkele veelvoorkomende voorbeelden. Het activiteitenlogboek bevat de laatste 90 dagen van bewerkingen. Het gebruik van datums voor deze tijd resulteert in een foutbericht.  

Bekijk wat de huidige datum/tijd is om te controleren op welke tijden u moet gebruiken in de onderstaande opdrachten:
```powershell
Get-Date
```

Ontvang logboekvermeldingen vanaf deze tijd/datum om te presenteren:

```powershell
Get-AzLog -StartTime 2019-03-01T10:30
```

Logboekvermeldingen tussen een tijd/datumbereik:

```powershell
Get-AzLog -StartTime 2019-01-01T10:30 -EndTime 2015-01-01T11:30
```

Logboekvermeldingen ophalen uit een specifieke resourcegroep:

```powershell
Get-AzLog -ResourceGroup 'myrg1'
```

Ontvang logboekvermeldingen van een specifieke resourceprovider tussen een tijd/datumbereik:

```powershell
Get-AzLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Alle logboekvermeldingen met een specifieke beller:

```powershell
Get-AzLog -Caller 'myname@company.com'
```

Met de volgende opdracht worden de laatste 1000 gebeurtenissen uit het activiteitenlogboek opgehaald:

```powershell
Get-AzLog -MaxRecord 10
```

`Get-AzLog`ondersteunt vele andere parameters. Zie `Get-AzLog` de referentie voor meer informatie.

> [!NOTE]
> `Get-AzLog`biedt slechts 15 dagen geschiedenis. Met de parameter **-MaxRecords** u de laatste N-gebeurtenissen na 15 dagen opvragen. Als u toegang wilt krijgen tot gebeurtenissen ouder dan 15 dagen, gebruikt u de REST API of SDK (C#-voorbeeld met behulp van de SDK). Als u **StartTime**niet opneemt, is de standaardwaarde **EndTime** min één uur. Als u **EndTime**niet opneemt, is de standaardwaarde de huidige tijd. Alle tijden zijn in UTC.
> 
> 

## <a name="retrieve-alerts-history"></a>Waarschuwingsgeschiedenis ophalen
Als u alle waarschuwingsgebeurtenissen wilt weergeven, u de azure resource manager-logboeken opvragen aan de hand van de volgende voorbeelden.

```powershell
Get-AzLog -Caller "Microsoft.Insights/alertRules" -DetailedOutput -StartTime 2015-03-01
```

Als u de geschiedenis voor een specifieke `Get-AzAlertHistory` waarschuwingsregel wilt weergeven, u de cmdlet gebruiken en de resource-id van de waarschuwingsregel doorgeven.

```powershell
Get-AzAlertHistory -ResourceId /subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/myalert -StartTime 2016-03-1 -Status Activated
```

De `Get-AzAlertHistory` cmdlet ondersteunt verschillende parameters. Meer informatie, zie [Get-AlertHistory](https://msdn.microsoft.com/library/mt282453.aspx).

## <a name="retrieve-information-on-alert-rules"></a>Informatie ophalen over waarschuwingsregels
Alle volgende opdrachten werken op een Resource Groep met de naam "montest".

Alle eigenschappen van de waarschuwingsregel weergeven:

```powershell
Get-AzAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
```

Alle waarschuwingen in een resourcegroep ophalen:

```powershell
Get-AzAlertRule -ResourceGroup montest
```

Haal alle waarschuwingsregels op die zijn ingesteld voor een doelbron. Alle waarschuwingsregels die op een vm zijn ingesteld, zijn bijvoorbeeld ingesteld.

```powershell
Get-AzAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
```

`Get-AzAlertRule`ondersteunt andere parameters. Zie [Get-AlertRule](https://msdn.microsoft.com/library/mt282459.aspx) voor meer informatie.

## <a name="create-metric-alerts"></a>Metrische waarschuwingen maken
U `Add-AlertRule` de cmdlet gebruiken om een waarschuwingsregel te maken, bij te werken of uit te schakelen.

U e-mail- `New-AzAlertRuleEmail` en `New-AzAlertRuleWebhook`webhook-eigenschappen maken met respectievelijk en. Wijs deze eigenschappen in de cmdlet Waarschuwingsregel toe als acties aan de eigenschap **Acties** van de waarschuwingsregel.

In de volgende tabel worden de parameters en waarden beschreven die worden gebruikt om een waarschuwing te maken met behulp van een statistiek.

| parameter | waarde |
| --- | --- |
| Name |simpletestdiskwrite |
| Locatie van deze waarschuwingsregel |VS - oost |
| ResourceGroup |montest montest |
| TargetResourceId |/abonnementen/s1/resourceGroepen/montest/providers/Microsoft.Compute/virtualMachines/testconfig |
| MetricName van de waarschuwing die is gemaakt |\PhysicalDisk(_Total)\Schijfschrijft per seconde. Zie `Get-MetricDefinitions` de cmdlet over het ophalen van de exacte metrische namen |
| operator |GreaterThan |
| Drempelwaarde (tel/sec in voor deze statistiek) |1 |
| WindowSize (hh:mm:ss-notatie) |00:05:00 |
| aggregator (statistiek van de statistiek, die gemiddelde telling gebruikt, in dit geval) |Average |
| aangepaste e-mails (tekenreeksarray) |'foo@example.com','bar@example.com' |
| e-mail verzenden naar eigenaren, bijdragers en lezers |-SendtoServiceOwners |

Een e-mailactie maken

```powershell
$actionEmail = New-AzAlertRuleEmail -CustomEmail myname@company.com
```

Een Webhook-actie maken

```powershell
$actionWebhook = New-AzAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

De waarschuwingsregel op de CPU%-statistiek op een klassieke VM maken

```powershell
Add-AzMetricAlertRule -Name vmcpu_gt_1 -Location "East US" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Action $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

De waarschuwingsregel ophalen

```powershell
Get-AzAlertRule -Name vmcpu_gt_1 -ResourceGroup myrg1 -DetailedOutput
```

De cmdlet Waarschuwing toevoegen werkt ook de regel bij als er al een waarschuwingsregel bestaat voor de opgegeven eigenschappen. Als u een waarschuwingsregel wilt uitschakelen, moet u de parameter **Uitschakelenregel opnemen.**

## <a name="get-a-list-of-available-metrics-for-alerts"></a>Een lijst met beschikbare statistieken voor waarschuwingen
U `Get-AzMetricDefinition` de cmdlet gebruiken om de lijst met alle statistieken voor een specifieke resource weer te geven.

```powershell
Get-AzMetricDefinition -ResourceId <resource_id>
```

In het volgende voorbeeld wordt een tabel gegenereerd met de metrische naam en de eenheid ervoor.

```powershell
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Een volledige lijst met `Get-AzMetricDefinition` beschikbare opties voor is beschikbaar op [Get-MetricDefinities](https://msdn.microsoft.com/library/mt282458.aspx).

## <a name="create-and-manage-activity-log-alerts"></a>Waarschuwingen voor activiteitenlogboeken maken en beheren
U `Set-AzActivityLogAlert` de cmdlet gebruiken om een waarschuwing voor het activiteitenlogboek in te stellen. Een waarschuwing voor het activiteitenlogboek vereist dat u eerst uw voorwaarden definieert als een woordenboek met voorwaarden en vervolgens een waarschuwing maakt die deze voorwaarden gebruikt.

```powershell

$condition1 = New-AzActivityLogAlertCondition -Field 'category' -Equal 'Administrative'
$condition2 = New-AzActivityLogAlertCondition -Field 'operationName' -Equal 'Microsoft.Compute/virtualMachines/write'
$additionalWebhookProperties = New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"
$additionalWebhookProperties.Add('customProperty', 'someValue')
$actionGrp1 = New-AzActionGroup -ActionGroupId '/subscriptions/<subid>/providers/Microsoft.Insights/actiongr1' -WebhookProperty $additionalWebhookProperties
Set-AzActivityLogAlert -Location 'Global' -Name 'alert on VM create' -ResourceGroupName 'myResourceGroup' -Scope '/subscriptions/<subid>' -Action $actionGrp1 -Condition $condition1, $condition2

```

De extra webhook-eigenschappen zijn optioneel. U de inhoud van een `Get-AzActivityLogAlert`activiteitslogboekwaarschuwing terugkrijgen met behulp van.

## <a name="create-and-manage-autoscale-settings"></a>Instellingen voor automatisch schalen maken en beheren
Met een resource (een web-app, VM, Cloud Service of Virtual Machine Scale Set) is slechts één instelling voor automatische schaal geconfigureerd.
Elke instelling voor automatisch schalen kan echter meerdere profielen hebben. Bijvoorbeeld een voor een op prestaties gebaseerd schaalprofiel en een tweede voor een schemaprofiel. Op elk profiel kunnen meerdere regels zijn geconfigureerd. Zie Een toepassing automatisch [schalen](../../cloud-services/cloud-services-how-to-scale-portal.md)voor meer informatie over Autoscale.

Hier zijn de stappen om te gebruiken:

1. Regel(en) maken.
2. Maak profiel(en) in kaart brengen van de regels die u eerder hebt gemaakt voor de profielen.
3. Optioneel: Meldingen maken voor automatisch schalen door webhook- en e-maileigenschappen te configureren.
4. Maak een instelling voor automatisch schalen met een naam op de doelbron door de profielen en meldingen in kaart te brengen die u in de vorige stappen hebt gemaakt.

In de volgende voorbeelden ziet u hoe u een instelling voor automatische schaal maken voor een virtuele machineschaalset voor een Windows-besturingssysteem op basis van de statistiek CPU-gebruik.

Maak eerst een regel om uit te schalen, waarbij het aantal instanties wordt verhoogd.

```powershell
$rule1 = New-AzAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 60 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionValue 1
```        

Maak vervolgens een regel om in te schalen, waarbij het aantal instanties wordt verlaagd.

```powershell
$rule2 = New-AzAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 30 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Decrease -ScaleActionValue 1
```

Maak vervolgens een profiel aan voor de regels.

```powershell
$profile1 = New-AzAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1,$rule2 -Name "My_Profile"
```

Maak een eigenschap webhook.

```powershell
$webhook_scale = New-AzAutoscaleWebhook -ServiceUri "https://example.com?mytoken=mytokenvalue"
```

Maak de eigenschap melding voor de instelling voor automatisch schalen, inclusief e-mail en de webhook die u eerder hebt gemaakt.

```powershell
$notification1= New-AzAutoscaleNotification -CustomEmails ashwink@microsoft.com -SendEmailToSubscriptionAdministrators SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
```

Maak ten slotte de instelling voor automatisch schalen om het profiel toe te voegen dat u eerder hebt gemaakt. 

```powershell
Add-AzAutoscaleSetting -Location "East US" -Name "MyScaleVMSSSetting" -ResourceGroup big2 -TargetResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -AutoscaleProfiles $profile1 -Notifications $notification1
```

Zie Automatisch schalen instellen voor meer informatie over het beheren van instellingen voor automatisch [schalen.](https://msdn.microsoft.com/library/mt282461.aspx)

## <a name="autoscale-history"></a>Geschiedenis autoschaal
In het volgende voorbeeld ziet u hoe u recente gebeurtenissen voor automatisch schalen en waarschuwingen weergeven. Gebruik de zoekfunctie voor activiteitenlogboeken om de autoscale-geschiedenis weer te geven.

```powershell
Get-AzLog -Caller "Microsoft.Insights/autoscaleSettings" -DetailedOutput -StartTime 2015-03-01
```

U `Get-AzAutoScaleHistory` de cmdlet gebruiken om de geschiedenis van AutoScale op te halen.

```powershell
Get-AzAutoScaleHistory -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/microsoft.insights/autoscalesettings/myScaleSetting -StartTime 2016-03-15 -DetailedOutput
```

Zie [Get-AutoscaleHistory](https://msdn.microsoft.com/library/mt282464.aspx)voor meer informatie.

### <a name="view-details-for-an-autoscale-setting"></a>Details weergeven voor een instelling voor automatisch schalen
U `Get-Autoscalesetting` de cmdlet gebruiken om meer informatie op te halen over de instelling voor automatisch schalen.

In het volgende voorbeeld ziet u details over alle instellingen voor autoschaal in de resourcegroep 'myrg1'.

```powershell
Get-AzAutoscalesetting -ResourceGroup myrg1 -DetailedOutput
```

In het volgende voorbeeld ziet u details over alle instellingen voor autoscalen in de resourcegroep 'myrg1' en in het bijzonder de instelling voor automatische schaal met de naam 'MyScaleVMSSSetting'.

```powershell
Get-AzAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting -DetailedOutput
```

### <a name="remove-an-autoscale-setting"></a>Een instelling voor automatisch schalen verwijderen
U `Remove-Autoscalesetting` de cmdlet gebruiken om een instelling voor automatisch schalen te verwijderen.

```powershell
Remove-AzAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting
```

## <a name="manage-log-profiles-for-activity-log"></a>Logboekprofielen voor activiteitenlogboek beheren
U een *logboekprofiel* maken en gegevens exporteren vanuit uw activiteitenlogboek naar een opslagaccount en u de gegevensbewaring hiervoor configureren. Optioneel u de gegevens ook streamen naar uw Event Hub. Deze functie bevindt zich momenteel in Preview en u slechts één logboekprofiel per abonnement maken. U de volgende cmdlets met uw huidige abonnement gebruiken om logboekprofielen te maken en te beheren. U ook een bepaald abonnement kiezen. Hoewel PowerShell standaard is voor het huidige abonnement, u dat altijd wijzigen met behulp van `Set-AzContext`. U het activiteitenlogboek configureren om gegevens te routeren naar een opslagaccount of gebeurtenishub binnen dat abonnement. Gegevens worden geschreven als blobbestanden in JSON-indeling.

### <a name="get-a-log-profile"></a>Een logboekprofiel opmaken
Als u uw bestaande logboekprofielen wilt ophalen, gebruikt u de `Get-AzLogProfile` cmdlet.

### <a name="add-a-log-profile-without-data-retention"></a>Een logboekprofiel toevoegen zonder gegevensbewaring
```powershell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Location global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>Een logboekprofiel verwijderen
```powershell
Remove-AzLogProfile -name my_log_profile_s1
```

### <a name="add-a-log-profile-with-data-retention"></a>Een logboekprofiel toevoegen met gegevensbehoud
U de eigenschap **-RetentionInDays** met het aantal dagen opgeven als een positief geheel getal, waar de gegevens worden bewaard.

```powershell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Location global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

### <a name="add-log-profile-with-retention-and-eventhub"></a>Logprofiel toevoegen met retentie en EventHub
Je je gegevens niet alleen routeren naar een opslagaccount, maar ook streamen naar een Event Hub. In deze preview release is de configuratie van het opslagaccount verplicht, maar event hub configuratie is optioneel.

```powershell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

## <a name="configure-diagnostics-logs"></a>Diagnostische logboeken configureren
Veel Azure-services bieden extra logboeken en telemetrie die een of meer van de volgende handelingen kunnen doen: 
 - worden geconfigureerd om gegevens op te slaan in uw Azure Storage-account
 - verzonden naar gebeurtenishubs
 - verzonden naar een Log Analytics-werkruimte. 

De bewerking kan alleen op resourceniveau worden uitgevoerd. Het opslagaccount of de gebeurtenishub moet aanwezig zijn in dezelfde regio als de doelbron waar de diagnostische instelling is geconfigureerd.

### <a name="get-diagnostic-setting"></a>Diagnostische instelling opdoen
```powershell
Get-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp
```

Diagnostische instelling uitschakelen

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $false
```

Diagnostische instelling inschakelen zonder retentie

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true
```

Diagnostische instelling inschakelen met retentie

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Diagnostische instelling inschakelen met retentie voor een specifieke logboekcategorie

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/sakteststorage -Categories NetworkSecurityGroupEvent -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Diagnostische instelling inschakelen voor gebeurtenishubs

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Enable $true
```

Diagnostische instelling inschakelen voor Logboekanalyse

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -WorkspaceId /subscriptions/s1/resourceGroups/insights-integration/providers/providers/microsoft.operationalinsights/workspaces/myWorkspace -Enabled $true

```

Houd er rekening mee dat de eigenschap WorkspaceId de *resource-id* van de werkruimte gebruikt. U de bron-id van uw Log Analytics-werkruimte verkrijgen met de volgende opdracht:

```powershell
(Get-AzOperationalInsightsWorkspace).ResourceId

```

Deze opdrachten kunnen worden gecombineerd om gegevens naar meerdere bestemmingen te verzenden.

