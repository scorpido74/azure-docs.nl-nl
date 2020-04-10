---
title: Sjabloonimplementatie wat-als (voorbeeld)
description: Bepaal welke wijzigingen er met uw resources gebeuren voordat u een Azure Resource Manager-sjabloon implementeert.
author: mumian
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: jgao
ms.openlocfilehash: b8e94d0b4f364e2873dfc21792a67f11c33483bf
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010185"
---
# <a name="arm-template-deployment-what-if-operation-preview"></a>ARM-sjabloonimplementatie what-if-bewerking (Voorbeeld)

Voordat u een ARM-sjabloon (Azure Resource Manager) implementeert, u een voorbeeld bekijken van de wijzigingen die zullen plaatsvinden. Azure Resource Manager biedt de wat-als-bewerking waarmee u zien hoe resources worden gewijzigd als u de sjabloon implementeert. De wat-als-bewerking brengt geen wijzigingen aan in bestaande resources. In plaats daarvan voorspelt het de wijzigingen als de opgegeven sjabloon wordt geïmplementeerd.

> [!NOTE]
> De wat-als-bewerking is momenteel in preview. Als preview-release kunnen de resultaten soms aantonen dat een resource verandert wanneer er daadwerkelijk geen verandering plaatsvindt. We werken eraan om deze problemen te verminderen, maar we hebben uw hulp nodig. Meld deze problemen [https://aka.ms/whatifissues](https://aka.ms/whatifissues)op .

U de wat-als-bewerking gebruiken met de PowerShell-opdrachten of REST API-bewerkingen.

## <a name="install-powershell-module"></a>PowerShell-module installeren

Als u wat-als wilt gebruiken in PowerShell, installeert u een voorbeeldversie van de az.resources-module in de PowerShell-galerie.

### <a name="install-preview-version"></a>Preview-versie installeren

Gebruik het als u de voorbeeldmodule wilt installeren:

```powershell
Install-Module Az.Resources -RequiredVersion 1.12.1-preview -AllowPrerelease
```

### <a name="uninstall-alpha-version"></a>Alfaversie verwijderen

Als u eerder een alfaversie van de wat-als-module hebt geïnstalleerd, verwijdert u de module. De alpha-versie was alleen beschikbaar voor gebruikers die zich hebben aangemeld voor een vroege preview. Als u dat voorbeeld niet hebt geïnstalleerd, u deze sectie overslaan.

1. Voer PowerShell uit als Administrator
1. Controleer uw geïnstalleerde versies van de az.resources-module.

   ```powershell
   Get-InstalledModule -Name Az.Resources -AllVersions | select Name,Version
   ```

1. Als u een geïnstalleerde versie hebt met een versienummer in de indeling **2.x.x-alpha,** verwijdert u die versie.

   ```powershell
   Uninstall-Module Az.Resources -RequiredVersion 2.0.1-alpha5 -AllowPrerelease
   ```

1. Het registreren van de wat-als-opslagplaats die u hebt gebruikt om de preview te installeren.

   ```powershell
   Unregister-PSRepository -Name WhatIfRepository
   ```

Je bent klaar om wat-als te gebruiken.

## <a name="see-results"></a>Bekijk de resultaten

In PowerShell bevat de uitvoer kleurgecodeerde resultaten waarmee u de verschillende soorten wijzigingen zien.

![Resource Manager-sjabloonimplementatie what-if-bewerking fullresourcepayload- en wijzigingstypen](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

De tekstuitvoer is:

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

U Azure PowerShell of Azure REST API gebruiken voor de wat-als-bewerking.

### <a name="azure-powershell"></a>Azure PowerShell

Als u een voorbeeld van de wijzigingen wilt `-Whatif` bekijken voordat u een sjabloon implementeert, voegt u de parameter switch toe aan de opdracht implementatie.

* `New-AzResourceGroupDeployment -Whatif`voor implementaties van resourcegroepen
* `New-AzSubscriptionDeployment -Whatif`en `New-AzDeployment -Whatif` voor implementaties op abonnementsniveau

U de `-Confirm` parameter Switch ook gebruiken om een voorbeeld van de wijzigingen te bekijken en wordt gevraagd om door te gaan met de implementatie.

* `New-AzResourceGroupDeployment -Confirm`voor implementaties van resourcegroepen
* `New-AzSubscriptionDeployment -Confirm`en `New-AzDeployment -Confirm` voor implementaties op abonnementsniveau

De voorgaande opdrachten retourneren een tekstoverzicht dat u handmatig inspecteren. Gebruik het als u een object wilt krijgen dat u programmatisch inspecteren op wijzigingen:

* `$results = Get-AzResourceGroupDeploymentWhatIfResult`voor implementaties van resourcegroepen
* `$results = Get-AzSubscriptionDeploymentWhatIfResult`of `$results = Get-AzDeploymentWhatIfResult` voor implementaties op abonnementsniveau

### <a name="azure-rest-api"></a>Azure REST API

Gebruik voor REST API:

* [Implementaties - Wat als](/rest/api/resources/deployments/whatif) voor implementaties van resourcegroepen
* [Implementaties - Wat als bij abonnementsbereik](/rest/api/resources/deployments/whatifatsubscriptionscope) voor implementaties op abonnementsniveau

## <a name="change-types"></a>Typen wijzigen

De wat-als-bewerking bevat zes verschillende soorten wijzigingen:

- **Maken:** de resource bestaat momenteel niet, maar wordt gedefinieerd in de sjabloon. De bron wordt gemaakt.

- **Verwijderen:** dit wijzigingstype is alleen van toepassing wanneer u de [volledige modus](deployment-modes.md) gebruikt voor implementatie. De resource bestaat, maar is niet gedefinieerd in de sjabloon. Met de volledige modus wordt de bron verwijderd. Alleen resources die [verwijdering in de volledige modus ondersteunen,](complete-mode-deletion.md) zijn opgenomen in dit wijzigingstype.

- **Negeren:** de bron bestaat, maar is niet gedefinieerd in de sjabloon. De resource wordt niet geïmplementeerd of gewijzigd.

- **NoChange:** de resource bestaat en wordt gedefinieerd in de sjabloon. De resource wordt opnieuw geïmplementeerd, maar de eigenschappen van de resource worden niet gewijzigd. Dit wijzigingstype wordt geretourneerd wanneer `FullResourcePayloads` [ResultFormat](#result-format) is ingesteld op , wat de standaardwaarde is.

- **Wijzigen:** de bron bestaat en wordt gedefinieerd in de sjabloon. De resource wordt opnieuw geïmplementeerd en de eigenschappen van de resource worden gewijzigd. Dit wijzigingstype wordt geretourneerd wanneer `FullResourcePayloads` [ResultFormat](#result-format) is ingesteld op , wat de standaardwaarde is.

- **Implementeren:** de resource bestaat en wordt gedefinieerd in de sjabloon. De resource wordt opnieuw geïmplementeerd. De eigenschappen van de resource kunnen al dan niet veranderen. De bewerking retourneert dit wijzigingstype wanneer deze niet voldoende informatie heeft om te bepalen of eigenschappen worden gewijzigd. U ziet deze voorwaarde alleen wanneer `ResourceIdOnly` [ResultFormat](#result-format) is ingesteld op .

## <a name="result-format"></a>Resultaatnotatie

U het detailniveau bepalen dat wordt geretourneerd over de voorspelde wijzigingen. Gebruik in de`New-Az*Deployment`implementatieopdrachten ( ) de parameter **-WhatIfResultFormat.** Gebruik in de programmatische`Get-Az*DeploymentWhatIf`objectopdrachten ( ) de parameter **ResultFormat.**

Stel de opmaakparameter in op **FullResourcePayloads** om een lijst met bronnen te krijgen die worden gewijzigd en details over de eigenschappen die zullen worden gewijzigd. Stel de opmaakparameter in op **ResourceIdOnly** om een lijst met bronnen te krijgen die worden gewijzigd. De standaardwaarde is **FullResourcePayloads.**  

De volgende resultaten tonen de twee verschillende uitvoernotaties:

- Volledige resource payloads

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

- Alleen resource-id

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

Om te zien hoe wat-als werkt, laten we een aantal tests uitvoeren. Implementeer eerst een [sjabloon waarmee een virtueel netwerk wordt gemaakt.](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-before.json) U gebruikt dit virtuele netwerk om te testen hoe wijzigingen worden gerapporteerd door wat-als.

```azurepowershell
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

### <a name="test-modification"></a>Testwijziging

Nadat de implementatie is voltooid, u de wat-als-bewerking testen. Implementeer deze keer een [sjabloon die het virtuele netwerk wijzigt.](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-after.json) Het mist een van de originele tags, een subnet is verwijderd, en het adres voorvoegsel is veranderd.

```azurepowershell
New-AzResourceGroupDeployment `
  -Whatif `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

De wat-als-uitvoer lijkt op:

![Wat-als-bewerkingsuitvoer van ResourceManager-sjabloon](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

De tekstuitvoer is:

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

Let bovenaan de uitvoer op dat kleuren zijn gedefinieerd om het type wijzigingen aan te geven.

Onder aan de uitvoer ziet u dat de tag-eigenaar is verwijderd. Het adresvoorvoegsel is gewijzigd van 10.0.0.0/16 naar 10.0.0.0/15. Het subnet met de naam subnet001 is verwijderd. Vergeet niet dat deze wijzigingen niet daadwerkelijk zijn geïmplementeerd. U ziet een voorbeeld van de wijzigingen die plaatsvinden als u de sjabloon implementeert.

Sommige eigenschappen die worden vermeld als verwijderd, worden niet echt gewijzigd. Eigenschappen kunnen onjuist worden gerapporteerd als verwijderd wanneer ze niet in de sjabloon staan, maar worden tijdens de implementatie automatisch ingesteld als standaardwaarden. Dit resultaat wordt beschouwd als "ruis" in de wat-als-respons. De uiteindelijke geïmplementeerde resource heeft de waarden die zijn ingesteld voor de eigenschappen. Naarmate de wat-als-bewerking afloopt, worden deze eigenschappen uit het resultaat gefilterd.

## <a name="programmatically-evaluate-what-if-results"></a>Programmatisch evalueren wat-als-resultaten

Laten we nu de wat-als-resultaten programmatisch evalueren door de opdracht in te stellen op een variabele.

```azurepowershell
$results = Get-AzResourceGroupDeploymentWhatIfResult `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

U een samenvatting van elke wijziging zien.

```azurepowershell
foreach ($change in $results.Changes)
{
  $change.Delta
}
```

## <a name="confirm-deletion"></a>Verwijdering bevestigen

De wat-als-bewerking ondersteunt het gebruik van [de implementatiemodus](deployment-modes.md). Wanneer de modus volledig is ingesteld, worden resources die niet in de sjabloon staan, verwijderd. In het volgende voorbeeld wordt een sjabloon geïmplementeerd die geen resources heeft die in de volledige modus [zijn gedefinieerd.](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json)

Als u een voorbeeld wilt bekijken `-Confirm` van wijzigingen voordat u een sjabloon implementeert, gebruikt u de parameter switch met de opdracht implementatie. Als de wijzigingen zijn zoals u had verwacht, bevestigt u dat u de implementatie wilt voltooien.

```azurepowershell
New-AzResourceGroupDeployment `
  -Confirm `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json" `
  -Mode Complete
```

Omdat er geen resources zijn gedefinieerd in de sjabloon en de implementatiemodus is ingesteld om te voltooien, wordt het virtuele netwerk verwijderd.

![De implementatie van de wat-als-uitvoerimplementatiemodus van ResourceManager is voltooid](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-mode-complete.png)

De tekstuitvoer is:

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

U ziet de verwachte wijzigingen en bevestigen dat u de implementatie wilt uitvoeren.

## <a name="next-steps"></a>Volgende stappen

- Als u onjuiste resultaten opmerkt van de preview-release [https://aka.ms/whatifissues](https://aka.ms/whatifissues)van wat-als, meldt u de problemen op .
- Zie [Resources implementeren met ARM-sjablonen en Azure PowerShell](deploy-powershell.md)voor het implementeren van sjablonen met Azure PowerShell.
- Zie [Resources met ARM-sjablonen en Resource Manager REST API](deploy-rest.md)implementeren als u sjablonen met REST wilt implementeren.
