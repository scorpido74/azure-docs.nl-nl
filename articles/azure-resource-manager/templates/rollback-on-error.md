---
title: Fout terugdraaien naar succesvolle implementatie
description: Geef op dat een mislukte implementatie moet worden teruggedraaid naar een geslaagde implementatie.
ms.topic: conceptual
ms.date: 10/04/2019
ms.openlocfilehash: 206c794996f58a4c5b6982c551ae50128ed4f5eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460140"
---
# <a name="rollback-on-error-to-successful-deployment"></a>Rollback-on-fout naar succesvolle implementatie

Wanneer een implementatie mislukt, u een eerdere, succesvolle implementatie automatisch opnieuw implementeren vanuit uw implementatiegeschiedenis. Deze functionaliteit is handig als u een bekende goede status hebt voor uw implementatie van uw infrastructuur en wilt terugkeren naar deze status. Er zijn een aantal kanttekeningen en beperkingen:

- De herschikking wordt uitgevoerd precies zoals het eerder werd uitgevoerd met dezelfde parameters. Je de parameters niet wijzigen.
- De vorige implementatie wordt uitgevoerd met de [volledige modus](./deployment-modes.md#complete-mode). Alle resources die niet in de vorige implementatie zijn opgenomen, worden verwijderd en alle resourceconfiguraties zijn ingesteld op de vorige status. Zorg ervoor dat u de [implementatiemodi](./deployment-modes.md)volledig begrijpt.
- De herschikking heeft alleen invloed op de resources, eventuele gegevenswijzigingen worden niet beïnvloed.
- U deze functie alleen gebruiken met implementaties van resourcegroepen, niet met implementaties op abonnements- of beheergroepniveau. Zie [Resourcegroepen en resources maken op abonnementsniveau](./deploy-to-subscription.md)voor meer informatie over de implementatie op abonnementsniveau.
- U deze optie alleen gebruiken bij implementaties op basisniveau. Implementaties van een geneste sjabloon zijn niet beschikbaar voor herschikking.

Als u deze optie wilt gebruiken, moeten uw implementaties unieke namen hebben, zodat ze in de geschiedenis kunnen worden geïdentificeerd. Als u geen unieke namen hebt, kan de huidige mislukte implementatie de eerder succesvolle implementatie in de geschiedenis overschrijven.

## <a name="powershell"></a>PowerShell

Als u de laatste succesvolle `-RollbackToLastDeployment` implementatie opnieuw wilt implementeren, voegt u de parameter toe als een vlag.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollbackToLastDeployment
```

Als u een specifieke implementatie `-RollBackDeploymentName` opnieuw wilt implementeren, gebruikt u de parameter en geeft u de naam van de implementatie op. De opgegeven implementatie moet zijn geslaagd.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollBackDeploymentName ExampleDeployment01
```

## <a name="azure-cli"></a>Azure-CLI

Als u de laatste succesvolle `--rollback-on-error` implementatie opnieuw wilt implementeren, voegt u de parameter toe als een vlag.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error
```

Als u een specifieke implementatie `--rollback-on-error` opnieuw wilt implementeren, gebruikt u de parameter en geeft u de naam van de implementatie op. De opgegeven implementatie moet zijn geslaagd.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment02 \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error ExampleDeployment01
```

## <a name="rest-api"></a>REST API

Als u de laatste succesvolle implementatie opnieuw wilt implementeren als de huidige implementatie mislukt, gebruikt u:

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "LastSuccessful",
    }
  }
}
```

Als u een specifieke implementatie opnieuw wilt implementeren als de huidige implementatie mislukt, gebruikt u:

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "SpecificDeployment",
      "deploymentName": "<deploymentname>"
    }
  }
}
```

De opgegeven implementatie moet zijn geslaagd.

## <a name="next-steps"></a>Volgende stappen

- Zie [Azure Deployment Manager](deployment-manager-overview.md)als u uw service veilig wilt uitrollen naar meer dan één regio.
- Zie [Azure Resource Manager-implementatiemodi](deployment-modes.md)als u wilt opgeven hoe u resources in de resourcegroep verwerken, maar niet in de sjabloon bent gedefinieerd.
- Zie [De structuur en de syntaxis van Azure Resource Manager-sjablonen begrijpen](template-syntax.md)als u wilt begrijpen hoe u parameters in uw sjabloon definiëren.
- Zie [Privésjabloon implementeren met SAS-token voor](secure-template-with-sas-token.md)informatie over het implementeren van een sjabloon waarvoor een SAS-token vereist is.
