---
title: VM's buiten bedrijfsuren starten/stoppen oplossing
description: Deze VM-beheer oplossing Start en stopt uw Azure Resource Manager virtuele machines volgens een planning en proactief bewaakt vanuit Azure Monitor Logboeken.
services: automation
ms.subservice: process-automation
ms.date: 12/04/2019
ms.topic: conceptual
ms.openlocfilehash: e37b6b800cbe0b4272df227e1411257b33a3e0cb
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75420811"
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>VM's buiten bedrijfsuren starten/stoppen oplossing in Azure Automation

De VM's buiten bedrijfsuren starten/stoppen oplossing Start en stopt uw virtuele Azure-machines op door de gebruiker gedefinieerde planningen, biedt inzichten via Azure Monitor-logboeken en verzendt optionele e-mail berichten met behulp van [actie groepen](../azure-monitor/platform/action-groups.md). Het ondersteunt zowel Azure Resource Manager als klassieke Vm's voor de meeste scenario's. Als u deze oplossing wilt gebruiken met klassieke Vm's, hebt u een klassiek runas-account nodig dat niet standaard wordt gemaakt. Zie [klassieke uitvoeren als-accounts](automation-create-standalone-account.md#classic-run-as-accounts)voor instructies voor het maken van een klassiek runas-account.

> [!NOTE]
> De VM's buiten bedrijfsuren starten/stoppen oplossing is getest met de Azure-modules die worden geïmporteerd in uw Automation-account wanneer u de oplossing implementeert. De oplossing werkt momenteel niet met nieuwere versies van de Azure-module. Dit is alleen van invloed op het Automation-account dat u gebruikt om de VM's buiten bedrijfsuren starten/stoppen-oplossing uit te voeren. U kunt nog steeds nieuwere versies van de Azure-module gebruiken in uw andere Automation-accounts, zoals beschreven in [Azure PowerShell-modules bijwerken in azure Automation](automation-update-azure-modules.md)

Deze oplossing biedt een gecentraliseerde automatiserings optie met lage kosten voor gebruikers die hun VM-kosten willen optimaliseren. Met deze oplossing kunt u het volgende doen:

- Plan Vm's om te starten en te stoppen.
- Plan Vm's om in oplopende volg orde te starten en te stoppen met behulp van Azure Tags (niet ondersteund voor klassieke Vm's).
- Werk Vm's autostop op basis van laag CPU-gebruik.

De volgende beperkingen zijn van toepassing op de huidige oplossing:

- Deze oplossing beheert Vm's in een wille keurige regio, maar kan alleen worden gebruikt in hetzelfde abonnement als uw Azure Automation-account.
- Deze oplossing is beschikbaar in Azure en AzureGov voor elke regio die ondersteuning biedt voor een Log Analytics-werk ruimte, een Azure Automation-account en waarschuwingen. AzureGov-regio's bieden momenteel geen ondersteuning voor e-mail functionaliteit.

> [!NOTE]
> Als u de oplossing gebruikt voor klassieke Vm's, worden alle Vm's opeenvolgend per Cloud service verwerkt. Virtuele machines worden nog steeds parallel verwerkt in verschillende Cloud Services. Als u meer dan 20 Vm's per Cloud service hebt, raden we u aan meerdere planningen te maken met het bovenliggende runbook **ScheduledStartStop_Parent** en 20 vm's per schema op te geven. Geef in de schema-eigenschappen als een lijst met door komma's gescheiden waarden VM-namen op in de para meter **VMList** . Als de Automation-taak voor deze oplossing meer dan drie uur wordt uitgevoerd, wordt deze tijdelijk ongedaan gemaakt of gestopt volgens de limiet voor de [billijke share](automation-runbook-execution.md#fair-share) .
>
> Azure Cloud Solution Provider-abonnementen (Azure CSP) ondersteunen alleen het Azure Resource Manager model, niet-Azure Resource Manager services zijn niet beschikbaar in het programma. Wanneer de oplossing starten/stoppen wordt uitgevoerd, kunnen er fouten optreden omdat deze cmdlets bevat voor het beheren van klassieke resources. Zie [beschik bare Services in CSP-abonnementen](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services#comments)voor meer informatie over CSP. Als u een CSP-abonnement gebruikt, moet u de variabele [**External_EnableClassicVMs**](#variables) aanpassen naar **Onwaar** na de implementatie.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Vereisten

De runbooks voor deze oplossing werken met een [uitvoeren als-account van Azure](automation-create-runas-account.md). Het run as-account is de voorkeurs verificatie methode, omdat deze gebruikmaakt van certificaat verificatie in plaats van een wacht woord dat regel matig verloopt of kan worden gewijzigd.

U wordt aangeraden een afzonderlijk Automation-account te gebruiken voor de oplossing VM starten/stoppen. Dit komt doordat de versies van Azure-modules regel matig worden bijgewerkt en de para meters ervan kunnen veranderen. De oplossing voor het starten/stoppen van de VM is niet bijgewerkt op dezelfde uitgebracht zodat deze mogelijk niet werkt met nieuwere versies van de cmdlets die worden gebruikt. U wordt ook aangeraden de module-updates in een test Automation-account te testen voordat u ze importeert in uw productie Automation-account (s).

### <a name="permissions-needed-to-deploy"></a>Machtigingen die nodig zijn om te implementeren

Er zijn bepaalde machtigingen die een gebruiker nodig heeft om de Vm's voor het starten/stoppen van virtuele machines te implementeren. Deze machtigingen verschillen als u een vooraf gemaakt Automation-account en Log Analytics werk ruimte gebruikt of tijdens de implementatie nieuwe maakt. Als u een bijdrager aan het abonnement en een globale beheerder in uw Azure Active Directory-Tenant bent, hoeft u de volgende machtigingen niet te configureren. Als u deze rechten niet hebt of een aangepaste rol wilt configureren, raadpleegt u de onderstaande machtigingen.

#### <a name="pre-existing-automation-account-and-log-analytics-workspace"></a>Bestaand Automation-account en Log Analytics werk ruimte

Voor het implementeren van de oplossing voor het starten/stoppen van Vm's buiten kantoor uren naar een bestaand Automation-account en Log Analytics werk ruimte, moeten de gebruiker die de oplossing implementeert de volgende machtigingen voor de **resource groep**hebben. Zie [aangepaste rollen voor Azure-resources](../role-based-access-control/custom-roles.md)voor meer informatie over rollen.

| Machtiging | Scope|
| --- | --- |
| Microsoft.Automation/automationAccounts/read | Resourcegroep |
| Microsoft.Automation/automationAccounts/variables/write | Resourcegroep |
| Microsoft.Automation/automationAccounts/schedules/write | Resourcegroep |
| Microsoft.Automation/automationAccounts/runbooks/write | Resourcegroep |
| Microsoft.Automation/automationAccounts/connections/write | Resourcegroep |
| Microsoft.Automation/automationAccounts/certificates/write | Resourcegroep |
| Microsoft.Automation/automationAccounts/modules/write | Resourcegroep |
| Microsoft.Automation/automationAccounts/modules/read | Resourcegroep |
| Microsoft.automation/automationAccounts/jobSchedules/write | Resourcegroep |
| Microsoft.Automation/automationAccounts/jobs/write | Resourcegroep |
| Microsoft.Automation/automationAccounts/jobs/read | Resourcegroep |
| Microsoft.OperationsManagement/solutions/write | Resourcegroep |
| Microsoft.OperationalInsights/workspaces/* | Resourcegroep |
| Microsoft.Insights/diagnosticSettings/write | Resourcegroep |
| Microsoft.Insights/ActionGroups/Write | Resourcegroep |
| Microsoft.Insights/ActionGroups/read | Resourcegroep |
| Microsoft.Resources/subscriptions/resourceGroups/read | Resourcegroep |
| Microsoft.Resources/deployments/* | Resourcegroep |

#### <a name="new-automation-account-and-a-new-log-analytics-workspace"></a>Nieuw Automation-account en een nieuwe Log Analytics-werk ruimte

Voor het implementeren van de oplossing voor het starten/stoppen van Vm's buiten kantoor uren naar een nieuw Automation-account en Log Analytics werk ruimte moet de gebruiker die de oplossing implementeert, de in de voor gaande sectie gedefinieerde machtigingen en de volgende machtigingen hebben:

- Co-beheerder van het abonnement: het is alleen nodig om het klassieke uitvoeren als-account te maken als u klassieke Vm's wilt beheren. [Klassieke runas-accounts](automation-create-standalone-account.md#classic-run-as-accounts) worden niet meer standaard gemaakt.
- Een lid van de rol ontwikkelaar van [Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md) **toepassing** . Zie [machtigingen voor het configureren van run as-accounts](manage-runas-account.md#permissions)voor meer informatie over het configureren van uitvoeren als-accounts.
- Inzender voor het abonnement of de volgende machtigingen.

| Machtiging |Scope|
| --- | --- |
| Micro soft. Authorization/Operations/Read | Abonnement|
| Micro soft. Authorization/permissions/Read |Abonnement|
| Micro soft. Authorization/roleAssignments/lezen | Abonnement |
| Microsoft.Authorization/roleAssignments/write | Abonnement |
| Microsoft.Authorization/roleAssignments/delete | Abonnement |
| Microsoft.Automation/automationAccounts/connections/read | Resourcegroep |
| Microsoft.Automation/automationAccounts/certificates/read | Resourcegroep |
| Microsoft.Automation/automationAccounts/write | Resourcegroep |
| Microsoft.OperationalInsights/workspaces/write | Resourcegroep |

## <a name="deploy-the-solution"></a>De oplossing implementeren

Voer de volgende stappen uit om de VM's buiten bedrijfsuren starten/stoppen oplossing toe te voegen aan uw Automation-account en configureer vervolgens de variabelen om de oplossing aan te passen.

1. Selecteer vanuit een Automation-account de optie **VM starten/stoppen** onder **gerelateerde resources**. Hier kunt u op meer **informatie klikken en de oplossing inschakelen**. Als u al een VM-oplossing voor starten/stoppen hebt geïmplementeerd, kunt u deze selecteren door te klikken op **de oplossing beheren** en deze te zoeken in de lijst.

   ![Inschakelen vanuit Automation-account](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > U kunt de app ook maken op een wille keurige locatie in de Azure Portal door te klikken op **een resource maken**. Typ op de pagina Marketplace een tref woord zoals **starten** of **starten/stoppen**. Als u begint te typen, wordt de lijst gefilterd op basis van uw invoer. U kunt ook een of meer tref woorden in de volledige naam van de oplossing typen en vervolgens op ENTER drukken. Selecteer **VM's buiten bedrijfsuren starten/stoppen** in de zoek resultaten.

2. Controleer op de pagina **VM's buiten bedrijfsuren starten/stoppen** voor de geselecteerde oplossing de samenvattings informatie en klik vervolgens op **maken**.

   ![Azure Portal](media/automation-solution-vm-management/azure-portal-01.png)

3. De pagina **oplossing toevoegen** wordt weer gegeven. U wordt gevraagd de oplossing te configureren voordat u deze kunt importeren in uw Automation-abonnement.

   ![Pagina oplossing toevoegen van VM-beheer](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

4. Selecteer op de pagina **oplossing toevoegen** de optie **werk ruimte**. Selecteer een Log Analytics-werk ruimte die is gekoppeld aan hetzelfde Azure-abonnement waarin het Automation-account zich bevindt. Als u geen werk ruimte hebt, selecteert u **nieuwe werk ruimte maken**. Voer de volgende stappen uit op de pagina **werk ruimte log Analytics** :
   - Geef een naam op voor de nieuwe **log Analytics-werk ruimte**, zoals ' ContosoLAWorkspace '.
   - Selecteer een **abonnement** om te koppelen door te selecteren in de vervolg keuzelijst als de standaard instelling is geselecteerd.
   - Voor **resource groep**kunt u een nieuwe resource groep maken of een bestaande selecteren.
   - Selecteer een **locatie**.
   - Selecteer een **prijscategorie**. Kies de optie **per GB (zelfstandig)** . Azure Monitor-logboeken hebben bijgewerkte [prijzen](https://azure.microsoft.com/pricing/details/log-analytics/) en de laag per GB is de enige optie.

   > [!NOTE]
   > Bij het inschakelen van oplossingen worden slechts bepaalde regio's ondersteund voor het koppelen van een Log Analytics-werkruimte aan een Automation-Account.
   >
   > Zie [regio toewijzing voor Automation-account en log Analytics-werk ruimte](how-to/region-mappings.md)voor een lijst met de ondersteunde toewijzings paren.

5. Nadat u de vereiste gegevens op de pagina **log Analytics werk ruimte** hebt opgegeven, klikt u op **maken**. U kunt de voortgang bijhouden onder **meldingen** in het menu, waarmee u wordt teruggebracht naar de pagina **oplossing toevoegen** wanneer u klaar bent.
6. Selecteer op de pagina **oplossing toevoegen** de optie **Automation-account**. Als u een nieuwe Log Analytics-werk ruimte maakt, kunt u een nieuw Automation-account maken dat u eraan wilt koppelen of een bestaand Automation-account selecteren dat nog niet aan een Log Analytics-werk ruimte is gekoppeld. Selecteer een bestaand Automation-account of klik op **een Automation-account maken**en geef op de pagina **Automation-account toevoegen** de volgende informatie op:
   - Voer in het veld **Naam** de naam van het Automation-account in.

     Alle andere opties worden automatisch ingevuld op basis van de geselecteerde Log Analytics werk ruimte. Deze opties kunnen niet worden gewijzigd. Een Uitvoeren als-account voor Azure is de standaardmethode voor verificatie voor de runbooks die zijn opgenomen in deze oplossing. Nadat u op **OK**hebt geklikt, worden de configuratie opties gevalideerd en wordt het Automation-account gemaakt. U kunt de voortgang bijhouden onder **Meldingen** in het menu.

7. Selecteer ten slotte op de pagina **oplossing toevoegen** de optie **configuratie**. De pagina **para meters** wordt weer gegeven.

   ![De pagina para meters voor de oplossing](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

   Hier wordt u gevraagd het volgende te doen:
   - Geef de **namen**van de doel-ResourceGroup op. Deze waarden zijn namen van resource groepen die virtuele machines bevatten die door deze oplossing moeten worden beheerd. U kunt meer dan één naam invoeren en deze scheiden door een komma te gebruiken (waarden zijn niet hoofdletter gevoelig). Jokertekens worden ondersteund als de bewerking moet worden gericht op VM's in alle resourcegroepen in het abonnement. Deze waarde wordt opgeslagen in de variabelen **External_Start_ResourceGroupNames** en **External_Stop_ResourceGroupNames** .
   - Geef de **lijst met uitsluitingen van de virtuele machine op (teken reeks)** . Deze waarde is de naam van een of meer virtuele machines uit de doel resource groep. U kunt meer dan één naam invoeren en deze scheiden door een komma te gebruiken (waarden zijn niet hoofdletter gevoelig). Het gebruik van een Joker teken wordt ondersteund. Deze waarde wordt opgeslagen in de variabele **External_ExcludeVMNames** .
   - Selecteer een **planning**. Selecteer een datum en tijd voor de planning. Een opnieuw uitgevoerd dagelijks schema wordt gemaakt op basis van de tijd die u hebt geselecteerd. Het selecteren van een andere regio is niet beschikbaar. Zie [het schema voor opstarten en afsluiten aanpassen](#modify-the-startup-and-shutdown-schedules)als u de planning wilt configureren voor uw specifieke tijd zone nadat u de oplossing hebt geconfigureerd.
   - Als u **e-mail meldingen** van een actie groep wilt ontvangen, accepteert u de standaard waarde **Ja** en geeft u een geldig e-mail adres op. Als u **Nee** selecteert maar besluit op een later tijdstip dat u e-mail meldingen wilt ontvangen, kunt u de [actie groep](../azure-monitor/platform/action-groups.md) bijwerken die is gemaakt met geldige e-mail adressen gescheiden door een komma. U moet ook de volgende waarschuwings regels inschakelen:

     - AutoStop_VM_Child
     - Scheduled_StartStop_Parent
     - Sequenced_StartStop_Parent

     > [!IMPORTANT]
     > De standaard waarde voor de **doel-ResourceGroup namen** is een **&ast;** . Hiermee worden alle virtuele machines in een abonnement gericht. Als u niet wilt dat de oplossing alle virtuele machines in uw abonnement heeft, moet deze waarde worden bijgewerkt naar een lijst met namen van resource groepen voordat de planningen worden ingeschakeld.

8. Nadat u de aanvankelijk vereiste instellingen voor de oplossing hebt geconfigureerd, klikt u op **OK** om de pagina **para meters** te sluiten en selecteert u **maken**. Nadat alle instellingen zijn gevalideerd, wordt de oplossing geïmplementeerd voor uw abonnement. Het kan enkele seconden duren voordat dit proces is voltooid en u kunt de voortgang bijhouden onder **meldingen** in het menu.

> [!NOTE]
> Als u een abonnement voor Azure Cloud Solution Provider (Azure CSP) hebt nadat de implementatie is voltooid, gaat u in uw Automation-account naar **variabelen** onder **gedeelde resources** en stelt u de [**External_EnableClassicVMs**](#variables) variabele in op **Onwaar**. Zo stopt de oplossing met het zoeken naar klassieke VM-resources.

## <a name="scenarios"></a>Scenario's

De oplossing bevat drie afzonderlijke scenario's. Deze scenario's zijn:

### <a name="scenario-1-startstop-vms-on-a-schedule"></a>Scenario 1: Vm's starten/stoppen volgens een planning

Dit scenario is de standaard configuratie wanneer u de oplossing voor het eerst implementeert. U kunt deze bijvoorbeeld zo configureren dat alle virtuele machines in een abonnement worden gestopt wanneer u het werk in de avond verlaat en ze 's morgens start wanneer u weer terug bent in het kantoor. Wanneer u de planning **gepland-StartVM** en **gepland-StopVM** configureert tijdens de implementatie, worden de beoogde vm's gestart en gestopt. Het configureren van deze oplossing voor het stoppen van Vm's wordt ondersteund, Zie [de opstart-en afsluit Schema's wijzigen](#modify-the-startup-and-shutdown-schedules) voor meer informatie over het configureren van een aangepaste planning.

> [!NOTE]
> De tijd zone is uw huidige tijd zone bij het configureren van de plannings tijd parameter. Het wordt echter opgeslagen in UTC-indeling in Azure Automation. U hoeft geen tijd zone conversie uit te voeren, aangezien deze wordt verwerkt tijdens de implementatie.

U bepaalt welke Vm's binnen het bereik vallen door de volgende variabelen te configureren: **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames**en **External_ExcludeVMNames**.

U kunt de actie richten op een abonnement en resource groep, of een specifieke lijst met Vm's maken, maar niet beide.

#### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>De start-en stop acties voor een abonnement en resource groep richten

1. Configureer de variabelen **External_Stop_ResourceGroupNames** en **External_ExcludeVMNames** om de doel-vm's op te geven.
2. De **geplande-StartVM** en **geplande StopVM-** schema's inschakelen en bijwerken.
3. Voer het **ScheduledStartStop_Parent** runbook uit met de actie parameter ingesteld op **Start** en de para meter WHATIF ingesteld op **True** om een voor beeld van de wijzigingen weer te geven.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>De start-en stop actie richten op de VM-lijst

1. Voer het **ScheduledStartStop_Parent** runbook uit met de actie parameter ingesteld op **starten**, voeg een door Komma's gescheiden lijst met vm's toe aan de para meter *VMList* en stel de para meter WHATIF in op **True**. Bekijk een voor beeld van uw wijzigingen.
1. Configureer de para meter **External_ExcludeVMNames** met een door komma's gescheiden lijst met virtuele machines (VM1, VM2, VM3).
1. In dit scenario wordt de **External_Start_ResourceGroupNames** -en **External_Stop_ResourceGroupnames** variabelen niet nageleefd. Voor dit scenario moet u uw eigen Automation-schema maken. Zie [een Runbook plannen in azure Automation](../automation/automation-schedules.md)voor meer informatie.

> [!NOTE]
> De waarde voor **doel-ResourceGroup-namen** wordt opgeslagen als de waarde voor zowel **External_Start_ResourceGroupNames** als **External_Stop_ResourceGroupNames**. Voor nadere granulariteit kunt u elk van deze variabelen wijzigen in doel verschillende resource groepen. Voor de start actie gebruikt u **External_Start_ResourceGroupNames**, en voor stop actie gebruikt u **External_Stop_ResourceGroupNames**. Vm's worden automatisch toegevoegd aan de planningen starten en stoppen.

### <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a>Scenario 2: VM'S in volg orde starten/stoppen met behulp van Tags

In een omgeving met twee of meer onderdelen op meerdere Vm's die een gedistribueerde werk belasting ondersteunen, wordt de volg orde waarin de onderdelen worden gestart en gestopt, op de juiste wijze ondersteund. U kunt dit scenario uitvoeren door de volgende stappen uit te voeren:

#### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>De start-en stop acties voor een abonnement en resource groep richten

1. Voeg een **sequencestart** -en **sequencestop** -tag toe met een positieve integerwaarde voor virtuele machines die zijn gericht op **External_Start_ResourceGroupNames** en **External_Stop_ResourceGroupNames** variabelen. De start-en stop acties worden in oplopende volg orde uitgevoerd. Zie een [virtuele Windows-machine coderen in azure](../virtual-machines/windows/tag.md) en [een virtuele Linux-machine in azure labelen](../virtual-machines/linux/tag.md)voor meer informatie over het coderen van een VM.
1. Wijzig de planningen **Sequenced-StartVM** en **Sequence-StopVM** in de datum en tijd die aan uw vereisten voldoen en schakel de planning in.
1. Voer het **SequencedStartStop_Parent** runbook uit met de actie parameter ingesteld op **Start** en de para meter WHATIF ingesteld op **True** om een voor beeld van de wijzigingen weer te geven.
1. Bekijk een voor beeld van de actie en breng de benodigde wijzigingen aan voordat u implementeert op productie-Vm's. Als u klaar bent, voert u het runbook hand matig uit met de para meter ingesteld op **False**of laat u het Automation **-schema Sequenced-StartVM** en **Sequence-StopVM** automatisch uitvoeren volgens uw voorgeschreven planning.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>De start-en stop actie richten op de VM-lijst

1. Voeg een **sequencestart** -en **sequencestop** -tag toe met een positieve integerwaarde voor vm's die u wilt toevoegen aan de para meter **VMList** .
1. Voer het **SequencedStartStop_Parent** runbook uit met de actie parameter ingesteld op **starten**, voeg een door Komma's gescheiden lijst met vm's toe aan de para meter *VMList* en stel de para meter WHATIF in op **True**. Bekijk een voor beeld van uw wijzigingen.
1. Configureer de para meter **External_ExcludeVMNames** met een door komma's gescheiden lijst met virtuele machines (VM1, VM2, VM3).
1. In dit scenario wordt de **External_Start_ResourceGroupNames** -en **External_Stop_ResourceGroupnames** variabelen niet nageleefd. Voor dit scenario moet u uw eigen Automation-schema maken. Zie [een Runbook plannen in azure Automation](../automation/automation-schedules.md)voor meer informatie.
1. Bekijk een voor beeld van de actie en breng de benodigde wijzigingen aan voordat u implementeert op productie-Vm's. Als u klaar bent, voert u hand matig de bewakings-en diagnostische/bewakings actie-groupsrunbook uit met de para meter ingesteld op **False**, of laat u het Automation-schema **Sequenced-StartVM** en **Sequence-StopVM** automatisch uitvoeren volgens uw voorgeschreven schema.

### <a name="scenario-3-startstop-automatically-based-on-cpu-utilization"></a>Scenario 3: automatisch starten/stoppen op basis van CPU-gebruik

Met deze oplossing kunt u de kosten van het uitvoeren van virtuele machines in uw abonnement beheren door Azure-Vm's te evalueren die niet worden gebruikt tijdens niet-piek perioden, zoals na uur, en ze automatisch af te sluiten als het processor gebruik lager is dan x%.

De oplossing is standaard vooraf geconfigureerd om het percentage CPU-metriek te evalueren om te controleren of het gemiddelde gebruik 5% of minder is. Dit scenario wordt bepaald door de volgende variabelen en kan worden gewijzigd als de standaard waarden niet voldoen aan uw vereisten:

- External_AutoStop_MetricName
- External_AutoStop_Threshold
- External_AutoStop_TimeAggregationOperator
- External_AutoStop_TimeWindow

U kunt de actie richten op een abonnement en resource groep, of een specifieke lijst met Vm's maken, maar niet beide.

#### <a name="target-the-stop-action-against-a-subscription-and-resource-group"></a>De actie stoppen op basis van een abonnement en resource groep

1. Configureer de variabelen **External_Stop_ResourceGroupNames** en **External_ExcludeVMNames** om de doel-vm's op te geven.
1. Het **Schedule_AutoStop_CreateAlert_Parent** schema inschakelen en bijwerken.
1. Voer het **AutoStop_CreateAlert_Parent** runbook uit met de actie parameter ingesteld op **Start** en de para meter WHATIF ingesteld op **True** om een voor beeld van de wijzigingen weer te geven.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>De start-en stop actie richten op de VM-lijst

1. Voer het **AutoStop_CreateAlert_Parent** runbook uit met de actie parameter ingesteld op **starten**, voeg een door Komma's gescheiden lijst met vm's toe aan de para meter *VMList* en stel de para meter WHATIF in op **True**. Bekijk een voor beeld van uw wijzigingen.
1. Configureer de para meter **External_ExcludeVMNames** met een door komma's gescheiden lijst met virtuele machines (VM1, VM2, VM3).
1. In dit scenario wordt de **External_Start_ResourceGroupNames** -en **External_Stop_ResourceGroupnames** variabelen niet nageleefd. Voor dit scenario moet u uw eigen Automation-schema maken. Zie [een Runbook plannen in azure Automation](../automation/automation-schedules.md)voor meer informatie.

Nu u een planning hebt voor het stoppen van Vm's op basis van CPU-gebruik, moet u een van de volgende planningen inschakelen om ze te starten.

- Doel start actie op basis van het abonnement en de resource groep. Raadpleeg de stappen in [scenario 1](#scenario-1-startstop-vms-on-a-schedule) voor het testen en inschakelen van planningen **gepland StartVM** .
- Doel start actie op basis van abonnement, resource groep en label. Raadpleeg de stappen in [scenario 2](#scenario-2-startstop-vms-in-sequence-by-using-tags) voor het testen en inschakelen van planningen **met Sequence-StartVM** .

## <a name="solution-components"></a>Oplossingsonderdelen

Deze oplossing omvat vooraf geconfigureerde runbooks, planningen en integratie met Azure Monitor-logboeken, zodat u de opstart-en afsluit werkzaamheden van uw virtuele machines kunt aanpassen aan uw bedrijfs behoeften.

### <a name="runbooks"></a>Runbooks

De volgende tabel geeft een lijst van de runbooks die door deze oplossing zijn geïmplementeerd voor uw Automation-account. Breng geen wijzigingen aan in de runbook-code. In plaats daarvan schrijft u uw eigen runbook voor nieuwe functionaliteit.

> [!IMPORTANT]
> Voer geen wille keurig runbook met ' Child ' toe aan de naam.

Alle bovenliggende runbooks bevatten de para meter _WhatIf_ . Als deze eigenschap is ingesteld op **True**, ondersteunt _WhatIf_ de nauw keurigheid van het runbook wanneer het wordt uitgevoerd zonder de para meter _WhatIf_ en worden de juiste vm's gevalideerd. Een runbook voert alleen de gedefinieerde acties uit wanneer de para meter _WhatIf_ is ingesteld op **False**.

|Runbook | Parameters | Beschrijving|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | Aangeroepen vanuit het bovenliggende runbook. Dit runbook maakt waarschuwingen per resource voor het scenario voor automatisch stoppen.|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf: True of False  | Hiermee worden Azure-waarschuwings regels gemaakt of bijgewerkt op Vm's in het doel abonnement of resource groepen. <br> VMList: een door Komma's gescheiden lijst met virtuele machines. Bijvoorbeeld, _VM1, VM2, VM3_.<br> *WhatIf* valideert de runbook-logica zonder te worden uitgevoerd.|
|AutoStop_Disable | geen | Hiermee schakelt u waarschuwingen voor uitschakelen en standaard schema uit.|
|AutoStop_StopVM_Child | WebHookData | Aangeroepen vanuit het bovenliggende runbook. Waarschuwings regels roepen dit runbook aan om de VM te stoppen.|
|Bootstrap_Main | geen | Wordt één keer gebruikt voor het instellen van Boots trap configuraties zoals webhookURI, die doorgaans niet toegankelijk zijn vanaf Azure Resource Manager. Dit runbook wordt automatisch verwijderd bij een geslaagde implementatie.|
|ScheduledStartStop_Child | VMName <br> Actie: starten of stoppen <br> ResourceGroupName | Aangeroepen vanuit het bovenliggende runbook. Hiermee wordt een start-of stop actie uitgevoerd voor de geplande beëindiging.|
|ScheduledStartStop_Parent | Actie: starten of stoppen <br>VMList <br> WhatIf: True of False | Deze instelling is van invloed op alle virtuele machines in het abonnement. Bewerk de **External_Start_ResourceGroupNames** en **External_Stop_ResourceGroupNames** alleen worden uitgevoerd op deze doel resource groepen. U kunt ook specifieke Vm's uitsluiten door de **External_ExcludeVMNames** variabele bij te werken.<br> VMList: een door Komma's gescheiden lijst met virtuele machines. Bijvoorbeeld, _VM1, VM2, VM3_.<br> _WhatIf_ valideert de runbook-logica zonder te worden uitgevoerd.|
|SequencedStartStop_Parent | Actie: starten of stoppen <br> WhatIf: True of False<br>VMList| Maak Tags met de naam **sequencestart** en **sequencestop** op elke virtuele machine waarvoor u de activiteit start/stop wilt sequentieel. Deze label namen zijn hoofdletter gevoelig. De waarde van het label moet een positief geheel getal zijn (1, 2, 3) dat overeenkomt met de volg orde waarin u wilt starten of stoppen. <br> VMList: een door Komma's gescheiden lijst met virtuele machines. Bijvoorbeeld, _VM1, VM2, VM3_. <br> _WhatIf_ valideert de runbook-logica zonder te worden uitgevoerd. <br> **Opmerking**: vm's moeten zich in azure Automation variabelen bezien als External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames en External_ExcludeVMNames. Ze moeten de juiste labels hebben om de acties van kracht te laten worden.|

### <a name="variables"></a>Variabelen

De volgende tabel bevat de variabelen die zijn gemaakt in uw Automation-account. Wijzig alleen variabelen die met **extern**zijn gerepareerd. Het wijzigen van variabelen die met **interne** worden opgelost, leidt tot ongewenste effecten.

|Variabele | Beschrijving|
|---------|------------|
|External_AutoStop_Condition | De voorwaardelijke operator die is vereist voor het configureren van de voor waarde voordat een waarschuwing wordt geactiveerd. Acceptabele waarden zijn **GreaterThan**, **GreaterThanOrEqual**, **LessThan**en **LessThanOrEqual**.|
|External_AutoStop_Description | De waarschuwing voor het stoppen van de virtuele machine als het CPU-percentage de drempel waarde overschrijdt.|
|External_AutoStop_MetricName | De naam van de prestatie gegevens waarvoor de Azure-waarschuwings regel moet worden geconfigureerd.|
|External_AutoStop_Threshold | De drempel waarde voor de Azure-waarschuwings regel die is opgegeven in de variabele _External_AutoStop_MetricName_. Percentage waarden kunnen variëren van 1 tot en met 100.|
|External_AutoStop_TimeAggregationOperator | De tijds samenvoegings operator, die wordt toegepast op de geselecteerde venster grootte om de voor waarde te evalueren. Acceptabele waarden zijn **Average**, **minimum**, **maximum**, **Total**en **last**.|
|External_AutoStop_TimeWindow | De venster grootte waarover Azure geselecteerde metrische gegevens analyseert voor het activeren van een waarschuwing. Deze para meter accepteert invoer in time span-indeling. Mogelijke waarden zijn 5 minuten tot zes uur.|
|External_EnableClassicVMs| Hiermee geeft u op of klassieke Vm's zijn gericht op de oplossing. De standaardwaarde is True (Waar). Dit moet worden ingesteld op False voor CSP-abonnementen. Klassieke Vm's vereisen een [klassiek uitvoeren als-account](automation-create-standalone-account.md#classic-run-as-accounts).|
|External_ExcludeVMNames | Voer de namen van de virtuele machines in die moeten worden uitgesloten, waarbij de namen worden gescheiden door een komma zonder spaties. Dit is beperkt tot 140 Vm's. Als u meer dan 140 Vm's toevoegt aan deze door komma's gescheiden lijst, kunnen Vm's die zijn ingesteld om te worden uitgesloten, onbedoeld worden gestart of gestopt.|
|External_Start_ResourceGroupNames | Hiermee geeft u een of meer resource groepen op en scheidt u de waarden met behulp van een komma, die is gericht op Start acties.|
|External_Stop_ResourceGroupNames | Hiermee geeft u een of meer resource groepen op en scheidt u de waarden met behulp van een komma, gericht op het stoppen van acties.|
|Internal_AutomationAccountName | Hiermee geeft u de naam van het Automation-account.|
|Internal_AutoSnooze_WebhookUri | Hiermee geeft u de webhook-URI op die voor het scenario voor autostop is aangeroepen.|
|Internal_AzureSubscriptionId | Hiermee geeft u de ID van het Azure-abonnement op.|
|Internal_ResourceGroupName | Hiermee geeft u de naam van de resource groep voor het Automation-account.|

In alle scenario's zijn de **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames**en **External_ExcludeVMNames** variabelen nodig voor het doel van vm's, met uitzonde ring van het leveren van een door komma's gescheiden lijst met vm's voor de **AutoStop_CreateAlert_Parent**, **SequencedStartStop_Parent**en **ScheduledStartStop_Parent** runbooks. Dat wil zeggen dat uw virtuele machines zich in doel resource groepen moeten bevinden om te starten en te stoppen. De logica werkt op vergelijk bare wijze als het Azure-beleid. in dat doel kunt u het abonnement of de resource groep richten en acties laten overnemen door nieuw gemaakte Vm's. Op deze manier wordt voor komen dat u een afzonderlijke planning moet onderhouden voor elke VM en het beheer begint en stopt op schaal.

### <a name="schedules"></a>Schema's

De volgende tabel bevat een overzicht van de standaard schema's die zijn gemaakt in uw Automation-account. U kunt ze wijzigen of uw eigen aangepaste schema's maken. Standaard zijn alle schema's uitgeschakeld, met uitzonde ring van **Scheduled_StartVM** en **Scheduled_StopVM**.

U moet niet alle schema's inschakelen, omdat dit mogelijk overlappende plannings acties kan maken. U kunt het beste bepalen welke optimalisaties u wilt uitvoeren en dienovereenkomstig wijzigen. Zie de voorbeeld scenario's in de sectie Overzicht voor meer informatie.

|Schema naam | Frequency | Beschrijving|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | Om de 8 uur | Voert het AutoStop_CreateAlert_Parent runbook uit om de 8 uur, waardoor de op virtuele machines gebaseerde waarden in External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames en External_ExcludeVMNames in Azure Automation variabelen worden gestopt. U kunt ook een door komma's gescheiden lijst met Vm's opgeven met behulp van de para meter VMList.|
|Scheduled_StopVM | Door de gebruiker gedefinieerd, dagelijks | Voert het Scheduled_Parent runbook uit met een para _meter van elke_ dag op de opgegeven tijd. Stopt automatisch alle virtuele machines die voldoen aan de regels die zijn gedefinieerd door activa variabelen. Schakel het gerelateerde schema in, **gepland-StartVM**.|
|Scheduled_StartVM | Door de gebruiker gedefinieerd, dagelijks | Voert het Scheduled_Parent runbook uit met een para _meter van elke_ dag op de opgegeven tijd. Start automatisch alle virtuele machines die voldoen aan de regels die zijn gedefinieerd door de juiste variabelen. Schakel het gerelateerde schema in, **gepland-StopVM**.|
|Sequenced-StopVM | 1:00 uur (UTC), elke vrijdag | Voert het Sequenced_Parent runbook uit met een para meter van _Stop_ elke vrijdag op de opgegeven tijd. Opeenvolgend (oplopend) stopt alle virtuele machines met een tag van **SequenceStop** die zijn gedefinieerd door de juiste variabelen. Zie de sectie Runbooks voor meer informatie over label waarden en activa variabelen. Schakel het gerelateerde schema in, **Sequenced-StartVM**.|
|Sequenced-StartVM | 1:00 uur (UTC), elke maandag | Voert het Sequenced_Parent runbook uit met een para _meter van elke_ maandag op het opgegeven tijdstip. Na elkaar (aflopend) worden alle virtuele machines gestart met een tag van **SequenceStart** dat is gedefinieerd door de juiste variabelen. Zie de sectie Runbooks voor meer informatie over label waarden en activa variabelen. Schakel het gerelateerde schema in, **Sequenced-StopVM**.|

## <a name="azure-monitor-logs-records"></a>Azure Monitor registreert records

Met Automation worden twee typen records gemaakt in de werk ruimte Log Analytics: taak logboeken en taak stromen.

### <a name="job-logs"></a>Taaklogboeken

|Eigenschap | Beschrijving|
|----------|----------|
|Caller |  Wie de bewerking heeft gestart. Mogelijke waarden zijn een e-mailadres of het systeem voor geplande taken.|
|Category | Classificatie van het type gegevens. Voor Automation is de waarde JobLogs.|
|CorrelationId | De GUID die de correlatie-ID van de runbook-taak is.|
|JobId | De GUID die de ID van de runbook-taak is.|
|operationName | Hiermee wordt het type bewerking opgegeven dat in Azure wordt uitgevoerd. Voor Automation is de waarde taak.|
|resourceId | Hiermee wordt het resourcetype in Azure opgegeven. Voor Automation is de waarde het Automation-account dat is gekoppeld aan het runbook.|
|ResourceGroup | Hiermee wordt de resourcegroepnaam van de runbooktaak opgegeven.|
|ResourceProvider | Hiermee wordt de Azure-service opgegeven waarmee de resources worden geleverd die u kunt implementeren en beheren. Voor Automation is de waarde Azure Automation.|
|ResourceType | Hiermee wordt het resourcetype in Azure opgegeven. Voor Automation is de waarde het Automation-account dat is gekoppeld aan het runbook.|
|resultType | De status van de runbooktaak. Mogelijke waarden zijn:<br>- Gestart<br>- Gestopt<br>- Onderbroken<br>- Mislukt<br>- Geslaagd|
|resultDescription | Hiermee wordt resultaatstatus van de runbooktaak beschreven. Mogelijke waarden zijn:<br>- Taak is gestart<br>- Taak is mislukt<br>- Taak is voltooid|
|RunbookName | Hiermee wordt de naam van het runbook opgegeven.|
|SourceSystem | Hiermee wordt het bronsysteem voor de verzonden gegevens opgegeven. Voor Automation is de waarde OpsManager|
|StreamType | Hiermee wordt het type gebeurtenis opgegeven. Mogelijke waarden zijn:<br>- Uitgebreid<br>- Uitvoer<br>- Fout<br>- Waarschuwing|
|SubscriptionId | Hiermee wordt de abonnements-id van de taak opgegeven.
|Tijd | Datum en tijd van uitvoering van de runbooktaak.|

### <a name="job-streams"></a>Taakstromen

|Eigenschap | Beschrijving|
|----------|----------|
|Caller |  Wie de bewerking heeft gestart. Mogelijke waarden zijn een e-mailadres of het systeem voor geplande taken.|
|Category | Classificatie van het type gegevens. Voor Automation is de waarde JobStreams.|
|JobId | De GUID die de ID van de runbook-taak is.|
|operationName | Hiermee wordt het type bewerking opgegeven dat in Azure wordt uitgevoerd. Voor Automation is de waarde taak.|
|ResourceGroup | Hiermee wordt de resourcegroepnaam van de runbooktaak opgegeven.|
|resourceId | Hiermee geeft u de resource-ID in azure op. Voor Automation is de waarde het Automation-account dat is gekoppeld aan het runbook.|
|ResourceProvider | Hiermee wordt de Azure-service opgegeven waarmee de resources worden geleverd die u kunt implementeren en beheren. Voor Automation is de waarde Azure Automation.|
|ResourceType | Hiermee wordt het resourcetype in Azure opgegeven. Voor Automation is de waarde het Automation-account dat is gekoppeld aan het runbook.|
|resultType | Het resultaat van de runbooktaak op het moment dat de gebeurtenis werd gegenereerd. Een mogelijke waarde is:<br>- Wordt uitgevoerd|
|resultDescription | Bevat de uitvoerstroom van het runbook.|
|RunbookName | De naam van het runbook.|
|SourceSystem | Hiermee wordt het bronsysteem voor de verzonden gegevens opgegeven. Voor Automation is de waarde OpsManager.|
|StreamType | Het type taakstroom. Mogelijke waarden zijn:<br>-Voortgang<br>- Uitvoer<br>- Waarschuwing<br>- Fout<br>- Foutopsporing<br>- Uitgebreid|
|Tijd | Datum en tijd van uitvoering van de runbooktaak.|

Wanneer u een zoek opdracht in het logboek uitvoert waarmee categorie records van **JobLogs** of **JobStreams**worden geretourneerd, kunt u de weer gave **JobLogs** of **JobStreams** selecteren, waarin een reeks tegels wordt weer gegeven met een overzicht van de updates die worden geretourneerd door de zoek opdracht.

## <a name="sample-log-searches"></a>Voorbeeldzoekopdrachten in logboeken

De volgende tabel bevat voorbeeldzoekopdrachten in logboeken voor taakrecords die worden verzameld met deze oplossing.

|Query | Beschrijving|
|----------|----------|
|Taken zoeken voor runbook-ScheduledStartStop_Parent die zijn voltooid | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Taken zoeken voor runbook-SequencedStartStop_Parent die zijn voltooid | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|

## <a name="viewing-the-solution"></a>De oplossing weer geven

Als u de oplossing wilt openen, gaat u naar uw Automation-account en selecteert u **werk ruimte** onder **gerelateerde resources**. Selecteer op de pagina log Analytics **oplossingen** onder **Algemeen**. Selecteer op de pagina **oplossingen** de oplossing **Start-Stop-VM [werk ruimte]** in de lijst.

Als u de oplossing selecteert, wordt de oplossings pagina **Start-Stop-VM [werkruimte]** weer gegeven. Hier kunt u belang rijke informatie bekijken, zoals de tegel **StartStopVM** . Net als in uw Log Analytics-werk ruimte worden in deze tegel een aantal en een grafische weer gave weer gegeven van de runbook-taken voor de oplossing die is gestart en voltooid.

![Pagina Automation Updatebeheer-oplossing](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

Hier kunt u verdere analyse van de taak records uitvoeren door op de ring-tegel te klikken. Het oplossings Dashboard toont taak geschiedenis en vooraf gedefinieerde zoek opdrachten voor Logboeken. Ga naar de geavanceerde log Analytics-Portal om te zoeken op basis van uw zoek query's.

## <a name="configure-email-notifications"></a>E-mailmeldingen configureren

Als u e-mail meldingen wilt wijzigen nadat de oplossing is geïmplementeerd, wijzigt u de actie groep die is gemaakt tijdens de implementatie.  

> [!NOTE]
> Abonnementen in de Azure Government Cloud bieden geen ondersteuning voor de e-mail functionaliteit van deze oplossing.

Ga in het Azure Portal naar controle-> actie groepen. Selecteer de actie groep met de titel **StartStop_VM_Notication**.

![Pagina Automation Updatebeheer-oplossing](media/automation-solution-vm-management/azure-monitor.png)

Klik op de pagina **StartStop_VM_Notification** op **Details bewerken** onder **Details**. Hiermee opent u de pagina **e-mail/SMS/push/Voice** . Werk het e-mail adres bij en klik op **OK** om uw wijzigingen op te slaan.

![Pagina Automation Updatebeheer-oplossing](media/automation-solution-vm-management/change-email.png)

U kunt ook aanvullende acties toevoegen aan de actie groep, Zie [actie groepen](../azure-monitor/platform/action-groups.md) voor meer informatie over actie groepen

Hier volgt een voor beeld van een e-mail bericht dat wordt verzonden wanneer virtuele machines worden afgesloten met de oplossing.

![Pagina Automation Updatebeheer-oplossing](media/automation-solution-vm-management/email.png)

## <a name="add-exclude-vms"></a>Vm's toevoegen/uitsluiten

De oplossing biedt de mogelijkheid om Vm's toe te voegen aan de hand van de oplossing of om specifieke machines uit te sluiten van de oplossing.

### <a name="add-a-vm"></a>Een virtuele machine toevoegen

Er zijn een aantal opties die u kunt gebruiken om ervoor te zorgen dat een virtuele machine wordt opgenomen in de oplossing starten/stoppen wanneer deze wordt uitgevoerd.

* Elk van de bovenliggende [runbooks](#runbooks) van de oplossing heeft een **VMList** -para meter. U kunt een door komma's gescheiden lijst met VM-namen door geven aan deze para meter bij het plannen van het juiste bovenliggende runbook voor uw situatie en deze Vm's worden opgenomen wanneer de oplossing wordt uitgevoerd.

* Als u meerdere Vm's wilt selecteren, stelt u de **External_Start_ResourceGroupNames** en **External_Stop_ResourceGroupNames** in met de namen van de resource groepen die de vm's bevatten die u wilt starten of stoppen. U kunt deze waarde ook instellen op `*`, zodat de oplossing wordt uitgevoerd voor alle resource groepen in het abonnement.

### <a name="exclude-a-vm"></a>Een virtuele machine uitsluiten

Als u een virtuele machine wilt uitsluiten van de oplossing, kunt u deze toevoegen aan de **External_ExcludeVMNames** variabele. Deze variabele is een door komma's gescheiden lijst met specifieke Vm's die moeten worden uitgesloten van de oplossing voor starten en stoppen. Deze lijst is beperkt tot 140 Vm's. Als u meer dan 140 Vm's toevoegt aan deze door komma's gescheiden lijst, kunnen Vm's die zijn ingesteld om te worden uitgesloten, onbedoeld worden gestart of gestopt.

## <a name="modify-the-startup-and-shutdown-schedules"></a>De planningen voor opstarten en afsluiten wijzigen

Het beheren van de opstart-en afsluit schema's in deze oplossing volgt dezelfde stappen als beschreven in [een Runbook plannen in azure Automation](automation-schedules.md). Er moet een apart schema zijn om Vm's te starten en te stoppen.

Het configureren van de oplossing om alleen Vm's op een bepaald moment te stoppen, wordt ondersteund. In dit scenario maakt u alleen een **Stop** schema en geen bijbehorende **begin** geplande taken. Hiervoor doet u het volgende:

1. Zorg ervoor dat u de resource groepen hebt toegevoegd voor de virtuele machines die moeten worden afgesloten in de **External_Stop_ResourceGroupNames** variabele.
2. Maak uw eigen planning voor het tijdstip waarop u de virtuele machines wilt afsluiten.
3. Ga naar het **ScheduledStartStop_Parent** runbook en klik op **schema**. Hierdoor kunt u het schema selecteren dat u in de vorige stap hebt gemaakt.
4. Selecteer **para meters en voer instellingen uit** en stel de actie parameter in op stoppen.
5. Klik op **OK** om uw wijzigingen op te slaan.

## <a name="update-the-solution"></a>De oplossing bijwerken

Als u een eerdere versie van deze oplossing hebt geïmplementeerd, moet u deze eerst verwijderen uit uw account voordat u een bijgewerkte versie implementeert. Volg de stappen om [de oplossing te verwijderen](#remove-the-solution) en volg de bovenstaande stappen om [de oplossing te implementeren](#deploy-the-solution).

## <a name="remove-the-solution"></a>De oplossing verwijderen

Als u besluit dat u de oplossing niet meer nodig hebt, kunt u deze verwijderen uit het Automation-account. Als u de oplossing verwijdert, worden alleen de runbooks verwijderd. De schema's of variabelen die zijn gemaakt toen de oplossing werd toegevoegd, worden niet verwijderd. U moet deze activa hand matig verwijderen als u deze niet gebruikt met andere runbooks.

Voer de volgende stappen uit om de oplossing te verwijderen:

1. Selecteer in uw Automation-account, onder **gerelateerde resources**, de optie **gekoppelde werk ruimte**.
1. Selecteer **Ga naar werk ruimte**.
1. Onder **Algemeen**selecteert u **oplossingen**. 
1. Selecteer op de pagina **oplossingen** de oplossing **Start-Stop-VM [werk ruimte]** . Selecteer op de pagina **VMManagementSolution [workspace]** in het menu **verwijderen**.<br><br> ![verwijderen van de oplossing voor VM-beheer](media/automation-solution-vm-management/vm-management-solution-delete.png)
1. Bevestig in het venster **oplossing verwijderen** dat u de oplossing wilt verwijderen.
1. Terwijl de informatie wordt gecontroleerd en de oplossing wordt verwijderd, kunt u de voortgang bijhouden onder **meldingen** in het menu. U keert terug naar de pagina **oplossingen** nadat het proces voor het verwijderen van de oplossing wordt gestart.

Het Automation-account en de Log Analytics-werk ruimte worden niet verwijderd als onderdeel van dit proces. Als u de Log Analytics-werk ruimte niet wilt behouden, moet u deze hand matig verwijderen. Dit kan worden bereikt vanuit de Azure Portal:

1. In Azure Portal zoekt en selecteert u **log Analytics-werk ruimten**.
1. Selecteer de werk ruimte op de pagina **log Analytics-werk ruimten** .
1. Selecteer **verwijderen** in het menu op de pagina werkruimte instellingen.

Als u de onderdelen van het Azure Automation-account niet wilt behouden, kunt u elke hand matig verwijderen. Zie de [oplossings onderdelen](#solution-components)voor de lijst met runbooks, variabelen en planningen die zijn gemaakt met de oplossing.

## <a name="next-steps"></a>Volgende stappen

- Zie [Zoek opdrachten in Logboeken in azure monitor logboeken](../log-analytics/log-analytics-log-searches.md)voor meer informatie over het maken van verschillende Zoek query's en het controleren van de Automation-taak logboeken met Azure monitor-Logboeken.
- Zie [Runbooktaken bijhouden](automation-runbook-execution.md) voor meer informatie over runbookuitvoering, het bewaken van runbooktaken en andere technische details.
- Zie [Azure Storage-gegevens verzamelen in azure monitor logs Overview](../azure-monitor/platform/collect-azure-metrics-logs.md)voor meer informatie over Azure monitor logboeken en bronnen voor gegevens verzameling.
