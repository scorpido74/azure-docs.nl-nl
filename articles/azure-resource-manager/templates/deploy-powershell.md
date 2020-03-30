---
title: Resources implementeren met PowerShell en sjabloon
description: Gebruik Azure Resource Manager en Azure PowerShell om resources te implementeren in Azure. De resources zijn gedefinieerd in een Resource Manager-sjabloon.
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: e595aa8f86a24e59c8e00d24ea8e9dcb0875a8f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153264"
---
# <a name="deploy-resources-with-arm-templates-and-azure-powershell"></a>Resources implementeren met ARM-sjablonen en Azure PowerShell

Meer informatie over het gebruik van Azure PowerShell met ARM-sjablonen (Azure Resource Manager) om uw resources te implementeren in Azure. Zie overzicht van de implementatie van sjablonen voor meer informatie over de concepten voor het implementeren en beheren van uw [Azure-oplossingen.](overview.md)

## <a name="deployment-scope"></a>Implementatiebereik

U uw implementatie targeten op een resourcegroep, abonnement, beheergroep of tenant. In de meeste gevallen targetu de implementatie op een resourcegroep. Als u beleids- en roltoewijzingen wilt toepassen in een groter bereik, gebruikt u abonnements-, beheergroep- of tenantimplementaties. Wanneer u een abonnement implementeert, u een resourcegroep maken en er resources op implementeren.

Afhankelijk van het bereik van de implementatie gebruikt u verschillende opdrachten.

Als u wilt implementeren in een **resourcegroep,** gebruikt u [Nieuw-AzResourceGroupDeployment:](/powershell/module/az.resources/new-azresourcegroupdeployment)

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <path-to-template>
```

Als u wilt implementeren op een **abonnement,** gebruikt u Nieuwe-AzSubscriptionDeployment:

```azurepowershell
New-AzSubscriptionDeployment -Location <location> -TemplateFile <path-to-template>
```

Zie [Resourcegroepen en resources maken op abonnementsniveau](deploy-to-subscription.md)voor meer informatie over implementaties op abonnementsniveau.

Als u wilt implementeren in een **beheergroep,** gebruikt u [New-AzManagementGroupDeployment](/powershell/module/az.resources/New-AzManagementGroupDeployment).

```azurepowershell
New-AzManagementGroupDeployment -Location <location> -TemplateFile <path-to-template>
```

Zie [Resources maken op het niveau van de beheergroep voor](deploy-to-management-group.md)meer informatie over implementaties op managementgroepenniveau.

Als u wilt implementeren in een **tenant,** gebruikt u [Nieuw-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment).

```azurepowershell
New-AzTenantDeployment -Location <location> -TemplateFile <path-to-template>
```

Zie Resources maken op tenantniveau voor meer informatie over implementaties op [tenantniveau.](deploy-to-tenant.md)

De voorbeelden in dit artikel gebruiken implementaties van resourcegroepen.

## <a name="prerequisites"></a>Vereisten

U hebt een sjabloon nodig om te implementeren. Als u er nog geen hebt, downloadt en slaat u een [voorbeeldsjabloon](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) op van de repo Azure Quickstart-sjablonen. De lokale bestandsnaam die in dit artikel wordt gebruikt, is **c:\MyTemplates\azuredeploy.json**.

Tenzij u de Azure Cloud Shell gebruikt om sjablonen te implementeren, moet u Azure PowerShell installeren en verbinding maken met Azure:

- **Installeer Azure PowerShell-cmdlets op uw lokale computer.** Zie [Aan de slag met Azure PowerShell](/powershell/azure/get-started-azureps) voor meer informatie.
- **Maak verbinding met Azure via [Connect-AZAccount](/powershell/module/az.accounts/connect-azaccount)**. Als u meerdere Azure-abonnementen hebt, moet u mogelijk ook [Set-AzContext](/powershell/module/Az.Accounts/Set-AzContext)uitvoeren. Zie [Meerdere Azure-abonnementen gebruiken](/powershell/azure/manage-subscriptions-azureps)voor meer informatie.

## <a name="deploy-local-template"></a>Lokale sjabloon implementeren

In het volgende voorbeeld wordt een resourcegroep gemaakt en wordt een sjabloon van uw lokale machine geïmplementeerd. De naam van de resourcegroep kan alleen alfanumerieke tekens, perioden, underscores, koppeltekens en haakjes bevatten. Het kan maximaal 90 tekens zijn. Het kan niet eindigen in een periode.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json
```

De implementatie kan enkele minuten duren.

## <a name="deploy-remote-template"></a>Externe sjabloon implementeren

In plaats van ARM-sjablonen op uw lokale machine op te slaan, u deze liever opslaan op een externe locatie. U sjablonen opslaan in een bronbeheeropslagplaats (zoals GitHub). U ze ook opslaan in een Azure-opslagaccount voor gedeelde toegang in uw organisatie.

Als u een externe sjabloon wilt implementeren, gebruikt u de parameter **TemplateUri.** Gebruik de URI in het voorbeeld om de voorbeeldsjabloon van GitHub te implementeren.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Het voorgaande voorbeeld vereist een openbaar toegankelijke URI voor de sjabloon, die werkt voor de meeste scenario's omdat uw sjabloon geen gevoelige gegevens mag bevatten. Als u gevoelige gegevens moet opgeven (zoals een beheerderswachtwoord), geeft u die waarde door als een veilige parameter. Als u echter niet wilt dat uw sjabloon openbaar toegankelijk is, u deze beveiligen door deze op te slaan in een privéopslagcontainer. Zie [Privésjabloon implementeren met SAS-token voor](secure-template-with-sas-token.md)informatie over het implementeren van een sjabloon waarvoor een SAS-token (Shared Access Signature) vereist is. Zie [Zelfstudie: Azure Key Vault integreren in ARM-sjabloonimplementatie](template-tutorial-use-key-vault.md)voor het doorlopen van een zelfstudie.

## <a name="deploy-from-azure-cloud-shell"></a>Implementeren vanuit Azure Cloud Shell

U de [Azure Cloud Shell](https://shell.azure.com) gebruiken om uw sjabloon te implementeren. Als u een externe sjabloon wilt implementeren, geeft u de URI van de sjabloon op. Als u een lokale sjabloon wilt implementeren, moet u uw sjabloon eerst laden in het opslagaccount voor uw Cloud Shell. Als u bestanden naar de shell wilt uploaden, selecteert u het **menupictogram Bestanden uploaden/downloaden** in het shell-venster.

Als u de cloudshell [https://shell.azure.com](https://shell.azure.com)wilt openen, bladert u naar of selecteert u **Try-It** in de volgende codesectie:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Als u de code in de shell wilt plakken, klikt u met de rechtermuisknop in de schaal en selecteert u **Plakken**.

## <a name="pass-parameter-values"></a>Parameterwaarden doorgeven

Als u parameterwaarden wilt doorgeven, u inlineparameters of een parameterbestand gebruiken.

### <a name="inline-parameters"></a>Inlineparameters

Als u inlineparameters wilt doorgeven, geeft `New-AzResourceGroupDeployment` u de namen van de parameter op met de opdracht. Als u bijvoorbeeld een tekenreeks en array wilt doorgeven aan een sjabloon, gebruikt u:

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString "inline string" `
  -exampleArray $arrayParam
```

U ook de inhoud van het bestand krijgen en die inhoud als inlineparameter opgeven.

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString $(Get-Content -Path c:\MyTemplates\stringcontent.txt -Raw) `
  -exampleArray $arrayParam
```

Het ophalen van een parameterwaarde uit een bestand is handig wanneer u configuratiewaarden moet opgeven. U bijvoorbeeld [cloud-init-waarden opgeven voor een virtuele Linux-machine.](../../virtual-machines/linux/using-cloud-init.md)

Als u een array met objecten wilt doorgeven, maakt u hashtabellen in PowerShell en voegt u deze toe aan een array. Geef die array als parameter tijdens de implementatie.

```powershell
$hash1 = @{ Name = "firstSubnet"; AddressPrefix = "10.0.0.0/24"}
$hash2 = @{ Name = "secondSubnet"; AddressPrefix = "10.0.1.0/24"}
$subnetArray = $hash1, $hash2
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleArray $subnetArray
```

### <a name="parameter-files"></a>Parameterbestanden

In plaats van parameters door te geven als inlinewaarden in uw script, u het gemakkelijker vinden om een JSON-bestand te gebruiken dat de parameterwaarden bevat. Het parameterbestand kan een lokaal bestand zijn of een extern bestand met een toegankelijke URI.

Zie [Resourcebeheer-parameterbestand maken](parameter-files.md)voor meer informatie over het parameterbestand .

Als u een lokaal parameterbestand wilt doorgeven, gebruikt u de parameter **TemplateParameterFile:**

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

Als u een extern parameterbestand wilt doorgeven, gebruikt u de parameter **TemplateParameterUri:**

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json
```

## <a name="test-template-deployments"></a>Sjabloonimplementaties testen

Als u uw sjabloon- en parameterwaarden wilt testen zonder resources daadwerkelijk te implementeren, gebruikt u [Test-AzResourceGroupDeployment](/powershell/module/az.resources/test-azresourcegroupdeployment). 

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json -storageAccountType Standard_GRS
```

Als er geen fouten worden gedetecteerd, wordt de opdracht zonder antwoord voltooid. Als er een fout wordt gedetecteerd, retourneert de opdracht een foutbericht. Als u bijvoorbeeld een onjuiste waarde doorgeeft voor de SKU van het opslagaccount, wordt de volgende fout geretourneerd:

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json -storageAccountType badSku

Code    : InvalidTemplate
Message : Deployment template validation failed: 'The provided value 'badSku' for the template parameter 'storageAccountType'
          at line '15' and column '24' is not valid. The parameter value is not part of the allowed value(s):
          'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.
Details :
```

Als er een syntaxisfout in uw sjabloon is, wordt met de opdracht een fout geretourneerd die aangeeft dat de sjabloon niet kan worden ontwenoft. Het bericht geeft het regelnummer en de positie van de parsingfout aan.

```powershell
Test-AzResourceGroupDeployment : After parsing a value an unexpected character was encountered: 
  ". Path 'variables', line 31, position 3.
```

## <a name="next-steps"></a>Volgende stappen

- Als u wilt terugdraaien naar een geslaagde implementatie wanneer er een fout optreedt, raadpleegt u [Rollback-on error naar succesvolle implementatie](rollback-on-error.md).
- Zie [Azure Resource Manager-implementatiemodi](deployment-modes.md)als u wilt opgeven hoe u resources in de resourcegroep verwerken, maar niet in de sjabloon bent gedefinieerd.
- Zie [De structuur en syntaxis van ARM-sjablonen](template-syntax.md)begrijpen als u wilt begrijpen hoe u parameters in uw sjabloon definiëren.
- Zie [Privésjabloon implementeren met SAS-token voor](secure-template-with-sas-token.md)informatie over het implementeren van een sjabloon waarvoor een SAS-token vereist is.
