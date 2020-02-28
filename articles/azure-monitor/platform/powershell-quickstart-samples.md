---
title: Voor beelden van Azure Monitor Power shell-snel starten
description: Gebruik Power shell om toegang te krijgen tot Azure Monitor functies, zoals automatisch schalen, waarschuwingen, webhooks en het zoeken naar activiteiten Logboeken.
ms.subservice: ''
ms.topic: conceptual
ms.date: 2/14/2018
ms.openlocfilehash: 9f039f71954998ef561d1efd1e559318740c86ab
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77659286"
---
# <a name="azure-monitor-powershell-quick-start-samples"></a>Voor beelden van Azure Monitor Power shell-snel starten
In dit artikel wordt beschreven hoe u Power shell-opdrachten kunt gebruiken om toegang te krijgen tot Azure Monitor-functies.

> [!NOTE]
> Azure Monitor is de nieuwe naam voor wat ' Azure Insights ' werd genoemd tot Sept 25e, 2016. De naam ruimten en daarom bevatten de volgende opdrachten nog steeds het woord ' Insights '.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="set-up-powershell"></a>Power shell instellen
Als u dat nog niet hebt gedaan, stelt u in dat Power shell op uw computer moet worden uitgevoerd. Zie [Power Shell installeren en configureren](/powershell/azure/overview)voor meer informatie.

## <a name="examples-in-this-article"></a>Voor beelden in dit artikel
In de voor beelden in dit artikel wordt beschreven hoe u Azure Monitor-cmdlets kunt gebruiken. U kunt ook de volledige lijst met Azure Monitor Power shell-cmdlets bekijken bij [Azure monitor-cmdlets (inzichten)](https://docs.microsoft.com/powershell/module/az.applicationinsights).

## <a name="sign-in-and-use-subscriptions"></a>Aanmelden en abonnementen gebruiken
Meld u eerst aan bij uw Azure-abonnement.

```powershell
Connect-AzAccount
```

Er wordt een aanmeldings scherm weer gegeven. Zodra u zich hebt aangemeld met uw account, TenantID en standaard abonnements-ID worden weer gegeven. Alle Azure-cmdlets werken in de context van uw standaard abonnement. Als u de lijst met abonnementen waartoe u toegang hebt, wilt weer geven, gebruikt u de volgende opdracht:

```powershell
Get-AzSubscription
```

Gebruik de volgende opdracht om de werk context te bekijken (welk abonnement uw opdrachten worden uitgevoerd):

```powershell
Get-AzContext
```
Als u uw werk context wilt wijzigen in een ander abonnement, gebruikt u de volgende opdracht:

```powershell
Set-AzContext -SubscriptionId <subscriptionid>
```


## <a name="retrieve-activity-log-for-a-subscription"></a>Activiteiten logboek ophalen voor een abonnement
Gebruik de cmdlet [Get-AzLog](https://docs.microsoft.com/powershell/module/az.monitor/get-azlog) .  Hier volgen enkele algemene voor beelden. Het activiteiten logboek bevat de laatste 90 dagen aan bewerkingen. Het gebruik van datums vóór deze tijd resulteert in een fout bericht.  

Bekijk wat de huidige datum/tijd is om te controleren welke tijden moeten worden gebruikt in de onderstaande opdrachten:
```powershell
Get-Date
```

Logboek vermeldingen ophalen van deze tijd/datum om te presen teren:

```powershell
Get-AzLog -StartTime 2019-03-01T10:30
```

Logboek vermeldingen ophalen tussen een tijd/datum bereik:

```powershell
Get-AzLog -StartTime 2019-01-01T10:30 -EndTime 2015-01-01T11:30
```

Logboek vermeldingen ophalen van een specifieke resource groep:

```powershell
Get-AzLog -ResourceGroup 'myrg1'
```

Logboek vermeldingen ophalen van een specifieke resource provider tussen een tijd/datum bereik:

```powershell
Get-AzLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Alle logboek vermeldingen ophalen met een specifieke aanroeper:

```powershell
Get-AzLog -Caller 'myname@company.com'
```

Met de volgende opdracht worden de laatste 1000 gebeurtenissen uit het activiteiten logboek opgehaald:

```powershell
Get-AzLog -MaxRecord 10
```

`Get-AzLog` ondersteunt veel andere para meters. Zie de `Get-AzLog` referentie voor meer informatie.

> [!NOTE]
> `Get-AzLog` biedt alleen 15 dagen aan geschiedenis. Met de para meter **-MaxRecords** kunt u een query uitvoeren op de laatste N gebeurtenissen, na 15 dagen. Als u toegang wilt hebben tot gebeurtenissen die ouder zijn dan 15 dagen,C# gebruikt u de rest API of SDK (steek proef met de SDK). Als u **StartTime**niet opneemt, wordt de standaard waarde **EndTime** min één uur. Als u geen **EndTime**opgeeft, is de standaard waarde huidige tijd. Alle tijden zijn in UTC.
> 
> 

## <a name="retrieve-alerts-history"></a>Geschiedenis van waarschuwingen ophalen
Als u alle waarschuwings gebeurtenissen wilt weer geven, kunt u een query uitvoeren op de Azure Resource Manager logboeken met behulp van de volgende voor beelden.

```powershell
Get-AzLog -Caller "Microsoft.Insights/alertRules" -DetailedOutput -StartTime 2015-03-01
```

Als u de geschiedenis voor een specifieke waarschuwings regel wilt weer geven, kunt u de cmdlet `Get-AzAlertHistory` gebruiken om de resource-ID van de waarschuwings regel door te geven.

```powershell
Get-AzAlertHistory -ResourceId /subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/myalert -StartTime 2016-03-1 -Status Activated
```

De cmdlet `Get-AzAlertHistory` ondersteunt verschillende para meters. Zie [Get-AlertHistory](https://msdn.microsoft.com/library/mt282453.aspx)voor meer informatie.

## <a name="retrieve-information-on-alert-rules"></a>Informatie over waarschuwings regels ophalen
Alle volgende opdrachten worden toegepast op een resource groep met de naam ' montest '.

Bekijk alle eigenschappen van de waarschuwings regel:

```powershell
Get-AzAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
```

Alle waarschuwingen ophalen voor een resource groep:

```powershell
Get-AzAlertRule -ResourceGroup montest
```

Alle waarschuwings regels ophalen die zijn ingesteld voor een doel resource. Bijvoorbeeld alle waarschuwings regels die zijn ingesteld op een virtuele machine.

```powershell
Get-AzAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
```

`Get-AzAlertRule` ondersteunt andere para meters. Zie [Get-AlertRule](https://msdn.microsoft.com/library/mt282459.aspx) voor meer informatie.

## <a name="create-metric-alerts"></a>Metrische waarschuwingen maken
U kunt de cmdlet `Add-AlertRule` gebruiken om een waarschuwings regel te maken, bij te werken of uit te scha kelen.

U kunt de eigenschappen e-mail en webhook maken met behulp van `New-AzAlertRuleEmail` en `New-AzAlertRuleWebhook`. Wijs in de cmdlet waarschuwings regel deze eigenschappen toe als acties voor de eigenschap **Actions** van de waarschuwings regel.

De volgende tabel beschrijft de para meters en waarden die worden gebruikt om een waarschuwing te maken met behulp van een metriek.

| parameter | waarde |
| --- | --- |
| Name |simpletestdiskwrite |
| Locatie van deze waarschuwings regel |VS - oost |
| ResourceGroup |montest |
| TargetResourceId |/subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig |
| Metrische waarde van de waarschuwing die wordt gemaakt |\PhysicalDisk (_Total) \Gelezen schrijf bewerkingen per seconde. Raadpleeg de `Get-MetricDefinitions`-cmdlet voor informatie over het ophalen van de exacte metrische namen |
| operator |GreaterThan |
| Drempel waarde (aantal per seconde in voor deze metrische gegevens) |1 |
| WindowSize (UU: mm: SS-indeling) |00:05:00 |
| aggregator (statistiek van de metrische gegevens, waarbij het gemiddelde aantal in dit geval wordt gebruikt) |Average |
| aangepaste e-mail berichten (teken reeks matrix) |'foo@example.com', 'bar@example.com' |
| e-mail verzenden naar eigen aars, mede werkers en lezers |-SendToServiceOwners |

Een e-mail actie maken

```powershell
$actionEmail = New-AzAlertRuleEmail -CustomEmail myname@company.com
```

Een webhook-actie maken

```powershell
$actionWebhook = New-AzAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

De waarschuwings regel maken voor de CPU% metric op een klassieke virtuele machine

```powershell
Add-AzMetricAlertRule -Name vmcpu_gt_1 -Location "East US" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Action $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

De waarschuwings regel ophalen

```powershell
Get-AzAlertRule -Name vmcpu_gt_1 -ResourceGroup myrg1 -DetailedOutput
```

Met de cmdlet Add alert wordt de regel ook bijgewerkt als er al een waarschuwings regel bestaat voor de opgegeven eigenschappen. Als u een waarschuwings regel wilt uitschakelen, neemt u de para meter **-DisableRule**op.

## <a name="get-a-list-of-available-metrics-for-alerts"></a>Een lijst met beschik bare metrische gegevens voor waarschuwingen ophalen
U kunt de cmdlet `Get-AzMetricDefinition` gebruiken om de lijst met alle metrische gegevens voor een specifieke resource weer te geven.

```powershell
Get-AzMetricDefinition -ResourceId <resource_id>
```

In het volgende voor beeld wordt een tabel met de metrische naam en de eenheid gegenereerd.

```powershell
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

U kunt een volledige lijst met beschik bare opties voor `Get-AzMetricDefinition` vinden op [Get-MetricDefinitions](https://msdn.microsoft.com/library/mt282458.aspx).

## <a name="create-and-manage-activity-log-alerts"></a>Waarschuwingen voor activiteiten logboeken maken en beheren
U kunt de cmdlet `Set-AzActivityLogAlert` gebruiken om een waarschuwing voor een activiteiten logboek in te stellen. Een waarschuwing voor een activiteiten logboek vereist dat u eerst uw voor waarden definieert als een woorden lijst en vervolgens een waarschuwing maakt die deze voor waarden gebruikt.

```powershell

$condition1 = New-AzActivityLogAlertCondition -Field 'category' -Equal 'Administrative'
$condition2 = New-AzActivityLogAlertCondition -Field 'operationName' -Equal 'Microsoft.Compute/virtualMachines/write'
$additionalWebhookProperties = New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"
$additionalWebhookProperties.Add('customProperty', 'someValue')
$actionGrp1 = New-AzActionGroup -ActionGroupId '/subscriptions/<subid>/providers/Microsoft.Insights/actiongr1' -WebhookProperty $additionalWebhookProperties
Set-AzActivityLogAlert -Location 'Global' -Name 'alert on VM create' -ResourceGroupName 'myResourceGroup' -Scope '/subscriptions/<subid>' -Action $actionGrp1 -Condition $condition1, $condition2

```

De aanvullende eigenschappen van de webhook zijn optioneel. U kunt de inhoud van een waarschuwing voor een activiteiten logboek terughalen met behulp van `Get-AzActivityLogAlert`.

## <a name="create-and-manage-autoscale-settings"></a>Instellingen voor automatisch schalen maken en beheren
Voor een resource (een web-app, VM, Cloud service of virtuele-machine Schaalset) kan slechts één instelling voor automatisch schalen zijn geconfigureerd.
Elke instelling voor automatisch schalen kan echter meerdere profielen hebben. Een voor beeld: een voor een schaal profiel op basis van prestaties en een tweede voor een op een planning gebaseerd profiel. Voor elk profiel kunnen meerdere regels worden geconfigureerd. Zie [een toepassing automatisch schalen](../../cloud-services/cloud-services-how-to-scale-portal.md)voor meer informatie over automatisch schalen.

Hier volgen de stappen die u moet gebruiken:

1. Regel (s) maken.
2. Maak een of meer profielen toewijzing van de regels die u eerder hebt gemaakt voor de profielen.
3. Optioneel: meldingen voor automatisch schalen maken door webhook en e-mail eigenschappen te configureren.
4. Maak een instelling voor automatisch schalen met een naam op de doel resource door de profielen en meldingen toe te wijzen die u in de vorige stappen hebt gemaakt.

In de volgende voor beelden ziet u hoe u een instelling voor automatisch schalen kunt maken voor een Schaalset voor virtuele machines voor een Windows-besturings systeem op basis van de metrische gegevens van het CPU-gebruik.

Maak eerst een regel om uit te schalen, met een toename van het aantal exemplaren.

```powershell
$rule1 = New-AzAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 60 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionValue 1
```        

Maak vervolgens een regel om in te schalen, waarbij het aantal exemplaren afneemt.

```powershell
$rule2 = New-AzAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 30 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Decrease -ScaleActionValue 1
```

Maak vervolgens een profiel voor de regels.

```powershell
$profile1 = New-AzAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1,$rule2 -Name "My_Profile"
```

Een webhook-eigenschap maken.

```powershell
$webhook_scale = New-AzAutoscaleWebhook -ServiceUri "https://example.com?mytoken=mytokenvalue"
```

Maak de meldings eigenschap voor de instelling voor automatisch schalen, inclusief e-mail en de webhook die u eerder hebt gemaakt.

```powershell
$notification1= New-AzAutoscaleNotification -CustomEmails ashwink@microsoft.com -SendEmailToSubscriptionAdministrators SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
```

Maak ten slotte de instelling voor automatisch schalen om het profiel toe te voegen dat u eerder hebt gemaakt. 

```powershell
Add-AzAutoscaleSetting -Location "East US" -Name "MyScaleVMSSSetting" -ResourceGroup big2 -TargetResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -AutoscaleProfiles $profile1 -Notifications $notification1
```

Zie [Get-AutoscaleSetting](https://msdn.microsoft.com/library/mt282461.aspx)voor meer informatie over het beheren van instellingen voor automatisch schalen.

## <a name="autoscale-history"></a>Geschiedenis van automatisch schalen
In het volgende voor beeld ziet u hoe u recente automatisch schalen en waarschuwings gebeurtenissen kunt weer geven. Gebruik de zoek functie voor activiteiten Logboeken om de geschiedenis van automatisch schalen weer te geven.

```powershell
Get-AzLog -Caller "Microsoft.Insights/autoscaleSettings" -DetailedOutput -StartTime 2015-03-01
```

U kunt de cmdlet `Get-AzAutoScaleHistory` gebruiken om de geschiedenis van automatisch schalen op te halen.

```powershell
Get-AzAutoScaleHistory -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/microsoft.insights/autoscalesettings/myScaleSetting -StartTime 2016-03-15 -DetailedOutput
```

Zie [Get-AutoscaleHistory](https://msdn.microsoft.com/library/mt282464.aspx)voor meer informatie.

### <a name="view-details-for-an-autoscale-setting"></a>Details weer geven voor een instelling voor automatisch schalen
U kunt de cmdlet `Get-Autoscalesetting` gebruiken om meer informatie over de instelling voor automatisch schalen op te halen.

In het volgende voor beeld ziet u details over alle instellingen voor automatisch schalen in de resource groep ' myrg1 '.

```powershell
Get-AzAutoscalesetting -ResourceGroup myrg1 -DetailedOutput
```

In het volgende voor beeld ziet u details over alle instellingen voor automatisch schalen in de resource groep ' myrg1 ' en met name de instelling voor automatisch schalen met de naam ' MyScaleVMSSSetting '.

```powershell
Get-AzAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting -DetailedOutput
```

### <a name="remove-an-autoscale-setting"></a>Een instelling voor automatisch schalen verwijderen
U kunt de cmdlet `Remove-Autoscalesetting` gebruiken om een instelling voor automatisch schalen te verwijderen.

```powershell
Remove-AzAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting
```

## <a name="manage-log-profiles-for-activity-log"></a>Logboek profielen voor het activiteiten logboek beheren
U kunt een *logboek profiel* maken en gegevens uit uw activiteiten logboek exporteren naar een opslag account en u kunt de gegevens retentie configureren. U kunt eventueel ook de gegevens streamen naar uw event hub. Deze functie is momenteel beschikbaar als preview-versie en u kunt slechts één logboek profiel per abonnement maken. U kunt de volgende cmdlets gebruiken met uw huidige abonnement om logboek profielen te maken en te beheren. U kunt ook een bepaald abonnement kiezen. Hoewel Power Shell standaard het huidige abonnement heeft, kunt u dit altijd wijzigen met behulp van `Set-AzContext`. U kunt het activiteiten logboek configureren voor het routeren van gegevens naar een opslag account of event hub binnen dat abonnement. Gegevens worden geschreven als blob-bestanden in JSON-indeling.

### <a name="get-a-log-profile"></a>Een logboek profiel ophalen
Gebruik de cmdlet `Get-AzLogProfile` om uw bestaande logboek profielen op te halen.

### <a name="add-a-log-profile-without-data-retention"></a>Een logboek profiel zonder gegevens retentie toevoegen
```powershell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Location global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>Een logboek profiel verwijderen
```powershell
Remove-AzLogProfile -name my_log_profile_s1
```

### <a name="add-a-log-profile-with-data-retention"></a>Een logboek profiel met gegevens retentie toevoegen
U kunt de eigenschap **-RetentionInDays** opgeven met het aantal dagen, als een positief geheel getal, waarbij de gegevens behouden blijven.

```powershell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Location global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

### <a name="add-log-profile-with-retention-and-eventhub"></a>Een logboek profiel met retentie en EventHub toevoegen
Naast het door sturen van uw gegevens naar een opslag account, kunt u deze ook streamen naar een event hub. In deze preview-versie is de configuratie van het opslag account verplicht, maar is de Event hub-configuratie optioneel.

```powershell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

## <a name="configure-diagnostics-logs"></a>Diagnostische logboeken configureren
Veel Azure-Services bieden extra logboeken en telemetrie die een of meer van de volgende kunnen doen: 
 - worden geconfigureerd om gegevens in uw Azure Storage-account op te slaan
 - verzonden naar Event Hubs
 - verzonden naar een Log Analytics-werk ruimte. 

De bewerking kan alleen op resource niveau worden uitgevoerd. Het opslag account of Event Hub moet zich in dezelfde regio bevinden als de doel resource waar de diagnostische instelling is geconfigureerd.

### <a name="get-diagnostic-setting"></a>Diagnostische instelling ophalen
```powershell
Get-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp
```

Diagnostische instelling uitschakelen

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $false
```

Diagnostische instelling inschakelen zonder Bewaar periode

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true
```

Diagnostische instellingen inschakelen met retentie

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Diagnostische instellingen inschakelen met retentie voor een specifieke logboek categorie

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/sakteststorage -Categories NetworkSecurityGroupEvent -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Diagnostische instelling voor Event Hubs inschakelen

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Enable $true
```

Diagnostische instelling voor Log Analytics inschakelen

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -WorkspaceId /subscriptions/s1/resourceGroups/insights-integration/providers/providers/microsoft.operationalinsights/workspaces/myWorkspace -Enabled $true

```

Houd er rekening mee dat de eigenschap WorkspaceId de *resource-id* van de werk ruimte gebruikt. U vindt de resource-ID van uw Log Analytics-werkruimte met de volgende opdracht:

```powershell
(Get-AzOperationalInsightsWorkspace).ResourceId

```

Deze opdrachten kunnen worden gecombineerd om gegevens naar meerdere bestemmingen te verzenden.

