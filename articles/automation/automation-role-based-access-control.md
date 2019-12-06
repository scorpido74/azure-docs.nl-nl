---
title: Toegangsbeheer op basis van rollen in Azure Automation
description: Met op rollen gebaseerd toegangsbeheer (RBAC) beschikt u over toegangsbeheer voor Azure-resources. In dit artikel wordt beschreven hoe u RBAC instelt in Azure Automation.
keywords: automatisering rbac, rolgebaseerde toegangscontrole, azure rbac
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 05/17/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 0ee524768f46de965b1755f2cfffdf9e2034bec8
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850785"
---
# <a name="role-based-access-control-in-azure-automation"></a>Toegangsbeheer op basis van rollen in Azure Automation

Met op rollen gebaseerd toegangsbeheer (RBAC) beschikt u over toegangsbeheer voor Azure-resources. Met [RBAC](../role-based-access-control/overview.md)kunt u taken binnen uw team scheiden en alleen de hoeveelheid toegang verlenen aan gebruikers, groepen en toepassingen die ze nodig hebben om hun taken uit te voeren. Op rollen gebaseerde toegang kan worden verleend aan gebruikers via de Azure-portal, Azure-opdrachtregelprogramma's of Azure Management-API's.

## <a name="roles-in-automation-accounts"></a>Rollen in Automation-accounts

In Azure Automation wordt toegang verleend door de juiste RBAC-rol toe te wijzen aan gebruikers, groepen en toepassingen bij het Automation-accountbereik. Hieronder vindt u de ingebouwde rollen die worden ondersteund met een Automation-account:

| **Rol** | **Beschrijving** |
|:--- |:--- |
| Eigenaar |De rol eigenaar biedt toegang tot alle resources en acties binnen een Automation-account, inclusief toegang tot andere gebruikers, groepen en toepassingen om het Automation-account te beheren. |
| Inzender |De rol van Bijdrager maakt het mogelijk om alles te beheren, behalve de toegangsrechten van andere gebruikers te wijzigen naar een Automation-account. |
| Lezer |Met de rol van Lezer kunt u alle resources in een Automation-account bekijken, maar niets wijzigen. |
| Automation-operator |Met de rol Automation-operator kunt u de naam en eigenschappen van een runbook weer geven en taken maken en beheren voor alle runbooks in een Automation-account. Deze rol is handig als u niet wilt dat uw Automation-accountresources zoals referentieassets en runbooks worden weergegeven of gewijzigd, maar u wel wilt toestaan dat leden van uw organisatie deze runbooks uitvoeren. |
|Automation-taak operator|Met de rol Automation-taak operator kunt u taken maken en beheren voor alle runbooks in een Automation-account.|
|Automation-Runbook-operator|Met de rol Automation-Runbook-operator kunt u de naam en eigenschappen van een runbook weer geven.|
| Inzender van Log Analytics | Met de rol Log Analytics Inzender kunt u alle bewakings gegevens lezen en bewakings instellingen bewerken. Het bewerken van bewakings instellingen omvat het toevoegen van de VM-extensie aan Vm's, het lezen van opslag account sleutels om het verzamelen van Logboeken vanuit Azure Storage te kunnen configureren, Automation-accounts te maken en te configureren, oplossingen toe te voegen en Azure Diagnostics te configureren op alle Azure-resources.|
| Lezer van Log Analytics | Met de rol Log Analytics lezer kunt u alle bewakings gegevens weer geven en doorzoeken, evenals controle-instellingen weer geven. Dit omvat het weer geven van de configuratie van Azure Diagnostics op alle Azure-resources. |
| Inzender bewaken | Met de rol bewakings bijdrager kunt u alle bewakings gegevens lezen en bewakings instellingen bijwerken.|
| Bewakings lezer | Met de rol controle lezer kunt u alle bewakings gegevens lezen. |
| Beheerder van gebruikerstoegang |De beheerdersrol gebruiker toegang kunt u gebruikerstoegang tot Azure Automation-accounts beheren. |

## <a name="role-permissions"></a>Rolmachtigingen

In de volgende tabellen worden de specifieke machtigingen beschreven die aan elke rol worden gegeven. Dit kan acties omvatten, waarmee machtigingen en de vermeden worden beperkt.

### <a name="owner"></a>Eigenaar

Een eigenaar kan alles beheren, inclusief toegang. In de volgende tabel ziet u de machtigingen die zijn verleend voor de rol:

|Acties|Beschrijving|
|---|---|
|Microsoft.Automation/automationAccounts/|Resources van alle typen maken en beheren.|

### <a name="contributor"></a>Inzender

Een mede werker kan alles behalve toegang beheren. De volgende tabel bevat de verleende en geweigerde machtigingen voor de rol:

|**Acties**  |**Beschrijving**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/|Resources van alle typen maken en beheren|
|**Geen acties**||
|Microsoft.Authorization/*/Delete| Rollen en roltoewijzingen verwijderen.       |
|Microsoft.Authorization/*/Write     |  Rollen en roltoewijzingen maken.       |
|Microsoft.Authorization/elevateAccess/Action    | Hiermee weigert u de mogelijkheid om een beheerder voor gebruikers toegang te maken.       |

### <a name="reader"></a>Lezer

Een lezer kan alle resources in een Automation-account weer geven, maar kan geen wijzigingen aanbrengen.

|**Acties**  |**Beschrijving**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/read|Alle resources in een Automation-account weer geven. |

### <a name="automation-operator"></a>Automation-operator

Een Automation-operator kan taken maken en beheren, en de namen en eigenschappen van runbook lezen voor alle runbooks in een Automation-account.  Opmerking: als u operator toegang tot afzonderlijke runbooks wilt beheren, stelt u deze rol niet in en gebruikt u in plaats daarvan de rollen ' Automation job operator ' en ' Automation Runbook Opera tors ' in combi natie. In de volgende tabel ziet u de machtigingen die zijn verleend voor de rol:

|**Acties**  |**Beschrijving**  |
|---------|---------|
|Microsoft.Authorization/*/read|Lees autorisatie.|
|Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read|Hybrid Runbook Worker resources lezen.|
|Microsoft.Automation/automationAccounts/jobs/read|Taken van het runbook weer geven.|
|Microsoft.Automation/automationAccounts/jobs/resume/action|Een onderbroken taak hervatten.|
|Microsoft.Automation/automationAccounts/jobs/stop/action|Annuleren van een taak wordt uitgevoerd.|
|Microsoft.Automation/automationAccounts/jobs/streams/read|Lees de taak stromen en uitvoer.|
|Microsoft.Automation/automationAccounts/jobs/output/read|De uitvoer van een taak ophalen.|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|Pauzeren van een taak wordt uitgevoerd.|
|Microsoft.Automation/automationAccounts/jobs/write|Maak taken.|
|Microsoft.Automation/automationAccounts/jobSchedules/read|Een Azure Automation taak schema ophalen.|
|Microsoft.Automation/automationAccounts/jobSchedules/write|Maak een Azure Automation taak schema.|
|Microsoft.Automation/automationAccounts/linkedWorkspace/read|De werk ruimte die is gekoppeld aan het Automation-account ophalen.|
|Microsoft.Automation/automationAccounts/read|Een Azure Automation-account ophalen.|
|Microsoft.Automation/automationAccounts/runbooks/read|Een Azure Automation runbook ophalen.|
|Microsoft.Automation/automationAccounts/schedules/read|Een Azure Automation schema-Asset ophalen.|
|Microsoft.Automation/automationAccounts/schedules/write|Een Azure Automation Schedule-Asset maken of bijwerken.|
|Microsoft.Resources/subscriptions/resourceGroups/read      |Lees rollen en roltoewijzingen.         |
|Microsoft.Resources/deployments/*      |Implementaties van resource groepen maken en beheren.         |
|Microsoft.Insights/alertRules/*      | Waarschuwings regels maken en beheren.        |
|Microsoft.Support/* |Ondersteunings tickets maken en beheren.|

### <a name="automation-job-operator"></a>Automation-taak operator

Er wordt een rol voor Automation-taak operator verleend voor het Automation-account bereik. Hierdoor kunnen de operator machtigingen voor het maken en beheren van taken voor alle runbooks in het account. In de volgende tabel ziet u de machtigingen die zijn verleend voor de rol:

|**Acties**  |**Beschrijving**  |
|---------|---------|
|Microsoft.Authorization/*/read|Lees autorisatie.|
|Microsoft.Automation/automationAccounts/jobs/read|Taken van het runbook weer geven.|
|Microsoft.Automation/automationAccounts/jobs/resume/action|Een onderbroken taak hervatten.|
|Microsoft.Automation/automationAccounts/jobs/stop/action|Annuleren van een taak wordt uitgevoerd.|
|Microsoft.Automation/automationAccounts/jobs/streams/read|Lees de taak stromen en uitvoer.|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|Pauzeren van een taak wordt uitgevoerd.|
|Microsoft.Automation/automationAccounts/jobs/write|Maak taken.|
|Microsoft.Resources/subscriptions/resourceGroups/read      |  Lees rollen en roltoewijzingen.       |
|Microsoft.Resources/deployments/*      |Implementaties van resource groepen maken en beheren.         |
|Microsoft.Insights/alertRules/*      | Waarschuwings regels maken en beheren.        |
|Microsoft.Support/* |Ondersteunings tickets maken en beheren.|

### <a name="automation-runbook-operator"></a>Automation-Runbook-operator

Er wordt een Automation Runbook-operator functie verleend op het Runbook-bereik. Een Automation-Runbook-operator kan de naam en eigenschappen van het Runbook weer geven.  Deze rol in combi natie met de rol ' Automation-taak operator ' stelt de operator in staat om ook taken voor het runbook te maken en te beheren. In de volgende tabel ziet u de machtigingen die zijn verleend voor de rol:

|**Acties**  |**Beschrijving**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/runbooks/read     | De runbooks weer geven.        |
|Microsoft.Authorization/*/read      | Lees autorisatie.        |
|Microsoft.Resources/subscriptions/resourceGroups/read      |Lees rollen en roltoewijzingen.         |
|Microsoft.Resources/deployments/*      | Implementaties van resource groepen maken en beheren.         |
|Microsoft.Insights/alertRules/*      | Waarschuwings regels maken en beheren.        |
|Microsoft.Support/*      | Ondersteunings tickets maken en beheren.        |

### <a name="log-analytics-contributor"></a>Inzender van Log Analytics

Een Log Analytics Inzender kan alle bewakings gegevens lezen en controle-instellingen bewerken. Het bewerken van bewakings instellingen omvat het toevoegen van de VM-extensie aan Vm's; lezen van opslag account sleutels om het verzamelen van logboeken van Azure Storage te kunnen configureren. Automation-accounts maken en configureren; oplossingen toevoegen; en het configureren van Azure Diagnostics voor alle Azure-resources. In de volgende tabel ziet u de machtigingen die zijn verleend voor de rol:

|**Acties**  |**Beschrijving**  |
|---------|---------|
|*/read|Lees resources van alle typen, met uitzonde ring van geheimen.|
|Microsoft.Automation/automationAccounts/*|Automation-accounts beheren.|
|Microsoft.ClassicCompute/virtualMachines/extensions/*|Extensies voor virtuele machines maken en beheren.|
|Microsoft.ClassicStorage/storageAccounts/listKeys/action|Lijst met klassieke opslag account-sleutels.|
|Microsoft.Compute/virtualMachines/extensions/*|Maak en beheer klassieke virtuele-machine uitbreidingen.|
|Microsoft.Insights/alertRules/*|Waarschuwings regels lezen/schrijven/verwijderen.|
|Microsoft.Insights/diagnosticSettings/*|Diagnostische instellingen lezen/schrijven/verwijderen.|
|Microsoft.OperationalInsights/*|Azure Monitor logboeken beheren.|
|Microsoft.OperationsManagement/*|Beheer oplossingen in werk ruimten.|
|Microsoft.Resources/deployments/*|Implementaties van resource groepen maken en beheren.|
|Microsoft.Resources/subscriptions/resourcegroups/deployments/*|Implementaties van resource groepen maken en beheren.|
|Microsoft.Storage/storageAccounts/listKeys/action|Sleutels van opslag account weer geven.|
|Microsoft.Support/*|Ondersteunings tickets maken en beheren.|

### <a name="log-analytics-reader"></a>Lezer van Log Analytics

Een Log Analytics lezer kan alle bewakings gegevens weer geven en doorzoeken en controle-instellingen weer geven, inclusief het weer geven van de configuratie van Azure Diagnostics op alle Azure-resources. In de volgende tabel ziet u de machtigingen die zijn verleend of geweigerd voor de rol:

|**Acties**  |**Beschrijving**  |
|---------|---------|
|*/read|Lees resources van alle typen, met uitzonde ring van geheimen.|
|Microsoft.OperationalInsights/workspaces/analytics/query/action|Query's beheren in Azure Monitor-Logboeken.|
|Microsoft.OperationalInsights/workspaces/search/action|Azure Monitor logboek gegevens zoeken.|
|Microsoft.Support/*|Ondersteunings tickets maken en beheren.|
|**Geen acties**| |
|Microsoft.OperationalInsights/workspaces/sharedKeys/read|De gedeelde toegangs sleutels kunnen niet worden gelezen.|

### <a name="monitoring-contributor"></a>Inzender bewaken

Een mede werker van de bewaking kan alle bewakings gegevens lezen en bewakings instellingen bijwerken. In de volgende tabel ziet u de machtigingen die zijn verleend voor de rol:

|**Acties**  |**Beschrijving**  |
|---------|---------|
|*/read|Lees resources van alle typen, met uitzonde ring van geheimen.|
|Microsoft.AlertsManagement/alerts/*|Waarschuwingen beheren.|
|Microsoft.AlertsManagement/alertsSummary/*|Het waarschuwings Dashboard beheren.|
|Microsoft.Insights/AlertRules/*|Waarschuwings regels beheren.|
|Microsoft.Insights/components/*|Application Insights onderdelen beheren.|
|Microsoft.Insights/DiagnosticSettings/*|Diagnostische instellingen beheren.|
|Microsoft.Insights/eventtypes/*|Lijst activiteiten logboek gebeurtenissen (beheer gebeurtenissen) in een abonnement. Deze machtiging is van toepassing op zowel toegang via het programma als de portal tot het activiteiten logboek.|
|Microsoft.Insights/LogDefinitions/*|Deze machtiging is nodig voor gebruikers die toegang moeten hebben tot activiteiten logboeken via de portal. Lijst met logboek categorieën in het activiteiten logboek.|
|Microsoft.Insights/MetricDefinitions/*|Metrische definities lezen (lijst met beschik bare meet typen voor een resource).|
|Microsoft.Insights/Metrics/*|Meet gegevens voor een resource lezen.|
|Microsoft.Insights/Register/Action|Registreer de micro soft. Insights-provider.|
|Microsoft.Insights/webtests/*|Application Insights-webtests beheren.|
|Microsoft.OperationalInsights/workspaces/intelligencepacks/*|De oplossings pakketten van Azure Monitor-logboeken beheren.|
|Microsoft.OperationalInsights/workspaces/savedSearches/*|Opgeslagen Zoek opdrachten in Azure Monitor logboeken beheren.|
|Microsoft.OperationalInsights/workspaces/search/action|Zoeken Log Analytics-werk ruimten.|
|Microsoft.OperationalInsights/workspaces/sharedKeys/action|Lijst met sleutels voor een Log Analytics-werk ruimte.|
|Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*|Beheer van Azure Monitor-logboeken voor opslag inzicht in de configuratie.|
|Microsoft.Support/*|Ondersteunings tickets maken en beheren.|
|Microsoft.WorkloadMonitor/workloads/*|Werk belastingen beheren.|

### <a name="monitoring-reader"></a>Bewakings lezer

Een bewakings lezer kan alle bewakings gegevens lezen. In de volgende tabel ziet u de machtigingen die zijn verleend voor de rol:

|**Acties**  |**Beschrijving**  |
|---------|---------|
|*/read|Lees resources van alle typen, met uitzonde ring van geheimen.|
|Microsoft.OperationalInsights/workspaces/search/action|Zoeken Log Analytics-werk ruimten.|
|Microsoft.Support/*|Ondersteunings tickets maken en beheren|

### <a name="user-access-administrator"></a>Beheerder van gebruikerstoegang

Een beheerder van de gebruikers toegang kan de gebruikers toegang tot Azure-resources beheren. In de volgende tabel ziet u de machtigingen die zijn verleend voor de rol:

|**Acties**  |**Beschrijving**  |
|---------|---------|
|*/read|Alle resources lezen|
|Microsoft.Authorization/*|Autorisatie beheren|
|Microsoft.Support/*|Ondersteunings tickets maken en beheren|

## <a name="onboarding"></a>Voorbereiden

In de volgende tabellen ziet u de mini maal vereiste machtigingen voor het onboarden van virtuele machines voor het bijhouden van wijzigingen of update beheer oplossingen.

### <a name="onboarding-from-a-virtual-machine"></a>Onboarding van een virtuele machine

|**Actie**  |**Hebt**  |**Minimum bereik**  |
|---------|---------|---------|
|Nieuwe implementatie schrijven      | Microsoft.Resources/deployments/*          |Abonnement          |
|Nieuwe resource groep schrijven      | Microsoft.Resources/subscriptions/resourceGroups/write        | Abonnement          |
|Nieuwe standaardwerk ruimte maken      | Microsoft.OperationalInsights/workspaces/write         | Resourcegroep         |
|Nieuw account maken      |  Microsoft.Automation/automationAccounts/write        |Resourcegroep         |
|Werk ruimte en account koppelen      |Microsoft.OperationalInsights/workspaces/write</br>Microsoft.Automation/automationAccounts/read|Werkruimte</br>Automation-account
|Oplossing maken      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write |Resourcegroep          |
|MMA-extensie maken      | Microsoft.Compute/virtualMachines/write         | Virtuele machine         |
|Opgeslagen zoek opdracht maken      | Microsoft.OperationalInsights/workspaces/write          | Werkruimte         |
|Scope configuratie maken      | Microsoft.OperationalInsights/workspaces/write          | Werkruimte         |
|Oplossing koppelen aan Scope configuratie      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write         | Oplossing         |
|Status controle voor onboarding-werk ruimte lezen      | Microsoft.OperationalInsights/workspaces/read         | Werkruimte         |
|Status controle voor onboarding-de eigenschap gekoppelde werk ruimte van het account lezen     | Microsoft.Automation/automationAccounts/read      | Automation-account        |
|Status controle voor onboarding-Lees oplossing      | Microsoft.OperationalInsights/workspaces/intelligencepacks/read          | Oplossing         |
|Controle van status van onboarding-VM lezen      | Microsoft.Compute/virtualMachines/read         | Virtuele machine         |
|Status controle voor onboarding-account lezen      | Microsoft.Automation/automationAccounts/read  |  Automation-account   |
| Controle van de werk ruimte voor VM<sup>1</sup> voorbereiden       | Microsoft.OperationalInsights/workspaces/read         | Abonnement         |
| De Log Analytics provider registreren |Microsoft.Insights/register/action | Abonnement|

<sup>1</sup> deze machtiging is vereist voor de onboarding van de VM-Portal.

### <a name="onboarding-from-automation-account"></a>Onboarding vanuit Automation-account

|**Actie**  |**Hebt** |**Minimum bereik**  |
|---------|---------|---------|
|Nieuwe implementatie maken     | Microsoft.Resources/deployments/*        | Abonnement         |
|Nieuwe resource groep maken     | Microsoft.Resources/subscriptions/resourceGroups/write         | Abonnement        |
|Blade AutomationOnboarding-nieuwe werk ruimte maken     |Microsoft.OperationalInsights/workspaces/write           | Resourcegroep        |
|Blade AutomationOnboarding-gekoppelde werk ruimte lezen     | Microsoft.Automation/automationAccounts/read        | Automation-account       |
|Blade AutomationOnboarding-oplossing lezen     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read         | Oplossing        |
|Blade AutomationOnboarding-werk ruimte lezen     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read        | Werkruimte        |
|Koppeling maken voor de werk ruimte en het account     | Microsoft.OperationalInsights/workspaces/write        | Werkruimte        |
|Account voor Shoebox schrijven      | Microsoft.Automation/automationAccounts/write        | Account        |
|Oplossing maken      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write        | Resourcegroep         |
|Opgeslagen zoek opdracht maken/bewerken     | Microsoft.OperationalInsights/workspaces/write        | Werkruimte        |
|Scope configuratie maken/bewerken     | Microsoft.OperationalInsights/workspaces/write        | Werkruimte        |
|Oplossing koppelen aan Scope configuratie      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write         | Oplossing         |
| De Log Analytics provider registreren |Microsoft.Insights/register/action | Abonnement|
|**Stap 2: meerdere Vm's onboarden**     |         |         |
|Blade VMOnboarding-uitbrei ding MMA maken     | Microsoft.Compute/virtualMachines/write           | Virtuele machine        |
|Opgeslagen zoek opdracht maken/bewerken     | Microsoft.OperationalInsights/workspaces/write           | Werkruimte        |
|Scope configuratie maken/bewerken  | Microsoft.OperationalInsights/workspaces/write   | Werkruimte|

## <a name="update-management"></a>Updatebeheer

Update beheer bereikt meerdere services voor het leveren van de service. De volgende tabel bevat de machtigingen die nodig zijn voor het beheren van implementaties van update beheer:

|**Resource**  |**Rol**  |**Bereik**  |
|---------|---------|---------|
|Automation-account     | Inzender van Log Analytics       | Automation-account        |
|Automation-account    | Inzender voor virtuele machines        | Resource groep voor het account        |
|Log Analytics-werkruimte     | Inzender van Log Analytics| Log Analytics-werkruimte        |
|Log Analytics-werkruimte |Lezer van Log Analytics| Abonnement|
|Oplossing     |Inzender van Log Analytics         | Oplossing|
|Virtuele machine     | Inzender voor virtuele machines        | Virtuele machine        |

## <a name="configure-rbac-for-your-automation-account"></a>RBAC configureren voor uw Automation-account

In de volgende sectie ziet u hoe u RBAC kunt configureren voor uw Automation-account via de [Portal](#configure-rbac-using-the-azure-portal) en [Power shell](#configure-rbac-using-powershell)

### <a name="configure-rbac-using-the-azure-portal"></a>RBAC configureren met behulp van de Azure Portal

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/) en open uw Automation-account via de pagina Automation-accounts.
2. Klik in de linkerbovenhoek op het besturings element **toegangs beheer (IAM)** . Hiermee opent u de pagina **toegangs beheer (IAM)** waar u nieuwe gebruikers, groepen en toepassingen kunt toevoegen om uw Automation-account te beheren en bestaande rollen kunt weer geven die kunnen worden geconfigureerd voor het Automation-account.
3. Klik op de **roltoewijzingen** tabblad.

   ![De knop Toegang](media/automation-role-based-access-control/automation-01-access-button.png)

#### <a name="add-a-new-user-and-assign-a-role"></a>Een nieuwe gebruiker toevoegen en een rol toewijzen

1. Klik op de pagina **toegangs beheer (IAM)** op **+ roltoewijzing toevoegen** om de pagina **roltoewijzing toevoegen** te openen, waar u een gebruiker, groep of toepassing kunt toevoegen en een rol aan de toewijzing wilt toewijzen.

2. Selecteer een rol in de lijst met beschikbare rollen. U kunt kiezen uit een van de beschik bare ingebouwde rollen die een Automation-account ondersteunt of een aangepaste rol die u mogelijk hebt gedefinieerd.

3. Typ de gebruikers naam van de gebruiker aan wie u machtigingen wilt verlenen in het veld **selecteren** . Selecteer de gebruiker in de lijst en klik op **Opslaan**.

   ![Gebruikers toevoegen](media/automation-role-based-access-control/automation-04-add-users.png)

   U ziet nu dat de gebruiker is toegevoegd aan de pagina **gebruikers** met de geselecteerde rol toegewezen

   ![Gebruikers weergeven](media/automation-role-based-access-control/automation-05-list-users.png)

   U kunt ook een rol aan de gebruiker toewijzen via de pagina **Rollen**.
4. Klik op **functies** op de pagina **toegangs beheer (IAM)** om de pagina **rollen** te openen. Via deze pagina kunt u het volgende bekijken: de naam van de rol, het aantal gebruikers en de groepen die aan die rol zijn toegewezen.

    ![Rol toewijzen vanaf pagina Gebruikers](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)

   > [!NOTE]
   > Toegangs beheer op basis van rollen kan alleen worden ingesteld in het bereik van het Automation-account en niet op een resource onder het Automation-account.

#### <a name="remove-a-user"></a>Een gebruiker verwijderen

U kunt de toegangs machtiging verwijderen voor een gebruiker die het Automation-account niet beheert of die niet langer werkt voor de organisatie. Hieronder vindt u de stappen voor het verwijderen van een gebruiker:

1. Selecteer op de pagina **toegangs beheer (IAM)** de gebruiker die u wilt verwijderen en klik op **verwijderen**.
2. Klik op de pagina met toewijzingsdetails op de knop **Verwijderen**.
3. Klik op **Ja** om het verwijderen te bevestigen.

   ![Gebruikers verwijderen](media/automation-role-based-access-control/automation-08-remove-users.png)

### <a name="configure-rbac-using-powershell"></a>RBAC configureren met Power shell

Op rollen gebaseerde toegang kan ook worden geconfigureerd voor een Automation-account met behulp van de volgende [Azure PowerShell-cmdlets](../role-based-access-control/role-assignments-powershell.md):

[Get-azurermroledefinition wordt](/previous-versions/azure/mt603792(v=azure.100)) geeft een lijst van alle RBAC-rollen die beschikbaar zijn in azure Active Directory. U kunt deze opdracht samen met de eigenschap **Naam** gebruiken om alle acties die kunnen worden uitgevoerd door een specifieke rol weer te geven.

```azurepowershell-interactive
Get-AzureRmRoleDefinition -Name 'Automation Operator'
```

Hier volgt een voor beeld van de uitvoer:

```azurepowershell
Name             : Automation Operator
Id               : d3881f73-407a-4167-8283-e981cbba0404
IsCustom         : False
Description      : Automation Operators are able to start, stop, suspend, and resume jobs
Actions          : {Microsoft.Authorization/*/read, Microsoft.Automation/automationAccounts/jobs/read, Microsoft.Automation/automationAccounts/jobs/resume/action,
                   Microsoft.Automation/automationAccounts/jobs/stop/action...}
NotActions       : {}
AssignableScopes : {/}
```

[Get-AzureRmRoleAssignment](/previous-versions/azure/mt619413(v=azure.100)) geeft een overzicht van Azure AD RBAC-roltoewijzingen voor het opgegeven bereik. Zonder parameters worden met deze opdracht alle roltoewijzingen geretourneerd die onder het abonnement zijn gemaakt. Gebruik de parameter **ExpandPrincipalGroups** om toegangstoewijzingen voor de opgegeven gebruiker weer te geven en voor de groepen waarvan de gebruiker lid is.
    **Voorbeeld:** gebruik de volgende opdracht om alle gebruikers en de bijbehorende rollen binnen een Automation-account weer te geven.

```azurepowershell-interactive
Get-AzureRMRoleAssignment -scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Hier volgt een voor beeld van de uitvoer:

```powershell
RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Automation/automationAccounts/myAutomationAccount/provid
                     ers/Microsoft.Authorization/roleAssignments/cc594d39-ac10-46c4-9505-f182a355c41f
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Automation/automationAccounts/myAutomationAccount
DisplayName        : admin@contoso.com
SignInName         : admin@contoso.com
RoleDefinitionName : Automation Operator
RoleDefinitionId   : d3881f73-407a-4167-8283-e981cbba0404
ObjectId           : 15f26a47-812d-489a-8197-3d4853558347
ObjectType         : User
```

[New-AzureRmRoleAssignment](/previous-versions/azure/mt603580(v=azure.100)) om toegang te verlenen aan gebruikers, groepen en toepassingen aan een bepaald bereik.
    **Voor beeld:** Gebruik de volgende opdracht om de rol ' Automation-operator ' toe te wijzen voor een gebruiker in het Automation-account bereik.

```azurepowershell-interactive
New-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to grant access> -RoleDefinitionName 'Automation operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Hier volgt een voor beeld van de uitvoer:

```azurepowershell
RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/myResourceGroup/Providers/Microsoft.Automation/automationAccounts/myAutomationAccount/provid
                     ers/Microsoft.Authorization/roleAssignments/25377770-561e-4496-8b4f-7cba1d6fa346
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/myResourceGroup/Providers/Microsoft.Automation/automationAccounts/myAutomationAccount
DisplayName        : admin@contoso.com
SignInName         : admin@contoso.com
RoleDefinitionName : Automation Operator
RoleDefinitionId   : d3881f73-407a-4167-8283-e981cbba0404
ObjectId           : f5ecbe87-1181-43d2-88d5-a8f5e9d8014e
ObjectType         : User
```

Gebruik [Remove-AzureRmRoleAssignment](/previous-versions/azure/mt603781(v=azure.100)) om de toegang van een opgegeven gebruiker, groep of toepassing te verwijderen uit een bepaald bereik.
    **Voor beeld:** Gebruik de volgende opdracht om de gebruiker te verwijderen uit de rol ' Automation-operator ' in het Automation-account bereik.

```azurepowershell-interactive
Remove-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to remove> -RoleDefinitionName 'Automation Operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Vervang in de voor gaande voor beelden de **aanmeldings-id**, **abonnements-id**, naam van de **resource groep**en de naam van het **Automation-account** door uw account gegevens. Kies **Ja** om te bevestigen voordat u verdergaat met het verwijderen van de roltoewijzing voor de gebruiker.

### <a name="user-experience-for-automation-operator-role---automation-account"></a>Gebruikers ervaring voor de rol van Automation-operator-Automation-account

Wanneer een gebruiker die is toegewezen aan de rol van Automation-operator in het bereik van de Automation-account, het Automation-account bekijkt waaraan ze zijn toegewezen, kan deze alleen de lijst met runbooks, runbook-taken en schema's weer geven die in het Automation-account zijn gemaakt, maar ze niet kunnen bekijken inhoudsindexdefinitie. De gebruiker kan de runbooktaak starten, stoppen, onderbreken, hervatten of plannen. De gebruiker heeft geen toegang tot andere Automation-resources, zoals configuraties, Hybrid worker-groepen of DSC-knoop punten.

![Geen toegang tot resources](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)

## <a name="configure-rbac-for-runbooks"></a>RBAC configureren voor Runbooks

Met Azure Automation kunt u RBAC toewijzen aan specifieke runbooks. Als u dit wilt doen, voert u het volgende script uit om een gebruiker toe te voegen aan een specifiek runbook. Het volgende script kan worden uitgevoerd door een Automation-account beheerder of een Tenant beheerder.

```azurepowershell-interactive
$rgName = "<Resource Group Name>" # Resource Group name for the Automation Account
$automationAccountName ="<Automation Account Name>" # Name of the Automation Account
$rbName = "<Name of Runbook>" # Name of the runbook
$userId = "<User ObjectId>" # Azure Active Directory (AAD) user's ObjectId from the directory

# Gets the Automation Account resource
$aa = Get-AzureRmResource -ResourceGroupName $rgName -ResourceType "Microsoft.Automation/automationAccounts" -ResourceName $automationAccountName

# Get the Runbook resource
$rb = Get-AzureRmResource -ResourceGroupName $rgName -ResourceType "Microsoft.Automation/automationAccounts/runbooks" -ResourceName "$automationAccountName/$rbName"

# The Automation Job Operator role only needs to be ran once per user.
New-AzureRmRoleAssignment -ObjectId $userId -RoleDefinitionName "Automation Job Operator" -Scope $aa.ResourceId

# Adds the user to the Automation Runbook Operator role to the Runbook scope
New-AzureRmRoleAssignment -ObjectId $userId -RoleDefinitionName "Automation Runbook Operator" -Scope $rb.ResourceId
```

Als de gebruiker zich eenmaal heeft vooraangemeld bij de Azure Portal, kunt u **alle resources**weer geven. In de lijst ziet u het Runbook dat ze als een **Automation-runbook-operator** zijn toegevoegd voor.

![Runbook RBAC in de portal](./media/automation-role-based-access-control/runbook-rbac.png)

### <a name="user-experience-for-automation-operator-role---runbook"></a>Gebruikers ervaring voor de rol van Automation-operator-Runbook

Wanneer een gebruiker die is toegewezen aan de rol Automation-operator in het Runbook-bereik, een Runbook weergeeft waaraan ze zijn toegewezen, kan het runbook alleen worden gestart en de runbook-taken worden weer gegeven.

![Heeft alleen toegang tot start](media/automation-role-based-access-control/automation-only-start.png)

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg [RBAC met Azure PowerShell beheren](../role-based-access-control/role-assignments-powershell.md) voor informatie over verschillende manieren om RBAC voor Azure Automation te configureren.
* Zie [Een runbook starten](automation-starting-a-runbook.md) voor informatie over verschillende manieren om een runbook te starten.
* Raadpleeg [Azure Automation-runbooktypen](automation-runbook-types.md) voor informatie over verschillende typen runbooks

