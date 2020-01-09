---
title: Fout bij het terugdraaien naar een geslaagde implementatie
description: Geef op dat een mislukte implementatie moet worden teruggedraaid naar een geslaagde implementatie.
ms.topic: conceptual
ms.date: 10/04/2019
ms.openlocfilehash: 32ba5485e1980eb819bf5429fbfbb597dfe75c2a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75484296"
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

Als u de laatste geslaagde implementatie opnieuw wilt implementeren, voegt u de para meter `-RollbackToLastDeployment` als een vlag toe.

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

Als u de laatste geslaagde implementatie opnieuw wilt implementeren, voegt u de para meter `--rollback-on-error` als een vlag toe.

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

## <a name="rest-api"></a>REST API

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
- Zie [inzicht in de structuur en syntaxis van Azure Resource Manager sjablonen](template-syntax.md)voor meer informatie over het definiëren van para meters in uw sjabloon.
- Zie voor meer informatie over het implementeren van een sjabloon waarvoor een SAS-token is vereist een [persoonlijke sjabloon implementeren met SAS-token](secure-template-with-sas-token.md).
