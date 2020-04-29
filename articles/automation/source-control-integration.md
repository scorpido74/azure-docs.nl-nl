---
title: Integratie van broncodebeheer in Azure Automation
description: In dit artikel wordt de integratie van broncode beheer met GitHub in Azure Automation beschreven.
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: 166902978d1641458f18aeee6269c8d819e85233
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80132929"
---
# <a name="source-control-integration-in-azure-automation"></a>Integratie van broncodebeheer in Azure Automation

 De integratie van broncode beheer in Azure Automation ondersteunt synchronisatie met één richting vanuit uw opslag plaats voor bron beheer. Met broncode beheer kunt u uw runbooks in uw Automation-account up-to-date houden met scripts in uw opslag plaats voor GitHub of Azure opslag plaatsen-bron beheer. Met deze functie kunt u eenvoudig code promoten die in uw ontwikkel omgeving is getest op uw productie Automation-account.
 
 Met broncode beheer integratie kunt u eenvoudig samen werken met uw team, wijzigingen bijhouden en terugkeren naar eerdere versies van uw runbooks. Met broncode beheer kunt u bijvoorbeeld verschillende vertakkingen in broncode beheer synchroniseren met uw ontwikkelings-, test-en productie Automation-accounts. 

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [de module Azure PowerShell installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor de installatie-instructies voor AZ module op uw Hybrid Runbook Worker. Voor uw Automation-account kunt u uw modules bijwerken naar de nieuwste versie met behulp van [het bijwerken van Azure PowerShell-modules in azure Automation](automation-update-azure-modules.md).

## <a name="source-control-types"></a>Broncode beheer typen

Azure Automation ondersteunt drie typen broncode beheer:

* GitHub
* Azure-opslag plaatsen (Git)
* Azure-opslag plaatsen (TFVC)

## <a name="prerequisites"></a>Vereisten

* Een broncode beheer bibliotheek (GitHub of Azure opslag plaatsen)
* Een [uitvoeren als-account](manage-runas-account.md)
* De [meest recente Azure-modules](automation-update-azure-modules.md) in uw Automation- `Az.Accounts` account, inclusief de module ( `AzureRM.Profile`AZ module equivalent van)

> [!NOTE]
> Synchronisatie taken voor broncode beheer worden uitgevoerd onder het Automation-account van de gebruiker en worden gefactureerd tegen hetzelfde aantal als andere Automation-taken.

## <a name="configuring-source-control"></a>Broncode beheer configureren

In deze sectie wordt uitgelegd hoe u broncode beheer voor uw Automation-account kunt configureren. U kunt de Azure Portal of Power shell gebruiken.

### <a name="configure-source-control-in-azure-portal"></a>Broncode beheer configureren in Azure Portal

Gebruik deze procedure voor het configureren van broncode beheer met behulp van de Azure Portal.

1. Selecteer in uw Automation-account **broncode beheer** en klik op **toevoegen**.

    ![Broncode beheer selecteren](./media/source-control-integration/select-source-control.png)

2. Kies **broncode beheer type**en klik vervolgens op **verifiëren**. 

3. Er wordt een browser venster geopend en u wordt gevraagd om u aan te melden. Volg de aanwijzingen om de verificatie te volt ooien.

4. Op de pagina overzicht van broncode beheer gebruikt u de velden om de eigenschappen van de bron besturing die hieronder zijn gedefinieerd, in te vullen. Klik op **Opslaan** wanneer u klaar bent. 

    |Eigenschap  |Beschrijving  |
    |---------|---------|
    |Naam van broncode beheer     | Een beschrijvende naam voor het broncode beheer. Deze naam mag alleen letters en cijfers bevatten.        |
    |Type broncode beheer     | Type broncode beheer mechanisme. De volgende opties zijn beschikbaar:</br> * GitHub</br>* Azure opslag plaatsen (Git)</br> * Azure opslag plaatsen (TFVC)        |
    |Opslagplaats     | De naam van de opslag plaats of het project. De eerste 200-opslag plaatsen worden opgehaald. Als u wilt zoeken naar een opslag plaats, typt u de naam in het veld en klikt u op **zoeken op github**.|
    |Vertakking     | Vertakking waaruit de bron bestanden moeten worden gehaald. Vertakkings doelen zijn niet beschikbaar voor het TFVC-broncode beheer type.          |
    |Mappad     | De map die de runbooks bevat die moeten worden gesynchroniseerd, bijvoorbeeld **/Runbooks**. Alleen runbooks in de opgegeven map worden gesynchroniseerd. Recursie wordt niet ondersteund.        |
    |Automatische synchronisatie<sup>1</sup>     | Instelling waarmee automatische synchronisatie wordt in-of uitgeschakeld wanneer een door Voer in de bron beheer opslagplaats wordt gemaakt.        |
    |Runbook publiceren     | Instelling van op als runbooks automatisch worden gepubliceerd na synchronisatie vanuit broncode beheer, en anders uit.           |
    |Beschrijving     | Tekst waarin aanvullende details over het broncode beheer worden opgegeven.        |

    <sup>1</sup> als u automatische synchronisatie wilt inschakelen bij het configureren van de integratie van broncode beheer met Azure opslag plaatsen, moet u een project beheerder zijn.

   ![Samen vatting van broncode beheer](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> De aanmelding voor uw opslag plaats voor broncode beheer kan afwijken van uw aanmeldings gegevens voor de Azure Portal. Zorg ervoor dat u bent aangemeld met het juiste account voor de bron beheer opslagplaats bij het configureren van broncode beheer. Als er een twijfel is, opent u een nieuw tabblad in uw browser, meldt u zich af bij **dev.Azure.com**, **VisualStudio.com**of **github.com**en probeert u opnieuw verbinding te maken met broncode beheer.

### <a name="configure-source-control-in-powershell"></a>Broncode beheer configureren in Power shell

U kunt Power shell ook gebruiken voor het configureren van broncode beheer in Azure Automation. Als u de Power shell-cmdlets voor deze bewerking wilt gebruiken, hebt u een persoonlijk toegangs token (PAT) nodig. Gebruik de cmdlet [New-AzAutomationSourceControl](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationsourcecontrol?view=azps-3.5.0
) om de bron beheer verbinding te maken. Voor deze cmdlet is een beveiligde teken reeks vereist voor de PAT. Zie [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-6)voor meer informatie over het maken van een beveiligde teken reeks.

De volgende subsecties illustreren het maken van de broncode beheer verbinding voor GitHub, Azure opslag plaatsen (Git) en Azure opslag plaatsen (TFVC). 

#### <a name="create-source-control-connection-for-github"></a>Broncode beheer verbinding maken voor GitHub

```powershell-interactive
New-AzAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

#### <a name="create-source-control-connection-for-azure-repos-git"></a>Broncode beheer verbinding maken voor Azure opslag plaatsen (Git)

> [!NOTE]
> Azure opslag plaatsen (Git) maakt gebruik van een URL die toegang heeft tot **dev.Azure.com** in plaats van **VisualStudio.com**, die wordt gebruikt in eerdere notaties. De indeling `https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname>` van de oudere URL is afgeschaft, maar wordt nog steeds ondersteund. De nieuwe indeling heeft de voor keur.


```powershell-interactive
New-AzAutomationSourceControl -Name SCReposGit -RepoUrl https://dev.azure.com/<accountname>/<adoprojectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="create-source-control-connection-for-azure-repos-tfvc"></a>Broncode beheer verbinding maken voor Azure opslag plaatsen (TFVC)

> [!NOTE]
> Azure opslag plaatsen (TFVC) gebruikt een URL die toegang heeft tot **dev.Azure.com** in plaats van **VisualStudio.com**, die wordt gebruikt in eerdere notaties. De indeling `https://<accountname>.visualstudio.com/<projectname>/_versionControl` van de oudere URL is afgeschaft, maar wordt nog steeds ondersteund. De nieuwe indeling heeft de voor keur.

```powershell-interactive
New-AzAutomationSourceControl -Name SCReposTFVC -RepoUrl https://dev.azure.com/<accountname>/<adoprojectname>/_git/<repositoryname> -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="personal-access-token-pat-permissions"></a>Machtigingen voor het persoonlijke toegangs token (PAT)

Voor broncode beheer zijn enkele minimale machtigingen vereist voor PATs. De volgende subsecties bevatten de minimale machtigingen die zijn vereist voor GitHub en Azure opslag plaatsen.

##### <a name="minimum-pat-permissions-for-github"></a>Minimale machtigingen voor PAT voor GitHub

In de volgende tabel worden de minimale machtigingen voor PAT gedefinieerd die zijn vereist voor GitHub. Zie voor meer informatie over het maken van een PAT in GitHub [een persoonlijk toegangs token maken voor de opdracht regel](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).

|Bereik  |Beschrijving  |
|---------|---------|
|**`repo`**     |         |
|`repo:status`     | Toegangs status voor door voeren         |
|`repo_deployment`      | Toegangs implementatie status         |
|`public_repo`     | Open bare opslag plaatsen openen         |
|**`admin:repo_hook`**     |         |
|`write:repo_hook`     | Opslag plaats-hooks schrijven         |
|`read:repo_hook`|Opslag plaats hooks lezen|

##### <a name="minimum-pat-permissions-for-azure-repos"></a>Minimale machtigingen voor PAT voor Azure opslag plaatsen

In de volgende lijst worden de minimale machtigingen voor PAT gedefinieerd die zijn vereist voor Azure opslag plaatsen. Zie [toegang verifiëren met persoonlijke toegangs tokens](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate)voor meer informatie over het maken van een Pat in azure opslag plaatsen.

| Bereik  |  Toegangs type  |
|---------| ----------|
| `Code`      | Lezen  |
| `Project and team` | Lezen |
| `Identity` | Lezen     |
| `User profile` | Lezen     |
| `Work items` | Lezen    |
| `Service connections` | Lezen, query's, beheren<sup>1</sup>    |

<sup>1</sup> de `Service connections` machtiging is alleen vereist als u automatische synchronisatie hebt ingeschakeld.

## <a name="synchronizing"></a>Momenteel

Volg deze stappen om te synchroniseren met broncode beheer. 

1. Selecteer de bron in de tabel op de pagina broncode beheer. 

2. Klik op **synchronisatie starten** om het synchronisatie proces te starten. 

3. Klik op het tabblad **synchronisatie taken** om de status van de huidige synchronisatie taak of vorige te bekijken. 

4. Selecteer in de vervolg keuzelijst **broncode beheer** een broncode beheer mechanisme.

    ![Synchronisatie status](./media/source-control-integration/sync-status.png)

5. Als u op een taak klikt, kunt u de uitvoer van de taak bekijken. Het volgende voor beeld is de uitvoer van een bron beheer synchronisatie taak.

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

6. Aanvullende logboek registratie is beschikbaar door **alle logboeken** te selecteren op de pagina overzicht van de synchronisatie taak van broncode beheer. Deze aanvullende logboek vermeldingen kunnen u helpen bij het oplossen van problemen die zich kunnen voordoen bij het gebruik van broncode beheer.

## <a name="disconnecting-source-control"></a>Verbinding met broncode beheer verbreken

De verbinding met een broncode beheer bibliotheek verbreken:

1. Open **broncode beheer** onder **account instellingen** in uw Automation-account.

2. Selecteer het bron beheer mechanisme dat u wilt verwijderen. 

3. Klik op de pagina overzicht van broncode beheer op **verwijderen**.

## <a name="handling-encoding-issues"></a>Coderings problemen verwerken

Als meerdere personen runbooks bewerken in uw opslag plaats voor bron beheer met behulp van verschillende editors, kunnen er coderings problemen optreden. Zie voor meer informatie over deze situatie [Veelvoorkomende oorzaken van het coderen van problemen](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues).

## <a name="updating-the-pat"></a>De PAT bijwerken

Op dit moment kunt u de Azure Portal niet gebruiken om de PAT bij te werken in broncode beheer. Als uw PAT is verlopen of ingetrokken, kunt u op een van de volgende manieren broncode beheer bijwerken met een nieuw toegangs token:

* Gebruik de [rest API](https://docs.microsoft.com/rest/api/automation/sourcecontrol/update).
* Gebruik de cmdlet [Update-AzAutomationSourceControl](/powershell/module/az.automation/update-azautomationsourcecontrol) .

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Automation runbook-typen](automation-runbook-types.md)voor meer informatie over de typen runbook en hun voor delen en beperkingen.
