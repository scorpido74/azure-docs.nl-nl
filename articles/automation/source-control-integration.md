---
title: Integratie van broncode beheer in Azure Automation
description: In dit artikel wordt de integratie van broncode beheer met GitHub in Azure Automation beschreven.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 12/10/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4e5e0135e4ab56d319c78b7daeab9dd2e9d101f9
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74996507"
---
# <a name="source-control-integration-in-azure-automation"></a>Integratie van broncodebeheer in Azure Automation

Met broncode beheer kunt u uw runbooks in uw Automation-account up-to-date houden met uw scripts in uw opslag plaats voor GitHub of Azure opslag plaatsen-bron beheer. Met broncode beheer kunt u eenvoudig samen werken met uw team, wijzigingen bijhouden en terugkeren naar eerdere versies van uw runbooks. Met broncode beheer kunt u bijvoorbeeld verschillende vertakkingen in broncode beheer synchroniseren met uw ontwikkelings-, test-of productie Automation-accounts. Zo kunt u eenvoudig code promoten die in uw ontwikkel omgeving is getest op uw productie Automation-account. De integratie van broncode beheer met Automation ondersteunt één richtings synchronisatie vanuit uw opslag plaats voor bron beheer.

Azure Automation ondersteunt drie typen broncode beheer:

* GitHub
* Azure-opslag plaatsen (Git)
* Azure Repos (TFVC)

## <a name="pre-requisites"></a>Vereisten

* Een broncode beheer bibliotheek (GitHub of Azure opslag plaatsen)
* Een [uitvoeren als-account](manage-runas-account.md)
* Zorg ervoor dat u beschikt over de [nieuwste Azure-modules](automation-update-azure-modules.md) in uw Automation-account, inclusief de module **AzureRM. profile** . 

> [!NOTE]
> Synchronisatie taken van broncode beheer worden uitgevoerd onder het Automation-account van de gebruiker en worden gefactureerd tegen hetzelfde aantal als andere Automation-taken.

## <a name="configure-source-control---azure-portal"></a>Broncode beheer configureren-Azure Portal

Selecteer in uw Automation-account **broncode beheer** en klik op **+ toevoegen**

![Broncode beheer selecteren](./media/source-control-integration/select-source-control.png)

Kies **broncode beheer type**en klik op **verifiëren**. Een browser venster wordt geopend en u wordt gevraagd om u aan te melden. Volg de aanwijzingen om de verificatie te volt ooien.

Vul op de pagina **overzicht van broncode beheer** de gegevens in en klik op **Opslaan**. De volgende tabel bevat een beschrijving van de beschik bare velden.

|Eigenschap  |Beschrijving  |
|---------|---------|
|Naam van broncode beheer     | Een beschrijvende naam voor het broncode beheer. *Deze naam mag alleen letters en cijfers bevatten.*        |
|Type broncode beheer     | Het type bron beheer bron. De volgende opties zijn beschikbaar:</br> GitHub</br>Azure-opslag plaatsen (Git)</br> Azure Repos (TFVC)        |
|Opslagplaats     | De naam van de opslag plaats of het project. De eerste 200 opslag plaatsen worden geretourneerd. Als u wilt zoeken naar een opslag plaats, typt u de naam in het veld en klikt u op **zoeken op github**.|
|Branch     | De vertakking waaruit de bron bestanden moeten worden gehaald. Vertakkings doelen zijn niet beschikbaar voor het TFVC-broncode beheer type.          |
|Mappad     | De map die de runbooks bevat die moeten worden gesynchroniseerd. Voor beeld:/Runbooks </br>*Alleen runbooks in de opgegeven map worden gesynchroniseerd. Recursie wordt niet ondersteund.*        |
|Automatische synchronisatie<sup>1</sup>     | Hiermee schakelt u automatische synchronisatie in of uit wanneer een door Voer wordt gemaakt in de bron beheer opslagplaats         |
|Runbook publiceren     | Als deze functie is ingesteld op **on**, wordt automatisch gepubliceerd nadat runbooks zijn gesynchroniseerd vanuit broncode beheer.         |
|Beschrijving     | Een tekst veld om aanvullende details op te geven        |

<sup>1</sup> als u automatische synchronisatie wilt inschakelen bij het configureren van de integratie van broncode beheer met Azure opslag plaatsen, moet u een project beheerder zijn.

![Samen vatting van broncode beheer](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> Uw aanmelding voor uw opslag plaats voor broncode beheer kan afwijken van uw aanmeldings gegevens voor de Azure Portal. Zorg ervoor dat u bent aangemeld met het juiste account voor de bron beheer opslagplaats bij het configureren van broncode beheer. Als er een twijfel is, opent u een nieuw tabblad in uw browser en meldt u zich af bij visualstudio.com of github.com en probeert u opnieuw verbinding te maken met broncode beheer.

## <a name="configure-source-control---powershell"></a>Broncode beheer configureren-Power shell

U kunt Power shell ook gebruiken voor het configureren van broncode beheer in Azure Automation. Voor het configureren van broncode beheer met de Power shell-cmdlets is een persoonlijk toegangs token (PAT) vereist. U gebruikt de [New-AzureRmAutomationSourceControl](/powershell/module/AzureRM.Automation/New-AzureRmAutomationSourceControl) om de broncode beheer verbinding te maken. Voor de cmdlet is een veilige teken reeks van het persoonlijke toegangs token vereist. Zie [ConvertTo](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-6)voor meer informatie over het maken van een veilige teken reeks.

### <a name="azure-repos-git"></a>Azure-opslag plaatsen (Git)

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCReposGit -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

### <a name="azure-repos-tfvc"></a>Azure Repos (TFVC)

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCReposTFVC -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_versionControl -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

### <a name="github"></a>GitHub

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

### <a name="personal-access-token-permissions"></a>Machtigingen voor persoonlijke toegangs tokens

Voor broncode beheer zijn enkele minimale machtigingen vereist voor persoonlijke toegangs tokens. De volgende tabellen bevatten de minimale machtigingen die zijn vereist voor GitHub en Azure opslag plaatsen.

#### <a name="github"></a>GitHub

Voor meer informatie over het maken van een persoonlijk toegangs token in GitHub gaat u naar [een persoonlijk toegangs token maken voor de opdracht regel](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).

|Scope  |Beschrijving  |
|---------|---------|
|**opslagplaats**     |         |
|opslag plaats: status     | Toegangs status voor door voeren         |
|repo_deployment      | Toegangs implementatie status         |
|public_repo     | Open bare opslag plaatsen openen         |
|**admin:repo_hook**     |         |
|schrijven: repo_hook     | Opslag plaats-hooks schrijven         |
|lezen: repo_hook|Opslag plaats hooks lezen|

#### <a name="azure-repos"></a>Azure-opslagplaatsen

Ga voor meer informatie over het maken van een persoonlijk toegangs token in azure opslag plaatsen naar [verificatie van toegang met persoonlijke toegangs tokens](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate).

|Scope  |
|---------|
|Code (lezen)     |
|Project en team (lezen)|
|Identiteit (lezen)      |
|Gebruikers profiel (lezen)     |
|Werk items (lezen)    |
|Service verbindingen (lezen, query's en beheren)<sup>1</sup>    |

<sup>1</sup> de machtiging voor service verbindingen is alleen vereist als u automatische synchronisatie hebt ingeschakeld.

## <a name="syncing"></a>Synchroniseren

Selecteer de bron in de tabel op de pagina **broncode beheer** . Klik op **synchronisatie starten** om het synchronisatie proces te starten.

U kunt de status van de huidige synchronisatie taak of vorige synchroniseren bekijken door te klikken op het tabblad **synchronisatie taken** . Selecteer in de vervolg keuzelijst **broncode beheer** een broncode beheer.

![Synchronisatie status](./media/source-control-integration/sync-status.png)

Als u op een taak klikt, kunt u de uitvoer van de taak bekijken. Het volgende voor beeld is de uitvoer van een bron beheer synchronisatie taak.

```output
========================================================================================================

Azure Automation Source Control.
Supported runbooks to sync: PowerShell Workflow, PowerShell Scripts, DSC Configurations, Graphical, and Python 2.

Setting AzureRmEnvironment.

Getting AzureRunAsConnection.

Logging in to Azure...

Source control information for syncing:

[Url = https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl] [FolderPath = /Runbooks]

Verifying url: https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl

Connecting to VSTS...


Source Control Sync Summary:


2 files synced:
 - ExampleRunbook1.ps1
 - ExampleRunbook2.ps1



========================================================================================================
```

Aanvullende logboek registratie is beschikbaar door **alle logboeken** te selecteren op de pagina overzicht van de **synchronisatie taak van broncode beheer** . Deze aanvullende logboek vermeldingen kunnen u helpen bij het oplossen van problemen die zich kunnen voordoen bij het gebruik van broncode beheer.

## <a name="disconnecting-source-control"></a>Verbinding met broncode beheer verbreken

Als u de verbinding met een bron beheer opslagplaats wilt verbreken, opent u **broncode beheer** onder **account instellingen** in uw Automation-account.

Selecteer het bron beheer dat u wilt verwijderen. Klik op de pagina **overzicht van broncode beheer** op **verwijderen**.

## <a name="encoding"></a>Encoding

Als meerdere personen runbooks bewerken in uw opslag plaats voor bron beheer met verschillende editors, is er een kans om problemen met de code ring op te lossen. Deze situatie kan leiden tot onjuiste tekens in uw runbook. Zie [algemene oorzaken van coderings problemen](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues) voor meer informatie.

## <a name="updating-the-access-token"></a>Het toegangs token wordt bijgewerkt

Op dit moment is het niet mogelijk om het toegangs token in broncode beheer bij te werken vanuit de portal. Nadat uw persoonlijke toegangs token is verlopen of ingetrokken, kunt u op de volgende manieren broncode beheer bijwerken met een nieuw toegangs token:

* Via de [rest-API](https://docs.microsoft.com/rest/api/automation/sourcecontrol/update).
* Met behulp van de cmdlet [Update-AzAutomationSourceControl](/powershell/module/az.automation/update-azautomationsourcecontrol) .

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Automation-runbooktypen](automation-runbook-types.md) voor meer informatie over runbooktypen, hun voordelen en beperkingen
