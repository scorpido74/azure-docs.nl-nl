---
title: Resources implementeren met Power shell en sjabloon
description: Gebruik Azure Resource Manager en Azure PowerShell om resources te implementeren in Azure. De resources zijn gedefinieerd in een Resource Manager-sjabloon.
ms.topic: conceptual
ms.date: 09/08/2020
ms.openlocfilehash: e47de54558962215fe3be78f5b9c45c8d46c54a3
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91372439"
---
# <a name="deploy-resources-with-arm-templates-and-azure-powershell"></a>Resources implementeren met ARM-sjablonen en Azure PowerShell

In dit artikel wordt uitgelegd hoe u Azure PowerShell kunt gebruiken met Azure Resource Manager sjablonen (ARM-sjablonen) om uw resources te implementeren in Azure. Als u niet bekend bent met de concepten van het implementeren en beheren van uw Azure-oplossingen, raadpleegt u [overzicht van sjabloon implementatie](overview.md).

## <a name="deployment-scope"></a>Implementatie bereik

U kunt uw implementatie richten op een resource groep, een abonnement, een beheer groep of een Tenant. In de meeste gevallen moet u de implementatie richten op een resource groep. Als u beleids regels en roltoewijzingen wilt Toep assen in een groter bereik, gebruikt u abonnement, beheer groep of Tenant implementaties. Wanneer u naar een abonnement implementeert, kunt u een resource groep maken en er resources op implementeren.

Afhankelijk van het bereik van de implementatie, gebruikt u verschillende opdrachten.

* Als u wilt implementeren in een **resource groep**, gebruikt u [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment):

  ```azurepowershell
  New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <path-to-template>
  ```

* Als u wilt implementeren in een **abonnement**, gebruikt u New-AzSubscriptionDeployment:

  ```azurepowershell
  New-AzSubscriptionDeployment -Location <location> -TemplateFile <path-to-template>
  ```

  Zie [resource groepen en-resources op abonnements niveau maken](deploy-to-subscription.md)voor meer informatie over implementaties op abonnements niveau.

* Als u wilt implementeren in een **beheer groep**, gebruikt u [New-AzManagementGroupDeployment](/powershell/module/az.resources/New-AzManagementGroupDeployment).

  ```azurepowershell
  New-AzManagementGroupDeployment -Location <location> -TemplateFile <path-to-template>
  ```

  Zie [resources maken op het niveau van de beheer groep](deploy-to-management-group.md)voor meer informatie over implementaties op het niveau van beheer groepen.

* Gebruik [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment)om te implementeren in een **Tenant**.

  ```azurepowershell
  New-AzTenantDeployment -Location <location> -TemplateFile <path-to-template>
  ```

  Zie [resources maken op Tenant niveau](deploy-to-tenant.md)voor meer informatie over implementaties op Tenant niveau.

In de voor beelden in dit artikel worden de implementaties van resource groepen gebruikt.

## <a name="prerequisites"></a>Vereisten

U hebt een sjabloon nodig om te implementeren. Als u er nog geen hebt, kunt u een [voorbeeld sjabloon](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) downloaden en opslaan vanuit de Azure Quick Start-sjablonen opslag plaats. De lokale bestands naam die in dit artikel wordt gebruikt, is **c:\MyTemplates\azuredeploy.jsop**.

Tenzij u de Azure Cloud Shell gebruikt om sjablonen te implementeren, moet u Azure PowerShell installeren en verbinding maken met Azure:

- **Installeer Azure PowerShell-cmdlets op de lokale computer.** Zie [Aan de slag met Azure PowerShell](/powershell/azure/get-started-azureps) voor meer informatie.
- **Maak verbinding met Azure via [Connect-AZAccount](/powershell/module/az.accounts/connect-azaccount)**. Als u meerdere Azure-abonnementen hebt, moet u mogelijk ook [set-AzContext](/powershell/module/Az.Accounts/Set-AzContext)uitvoeren. Zie [meerdere Azure-abonnementen gebruiken](/powershell/azure/manage-subscriptions-azureps)voor meer informatie.

## <a name="deploy-local-template"></a>Lokale sjabloon implementeren

In het volgende voor beeld wordt een resource groep gemaakt en een sjabloon van uw lokale computer geïmplementeerd. De naam van de resource groep mag alleen alfanumerieke tekens, punten, onderstrepings teken, afbreek streepjes en haakjes bevatten. Het kan Maxi maal 90 tekens lang zijn. Deze kan niet eindigen op een punt.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -Name ExampleDeployment `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json
```

De implementatie kan enkele minuten duren.

## <a name="deployment-name"></a>Naam van implementatie

In het voor gaande voor beeld heet u de implementatie `ExampleDeployment` . Als u geen naam opgeeft voor de implementatie, wordt de naam van het sjabloon bestand gebruikt. Als u bijvoorbeeld een sjabloon implementeert `azuredeploy.json` met de naam en u geen implementatie naam opgeeft, wordt de implementatie een naam genoemd `azuredeploy` .

Telkens wanneer u een implementatie uitvoert, wordt een item toegevoegd aan de implementatie geschiedenis van de resource groep met de naam van de implementatie. Als u een andere implementatie uitvoert en deze dezelfde naam geeft, wordt de vorige vermelding vervangen door de huidige implementatie. Als u de unieke vermeldingen in de implementatie geschiedenis wilt behouden, geeft u elke implementatie een unieke naam.

Als u een unieke naam wilt maken, kunt u een wille keurig getal toewijzen.

```azurepowershell-interactive
$suffix = Get-Random -Maximum 1000
$deploymentName = "ExampleDeployment" + $suffix
```

U kunt ook een datum waarde toevoegen.

```azurepowershell-interactive
$today=Get-Date -Format "MM-dd-yyyy"
$deploymentName="ExampleDeployment"+"$today"
```

Als u gelijktijdige implementaties uitvoert naar dezelfde resource groep met dezelfde implementatie naam, wordt alleen de laatste implementatie voltooid. Implementaties met dezelfde naam die nog niet zijn voltooid, worden vervangen door de laatste implementatie. Als u bijvoorbeeld een implementatie uitvoert met de naam `newStorage` die een opslag account implementeert `storage1` en tegelijkertijd een andere implementatie uitvoert met de naam `newStorage` die een opslag account implementeert `storage2` , implementeert u slechts één opslag account. De naam van het resulterende opslag account is `storage2` .

Als u echter een implementatie uitvoert met de naam `newStorage` die een opslag account implementeert `storage1` en direct nadat de implementatie is voltooid, voert u `newStorage` `storage2` twee opslag accounts uit met een naam die een opslag account implementeert. Een heeft `storage1` de naam en de andere heet `storage2` . Maar u hebt slechts één vermelding in de implementatie geschiedenis.

Wanneer u een unieke naam voor elke implementatie opgeeft, kunt u deze gelijktijdig zonder conflict uitvoeren. Als u een implementatie uitvoert met de naam `newStorage1` die een opslag account implementeert `storage1` en tegelijkertijd een andere implementatie uitvoert met de naam `newStorage2` die een opslag account implementeert `storage2` , hebt u twee opslag accounts en twee vermeldingen in de implementatie geschiedenis.

Geef elke implementatie een unieke naam om conflicten met gelijktijdige implementaties te voor komen en te zorgen voor unieke vermeldingen in de implementatie geschiedenis.

## <a name="deploy-remote-template"></a>Externe sjabloon implementeren

In plaats van ARM-sjablonen op uw lokale computer op te slaan, kunt u ze beter opslaan op een externe locatie. U kunt sjablonen opslaan in een broncode beheer bibliotheek (zoals GitHub). U kunt ze ook opslaan in een Azure-opslag account voor gedeelde toegang in uw organisatie.

Als u een externe sjabloon wilt implementeren, gebruikt u de para meter **TemplateUri** . Gebruik de URI in het voor beeld om de voorbeeld sjabloon te implementeren vanuit GitHub.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

In het voor gaande voor beeld is een openbaar toegankelijke URI vereist voor de sjabloon, die voor de meeste scenario's werkt, omdat uw sjabloon geen gevoelige gegevens mag bevatten. Als u gevoelige gegevens (zoals een beheerders wachtwoord) moet opgeven, geeft u die waarde als een beveiligde para meter door. Als u niet wilt dat uw sjabloon openbaar toegankelijk is, kunt u deze beveiligen door deze op te slaan in een persoonlijke opslag container. Zie voor meer informatie over het implementeren van een sjabloon waarvoor een SAS-token (Shared Access Signature) is vereist een [persoonlijke sjabloon implementeren met SAS-token](secure-template-with-sas-token.md). Als u een zelf studie wilt door lopen, raadpleegt u [zelf studie: Azure Key Vault integreren in arm-sjabloon implementatie](template-tutorial-use-key-vault.md).

## <a name="deploy-template-spec"></a>Sjabloonspecificatie implementeren

In plaats van een lokale of externe sjabloon te implementeren, kunt u een [sjabloon specificatie](template-specs.md)maken. De sjabloon specificatie is een resource in uw Azure-abonnement die een ARM-sjabloon bevat. Het is eenvoudig om de sjabloon veilig te delen met gebruikers in uw organisatie. U gebruikt op rollen gebaseerd toegangs beheer van Azure (Azure RBAC) om toegang te verlenen tot de sjabloon specificatie. Deze functie is momenteel beschikbaar als preview-versie.

In de volgende voor beelden ziet u hoe u een sjabloon specificatie maakt en implementeert. Deze opdrachten zijn alleen beschikbaar als u zich hebt [geregistreerd voor de preview-versie](https://aka.ms/templateSpecOnboarding).

Eerst maakt u de sjabloon specificatie door de ARM-sjabloon op te geven.

```azurepowershell
New-AzTemplateSpec -Name storageSpec -Version 1.0 -ResourceGroupName templateSpecsRg -Location westus2 -TemplateJsonFile ./mainTemplate.json
```

Vervolgens krijgt u de ID voor sjabloon specificatie en implementeert u deze.

```azurepowershell
$id = (Get-AzTemplateSpec -Name storageSpec -ResourceGroupName templateSpecsRg -Version 1.0).Version.Id

New-AzResourceGroupDeployment `
  -ResourceGroupName demoRG `
  -TemplateSpecId $id
```

Zie [Azure Resource Manager-sjabloon specificaties (preview-versie)](template-specs.md)voor meer informatie.

## <a name="preview-changes"></a>Preview-wijzigingen

Voordat u uw sjabloon implementeert, kunt u een voor beeld bekijken van de wijzigingen die door de sjabloon in uw omgeving worden aangebracht. Gebruik de [bewerking What-if](template-deploy-what-if.md) om te controleren of de sjabloon de wijzigingen aanbrengt die u verwacht. Wat-als ook de sjabloon voor fouten valideert.

## <a name="deploy-from-azure-cloud-shell"></a>Implementeren vanuit Azure Cloud Shell

U kunt de [Azure Cloud shell](https://shell.azure.com) gebruiken om uw sjabloon te implementeren. Als u een externe sjabloon wilt implementeren, geeft u de URI van de sjabloon op. Als u een lokale sjabloon wilt implementeren, moet u uw sjabloon eerst laden in het opslag account voor uw Cloud Shell. Als u bestanden naar de shell wilt uploaden, selecteert u het menu pictogram **bestanden uploaden/downloaden** in het shell-venster.

Als u de Cloud Shell wilt openen, bladert u naar [https://shell.azure.com](https://shell.azure.com) of selecteert u **try-it** in de volgende code sectie:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Als u de code in de shell wilt plakken, klikt u met de rechter muisknop in de shell en selecteert u **Plakken**.

## <a name="pass-parameter-values"></a>Parameter waarden door geven

Als u parameter waarden wilt door geven, kunt u inline-para meters of een parameter bestand gebruiken.

### <a name="inline-parameters"></a>Inline-para meters

Als u inline-para meters wilt door geven, geeft u de namen van de para meter op met de `New-AzResourceGroupDeployment` opdracht. Als u bijvoorbeeld een teken reeks en een matrix wilt door geven aan een sjabloon, gebruikt u:

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

Het ophalen van een parameter waarde uit een bestand is handig wanneer u configuratie waarden moet opgeven. U kunt bijvoorbeeld [Cloud-init-waarden opgeven voor een virtuele Linux-machine](../../virtual-machines/linux/using-cloud-init.md).

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

Zie voor meer informatie over het parameter bestand [Resource Manager-parameter bestand maken](parameter-files.md).

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

## <a name="next-steps"></a>Volgende stappen

- Als u wilt terugkeren naar een geslaagde implementatie wanneer u een fout krijgt, raadpleegt u [herstellen bij fout naar geslaagde implementatie](rollback-on-error.md).
- Zie [Azure Resource Manager implementatie modi](deployment-modes.md)om op te geven hoe u resources wilt afhandelen die in de resource groep aanwezig zijn, maar die niet zijn gedefinieerd in de sjabloon.
- Zie [inzicht in de structuur en syntaxis van arm-sjablonen](template-syntax.md)voor informatie over het definiëren van para meters in uw sjabloon.
- Zie voor meer informatie over het implementeren van een sjabloon waarvoor een SAS-token is vereist een [persoonlijke sjabloon implementeren met SAS-token](secure-template-with-sas-token.md).
