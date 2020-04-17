---
title: Op rollen gebaseerd toegangsbeheer in Azure Automation
description: Met op rollen gebaseerd toegangsbeheer (RBAC) beschikt u over toegangsbeheer voor Azure-resources. In dit artikel wordt beschreven hoe u RBAC instelt in Azure Automation.
keywords: automatisering rbac, rolgebaseerde toegangscontrole, azure rbac
services: automation
ms.subservice: shared-capabilities
ms.date: 05/17/2018
ms.topic: conceptual
ms.openlocfilehash: a49f2596df91c44deafa1be83483f8972e223742
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535567"
---
# <a name="role-based-access-control-in-azure-automation"></a>Op rollen gebaseerd toegangsbeheer in Azure Automation

Met op rollen gebaseerd toegangsbeheer (RBAC) beschikt u over toegangsbeheer voor Azure-resources. Met [RBAC](../role-based-access-control/overview.md)u taken binnen uw team scheiden en alleen de hoeveelheid toegang verlenen aan gebruikers, groepen en toepassingen die ze nodig hebben om hun taken uit te voeren. U op rollen gebaseerde toegang verlenen aan gebruikers met behulp van de Azure-portal, Azure Command-Line-hulpprogramma's of Azure Management API's.

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor installatie-instructies voor az-modules op uw hybride runbookworker. Voor uw Automatiseringsaccount u uw modules bijwerken naar de nieuwste versie met [Azure PowerShell-modules bijwerken in Azure Automation.](automation-update-azure-modules.md)

## <a name="roles-in-automation-accounts"></a>Rollen in automatiseringsaccounts

In Azure Automation wordt toegang verleend door de juiste RBAC-rol toe te wijzen aan gebruikers, groepen en toepassingen bij het Automation-accountbereik. Hieronder vindt u de ingebouwde rollen die worden ondersteund met een Automation-account:

| **Rol** | **Beschrijving** |
|:--- |:--- |
| Eigenaar |De rol Eigenaar geeft toegang tot alle bronnen en acties binnen een Automatiseringsaccount, inclusief het bieden van toegang aan andere gebruikers, groepen en toepassingen om het automatiseringsaccount te beheren. |
| Inzender |De rol van Bijdrager maakt het mogelijk om alles te beheren, behalve de toegangsrechten van andere gebruikers te wijzigen naar een Automation-account. |
| Lezer |Met de rol van Lezer kunt u alle resources in een Automation-account bekijken, maar niets wijzigen. |
| Automation-operator |Met de rol Automation Operator u de naam en eigenschappen van runbook weergeven en taken maken en beheren voor alle runbooks in een Automatiseringsaccount. Deze rol is handig als u wilt voorkomen dat uw automatiseringsaccountbronnen zoals referenties en runbooks worden bekeken of gewijzigd, maar leden van uw organisatie deze runbooks toch kunnen uitvoeren. |
|Operator van automatiseringswerktaak|Met de rol Automatiseringstaakoperator u taken maken en beheren voor alle runbooks in een Automatiseringsaccount.|
|Operator voor automatiseringsrunbook|Met de rol Automation Runbook Operator u de naam en eigenschappen van een runbook weergeven.|
| Inzender van Log Analytics | Met de rol Van De bijdrager van het Analysevan logboek u alle controlegegevens lezen en controle-instellingen bewerken. De instellingen voor het bewerken van bewakingscontrole omvatten het toevoegen van de VM-extensie aan VM's, het lezen van opslagaccountsleutels om het verzamelen van logboeken uit Azure-opslag te kunnen configureren, het maken en configureren van Automatiseringsaccounts, het toevoegen van oplossingen en het configureren van Azure-diagnoses op alle Azure-bronnen.|
| Lezer van Log Analytics | Met de rol Log Analytics Reader u alle bewakingsgegevens bekijken en doorzoeken en bewakingsinstellingen bekijken. Dit omvat het bekijken van de configuratie van Azure-diagnose op alle Azure-resources. |
| Monitorbijdrager | Met de rol Monitorbijdrager u alle bewakingsgegevens lezen en bewakingsinstellingen bijwerken.|
| Monitoring Reader | Met de rol Monitoring Reader u alle bewakingsgegevens lezen. |
| Beheerder van gebruikerstoegang |De beheerdersrol gebruiker toegang kunt u gebruikerstoegang tot Azure Automation-accounts beheren. |

## <a name="role-permissions"></a>Functiemachtigingen

In de volgende tabellen worden de specifieke machtigingen beschreven die aan elke rol worden gegeven. Dit kunnen acties zijn, die machtigingen geven, en NotActions, die deze beperken.

### <a name="owner"></a>Eigenaar

Een eigenaar kan alles beheren, inclusief toegang. In de volgende tabel worden de machtigingen voor de rol weergegeven:

|Acties|Beschrijving|
|---|---|
|Microsoft.Automation/automationAccounts/|Maak en beheer resources van alle typen.|

### <a name="contributor"></a>Inzender

Een inzender kan alles beheren behalve toegang. In de volgende tabel worden de machtigingen weergegeven die zijn verleend en geweigerd voor de rol:

|**Acties**  |**Beschrijving**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/|Bronnen van alle typen maken en beheren|
|**Geen acties**||
|Microsoft.Authorization/*/Delete| Rollen en roltoewijzingen verwijderen.       |
|Microsoft.Authorization/*/Write     |  Maak rollen en roltoewijzingen.       |
|Microsoft.Authorization/elevateAccess/Action    | Ontkent de mogelijkheid om een gebruikerstoegangsbeheerder te maken.       |

### <a name="reader"></a>Lezer

Een reader kan alle bronnen in een Automatiseringsaccount bekijken, maar kan geen wijzigingen aanbrengen.

|**Acties**  |**Beschrijving**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/gelezen|Bekijk alle resources in een Automatiseringsaccount. |

### <a name="automation-operator"></a>Automation-operator

Een automatiseringsoperator kan taken maken en beheren en runbooknamen en -eigenschappen lezen voor alle runbooks in een Automatiseringsaccount.  Opmerking: Als u de operatortoegang tot afzonderlijke runbooks wilt beheren, stel deze rol dan niet in en gebruik in plaats daarvan de rollen 'Automatiseringstaakoperator' en 'Automation Runbook Operator' in combinatie. In de volgende tabel worden de machtigingen voor de rol weergegeven:

|**Acties**  |**Beschrijving**  |
|---------|---------|
|Microsoft.Authorization/*/gelezen|Lees autorisatie.|
|Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read|Lees De bronnen van hybride runbookworker.|
|Microsoft.Automation/automationAccounts/jobs/read|Lijst taken van het runbook.|
|Microsoft.Automation/automationAccounts/taken/hervatten/actie|Hervat een taak die is onderbroken.|
|Microsoft.Automation/automationAccounts/taken/stop/action|Een lopende taak annuleren.|
|Microsoft.Automation/automationAccounts/jobs/streams/read|Lees de werkstromen en uitvoer.|
|Microsoft.Automation/automationAccounts/jobs/output/read|Krijg de output van een taak.|
|Microsoft.Automation/automationAccounts/taken/suspend/action|Een lopende taak onderbreken.|
|Microsoft.Automation/automationAccounts/jobs/write|Maak banen.|
|Microsoft.Automation/automationAccounts/jobSchedules/read|Ontvang een azure automation-taakschema.|
|Microsoft.Automation/automationAccounts/jobSchedules/write|Maak een azure automation-taakschema.|
|Microsoft.Automation/automationAccounts/linkedWorkspace/read|Download de werkruimte die is gekoppeld aan het automatiseringsaccount.|
|Microsoft.Automation/automationAccounts/gelezen|Een Azure Automation-account openen.|
|Microsoft.Automation/automationAccounts/runbooks/read|Download een Azure Automation-runbook.|
|Microsoft.Automation/automationAccounts/schema's/gelezen|Ontvang een Azure Automation-planningsplanningsitem.|
|Microsoft.Automation/automationAccounts/schema's/schrijven|Een Azure Automation-planningsplanningsitem maken of bijwerken.|
|Microsoft.Resources/abonnementen/resourceGroepen/gelezen      |Rollen en roltoewijzingen lezen.         |
|Microsoft.Resources/implementaties/*      |Implementaties van resourcegroepen maken en beheren.         |
|Microsoft.Insights/alertRegels/*      | Waarschuwingsregels maken en beheren.        |
|Microsoft.Support/* |Ondersteuningstickets maken en beheren.|

### <a name="automation-job-operator"></a>Operator van automatiseringswerktaak

Een rol van automatiseringstaakoperator wordt verleend op het accountbereik Automatisering.Hierdoor kunnen de operatormachtigingen taken maken en beheren voor alle runbooks in het account. In de volgende tabel worden de machtigingen voor de rol weergegeven:

|**Acties**  |**Beschrijving**  |
|---------|---------|
|Microsoft.Authorization/*/gelezen|Lees autorisatie.|
|Microsoft.Automation/automationAccounts/jobs/read|Lijst taken van het runbook.|
|Microsoft.Automation/automationAccounts/taken/hervatten/actie|Hervat een taak die is onderbroken.|
|Microsoft.Automation/automationAccounts/taken/stop/action|Een lopende taak annuleren.|
|Microsoft.Automation/automationAccounts/jobs/streams/read|Lees de werkstromen en uitvoer.|
|Microsoft.Automation/automationAccounts/taken/suspend/action|Een lopende taak onderbreken.|
|Microsoft.Automation/automationAccounts/jobs/write|Maak banen.|
|Microsoft.Resources/abonnementen/resourceGroepen/gelezen      |  Rollen en roltoewijzingen lezen.       |
|Microsoft.Resources/implementaties/*      |Implementaties van resourcegroepen maken en beheren.         |
|Microsoft.Insights/alertRegels/*      | Waarschuwingsregels maken en beheren.        |
|Microsoft.Support/* |Ondersteuningstickets maken en beheren.|

### <a name="automation-runbook-operator"></a>Operator voor automatiseringsrunbook

Een rol van automation Runbook Operator wordt verleend op het bereik van runbook. Een operator van een automatiseringsrunboek kan de naam en eigenschappen van het runbook weergeven.Deze rol in combinatie met de rol 'Automation Job Operator' stelt de operator in staat om ook taken voor het runbook te creëren en te beheren. In de volgende tabel worden de machtigingen voor de rol weergegeven:

|**Acties**  |**Beschrijving**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/runbooks/read     | Vermeld de runbooks.        |
|Microsoft.Authorization/*/gelezen      | Lees autorisatie.        |
|Microsoft.Resources/abonnementen/resourceGroepen/gelezen      |Rollen en roltoewijzingen lezen.         |
|Microsoft.Resources/implementaties/*      | Implementaties van resourcegroepen maken en beheren.         |
|Microsoft.Insights/alertRegels/*      | Waarschuwingsregels maken en beheren.        |
|Microsoft.Support/*      | Ondersteuningstickets maken en beheren.        |

### <a name="log-analytics-contributor"></a>Inzender van Log Analytics

Een medewerker van Log Analytics kan alle bewakingsgegevens lezen en bewakingsinstellingen bewerken. De bewakingsinstellingen voor bewerken omvatten het toevoegen van de VM-extensie aan VM's; leesopslagaccountsleutels om verzameling logboeken uit Azure Storage te kunnen configureren; Het maken en configureren van Automatiseringsaccounts; het toevoegen van oplossingen; en azure-diagnose configureren op alle Azure-resources. In de volgende tabel worden de machtigingen voor de rol weergegeven:

|**Acties**  |**Beschrijving**  |
|---------|---------|
|*/lezen|Lees bronnen van alle soorten, behalve geheimen.|
|Microsoft.Automation/automationAccounts/*|Automatiseringsaccounts beheren.|
|Microsoft.ClassicCompute/virtualMachines/extensies/*|Virtuele machine-extensies maken en beheren.|
|Microsoft.ClassicStorage/storageAccounts/listKeys/action|Lijst klassieke opslagaccountsleutels.|
|Microsoft.Compute/virtualMachines/extensies/*|Klassieke virtuele machine-extensies maken en beheren.|
|Microsoft.Insights/alertRegels/*|Waarschuwingsregels lezen/schrijven/verwijderen.|
|Microsoft.Insights/diagnosticSettings/*|Diagnostische instellingen lezen/schrijven/verwijderen.|
|Microsoft.OperationalInsights/*|Azure Monitor-logboeken beheren.|
|Microsoft.OperationsManagement/*|Beheer oplossingen in werkruimten.|
|Microsoft.Resources/implementaties/*|Implementaties van resourcegroepen maken en beheren.|
|Microsoft.Resources/abonnementen/resourcegroepen/implementaties/*|Implementaties van resourcegroepen maken en beheren.|
|Microsoft.Storage/storageAccounts/listKeys/action|Lijst opslagaccountsleutels.|
|Microsoft.Support/*|Ondersteuningstickets maken en beheren.|

### <a name="log-analytics-reader"></a>Lezer van Log Analytics

Een logboekanalyselezer kan alle bewakingsgegevens bekijken en doorzoeken en bewakingsinstellingen weergeven, inclusief het bekijken van de configuratie van Azure-diagnose sop alle Azure-bronnen. In de volgende tabel worden de machtigingen weergegeven die voor de rol zijn verleend of geweigerd:

|**Acties**  |**Beschrijving**  |
|---------|---------|
|*/lezen|Lees bronnen van alle soorten, behalve geheimen.|
|Microsoft.OperationalInsights/workspaces/analytics/query/action|Query's beheren in Azure Monitor-logboeken.|
|Microsoft.OperationalInsights/workspaces/zoeken/actie|Zoeken in Azure Monitor-logboekgegevens.|
|Microsoft.Support/*|Ondersteuningstickets maken en beheren.|
|**Geen acties**| |
|Microsoft.OperationalInsights/workspaces/sharedKeys/read|Niet in staat om de gedeelde toegangssleutels te lezen.|

### <a name="monitoring-contributor"></a>Monitorbijdrager

Een monitormedewerker kan alle bewakingsgegevens en updatecontrole-instellingen lezen. In de volgende tabel worden de machtigingen voor de rol weergegeven:

|**Acties**  |**Beschrijving**  |
|---------|---------|
|*/lezen|Lees bronnen van alle soorten, behalve geheimen.|
|Microsoft.AlertsBeheer/waarschuwingen/*|Waarschuwingen beheren.|
|Microsoft.AlertsManagement/alertsSamenvatting/*|Het dashboard Waarschuwing beheren.|
|Microsoft.Insights/Waarschuwingsregels/*|Waarschuwingsregels beheren.|
|Microsoft.Insights/componenten/*|De componenten van Application Insights beheren.|
|Microsoft.Insights/DiagnosticSettings/*|Diagnostische instellingen beheren.|
|Microsoft.Insights/eventtypes/*|Activiteitslogboekgebeurtenissen (beheergebeurtenissen) in een abonnement weergeven. Deze toestemming is van toepassing op zowel programmatische als portaltoegang tot het activiteitenlogboek.|
|Microsoft.Insights/LogDefinities/*|Deze toestemming is nodig voor gebruikers die via de portal toegang nodig hebben tot activiteitslogboeken. Lijstlogboekcategorieën in activiteitenlogboek.|
|Microsoft.Insights/MetricDefinitions/*|Lees metrische definities (lijst met beschikbare metrische typen voor een resource).|
|Microsoft.Insights/Metrics/*|Lees statistieken voor een resource.|
|Microsoft.Insights/Register/Actie|Registreer de Microsoft.Insights-provider.|
|Microsoft.Insights/webtests/*|Webtests voor Application Insights beheren.|
|Microsoft.OperationalInsights/workspaces/intelligencepacks/*|Azure Monitor-logboekenlogboeken beheren.|
|Microsoft.OperationalInsights/workspaces/savedSearches/*|Azure Monitor-logboeken logboeken opgeslagen zoekopdrachten beheren.|
|Microsoft.OperationalInsights/workspaces/zoeken/actie|Zoeken in Log Analytics-werkruimten.|
|Microsoft.OperationalInsights/workspaces/sharedKeys/action|Lijstsleutels voor een Log Analytics-werkruimte.|
|Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*|Azure Monitor beheren logboeken opslaginzichtconfiguraties.|
|Microsoft.Support/*|Ondersteuningstickets maken en beheren.|
|Microsoft.WorkloadMonitor/workloads/*|Workloads beheren.|

### <a name="monitoring-reader"></a>Monitoring Reader

Een monitoringreader kan alle bewakingsgegevens lezen. In de volgende tabel worden de machtigingen voor de rol weergegeven:

|**Acties**  |**Beschrijving**  |
|---------|---------|
|*/lezen|Lees bronnen van alle soorten, behalve geheimen.|
|Microsoft.OperationalInsights/workspaces/zoeken/actie|Zoeken in Log Analytics-werkruimten.|
|Microsoft.Support/*|Ondersteuningstickets maken en beheren|

### <a name="user-access-administrator"></a>Beheerder van gebruikerstoegang

Een gebruikerstoegangsbeheerder kan gebruikerstoegang tot Azure-bronnen beheren. In de volgende tabel worden de machtigingen voor de rol weergegeven:

|**Acties**  |**Beschrijving**  |
|---------|---------|
|*/lezen|Alle bronnen lezen|
|Microsoft.Authorization/*|Autorisatie beheren|
|Microsoft.Support/*|Ondersteuningstickets maken en beheren|

## <a name="onboarding-permissions"></a>Onboarding-machtigingen

In de volgende secties worden de minimaal vereiste machtigingen beschreven die nodig zijn voor het inwerken van virtuele machines voor de oplossingen voor het bijhouden of bijwerken van wijzigingen.

### <a name="permissions-for-onboarding-from-a-vm"></a>Machtigingen voor onboarding van een vm

|**Actie**  |**Machtiging**  |**Minimumbereik**  |
|---------|---------|---------|
|Nieuwe implementatie schrijven      | Microsoft.Resources/implementaties/*          |Abonnement          |
|Nieuwe resourcegroep schrijven      | Microsoft.Resources/abonnementen/resourceGroepen/schrijven        | Abonnement          |
|Nieuwe standaardwerkruimte maken      | Microsoft.OperationalInsights/workspaces/write         | Resourcegroep         |
|Nieuw account maken      |  Microsoft.Automation/automationAccounts/write        |Resourcegroep         |
|Werkruimte en account koppelen      |Microsoft.OperationalInsights/workspaces/write</br>Microsoft.Automation/automationAccounts/gelezen|Werkruimte</br>Automation-account
|MMA-extensie maken      | Microsoft.Compute/virtualMachines/write         | Virtuele machine         |
|Opgeslagen zoekopdracht maken      | Microsoft.OperationalInsights/workspaces/write          | Werkruimte         |
|Scope config maken      | Microsoft.OperationalInsights/workspaces/write          | Werkruimte         |
|Onboarding state check - Werkruimte lezen      | Microsoft.OperationalInsights/workspaces/lezen         | Werkruimte         |
|Onboarding state check - De eigenschap Linked Workspace van account lezen     | Microsoft.Automation/automationAccounts/gelezen      | Automation-account        |
|Onboarding state check - Lees oplossing      | Microsoft.OperationalInsights/workspaces/intelligencepacks/read          | Oplossing         |
|Onboarding state check - Vm lezen      | Microsoft.Compute/virtualMachines/lezen         | Virtuele machine         |
|Onboarding state check - Account lezen      | Microsoft.Automation/automationAccounts/gelezen  |  Automation-account   |
| Onboarding workspace check voor VM<sup>1</sup>       | Microsoft.OperationalInsights/workspaces/lezen         | Abonnement         |
| De Log Analytics-provider registreren |Microsoft.Insights/register/actie | Abonnement|

<sup>1</sup> Deze toestemming is nodig om aan boord te gaan via de VM-portalervaring.

### <a name="permissions-for-onboarding-from-automation-account"></a>Machtigingen voor onboarding vanuit automatiseringsaccount

|**Actie**  |**Machtiging** |**Minimumbereik**  |
|---------|---------|---------|
|Nieuwe implementatie maken     | Microsoft.Resources/implementaties/*        | Abonnement         |
|Nieuwe resourcegroep maken     | Microsoft.Resources/abonnementen/resourceGroepen/schrijven         | Abonnement        |
|AutomationOnboarding blade - Creëer nieuwe werkruimte     |Microsoft.OperationalInsights/workspaces/write           | Resourcegroep        |
|AutomationOnboarding blade - lees gekoppelde werkruimte     | Microsoft.Automation/automationAccounts/gelezen        | Automation-account       |
|AutomationOnboarding blade - leesoplossing     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read         | Oplossing        |
|AutomationOnboarding blade - lees werkruimte     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read        | Werkruimte        |
|Koppeling maken voor werkruimte en account     | Microsoft.OperationalInsights/workspaces/write        | Werkruimte        |
|Schrijf account voor schoenendoos      | Microsoft.Automation/automationAccounts/write        | Account        |
|Opgeslagen zoeken maken/bewerken     | Microsoft.OperationalInsights/workspaces/write        | Werkruimte        |
|Bereikconfig maken/bewerken     | Microsoft.OperationalInsights/workspaces/write        | Werkruimte        |
| De Log Analytics-provider registreren |Microsoft.Insights/register/actie | Abonnement|
|**Stap 2 - Meerdere VM's aan boord**     |         |         |
|VMOnboarding blade - MMA-extensie maken     | Microsoft.Compute/virtualMachines/write           | Virtuele machine        |
|Opgeslagen zoeken maken/ bewerken     | Microsoft.OperationalInsights/workspaces/write           | Werkruimte        |
|Scope config maken / bewerken  | Microsoft.OperationalInsights/workspaces/write   | Werkruimte|

## <a name="update-management-permissions"></a>Beheermachtigingen bijwerken

Updatebeheer bereikt meerdere services om de service te bieden. In de volgende tabel worden de machtigingen weergegeven die nodig zijn voor het beheren van implementaties van updatebeheer:

|**Resource**  |**Rol**  |**Bereik**  |
|---------|---------|---------|
|Automation-account     | Inzender van Log Analytics       | Automation-account        |
|Automation-account    | Inzender voor virtuele machines        | Resourcegroep voor het account        |
|Log Analytics-werkruimte     | Inzender van Log Analytics| Log Analytics-werkruimte        |
|Log Analytics-werkruimte |Lezer van Log Analytics| Abonnement|
|Oplossing     |Inzender van Log Analytics         | Oplossing|
|Virtuele machine     | Inzender voor virtuele machines        | Virtuele machine        |

## <a name="configure-rbac-for-your-automation-account"></a>RBAC configureren voor uw Automatiseringsaccount

In de volgende sectie ziet u hoe u RBAC configureert op uw Automatiseringsaccount via de [Azure-portal](#configure-rbac-using-the-azure-portal) en [PowerShell.](#configure-rbac-using-powershell)

### <a name="configure-rbac-using-the-azure-portal"></a>RBAC configureren met de Azure-portal

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/) en open uw Automation-account via de pagina Automation-accounts.
2. Klik op **Toegangsbesturingselement (IAM)** om de pagina Toegangsbesturingselement (IAM) te openen. U deze pagina gebruiken om nieuwe gebruikers, groepen en toepassingen toe te voegen om uw Automatiseringsaccount te beheren en bestaande rollen weer te geven die configureerbaar zijn voor het automatiseringsaccount.
3. Klik op het tabblad **Roltoewijzingen**.

   ![De knop Toegang](media/automation-role-based-access-control/automation-01-access-button.png)

#### <a name="add-a-new-user-and-assign-a-role"></a>Een nieuwe gebruiker toevoegen en een rol toewijzen

1. Klik op de pagina Toegangsbesturingselement (IAM) op **+ Roltoewijzing toevoegen**. Met deze actie wordt de pagina Roltoewijzing toevoegen geopend waar u een gebruiker, groep of toepassing toevoegen en een overeenkomstige rol toewijzen.

2. Selecteer een rol in de lijst met beschikbare rollen. U een van de beschikbare ingebouwde rollen kiezen die een Automatiseringsaccount ondersteunt of een aangepaste rol die u mogelijk hebt gedefinieerd.

3. Typ de naam van de gebruiker aan wie u machtigingen wilt geven in het veld **Selecteren.** Kies de gebruiker in de lijst en klik op **Opslaan**.

   ![Gebruikers toevoegen](media/automation-role-based-access-control/automation-04-add-users.png)

   Nu moet u de gebruiker zien die is toegevoegd aan de pagina Gebruikers, waarbij de geselecteerde rol is toegewezen.

   ![Gebruikers weergeven](media/automation-role-based-access-control/automation-05-list-users.png)

   U kunt ook een rol aan de gebruiker toewijzen via de pagina Rollen.
4. Klik **op Rollen** op de pagina Toegangsbeheer (IAM) om de pagina Rollen te openen. U de naam van de rol en het aantal gebruikers en groepen weergeven dat aan die rol is toegewezen.

    ![Rol toewijzen vanaf pagina Gebruikers](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)

   > [!NOTE]
   > U alleen op rollen gebaseerd toegangsbeheer instellen op het accountbereik Automatisering en niet op een resource onder het automatiseringsaccount.

#### <a name="remove-a-user"></a>Een gebruiker verwijderen

U de toegangsmachtiging verwijderen voor een gebruiker die het Automatiseringsaccount niet beheert of die niet meer voor de organisatie werkt. Hieronder vindt u de stappen voor het verwijderen van een gebruiker:

1. Selecteer op de pagina Toegangsbesturingselement (IAM) de gebruiker die u wilt verwijderen en klik op **Verwijderen**.
2. Klik op de pagina met toewijzingsdetails op de knop **Verwijderen**.
3. Klik op **Ja** om het verwijderen te bevestigen.

   ![Gebruikers verwijderen](media/automation-role-based-access-control/automation-08-remove-users.png)

### <a name="configure-rbac-using-powershell"></a>RBAC configureren met PowerShell

U ook op rollen gebaseerde toegang tot een Automatiseringsaccount configureren met de volgende [Azure PowerShell-cmdlets:](../role-based-access-control/role-assignments-powershell.md)

[Get-AzRoleDefinition](https://docs.microsoft.com/powershell/module/Az.Resources/Get-AzRoleDefinition?view=azps-3.7.0) bevat alle RBAC-rollen die beschikbaar zijn in Azure Active Directory. U deze cmdlet `Name` met de parameter gebruiken om alle acties weer te geven die een specifieke rol kan uitvoeren.

```azurepowershell-interactive
Get-AzRoleDefinition -Name 'Automation Operator'
```

Het volgende is de voorbeelduitvoer:

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

[Get-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/get-azroleassignment?view=azps-3.7.0) bevat Azure AD RBAC-roltoewijzingen op het opgegeven bereik. Zonder parameters retourneert deze cmdlet alle roltoewijzingen die onder het abonnement zijn gemaakt. Gebruik `ExpandPrincipalGroups` de parameter om toegangstoewijzingen voor de opgegeven gebruiker en de groepen waartoe de gebruiker behoort, weer te geven.

**Voorbeeld:** Gebruik de volgende cmdlet om alle gebruikers en hun rollen binnen een Automation-account weer te geven.

```azurepowershell-interactive
Get-AzRoleAssignment -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Het volgende is de voorbeelduitvoer:

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

Gebruik [Nieuw-AzRoleAssignment](https://docs.microsoft.com/powershell/module/Az.Resources/New-AzRoleAssignment?view=azps-3.7.0) om toegang toe te wijzen aan gebruikers, groepen en toepassingen aan een bepaald bereik.
    
**Voorbeeld:** Gebruik de volgende opdracht om de rol 'Automatiseringsoperator' toe te wijzen aan een gebruiker in het bereik van het automatiseringsaccount.

```azurepowershell-interactive
New-AzRoleAssignment -SignInName <sign-in Id of a user you wish to grant access> -RoleDefinitionName 'Automation operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Het volgende is de voorbeelduitvoer:

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

Gebruik [Remove-AzRoleAssignment](https://docs.microsoft.com/powershell/module/Az.Resources/Remove-AzRoleAssignment?view=azps-3.7.0) om de toegang van een bepaalde gebruiker, groep of toepassing uit een bepaald bereik te verwijderen.

**Voorbeeld:** Gebruik de volgende opdracht om de gebruiker te verwijderen uit de rol Automation Operator in het bereik van het automatiseringsaccount.

```azurepowershell-interactive
Remove-AzRoleAssignment -SignInName <sign-in Id of a user you wish to remove> -RoleDefinitionName 'Automation Operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Vervang in het voorgaande `SubscriptionID` `Resource Group Name`voorbeeld `Automation account name` `sign-in ID of a user you wish to remove`, , en met uw accountgegevens. Kies **ja** wanneer u wordt gevraagd om te bevestigen voordat u verder gaat met het verwijderen van toewijzingen van gebruikersrollen.

### <a name="user-experience-for-automation-operator-role---automation-account"></a>Gebruikerservaring voor de rol Automation Operator - Automatiseringsaccount

Wanneer een gebruiker die is toegewezen aan de rol Automatiseringsoperator op het automatiseringsaccountbereik het automatiseringsaccount weergeeft waaraan hij/zij is toegewezen, kan de gebruiker alleen de lijst met runbooks, runbook-taken en planningen bekijken die zijn gemaakt in het automatiseringsaccount. Deze gebruiker kan de definities van deze items niet bekijken. De gebruiker kan de runbook-taak starten, stoppen, onderbreken, hervatten of plannen. De gebruiker heeft echter geen toegang tot andere automatiseringsbronnen, zoals configuraties, hybride werknemersgroepen of DSC-knooppunten.

![Geen toegang tot bronnen](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)

## <a name="configure-rbac-for-runbooks"></a>RBAC configureren voor runbooks

Met Azure Automation u RBAC toewijzen aan specifieke runbooks. Voer hiervoor het volgende script uit om een gebruiker toe te voegen aan een specifiek runbook. Een beheerder van een automatiseringsaccount of een tenantbeheerder kan dit script uitvoeren.

```azurepowershell-interactive
$rgName = "<Resource Group Name>" # Resource Group name for the Automation account
$automationAccountName ="<Automation account name>" # Name of the Automation account
$rbName = "<Name of Runbook>" # Name of the runbook
$userId = "<User ObjectId>" # Azure Active Directory (AAD) user's ObjectId from the directory

# Gets the Automation account resource
$aa = Get-AzResource -ResourceGroupName $rgName -ResourceType "Microsoft.Automation/automationAccounts" -ResourceName $automationAccountName

# Get the Runbook resource
$rb = Get-AzResource -ResourceGroupName $rgName -ResourceType "Microsoft.Automation/automationAccounts/runbooks" -ResourceName "$automationAccountName/$rbName"

# The Automation Job Operator role only needs to be run once per user.
New-AzRoleAssignment -ObjectId $userId -RoleDefinitionName "Automation Job Operator" -Scope $aa.ResourceId

# Adds the user to the Automation Runbook Operator role to the Runbook scope
New-AzRoleAssignment -ObjectId $userId -RoleDefinitionName "Automation Runbook Operator" -Scope $rb.ResourceId
```

Zodra het script is uitgevoerd, laat de gebruiker zich aanmelden bij de Azure-portal en selecteert u **Alle bronnen**. In de lijst kan de gebruiker het runbook zien waarvoor hij/zij is toegevoegd als operator automation runbook.

![Runbook RBAC in de portal](./media/automation-role-based-access-control/runbook-rbac.png)

### <a name="user-experience-for-automation-operator-role---runbook"></a>Gebruikerservaring voor de operatorrol van automatisering - Runbook

Wanneer een gebruiker die is toegewezen aan de rol Automatiseringsoperator in het bereik van runbook een toegewezen runbook weerziet, kan de gebruiker alleen het runbook starten en de runbook-taken weergeven.

![Heeft alleen toegang om te starten](media/automation-role-based-access-control/automation-only-start.png)

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg [RBAC beheren met Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)voor informatie over manieren om RBAC voor Azure Automation te configureren.
* Zie [Een runbook](automation-starting-a-runbook.md)starten voor meer informatie over manieren om een runbook te starten.
* Raadpleeg Azure [Automation runbook-typen](automation-runbook-types.md)voor informatie over runbook-typen .