---
title: Vm's starten/stoppen tijdens off-hours oplossing
description: Deze VM-beheeroplossing start en stopt uw virtuele Azure-machines volgens schema en bewaakt proactief vanuit Azure Monitor-logboeken.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 906c7728365cc902549bd46c57972e1c90af979c
ms.sourcegitcommit: 515482c6348d5bef78bb5def9b71c01bb469ed80
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80607478"
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>Oplossing voor VM's buiten bedrijfsuren starten/stoppen in Azure Automation

De VM's starten/stoppen tijdens het buitenkantoor van de oplossing starten en stoppen uw Virtuele Azure-machines op door de gebruiker gedefinieerde schema's, bieden inzichten via Azure Monitor-logboeken en verzendt optionele e-mails met behulp van [actiegroepen.](../azure-monitor/platform/action-groups.md) Het ondersteunt zowel Azure Resource Manager als klassieke VM's voor de meeste scenario's. 

Als u deze oplossing met klassieke VM's wilt gebruiken, hebt u een Classic RunAs-account nodig, dat niet standaard is gemaakt. Zie [Klassieke runas-accounts](automation-create-standalone-account.md#classic-run-as-accounts)voor instructies over het maken van een Classic RunAs-account.

> [!NOTE]
> De vm's voor starten/stoppen tijdens off-hours-oplossing zijn bijgewerkt om de nieuwste versies van de Beschikbare Azure-modules te ondersteunen.

Deze oplossing biedt een gedecentraliseerde low-cost automatiseringsoptie voor gebruikers die hun VM-kosten willen optimaliseren. Met deze oplossing u:

- [Vm's plannen om te starten en te stoppen.](automation-solution-vm-management-config.md#schedule)
- Vm's plannen om te starten en te stoppen in oplopende volgorde [met Azure Tags](automation-solution-vm-management-config.md#tags) (niet ondersteund voor klassieke VM's).
- Autostop VM's op basis [van een laag CPU-gebruik.](automation-solution-vm-management-config.md#cpuutil)

De volgende beperkingen zijn beperkingen met de huidige oplossing:

- Deze oplossing beheert VM's in elke regio, maar kan alleen worden gebruikt in hetzelfde abonnement als uw Azure Automation-account.
- Deze oplossing is beschikbaar in Azure en Azure Government voor elke regio die een Log Analytics-werkruimte, een Azure Automation-account en waarschuwingen ondersteunt. Azure Government-regio's ondersteunen momenteel geen e-mailfunctionaliteit.

> [!NOTE]
> Als u de oplossing voor klassieke VM's gebruikt, worden al uw VM's achtereenvolgens per cloudservice verwerkt. Virtuele machines worden nog steeds parallel verwerkt in verschillende cloudservices. Als u meer dan 20 VM's per cloudservice hebt, raden we u aan meerdere schema's te maken met de bovenliggende runbook **ScheduledStartStop_Parent** en 20 VM's per planning op te geven. Geef in de eigenschappen van de planning op als een lijst met door komma's gescheiden lijst, VM-namen in de parameter **VMList.** Als de automatiseringstaak voor deze oplossing anders meer dan drie uur wordt uitgevoerd, wordt deze tijdelijk gelost of gestopt volgens de [limiet voor het eerlijke aandeel.](automation-runbook-execution.md#fair-share)
>
> Azure Cloud Solution Provider (Azure CSP)-abonnementen ondersteunen alleen het Azure Resource Manager-model, niet-Azure Resource Manager-services zijn niet beschikbaar in het programma. Wanneer de Start/Stop-oplossing wordt uitgevoerd, u fouten ontvangen omdat deze cmdlets heeft om klassieke resources te beheren. Zie Beschikbare services in [CSP-abonnementen](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services)voor meer informatie over CSP. Als u een CSP-abonnement gebruikt, moet u de [**External_EnableClassicVMs**](#variables) variabele wijzigen in **False** na implementatie.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Vereisten

De runbooks voor deze oplossing werken met een [Azure Run As-account](automation-create-runas-account.md). Het Run As-account is de voorkeursverificatiemethode, omdat het certificaatverificatie gebruikt in plaats van een wachtwoord dat mogelijk verloopt of vaak wordt gewijzigd.

We raden u aan een apart automatiseringsaccount te gebruiken voor de VM-oplossing start/stop. Dit komt omdat Azure-moduleversies vaak worden bijgewerkt en de parameters ervan kunnen veranderen. De Start/Stop VM-oplossing wordt niet op dezelfde cadans geüpgraded, zodat deze mogelijk niet werkt met nieuwere versies van de cmdlets die worden gebruikt. We raden u ook aan module-updates te testen in een automatiseringsaccount voor tests voordat u deze importeert in uw productieautomatiseringsaccount(s).

### <a name="permissions"></a>Machtigingen

Er zijn bepaalde machtigingen die een gebruiker moet hebben om de VM's starten/stoppen te implementeren tijdens off hours-oplossing. Deze machtigingen zijn anders als u een vooraf gemaakte werkruimte voor automatiseringsaccount en logboekanalyse gebruikt of nieuwe machtigingen maakt tijdens de implementatie. Als u een bijdrager bent op het abonnement en een globale beheerder in uw Azure Active Directory-tenant, hoeft u de volgende machtigingen niet te configureren. Als u deze rechten niet hebt of een aangepaste rol moet configureren, raadpleegt u de onderstaande machtigingen.

#### <a name="pre-existing-automation-account-and-log-analytics-workspace"></a>Reeds bestaande werkruimte automatiseringsaccount en logboekanalyse

Als u de begin-/stop-VM's tijdens de oplossing voor buitenkantoor wilt implementeren in een bestaande werkruimte voor automatiseringsaccounts en Logboekanalyse, heeft de gebruiker die de oplossing implementeert de volgende machtigingen voor de **resourcegroep**nodig. Zie [Aangepaste rollen voor Azure-resources voor](../role-based-access-control/custom-roles.md)meer informatie over rollen.

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

#### <a name="new-automation-account-and-a-new-log-analytics-workspace"></a>Nieuw automatiseringsaccount en een nieuwe Log Analytics-werkruimte

Als u de begin-/stop-VM's tijdens de oplossing voor buitenkantoor wilt implementeren in een nieuwe werkruimte voor automatiseringsaccount en Logboekanalyse, heeft de gebruiker die de oplossing implementeert, de machtigingen nodig die in de vorige sectie zijn gedefinieerd, evenals de volgende machtigingen:

- Mede-beheerder op abonnement - Het is alleen nodig om de Classic Run As-account te maken als u klassieke VM's gaat beheren. [Klassieke RunAs-accounts](automation-create-standalone-account.md#classic-run-as-accounts) worden niet langer standaard gemaakt.
- Een lid van de rol [Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md) Application **Developer.** Zie Machtigingen voor het configureren van [Run As-accounts voor](manage-runas-account.md#permissions)meer informatie over het configureren van Run As-accounts.
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

Deze oplossing omvat vooraf geconfigureerde runbooks, planningen en integratie met Azure Monitor-logboeken, zodat u het opstarten en afsluiten van uw virtuele machines afstemmen op uw bedrijfsbehoeften.

### <a name="runbooks"></a>Runbooks

In de volgende tabel worden de runbooks weergegeven die door deze oplossing naar uw Automatiseringsaccount zijn geïmplementeerd. Breng geen wijzigingen aan in de runbook-code. Schrijf in plaats daarvan je eigen runbook voor nieuwe functionaliteit.

> [!IMPORTANT]
> Voer geen runbook met *onderliggende* waarde op de naam.

Alle bovenliggende runbooks bevatten de parameter _WhatIf._ Wanneer ingesteld op **True**, _WhatIf_ ondersteunt detaillering van het exacte gedrag van de runbook neemt wanneer uitgevoerd zonder de _parameter WhatIf_ en valideert de juiste VM's worden gericht. Een runbook voert alleen de gedefinieerde acties uit wanneer de parameter _WhatIf_ is ingesteld op **False**.

|Runbook | Parameters | Beschrijving|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> Waarschuwingsactie <br> WebHookURI (WebHookURI) | Gebeld vanuit het ouderloopboek. Met dit runbook worden waarschuwingen per resource voor het AutoStop-scenario aangenomen.|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf: Waar of Onwaar  | Hiermee worden Azure-waarschuwingsregels voor VM's gemaakt of bijgewerkt in de beoogde abonnements- of brongroepen. <br> VMList: Door komma's gescheiden lijst met VM's. Bijvoorbeeld _vm1, vm2, vm3_.<br> *WhatIf* valideert de runbook-logica zonder uit te voeren.|
|AutoStop_Disable | geen | Hiermee schakelt u Waarschuwingen voor Automatisch stoppen en standaardplanning uit.|
|AutoStop_VM_Child | WebHookData | Gebeld vanuit het ouderloopboek. Waarschuwingsregels roepen dit runbook op om de klassieke vm te stoppen.|
|AutoStop_VM_Child_ARM | WebHookData |Gebeld vanuit het ouderloopboek. Waarschuwingsregels roepen dit runbook op om de VM te stoppen.  |
|ScheduledStartStop_Base_Classic | CloudServiceName<br> Actie: Starten of stoppen<br> VMList  | Deze runbook wordt gebruikt om actiestart of -stop uit te voeren in de klassieke VM-groep van Cloud Services.<br> VMList: Door komma's gescheiden lijst met VM's. Bijvoorbeeld _vm1, vm2, vm3_. |
|ScheduledStartStop_Child | VMName <br> Actie: Starten of stoppen <br> ResourceGroupName | Gebeld vanuit het ouderloopboek. Hiermee wordt een start- of stopactie uitgevoerd voor de geplande stop.|
|ScheduledStartStop_Child_Classic | VMName<br> Actie: Starten of stoppen<br> ResourceGroupName | Gebeld vanuit het ouderloopboek. Hiermee voert u een start- of stopactie uit voor de geplande stop voor klassieke VM's. |
|ScheduledStartStop_Parent | Actie: Starten of stoppen <br>VMList <br> WhatIf: Waar of Onwaar | Deze instelling is van invloed op alle VM's in het abonnement. Bewerk de **External_Start_ResourceGroupNames** en **External_Stop_ResourceGroupNames** alleen uit te voeren op deze gerichte resourcegroepen. U ook specifieke VM's uitsluiten door de **External_ExcludeVMNames** variabele bij te werken.<br> VMList: Door komma's gescheiden lijst met VM's. Bijvoorbeeld _vm1, vm2, vm3_.<br> _WhatIf_ valideert de runbook-logica zonder uit te voeren.|
|SequencedStartStop_Parent | Actie: Starten of stoppen <br> WhatIf: Waar of Onwaar<br>VMList| Maak tags met de naam **sequencestart** en **sequencestop** op elke VM waarvoor u start-/stopactiviteit wilt invoegen. Deze tagnamen zijn hoofdlettergevoelig. De waarde van de tag moet een positief geheel getal (1, 2, 3) zijn dat overeenkomt met de volgorde waarin u wilt starten of stoppen. <br> VMList: Door komma's gescheiden lijst met VM's. Bijvoorbeeld _vm1, vm2, vm3_. <br> _WhatIf_ valideert de runbook-logica zonder uit te voeren. <br> **Opmerking:** VM's moeten zich binnen resourcegroepen bevinden die zijn gedefinieerd als External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames en External_ExcludeVMNames in Azure Automation-variabelen. Ze moeten de juiste tags hebben om acties van kracht te laten worden.|

### <a name="variables"></a>Variabelen

In de volgende tabel worden de variabelen weergegeven die zijn gemaakt in uw automatiseringsaccount. Wijzig alleen variabelen die vooraf zijn gekoppeld aan **Extern**. Het wijzigen van variabelen vooraf vastgesteld met **interne** oorzaken ongewenste effecten.

|Variabele | Beschrijving|
|---------|------------|
|External_AutoStop_Condition | De voorwaardelijke operator die nodig is voor het configureren van de voorwaarde voordat een waarschuwing wordt geactiveerd. Aanvaardbare waarden zijn **groter dan**, **groter thanorequal**, **minder dan**, en **lessthanorequal**.|
|External_AutoStop_Description | De waarschuwing om de VM te stoppen als het CPU-percentage de drempelwaarde overschrijdt.|
|External_AutoStop_Frequency | De evaluatiefrequentie voor regel. Deze parameter accepteert invoer in tijdspanneformaat. Mogelijke waarden zijn van 5 minuten tot 6 uur. |
|External_AutoStop_MetricName | De naam van de prestatiestatistiek waarvoor de Azure Alert-regel moet worden geconfigureerd.|
|External_AutoStop_Severity | Ernst van de metrische waarschuwing, die kan variëren van 0 tot 4. |
|External_AutoStop_Threshold | De drempelwaarde voor de Azure Alert-regel die is opgegeven in de variabele _External_AutoStop_MetricName_. Percentagewaarden kunnen variëren van 1 tot 100.|
|External_AutoStop_TimeAggregationOperator | De tijdaggregatieoperator, die wordt toegepast op de geselecteerde venstergrootte om de voorwaarde te evalueren. Acceptabele waarden zijn **Gemiddeld**, **Minimum**, **Maximum**, **Totaal**en **Laatste**.|
|External_AutoStop_TimeWindow | De venstergrootte waarin Azure geselecteerde statistieken analyseert voor het activeren van een waarschuwing. Deze parameter accepteert invoer in tijdspanneformaat. Mogelijke waarden zijn 5 minuten tot 6 uur.|
|External_EnableClassicVMs| Hiermee geeft u op of klassieke VM's zijn gericht op de oplossing. De standaardwaarde is True. Dit moet worden ingesteld op False voor CSP-abonnementen. Klassieke VM's vereisen een [Classic Run-As-account.](automation-create-standalone-account.md#classic-run-as-accounts)|
|External_ExcludeVMNames | Voer VM-namen in die moeten worden uitgesloten en waarbij namen worden gescheiden met behulp van een komma zonder spaties. Dit is beperkt tot 140 VM's. Als u meer dan 140 VM's toevoegt aan deze door komma's gescheiden lijst, kunnen VM's die zijn ingesteld om te worden uitgesloten, per ongeluk worden gestart of gestopt.|
|External_Start_ResourceGroupNames | Hiermee geeft u een of meer resourcegroepen op, waardoor waarden worden gescheiden met behulp van een komma die is getarget voor startacties.|
|External_Stop_ResourceGroupNames | Hiermee geeft u een of meer resourcegroepen op, waardoor waarden worden gescheiden met behulp van een komma die is getarget voor stopacties.|
|External_WaitTimeForVMRetrySeconds |De wachttijd in seconden voor de acties die moeten worden uitgevoerd op de VM's voor de gesequenced start/stop-runbook.<br> De standaardwaarde bedraagt 2100 seconden en ondersteunt configureren tot een maximale waarde van 10800 of drie uur.|
|Internal_AutomationAccountName | Hiermee geeft u de naam op van het automatiseringsaccount.|
|Internal_AutoSnooze_ARM_WebhookURI | Hiermee geeft u Webhook URI op die is ingeschakeld voor het AutoStop-scenario voor VM's.|
|Internal_AutoSnooze_WebhookUri | Hiermee geeft u Webhook URI op die is ingeschakeld voor het AutoStop-scenario voor klassieke VM's.|
|Internal_AzureSubscriptionId | Hiermee geeft u de Azure-abonnements-id op.|
|Internal_ResourceGroupName | Hiermee geeft u de naam van de groep automatiseringsaccountbron op.|

>[!NOTE]
>Voor de variabele **External_WaitTimeForVMRetryInSeconds**is de standaardwaarde bijgewerkt van 600 naar 2100. Met deze variabele kan de **runbook Voor het start/stop-scenario** van de reeks reeks seconden op de onderliggende bewerkingen wachten voordat deze verder gaat met de volgende actie.
>

In alle scenario's zijn de **External_Start_ResourceGroupNames,** **External_Stop_ResourceGroupNames**en **External_ExcludeVMNames** variabelen nodig voor het targeten van VM's, met uitzondering van het verstrekken van een door komma's gescheiden lijst met VM's voor de **AutoStop_CreateAlert_Parent,** **SequencedStartStop_Parent**en **ScheduledStartStop_Parent** runbooks. Dat wil zeggen dat uw VM's zich in doelbrongroepen moeten bevinden om acties te starten en te stoppen. De logica werkt vergelijkbaar met azure-beleid, omdat u de abonnements- of resourcegroep targeten en acties laten overnemen door nieuw gemaakte VM's. Deze aanpak voorkomt dat u voor elke vm een apart schema moet bijhouden en start- en stops in schaal moet beheren.

### <a name="schedules"></a>Planningen

In de volgende tabel worden alle standaardschema's weergegeven die zijn gemaakt in uw automatiseringsaccount.U ze wijzigen of uw eigen aangepaste schema's maken.Standaard zijn alle schema's uitgeschakeld, behalve **voor Scheduled_StartVM** en **Scheduled_StopVM**.

U moet niet alle schema's inschakelen, omdat hierdoor mogelijk overlappende planningsacties kunnen worden gemaakt. Het is het beste om te bepalen welke optimalisaties u wilt uitvoeren en dienovereenkomstig aan te passen. Zie de voorbeeldscenario's in het overzichtsgedeelte voor verdere uitleg.

|Naam plannen | Frequency | Beschrijving|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | Om de 8 uur | Hiermee wordt de AutoStop_CreateAlert_Parent runbook elke 8 uur uitgevoerd, waardoor de vm-gebaseerde waarden in External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames en External_ExcludeVMNames in Azure Automation-variabelen worden gestopt. U ook een door komma's gescheiden lijst met VM's opgeven met de parameter VMList.|
|Scheduled_StopVM | Gebruiker gedefinieerd, dagelijks | Hiermee voert u het Scheduled_Parent runbook uit met elke dag een parameter _Stoppen_ op het opgegeven tijdstip.Hiermee worden automatisch alle VM's gestopt die voldoen aan de regels die zijn gedefinieerd door activavariabelen.Schakel het bijbehorende schema, **Scheduled-StartVM**in .|
|Scheduled_StartVM | Gebruiker gedefinieerd, dagelijks | Hiermee voert u het Scheduled_Parent runbook uit met elke dag een parameter start van _start_ op het opgegeven tijdstip. Hiermee worden automatisch alle VM's gestart die voldoen aan de regels die zijn gedefinieerd door de juiste variabelen.Schakel het bijbehorende schema, **Scheduled-StopVM**in .|
|Gesequenced-StopVM | 1:00 AM (UTC), elke vrijdag | Hiermee voert u het Sequenced_Parent runbook uit met elke vrijdag een parameter _Stoppen_ op het opgegeven tijdstip.Sequentiële (oplopende) stopt alle VM's met een tag van **SequenceStop** gedefinieerd door de juiste variabelen. Zie de sectie Runbooks voor meer informatie over tagwaarden en activavariabelen.Schakel het bijbehorende schema, **Sequenced-StartVM**in .|
|Gesequenced-StartVM | 13:00 uur (UTC), elke maandag | Hiermee wordt het Sequenced_Parent runbook uitgevoerd met een parameter _van Start_ elke maandag op het opgegeven tijdstip. Achtereenvolgens (aflopend) begint alle VM's met een tag van **SequenceStart** gedefinieerd door de juiste variabelen. Zie de sectie Runbooks voor meer informatie over tagwaarden en activavariabelen. Schakel het bijbehorende schema, **Sequenced-StopVM**in .|

## <a name="enable-the-solution"></a>De oplossing inschakelen

Als u de oplossing wilt gebruiken, voert u de stappen uit in de [oplossing Begin-/Stop-VM's inschakelen.](automation-solution-vm-management-enable.md)

## <a name="viewing-the-solution"></a>De oplossing bekijken

U hebt toegang tot de oplossing nadat u deze op een van de volgende manieren hebt ingeschakeld:

* Selecteer **vm starten/stoppen** in uw automatiseringsaccount onder **Gerelateerde resources**. Selecteer op de pagina **VM starten/stoppen** **de optie De oplossing beheren** aan de rechterkant van de pagina onder de sectie **Vm-oplossingen voor begin/einde beheren**.

* Navigeer naar de werkruimte Log Analytics die is gekoppeld aan uw automatiseringsaccount en selecteer **oplossingen** in het linkerdeelvenster nadat u de werkruimte hebt geselecteerd. Selecteer **op** de pagina Oplossingen de **oplossing Start-Stop-VM[workspace]** in de lijst.  

Als u de oplossing selecteert, wordt de oplossingspagina **Start-Stop-VM[workspace]** weergegeven. Hier u belangrijke details bekijken, zoals de **StartStopVM-tegel.** Net als in uw werkruimte Log Analytics wordt in deze tegel een telling en een grafische weergave weergegeven van de runbook-taken voor de oplossing die zijn gestart en met succes zijn voltooid.

![Pagina automatiseringsupdatebeheeroplossing](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

Vanaf hier u de taakrecords verder analyseren door op de donuttegel te klikken. Het oplossingsdashboard toont taakgeschiedenis en vooraf gedefinieerde logboekzoekopdrachten. Schakel over naar de geavanceerde portal voor logboekanalyse om te zoeken op basis van uw zoekopdrachten.

## <a name="update-the-solution"></a>De oplossing bijwerken

Als u een eerdere versie van deze oplossing hebt geïmplementeerd, moet u deze eerst uit uw account verwijderen voordat u een bijgewerkte versie implementeert. Volg de stappen om [de oplossing](#remove-the-solution) te verwijderen en volg vervolgens de stappen om de oplossing te [implementeren.](automation-solution-vm-management-enable.md)

## <a name="remove-the-solution"></a>De oplossing verwijderen

Als u besluit dat u de oplossing niet meer hoeft te gebruiken, u deze verwijderen uit het automatiseringsaccount. Als u de oplossing verwijdert, worden alleen de runbooks verwijderd. De planningen of variabelen die zijn gemaakt toen de oplossing werd toegevoegd, worden niet verwijderd. Deze elementen moet u handmatig verwijderen als u ze niet gebruikt met andere runbooks.

Voer de volgende stappen uit om de oplossing te verwijderen:

1. Selecteer **Gekoppelde werkruimte**in uw Automatiseringsaccount onder **Gerelateerde resources**.

2. Selecteer **Ga naar werkruimte**.

3. Selecteer **Onder Algemeen**de optie **Oplossingen**. 

4. Selecteer **op** de pagina Oplossingen de oplossing **Start-Stop-VM[Workspace]**. Selecteer op de pagina **VMManagementSolution[Workspace]** in het menu de optie **Verwijderen**.<br><br> ![VM Mgmt-oplossing verwijderen](media/automation-solution-vm-management/vm-management-solution-delete.png)

5. Controleer **in** het venster Oplossing verwijderen of u de oplossing wilt verwijderen.

6. Terwijl de informatie is geverifieerd en de oplossing wordt verwijderd, u de voortgang ervan bijhouden onder **Meldingen** in het menu. U wordt na het proces teruggestuurd naar de pagina **Oplossingen** om de oplossing te verwijderen.

De werkruimte Automatiseringsaccount en Log Analytics worden niet verwijderd als onderdeel van dit proces. Als u de werkruimte Log Analytics niet wilt behouden, moet u deze handmatig verwijderen. Dit kan worden bereikt vanuit de Azure-portal:

1. Zoek en selecteer in Azure-portal **log Analytics-werkruimten**.

2. Selecteer op de pagina **Logboekanalyse-werkruimten** de werkruimte.

3. Selecteer **Verwijderen** in het menu op de pagina werkruimte-instellingen.

Als u de onderdelen van het Azure Automation-account niet wilt behouden, u ze handmatig verwijderen. Zie de [componenten oplossing](#solution-components)voor de lijst met runbooks, variabelen en schema's die door de oplossing zijn gemaakt.

## <a name="next-steps"></a>Volgende stappen

[Schakel](automation-solution-vm-management-enable.md) de oplossing Start/Stop in tijdens off-hours-oplossing voor uw Azure VM's.
