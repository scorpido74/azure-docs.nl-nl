---
title: Sjabloonimlementatie wat-als (preview)
description: Bepaal welke wijzigingen er in uw resources optreden voordat u een Azure Resource Manager sjabloon implementeert.
author: mumian
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: jgao
ms.openlocfilehash: 117215e7c41ad7f354c9e76f764e9af1f50b74c1
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149239"
---
# <a name="resource-manager-template-deployment-what-if-operation-preview"></a>Implementatie van een resource manager-sjabloon wat-als-bewerking (preview)

Voordat u een sjabloon implementeert, wilt u mogelijk een voor beeld bekijken van de wijzigingen die zich voordoen. Azure Resource Manager biedt de What-if-bewerking om te laten zien hoe resources worden gewijzigd als u de sjabloon implementeert. Met de bewerking What-if worden geen wijzigingen aangebracht in bestaande resources. In plaats daarvan worden de wijzigingen voor speld als de opgegeven sjabloon wordt geïmplementeerd.

> [!NOTE]
> De What-if-bewerking is momenteel beschikbaar als preview-versie. Als u deze wilt gebruiken, moet u [zich aanmelden voor de preview-versie](https://aka.ms/armtemplatepreviews). In het geval van een preview-versie kunnen de resultaten soms aangeven dat een resource wordt gewijzigd wanneer er niets wordt gewijzigd. We werken eraan om deze problemen te reduceren, maar we hebben uw hulp nodig. Meld deze problemen aan [https://aka.ms/armwhatifissues](https://aka.ms/armwhatifissues).

U kunt de What-if-bewerking gebruiken met de `New-AzDeploymentWhatIf` Power shell-opdracht of de [implementaties-What if rest-](/rest/api/resources/deployments/whatif) bewerking.

In Power shell ziet de uitvoer er als volgt uit:

![Implementatie van Resource Manager-sjabloon wat-als-bewerking fullresourcepayload en type wijziging](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

## <a name="change-types"></a>Wijzigings typen

Met de bewerking What-if wordt zes verschillende soorten wijzigingen vermeld:

- **Maken**: de resource bestaat momenteel niet, maar is in de sjabloon gedefinieerd. De resource wordt gemaakt.

- **Verwijderen**: dit wijzigings type is alleen van toepassing wanneer u de [modus volledig](deployment-modes.md) gebruikt voor de implementatie. De resource bestaat wel, maar is niet gedefinieerd in de sjabloon. In de modus volledig wordt de resource verwijderd. Alleen resources die de verwijdering van de [volledige modus ondersteunen](complete-mode-deletion.md) , zijn opgenomen in dit wijzigings type.

- **Negeren**: de resource bestaat wel, maar is niet gedefinieerd in de sjabloon. De resource wordt niet geïmplementeerd of gewijzigd.

- **Ongewijzigd**: de resource bestaat en is gedefinieerd in de sjabloon. De resource wordt opnieuw geïmplementeerd, maar de eigenschappen van de resource worden niet gewijzigd. Dit type wijziging wordt geretourneerd wanneer [ResultFormat](#result-format) is ingesteld op `FullResourcePayloads`. Dit is de standaard waarde.

- **Wijzigen**: de resource bestaat en is gedefinieerd in de sjabloon. De resource wordt opnieuw geïmplementeerd en de eigenschappen van de resource worden gewijzigd. Dit type wijziging wordt geretourneerd wanneer [ResultFormat](#result-format) is ingesteld op `FullResourcePayloads`. Dit is de standaard waarde.

- **Implementeren**: de resource bestaat en is gedefinieerd in de sjabloon. De resource wordt opnieuw geïmplementeerd. De eigenschappen van de resource kunnen of mogen niet worden gewijzigd. De bewerking retourneert dit wijzigings type wanneer het niet genoeg informatie heeft om te bepalen of eigenschappen worden gewijzigd. U ziet deze voor waarde alleen wanneer [ResultFormat](#result-format) is ingesteld op `ResourceIdOnly`.

## <a name="deployment-scope"></a>Implementatie bereik

U kunt de What-if-bewerking gebruiken voor implementaties op het niveau van het abonnement of de resource groep. U stelt het implementatie bereik in met de para meter `-ScopeType`. De geaccepteerde waarden zijn `Subscription` en `ResourceGroup`. In dit artikel worden de implementaties van resource groepen gedemonstreerd.

Zie [resource groepen en-resources op abonnements niveau maken](deploy-to-subscription.md#)voor meer informatie over implementaties op abonnements niveau.

## <a name="result-format"></a>Resultaat indeling

U kunt het detail niveau bepalen dat wordt geretourneerd over de voorspelde wijzigingen. Stel de para meter `ResultFormat` in op `FullResourcePayloads` om een lijst met resources te verkrijgen wat er wordt gewijzigd en Details over de eigenschappen die worden gewijzigd. Stel de para meter `ResultFormat` in op `ResourceIdOnly` om een lijst met resources te verkrijgen die zullen worden gewijzigd. De standaardwaarde is `FullResourcePayloads`.  

In de volgende scherm afbeeldingen ziet u de twee verschillende uitvoer indelingen:

- Volledige nettoladingen van resources

    ![Implementatie van Resource Manager-sjabloon wat-als-bewerking fullresourcepayloads-uitvoer](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-fullresourcepayload.png)

- Alleen Resource-ID

    ![Implementatie van Resource Manager-sjabloon wat-als-bewerking resourceidonly-uitvoer](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-resourceidonly.png)

## <a name="run-what-if-operation"></a>Wat-als-bewerking uitvoeren

### <a name="set-up-environment"></a>Omgeving instellen

Laten we een aantal tests uitvoeren om te zien hoe-als werkt. Implementeer eerst een sjabloon van [Azure Quick Start-sjablonen waarmee een opslag account wordt gemaakt](https://github.com/Azure/azure-quickstart-templates/blob/master/101-storage-account-create/azuredeploy.json). Het standaard type opslag account is `Standard_LRS`. U gebruikt dit opslag account om te testen hoe wijzigingen worden gerapporteerd met wat-als.

```azurepowershell-interactive
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

### <a name="test-modification"></a>Wijziging testen

Nadat de implementatie is voltooid, bent u klaar om de bewerking wat als' te testen. Voer de What-if-opdracht uit, maar wijzig het type opslag account in `Standard_GRS`.

```azurepowershell-interactive
New-AzDeploymentWhatIf `
  -ScopeType ResourceGroup `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" `
  -storageAccountType Standard_GRS
```

De What-if-uitvoer is vergelijkbaar met:

![Implementatie van Resource Manager-sjabloon wat-als uitvoer bewerking](./media/template-deploy-what-if/resource-manager-deployment-whatif-output.png)

Boven aan de uitvoer wordt aangegeven dat kleuren worden gedefinieerd om het type wijzigingen aan te geven.

Onder aan de uitvoer wordt de SKU-naam (type opslag account) gewijzigd van **Standard_LRS** in **Standard_GRS**.

### <a name="test-deletion"></a>Test verwijderen

De bewerking What-if ondersteunt het gebruik van de [implementatie modus](deployment-modes.md). Als deze modus is ingesteld op volt ooien, worden de resources die niet in de sjabloon staan, verwijderd. In het volgende voor beeld wordt een [sjabloon geïmplementeerd waarvoor geen resources zijn gedefinieerd](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json) in de volledige modus.

```azurepowershell-interactive
New-AzDeploymentWhatIf `
  -ScopeType ResourceGroup `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json" `
  -Mode Complete
```

Omdat er geen resources zijn gedefinieerd in de sjabloon en de implementatie modus is ingesteld op voltooid, wordt het opslag account verwijderd.

![Implementatie van de implementatie modus voor het uitvoeren van een resource manager-sjabloon](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-mode-complete.png)

Het is belang rijk om te onthouden wat-als er geen werkelijke wijzigingen worden aangebracht. Het opslag account bevindt zich nog in de resource groep.

## <a name="next-steps"></a>Volgende stappen

- Als u onjuiste resultaten van de preview-versie van What-if ziet, kunt u de problemen melden op [https://aka.ms/armwhatifissues](https://aka.ms/armwhatifissues).
- Zie [resources implementeren met Resource Manager-sjablonen en Azure PowerShell](resource-group-template-deploy.md)voor meer informatie over het implementeren van sjablonen met Azure PowerShell.
- Zie [resources implementeren met Resource Manager-sjablonen en resource manager rest API](resource-group-template-deploy-rest.md)voor meer informatie over het implementeren van sjablonen met rest.
- Als u wilt terugkeren naar een geslaagde implementatie wanneer u een fout krijgt, raadpleegt u [herstellen bij fout naar geslaagde implementatie](rollback-on-error.md).
