---
title: Sjabloonimlementatie wat-als (preview)
description: Bepaal welke wijzigingen er in uw resources optreden voordat u een Azure Resource Manager sjabloon implementeert.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/04/2020
ms.author: tomfitz
ms.openlocfilehash: 62f46d158bea9507246fda7f24750c3743a5e1f1
ms.sourcegitcommit: c052c99fd0ddd1171a08077388d221482026cd58
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/04/2020
ms.locfileid: "84424241"
---
# <a name="arm-template-deployment-what-if-operation-preview"></a>Wat als'-bewerking van ARM-sjabloon implementatie (preview-versie)

Voordat u een Azure Resource Manager-sjabloon (ARM) implementeert, kunt u een voor beeld bekijken van de wijzigingen die zich voordoen. Azure Resource Manager biedt de What-if-bewerking om te laten zien hoe resources worden gewijzigd als u de sjabloon implementeert. Met de bewerking What-if worden geen wijzigingen aangebracht in bestaande resources. In plaats daarvan worden de wijzigingen voor speld als de opgegeven sjabloon wordt geïmplementeerd.

> [!NOTE]
> De What-if-bewerking is momenteel beschikbaar als preview-versie. In het geval van een preview-versie kunnen de resultaten soms aangeven dat een resource wordt gewijzigd wanneer er niets wordt gewijzigd. We werken eraan om deze problemen te reduceren, maar we hebben uw hulp nodig. Meld deze problemen op [https://aka.ms/whatifissues](https://aka.ms/whatifissues) .

U kunt de What-if-bewerking met Azure PowerShell, Azure CLI of REST API-bewerkingen gebruiken. Wat-als wordt ondersteund voor implementaties van resource groep en abonnements niveau.

## <a name="install-azure-powershell-module"></a>Azure PowerShell module installeren

Als u wilt gebruiken wat-als in Power shell, moet u versie **4,2 of hoger van de module AZ**hebben.

Voordat u de vereiste module installeert, moet u ervoor zorgen dat u Power shell core (6. x of 7. x) hebt. Als u Power shell 5. x of eerder hebt, moet u [uw versie van Power shell bijwerken](/powershell/scripting/install/installing-powershell). U kunt de vereiste module niet installeren op Power shell 5. x of lager.

### <a name="install-latest-version"></a>Nieuwste versie installeren

Als u de module wilt installeren, gebruikt u:

```powershell
Install-Module -Name Az -Force
```

Zie [Install Azure PowerShell](/powershell/azure/install-az-ps)(Engelstalig) voor meer informatie over het installeren van modules.

### <a name="uninstall-alpha-version"></a>Alfa versie verwijderen

Als u eerder een alpha-versie van de What-if-module hebt geïnstalleerd, verwijdert u die module. De versie van alpha is alleen beschikbaar voor gebruikers die zich hebben geregistreerd voor een vroege preview. Als u deze preview niet hebt geïnstalleerd, kunt u deze sectie overs Laan.

1. Voer PowerShell uit als Administrator
1. Controleer de geïnstalleerde versies van de module AZ. resources.

   ```powershell
   Get-InstalledModule -Name Az.Resources -AllVersions | select Name,Version
   ```

1. Als u een geïnstalleerde versie hebt met een versie nummer in de indeling **2. x. x-alfa**, verwijdert u die versie.

   ```powershell
   Uninstall-Module Az.Resources -RequiredVersion 2.0.1-alpha5 -AllowPrerelease
   ```

1. Hef de registratie van de What-if-opslag plaats op die u hebt gebruikt om de preview-versie te installeren.

   ```powershell
   Unregister-PSRepository -Name WhatIfRepository
   ```

U kunt nu wat als gebruiken.

## <a name="install-azure-cli-module"></a>Azure CLI-module installeren

Als u wilt gebruiken wat-als in azure CLI, moet u over Azure CLI 2.5.0 of hoger beschikken. Installeer, indien nodig, [de nieuwste versie van Azure cli](/cli/azure/install-azure-cli).

## <a name="see-results"></a>Resultaten weer geven

Wanneer u wat-als gebruikt in Power shell of Azure CLI, bevat de uitvoer kleur codes die u helpen de verschillende soorten wijzigingen te zien.

![Implementatie van Resource Manager-sjabloon wat-als-bewerking fullresourcepayload en type wijziging](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

De tekst uitvoer is:

```powershell
Resource and property changes are indicated with these symbols:
  - Delete
  + Create
  ~ Modify

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
    - tags.Owner: "Team A"
    ~ properties.addressSpace.addressPrefixes: [
      - 0: "10.0.0.0/16"
      + 0: "10.0.0.0/15"
      ]
    ~ properties.subnets: [
      - 0:

          name:                     "subnet001"
          properties.addressPrefix: "10.0.0.0/24"

      ]

Resource changes: 1 to modify.
```

## <a name="what-if-commands"></a>Wat-als-opdrachten

### <a name="azure-powershell"></a>Azure PowerShell

Als u de wijzigingen wilt bekijken voordat u een sjabloon implementeert, gebruikt u [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) of [New-AzSubscriptionDeployment](/powershell/module/az.resources/new-azdeployment). Voeg de `-Whatif` para meter switch toe aan de implementatie opdracht.

* `New-AzResourceGroupDeployment -Whatif`voor implementaties van resource groepen
* `New-AzSubscriptionDeployment -Whatif`en `New-AzDeployment -Whatif` voor implementaties op abonnements niveau

U kunt de `-Confirm` para meter switch gebruiken om de wijzigingen te bekijken en u wordt gevraagd om door te gaan met de implementatie.

* `New-AzResourceGroupDeployment -Confirm`voor implementaties van resource groepen
* `New-AzSubscriptionDeployment -Confirm`en `New-AzDeployment -Confirm` voor implementaties op abonnements niveau

De voor gaande opdrachten retour neren een tekst samenvatting die u hand matig kunt controleren. Gebruik [Get-AzResourceGroupDeploymentWhatIfResult](/powershell/module/az.resources/get-azresourcegroupdeploymentwhatifresult) of [Get-AzSubscriptionDeploymentWhatIfResult](/powershell/module/az.resources/get-azdeploymentwhatifresult)om een object op te halen dat u programmatisch kunt controleren op wijzigingen.

* `$results = Get-AzResourceGroupDeploymentWhatIfResult`voor implementaties van resource groepen
* `$results = Get-AzSubscriptionDeploymentWhatIfResult`of `$results = Get-AzDeploymentWhatIfResult` voor implementaties op abonnements niveau

### <a name="azure-cli"></a>Azure CLI

Als u de wijzigingen wilt bekijken voordat u een sjabloon implementeert, gebruikt u [AZ Deployment Group What-if](/cli/azure/deployment/group#az-deployment-group-what-if) of [AZ Deployment sub What-if](/cli/azure/deployment/sub#az-deployment-sub-what-if).

* `az deployment group what-if`voor implementaties van resource groepen
* `az deployment sub what-if`voor implementaties op abonnements niveau

U kunt de `--confirm-with-what-if` Switch (of het bijbehorende korte formulier `-c` ) gebruiken om de wijzigingen te bekijken en u wordt gevraagd om door te gaan met de implementatie. Voeg deze switch toe aan [AZ-implementatie groep Create](/cli/azure/deployment/group#az-deployment-group-create) of [AZ Deployment sub Create](/cli/azure/deployment/sub#az-deployment-sub-create).

* `az deployment group create --confirm-with-what-if`of `-c` voor implementaties van resource groepen
* `az deployment sub create --confirm-with-what-if`of `-c` voor implementaties op abonnements niveau

De voor gaande opdrachten retour neren een tekst samenvatting die u hand matig kunt controleren. Als u een JSON-object wilt ophalen dat u programmatisch kunt controleren op wijzigingen, gebruikt u:

* `az deployment group what-if --no-pretty-print`voor implementaties van resource groepen
* `az deployment sub what-if --no-pretty-print`voor implementaties op abonnements niveau

### <a name="azure-rest-api"></a>Azure REST API

Voor REST API gebruikt u:

* [Implementaties-What if](/rest/api/resources/deployments/whatif) voor implementaties van resource groepen
* [Implementaties-What if op abonnements bereik](/rest/api/resources/deployments/whatifatsubscriptionscope) voor implementaties op abonnements niveau

## <a name="change-types"></a>Wijzigings typen

Met de bewerking What-if wordt zes verschillende soorten wijzigingen vermeld:

- **Maken**: de resource bestaat momenteel niet, maar is in de sjabloon gedefinieerd. De resource wordt gemaakt.

- **Verwijderen**: dit wijzigings type is alleen van toepassing wanneer u de [modus volledig](deployment-modes.md) gebruikt voor de implementatie. De resource bestaat wel, maar is niet gedefinieerd in de sjabloon. In de modus volledig wordt de resource verwijderd. Alleen resources die de verwijdering van de [volledige modus ondersteunen](complete-mode-deletion.md) , zijn opgenomen in dit wijzigings type.

- **Negeren**: de resource bestaat wel, maar is niet gedefinieerd in de sjabloon. De resource wordt niet geïmplementeerd of gewijzigd.

- **Ongewijzigd**: de resource bestaat en is gedefinieerd in de sjabloon. De resource wordt opnieuw geïmplementeerd, maar de eigenschappen van de resource worden niet gewijzigd. Dit type wijziging wordt geretourneerd wanneer [ResultFormat](#result-format) is ingesteld op `FullResourcePayloads` . Dit is de standaard waarde.

- **Wijzigen**: de resource bestaat en is gedefinieerd in de sjabloon. De resource wordt opnieuw geïmplementeerd en de eigenschappen van de resource worden gewijzigd. Dit type wijziging wordt geretourneerd wanneer [ResultFormat](#result-format) is ingesteld op `FullResourcePayloads` . Dit is de standaard waarde.

- **Implementeren**: de resource bestaat en is gedefinieerd in de sjabloon. De resource wordt opnieuw geïmplementeerd. De eigenschappen van de resource kunnen of mogen niet worden gewijzigd. De bewerking retourneert dit wijzigings type wanneer het niet genoeg informatie heeft om te bepalen of eigenschappen worden gewijzigd. U ziet deze voor waarde alleen wanneer [ResultFormat](#result-format) is ingesteld op `ResourceIdOnly` .

## <a name="result-format"></a>Resultaat indeling

U bepaalt het detail niveau dat wordt geretourneerd over de voorspelde wijzigingen. U hebt hiervoor twee opties:

* **FullResourcePayloads** : retourneert een lijst met resources die worden gewijzigd en Details over de eigenschappen die worden gewijzigd
* **ResourceIdOnly** : retourneert een lijst met resources die worden gewijzigd

De standaard waarde is **FullResourcePayloads**.

Voor Power shell-implementatie opdrachten gebruikt u de `-WhatIfResultFormat` para meter. Gebruik de para meter in de programmatische-object opdrachten `ResultFormat` .

Voor Azure CLI gebruikt u de `--result-format` para meter.
 
In de volgende resultaten ziet u de twee verschillende uitvoer indelingen:

- Volledige nettoladingen van resources

  ```powershell
  Resource and property changes are indicated with these symbols:
    - Delete
    + Create
    ~ Modify

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
      - tags.Owner: "Team A"
      ~ properties.addressSpace.addressPrefixes: [
        - 0: "10.0.0.0/16"
        + 0: "10.0.0.0/15"
        ]
      ~ properties.subnets: [
        - 0:

          name:                     "subnet001"
          properties.addressPrefix: "10.0.0.0/24"

        ]

  Resource changes: 1 to modify.
  ```

- Alleen Resource-ID

  ```powershell
  Resource and property changes are indicated with this symbol:
    ! Deploy

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ! Microsoft.Network/virtualNetworks/vnet-001

  Resource changes: 1 to deploy.
  ```

## <a name="run-what-if-operation"></a>Wat-als-bewerking uitvoeren

### <a name="set-up-environment"></a>Omgeving instellen

Laten we een aantal tests uitvoeren om te zien hoe-als werkt. Implementeer eerst een [sjabloon waarmee een virtueel netwerk wordt gemaakt](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-before.json). U gebruikt dit virtuele netwerk om te testen hoe wijzigingen worden gerapporteerd met wat-als.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
az group create \
  --name ExampleGroup \
  --location "Central US"
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

---

### <a name="test-modification"></a>Wijziging testen

Nadat de implementatie is voltooid, bent u klaar om de bewerking wat als' te testen. Deze keer dat u een [sjabloon implementeert waarmee het virtuele netwerk wordt gewijzigd](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-after.json). Er ontbreken een of meer originele Tags, een subnet is verwijderd en het adres voorvoegsel is gewijzigd.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Whatif `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
az deployment group what-if \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

---

De What-if-uitvoer ziet er ongeveer als volgt uit:

![Implementatie van Resource Manager-sjabloon wat-als uitvoer bewerking](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

De tekst uitvoer is:

```powershell
Resource and property changes are indicated with these symbols:
  - Delete
  + Create
  ~ Modify

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
    - tags.Owner: "Team A"
    ~ properties.addressSpace.addressPrefixes: [
      - 0: "10.0.0.0/16"
      + 0: "10.0.0.0/15"
      ]
    ~ properties.subnets: [
      - 0:

        name:                     "subnet001"
        properties.addressPrefix: "10.0.0.0/24"

      ]

Resource changes: 1 to modify.
```

Boven aan de uitvoer wordt aangegeven dat kleuren worden gedefinieerd om het type wijzigingen aan te geven.

Onder aan de uitvoer ziet u dat de tag-eigenaar is verwijderd. Het adres voorvoegsel is gewijzigd van 10.0.0.0/16 naar 10.0.0.0/15. Het subnet met de naam subnet001 is verwijderd. Houd er rekening mee dat deze wijzigingen niet daad werkelijk zijn geïmplementeerd. U ziet een voor beeld van de wijzigingen die zich voordoen als u de sjabloon implementeert.

Sommige van de eigenschappen die worden weer gegeven als verwijderd, worden niet daad werkelijk gewijzigd. Eigenschappen kunnen niet worden gerapporteerd als verwijderd als ze niet in de sjabloon staan, maar worden tijdens de implementatie automatisch ingesteld als standaard waarden. Dit resultaat wordt als ' ruis ' beschouwd in het wat-als-antwoord. Voor de uiteindelijke geïmplementeerde resource zijn de waarden ingesteld voor de eigenschappen. Als de ' wat als'-bewerking is verouderd, worden deze eigenschappen uit het resultaat gefilterd.

## <a name="programmatically-evaluate-what-if-results"></a>Wat-als-resultaten programmatisch evalueren

Nu kunnen we de What-if-resultaten programmatisch evalueren door de opdracht in te stellen op een variabele.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$results = Get-AzResourceGroupDeploymentWhatIfResult `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

U kunt een samen vatting van elke wijziging bekijken.

```azurepowershell
foreach ($change in $results.Changes)
{
  $change.Delta
}
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
results=$(az deployment group what-if --resource-group ExampleGroup --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json" --no-pretty-print)
```

---

## <a name="confirm-deletion"></a>Verwijdering bevestigen

De bewerking What-if ondersteunt het gebruik van de [implementatie modus](deployment-modes.md). Als deze modus is ingesteld op volt ooien, worden de resources die niet in de sjabloon staan, verwijderd. In het volgende voor beeld wordt een [sjabloon geïmplementeerd waarvoor geen resources zijn gedefinieerd](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json) in de volledige modus.

Als u de wijzigingen wilt bekijken voordat u een sjabloon implementeert, gebruikt u de para meter switch bevestigen met de implementatie opdracht. Als de wijzigingen naar verwachting zijn, moet u bevestigen dat u de implementatie wilt volt ooien.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -Mode Complete `
  -Confirm `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json"
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --resource-group ExampleGroup \
  --mode Complete \
  --confirm-with-what-if \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json"
```

---

Omdat er geen resources zijn gedefinieerd in de sjabloon en de implementatie modus is ingesteld op voltooid, wordt het virtuele netwerk verwijderd.

![Implementatie van de implementatie modus voor het uitvoeren van een resource manager-sjabloon](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-mode-complete.png)

De tekst uitvoer is:

```powershell
Resource and property changes are indicated with this symbol:
  - Delete

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  - Microsoft.Network/virtualNetworks/vnet-001

      id:
"/subscriptions/./resourceGroups/ExampleGroup/providers/Microsoft.Network/virtualNet
works/vnet-001"
      location:        "centralus"
      name:            "vnet-001"
      tags.CostCenter: "12345"
      tags.Owner:      "Team A"
      type:            "Microsoft.Network/virtualNetworks"

Resource changes: 1 to delete.

Are you sure you want to execute the deployment?
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "Y"):
```

U ziet de verwachte wijzigingen en u kunt bevestigen dat u de implementatie wilt uitvoeren.

## <a name="sdks"></a>SDK's

U kunt de What-if-bewerking gebruiken via de Azure Sdk's.

* Gebruik [wat als'](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.operations.deploymentsoperations?view=azure-python#what-if-resource-group-name--deployment-name--properties--location-none--custom-headers-none--raw-false--polling-true----operation-config-)voor python.

* Gebruik de [klasse DeploymentWhatIf](/java/api/com.microsoft.azure.management.resources.deploymentwhatif?view=azure-java-stable)voor Java.

* Voor .NET gebruikt u de [DeploymentWhatIf-klasse](/dotnet/api/microsoft.azure.management.resourcemanager.models.deploymentwhatif?view=azure-dotnet).

## <a name="next-steps"></a>Volgende stappen

- Als u onjuiste resultaten van de preview-versie van What-if ziet, kunt u de problemen melden op [https://aka.ms/whatifissues](https://aka.ms/whatifissues) .
- Zie [resources implementeren met arm-sjablonen en Azure PowerShell](deploy-powershell.md)voor meer informatie over het implementeren van sjablonen met Azure PowerShell.
- Zie [resources implementeren met arm-sjablonen en Azure cli](deploy-cli.md)voor meer informatie over het implementeren van sjablonen met Azure cli.
- Zie [resources implementeren met arm-sjablonen en Resource Manager rest API](deploy-rest.md)als u sjablonen wilt implementeren met rest.
