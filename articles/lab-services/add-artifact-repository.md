---
title: Een artefact-opslagplaats toevoegen aan uw lab in Azure DevTest Labs | Microsoft Documenten
description: Meer informatie over het toevoegen van een artefact-opslagplaats aan uw lab in Azure DevTest-labs.
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/21/2019
ms.author: spelluru
ms.openlocfilehash: 2bb871119bece71c705ad9621a7c76c4b5ed0bc7
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770242"
---
# <a name="add-an-artifact-repository-to-your-lab-in-devtest-labs"></a>Voeg een artefactrepository toe aan uw lab in DevTest Labs
Met DevTest Labs u een artefact opgeven dat aan een VM moet worden toegevoegd op het moment dat de VM wordt gemaakt of nadat de VM is gemaakt. Dit artefact kan een hulpmiddel of een toepassing zijn die u op de VM wilt installeren. Artefacten worden gedefinieerd in een JSON-bestand dat is geladen vanuit een GitHub- of Azure DevOps Git-opslagplaats.

De [openbare artefact repository](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts), onderhouden door DevTest Labs, biedt veel gemeenschappelijke tools voor zowel Windows als Linux. Een link naar deze repository wordt automatisch toegevoegd aan uw lab. U uw eigen artefactrepository maken met specifieke hulpprogramma's die niet beschikbaar zijn in de openbare artefact-opslagplaats. Zie Aangepaste artefacten maken voor meer informatie over het maken van aangepaste [artefacten.](devtest-lab-artifact-author.md)

In dit artikel vindt u informatie over het toevoegen van uw aangepaste artefactrepository met Azure-portal, Azure Resource Management-sjablonen en Azure PowerShell. U het toevoegen van een artefact-opslagplaats aan een lab automatiseren door PowerShell- of CLI-scripts te schrijven.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten
Als u een opslagplaats aan uw lab wilt toevoegen, krijgt u eerst belangrijke informatie uit uw opslagplaats. In de volgende secties wordt beschreven hoe u de vereiste informatie krijgen voor repositories die worden gehost op **GitHub** of **Azure DevOps.**

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>Download de URL voor de GitHub-repository-kloon en het persoonlijke toegangstoken

1. Ga naar de startpagina van de GitHub-opslagplaats met de definities van de sjabloonartefact of Resource Manager.
2. Selecteer **Klonen of downloaden**.
3. Als u de URL naar het klembord wilt kopiëren, selecteert u de **URL-knop HTTPS-kloon.** Sla de URL op voor later gebruik.
4. Selecteer in de rechterbovenhoek van GitHub de profielafbeelding en selecteer **Vervolgens Instellingen**.
5. Selecteer **ontwikkelaarsinstellingen**in het menu **Persoonlijke instellingen** aan de linkerkant .
6. Selecteer **Persoonlijke toegangstokens** in het linkermenu.
7. Selecteer **Nieuw token genereren**.
8. Voer op de pagina **Nieuwe persoonlijke toegangtoken** onder **Token-beschrijving**een beschrijving in. Accepteer de standaarditems onder **Bereik selecteren**en selecteer **Token genereren**.
9. Sla het gegenereerde token op. Je gebruikt het token later.
10. Sluit GitHub.   

### <a name="get-the-azure-repos-clone-url-and-personal-access-token"></a>Download de URL voor de Azure Repos-kloon en het persoonlijke toegangstoken
1. Ga naar de startpagina van uw teamverzameling `https://contoso-web-team.visualstudio.com`(bijvoorbeeld) en selecteer vervolgens uw project.
2. Selecteer Op de startpagina van het project de optie **Code**.
3. Als u de kloon-URL wilt weergeven, selecteert u op de pagina **projectcode** de optie **Kloon**.
4. Sla de URL op. U gebruikt de URL later.
5. Als u een persoonlijk toegangstoken wilt maken, selecteert u in het vervolgkeuzemenu gebruikersaccount **Mijn profiel**.
6. Selecteer **beveiliging**op de pagina profielinformatie .
7. Selecteer op het tabblad **Beveiligings- > Persoonlijke toegangstokens** de optie **+ Nieuw token**.
8. Ga op de pagina **Een nieuwe persoonlijke toegangstoken maken:**
   1. Voer een **naam** in voor het token.
   2. Selecteer **alle** toegankelijke **organisaties**in de lijst Organisatie .
   3. Selecteer in de lijst **Expiratie (UTC)** **90 dagen**of een aangepaste gedefinieerde vervaldatum.
   4. Selecteer de optie **Volledige toegang** voor Scopes.
   5. Selecteer **Maken**.
9. Het nieuwe token wordt weergegeven in de lijst **Met persoonlijke toegangstokens.** Selecteer **Token kopiëren**en sla de tokenwaarde op voor later gebruik.
10. Ga verder naar de sectie Uw lab verbinden met de opslagplaats.

## <a name="use-azure-portal"></a>Azure Portal gebruiken
In deze sectie vindt u stappen om een artefact-opslagplaats toe te voegen aan een lab in de Azure-portal.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Selecteer **Meer services**en selecteer Vervolgens **DevTest Labs** in de lijst met services.
3. Selecteer in de lijst met labs uw lab.
4. Selecteer **Configuratie en beleid** in het linkermenu.
5. Selecteer **Repositories** onder sectie **Externe bronnen** in het linkermenu.
6. Selecteer **+ Toevoegen** op de werkbalk.

    ![De knop Opslagplaats toevoegen](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. Geef op de pagina **Repositories** de volgende informatie op:
   1. **Naam**. Voer een naam in voor de opslagplaats.
   2. **Git Clone Url**. Voer de URL van de Git HTTPS-kloon in die u eerder hebt gekopieerd van GitHub of Azure DevOps Services.
   3. **Tak**. Voer de branch in om aan uw definities te komen.
   4. **Persoonlijke toegangstoken**. Voer het persoonlijke toegangstoken in dat u eerder hebt gekregen van GitHub of Azure DevOps Services.
   5. **Mappaden**. Voer ten minste één mappad in ten opzichte van de kloon-URL die de definities van de artefact- of Resource Manager-sjabloon bevat. Wanneer u een submap opgeeft, moet u de slash doorsturen opnemen in het mappad.

        ![Repositories gebied](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. Selecteer **Opslaan**.

## <a name="use-azure-resource-manager-template"></a>Sjabloon Azure Resource Manager gebruiken
Azure Resource Management -sjablonen (Azure Resource Manager) zijn JSON-bestanden die resources in Azure beschrijven die u wilt maken. Zie Sjablonen voor Azure [Resource Manager ontwerpen](../azure-resource-manager/templates/template-syntax.md)voor meer informatie over deze sjablonen.

In deze sectie vindt u stappen om een artefact-opslagplaats aan een lab toe te voegen met behulp van een Azure Resource Manager-sjabloon.  De sjabloon maakt het lab als het nog niet bestaat.

### <a name="template"></a>Template
De voorbeeldsjabloon die in dit artikel wordt gebruikt, verzamelt de volgende informatie via parameters. De meeste parameters hebben wel slimme standaardwaarden, maar er zijn een paar waarden die moeten worden opgegeven. U moet de labnaam, URI voor de artefact-opslagplaats en het beveiligingstoken voor de opslagplaats opgeven.

- De naam van het lab.
- Weergavenaam voor de artefact-opslagplaats in de gebruikersinterface (UI) van DevTest Labs. De standaardwaarde `Team Repository`is: .
- URI naar de opslagplaats `https://github.com/<myteam>/<nameofrepo>.git` (Voorbeeld: of `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"`.
- Vertakking in de opslagplaats die artefacten bevat. De standaardwaarde `master`is: .
- Naam van de map die artefacten bevat. De standaardwaarde `/Artifacts`is: .
- Type van de opslagplaats. Toegestane waarden `VsoGit` zijn `GitHub`of .
- Toegangstoken voor de repository.

    ```json
    {

        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "labName": {
                "type": "string"
            },
            "artifactRepositoryDisplayName": {
                "type": "string",
                "defaultValue": "Team Repository"
            },
            "artifactRepoUri": {
                "type": "string"
            },
            "artifactRepoBranch": {
                "type": "string",
                "defaultValue": "master"
            },
            "artifactRepoFolder": {
                "type": "string",
                "defaultValue": "/Artifacts"
            },
            "artifactRepoType": {
                "type": "string",
                "allowedValues": ["VsoGit", "GitHub"]
            },
            "artifactRepoSecurityToken": {
                "type": "securestring"
            }
        },
        "variables": {
            "artifactRepositoryName": "[concat('Repo-', uniqueString(subscription().subscriptionId))]"
        },
        "resources": [{
                "apiVersion": "2016-05-15",
                "type": "Microsoft.DevTestLab/labs",
                "name": "[parameters('labName')]",
                "location": "[resourceGroup().location]",
                "resources": [
                    {
                        "apiVersion": "2016-05-15",
                        "name": "[variables('artifactRepositoryName')]",
                        "type": "artifactSources",
                        "dependsOn": [
                            "[resourceId('Microsoft.DevTestLab/labs', parameters('labName'))]"
                        ],
                        "properties": {
                            "uri": "[parameters('artifactRepoUri')]",
                            "folderPath": "[parameters('artifactRepoFolder')]",
                            "branchRef": "[parameters('artifactRepoBranch')]",
                            "displayName": "[parameters('artifactRepositoryDisplayName')]",
                            "securityToken": "[parameters('artifactRepoSecurityToken')]",
                            "sourceType": "[parameters('artifactRepoType')]",
                            "status": "Enabled"
                        }
                    }
                ]
            }
        ]
    }
    ```


### <a name="deploy-the-template"></a>De sjabloon implementeren
Er zijn een paar manieren om de sjabloon te implementeren in Azure en de bron te laten maken, als deze niet bestaat of wordt bijgewerkt, als deze bestaat. Zie voor meer informatie de volgende artikelen:

- [Resources implementeren met Resource Manager-sjablonen en Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
- [Resources implementeren met Resource Manager-sjablonen en Azure CLI](../azure-resource-manager/templates/deploy-cli.md)
- [Resources implementeren met Resource Manager-sjablonen en Azure Portal](../azure-resource-manager/templates/deploy-portal.md)
- [Resources implementeren met Resource Manager-sjablonen en Resource Manager REST API](../azure-resource-manager/templates/deploy-rest.md)

Laten we verder gaan en kijken hoe u de sjabloon in PowerShell implementeren. Cmdlets die worden gebruikt om de sjabloon te implementeren, zijn contextspecifiek, zodat huidige tenant en huidig abonnement worden gebruikt. Gebruik [Set-AzContext](/powershell/module/az.accounts/set-azcontext) voordat u de sjabloon implementeert, indien nodig, om de context te wijzigen.

Maak eerst een resourcegroep met [Nieuwe AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Als de resourcegroep die u wilt gebruiken al bestaat, slaat u deze stap over.

```powershell
New-AzResourceGroup -Name MyLabResourceGroup1 -Location westus
```

Maak vervolgens een implementatie naar de resourcegroep met [Nieuwe AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). Met deze cmdlet worden de resourcewijzigingen in Azure aangebracht. Er kunnen verschillende resourceimplementaties worden uitgevoerd naar een bepaalde resourcegroep. Als u meerdere keren implementeert in dezelfde resourcegroep, controleert u of de naam van elke implementatie uniek is.

```powershell
New-AzResourceGroupDeployment `
    -Name MyLabResourceGroup-Deployment1 `
    -ResourceGroupName MyLabResourceGroup1 `
    -TemplateFile azuredeploy.json `
    -TemplateParameterFile azuredeploy.parameters.json
```

Nadat Nieuw-AzResourceGroupDeployment is uitgevoerd, worden belangrijke informatie van de opdracht uitgevoerd, zoals de inrichtingsstatus (moet worden opgevolgd) en eventuele uitvoer voor de sjabloon.

## <a name="use-azure-powershell"></a>Azure PowerShell gebruiken
In deze sectie ziet u een voorbeeld van PowerShell-script dat kan worden gebruikt om een artefact-opslagplaats aan een lab toe te voegen. Als u azure PowerShell niet hebt, raadpleegt u [Azure PowerShell installeren en configureren](/powershell/azure/overview?view=azps-1.2.0) voor gedetailleerde instructies om het te installeren.

### <a name="full-script"></a>Volledige script
Hier is het volledige script, met inbegrip van een aantal verbose berichten en opmerkingen:

**Nieuw-DevTestLabArtefactRepository.ps1:**

```powershell

<#

.SYNOPSIS
This script creates a new custom repository and adds it to an existing DevTest Lab.

.PARAMETER LabName
The name of the lab.

.PARAMETER LabResourceGroupName
The name of the resource group that contains the lab.

.PARAMETER ArtifactRepositoryName
Name for the new artifact repository.
Script creates a random name for the respository if it is not specified.

.PARAMETER ArtifactRepositoryDisplayName
Display name for the artifact repository.
This is the name that shows up in the Azure portal (https://portal.azure.com) when viewing all the artifact repositories for a lab.

.PARAMETER RepositoryUri
Uri to the repository.

.PARAMETER RepositoryBranch
Branch in which artifact files can be found. Defaults to 'master'.

.PARAMETER FolderPath
Folder under which artifacts can be found. Defaults to '/Artifacts'

.PARAMETER PersonalAccessToken
Security token for access to GitHub or VSOGit repository.
See https://azure.microsoft.com/documentation/articles/devtest-lab-add-artifact-repo/ for instructions to get personal access token

.PARAMETER SourceType
Whether artifact is VSOGit or GitHub repository.

.EXAMPLE
Set-AzContext -SubscriptionId 11111111-1111-1111-1111-111111111111
.\New-DevTestLabArtifactRepository.ps1 -LabName "mydevtestlab" -LabResourceGroupName "mydtlrg" -ArtifactRepositoryName "MyTeam Repository" -RepositoryUri "https://github.com/<myteam>/<nameofrepo>.git" -PersonalAccessToken "1111...." -SourceType "GitHub"

.NOTES
Script uses the current Az context. To set the context, use the Set-AzContext cmdlet

#>


[CmdletBinding()]
Param(

    [Parameter(Mandatory=$true)]
    $LabName,

    [Parameter(Mandatory=$true)]
    $LabResourceGroupName,
    $ArtifactRepositoryName,
    $ArtifactRepositoryDisplayName  = 'Team Artifact Repository',

    [Parameter(Mandatory=$true)]
    $RepositoryUri,
    $RepositoryBranch = 'master',
    $FolderPath = '/Artifacts',

    [Parameter(Mandatory=$true)]
    $PersonalAccessToken ,

    [Parameter(Mandatory=$true)]
    [ValidateSet('VsoGit', 'GitHub')]
    $SourceType
)


#Set artifact repository internal name,
# if not set by user.

if ($ArtifactRepositoryName -eq $null){
    $ArtifactRepositoryName = "PrivateRepo" + (Get-Random -Maximum 999)
}

# Sign in to Azure
Connect-AzAccount


#Get Lab Resource
$LabResource = Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceName $LabName -ResourceGroupName $LabResourceGroupName

Write-Verbose "Lab Name: $($LabResource.Name)"
Write-Verbose "Lab Resource Group Name: $($LabResource.ResourceGroupName)"
Write-Verbose "Lab Resource Location: $($LabResource.Location)"

Write-Verbose "Artifact Repository Internal Name: $ArtifactRepositoryName"

#Prepare properties object for call to New-AzResource
$propertiesObject = @{
    uri = $RepositoryUri;
    folderPath = $FolderPath;
    branchRef = $RepositoryBranch;
    displayName = $ArtifactRepositoryDisplayName;
    securityToken = $PersonalAccessToken;
    sourceType = $SourceType;
    status = 'Enabled'
}

Write-Verbose @"Properties to be passed to New-AzResource:$($propertiesObject | Out-String)"

#Resource will be added to current subscription.
$resourcetype = 'Microsoft.DevTestLab/labs/artifactSources'
$resourceName = $LabName + '/' + $ArtifactRepositoryName
Write-Verbose "Az ResourceType: $resourcetype"
Write-Verbose "Az ResourceName: $resourceName"

Write-Verbose "Creating artifact repository '$ArtifactRepositoryDisplayName'..."
$result = New-AzResource -Location $LabResource.Location -ResourceGroupName $LabResource.ResourceGroupName -properties $propertiesObject -ResourceType $resourcetype -ResourceName $resourceName -ApiVersion 2016-05-15 -Force


#Alternate implementation:
# Use resourceId rather than resourcetype and resourcename parameters.
# Using resourceId allows you to specify the $SubscriptionId rather than using the
# subscription id of Get-AzContext.
#$resourceId = "/subscriptions/$SubscriptionId/resourceGroups/$($LabResource.ResourceGroupName)/providers/Microsoft.DevTestLab/labs/$LabName/artifactSources/$ArtifactRepositoryName"
#$result = New-AzResource -properties $propertiesObject -ResourceId $resourceId -ApiVersion 2016-05-15 -Force


# Check the result
if ($result.Properties.ProvisioningState -eq "Succeeded") {
    Write-Verbose ("Successfully added artifact repository source '$ArtifactRepositoryDisplayName'")
}
else {
    Write-Error ("Error adding artifact repository source '$ArtifactRepositoryDisplayName'")
}

#Return the newly created resource so it may be used in subsequent scripts
return $result
```

### <a name="run-the-powershell-script"></a>Het PowerShell-script uitvoeren
In het volgende voorbeeld ziet u hoe u het script uitvoert:

```powershell
Set-AzContext -SubscriptionId <Your Azure subscription ID>

.\New-DevTestLabArtifactRepository.ps1 -LabName "mydevtestlab" -LabResourceGroupName "mydtlrg" -ArtifactRepositoryName "MyTeam Repository" -RepositoryUri "https://github.com/<myteam>/<nameofrepo>.git" -PersonalAccessToken "1111...." -SourceType "GitHub"
```


### <a name="parameters"></a>Parameters
Het voorbeeld PowerShell-script in dit artikel neemt de volgende parameters:

| Parameter | Beschrijving |
| --------- | ----------- |
| LabName (LabName) | De naam van het lab. |
| ArtefactRepositoryName | Naam voor de nieuwe artefact repository. Het script maakt een willekeurige naam voor de respository als deze niet is opgegeven. |
| ArtefactRepositoryDisplayName | Geef de naam weer voor de artefact-opslagplaats. Dit is de naam die wordthttps://portal.azure.com) weergegeven in de Azure-portal (bij het bekijken van alle artefactrepositories voor een lab. |
| RepositoryUri RepositoryUri | Uri naar de opslagplaats. Voorbeelden: `https://github.com/<myteam>/<nameofrepo>.git` of `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"`.|
| RepositoryBranch RepositoryBranch | Tak waarin artefactbestanden kunnen worden gevonden. Standaard 'master'. |
| FolderPad | Map waaronder artefacten kunnen worden gevonden. Standaardinstellingen voor '/Artefacten' |
| PersonalAccessToken | Beveiligingstoken voor toegang tot de GitHub- of VSOGit-repository. Zie de sectie vereisten voor instructies voor persoonlijke toegangstoken |
| SourceType | Of artefact vsogit of GitHub repository is. |

De repository zelf heeft een interne naam nodig voor identificatie, wat anders is dan de weergavenaam die wordt gezien in de Azure-portal. U ziet de interne naam niet met behulp van de Azure-portal, maar u ziet deze wel bij het gebruik van Azure REST API's of Azure PowerShell. Het script geeft een naam, als een niet is opgegeven door de gebruiker van ons script.

```powershell
#Set artifact repository name, if not set by user
if ($ArtifactRepositoryName -eq $null){
    $ArtifactRepositoryName = "PrivateRepo" + (Get-Random -Maximum 999)
}
```

### <a name="powershell-commands-used-in-the-script"></a>PowerShell-opdrachten die in het script worden gebruikt

| PowerShell-opdracht | Opmerkingen |
| ------------------ | ----- |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Deze opdracht wordt gebruikt om details over het lab te krijgen, zoals de locatie. |
| [Nieuw-AzResource](/powershell/module/az.resources/new-azresource) | Er is geen specifieke opdracht voor het toevoegen van artefact-opslagplaatsen. De generieke [Nieuw-AzResource](/powershell/module/az.resources/new-azresource) cmdlet doet het werk. Deze cmdlet heeft de **ResourceId** of het **resourcename-** en **ResourceType-paar** nodig om te weten welk type resource u wilt maken. In dit voorbeeldscript worden de resourcenaam en het resourcetypepaar gebruikt. <br/><br/>U merkt op dat u de artefact-opslagplaatsbron maakt op dezelfde locatie en onder dezelfde brongroep als het lab.|

Het script voegt een nieuwe bron toe aan het huidige abonnement. Gebruik [Get-AzContext](/powershell/module/az.accounts/get-azcontext) om deze informatie te bekijken. Gebruik [Set-AzContext](/powershell/module/az.accounts/set-azcontext) om de huidige tenant en het huidige abonnement in te stellen.

De beste manier om de bronnaam en informatie over het type resource te ontdekken, is door de website [van De APU's voor Testdrive Azure REST te](https://azure.github.io/projects/apis/) gebruiken. Bekijk de [DevTest Labs - 2016-05-15](https://aka.ms/dtlrestapis) provider om de beschikbare REST API's voor de DevTest Labs provider te zien. Het script gebruikers de volgende resource-ID.

```powershell
"/subscriptions/$SubscriptionId/resourceGroups/$($LabResource.ResourceGroupName)/providers/Microsoft.DevTestLab/labs/$LabName/artifactSources/$ArtifactRepositoryName"
```

Het resourcetype is alles dat wordt weergegeven na 'providers' in de URI, behalve voor items die in de krullende haakjes worden vermeld. De naam van de bron is alles gezien in de krullende haakjes. Als er meer dan één item wordt verwacht voor de resourcenaam, scheidt u elk item met een slash zoals we hebben gedaan.

```powershell
$resourcetype = 'Microsoft.DevTestLab/labs/artifactSources'
$resourceName = $LabName + '/' + $ArtifactRepositoryName
```


## <a name="next-steps"></a>Volgende stappen
- [Verplichte artefacten voor uw lab opgeven in Azure DevTest Labs](devtest-lab-mandatory-artifacts.md)
- [Maak aangepaste artefacten voor uw Virtuele Machine van DevTest Labs](devtest-lab-artifact-author.md)
- [Artefact-fouten in het lab diagnosticeren](devtest-lab-troubleshoot-artifact-failure.md)
