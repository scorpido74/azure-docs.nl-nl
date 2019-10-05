---
title: Terugkeren naar de fout bij een geslaagde Azure-implementatie
description: Geef op dat een mislukte implementatie moet worden teruggedraaid naar een geslaagde implementatie.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: tomfitz
ms.openlocfilehash: 035b684bb9f5fbc10eb13e642c3fd5945b85c561
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2019
ms.locfileid: "71975444"
---
# <a name="rollback-on-error-to-successful-deployment"></a>Ongedaan maken bij fout bij geslaagde implementatie

Wanneer een implementatie mislukt, kunt u automatisch een eerdere, geslaagde implementatie uit de implementatie geschiedenis opnieuw implementeren. Deze functie is handig als u een bekende goede status hebt voor de implementatie van uw infra structuur en u wilt terugkeren naar deze status. Er zijn een aantal voor behoud en beperkingen:

- De implementatie wordt precies zo uitgevoerd als deze eerder met dezelfde para meters is uitgevoerd. U kunt de para meters niet wijzigen.
- De vorige implementatie wordt uitgevoerd met de [volledige modus](./deployment-modes.md#complete-mode). Alle resources die geen deel uitmaken van de vorige implementatie worden verwijderd en alle resource configuraties worden ingesteld op de vorige status. Zorg ervoor dat u de [implementatie modi](./deployment-modes.md)volledig begrijpt.
- De herimplementatie heeft alleen invloed op de resources, maar wijzigingen in de gegevens worden niet beïnvloed.
- U kunt deze functie alleen gebruiken met implementaties van resource groepen, niet op abonnementen of op het niveau van beheer groepen. Zie [resource groepen en-resources op abonnements niveau maken](./deploy-to-subscription.md)voor meer informatie over de implementatie op abonnements niveau.
- U kunt deze optie alleen gebruiken bij implementaties op hoofd niveau. Implementaties van een geneste sjabloon zijn niet beschikbaar voor opnieuw implementeren.

Als u deze optie wilt gebruiken, moeten uw implementaties unieke namen hebben zodat deze in de geschiedenis kunnen worden geïdentificeerd. Als u geen unieke namen hebt, kan de huidige mislukte implementatie de eerder geslaagde implementatie in de geschiedenis overschrijven.

## <a name="powershell"></a>PowerShell

Als u de laatste geslaagde implementatie opnieuw wilt implementeren, voegt u de para meter `-RollbackToLastDeployment` toe als een vlag.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollbackToLastDeployment
```

Als u een specifieke implementatie opnieuw wilt implementeren, gebruikt u de para meter `-RollBackDeploymentName` en geeft u de naam van de implementatie op. De opgegeven implementatie moet zijn geslaagd.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollBackDeploymentName ExampleDeployment01
```

## <a name="azure-cli"></a>Azure-CLI

Als u de laatste geslaagde implementatie opnieuw wilt implementeren, voegt u de para meter `--rollback-on-error` toe als een vlag.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error
```

Als u een specifieke implementatie opnieuw wilt implementeren, gebruikt u de para meter `--rollback-on-error` en geeft u de naam van de implementatie op. De opgegeven implementatie moet zijn geslaagd.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment02 \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error ExampleDeployment01
```

## <a name="rest-api"></a>REST-API

Gebruik de volgende opdracht om de laatste geslaagde implementatie opnieuw te implementeren als de huidige implementatie mislukt:

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

- Zie [Azure Deployment Manager](deployment-manager-overview.md)als u uw service veilig wilt implementeren in meer dan één regio.
- Zie [Azure Resource Manager implementatie modi](deployment-modes.md)om op te geven hoe u resources wilt afhandelen die in de resource groep aanwezig zijn, maar die niet zijn gedefinieerd in de sjabloon.
- Zie [inzicht in de structuur en syntaxis van Azure Resource Manager sjablonen](resource-group-authoring-templates.md)voor meer informatie over het definiëren van para meters in uw sjabloon.
- Zie voor meer informatie over het implementeren van een sjabloon waarvoor een SAS-token is vereist een [persoonlijke sjabloon implementeren met SAS-token](resource-manager-powershell-sas-token.md).
