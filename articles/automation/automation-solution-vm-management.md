---
title: Vm's starten/stoppen tijdens off-hours oplossing
description: Deze VM-beheeroplossing start en stopt uw virtuele Azure-machines volgens schema en bewaakt proactief vanuit Azure Monitor-logboeken.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 369e3bcf4e5913f4a3ff82206d1e24a206db3f34
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681296"
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>Vm's starten/stoppen tijdens off-hours oplossing in Azure Automation

De **VM's starten/stoppen tijdens off-hours oplossing** start of stopt uw virtuele Azure-machines. Het start of stopt machines op door de gebruiker gedefinieerde schema's, biedt inzichten via Azure Monitor-logboeken en verzendt optionele e-mails met behulp van [actiegroepen.](../azure-monitor/platform/action-groups.md) De oplossing ondersteunt zowel Azure Resource Manager als klassieke VM's voor de meeste scenario's. 

Deze oplossing biedt een gedecentraliseerde low-cost automatiseringsoptie voor gebruikers die hun VM-kosten willen optimaliseren. Met deze oplossing u:

- [Vm's plannen om te starten en te stoppen.](automation-solution-vm-management-config.md#schedule)
- Vm's plannen om te starten en te stoppen in oplopende volgorde [met Azure Tags](automation-solution-vm-management-config.md#tags) (niet ondersteund voor klassieke VM's).
- Autostop VM's op basis [van een laag CPU-gebruik.](automation-solution-vm-management-config.md#cpuutil)

> [!NOTE]
> De **VM's starten/stoppen tijdens off-hours-oplossing** is bijgewerkt om de nieuwste versies van de Beschikbare Azure-modules te ondersteunen.

De volgende beperkingen zijn beperkingen met de huidige oplossing:

- Het beheert VM's in elke regio, maar kan alleen worden gebruikt in hetzelfde abonnement als uw Azure Automation-account.
- Het is beschikbaar in Azure en Azure Government voor elke regio die een Log Analytics-werkruimte, een Azure Automation-account en waarschuwingen ondersteunt. Azure Government-regio's ondersteunen momenteel geen e-mailfunctionaliteit.

## <a name="solution-prerequisites"></a>Oplossingsvereisten

De runbooks voor deze oplossing werken met een [Azure Run As-account](automation-create-runas-account.md). Het Run As-account is de voorkeursverificatiemethode omdat deze certificaatverificatie gebruikt in plaats van een wachtwoord dat mogelijk verloopt of vaak wordt gewijzigd.

We raden u aan een apart Automatiseringsaccount te gebruiken voor de **Vm's start/stop tijdens off-hours** oplossing. Azure-moduleversies worden regelmatig bijgewerkt en de parameters kunnen worden gewijzigd. De oplossing wordt niet geüpgraded op dezelfde cadans en werkt mogelijk niet met nieuwere versies van de cmdlets die het gebruikt. U wordt aangeraden module-updates te testen in een testautomatiseringsaccount voordat u deze importeert in uw productieautomatiseringsaccount(en).

## <a name="solution-permissions"></a>Oplossingsmachtigingen

U moet over bepaalde machtigingen beschikken om de **VM's starten/stoppen te implementeren tijdens off-hours** oplossing. De machtigingen zijn anders als de oplossing een vooraf gemaakt automatiseringsaccount en loganalytics-werkruimte gebruikt op basis van de machtigingen die nodig zijn als de oplossing een nieuw account en werkruimte maakt tijdens de implementatie. 

U hoeft geen machtigingen te configureren als u een bijdrager aan het abonnement en een globale beheerder in uw Azure Active Directory-tenant bent. Als u deze rechten niet hebt of een aangepaste rol moet configureren, moet u ervoor zorgen dat u de onderstaande machtigingen hebt.

### <a name="permissions-for-pre-existing-automation-account-and-log-analytics-workspace"></a>Machtigingen voor reeds bestaande automatiseringsaccount en Logboekanalysewerkruimte

Als u de **vm's start/stop tijdens off-hours-oplossing** wilt implementeren in een bestaand automatiseringsaccount en de werkruimte Log Analytics, heeft de gebruiker die de oplossing implementeert de volgende machtigingen voor het bereik van de resourcegroep vereist. Zie [Aangepaste rollen voor Azure-resources voor](../role-based-access-control/custom-roles.md)meer informatie over rollen.

| Machtiging | Bereik|
| --- | --- |
| Microsoft.Automation/automationAccounts/gelezen | Resourcegroep |
| Microsoft.Automation/automationAccounts/variabelen/schrijven | Resourcegroep |
| Microsoft.Automation/automationAccounts/schema's/schrijven | Resourcegroep |
| Microsoft.Automation/automationAccounts/runbooks/write | Resourcegroep |
| Microsoft.Automation/automationAccounts/connections/write | Resourcegroep |
| Microsoft.Automation/automationAccounts/certificaten/schrijven | Resourcegroep |
| Microsoft.Automation/automationAccounts/modules/write | Resourcegroep |
| Microsoft.Automation/automationAccounts/modules/gelezen | Resourcegroep |
| Microsoft.automation/automationAccounts/jobSchedules/write | Resourcegroep |
| Microsoft.Automation/automationAccounts/jobs/write | Resourcegroep |
| Microsoft.Automation/automationAccounts/jobs/read | Resourcegroep |
| Microsoft.OperationsManagement/oplossingen/schrijven | Resourcegroep |
| Microsoft.OperationalInsights/workspaces/* | Resourcegroep |
| Microsoft.Insights/diagnosticSettings/write | Resourcegroep |
| Microsoft.Insights/actiegroepen/schrijven | Resourcegroep |
| Microsoft.Insights/Actiegroepen/lezen | Resourcegroep |
| Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Resourcegroep |
| Microsoft.Resources/implementaties/* | Resourcegroep |

### <a name="permissions-for-new-automation-account-and-new-log-analytics-workspace"></a>Machtigingen voor een nieuw Automatiseringsaccount en nieuwe werkruimte Log Analytics

U de **VM's start/stop tijdens off-hours-oplossing** implementeren in een nieuw Automatiseringsaccount en de werkruimte Log Analytics. In dit geval heeft de gebruiker die de oplossing implementeert de machtigingen nodig die zijn gedefinieerd in de vorige sectie en de machtigingen die in deze sectie zijn gedefinieerd. 

De gebruiker die de oplossing implementeert, heeft de volgende rollen nodig:

- Medebeheerder op abonnement. Deze rol is vereist om het Classic Run As-account aan te maken als u klassieke VM's gaat beheren. [Classic Run As accounts](automation-create-standalone-account.md#create-a-classic-run-as-account) worden niet langer standaard gemaakt.
- Lidmaatschap van de rol [Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md) Application Developer. Zie Machtigingen voor het configureren van [Run As-accounts voor](manage-runas-account.md#permissions)meer informatie over het configureren van Run As-accounts.
- Bijdrager aan het abonnement of de volgende machtigingen.

| Machtiging |Bereik|
| --- | --- |
| Microsoft.Authorization/Operations/lezen | Abonnement|
| Microsoft.Authorization/permissions/read |Abonnement|
| Microsoft.Authorization/roleAssignments/read | Abonnement |
| Microsoft.Authorization/roleAssignments/write | Abonnement |
| Microsoft.Authorization/roleAssignments/delete | Abonnement |
| Microsoft.Automation/automationAccounts/connections/read | Resourcegroep |
| Microsoft.Automation/automationAccounts/certificaten/lezen | Resourcegroep |
| Microsoft.Automation/automationAccounts/write | Resourcegroep |
| Microsoft.OperationalInsights/workspaces/write | Resourcegroep |

## <a name="solution-components"></a>Oplossingsonderdelen

De **VM's starten/stoppen tijdens off-hours-oplossing** bevat vooraf geconfigureerde runbooks, -planningen en -integratie met Azure Monitor-logboeken. U deze elementen gebruiken om het opstarten en afsluiten van uw VM's aan te passen aan uw bedrijfsbehoeften.

### <a name="runbooks"></a>Runbooks

In de volgende tabel worden de runbooks weergegeven die de oplossing implementeert in uw Automatiseringsaccount. Breng GEEN wijzigingen aan in de runbook-code. Schrijf in plaats daarvan je eigen runbook voor nieuwe functionaliteit.

> [!IMPORTANT]
> Voer geen runbook met **onderliggende** waarde op de naam.

Alle bovenliggende runbooks bevatten de `WhatIf` parameter. Wanneer de parameter is ingesteld op True, ondersteunt de parameter de details van het exacte gedrag dat de runbook neemt wanneer deze wordt uitgevoerd zonder de parameter en valideert hij dat de juiste VM's zijn gericht. Een runbook voert alleen de `WhatIf` gedefinieerde acties uit wanneer de parameter is ingesteld op False.

|Runbook | Parameters | Beschrijving|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> Waarschuwingsactie <br> WebHookURI (WebHookURI) | Gebeld vanuit het ouderloopboek. Met dit runbook worden waarschuwingen per resource voor het Scenario Automatisch stoppen aanmaken.|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf: Waar of Onwaar  | Hiermee worden Azure-waarschuwingsregels voor VM's gemaakt of bijgewerkt in de beoogde abonnements- of brongroepen. <br> `VMList`is een door komma's gescheiden lijst met VM's. Bijvoorbeeld `vm1, vm2, vm3`.<br> `WhatIf`hiermee validatie van runbook-logica mogelijk wordt zonder uit te voeren.|
|AutoStop_Disable | Geen | Hiermee schakelt u waarschuwingen voor automatisch stoppen en standaardplanning uit.|
|AutoStop_VM_Child | WebHookData | Gebeld vanuit het ouderloopboek. Waarschuwingsregels roepen dit runbook op om een klassieke VM te stoppen.|
|AutoStop_VM_Child_ARM | WebHookData |Gebeld vanuit het ouderloopboek. Waarschuwingsregels roepen dit runbook op om een VM te stoppen.  |
|ScheduledStartStop_Base_Classic | CloudServiceName<br> Actie: Starten of stoppen<br> VMList  | Hiermee voert u actiestart of -stop uit in de klassieke VM-groep by Cloud Services. |
|ScheduledStartStop_Child | VMName <br> Actie: Starten of stoppen <br> ResourceGroupName | Gebeld vanuit het ouderloopboek. Hiermee wordt een start- of stopactie uitgevoerd voor de geplande stop.|
|ScheduledStartStop_Child_Classic | VMName<br> Actie: Starten of stoppen<br> ResourceGroupName | Gebeld vanuit het ouderloopboek. Hiermee voert u een start- of stopactie uit voor de geplande stop voor klassieke VM's. |
|ScheduledStartStop_Parent | Actie: Starten of stoppen <br>VMList <br> WhatIf: Waar of Onwaar | Hiermee worden alle VM's in het abonnement gestart of gestopt. Bewerk de `External_Start_ResourceGroupNames` variabelen `External_Stop_ResourceGroupNames` en voer alleen uit op deze gerichte resourcegroepen. U ook specifieke VM's uitsluiten door de `External_ExcludeVMNames` variabele bij te werken.|
|SequencedStartStop_Parent | Actie: Starten of stoppen <br> WhatIf: Waar of Onwaar<br>VMList| Hiermee maakt u tags met de naam **sequencestart** en **sequencestop** op elke VM waarvoor u start-/stopactiviteit wilt invoegen. Deze tagnamen zijn hoofdlettergevoelig. De waarde van de tag moet een positief geheel getal (1, 2, 3) zijn dat overeenkomt met de volgorde waarin u wilt starten of stoppen. <br>**Opmerking**: VM's moeten zich `External_Start_ResourceGroupNames`binnen `External_Stop_ResourceGroupNames`resourcegroepen bevindt die zijn gedefinieerd in , en `External_ExcludeVMNames` variabelen. Ze moeten de juiste tags hebben om acties van kracht te laten worden.|

### <a name="variables"></a>Variabelen

In de volgende tabel worden de variabelen weergegeven die zijn gemaakt in uw automatiseringsaccount. Wijzig alleen variabelen die `External`vooraf zijn vastgezet met . Het wijzigen van variabelen `Internal` vooraf vastgesteld met ongewenste effecten veroorzaakt.

> [!NOTE]
> Beperkingen op vm-naam en resourcegroep zijn grotendeels het gevolg van variabele grootte. Zie [Variabele elementen in Azure Automation](https://docs.microsoft.com/azure/automation/shared-resources/variables).

|Variabele | Beschrijving|
|---------|------------|
|External_AutoStop_Condition | De voorwaardelijke operator die nodig is voor het configureren van de voorwaarde voordat een waarschuwing wordt geactiveerd. Aanvaardbare waarden `GreaterThan` `GreaterThanOrEqual`zijn `LessThan`, `LessThanOrEqual`, en .|
|External_AutoStop_Description | De waarschuwing om de VM te stoppen als het CPU-percentage de drempelwaarde overschrijdt.|
|External_AutoStop_Frequency | De evaluatiefrequentie voor regel. Deze parameter accepteert invoer in tijdspanneformaat. Mogelijke waarden zijn 5 minuten tot 6 uur. |
|External_AutoStop_MetricName | De naam van de prestatiestatistiek waarvoor de Azure Alert-regel moet worden geconfigureerd.|
|External_AutoStop_Severity | Ernst van de metrische waarschuwing, die kan variëren van 0 tot 4. |
|External_AutoStop_Threshold | De drempelwaarde voor de Azure `External_AutoStop_MetricName`Alert-regel die is opgegeven in de variabele . Percentagewaarden variëren van 1 tot 100.|
|External_AutoStop_TimeAggregationOperator | De tijdaggregatieoperator is toegepast op de geselecteerde venstergrootte om de voorwaarde te evalueren. Aanvaardbare waarden `Average` `Minimum`zijn `Maximum` `Total`, `Last`, , en .|
|External_AutoStop_TimeWindow | De grootte van het venster waarin Azure geselecteerde statistieken analyseert voor het activeren van een waarschuwing. Deze parameter accepteert invoer in tijdspanneformaat. Mogelijke waarden zijn 5 minuten tot 6 uur.|
|External_EnableClassicVMs| Waarde die aangeeft of klassieke VM's zijn gericht op de oplossing. De standaardwaarde is True. Stel deze variabele in op False voor CSP-abonnementen (Azure Cloud Solution Provider). Klassieke VM's vereisen een [Classic Run As-account.](automation-create-standalone-account.md#create-a-classic-run-as-account)|
|External_ExcludeVMNames | Door komma's gescheiden lijst met VM-namen die moeten worden uitgesloten, beperkt tot 140 VM's. Als u meer dan 140 VM's aan de lijst toevoegt, kunnen VM's die zijn ingesteld om te worden uitgesloten, per ongeluk worden gestart of gestopt.|
|External_Start_ResourceGroupNames | Door komma's gescheiden lijst van een of meer resourcegroepen die zijn getarget voor startacties.|
|External_Stop_ResourceGroupNames | Door komma's gescheiden lijst van een of meer resourcegroepen die zijn gericht op stopacties.|
|External_WaitTimeForVMRetrySeconds |De wachttijd in seconden voor de acties worden uitgevoerd op de VM's voor de **SequencedStartStop_Parent** runbook. Met deze variabele kan het runbook een bepaald aantal seconden op onderliggende bewerkingen wachten voordat deze verder gaat met de volgende actie. De maximale wachttijd is 10800, of drie uur. De standaardwaarde is 2100 seconden.|
|Internal_AutomationAccountName | Hiermee geeft u de naam op van het automatiseringsaccount.|
|Internal_AutoSnooze_ARM_WebhookURI | De webhook URI opgeroepen voor de AutoStop scenario voor VM's.|
|Internal_AutoSnooze_WebhookUri | De webhook URI opgeroepen voor de AutoStop scenario voor klassieke VM's.|
|Internal_AzureSubscriptionId | De Azure-abonnements-ID.|
|Internal_ResourceGroupName | De naam van de groep automatiseringsaccountbron.|

>[!NOTE]
>Voor de `External_WaitTimeForVMRetryInSeconds`variabele is de standaardwaarde bijgewerkt van 600 naar 2100. 

Voor alle scenario's `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames`zijn `External_ExcludeVMNames` de variabelen en zijn ze nodig voor het targeten van VM's, met uitzondering van de door komma's gescheiden VM-lijsten voor de **AutoStop_CreateAlert_Parent,** **SequencedStartStop_Parent**en **ScheduledStartStop_Parent** runbooks. Dat wil zeggen dat uw VM's behoren tot doelgroepen voor het starten en stoppen van acties. De logica werkt vergelijkbaar met azure-beleid, omdat u de abonnements- of resourcegroep targeten en acties laten overnemen door nieuw gemaakte VM's. Deze aanpak voorkomt dat u voor elke vm een apart schema moet bijhouden en start- en stops in schaal moet beheren.

### <a name="schedules"></a>Planningen

In de volgende tabel worden alle standaardschema's weergegeven die zijn gemaakt in uw automatiseringsaccount.U ze wijzigen of uw eigen aangepaste schema's maken.Standaard zijn alle schema's uitgeschakeld, behalve voor de **Scheduled_StartVM** en **Scheduled_StopVM** schema's.

Schakel niet alle schema's in, omdat dit overlappende planningsacties kan maken. Het is het beste om te bepalen welke optimalisaties u wilt doen en deze dienovereenkomstig aan te passen. Zie de voorbeeldscenario's in het overzichtsgedeelte voor verdere uitleg.

|Naam plannen | Frequency | Beschrijving|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | Om de 8 uur | Hiermee wordt de **AutoStop_CreateAlert_Parent** runbook elke 8 uur uitgevoerd, `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames`waardoor `External_ExcludeVMNames` de op VM gebaseerde waarden in , en variabelen worden gestopt. U ook een door komma's gescheiden lijst `VMList` met VM's opgeven met behulp van de parameter.|
|Scheduled_StopVM | Door de gebruiker gedefinieerd, dagelijks | Hiermee **ScheduledStopStart_Parent** voert u het ScheduledStopStart_Parent `Stop` runbook uit met een parameter van elke dag op het opgegeven tijdstip.Hiermee worden alle VM's die voldoen aan de regels die zijn gedefinieerd door variabele activa automatisch gestopt.Schakel het bijbehorende schema **Scheduled-StartVM in**.|
|Scheduled_StartVM | Door de gebruiker gedefinieerd, dagelijks | Hiermee wordt het **ScheduledStopStart_Parent** runbook `Start` uitgevoerd met een parameterwaarde van elke dag op het opgegeven tijdstip. Hiermee worden alle VM's die voldoen aan de regels die zijn gedefinieerd door variabele activa automatisch gestart.Schakel het bijbehorende schema **Scheduled-StopVM in**.|
|Gesequenced-StopVM | 1:00 AM (UTC), elke vrijdag | Hiermee wordt het **Sequenced_StopStop_Parent** runbook `Stop` uitgevoerd met een parameterwaarde van elke vrijdag op het opgegeven tijdstip.Sequentiële (oplopende) stopt alle VM's met een tag van **SequenceStop** gedefinieerd door de juiste variabelen. Zie [Runbooks voor](#runbooks)meer informatie over tagwaarden en activavariabelen.Schakel het bijbehorende schema, **Sequenced-StartVM**in .|
|Gesequenced-StartVM | 13:00 uur (UTC), elke maandag | Hiermee voert u het **SequencedStopStart_Parent** `Start` runbook uit met een parameterwaarde van elke maandag op het opgegeven tijdstip. Achtereenvolgens (aflopend) begint alle VM's met een tag van **SequenceStart** gedefinieerd door de juiste variabelen. Zie [Runbooks voor](#runbooks)meer informatie over tagwaarden en variabele elementen. Schakel het bijbehorende schema, **Sequenced-StopVM**in .

## <a name="use-of-the-solution-with-classic-vms"></a>Gebruik van de oplossing met klassieke VM's

Als u de **Start/stop VM's gebruikt tijdens off-hours** oplossing voor klassieke VM's, verwerkt Automation al uw VM's sequentieel per cloudservice. VM's worden nog steeds parallel verwerkt in verschillende cloudservices. 

Voor het gebruik van de oplossing met klassieke VM's hebt u een Classic Run As-account nodig, dat niet standaard is gemaakt. Zie [Een Klassiek run as-account maken](automation-create-standalone-account.md#create-a-classic-run-as-account)voor instructies voor het maken van een Classic Run As-account .

Als u meer dan 20 VM's per cloudservice hebt, volgen enkele aanbevelingen:

* Maak meerdere schema's met de bovenliggende runbook **ScheduledStartStop_Parent** en het opgeven van 20 VM's per schema. 
* Gebruik de `VMList` parameter in de planningseigenschappen om VM-namen op te geven als een lijst met door komma's gescheiden lijst. 

Als de automatiseringstaak voor deze oplossing anders meer dan drie uur wordt uitgevoerd, wordt deze tijdelijk gelost of gestopt volgens de [limiet voor het eerlijke aandeel.](automation-runbook-execution.md#fair-share)

Azure CSP-abonnementen ondersteunen alleen het Azure Resource Manager-model. Niet-Azure Resource Manager-services zijn niet beschikbaar in het programma. Wanneer de **VM's starten/stoppen tijdens off-hours-oplossing** wordt uitgevoerd, u fouten ontvangen omdat deze cmdlets heeft om klassieke resources te beheren. Zie Beschikbare services in [CSP-abonnementen](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services)voor meer informatie over CSP. Als u een CSP-abonnement gebruikt, moet u de [External_EnableClassicVMs](#variables) variabele na implementatie instellen op False.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-the-solution"></a>De oplossing inschakelen

Als u de oplossing wilt gebruiken, voert u de stappen uit in [de OPLOSSING Start-/stop-VM's inschakelen.](automation-solution-vm-management-enable.md)

## <a name="view-the-solution"></a>De oplossing bekijken

Gebruik een van de volgende mechanismen om toegang te krijgen tot de oplossing nadat u deze hebt ingeschakeld:

* Selecteer **vm starten/stoppen** in uw automatiseringsaccount onder **Gerelateerde resources**. Selecteer op de pagina VM starten/stoppen **de optie De oplossing beheren** aan de rechterkant van de pagina onder **VM-oplossingen voor begin/einde beheren**.

* Navigeer naar de werkruimte Log Analytics die is gekoppeld aan uw Automatiseringsaccount. Nadat u de werkruimte hebt geselecteerd, kiest u **Oplossingen** in het linkerdeelvenster. Selecteer op de pagina Oplossingen de **oplossing Start-Stop-VM[workspace]** in de lijst.  

Als u de oplossing selecteert, wordt de oplossingspagina **Start-Stop-VM[workspace]** weergegeven. Hier u belangrijke details bekijken, zoals de informatie in de **Tegel StartStopVM.** Net als in uw werkruimte Log Analytics wordt in deze tegel een telling en een grafische weergave weergegeven van de runbook-taken voor de oplossing die zijn gestart en met succes zijn voltooid.

![Pagina automatiseringsupdatebeheeroplossing](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

U de taakrecords verder analyseren door op de donuttegel te klikken. Het oplossingsdashboard toont taakgeschiedenis en vooraf gedefinieerde query's voor logboekquery's. Schakel over naar de geavanceerde portal voor logboekanalyse om te zoeken op basis van uw zoekopdrachten.

## <a name="update-the-solution"></a>De oplossing bijwerken

Als u een eerdere versie van deze oplossing hebt geïmplementeerd, verwijdert u deze uit uw account voordat u een bijgewerkte versie implementeert. Volg de stappen om [de oplossing](#remove-the-solution) te verwijderen en volg vervolgens de stappen om de oplossing te [implementeren.](automation-solution-vm-management-enable.md)

## <a name="remove-the-solution"></a>De oplossing verwijderen

Als u de oplossing niet meer hoeft te gebruiken, u deze verwijderen uit het automatiseringsaccount. Als u de oplossing verwijdert, worden alleen de runbooks verwijderd. De planningen of variabelen die zijn gemaakt toen de oplossing werd toegevoegd, worden niet verwijderd. Verwijder deze elementen handmatig als u ze niet gebruikt met andere runbooks.

Ga als u de oplossing verwijderen:

1. Selecteer **Gekoppelde werkruimte** onder **Gerelateerde resources**in uw automatiseringsaccount.

2. Selecteer **Ga naar werkruimte**.

3. Klik op **Oplossingen** onder **Algemeen**. 

4. Selecteer op de pagina Oplossingen de oplossing **Start-Stop-VM[Workspace]**. 

5. Selecteer **verwijderen** in het menu op de pagina **VMManagementSolution[Workspace].**<br><br> ![VM Mgmt-oplossing verwijderen](media/automation-solution-vm-management/vm-management-solution-delete.png)

6. Controleer **in** het venster Oplossing verwijderen of u de oplossing wilt verwijderen.

7. Terwijl de informatie is geverifieerd en de oplossing wordt verwijderd, u de voortgang bijhouden onder **Meldingen**, gekozen in het menu. U wordt na het proces teruggestuurd naar de pagina Oplossingen om de oplossing te verwijderen.

De werkruimte Automatiseringsaccount en Log Analytics worden niet verwijderd als onderdeel van dit proces. Als u de werkruimte Log Analytics niet wilt behouden, moet u deze handmatig verwijderen uit de Azure-portal:

1. Zoeken naar en selecteer **Logboekanalyse-werkruimten**.

2. Selecteer op de werkruimtepagina Log Analytics de werkruimte.

3. Selecteer **Verwijderen** in het menu op de pagina werkruimte-instellingen.

4. Als u de [onderdelen](#solution-components)van de Azure Automation-accountoplossing niet wilt behouden, u ze handmatig verwijderen.

## <a name="next-steps"></a>Volgende stappen

[Schakel](automation-solution-vm-management-enable.md) de **VM's start/stop in tijdens off-hours** oplossing voor uw Azure VM's.
