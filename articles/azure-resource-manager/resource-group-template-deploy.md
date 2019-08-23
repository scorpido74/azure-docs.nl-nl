---
title: Resources implementeren met Power shell en sjabloon | Microsoft Docs
description: Gebruik Azure Resource Manager en Azure PowerShell om resources te implementeren in Azure. De resources zijn gedefinieerd in een Resource Manager-sjabloon.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: tomfitz
ms.openlocfilehash: 1f9fb786933d03b27be47c9f778a5f1575ca17c2
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69970908"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>Resources implementeren met Resource Manager-sjablonen en Azure PowerShell

Meer informatie over het gebruik van Azure PowerShell met Resource Manager-sjablonen voor het implementeren van uw resources in Azure. Zie [Azure Resource Manager Overview](resource-group-overview.md)voor meer informatie over de concepten van het implementeren en beheren van uw Azure-oplossingen.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="deployment-scope"></a>Implementatie bereik

U kunt uw implementatie richten op een Azure-abonnement of een resource groep binnen een abonnement. In de meeste gevallen moet u de implementatie richten op een resource groep. Gebruik abonnements implementaties om beleids regels en roltoewijzingen toe te passen op het abonnement. U kunt ook abonnements implementaties gebruiken voor het maken van een resource groep en het implementeren van resources. Afhankelijk van het bereik van de implementatie, gebruikt u verschillende opdrachten.

Als u wilt implementeren in een **resource groep**, gebruikt u [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment):

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <path-to-template>
```

Als u wilt implementeren in een **abonnement**, gebruikt u [New-AzDeployment](/powershell/module/az.resources/new-azdeployment):

```azurepowershell
New-AzDeployment -Location <location> -TemplateFile <path-to-template>
```

Op dit moment worden implementaties van beheer groepen alleen ondersteund via de REST API. Zie [resources implementeren met Resource Manager-sjablonen en resource manager rest API](resource-group-template-deploy-rest.md).

In de voor beelden in dit artikel worden de implementaties van resource groepen gebruikt. Zie [resource groepen en-resources op abonnements niveau maken](deploy-to-subscription.md)voor meer informatie over abonnements implementaties.

## <a name="prerequisites"></a>Vereisten

U hebt een sjabloon nodig om te implementeren. Als u er nog geen hebt, kunt u een [voorbeeld sjabloon](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) downloaden en opslaan vanuit de Azure Quick Start-sjablonen opslag plaats. De lokale bestands naam die in dit artikel wordt gebruikt, is **c:\MyTemplates\azuredeploy.json**.

Tenzij u de Azure Cloud shell gebruikt om sjablonen te implementeren, moet u Azure PowerShell installeren en verbinding maken met Azure:

- **Installeer Azure PowerShell-cmdlets op de lokale computer.** Zie [Aan de slag met Azure PowerShell](/powershell/azure/get-started-azureps) voor meer informatie.
- **Maak verbinding met Azure via [Connect-AZAccount](/powershell/module/az.accounts/connect-azaccount)** . Als u meerdere Azure-abonnementen hebt, moet u mogelijk ook [set-AzContext](/powershell/module/Az.Accounts/Set-AzContext)uitvoeren. Zie [meerdere Azure-abonnementen gebruiken](/powershell/azure/manage-subscriptions-azureps)voor meer informatie.

## <a name="deploy-local-template"></a>Lokale sjabloon implementeren

In het volgende voor beeld wordt een resource groep gemaakt en een sjabloon van uw lokale computer geïmplementeerd. De naam van de resource groep mag alleen alfanumerieke tekens, punten, onderstrepings teken, afbreek streepjes en haakjes bevatten. Het kan Maxi maal 90 tekens lang zijn. Deze kan niet eindigen op een punt.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json
```

De implementatie kan enkele minuten duren.

## <a name="deploy-remote-template"></a>Externe sjabloon implementeren

In plaats van het opslaan van Resource Manager-sjablonen op de lokale computer, kunt u ze beter opslaan op een externe locatie. U kunt sjablonen opslaan in een broncode beheer bibliotheek (zoals GitHub). U kunt ze ook opslaan in een Azure-opslag account voor gedeelde toegang in uw organisatie.

Als u een externe sjabloon wilt implementeren, gebruikt u de para meter **TemplateUri** . Gebruik de URI in het voor beeld om de voorbeeld sjabloon te implementeren vanuit GitHub.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

In het voor gaande voor beeld is een openbaar toegankelijke URI vereist voor de sjabloon, die voor de meeste scenario's werkt, omdat uw sjabloon geen gevoelige gegevens mag bevatten. Als u gevoelige gegevens (zoals een beheerders wachtwoord) moet opgeven, geeft u die waarde als een beveiligde para meter door. Als u niet wilt dat uw sjabloon openbaar toegankelijk is, kunt u deze beveiligen door deze op te slaan in een persoonlijke opslag container. Zie voor meer informatie over het implementeren van een sjabloon waarvoor een SAS-token (Shared Access Signature) is vereist een [persoonlijke sjabloon implementeren met SAS-token](resource-manager-powershell-sas-token.md). Zie [zelf studie als u een zelf studie wilt door lopen: Azure Key Vault integreren in de Resource Manager-sjabloonimplementatie](./resource-manager-tutorial-use-key-vault.md) voor meer informatie.

## <a name="deploy-from-azure-cloud-shell"></a>Implementeren vanuit Azure Cloud shell

U kunt de [Azure Cloud shell](https://shell.azure.com) gebruiken om uw sjabloon te implementeren. Als u een externe sjabloon wilt implementeren, geeft u de URI van de sjabloon op. Als u een lokale sjabloon wilt implementeren, moet u uw sjabloon eerst laden in het opslag account voor uw Cloud Shell. Als u bestanden naar de shell wilt uploaden, selecteert u het menu pictogram **bestanden uploaden/downloaden** in het shell-venster.

Als u de Cloud shell wilt openen, [https://shell.azure.com](https://shell.azure.com)bladert u naar of selecteert u **try-it** in de volgende code sectie:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Als u de code in de shell wilt plakken, klikt u met de rechter muisknop in de shell en selecteert u **Plakken**.

## <a name="redeploy-when-deployment-fails"></a>Opnieuw implementeren wanneer de implementatie mislukt

Deze functie wordt ook wel bekend als *Rollback bij fout*. Wanneer een implementatie mislukt, kunt u automatisch een eerdere, geslaagde implementatie uit de implementatie geschiedenis opnieuw implementeren. Als u een herimplementatie wilt opgeven, `-RollbackToLastDeployment` gebruikt `-RollBackDeploymentName` u de para meter of in de implementatie opdracht. Deze functie is handig als u een bekende goede status hebt voor de implementatie van uw infra structuur en u wilt terugkeren naar deze status. Er zijn een aantal voor behoud en beperkingen:

- De implementatie wordt precies zo uitgevoerd als deze eerder met dezelfde para meters is uitgevoerd. U kunt de para meters niet wijzigen.
- De vorige implementatie wordt uitgevoerd met de [volledige modus](./deployment-modes.md#complete-mode). Alle resources die geen deel uitmaken van de vorige implementatie worden verwijderd en alle resource configuraties worden ingesteld op de vorige status. Zorg ervoor dat u de [implementatie modi](./deployment-modes.md)volledig begrijpt.
- De herimplementatie heeft alleen invloed op de resources, maar wijzigingen in de gegevens worden niet beïnvloed.
- Deze functie wordt alleen ondersteund voor implementaties van resource groepen, geen implementaties op abonnements niveau. Zie [resource groepen en-resources op abonnements niveau maken](./deploy-to-subscription.md)voor meer informatie over de implementatie op abonnements niveau.

Als u deze optie wilt gebruiken, moeten uw implementaties unieke namen hebben zodat deze in de geschiedenis kunnen worden geïdentificeerd. Als u geen unieke namen hebt, kan de huidige mislukte implementatie de eerder geslaagde implementatie in de geschiedenis overschrijven. U kunt deze optie alleen gebruiken bij implementaties op hoofd niveau. Implementaties van een geneste sjabloon zijn niet beschikbaar voor opnieuw implementeren.

Als u de laatste geslaagde implementatie opnieuw wilt implementeren `-RollbackToLastDeployment` , voegt u de para meter toe als een vlag.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollbackToLastDeployment
```

Als u een specifieke implementatie opnieuw wilt implementeren, `-RollBackDeploymentName` gebruikt u de para meter en geeft u de naam van de implementatie op.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollBackDeploymentName ExampleDeployment01
```

De opgegeven implementatie moet zijn geslaagd.

## <a name="pass-parameter-values"></a>Parameter waarden door geven

Als u parameter waarden wilt door geven, kunt u inline-para meters of een parameter bestand gebruiken.

### <a name="inline-parameters"></a>Inline-para meters

Als u inline-para meters wilt door geven, geeft u `New-AzResourceGroupDeployment` de namen van de para meter op met de opdracht. Als u bijvoorbeeld een teken reeks en een matrix wilt door geven aan een sjabloon, gebruikt u:

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString "inline string" `
  -exampleArray $arrayParam
```

U kunt ook de inhoud van het bestand ophalen en deze inhoud als een inline-para meter opgeven.

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString $(Get-Content -Path c:\MyTemplates\stringcontent.txt -Raw) `
  -exampleArray $arrayParam
```

Het ophalen van een parameter waarde uit een bestand is handig wanneer u configuratie waarden moet opgeven. U kunt bijvoorbeeld [Cloud-init-waarden opgeven voor een virtuele Linux-machine](../virtual-machines/linux/using-cloud-init.md).

Als u een matrix met objecten wilt door geven, maakt u hash-tabellen in Power shell en voegt u deze toe aan een matrix. Geef deze matrix als een para meter door tijdens de implementatie.

```powershell
$hash1 = @{ Name = "firstSubnet"; AddressPrefix = "10.0.0.0/24"}
$hash2 = @{ Name = "secondSubnet"; AddressPrefix = "10.0.1.0/24"}
$subnetArray = $hash1, $hash2
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleArray $subnetArray
```

### <a name="parameter-files"></a>Parameter bestanden

In plaats van para meters als inline waarden door te geven in uw script, is het wellicht eenvoudiger een JSON-bestand te gebruiken dat de parameter waarden bevat. Het parameter bestand kan een lokaal bestand of een extern bestand met een toegankelijke URI zijn.

Zie voor meer informatie over het parameter bestand [Resource Manager-parameter bestand maken](resource-manager-parameter-files.md).

Als u een lokaal parameter bestand wilt door geven, gebruikt u de para meter **TemplateParameterFile** :

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

Als u een extern parameter bestand wilt door geven, gebruikt u de para meter **TemplateParameterUri** :

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json
```

## <a name="test-template-deployments"></a>Sjabloon implementaties testen

Als u uw sjabloon en parameter waarden wilt testen zonder daad werkelijk resources te implementeren, gebruikt u [test-AzureRmResourceGroupDeployment](/powershell/module/az.resources/test-azresourcegroupdeployment). 

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json -storageAccountType Standard_GRS
```

Als er geen fouten worden gedetecteerd, wordt de opdracht zonder antwoord voltooid. Als er een fout wordt gedetecteerd, retourneert de opdracht een fout bericht. Als er bijvoorbeeld een onjuiste waarde voor de SKU van het opslag account wordt door gegeven, wordt de volgende fout geretourneerd:

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json -storageAccountType badSku

Code    : InvalidTemplate
Message : Deployment template validation failed: 'The provided value 'badSku' for the template parameter 'storageAccountType'
          at line '15' and column '24' is not valid. The parameter value is not part of the allowed value(s):
          'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.
Details :
```

Als uw sjabloon een syntaxis fout bevat, retourneert de opdracht een fout melding die aangeeft dat de sjabloon niet kan worden geparseerd. Het bericht geeft het regel nummer en de positie van de Parseerfout aan.

```powershell
Test-AzResourceGroupDeployment : After parsing a value an unexpected character was encountered: 
  ". Path 'variables', line 31, position 3.
```

## <a name="next-steps"></a>Volgende stappen

- Zie [Azure Deployment Manager](deployment-manager-overview.md)als u uw service veilig wilt implementeren in meer dan één regio.
- Zie [Azure Resource Manager implementatie modi](deployment-modes.md)om op te geven hoe u resources wilt afhandelen die in de resource groep aanwezig zijn, maar die niet zijn gedefinieerd in de sjabloon.
- Zie [inzicht in de structuur en syntaxis van Azure Resource Manager sjablonen](resource-group-authoring-templates.md)voor meer informatie over het definiëren van para meters in uw sjabloon.
- Zie voor meer informatie over het implementeren van een sjabloon waarvoor een SAS-token is vereist een [persoonlijke sjabloon implementeren met SAS-token](resource-manager-powershell-sas-token.md).
