---
title: Vm's starten/stoppen buiten kantoor uren
description: Deze VM-beheer oplossing Start en stopt uw virtuele Azure-machines volgens een planning en proactief bewaakt van Azure Monitor-Logboeken.
services: automation
ms.subservice: process-automation
ms.date: 04/28/2020
ms.topic: conceptual
ms.openlocfilehash: f7e30fd0d53af7ee61d919b56e9ffcd1f1b6bd36
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82207595"
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>Vm's starten/stoppen buiten kantoor uren in Azure Automation

Het **starten/stoppen van vm's buiten kantoor uren** oplossing start of stopt uw Azure virtual machines. Hiermee worden computers op door de gebruiker gedefinieerde planningen gestart of gestopt, vindt u inzichten via Azure Monitor-logboeken en verzendt u optionele e-mail berichten met behulp van [actie groepen](../azure-monitor/platform/action-groups.md). De oplossing ondersteunt zowel Azure Resource Manager als klassieke Vm's voor de meeste scenario's. 

Deze oplossing maakt gebruik van de cmdlet [Start-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/start-azurermvm?view=azurermps-6.13.0) om vm's te starten. Er wordt gebruikgemaakt van [Stop-AzureRmVM](https://docs.microsoft.com/powershell/module/AzureRM.Compute/Stop-AzureRmVM?view=azurermps-6.13.0) voor het stoppen van vm's.

> [!NOTE]
> De oplossing voor het **starten/stoppen van vm's buiten kantoor uren** is bijgewerkt ter ondersteuning van de nieuwste versies van de Azure-modules die beschikbaar zijn. De bijgewerkte versie van deze oplossing, die beschikbaar is in Marketplace, biedt geen ondersteuning voor AzureRM-modules omdat we hebben gemigreerd van AzureRM naar AZ-modules.

De oplossing biedt een gecentraliseerde automatiserings optie met lage kosten voor gebruikers die hun VM-kosten willen optimaliseren. Met deze oplossing kunt u het volgende doen:

- [Plan vm's om te starten en te stoppen](automation-solution-vm-management-config.md#schedule).
- Plan Vm's om in oplopende volg orde te starten en te stoppen met [behulp van Azure Tags](automation-solution-vm-management-config.md#tags) (niet ondersteund voor klassieke vm's).
- Werk Vm's autostop op basis van [laag CPU-gebruik](automation-solution-vm-management-config.md#cpuutil).

De volgende beperkingen zijn van toepassing op de huidige oplossing:

- Het beheert Vm's in een wille keurige regio, maar kan alleen worden gebruikt in hetzelfde abonnement als uw Azure Automation-account.
- Het is beschikbaar in Azure en Azure Government naar alle regio's die ondersteuning bieden voor een Log Analytics-werk ruimte, een Azure Automation-account en waarschuwingen. Azure Government regio's ondersteunen momenteel geen e-mail functionaliteit.

## <a name="solution-prerequisites"></a>Vereisten voor oplossingen

De runbooks voor deze oplossing werken met een [uitvoeren als-account van Azure](automation-create-runas-account.md). Het run as-account is de voorkeurs verificatie methode omdat deze gebruikmaakt van certificaat verificatie in plaats van een wacht woord dat regel matig verloopt of kan worden gewijzigd.

U wordt aangeraden een afzonderlijk Automation-account te gebruiken voor de oplossing voor het **starten/stoppen van vm's buiten kantoor uren** . Versies van Azure-modules worden vaak geüpgraded en de bijbehorende para meters kunnen veranderen. De oplossing is niet bijgewerkt op hetzelfde uitgebracht en werkt mogelijk niet met nieuwere versies van de cmdlets die worden gebruikt. U wordt aangeraden module-updates te testen in een test Automation-account voordat u deze importeert in uw productie Automation-account (s).

## <a name="solution-permissions"></a>Oplossings machtigingen

U moet bepaalde machtigingen hebben voor het implementeren van de **Vm's starten/stoppen buiten kantoor uren** . De machtigingen verschillen als de oplossing gebruikmaakt van een vooraf gemaakt Automation-account en Log Analytics werk ruimte van de machtigingen die nodig zijn als de oplossing tijdens de implementatie een nieuw account en een werk ruimte maakt. 

U hoeft geen machtigingen te configureren als u een bijdrager levert aan het abonnement en een globale beheerder in uw Azure Active Directory-Tenant. Als u niet over deze rechten beschikt of een aangepaste rol moet configureren, moet u ervoor zorgen dat u de machtigingen hebt die hieronder worden beschreven.

### <a name="permissions-for-pre-existing-automation-account-and-log-analytics-workspace"></a>Machtigingen voor bestaande Automation-accounts en Log Analytics werk ruimte

Voor het implementeren van de **virtuele machines starten/stoppen buiten kantoor** naar een bestaand Automation-account en log Analytics werk ruimte, moeten de gebruiker die de oplossing implementeert de volgende machtigingen hebben voor het bereik van de resource groep. Zie [aangepaste rollen voor Azure-resources](../role-based-access-control/custom-roles.md)voor meer informatie over rollen.

| Machtiging | Bereik|
| --- | --- |
| Micro soft. Automation/automationAccounts/lezen | Resourcegroep |
| Micro soft. Automation/automationAccounts/Varia bles/-schrijven | Resourcegroep |
| Micro soft. Automation/automationAccounts/schedules/write | Resourcegroep |
| Micro soft. Automation/automationAccounts/runbooks/schrijven | Resourcegroep |
| Micro soft. Automation/automationAccounts/Connections/write | Resourcegroep |
| Micro soft. Automation/automationAccounts/certificaten/schrijven | Resourcegroep |
| Micro soft. Automation/automationAccounts/modules/schrijven | Resourcegroep |
| Micro soft. Automation/automationAccounts/modules/lezen | Resourcegroep |
| Micro soft. Automation/automationAccounts/jobSchedules/write | Resourcegroep |
| Micro soft. Automation/automationAccounts/Jobs/schrijven | Resourcegroep |
| Micro soft. Automation/automationAccounts/Jobs/lezen | Resourcegroep |
| Micro soft. OperationsManagement/oplossingen/schrijven | Resourcegroep |
| Micro soft. OperationalInsights/werk ruimten/* | Resourcegroep |
| Micro soft. Insights/diagnosticSettings/schrijven | Resourcegroep |
| Micro soft. Insights/ActionGroups/schrijven | Resourcegroep |
| Micro soft. Insights/ActionGroups/lezen | Resourcegroep |
| Micro soft. resources/abonnementen/resourceGroups/lezen | Resourcegroep |
| Micro soft. resources/implementaties/* | Resourcegroep |

### <a name="permissions-for-new-automation-account-and-new-log-analytics-workspace"></a>Machtigingen voor het nieuwe Automation-account en de nieuwe Log Analytics-werk ruimte

U kunt de **virtuele machines starten/stoppen buiten kantoor** implementeren in een nieuw Automation-account en log Analytics-werk ruimte. In dit geval heeft de gebruiker die de oplossing implementeert de in de voor gaande sectie gedefinieerde machtigingen nodig en de machtigingen die in deze sectie zijn gedefinieerd. 

De gebruiker die de oplossing implementeert, heeft de volgende rollen nodig:

- Cobeheerder van het abonnement. Deze rol is vereist voor het maken van het klassieke uitvoeren als-account als u klassieke Vm's wilt beheren. [Klassieke uitvoeren als-accounts](automation-create-standalone-account.md#create-a-classic-run-as-account) worden niet meer standaard gemaakt.
- Lidmaatschap van de [Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md) Application Developer-rol. Zie [machtigingen voor het configureren van run as-accounts](manage-runas-account.md#permissions)voor meer informatie over het configureren van uitvoeren als-accounts.
- Inzender voor het abonnement of de volgende machtigingen.

| Machtiging |Bereik|
| --- | --- |
| Micro soft. Authorization/Operations/Read | Abonnement|
| Micro soft. Authorization/permissions/Read |Abonnement|
| Micro soft. Authorization/roleAssignments/lezen | Abonnement |
| Microsoft.Authorization/roleAssignments/write | Abonnement |
| Micro soft. Authorization/roleAssignments/verwijderen | Abonnement |
| Micro soft. Automation/automationAccounts/Connections/Read | Resourcegroep |
| Micro soft. Automation/automationAccounts/certificaten/lezen | Resourcegroep |
| Micro soft. Automation/automationAccounts/schrijven | Resourcegroep |
| Micro soft. OperationalInsights/werk ruimten/schrijven | Resourcegroep |

## <a name="solution-components"></a>Oplossingsonderdelen

De **oplossing voor het starten/stoppen van vm's buiten kantoor uren** bevat vooraf geconfigureerde runbooks, planningen en integratie met Azure monitor-Logboeken. U kunt deze elementen gebruiken om het opstarten en afsluiten van uw virtuele machines aan te passen aan de behoeften van uw bedrijf.

### <a name="runbooks"></a>Runbooks

De volgende tabel geeft een lijst van de runbooks die de oplossing implementeert voor uw Automation-account. Breng geen wijzigingen aan in de runbook-code. In plaats daarvan schrijft u uw eigen runbook voor nieuwe functionaliteit.

> [!IMPORTANT]
> Voer geen wille keurig runbook direct uit met een **onderliggend element** dat is toegevoegd aan de naam.

Alle bovenliggende runbooks bevatten de `WhatIf` para meter. Als deze eigenschap is ingesteld op True, wordt de para meter gebruikt om het exacte gedrag te bepalen dat het runbook uitvoert wanneer het wordt uitgevoerd zonder de para meter en wordt gevalideerd of de juiste Vm's zijn gericht. Een runbook voert alleen de gedefinieerde acties uit wanneer `WhatIf` de para meter is ingesteld op ONWAAR.

|Runbook | Parameters | Beschrijving|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | Aangeroepen vanuit het bovenliggende runbook. Dit runbook maakt waarschuwingen per resource voor het scenario voor automatisch stoppen.|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf: True of False  | Hiermee worden Azure-waarschuwings regels gemaakt of bijgewerkt op Vm's in het doel abonnement of resource groepen. <br> `VMList`is een door komma's gescheiden lijst met virtuele machines. Bijvoorbeeld `vm1, vm2, vm3`.<br> `WhatIf`Hiermee wordt de validatie van de runbook-logica mogelijk zonder dat deze wordt uitgevoerd.|
|AutoStop_Disable | Geen | Hiermee schakelt u waarschuwingen voor automatisch stoppen en standaard schema uit.|
|AutoStop_VM_Child | WebHookData | Aangeroepen vanuit het bovenliggende runbook. Waarschuwings regels roepen dit runbook aan om een klassieke virtuele machine te stoppen.|
|AutoStop_VM_Child_ARM | WebHookData |Aangeroepen vanuit het bovenliggende runbook. Waarschuwings regels roepen dit runbook aan om een virtuele machine te stoppen.  |
|ScheduledStartStop_Base_Classic | CloudServiceName<br> Actie: starten of stoppen<br> VMList  | Hiermee wordt de actie voor het starten of stoppen van een klassieke VM-groep door Cloud Services uitgevoerd. |
|ScheduledStartStop_Child | VMName <br> Actie: starten of stoppen <br> ResourceGroupName | Aangeroepen vanuit het bovenliggende runbook. Hiermee wordt een start-of stop actie uitgevoerd voor de geplande beëindiging.|
|ScheduledStartStop_Child_Classic | VMName<br> Actie: starten of stoppen<br> ResourceGroupName | Aangeroepen vanuit het bovenliggende runbook. Hiermee wordt een start-of stop actie uitgevoerd voor de geplande stop voor klassieke Vm's. |
|ScheduledStartStop_Parent | Actie: starten of stoppen <br>VMList <br> WhatIf: True of False | Hiermee worden alle virtuele machines in het abonnement gestart of gestopt. Bewerk de variabelen `External_Start_ResourceGroupNames` en `External_Stop_ResourceGroupNames` alleen voor het uitvoeren van deze doel resource groepen. U kunt ook specifieke Vm's uitsluiten door de `External_ExcludeVMNames` variabele bij te werken.|
|SequencedStartStop_Parent | Actie: starten of stoppen <br> WhatIf: True of False<br>VMList| Maakt labels met de naam **sequencestart** en **sequencestop** op elke virtuele machine waarvoor u de activiteit start/stop wilt sequentieren. Deze label namen zijn hoofdletter gevoelig. De waarde van het label moet een positief geheel getal zijn (1, 2, 3) dat overeenkomt met de volg orde waarin u wilt starten of stoppen. <br>**Opmerking**: vm's moeten zich in resource groepen bezien `External_Start_ResourceGroupNames`die `External_Stop_ResourceGroupNames`zijn gedefinieerd `External_ExcludeVMNames` in, en variabelen. Ze moeten de juiste labels hebben om de acties van kracht te laten worden.|

### <a name="variables"></a>Variabelen

De volgende tabel bevat de variabelen die zijn gemaakt in uw Automation-account. Wijzig variabelen alleen met de voor `External`voegsels. Het wijzigen van variabelen die `Internal` met een probleem worden opgelost, leidt tot ongewenste effecten.

> [!NOTE]
> De beperkingen voor de VM-naam en resource groep zijn grotendeels het resultaat van de variabele grootte. Zie [variabele assets in azure Automation](https://docs.microsoft.com/azure/automation/shared-resources/variables).

|Variabele | Beschrijving|
|---------|------------|
|External_AutoStop_Condition | De voorwaardelijke operator die is vereist voor het configureren van de voor waarde voordat een waarschuwing wordt geactiveerd. Acceptabele waarden `GreaterThan`zijn `GreaterThanOrEqual`, `LessThan`, en `LessThanOrEqual`.|
|External_AutoStop_Description | De waarschuwing voor het stoppen van de virtuele machine als het CPU-percentage de drempel waarde overschrijdt.|
|External_AutoStop_Frequency | De evaluatie frequentie voor de regel. Deze para meter accepteert invoer in time span-indeling. Mogelijke waarden zijn 5 minuten tot zes uur. |
|External_AutoStop_MetricName | De naam van de prestatie gegevens waarvoor de Azure-waarschuwings regel moet worden geconfigureerd.|
|External_AutoStop_Severity | Ernst van de metrische waarschuwing, die tussen 0 en 4 ligt. |
|External_AutoStop_Threshold | De drempel waarde voor de Azure-waarschuwings regel die is `External_AutoStop_MetricName`opgegeven in de variabele. Percentage waarden variëren van 1 tot en met 100.|
|External_AutoStop_TimeAggregationOperator | De tijd aggregatie operator die wordt toegepast op de geselecteerde venster grootte om de voor waarde te evalueren. Acceptabele waarden `Average`zijn `Minimum`, `Maximum` `Total`,, en `Last`.|
|External_AutoStop_TimeWindow | De grootte van het venster waarin Azure geselecteerde metrische gegevens analyseert voor het activeren van een waarschuwing. Deze para meter accepteert invoer in time span-indeling. Mogelijke waarden zijn 5 minuten tot zes uur.|
|External_EnableClassicVMs| Waarde die aangeeft of klassieke Vm's zijn gericht op de oplossing. De standaard waarde is True. Stel deze variabele in op False voor Azure Cloud Solution Provider-abonnementen (CSP). Klassieke Vm's vereisen een [klassiek uitvoeren als-account](automation-create-standalone-account.md#create-a-classic-run-as-account).|
|External_ExcludeVMNames | Een door komma's gescheiden lijst met VM-namen die moeten worden uitgesloten, beperkt tot 140 Vm's. Als u meer dan 140 Vm's aan de lijst toevoegt, kunnen de Vm's die zijn ingesteld om te worden uitgesloten, per ongeluk worden gestart of gestopt.|
|External_Start_ResourceGroupNames | Een door komma's gescheiden lijst van een of meer resource groepen die zijn gericht op Start acties.|
|External_Stop_ResourceGroupNames | Een door komma's gescheiden lijst van een of meer resource groepen die zijn gericht op Stop acties.|
|External_WaitTimeForVMRetrySeconds |De wacht tijd in seconden voor de acties die moeten worden uitgevoerd op de Vm's voor het **SequencedStartStop_Parent** runbook. Met deze variabele kan het runbook wachten op onderliggende bewerkingen gedurende een opgegeven aantal seconden voordat u doorgaat met de volgende actie. De maximale wacht tijd is 10800 of drie uur. De standaard waarde is 2100 seconden.|
|Internal_AutomationAccountName | Hiermee geeft u de naam van het Automation-account.|
|Internal_AutoSnooze_ARM_WebhookURI | De webhook-URI die wordt aangeroepen voor het scenario voor autostop voor Vm's.|
|Internal_AutoSnooze_WebhookUri | De webhook-URI die wordt aangeroepen voor het scenario voor autostop voor klassieke Vm's.|
|Internal_AzureSubscriptionId | De ID van het Azure-abonnement.|
|Internal_ResourceGroupName | De naam van de resource groep voor het Automation-account.|

>[!NOTE]
>De standaard waarde `External_WaitTimeForVMRetryInSeconds`voor de variabele is bijgewerkt van 600 naar 2100. 

In alle scenario's, de variabelen `External_Start_ResourceGroupNames`, `External_Stop_ResourceGroupNames`en `External_ExcludeVMNames` zijn nodig voor het richten op vm's, met uitzonde ring van de lijsten met door komma's gescheiden vm's voor de **AutoStop_CreateAlert_Parent**, **SequencedStartStop_Parent**en **ScheduledStartStop_Parent** runbooks. Dat wil zeggen dat uw Vm's deel moeten uitmaken van de doel resource groepen voor het starten en stoppen van de acties. De logica werkt op soort gelijke wijze als Azure Policy, in dat u het abonnement of de resource groep kunt bereiken en acties moet hebben overgenomen door nieuw gemaakte Vm's. Op deze manier wordt voor komen dat u een afzonderlijke planning moet onderhouden voor elke VM en het beheer begint en stopt op schaal.

### <a name="schedules"></a>Planningen

De volgende tabel bevat een overzicht van de standaard schema's die zijn gemaakt in uw Automation-account.U kunt ze wijzigen of uw eigen aangepaste schema's maken.Standaard zijn alle schema's uitgeschakeld, met uitzonde ring van de **Scheduled_StartVM** -en **Scheduled_StopVM** -schema's.

Schakel niet alle schema's in, omdat dit mogelijk overlappende plannings acties kan maken. U kunt het beste bepalen welke optimalisaties u wilt uitvoeren en ze dienovereenkomstig aanpassen. Zie de voorbeeld scenario's in de sectie Overzicht voor meer informatie.

|Schema naam | Frequency | Beschrijving|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | Om de 8 uur | Voert het **AutoStop_CreateAlert_Parent** runbook uit om de 8 uur, waardoor de op virtuele machines gebaseerde waarden in `External_Start_ResourceGroupNames`, `External_Stop_ResourceGroupNames`en `External_ExcludeVMNames` variabelen op zijn beurt worden gestopt. U kunt ook een door komma's gescheiden lijst met Vm's opgeven met behulp van de `VMList` para meter.|
|Scheduled_StopVM | Door de gebruiker gedefinieerd, dagelijks | Voert het **ScheduledStopStart_Parent** runbook uit met een para `Stop` meter van elke dag op de opgegeven tijd.Stopt automatisch alle virtuele machines die voldoen aan de regels die zijn gedefinieerd door variabele assets.Schakel de planning **StartVM**.|
|Scheduled_StartVM | Door de gebruiker gedefinieerd, dagelijks | Voert het **ScheduledStopStart_Parent** runbook uit met een parameter waarde `Start` van elke dag op de opgegeven tijd. Alle Vm's die voldoen aan de regels die zijn gedefinieerd door variabele assets, worden automatisch gestart.Schakel de planning **StopVM**.|
|Sequenced-StopVM | 1:00 uur (UTC), elke vrijdag | Voert het **Sequenced_StopStop_Parent** runbook uit met een parameter waarde `Stop` van elke vrijdag op de opgegeven tijd.Opeenvolgend (oplopend) stopt alle virtuele machines met een tag van **SequenceStop** die zijn gedefinieerd door de juiste variabelen. Zie [Runbooks](#runbooks)voor meer informatie over label waarden en activa variabelen.Schakel het gerelateerde schema in, **Sequenced-StartVM**.|
|Sequenced-StartVM | 1:00 uur (UTC), elke maandag | Voert het **SequencedStopStart_Parent** runbook uit met een parameter waarde `Start` van elke maandag op de opgegeven tijd. Na elkaar (aflopend) worden alle virtuele machines gestart met een tag van **SequenceStart** dat is gedefinieerd door de juiste variabelen. Zie [Runbooks](#runbooks)voor meer informatie over label waarden en variabele assets. Schakel het gerelateerde schema in, **Sequenced-StopVM**.

## <a name="use-of-the-solution-with-classic-vms"></a>Gebruik van de oplossing met klassieke Vm's

Als u de oplossing **Vm's starten/stoppen buiten kantoor uren** gebruikt voor klassieke vm's, worden alle vm's opeenvolgend per Cloud service verwerkt. Vm's worden nog steeds parallel verwerkt in verschillende Cloud Services. 

Voor het gebruik van de oplossing met klassieke Vm's hebt u een klassiek uitvoeren als-account nodig, dat niet standaard wordt gemaakt. Zie [een klassiek uitvoeren als-account maken](automation-create-standalone-account.md#create-a-classic-run-as-account)voor instructies voor het maken van een klassiek uitvoeren als-account.

Als u meer dan 20 Vm's per Cloud service hebt, zijn hier enkele aanbevelingen:

* Meerdere planningen maken met het bovenliggende runbook **ScheduledStartStop_Parent** en 20 vm's per schema opgeven. 
* In de schema-eigenschappen gebruikt u `VMList` de para meter om VM-namen op te geven als een lijst met door komma's gescheiden waarden. 

Als de Automation-taak voor deze oplossing meer dan drie uur wordt uitgevoerd, wordt deze tijdelijk uit het geheugen verwijderd of gestopt volgens de limiet voor de [billijke share](automation-runbook-execution.md#fair-share) .

Azure CSP-abonnementen bieden alleen ondersteuning voor het Azure Resource Manager model. Niet-Azure Resource Manager services zijn niet beschikbaar in het programma. Wanneer de **oplossing Vm's starten/stoppen buiten kantoor uren** wordt uitgevoerd, kunnen er fouten optreden omdat deze cmdlets bevat voor het beheren van klassieke resources. Zie [beschik bare Services in CSP-abonnementen](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services)voor meer informatie over CSP. Als u een CSP-abonnement gebruikt, moet u de variabele [External_EnableClassicVMs](#variables) instellen op False na de implementatie.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-the-solution"></a>De oplossing inschakelen

Als u de oplossing wilt gaan gebruiken, volgt u de stappen in de [oplossing Vm's starten/stoppen inschakelen](automation-solution-vm-management-enable.md).

## <a name="view-the-solution"></a>De oplossing bekijken

Gebruik een van de volgende mechanismen om toegang te krijgen tot de oplossing nadat u deze hebt ingeschakeld:

* Selecteer in uw Automation-account de optie **VM starten/stoppen** onder **gerelateerde resources**. Op de pagina virtuele machine starten/stoppen selecteert u **de oplossing beheren** aan de rechter kant van de pagina, onder oplossingen voor het **starten/stoppen van virtuele machines beheren**.

* Ga naar de Log Analytics werkruimte die aan uw Automation-account is gekoppeld. Nadat u de werk ruimte hebt geselecteerd, kiest u **oplossingen** in het linkerdeel venster. Selecteer op de pagina oplossingen de oplossing **Start-Stop-VM [werkruimte]** in de lijst.  

Als u de oplossing selecteert, wordt de oplossings pagina **Start-Stop-VM [werkruimte]** weer gegeven. Hier kunt u belang rijke informatie bekijken, zoals de informatie in de tegel **StartStopVM** . Net als in uw Log Analytics-werk ruimte worden in deze tegel een aantal en een grafische weer gave weer gegeven van de runbook-taken voor de oplossing die is gestart en voltooid.

![Pagina Automation Updatebeheer-oplossing](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

U kunt verdere analyse van de taak records uitvoeren door op de ring-tegel te klikken. Het oplossings Dashboard toont taak geschiedenis en vooraf gedefinieerde zoek opdrachten in het logboek. Ga naar de geavanceerde log Analytics-Portal om te zoeken op basis van uw zoek query's.

## <a name="update-the-solution"></a>De oplossing bijwerken

Als u een eerdere versie van deze oplossing hebt geïmplementeerd, verwijdert u deze uit uw account voordat u een bijgewerkte versie implementeert. Volg de stappen om [de oplossing te verwijderen](#remove-the-solution) en volg de stappen om [de oplossing te implementeren](automation-solution-vm-management-enable.md).

## <a name="remove-the-solution"></a>De oplossing verwijderen

Als u de oplossing niet meer nodig hebt, kunt u deze verwijderen uit het Automation-account. Als u de oplossing verwijdert, worden alleen de runbooks verwijderd. De schema's of variabelen die zijn gemaakt toen de oplossing werd toegevoegd, worden niet verwijderd. Verwijder deze assets hand matig als u deze niet gebruikt met andere runbooks.

De oplossing verwijderen:

1. Selecteer in uw Automation-account **gekoppelde werk ruimte** onder **gerelateerde resources**.

2. Selecteer **Ga naar werk ruimte**.

3. Klik op **oplossingen** onder **Algemeen**. 

4. Selecteer op de pagina oplossingen de oplossing **Start-Stop-VM [werk ruimte]**. 

5. Selecteer op de pagina **VMManagementSolution [workspace]** **verwijderen** in het menu.<br><br> ![VM-beheer oplossing verwijderen](media/automation-solution-vm-management/vm-management-solution-delete.png)

6. Bevestig in het venster **oplossing verwijderen** dat u de oplossing wilt verwijderen.

7. Terwijl de informatie wordt gecontroleerd en de oplossing wordt verwijderd, kunt u de voortgang bijhouden onder **meldingen**, geselecteerd in het menu. U keert terug naar de pagina oplossingen nadat het proces voor het verwijderen van de oplossing wordt gestart.

Het Automation-account en de Log Analytics-werk ruimte worden niet verwijderd als onderdeel van dit proces. Als u de Log Analytics-werk ruimte niet wilt gebruiken, moet u deze hand matig verwijderen uit de Azure Portal:

1. Zoek en selecteer **log Analytics-werk ruimten**.

2. Selecteer de werk ruimte op de pagina Log Analytics werk ruimte.

3. Selecteer **verwijderen** in het menu op de pagina werkruimte instellingen.

4. Als u de [oplossings onderdelen](#solution-components)van Azure Automation-account niet wilt blijven gebruiken, kunt u elke hand matig verwijderen.

## <a name="next-steps"></a>Volgende stappen

[Schakel](automation-solution-vm-management-enable.md) de oplossing **vm's starten/stoppen buiten kantoor uren** in voor uw Azure-vm's.
