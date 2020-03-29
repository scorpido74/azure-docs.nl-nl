---
title: MultiVM-omgevingen en PaaS-resources maken met sjablonen
description: Meer informatie over het maken van multi-VM-omgevingen en PaaS-resources in Azure DevTest Labs op basis van een Azure Resource Manager-sjabloon
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 1385b20847cf90c212a13591389dfb6cda08432a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169636"
---
# <a name="create-multi-vm-environments-and-paas-resources-with-azure-resource-manager-templates"></a>Multi-VM-omgevingen en PaaS-resources maken met Azure Resource Manager-sjablonen

Azure DevTest Labs-omgevingen stellen gebruikers in staat om complexe infrastructuren gemakkelijk op een consistente manier te implementeren binnen de grenzen van het lab. U [Azure Resource Manager-sjablonen](../azure-resource-manager/templates/template-syntax.md) gebruiken om omgevingen te maken met sets resources in DevTest Labs. Deze omgevingen kunnen alle Azure-bronnen bevatten die Resource Manager-sjablonen kunnen maken.

U eenvoudig [één virtuele machine (VM) tegelijk aan](devtest-lab-add-vm.md) een lab toevoegen met behulp van de [Azure-portal.](https://portal.azure.com) Scenario's zoals webapps met meerdere lagen of een SharePoint-farm hebben echter een mechanisme nodig om meerdere VM's in één stap te maken. Door Azure Resource Manager-sjablonen te gebruiken, u de infrastructuur en configuratie van uw Azure-oplossing definiëren en herhaaldelijk meerdere VM's implementeren in een consistente status.

Azure Resource Manager-sjablonen bieden ook de volgende voordelen:

- Azure Resource Manager-sjablonen worden rechtstreeks vanuit uw GitHub- of Azure Repos-bronbeheeropslagplaats geladen.
- Uw gebruikers kunnen een omgeving maken door een geconfigureerde Azure Resource Manager-sjabloon te kiezen uit de Azure-portal, net zoals ze doen met andere typen [VM-bases.](devtest-lab-comparing-vm-base-image-types.md)
- U Azure PaaS-resources en IaaS-VM's in een omgeving inrichten via een Azure Resource Manager-sjabloon.
- U de kosten van omgevingen in het lab bijhouden, naast afzonderlijke VM's die zijn gemaakt door andere soorten bases. PaaS-resources worden gemaakt en worden weergegeven in kostentracking. Het automatisch afsluiten van VM is echter niet van toepassing op PaaS-resources.

Zie Voordelen van het gebruik van Resource Manager-sjablonen voor het implementeren, bijwerken of verwijderen van veel labbronnen in één bewerking voor meer informatie over de voordelen [van het gebruik van Resource Manager-sjablonen.](../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager)

> [!NOTE]
> Wanneer u een Resource Manager-sjabloon als basis gebruikt om laboratorium-VM's te maken, zijn er enkele verschillen tussen het maken van meerdere VM's of één vm. Zie [De Azure Resource Manager-sjabloon](devtest-lab-use-resource-manager-template.md)van een virtuele machine gebruiken voor meer informatie.
>

## <a name="use-devtest-labs-public-environments"></a>DevTest Labs openbare omgevingen gebruiken
Azure DevTest Labs heeft een [openbare opslagplaats van Azure Resource Manager-sjablonen](https://github.com/Azure/azure-devtestlab/tree/master/Environments) die u gebruiken om omgevingen te maken zonder zelf verbinding te hoeven maken met een externe GitHub-bron. Deze openbare opslagplaats is vergelijkbaar met de openbare opslagplaats van artefacten die beschikbaar is in de Azure-portal voor elk lab dat u maakt. Met de omgevingsopslagplaats u snel aan de slag met vooraf geschreven omgevingssjablonen met weinig invoerparameters. Deze sjablonen bieden u een soepele ervaring op weg voor PaaS-bronnen in laboratoria.

In de openbare opslagplaats hebben het Team DevTest Labs en anderen veelgebruikte sjablonen gemaakt en gedeeld, zoals Azure Web Apps, Service Fabric Cluster en een SharePoint Farm-omgeving voor ontwikkeling. U deze sjablonen rechtstreeks gebruiken of aanpassen aan uw behoeften. Zie [Openbare omgevingen configureren en gebruiken in DevTest Labs](devtest-lab-configure-use-public-environments.md)voor meer informatie. Nadat u uw eigen sjablonen hebt gemaakt, u ze opslaan in deze opslagplaats om ze met anderen te delen of uw eigen Git-opslagplaats instellen.

<a name="configure-your-own-template-repositories"></a>
## <a name="create-your-own-template-repositories"></a>Uw eigen sjabloonrepositories maken

Als een van de aanbevolen procedures met infrastructuur-als-code en configuratie-as-code, moet u omgevingssjablonen beheren in bronbeheer. Azure DevTest Labs volgt deze praktijk en laadt alle Azure Resource Manager-sjablonen rechtstreeks vanuit uw GitHub- of Azure Repos-opslagplaatsen. Als gevolg hiervan u Resource Manager-sjablonen gebruiken voor de hele releasecyclus, van de testomgeving tot de productieomgeving.

Er zijn verschillende regels die u moet volgen om uw Azure Resource Manager-sjablonen in een opslagplaats te organiseren:

- U moet de naam van het hoofdsjabloonbestand *azuredeploy.json*benoemen.

- Als u parameterwaarden wilt gebruiken die in een parameterbestand zijn gedefinieerd, moet het parameterbestand de naam *azuredeploy.parameters.json*krijgen.

  U de `_artifactsLocation` `_artifactsLocationSasToken` parameters gebruiken en de parameters koppelen aan URI-waarde, zodat DevTest Labs geneste sjablonen automatisch kan beheren. Zie [Geneste Azure Resource Manager-sjablonen implementeren voor testomgevingen voor](deploy-nested-template-environments.md)meer informatie.

- U metagegevens definiëren om de naam en beschrijving van de sjabloonweergave op te geven in een bestand met de naam *metadata.json,* als volgt:

  ```json
  {
    "itemDisplayName": "<your template name>",
    "description": "<description of the template>"
  }
  ```

![Sjabloonbestanden van Azure Resource Manager](./media/devtest-lab-create-environment-from-arm/master-template.png)

## <a name="add-template-repositories-to-the-lab"></a>Sjabloonopslagplaatsen toevoegen aan het lab

Nadat u uw opslagplaats hebt gemaakt en geconfigureerd, u deze toevoegen aan uw lab met behulp van de Azure-portal:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer **Alle services**en selecteer Vervolgens **DevTest Labs** in de lijst.
1. Selecteer in de lijst met labs het lab dat u wilt.
1. Selecteer **Configuratie en beleid**in het deelvenster **Overzicht** van het lab.

   ![Configuratie en beleid](./media/devtest-lab-create-environment-from-arm/configuration-and-policies-menu.png)

1. Selecteer **Repositories**in de lijst **Configuratie- en beleidsinstellingen** . De **Public Artifact Repo** repository wordt automatisch gegenereerd voor alle labs en maakt verbinding met de [Openbare GitHub-repository van DevTest Labs.](https://github.com/Azure/azure-devtestlab)

1. Als u de sjabloonopslagplaats azure resource beheer wilt toevoegen, selecteert u **Toevoegen**.

   ![Openbare repo](./media/devtest-lab-create-environment-from-arm/public-repo.png)

1. Voer in het deelvenster **Repositories** de volgende gegevens in:

   - **Naam:** Voer een repository naam in om te gebruiken in het lab.
   - **Git-kloon-URL**: Voer de URL van de Git HTTPS-kloon van GitHub of Azure Repos in.
   - **Branch** (optioneel): Voer de branchnaam in om toegang te krijgen tot de sjabloondefinities van Azure Resource Manager.
   - **Persoonlijke toegangstoken:** voer het persoonlijke toegangstoken in dat wordt gebruikt om veilig toegang te krijgen tot uw opslagplaats.
     - Als u uw token wilt ophalen bij Azure Repos, selecteert u onder uw profiel **gebruikersinstellingen** > **Beveiligingstoegangstokens****Security** > .
     - Als u uw token bij GitHub wilt ophalen, selecteert u onder uw profiel De optie **Instellingen** > **Instellingen** > **Persoonlijke toegangstokens**voor ontwikkelaars instellingen .
   - **Mappaden:** voer het mappad in dat ten opzichte van uw Git-kloon URI is voor uw artefactdefinities of de definities van uw Azure Resource Manager-sjabloon.

1. Selecteer **Opslaan**.

   ![Nieuwe opslagplaats toevoegen](./media/devtest-lab-create-environment-from-arm/repo-values.png)

Zodra u een Azure Resource Manager-sjabloon aan het lab hebt toegevoegd, kunnen uw labgebruikers omgevingen maken met behulp van de sjabloon.

## <a name="configure-access-rights-for-lab-users"></a>Toegangsrechten configureren voor labgebruikers

Labgebruikers hebben standaard **de readerrol,** zodat ze de resources in een resourcegroep niet kunnen wijzigen. Ze kunnen bijvoorbeeld hun resources niet stoppen of starten.

Voer de volgende stappen uit om de rol van uw labgebruikers **in zenderbijdrage te** geven zodat ze de resources in hun omgeving kunnen bewerken:

1. Selecteer in de [Azure-portal](https://portal.azure.com)in het **deelvenster Overzicht** van uw lab de optie Configuratie **en beleid**en selecteer **vervolgens Lab-instellingen**.

1. Selecteer in het deelvenster **Lab-instellingen** **De optie Inzender**en selecteer **Opslaan** om schrijfmachtigingen toe te kennen aan labgebruikers.

   ![Toegangsrechten voor labgebruikers configureren](./media/devtest-lab-create-environment-from-arm/config-access-rights.png)

In de volgende sectie worden omgevingen gemaakt van een Azure Resource Manager-sjabloon.

## <a name="create-environments-from-templates-in-the-azure-portal"></a>Omgevingen maken op basis van sjablonen in de Azure-portal

Zodra u een Azure Resource Manager-sjabloon aan het lab hebt toegevoegd, kunnen uw labgebruikers omgevingen maken in de Azure-portal door de volgende stappen te volgen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Selecteer **Alle services**en selecteer Vervolgens **DevTest Labs** in de lijst.

1. Selecteer in de lijst met labs het lab dat u wilt.

1. Selecteer Op de pagina van het lab de optie **Toevoegen**.

1. In **het basisvenster kiezen** worden de basisafbeeldingen weergegeven die u gebruiken, waarbij de Azure Resource Manager-sjablonen als eerste worden vermeld. Selecteer de gewenste Azure Resource Manager-sjabloon.

   ![Een basis kiezen](./media/devtest-lab-create-environment-from-arm/choose-a-base.png)

1. Voer in het deelvenster **Toevoegen** een **omgevingsnaamwaarde** in die u wilt weergeven aan gebruikers van de omgeving.

   De sjabloon Azure Resource Manager definieert de rest van de invoervelden. Als het bestand *azuredeploy.parameter.json van template azuredeploy.json* standaardwaarden definieert, worden deze waarden in de invoervelden weergegeven.

   Voor parameters van type *secure string*u geheimen uit uw Azure Key Vault gebruiken. Zie [Winkelgeheimen in Azure Key Vault](devtest-lab-store-secrets-in-key-vault.md)voor meer informatie over het opslaan van geheimen in een sleutelkluis en deze te gebruiken bij het maken van labbronnen.  

   ![Deelvenster toevoegen](./media/devtest-lab-create-environment-from-arm/add.png)

   > [!NOTE]
   > De volgende parameterwaarden worden niet weergegeven in de invoervelden, zelfs niet als de sjabloon deze opgeeft. In plaats daarvan worden in het formulier lege invoervelden weergegeven waarin labgebruikers waarden moeten invoeren bij het maken van de omgeving.
   >
   > - GEN-UNIEK
   > - GEN-UNIEK-[N]
   > - GEN-SSH-PUB-KEY
   > - GEN-WACHTWOORD

1. Selecteer **Toevoegen** om de omgeving te maken.

   De omgeving begint onmiddellijk met inrichten, met de status die wordt weergegeven in de lijst **Mijn virtuele machines.** Het lab maakt automatisch een nieuwe brongroep om alle resources in te richten die zijn gedefinieerd in de sjabloon Azure Resource Manager.

1. Zodra de omgeving is gemaakt, selecteert u de omgeving in de lijst **Mijn virtuele machines** om het deelvenster Resourcegroep te openen en door alle resources te bladeren die de omgeving heeft ingericht.

   ![Milieubronnen](./media/devtest-lab-create-environment-from-arm/all-environment-resources.png)

   U de omgeving ook uitbreiden om alleen de lijst met VM's te bekijken die de omgeving heeft ingericht.

   ![Mijn lijst met virtuele machines](./media/devtest-lab-create-environment-from-arm/my-vm-list.png)

1. Selecteer een van de omgevingen om de beschikbare acties weer te geven, zoals het toepassen van artefacten, het koppelen van gegevensschijven, het wijzigen van de tijd voor automatisch afsluiten en meer.

   ![Milieuacties](./media/devtest-lab-create-environment-from-arm/environment-actions.png)

<a name="automate-deployment-of-environments"></a>
## <a name="automate-environment-creation-with-powershell"></a>Het maken van omgevingen automatiseren met PowerShell

Het is mogelijk om de Azure-portal te gebruiken om één omgeving aan een lab toe te voegen, maar wanneer een ontwikkel- of testscenario meerdere omgevingen moet maken, is geautomatiseerde implementatie een betere ervaring.

Voordat u verdergaat, moet u een Azure Resource Manager-sjabloon hebben waarmee de te maken resources worden gedefinieerd. [Voeg de sjabloon toe en configureer deze in een Git-opslagplaats](#configure-your-own-template-repositories)en [voeg de opslagplaats toe aan het lab.](#add-template-repositories-to-the-lab)

Met het volgende voorbeeldscript wordt een omgeving in uw lab gemaakt. De opmerkingen helpen u het script beter te begrijpen.

1. Sla het volgende voorbeeld PowerShell-script op uw harde schijf op als *deployenv.ps1*.

   [!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

   ```powershell
   #Requires -Module Az.Resources

   [CmdletBinding()]

   param (
   # ID of the Azure Subscription for the lab
   [string] [Parameter(Mandatory=$true)] $SubscriptionId,

   # Name of the existing lab in which to create the environment
   [string] [Parameter(Mandatory=$true)] $LabName,

   # Name of the connected repository in the lab
   [string] [Parameter(Mandatory=$true)] $RepositoryName,

   # Name of the template (folder name in the Git repository)
   [string] [Parameter(Mandatory=$true)] $TemplateName,

   # Name of the environment to be created in the lab
   [string] [Parameter(Mandatory=$true)] $EnvironmentName,

   # The parameters to be passed to the template. Each parameter is prefixed with "-param_".
   # For example, if the template has a parameter named "TestVMName" with a value of "MyVMName",
   # the string in $Params will have the form: -param_TestVMName MyVMName.
   # This convention allows the script to dynamically handle different templates.
   [Parameter(ValueFromRemainingArguments=$true)]
       $Params
   )

   # Sign in to Azure.
   # Comment out the following statement to completely automate the environment creation.
   Connect-AzAccount

   # Select the subscription that has the lab.  
   Set-AzContext -SubscriptionId $SubscriptionId | Out-Null

   # Get information about the user, specifically the user ID, which is used later in the script.  
   $UserId = $((Get-AzADUser -UserPrincipalName (Get-AzContext).Account).Id.Guid)

   # Get information about the lab, such as lab location.
   $lab = Get-AzResource -ResourceType "Microsoft.DevTestLab/labs" -Name $LabName -ResourceGroupName $ResourceGroupName
   if ($lab -eq $null) { throw "Unable to find lab $LabName in subscription $SubscriptionId." }

   # Get information about the repository in the lab.
   $repository = Get-AzResource -ResourceGroupName $lab.ResourceGroupName `
       -ResourceType 'Microsoft.DevTestLab/labs/artifactsources' `
       -ResourceName $LabName `
       -ApiVersion 2016-05-15 `
       | Where-Object { $RepositoryName -in ($_.Name, $_.Properties.displayName) } `
       | Select-Object -First 1
   if ($repository -eq $null) { throw "Unable to find repository $RepositoryName in lab $LabName." }

   # Get information about the Resource Manager template base for the environment.
   $template = Get-AzResource -ResourceGroupName $lab.ResourceGroupName `
       -ResourceType "Microsoft.DevTestLab/labs/artifactSources/armTemplates" `
       -ResourceName "$LabName/$($repository.Name)" `
       -ApiVersion 2016-05-15 `
       | Where-Object { $TemplateName -in ($_.Name, $_.Properties.displayName) } `
       | Select-Object -First 1
   if ($template -eq $null) { throw "Unable to find template $TemplateName in lab $LabName." }

   # Build the template parameters with parameter name and values.  
   $parameters = Get-Member -InputObject $template.Properties.contents.parameters -MemberType NoteProperty | Select-Object -ExpandProperty Name
   $templateParameters = @()

   # Extract the custom parameters from $Params and format as name/value pairs.
   $Params | ForEach-Object {
       if ($_ -match '^-param_(.*)' -and $Matches[1] -in $parameters) {
           $name = $Matches[1]                
       } elseif ( $name ) {
           $templateParameters += @{ "name" = "$name"; "value" = "$_" }
           $name = $null #reset name variable
       }
   }

   # Once name/value pairs are isolated, create an object to hold the necessary template properties.
   $templateProperties = @{ "deploymentProperties" = @{ "armTemplateId" = "$($template.ResourceId)"; "parameters" = $templateParameters }; }

   # Now, create or deploy the environment in the lab by using the New-AzResource command.
   New-AzResource -Location $Lab.Location `
       -ResourceGroupName $lab.ResourceGroupName `
       -Properties $templateProperties `
       -ResourceType 'Microsoft.DevTestLab/labs/users/environments' `
       -ResourceName "$LabName/$UserId/$EnvironmentName" `
       -ApiVersion '2016-05-15' -Force

   Write-Output "Environment $EnvironmentName completed."
   ```

1. Voer het script als volgt uit met uw specifieke waarden voor SubscriptionId, LabName, ResourceGroupName, RepositoryName, TemplateName (map in de Git repo) en EnvironmentName.

   ```powershell
   ./deployenv.ps1 -SubscriptionId "000000000-0000-0000-0000-0000000000000" -LabName "mydevtestlab" -ResourceGroupName "mydevtestlabRG000000" -RepositoryName "myRepository" -TemplateName "My Environment template name" -EnvironmentName "myGroupEnv"
   ```

U Azure CLI ook gebruiken om resources te implementeren met Resource Manager-sjablonen. Zie [Resources implementeren met Resource Manager-sjablonen en Azure CLI voor](../azure-resource-manager/templates/deploy-cli.md)meer informatie.

> [!NOTE]
> Alleen een gebruiker met machtigingen voor labeigenaarkan VM's maken op basis van een Resource Manager-sjabloon met Azure PowerShell. Als u het maken van vm's wilt automatiseren met behulp van een Resource Manager-sjabloon en u alleen gebruikersmachtigingen hebt, u de WIJZIGINGsvm van de OPDRACHT [CLI-opdracht AZ-lab gebruiken.](/cli/azure/lab/vm#az-lab-vm-create)

## <a name="resource-manager-template-limitations-in-devtest-labs"></a>Beperkingen van resourcebeheer-sjablonen in DevTest Labs

Houd rekening met deze beperkingen bij het gebruik van Resource Manager-sjablonen in DevTest Labs:

- Resourcebeheersjablonen kunnen niet verwijzen naar de meeste bestaande bronnen. Ze kunnen alleen nieuwe bronnen maken. Als u Resource Manager-sjablonen hebt die u buiten DevTest Labs gebruikt en die verwijzen naar bestaande bronnen, u deze niet gebruiken in DevTest Labs. De enige uitzondering is dat u verwijzen naar een bestaand virtueel netwerk.

- U geen formules of aangepaste afbeeldingen maken van vm's uit het lab die zijn gemaakt op basis van een resourcemanagersjabloon.

- De meeste beleidsregels worden niet geëvalueerd wanneer u Resource Manager-sjablonen implementeert.

  U hebt bijvoorbeeld een labbeleid dat een gebruiker slechts vijf VM's kan maken. Een gebruiker kan echter een resourcemanagersjabloon implementeren waarmee tientallen VM's zijn gemaakt. Beleidsregels die niet worden geëvalueerd, zijn onder andere:

  - Aantal VM's per gebruiker

  - Aantal premium VM's per labgebruiker

  - Aantal premium schijven per labgebruiker

## <a name="next-steps"></a>Volgende stappen
- Zodra u een vm hebt gemaakt, u verbinding maken met de virtuele machine door **Verbinding maken** te selecteren in het beheervenster van de virtuele machine.
- Resources in een omgeving weergeven en beheren door de omgeving te selecteren in de lijst **Mijn virtuele machines** in uw lab.
- Ontdek de [Azure Resource Manager-sjablonen in de azure Quickstart-sjabloongalerie](https://github.com/Azure/azure-quickstart-templates).
