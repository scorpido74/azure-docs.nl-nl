---
title: Resources implementeren met Azure CLI en sjabloon
description: Gebruik Azure Resource Manager en Azure CLI om resources te implementeren in Azure. De resources zijn gedefinieerd in een Resource Manager-sjabloon.
ms.topic: conceptual
ms.date: 10/09/2019
ms.openlocfilehash: c31a139e40953bf3b652af2172cd5c51578c45cc
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149740"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>Resources implementeren met Resource Manager-sjablonen en Azure CLI

In dit artikel wordt uitgelegd hoe u Azure CLI gebruikt met Resource Manager-sjablonen om uw resources te implementeren in Azure. Zie [overzicht van Azure Resource Manager](resource-group-overview.md)als u niet bekend bent met de concepten van het implementeren en beheren van uw Azure-oplossingen.

[!INCLUDE [sample-cli-install](../../includes/sample-cli-install.md)]

Als Azure CLI niet is geïnstalleerd, kunt u de [Cloud shell](#deploy-template-from-cloud-shell)gebruiken.

## <a name="deployment-scope"></a>Implementatie bereik

U kunt uw implementatie richten op een Azure-abonnement of een resource groep binnen een abonnement. In de meeste gevallen moet u de implementatie richten op een resource groep. Gebruik abonnements implementaties om beleids regels en roltoewijzingen toe te passen op het abonnement. U kunt ook abonnements implementaties gebruiken voor het maken van een resource groep en het implementeren van resources. Afhankelijk van het bereik van de implementatie, gebruikt u verschillende opdrachten.

Gebruik [AZ Group Deployment Create](/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)om te implementeren in een **resource groep**:

```azurecli
az group deployment create --resource-group <resource-group-name> --template-file <path-to-template>
```

Als u wilt implementeren in een **abonnement**, gebruikt u [AZ Deployment Create](/cli/azure/deployment?view=azure-cli-latest#az-deployment-create):

```azurecli
az deployment create --location <location> --template-file <path-to-template>
```

Zie [resource groepen en-resources op abonnements niveau maken](deploy-to-subscription.md)voor meer informatie over implementaties op abonnements niveau.

Op dit moment worden implementaties van beheer groepen alleen ondersteund via de REST API. Zie [resources maken op het niveau van de beheer groep](deploy-to-management-group.md)voor meer informatie over implementaties op het niveau van beheer groepen.

In de voor beelden in dit artikel worden de implementaties van resource groepen gebruikt.

## <a name="deploy-local-template"></a>Lokale sjabloon implementeren

Wanneer u resources implementeert in azure, doet u het volgende:

1. Aanmelden bij uw Azure-account
2. Maak een resource groep die fungeert als de container voor de geïmplementeerde resources. De naam van de resource groep mag alleen alfanumerieke tekens, punten, onderstrepings teken, afbreek streepjes en haakjes bevatten. Het kan Maxi maal 90 tekens lang zijn. Deze kan niet eindigen op een punt.
3. Implementeren in de resource groep de sjabloon waarmee de te maken resources worden gedefinieerd

Een sjabloon kan para meters bevatten waarmee u de implementatie kunt aanpassen. U kunt bijvoorbeeld waarden opgeven die zijn afgestemd op een bepaalde omgeving (zoals dev, test en productie). De voorbeeld sjabloon definieert een para meter voor de SKU van het opslag account.

In het volgende voor beeld wordt een resource groep gemaakt en een sjabloon van uw lokale computer geïmplementeerd:

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

De implementatie kan enkele minuten duren. Wanneer het is voltooid, ziet u een bericht met het volgende resultaat:

```azurecli
"provisioningState": "Succeeded",
```

## <a name="deploy-remote-template"></a>Externe sjabloon implementeren

In plaats van het opslaan van Resource Manager-sjablonen op de lokale computer, kunt u ze beter opslaan op een externe locatie. U kunt sjablonen opslaan in een broncode beheer bibliotheek (zoals GitHub). U kunt ze ook opslaan in een Azure-opslag account voor gedeelde toegang in uw organisatie.

Als u een externe sjabloon wilt implementeren, gebruikt u de **sjabloon-URI-** para meter. Gebruik de URI in het voor beeld om de voorbeeld sjabloon te implementeren vanuit GitHub.

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

In het voor gaande voor beeld is een openbaar toegankelijke URI vereist voor de sjabloon, die voor de meeste scenario's werkt, omdat uw sjabloon geen gevoelige gegevens mag bevatten. Als u gevoelige gegevens (zoals een beheerders wachtwoord) moet opgeven, geeft u die waarde als een beveiligde para meter door. Als u niet wilt dat uw sjabloon openbaar toegankelijk is, kunt u deze beveiligen door deze op te slaan in een persoonlijke opslag container. Zie voor meer informatie over het implementeren van een sjabloon waarvoor een SAS-token (Shared Access Signature) is vereist een [persoonlijke sjabloon implementeren met SAS-token](resource-manager-cli-sas-token.md).

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../includes/resource-manager-cloud-shell-deploy.md)]

Gebruik de volgende opdrachten in de Cloud Shell:

```azurecli-interactive
az group create --name examplegroup --location "South Central US"
az group deployment create --resource-group examplegroup \
  --template-uri <copied URL> \
  --parameters storageAccountType=Standard_GRS
```

## <a name="parameters"></a>Parameters

Als u parameter waarden wilt door geven, kunt u inline-para meters of een parameter bestand gebruiken.

### <a name="inline-parameters"></a>Inline-para meters

Als u inline-para meters wilt door geven, geeft u de waarden op in `parameters`. Als u bijvoorbeeld een teken reeks en een matrix wilt door geven aan een sjabloon, gebruikt u:

```azurecli
az group deployment create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString='inline string' exampleArray='("value1", "value2")'
```

Als u Azure CLI gebruikt met Windows-opdracht prompt (CMD) of Power shell, geeft u de matrix de volgende notatie: `exampleArray="['value1','value2']"`.

U kunt ook de inhoud van het bestand ophalen en deze inhoud als een inline-para meter opgeven.

```azurecli
az group deployment create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString=@stringContent.txt exampleArray=@arrayContent.json
```

Het ophalen van een parameter waarde uit een bestand is handig wanneer u configuratie waarden moet opgeven. U kunt bijvoorbeeld [Cloud-init-waarden opgeven voor een virtuele Linux-machine](../virtual-machines/linux/using-cloud-init.md).

De indeling arrayContent. json is:

```json
[
    "value1",
    "value2"
]
```

### <a name="parameter-files"></a>Parameter bestanden

In plaats van para meters als inline waarden door te geven in uw script, is het wellicht eenvoudiger een JSON-bestand te gebruiken dat de parameter waarden bevat. Het parameter bestand moet een lokaal bestand zijn. Externe parameter bestanden worden niet ondersteund met Azure CLI.

Zie voor meer informatie over het parameter bestand [Resource Manager-parameter bestand maken](resource-manager-parameter-files.md).

Als u een lokaal parameter bestand wilt door geven, gebruikt u `@` om een lokaal bestand met de naam Storage. para meters. json op te geven.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

## <a name="handle-extended-json-format"></a>Uitgebreide JSON-indeling verwerken

Als u een sjabloon wilt implementeren met teken reeksen of opmerkingen met meerdere regels, moet u de `--handle-extended-json-format` schakelaar gebruiken.  Bijvoorbeeld:

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[
    parameters('location')
    ]", //defaults to resource group location
  "apiVersion": "2018-10-01",
  /*
    storage account and network interface
    must be deployed first
  */
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

## <a name="test-a-template-deployment"></a>Een sjabloon implementatie testen

Als u uw sjabloon en parameter waarden wilt testen zonder daad werkelijk resources te implementeren, gebruikt u [AZ Group Deployment validate](/cli/azure/group/deployment#az-group-deployment-validate).

```azurecli-interactive
az group deployment validate \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

Als er geen fouten worden gedetecteerd, wordt met de opdracht informatie over de test implementatie geretourneerd. In het bijzonder ziet u dat de **fout** waarde Null is.

```azurecli
{
  "error": null,
  "properties": {
      ...
```

Als er een fout wordt gedetecteerd, retourneert de opdracht een fout bericht. Als er bijvoorbeeld een onjuiste waarde voor de SKU van het opslag account wordt door gegeven, wordt de volgende fout geretourneerd:

```azurecli
{
  "error": {
    "code": "InvalidTemplate",
    "details": null,
    "message": "Deployment template validation failed: 'The provided value 'badSKU' for the template parameter
      'storageAccountType' at line '13' and column '20' is not valid. The parameter value is not part of the allowed
      value(s): 'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.",
    "target": null
  },
  "properties": null
}
```

Als uw sjabloon een syntaxis fout bevat, retourneert de opdracht een fout melding die aangeeft dat de sjabloon niet kan worden geparseerd. Het bericht geeft het regel nummer en de positie van de Parseerfout aan.

```azurecli
{
  "error": {
    "code": "InvalidTemplate",
    "details": null,
    "message": "Deployment template parse failed: 'After parsing a value an unexpected character was encountered:
      \". Path 'variables', line 31, position 3.'.",
    "target": null
  },
  "properties": null
}
```

## <a name="next-steps"></a>Volgende stappen

- Als u wilt terugkeren naar een geslaagde implementatie wanneer u een fout krijgt, raadpleegt u [herstellen bij fout naar geslaagde implementatie](rollback-on-error.md).
- Zie [Azure Resource Manager implementatie modi](deployment-modes.md)om op te geven hoe u resources wilt afhandelen die in de resource groep aanwezig zijn, maar die niet zijn gedefinieerd in de sjabloon.
- Zie [inzicht in de structuur en syntaxis van Azure Resource Manager sjablonen](resource-group-authoring-templates.md)voor meer informatie over het definiëren van para meters in uw sjabloon.
- Zie [problemen met algemene Azure-implementatie fouten oplossen met Azure Resource Manager](resource-manager-common-deployment-errors.md)voor tips over het oplossen van veelvoorkomende implementatie fouten.
- Zie voor meer informatie over het implementeren van een sjabloon waarvoor een SAS-token is vereist een [persoonlijke sjabloon implementeren met SAS-token](resource-manager-cli-sas-token.md).
- Zie [Azure Deployment Manager](deployment-manager-overview.md)als u uw service veilig wilt implementeren in meer dan één regio.
