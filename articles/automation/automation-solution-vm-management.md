---
title: Vm's starten/stoppen tijdens off-hours oplossing
description: Deze VM-beheeroplossing start en stopt uw virtuele Azure Resource Manager-machines in een planning en bewaakt proactief vanuit Azure Monitor-logboeken.
services: automation
ms.subservice: process-automation
ms.date: 02/25/2020
ms.topic: conceptual
ms.openlocfilehash: cbf181b9a6d3860854c7b61cca0e6c50810cced9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278543"
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>Oplossing voor VM's buiten bedrijfsuren starten/stoppen in Azure Automation

De VM's starten/stoppen tijdens het buitenkantoor van de oplossing starten en stoppen uw Virtuele Azure-machines op door de gebruiker gedefinieerde schema's, bieden inzichten via Azure Monitor-logboeken en verzendt optionele e-mails met behulp van [actiegroepen.](../azure-monitor/platform/action-groups.md) Het ondersteunt zowel Azure Resource Manager als klassieke VM's voor de meeste scenario's. Als u deze oplossing met klassieke VM's wilt gebruiken, hebt u een Classic RunAs-account nodig, dat niet standaard is gemaakt. Zie [Klassieke runas-accounts](automation-create-standalone-account.md#classic-run-as-accounts)voor instructies over het maken van een Classic RunAs-account.

> [!NOTE]
> De start/stop VM's tijdens off-hours oplossing is getest met de Azure-modules die worden geïmporteerd in uw automatiseringsaccount wanneer u de oplossing implementeert. De oplossing werkt momenteel niet met nieuwere versies van de Azure-module. Dit heeft alleen invloed op het automatiseringsaccount dat u gebruikt om de VM's starten/stoppen uit te voeren tijdens off-hours oplossing. U nog steeds nieuwere versies van de Azure-module gebruiken in uw andere automatiseringsaccounts, zoals beschreven in [Hoe Azure PowerShell-modules bijwerken in Azure Automation](automation-update-azure-modules.md)

Deze oplossing biedt een gedecentraliseerde low-cost automatiseringsoptie voor gebruikers die hun VM-kosten willen optimaliseren. Met deze oplossing u:

- Vm's plannen om te starten en te stoppen.
- Vm's plannen om te starten en te stoppen in oplopende volgorde met Azure Tags (niet ondersteund voor klassieke VM's).
- Autostop VM's op basis van een laag CPU-gebruik.

De volgende beperkingen zijn beperkingen met de huidige oplossing:

- Deze oplossing beheert VM's in elke regio, maar kan alleen worden gebruikt in hetzelfde abonnement als uw Azure Automation-account.
- Deze oplossing is beschikbaar in Azure en AzureGov voor elke regio die een Log Analytics-werkruimte, een Azure Automation-account en waarschuwingen ondersteunt. AzureGov-regio's bieden momenteel geen ondersteuning voor e-mailfunctionaliteit.

> [!NOTE]
> Als u de oplossing voor klassieke VM's gebruikt, worden al uw VM's achtereenvolgens per cloudservice verwerkt. Virtuele machines worden nog steeds parallel verwerkt in verschillende cloudservices. Als u meer dan 20 VM's per cloudservice hebt, raden we u aan meerdere schema's te maken met de bovenliggende runbook **ScheduledStartStop_Parent** en 20 VM's per planning op te geven. Geef in de eigenschappen van de planning op als een lijst met door komma's gescheiden lijst, VM-namen in de parameter **VMList.** Als de automatiseringstaak voor deze oplossing anders meer dan drie uur wordt uitgevoerd, wordt deze tijdelijk gelost of gestopt volgens de [limiet voor het eerlijke aandeel.](automation-runbook-execution.md#fair-share)
>
> Azure Cloud Solution Provider (Azure CSP)-abonnementen ondersteunen alleen het Azure Resource Manager-model, niet-Azure Resource Manager-services zijn niet beschikbaar in het programma. Wanneer de Start/Stop-oplossing wordt uitgevoerd, u fouten ontvangen omdat deze cmdlets heeft om klassieke resources te beheren. Zie Beschikbare services in [CSP-abonnementen](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services)voor meer informatie over CSP. Als u een CSP-abonnement gebruikt, moet u de [**External_EnableClassicVMs**](#variables) variabele wijzigen in **False** na implementatie.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Vereisten

De runbooks voor deze oplossing werken met een [Azure Run As-account](automation-create-runas-account.md). Het Run As-account is de voorkeursverificatiemethode, omdat het certificaatverificatie gebruikt in plaats van een wachtwoord dat mogelijk verloopt of vaak wordt gewijzigd.

We raden u aan een apart automatiseringsaccount te gebruiken voor de VM-oplossing start/stop. Dit komt omdat Azure-moduleversies vaak worden bijgewerkt en de parameters ervan kunnen veranderen. De Start/Stop VM-oplossing wordt niet op dezelfde cadans geüpgraded, zodat deze mogelijk niet werkt met nieuwere versies van de cmdlets die worden gebruikt. We raden u ook aan module-updates te testen in een automatiseringsaccount voor tests voordat u deze importeert in uw productieautomatiseringsaccount(s).

### <a name="permissions-needed-to-deploy"></a>Machtigingen die nodig zijn om te implementeren

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

## <a name="deploy-the-solution"></a>De oplossing implementeren

Voer de volgende stappen uit om de VM's starten/stoppen toe te voegen tijdens off-hours oplossing aan uw Automation-account en configureer vervolgens de variabelen om de oplossing aan te passen.

1. Selecteer **vm starten/stoppen** in een automatiseringsaccount onder **Gerelateerde resources**. Klik hier op **Meer informatie over en schakel de oplossing in.** Als u al een Start/Stop VM-oplossing hebt geïmplementeerd, u deze selecteren door op **De oplossing beheren** en deze in de lijst te vinden.

   ![Inschakelen vanuit automatiseringsaccount](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > U deze ook overal in de Azure-portal maken door op **Een resource maken**te klikken. Typ op de marketplace-pagina een trefwoord zoals **Start** of **Start/Stop**. Als u begint te typen, wordt de lijst gefilterd op basis van uw invoer. U ook een of meer zoekwoorden intypen vanuit de volledige naam van de oplossing en vervolgens op Enter drukken. Selecteer **VM's starten/stoppen tijdens off-uren** in de zoekresultaten.

2. Bekijk in de pagina **VM's starten/stoppen tijdens off-hours** voor de geselecteerde oplossing de overzichtsgegevens en klik vervolgens op **Maken**.

   ![Azure Portal](media/automation-solution-vm-management/azure-portal-01.png)

3. De pagina **Oplossing toevoegen** wordt weergegeven. U wordt gevraagd de oplossing te configureren voordat u deze importeren in uw automatiseringsabonnement.

   ![Pagina Oplossing toevoegen van VM-beheer](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

4. Selecteer **werkruimte**op de pagina **Oplossing toevoegen** . Selecteer een Log Analytics-werkruimte die is gekoppeld aan hetzelfde Azure-abonnement waarin het Automatiseringsaccount zich bevindt. Als u geen werkruimte hebt, selecteert u **Nieuwe werkruimte maken**. Voer op de **werkruimtepagina Log Analytics** de volgende stappen uit:
   - Geef een naam op voor de nieuwe **Log Analytics-werkruimte**, zoals 'ContosoLAWorkspace'.
   - Selecteer een **abonnement** waarnaar u wilt linken door in de vervolgkeuzelijst te selecteren of de standaardoptie niet geschikt is.
   - Voor **resourcegroep**u een nieuwe resourcegroep maken of een bestaande groep selecteren.
   - Selecteer een **locatie**.
   - Selecteer een **prijslaag**. Kies de optie **Per GB (Standalone).** Azure Monitor-logboeken hebben [de prijzen](https://azure.microsoft.com/pricing/details/log-analytics/) bijgewerkt en de laag Per GB is de enige optie.

   > [!NOTE]
   > Bij het inschakelen van oplossingen worden slechts bepaalde regio's ondersteund voor het koppelen van een Log Analytics-werkruimte aan een Automation-Account.
   >
   > Zie [Regiotoewijzing voor automatiseringsaccount en logboekanalysewerkruimte voor](how-to/region-mappings.md)een lijst met de ondersteunde toewijzingsparen.

5. Nadat u de vereiste informatie op de **werkruimtepagina Log Analytics** hebt verstrekt, klikt u op **Maken**. U de voortgang bijhouden onder **Meldingen** in het menu, waarmee u wordt geretourneerd naar de pagina **Oplossing toevoegen** wanneer u klaar bent.
6. Selecteer **op** de pagina Oplossing toevoegen de optie **Automatiseringsaccount**. Als u een nieuwe Log Analytics-werkruimte maakt, u een nieuw automatiseringsaccount maken waaraan u moet worden gekoppeld of een bestaand automatiseringsaccount selecteren dat nog niet is gekoppeld aan een loganalytics-werkruimte. Selecteer een bestaand automatiseringsaccount of klik op **Een automatiseringsaccount maken**en geef op de pagina **Automatiseringsaccount toevoegen** de volgende gegevens op:
   - Voer in het veld **Naam** de naam van het Automation-account in.

     Alle andere opties worden automatisch ingevuld op basis van de geselecteerde werkruimte Log Analytics. Deze opties kunnen niet worden gewijzigd. Een Uitvoeren als-account voor Azure is de standaardmethode voor verificatie voor de runbooks die zijn opgenomen in deze oplossing. Nadat u op **OK**hebt geklikt, worden de configuratieopties gevalideerd en wordt het automatiseringsaccount gemaakt. U kunt de voortgang bijhouden onder **Meldingen** in het menu.

7. Selecteer tot slot op de pagina **Oplossing toevoegen** de optie **Configuratie**. De pagina **Parameters** wordt weergegeven.

   ![Parameters pagina voor oplossing](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

   Hier wordt u gevraagd om:
   - Geef de **namen van doelbrongroepen op**. Deze waarden zijn namen van resourcegroepen die VM's bevatten die door deze oplossing moeten worden beheerd. U meer dan één naam invoeren en elk afzonderlijk maken met behulp van een komma (waarden zijn niet hoofdlettergevoelig). Jokertekens worden ondersteund als de bewerking moet worden gericht op VM's in alle resourcegroepen in het abonnement. Deze waarde wordt opgeslagen in de **variabelen External_Start_ResourceGroupNames** en **External_Stop_ResourceGroupNames.**
   - Geef de **lijst met vm-uitsluiting (tekenreeks) op**. Deze waarde is de naam van een of meer virtuele machines uit de doelgroep. U meer dan één naam invoeren en elk afzonderlijk maken met behulp van een komma (waarden zijn niet hoofdlettergevoelig). Het gebruik van een wildcard wordt ondersteund. Deze waarde wordt opgeslagen in de **variabele External_ExcludeVMNames.**
   - Selecteer een **planning**. Selecteer een datum en tijd voor uw planning. Er wordt een terugkerende dagelijkse planning gemaakt vanaf het tijdstip dat u hebt geselecteerd. Het selecteren van een andere regio is niet beschikbaar. Zie [Het opstart- en afsluitschema wijzigen](#modify-the-startup-and-shutdown-schedules)als u de planning wilt configureren voor uw specifieke tijdzone.
   - Als u **e-mailmeldingen** van een actiegroep wilt ontvangen, accepteert u de standaardwaarde **Ja** en geeft u een geldig e-mailadres op. Als u **Nee selecteert,** maar op een later tijdstip besluit dat u e-mailmeldingen wilt ontvangen, u de [actiegroep](../azure-monitor/platform/action-groups.md) bijwerken die is gemaakt met geldige e-mailadressen, gescheiden door een komma. U moet ook de volgende waarschuwingsregels inschakelen:

     - AutoStop_VM_Child
     - Scheduled_StartStop_Parent
     - Sequenced_StartStop_Parent

     > [!IMPORTANT]
     > De standaardwaarde voor **doelgroepgroepnamen** is een **&ast;**. Dit richt zich op alle VM's in een abonnement. Als u niet wilt dat de oplossing om alle VM's in uw abonnement te targeten, deze waarde moet worden bijgewerkt naar een lijst met namen van resourcegroepen voordat de planningen worden ingeschakeld.

8. Nadat u de eerste instellingen hebt geconfigureerd die nodig zijn voor de oplossing, klikt u op **OK** om de pagina **Parameters** te sluiten en **selecteer Maken**. Nadat alle instellingen zijn gevalideerd, wordt de oplossing geïmplementeerd in uw abonnement. Dit proces kan enkele seconden duren en u de voortgang ervan bijhouden onder **Meldingen** in het menu.

> [!NOTE]
> Als u een Azure Cloud Solution Provider-abonnement (Azure CSP) hebt, gaat u na implementatie voltooid in uw automatiseringsaccount naar **Variabelen** onder **Gedeelde resources** en stelt u de [**External_EnableClassicVMs**](#variables) variabele in op **False.** Dit voorkomt dat de oplossing op zoek gaat naar klassieke VM-bronnen.

## <a name="scenarios"></a>Scenario's

De oplossing bevat drie verschillende scenario's. Deze scenario's zijn:

### <a name="scenario-1-startstop-vms-on-a-schedule"></a>Scenario 1: VM's starten/stoppen volgens schema

Dit scenario is de standaardconfiguratie wanneer u de oplossing voor het eerst implementeert. U het bijvoorbeeld zo configureren dat alle VM's in een abonnement worden gestopt wanneer u 's avonds het werk verlaat en 's ochtends starten wanneer u weer op kantoor bent. Wanneer u de planningen **Scheduled-StartVM** en **Scheduled-StopVM** configureert tijdens de implementatie, starten en stoppen ze gerichte VM's. Als u deze oplossing configureert om vm's te stoppen, wordt ondersteuning ondersteund, zie [De opstart- en afsluitschema's wijzigen](#modify-the-startup-and-shutdown-schedules) om te leren hoe u een aangepast schema configureren.

> [!NOTE]
> De tijdzone is uw huidige tijdzone wanneer u de parameter planningstijd configureert. Het wordt echter opgeslagen in UTC-indeling in Azure Automation. U hoeft geen tijdzoneconversie te doen, omdat dit tijdens de implementatie wordt afgehandeld.

U bepaalt welke VM's binnen het bereik zijn door de volgende variabelen te configureren: **External_Start_ResourceGroupNames,** **External_Stop_ResourceGroupNames**en **External_ExcludeVMNames**.

U de actie richten op een abonnements- en resourcegroep of een specifieke lijst met VM's targeten, maar niet beide.

#### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>De start- en stopacties richten op een abonnements- en resourcegroep

1. Configureer de **External_Stop_ResourceGroupNames-** en **External_ExcludeVMNames** variabelen om de doel-VM's op te geven.
2. Schakel de **planningen Scheduled-StartVM** en **Scheduled-StopVM** in en bij.
3. Voer de **ScheduledStartStop_Parent** runbook uit met de parameter ACTION die is ingesteld om te **starten** en de parameter WHATIF is ingesteld op **True** om een voorbeeld van uw wijzigingen te bekijken.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>De start- en stopactie targeten op VM-lijst

1. Voer de **ScheduledStartStop_Parent** runbook uit met de parameter ACTION die is ingesteld om te **starten,** voeg een door komma's gescheiden lijst met VM's toe in de parameter *VMList* en stel de parameter WHATIF in op **True**. Bekijk een voorbeeld van uw wijzigingen.
1. Configureer de **parameter External_ExcludeVMNames** met een door komma's gescheiden lijst met VM's (VM1, VM2, VM3).
1. Dit scenario is niet eer de **External_Start_ResourceGroupNames** en **External_Stop_ResourceGroupnames** variabelen. Voor dit scenario moet u uw eigen automatiseringsschema maken. Zie Een [runbook plannen in Azure Automation](../automation/automation-schedules.md)voor meer informatie.

> [!NOTE]
> De waarde voor **doelbrongroepnamen** wordt opgeslagen als de waarde voor zowel **External_Start_ResourceGroupNames** als **External_Stop_ResourceGroupNames**. Voor verdere granulariteit u elk van deze variabelen wijzigen om verschillende resourcegroepen te targeten. Gebruik **External_Stop_ResourceGroupNames**voor het starten van actie **External_Start_ResourceGroupNames**en voor stopactie . VM's worden automatisch toegevoegd aan de start- en stopschema's.

### <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a>Scenario 2: VMS in volgorde starten/stoppen met behulp van tags

In een omgeving die twee of meer componenten op meerdere VM's bevat die een gedistribueerde werkbelasting ondersteunen, is het belangrijk om de volgorde te ondersteunen waarin componenten worden gestart en gestopt. U dit scenario uitvoeren door de volgende stappen uit te voeren:

#### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>De start- en stopacties richten op een abonnements- en resourcegroep

1. Voeg een **sequencestart** en een **sequencestoptag** met een positieve gehele waarde toe aan VM's die zijn gericht op **External_Start_ResourceGroupNames** en **External_Stop_ResourceGroupNames** variabelen. De begin- en stopacties worden uitgevoerd in oplopende volgorde. Zie [Een Virtuele Machine van Windows in Azure taggen](../virtual-machines/windows/tag.md) en een virtuele machine van Linux [taggen in Azure](../virtual-machines/linux/tag.md)voor meer informatie over het taggen van een virtuele machine.
1. Wijzig de schema's **Sequenced-StartVM** en **Sequenced-StopVM** tot de datum en tijd die aan uw vereisten voldoen en schakel de planning in.
1. Voer de **SequencedStartStop_Parent** runbook uit met de parameter ACTION die is ingesteld om te **starten** en de parameter WHATIF is ingesteld op **True** om een voorbeeld van uw wijzigingen te bekijken.
1. Bekijk een voorbeeld van de actie en breng de nodige wijzigingen aan voordat u deze implementeert tegen productie-VM's. Wanneer u klaar bent, voert u het runbook handmatig uit met de parameter ingesteld op **False**of laat u de automatiseringsplanning **Sequenced-StartVM** en **Sequenced-StopVM** automatisch uitvoeren volgens het voorgeschreven schema.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>De start- en stopactie targeten op VM-lijst

1. Voeg een **sequencestart** en een **sequencestoptag** met een positieve gehele waarde toe aan VM's die u wilt toevoegen aan de parameter **VMList.**
1. Voer de **SequencedStartStop_Parent** runbook uit met de parameter ACTION die is ingesteld om te **starten,** voeg een door komma's gescheiden lijst met VM's toe in de parameter *VMList* en stel de parameter WHATIF in op **True**. Bekijk een voorbeeld van uw wijzigingen.
1. Configureer de **parameter External_ExcludeVMNames** met een door komma's gescheiden lijst met VM's (VM1, VM2, VM3).
1. Dit scenario is niet eer de **External_Start_ResourceGroupNames** en **External_Stop_ResourceGroupnames** variabelen. Voor dit scenario moet u uw eigen automatiseringsschema maken. Zie Een [runbook plannen in Azure Automation](../automation/automation-schedules.md)voor meer informatie.
1. Bekijk een voorbeeld van de actie en breng de nodige wijzigingen aan voordat u deze implementeert tegen productie-VM's. Wanneer u klaar bent, voert u het monitoring-and-diagnostics/monitoring-action-groupsrunbook handmatig uit met de parameter ingesteld op **False,** of laat u de automatiseringsplanning **Sequenced-StartVM** en **Sequenced-StopVM** automatisch uitvoeren volgens uw voorgeschreven schema.

## <a name="solution-components"></a>Oplossingsonderdelen

Deze oplossing omvat vooraf geconfigureerde runbooks, planningen en integratie met Azure Monitor-logboeken, zodat u het opstarten en afsluiten van uw virtuele machines afstemmen op uw bedrijfsbehoeften.

### <a name="runbooks"></a>Runbooks

In de volgende tabel worden de runbooks weergegeven die door deze oplossing naar uw Automatiseringsaccount zijn geïmplementeerd. Breng geen wijzigingen aan in de runbook-code. Schrijf in plaats daarvan je eigen runbook voor nieuwe functionaliteit.

> [!IMPORTANT]
> Voer geen runbook met "kind" op zijn naam.

Alle bovenliggende runbooks bevatten de parameter _WhatIf._ Wanneer ingesteld op **True**, _WhatIf_ ondersteunt detaillering van het exacte gedrag van de runbook neemt wanneer uitgevoerd zonder de _parameter WhatIf_ en valideert de juiste VM's worden gericht. Een runbook voert alleen de gedefinieerde acties uit wanneer de parameter _WhatIf_ is ingesteld op **False**.

|Runbook | Parameters | Beschrijving|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> Waarschuwingsactie <br> WebHookURI (WebHookURI) | Gebeld vanuit het ouderloopboek. Met dit runbook worden waarschuwingen per resource voor het AutoStop-scenario aangenomen.|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf: Waar of Onwaar  | Hiermee worden Azure-waarschuwingsregels voor VM's gemaakt of bijgewerkt in de beoogde abonnements- of brongroepen. <br> VMList: Door komma's gescheiden lijst met VM's. Bijvoorbeeld _vm1, vm2, vm3_.<br> *WhatIf* valideert de runbook-logica zonder uit te voeren.|
|AutoStop_Disable | geen | Hiermee schakelt u Waarschuwingen voor Automatisch stoppen en standaardplanning uit.|
|AutoStop_StopVM_Child | WebHookData | Gebeld vanuit het ouderloopboek. Waarschuwingsregels roepen dit runbook op om de VM te stoppen.|
|Bootstrap_Main | geen | Eén keer gebruikt om bootstrapconfiguraties in te stellen, zoals webhookURI, die doorgaans niet toegankelijk zijn vanuit Azure Resource Manager. Dit runbook wordt automatisch verwijderd na een succesvolle implementatie.|
|ScheduledStartStop_Child | VMName <br> Actie: Starten of stoppen <br> ResourceGroupName | Gebeld vanuit het ouderloopboek. Hiermee wordt een start- of stopactie uitgevoerd voor de geplande stop.|
|ScheduledStartStop_Parent | Actie: Starten of stoppen <br>VMList <br> WhatIf: Waar of Onwaar | Deze instelling is van invloed op alle VM's in het abonnement. Bewerk de **External_Start_ResourceGroupNames** en **External_Stop_ResourceGroupNames** alleen uit te voeren op deze gerichte resourcegroepen. U ook specifieke VM's uitsluiten door de **External_ExcludeVMNames** variabele bij te werken.<br> VMList: Door komma's gescheiden lijst met VM's. Bijvoorbeeld _vm1, vm2, vm3_.<br> _WhatIf_ valideert de runbook-logica zonder uit te voeren.|
|SequencedStartStop_Parent | Actie: Starten of stoppen <br> WhatIf: Waar of Onwaar<br>VMList| Maak tags met de naam **sequencestart** en **sequencestop** op elke VM waarvoor u start-/stopactiviteit wilt invoegen. Deze tagnamen zijn hoofdlettergevoelig. De waarde van de tag moet een positief geheel getal (1, 2, 3) zijn dat overeenkomt met de volgorde waarin u wilt starten of stoppen. <br> VMList: Door komma's gescheiden lijst met VM's. Bijvoorbeeld _vm1, vm2, vm3_. <br> _WhatIf_ valideert de runbook-logica zonder uit te voeren. <br> **Opmerking:** VM's moeten zich binnen resourcegroepen bevinden die zijn gedefinieerd als External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames en External_ExcludeVMNames in Azure Automation-variabelen. Ze moeten de juiste tags hebben om acties van kracht te laten worden.|

### <a name="variables"></a>Variabelen

In de volgende tabel worden de variabelen weergegeven die zijn gemaakt in uw automatiseringsaccount. Wijzig alleen variabelen die vooraf zijn gekoppeld aan **Extern**. Het wijzigen van variabelen vooraf vastgesteld met **interne** oorzaken ongewenste effecten.

|Variabele | Beschrijving|
|---------|------------|
|External_AutoStop_Condition | De voorwaardelijke operator die nodig is voor het configureren van de voorwaarde voordat een waarschuwing wordt geactiveerd. Aanvaardbare waarden zijn **groter dan**, **groter thanorequal**, **minder dan**, en **lessthanorequal**.|
|External_AutoStop_Description | De waarschuwing om de VM te stoppen als het CPU-percentage de drempelwaarde overschrijdt.|
|External_AutoStop_MetricName | De naam van de prestatiestatistiek waarvoor de Azure Alert-regel moet worden geconfigureerd.|
|External_AutoStop_Threshold | De drempelwaarde voor de Azure Alert-regel die is opgegeven in de variabele _External_AutoStop_MetricName_. Percentagewaarden kunnen variëren van 1 tot 100.|
|External_AutoStop_TimeAggregationOperator | De tijdaggregatieoperator, die wordt toegepast op de geselecteerde venstergrootte om de voorwaarde te evalueren. Acceptabele waarden zijn **Gemiddeld**, **Minimum**, **Maximum**, **Totaal**en **Laatste**.|
|External_AutoStop_TimeWindow | De venstergrootte waarin Azure geselecteerde statistieken analyseert voor het activeren van een waarschuwing. Deze parameter accepteert invoer in tijdspanneformaat. Mogelijke waarden zijn 5 minuten tot 6 uur.|
|External_EnableClassicVMs| Hiermee geeft u op of klassieke VM's zijn gericht op de oplossing. De standaardwaarde is True. Dit moet worden ingesteld op False voor CSP-abonnementen. Klassieke VM's vereisen een [Classic Run-As-account.](automation-create-standalone-account.md#classic-run-as-accounts)|
|External_ExcludeVMNames | Voer VM-namen in die moeten worden uitgesloten en waarbij namen worden gescheiden met behulp van een komma zonder spaties. Dit is beperkt tot 140 VM's. Als u meer dan 140 VM's toevoegt aan deze door komma's gescheiden lijst, kunnen VM's die zijn ingesteld om te worden uitgesloten, per ongeluk worden gestart of gestopt.|
|External_Start_ResourceGroupNames | Hiermee geeft u een of meer resourcegroepen op, waardoor waarden worden gescheiden met behulp van een komma die is getarget voor startacties.|
|External_Stop_ResourceGroupNames | Hiermee geeft u een of meer resourcegroepen op, waardoor waarden worden gescheiden met behulp van een komma die is getarget voor stopacties.|
|Internal_AutomationAccountName | Hiermee geeft u de naam op van het automatiseringsaccount.|
|Internal_AutoSnooze_WebhookUri | Hiermee geeft u Webhook URI op die is opgeroepen voor het AutoStop-scenario.|
|Internal_AzureSubscriptionId | Hiermee geeft u de Azure-abonnements-id op.|
|Internal_ResourceGroupName | Hiermee geeft u de naam van de groep automatiseringsaccountbron op.|

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

## <a name="azure-monitor-logs-records"></a>Azure Monitor registreert records

Automatisering maakt twee soorten records in de werkruimte Log Analytics: taaklogboeken en taakstromen.

### <a name="job-logs"></a>Taaklogboeken

|Eigenschap | Beschrijving|
|----------|----------|
|Caller |  Wie de bewerking heeft gestart. Mogelijke waarden zijn een e-mailadres of het systeem voor geplande taken.|
|Categorie | Classificatie van het type gegevens. Voor Automation is de waarde JobLogs.|
|CorrelationId | GUID is de correlatie-id van de runbook-taak.|
|JobId | GUID dat is de ID van de runbook taak.|
|operationName | Hiermee wordt het type bewerking opgegeven dat in Azure wordt uitgevoerd. Voor Automatisering is de waarde Job.|
|resourceId | Hiermee wordt het resourcetype in Azure opgegeven. Voor Automation is de waarde het Automation-account dat is gekoppeld aan het runbook.|
|ResourceGroup | Hiermee wordt de resourcegroepnaam van de runbooktaak opgegeven.|
|ResourceProvider | Hiermee wordt de Azure-service opgegeven waarmee de resources worden geleverd die u kunt implementeren en beheren. Voor Automation is de waarde Azure Automation.|
|ResourceType | Hiermee wordt het resourcetype in Azure opgegeven. Voor Automation is de waarde het Automation-account dat is gekoppeld aan het runbook.|
|resultType | De status van de runbooktaak. Mogelijke waarden zijn:<br>- Gestart<br>- Gestopt<br>- Onderbroken<br>- Mislukt<br>- Geslaagd|
|resultDescription | Hiermee wordt resultaatstatus van de runbooktaak beschreven. Mogelijke waarden zijn:<br>- Taak is gestart<br>- Taak is mislukt<br>- Taak is voltooid|
|RunbookName | Hiermee wordt de naam van het runbook opgegeven.|
|SourceSystem | Hiermee wordt het bronsysteem voor de verzonden gegevens opgegeven. Voor Automatisering is de waarde OpsManager|
|StreamType | Hiermee wordt het type gebeurtenis opgegeven. Mogelijke waarden zijn:<br>- Uitgebreid<br>- Uitvoer<br>- Fout<br>- Waarschuwing|
|SubscriptionId | Hiermee wordt de abonnements-id van de taak opgegeven.
|Time | Datum en tijd van uitvoering van de runbooktaak.|

### <a name="job-streams"></a>Taakstromen

|Eigenschap | Beschrijving|
|----------|----------|
|Caller |  Wie de bewerking heeft gestart. Mogelijke waarden zijn een e-mailadres of het systeem voor geplande taken.|
|Categorie | Classificatie van het type gegevens. Voor Automation is de waarde JobStreams.|
|JobId | GUID dat is de ID van de runbook taak.|
|operationName | Hiermee wordt het type bewerking opgegeven dat in Azure wordt uitgevoerd. Voor Automatisering is de waarde Job.|
|ResourceGroup | Hiermee wordt de resourcegroepnaam van de runbooktaak opgegeven.|
|resourceId | Hiermee geeft u de bron-id in Azure op. Voor Automation is de waarde het Automation-account dat is gekoppeld aan het runbook.|
|ResourceProvider | Hiermee wordt de Azure-service opgegeven waarmee de resources worden geleverd die u kunt implementeren en beheren. Voor Automation is de waarde Azure Automation.|
|ResourceType | Hiermee wordt het resourcetype in Azure opgegeven. Voor Automation is de waarde het Automation-account dat is gekoppeld aan het runbook.|
|resultType | Het resultaat van de runbooktaak op het moment dat de gebeurtenis werd gegenereerd. Een mogelijke waarde is:<br>- Wordt uitgevoerd|
|resultDescription | Bevat de uitvoerstroom van het runbook.|
|RunbookName | De naam van het runbook.|
|SourceSystem | Hiermee wordt het bronsysteem voor de verzonden gegevens opgegeven. Voor Automatisering is de waarde OpsManager.|
|StreamType | Het type taakstroom. Mogelijke waarden zijn:<br>- Vooruitgang<br>- Uitvoer<br>- Waarschuwing<br>- Fout<br>- Foutopsporing<br>- Uitgebreid|
|Time | Datum en tijd van uitvoering van de runbooktaak.|

Wanneer u een logboekzoekopdracht uitvoert die categorierecords van **JobLogs** of **JobStreams retourneert,** u de weergave **JobLogs** of **JobStreams** selecteren, waarin een set tegels wordt weergegeven waarin de updates worden samengevat die door de zoekopdracht worden geretourneerd.

## <a name="sample-log-searches"></a>Voorbeeldzoekopdrachten in logboeken

De volgende tabel bevat voorbeeldzoekopdrachten in logboeken voor taakrecords die worden verzameld met deze oplossing.

|Query’s uitvoeren | Beschrijving|
|----------|----------|
|Vacatures zoeken voor ScheduledStartStop_Parent die zijn voltooid | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Vacatures zoeken voor runbook-SequencedStartStop_Parent die zijn voltooid | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|

## <a name="viewing-the-solution"></a>De oplossing bekijken

Als u toegang wilt krijgen tot de oplossing, navigeert u naar uw automatiseringsaccount en selecteert **u Werkruimte** onder **GERELATEERDE RESOURCES**. Selecteer op de pagina Logboekanalyse de optie **Oplossingen** onder **ALGEMEEN**. Selecteer **op** de pagina Oplossingen de oplossing **Start-Stop-VM[workspace]** in de lijst.

Als u de oplossing selecteert, wordt de oplossingspagina **Start-Stop-VM[workspace]** weergegeven. Hier u belangrijke details bekijken, zoals de **StartStopVM-tegel.** Net als in uw werkruimte Log Analytics wordt in deze tegel een telling en een grafische weergave weergegeven van de runbook-taken voor de oplossing die zijn gestart en met succes zijn voltooid.

![Pagina automatiseringsupdatebeheeroplossing](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

Vanaf hier u de taakrecords verder analyseren door op de donuttegel te klikken. Het oplossingsdashboard toont taakgeschiedenis en vooraf gedefinieerde logboekzoekopdrachten. Schakel over naar de geavanceerde portal voor logboekanalyse om te zoeken op basis van uw zoekopdrachten.

## <a name="configure-email-notifications"></a>E-mailmeldingen configureren

Als u e-mailmeldingen wilt wijzigen nadat de oplossing is geïmplementeerd, wijzigt u de actiegroep die tijdens de implementatie is gemaakt.  

> [!NOTE]
> Abonnementen in de Azure Government Cloud bieden geen ondersteuning voor de e-mailfunctionaliteit van deze oplossing.

Navigeer in de Azure-portal naar > actiegroepen controleren. Selecteer de actiegroep met de titel **StartStop_VM_Notication**.

![Pagina automatiseringsupdatebeheeroplossing](media/automation-solution-vm-management/azure-monitor.png)

Klik op de **pagina StartStop_VM_Notification** op **Details bewerken** onder **Details**. Hiermee wordt de **pagina E-mail/SMS/Push/Voice** geopend. Werk het e-mailadres bij en klik op **OK** om de wijzigingen op te slaan.

![Pagina automatiseringsupdatebeheeroplossing](media/automation-solution-vm-management/change-email.png)

U ook aanvullende acties toevoegen aan de actiegroep, voor meer informatie over actiegroepen, [zie actiegroepen](../azure-monitor/platform/action-groups.md)

Het volgende is een voorbeeld e-mail die wordt verzonden wanneer de oplossing sluit virtuele machines.

![Pagina automatiseringsupdatebeheeroplossing](media/automation-solution-vm-management/email.png)

## <a name="addexclude-vms"></a><a name="add-exclude-vms"></a>VM's toevoegen/uitsluiten

De oplossing biedt de mogelijkheid om VM's toe te voegen om te worden gericht door de oplossing of specifiek machines uit te sluiten van de oplossing.

### <a name="add-a-vm"></a>Een VM toevoegen

Er zijn een paar opties die u gebruiken om ervoor te zorgen dat een VM wordt opgenomen in de Start/Stop-oplossing wanneer deze wordt uitgevoerd.

* Elk van de bovenliggende [runbooks](#runbooks) van de oplossing heeft een **VMList-parameter.** U een door komma's gescheiden lijst met VM-namen doorgeven aan deze parameter bij het plannen van het juiste bovenliggende runbook voor uw situatie en deze VM's worden opgenomen wanneer de oplossing wordt uitgevoerd.

* Als u meerdere VM's wilt selecteren, stelt u de **External_Start_ResourceGroupNames** in en **External_Stop_ResourceGroupNames** met de namen van de resourcegroep die de VM's bevatten die u wilt starten of stoppen. U deze waarde `*`ook instellen op, om de oplossing te laten uitvoeren ten opzichte van alle resourcegroepen in het abonnement.

### <a name="exclude-a-vm"></a>Een VM uitsluiten

Als u een virtuele machine van de oplossing wilt uitsluiten, u deze toevoegen aan de **variabele External_ExcludeVMNames.** Deze variabele is een door komma's gescheiden lijst met specifieke VM's die u wilt uitsluiten van de Start/Stop-oplossing. Deze lijst is beperkt tot 140 VM's. Als u meer dan 140 VM's toevoegt aan deze door komma's gescheiden lijst, kunnen VM's die zijn ingesteld om te worden uitgesloten, per ongeluk worden gestart of gestopt.

## <a name="modify-the-startup-and-shutdown-schedules"></a>De opstart- en afsluitschema's wijzigen

Het beheren van de opstart- en afsluitschema's in deze oplossing volgt dezelfde stappen als beschreven in [het plannen van een runbook in Azure Automation.](automation-schedules.md) Er moet een apart schema zijn om VM's te starten en te stoppen.

Het configureren van de oplossing om vm's op een bepaald moment te stoppen, wordt ondersteund. In dit scenario maakt u gewoon een **stopschema** en geen bijbehorende **Start** gepland. Hiervoor doet u het volgende:

1. Zorg ervoor dat u de resourcegroepen hebt toegevoegd die de VM's moeten afsluiten in de **variabele External_Stop_ResourceGroupNames.**
2. Maak uw eigen schema voor de tijd die u wilt afsluiten van de VM's.
3. Navigeer naar het **ScheduledStartStop_Parent-runbook** en klik op **Planning**. Hiermee u het schema selecteren dat u in de vorige stap hebt gemaakt.
4. Selecteer **Parameters en voer instellingen uit** en stel de parameter ACTION in op 'Stoppen'.
5. Klik op **OK** om uw wijzigingen op te slaan.

## <a name="update-the-solution"></a>De oplossing bijwerken

Als u een eerdere versie van deze oplossing hebt geïmplementeerd, moet u deze eerst uit uw account verwijderen voordat u een bijgewerkte versie implementeert. Volg de stappen om [de oplossing](#remove-the-solution) te verwijderen en volg de bovenstaande stappen om de oplossing te [implementeren.](#deploy-the-solution)

## <a name="remove-the-solution"></a>De oplossing verwijderen

Als u besluit dat u de oplossing niet meer hoeft te gebruiken, u deze verwijderen uit het automatiseringsaccount. Als u de oplossing verwijdert, worden alleen de runbooks verwijderd. De planningen of variabelen die zijn gemaakt toen de oplossing werd toegevoegd, worden niet verwijderd. Deze elementen moet u handmatig verwijderen als u ze niet gebruikt met andere runbooks.

Voer de volgende stappen uit om de oplossing te verwijderen:

1. Selecteer **Gekoppelde werkruimte**in uw Automatiseringsaccount onder **Gerelateerde resources**.
1. Selecteer **Ga naar werkruimte**.
1. Selecteer **Onder Algemeen**de optie **Oplossingen**. 
1. Selecteer **op** de pagina Oplossingen de oplossing **Start-Stop-VM[Workspace]**. Selecteer op de pagina **VMManagementSolution[Workspace]** in het menu de optie **Verwijderen**.<br><br> ![VM Mgmt-oplossing verwijderen](media/automation-solution-vm-management/vm-management-solution-delete.png)
1. Controleer **in** het venster Oplossing verwijderen of u de oplossing wilt verwijderen.
1. Terwijl de informatie is geverifieerd en de oplossing wordt verwijderd, u de voortgang ervan bijhouden onder **Meldingen** in het menu. U wordt na het proces teruggestuurd naar de pagina **Oplossingen** om de oplossing te verwijderen.

De werkruimte Automatiseringsaccount en Log Analytics worden niet verwijderd als onderdeel van dit proces. Als u de werkruimte Log Analytics niet wilt behouden, moet u deze handmatig verwijderen. Dit kan worden bereikt vanuit de Azure-portal:

1. Zoek en selecteer in Azure-portal **log Analytics-werkruimten**.
1. Selecteer op de pagina **Logboekanalyse-werkruimten** de werkruimte.
1. Selecteer **Verwijderen** in het menu op de pagina werkruimte-instellingen.

Als u de onderdelen van het Azure Automation-account niet wilt behouden, u ze handmatig verwijderen. Zie de [componenten oplossing](#solution-components)voor de lijst met runbooks, variabelen en schema's die door de oplossing zijn gemaakt.

## <a name="next-steps"></a>Volgende stappen

- Zie [Logboekzoekopdrachten in Azure Monitor-logboeken](../log-analytics/log-analytics-log-searches.md)voor meer informatie over het maken van verschillende zoekopdrachten en het controleren van de functielogboeken voor automatisering met Azure Monitor-logboeken.
- Zie [Runbooktaken bijhouden](automation-runbook-execution.md) voor meer informatie over runbookuitvoering, het bewaken van runbooktaken en andere technische details.
- Zie [Azure-opslaggegevens verzamelen in azure monitor-logboekenoverzicht](../azure-monitor/platform/collect-azure-metrics-logs.md)voor meer informatie over Azure Monitor-logboeken en gegevensverzamelingsbronnen.
