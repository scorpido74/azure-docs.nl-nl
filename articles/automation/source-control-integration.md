---
title: Integratie van broncodebeheer in Azure Automation
description: In dit artikel wordt de integratie van bronbeheer met GitHub in Azure Automation beschreven.
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: 166902978d1641458f18aeee6269c8d819e85233
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132929"
---
# <a name="source-control-integration-in-azure-automation"></a>Integratie van broncodebeheer in Azure Automation

 Source control integration in Azure Automation ondersteunt synchronisatie in één richting vanuit uw bronbeheerrepository. Met bronbeheer u uw runbooks in uw Automation-account up-to-date houden met scripts in uw GitHub- of Azure Repos-bronbeheeropslagplaats. Deze functie maakt het eenvoudig om code die in uw ontwikkelomgeving is getest, te promoten in uw productieautomatiseringsaccount.
 
 Met de integratie van bronbeheer u eenvoudig samenwerken met uw team, wijzigingen bijhouden en terugdraaien naar eerdere versies van uw runbooks. Met bronbeheer u bijvoorbeeld verschillende branches synchroniseren in bronbeheer met uw accounts voor ontwikkelings-, test- en productieautomatisering. 

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor installatie-instructies voor az-modules op uw hybride runbookworker. Voor uw Automatiseringsaccount u uw modules bijwerken naar de nieuwste versie met [Azure PowerShell-modules bijwerken in Azure Automation.](automation-update-azure-modules.md)

## <a name="source-control-types"></a>Bronbeheertypen

Azure Automation ondersteunt drie typen bronbeheer:

* GitHub
* Azure Repos (Git)
* Azure Repos (TFVC)

## <a name="prerequisites"></a>Vereisten

* Een bronbeheeropslagplaats (GitHub of Azure Repos)
* Een [Run As-account](manage-runas-account.md)
* De [nieuwste Azure-modules](automation-update-azure-modules.md) in uw `Az.Accounts` Automatiseringsaccount, inclusief `AzureRM.Profile`de module (Az-module equivalent van)

> [!NOTE]
> Synchronisatietaken voor bronbeheer worden uitgevoerd onder het automatiseringsaccount van de gebruiker en worden in hetzelfde tempo gefactureerd als andere automatiseringstaken.

## <a name="configuring-source-control"></a>Bronbeheer configureren

In dit gedeelte wordt uitgelegd hoe u bronbeheer voor uw Automatiseringsaccount configureert. U de Azure-portal of PowerShell gebruiken.

### <a name="configure-source-control-in-azure-portal"></a>Bronbeheer configureren in Azure-portal

Gebruik deze procedure om bronbeheer te configureren met behulp van de Azure-portal.

1. Selecteer **bronbeheer** in uw automatiseringsaccount en klik op **Toevoegen**.

    ![Bronbesturingselement selecteren](./media/source-control-integration/select-source-control.png)

2. Kies **Bronbesturingselementtype**en klik op **Verifiëren**. 

3. Er wordt een browservenster geopend en wordt u gevraagd zich aan te melden. Volg de aanwijzingen om de verificatie te voltooien.

4. Gebruik op de pagina Overzicht van bronbeheer de velden om de onderstaande eigenschappen voor bronbeheer in te vullen. Klik **op Opslaan** als u klaar bent. 

    |Eigenschap  |Beschrijving  |
    |---------|---------|
    |Naam bronbesturingselement     | Een vriendelijke naam voor de broncontrole. Deze naam mag alleen letters en cijfers bevatten.        |
    |Broncontroletype     | Type broncontrolemechanisme. De volgende opties zijn beschikbaar:</br> * GitHub</br>* Azure Repos (Git)</br> * Azure Repos (TFVC)        |
    |Opslagplaats     | Naam van de opslagplaats of het project. De eerste 200 repositories worden opgehaald. Als u naar een opslagplaats wilt zoeken, typt u de naam in het veld en klikt u **op Zoeken op GitHub**.|
    |Vertakking     | Vertakking waaruit de bronbestanden kunnen worden opgehaald. Branchtargeting is niet beschikbaar voor het type TFVC-bronbesturingselement.          |
    |Mappad     | Map met de runbooks om bijvoorbeeld **/Runbooks**te synchroniseren. Alleen runbooks in de opgegeven map worden gesynchroniseerd. Recursie wordt niet ondersteund.        |
    |Automatisch synchroniseren<sup>1</sup>     | Instellen dat automatische synchronisatie in- of uitschakelt wanneer een commit wordt uitgevoerd in de bronbeheerrepository.        |
    |Runbook publiceren     | Instellen van Aan als runbooks automatisch worden gepubliceerd na synchronisatie vanuit bronbeheer en Anders uit.           |
    |Beschrijving     | Tekst met aanvullende details over het bronbesturingselement.        |

    <sup>1</sup> Als u Automatisch synchroniseren wilt inschakelen bij het configureren van de integratie van bronbeheer met Azure Repos, moet u projectbeheerder zijn.

   ![Overzicht van bronbesturingselement](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> De aanmelding voor uw bronbeheerrepository kan afwijken van uw aanmelding voor de Azure-portal. Zorg ervoor dat u bent ingelogd met het juiste account voor uw bronbeheerrepository bij het configureren van bronbeheer. Als er twijfel is, opent u een nieuw tabblad in uw browser, meldt u zich af bij **dev.azure.com,** **visualstudio.com**of **github.com**en probeert u opnieuw verbinding te maken met bronbeheer.

### <a name="configure-source-control-in-powershell"></a>Bronbeheer configureren in PowerShell

U PowerShell ook gebruiken om bronbeheer te configureren in Azure Automation. Om de PowerShell-cmdlets voor deze bewerking te gebruiken, hebt u een persoonlijk toegangstoken (PAT) nodig. Gebruik de cmdlet [Nieuw-AzAutomationSourceControl](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationsourcecontrol?view=azps-3.5.0
) om de bronbeheerverbinding te maken. Deze cmdlet vereist een veilige snaar voor de PAT. Zie [Convertto-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-6)voor meer informatie over het maken van een beveiligde tekenreeks.

De volgende subsecties illustreren de creatie van PowerShell van de bronbeheerverbinding voor GitHub, Azure Repos (Git) en Azure Repos (TFVC). 

#### <a name="create-source-control-connection-for-github"></a>Bronbeheerverbinding maken voor GitHub

```powershell-interactive
New-AzAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

#### <a name="create-source-control-connection-for-azure-repos-git"></a>Bronbeheerverbinding maken voor Azure Repos (Git)

> [!NOTE]
> Azure Repos (Git) gebruikt een URL die toegang biedt tot **dev.azure.com** in plaats van **visualstudio.com**, gebruikt in eerdere indelingen. De oudere `https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname>` URL-indeling wordt afgeschaft, maar wordt nog steeds ondersteund. Het nieuwe formaat heeft de voorkeur.


```powershell-interactive
New-AzAutomationSourceControl -Name SCReposGit -RepoUrl https://dev.azure.com/<accountname>/<adoprojectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="create-source-control-connection-for-azure-repos-tfvc"></a>Bronbeheerverbinding maken voor Azure Repos (TFVC)

> [!NOTE]
> Azure Repos (TFVC) gebruikt een URL die toegang heeft tot **dev.azure.com** in plaats van **visualstudio.com**, gebruikt in eerdere indelingen. De oudere `https://<accountname>.visualstudio.com/<projectname>/_versionControl` URL-indeling wordt afgeschaft, maar wordt nog steeds ondersteund. Het nieuwe formaat heeft de voorkeur.

```powershell-interactive
New-AzAutomationSourceControl -Name SCReposTFVC -RepoUrl https://dev.azure.com/<accountname>/<adoprojectname>/_git/<repositoryname> -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="personal-access-token-pat-permissions"></a>Pat-machtigingen (Personal Access Token)

Bronbeheer vereist een aantal minimale machtigingen voor PAT's. De volgende subsecties bevatten de minimale machtigingen die nodig zijn voor GitHub en Azure Repos.

##### <a name="minimum-pat-permissions-for-github"></a>Minimale PAT-machtigingen voor GitHub

In de volgende tabel worden de minimale PAT-machtigingen gedefinieerd die vereist zijn voor GitHub. Zie [Een persoonlijk toegangstoken maken voor de opdrachtregel voor](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)meer informatie over het maken van een PAT in GitHub.

|Bereik  |Beschrijving  |
|---------|---------|
|**`repo`**     |         |
|`repo:status`     | Status van toegangscommit         |
|`repo_deployment`      | Status van implementatie van toegang         |
|`public_repo`     | Toegang tot openbare repositories         |
|**`admin:repo_hook`**     |         |
|`write:repo_hook`     | Repository-haken schrijven         |
|`read:repo_hook`|Repository-haken lezen|

##### <a name="minimum-pat-permissions-for-azure-repos"></a>Minimale PAT-machtigingen voor Azure Repos

In de volgende lijst worden de minimale PAT-machtigingen gedefinieerd die nodig zijn voor Azure Repos. Zie [Toegang verifiëren met persoonlijke toegangstokens](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate)voor meer informatie over het maken van een PAT in Azure Repos.

| Bereik  |  Toegangstype  |
|---------| ----------|
| `Code`      | Lezen  |
| `Project and team` | Lezen |
| `Identity` | Lezen     |
| `User profile` | Lezen     |
| `Work items` | Lezen    |
| `Service connections` | Lezen, query's, beheer<sup>1</sup>    |

<sup>1</sup> `Service connections` De toestemming is alleen vereist als u autosync hebt ingeschakeld.

## <a name="synchronizing"></a>Synchroniseren

Volg deze stappen om te synchroniseren met bronbeheer. 

1. Selecteer de bron in de tabel op de pagina Bronbesturingselement. 

2. Klik **op Synchronisatie starten** om het synchronisatieproces te starten. 

3. De status van de huidige synchronisatietaak of vorige status weergeven door op het tabblad **Taken synchroniseren** te klikken. 

4. Selecteer in het vervolgkeuzemenu **Bronbeheer** een bronbeheermechanisme.

    ![Synchronisatiestatus](./media/source-control-integration/sync-status.png)

5. Als u op een taak klikt, u de taakuitvoer bekijken. Het volgende voorbeeld is de uitvoer van een synchronisatietaak voor bronbeheer.

    ```output
    ===================================================================

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

    ==================================================================

    ```

6. Extra logboekregistratie is beschikbaar door **Alle logboeken** te selecteren op de pagina Overzicht van het taakoverzicht voor bronbeheer. Met deze extra logboekvermeldingen u problemen oplossen die zich kunnen voordoen bij het gebruik van bronbeheer.

## <a name="disconnecting-source-control"></a>Bronbeheer loskoppelen

Ga als lid van de verbinding met een bronbeheerrepository:

1. Open **Source-besturingselement** onder **Accountinstellingen** in uw Automatiseringsaccount.

2. Selecteer het bronbesturingselementmechanisme dat u wilt verwijderen. 

3. Klik op de pagina Overzicht bronbeheer op **Verwijderen**.

## <a name="handling-encoding-issues"></a>Problemen met codering verwerken

Als meerdere mensen runbooks bewerken in uw bronbeheeropslagplaats met behulp van verschillende editors, kunnen coderingsproblemen optreden. Zie [Veelvoorkomende oorzaken van coderingsproblemen](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues)voor meer informatie over deze situatie.

## <a name="updating-the-pat"></a>De PAT bijwerken

Momenteel u de Azure-portal niet gebruiken om het PAT-in-bronbeheer bij te werken. Wanneer uw PAT is verlopen of ingetrokken, u bronbeheer op een van de volgende manieren bijwerken met een nieuw toegangstoken:

* Gebruik de [API REST](https://docs.microsoft.com/rest/api/automation/sourcecontrol/update).
* Gebruik de cmdlet [Update-AzAutomationSourceControl.](/powershell/module/az.automation/update-azautomationsourcecontrol)

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Automation runbook-typen](automation-runbook-types.md)voor meer informatie over runbook-typen en hun voordelen en beperkingen.
