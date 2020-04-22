---
title: Resources implementeren met Azure CLI en sjabloon
description: Gebruik Azure Resource Manager en Azure CLI om resources te implementeren in Azure. De resources zijn gedefinieerd in een Resource Manager-sjabloon.
ms.topic: conceptual
ms.date: 04/20/2020
ms.openlocfilehash: 8ee15699a085178add05137be895fe6b660b715b
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685692"
---
# <a name="deploy-resources-with-arm-templates-and-azure-cli"></a>Resources implementeren met ARM-sjablonen en Azure CLI

In dit artikel wordt uitgelegd hoe u Azure CLI gebruikt met ARM-sjablonen (Azure Resource Manager) om uw resources te implementeren in Azure. Zie overzicht van de implementatie van sjablonen als u niet bekend bent met de concepten voor het implementeren en beheren van uw [Azure-oplossingen.](overview.md)

De implementatieopdrachten zijn gewijzigd in Azure CLI-versie 2.2.0. De voorbeelden in dit artikel vereisen Azure CLI-versie 2.2.0 of hoger.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

Als Azure CLI niet is geïnstalleerd, u de [Cloud Shell](#deploy-template-from-cloud-shell)gebruiken.

## <a name="deployment-scope"></a>Implementatiebereik

U uw implementatie targeten op een resourcegroep, abonnement, beheergroep of tenant. In de meeste gevallen targetu de implementatie op een resourcegroep. Als u beleids- en roltoewijzingen wilt toepassen in een groter bereik, gebruikt u abonnements-, beheergroep- of tenantimplementaties. Wanneer u een abonnement implementeert, u een resourcegroep maken en er resources op implementeren.

Afhankelijk van het bereik van de implementatie gebruikt u verschillende opdrachten.

Als u wilt implementeren in een **resourcegroep,** gebruikt u [az-implementatiegroep maken:](/cli/azure/deployment/group?view=azure-cli-latest#az-deployment-group-create)

```azurecli-interactive
az deployment group create --resource-group <resource-group-name> --template-file <path-to-template>
```

Als u wilt implementeren op een **abonnement,** gebruikt u [submaken van AZ-implementatie:](/cli/azure/deployment/sub?view=azure-cli-latest#az-deployment-sub-create)

```azurecli-interactive
az deployment sub create --location <location> --template-file <path-to-template>
```

Zie [Resourcegroepen en resources maken op abonnementsniveau](deploy-to-subscription.md)voor meer informatie over implementaties op abonnementsniveau.

Als u wilt implementeren in een **beheergroep,** maakt u gebruik van [az-implementatiemg create:](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-create)

```azurecli-interactive
az deployment mg create --location <location> --template-file <path-to-template>
```

Zie [Resources maken op het niveau van de beheergroep voor](deploy-to-management-group.md)meer informatie over implementaties op managementgroepenniveau.

Als u wilt implementeren in een **tenant,** maakt u gebruik van [az-implementatietenant:](/cli/azure/deployment/tenant?view=azure-cli-latest#az-deployment-tenant-create)

```azurecli-interactive
az deployment tenant create --location <location> --template-file <path-to-template>
```

Zie Resources maken op tenantniveau voor meer informatie over implementaties op [tenantniveau.](deploy-to-tenant.md)

De voorbeelden in dit artikel gebruiken implementaties van resourcegroepen.

## <a name="deploy-local-template"></a>Lokale sjabloon implementeren

Wanneer u resources implementeert in Azure, gaat u als:

1. Aanmelden bij uw Azure-account
2. Maak een resourcegroep die fungeert als container voor de geïmplementeerde resources. De naam van de resourcegroep kan alleen alfanumerieke tekens, perioden, underscores, koppeltekens en haakjes bevatten. Het kan maximaal 90 tekens zijn. Het kan niet eindigen in een periode.
3. Implementeren in de resourcegroep van de sjabloon die de resources definieert die moeten worden gemaakt

Een sjabloon kan parameters bevatten waarmee u de implementatie aanpassen. U bijvoorbeeld waarden opgeven die zijn afgestemd op een bepaalde omgeving (zoals dev, test en productie). De voorbeeldsjabloon definieert een parameter voor de SKU voor opslagaccount.

In het volgende voorbeeld wordt een resourcegroep gemaakt en wordt een sjabloon van uw lokale machine geïmplementeerd:

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

De implementatie kan enkele minuten duren. Wanneer het is voltooid, ziet u een bericht met het resultaat:

```output
"provisioningState": "Succeeded",
```

## <a name="deploy-remote-template"></a>Externe sjabloon implementeren

In plaats van ARM-sjablonen op uw lokale machine op te slaan, u deze liever opslaan op een externe locatie. U sjablonen opslaan in een bronbeheeropslagplaats (zoals GitHub). U ze ook opslaan in een Azure-opslagaccount voor gedeelde toegang in uw organisatie.

Als u een externe sjabloon wilt implementeren, gebruikt u de parameter **template-uri.** Gebruik de URI in het voorbeeld om de voorbeeldsjabloon van GitHub te implementeren.

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

Het voorgaande voorbeeld vereist een openbaar toegankelijke URI voor de sjabloon, die werkt voor de meeste scenario's omdat uw sjabloon geen gevoelige gegevens mag bevatten. Als u gevoelige gegevens moet opgeven (zoals een beheerderswachtwoord), geeft u die waarde door als een veilige parameter. Als u echter niet wilt dat uw sjabloon openbaar toegankelijk is, u deze beveiligen door deze op te slaan in een privéopslagcontainer. Zie [Privésjabloon implementeren met SAS-token voor](secure-template-with-sas-token.md)informatie over het implementeren van een sjabloon waarvoor een SAS-token (Shared Access Signature) vereist is.

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../../includes/resource-manager-cloud-shell-deploy.md)]

Gebruik in de Cloud Shell de volgende opdrachten:

```azurecli-interactive
az group create --name examplegroup --location "South Central US"
az deployment group create --resource-group examplegroup \
  --template-uri <copied URL> \
  --parameters storageAccountType=Standard_GRS
```

## <a name="parameters"></a>Parameters

Als u parameterwaarden wilt doorgeven, u inlineparameters of een parameterbestand gebruiken.

### <a name="inline-parameters"></a>Inlineparameters

Als u inlineparameters wilt `parameters`doorgeven, geeft u de waarden op in . Als u bijvoorbeeld een tekenreeks en array aan een sjabloon wilt doorgeven, is een Bash-shell:

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString='inline string' exampleArray='("value1", "value2")'
```

Als u Azure CLI met Windows Command Prompt (CMD) of PowerShell `exampleArray="['value1','value2']"`gebruikt, geeft u de array in de indeling door: .

U ook de inhoud van het bestand krijgen en die inhoud als inlineparameter opgeven.

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString=@stringContent.txt exampleArray=@arrayContent.json
```

Het ophalen van een parameterwaarde uit een bestand is handig wanneer u configuratiewaarden moet opgeven. U bijvoorbeeld [cloud-init-waarden opgeven voor een virtuele Linux-machine.](../../virtual-machines/linux/using-cloud-init.md)

De arrayContent.json-indeling is:

```json
[
    "value1",
    "value2"
]
```

### <a name="parameter-files"></a>Parameterbestanden

In plaats van parameters door te geven als inlinewaarden in uw script, u het gemakkelijker vinden om een JSON-bestand te gebruiken dat de parameterwaarden bevat. Het parameterbestand moet een lokaal bestand zijn. Externe parameterbestanden worden niet ondersteund met Azure CLI.

Zie [Resourcebeheer-parameterbestand maken](parameter-files.md)voor meer informatie over het parameterbestand .

Als u een lokaal `@` parameterbestand wilt doorgeven, gebruikt u een lokaal bestand met de naam storage.parameters.json op te geven.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

## <a name="handle-extended-json-format"></a>Uitgebreide JSON-indeling verwerken

Als u een sjabloon met tekenreeksen of opmerkingen met meerdere regels wilt implementeren met `--handle-extended-json-format` Azure CLI met versie 2.3.0 of ouder, moet u de switch gebruiken.  Bijvoorbeeld:

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2018-10-01",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[
    parameters('location')
    ]", //defaults to resource group location
  /*
    storage account and network interface
    must be deployed first
  */
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

## <a name="test-a-template-deployment"></a>Een sjabloonimplementatie testen

Als u uw sjabloon- en parameterwaarden wilt testen zonder daadwerkelijk resources te implementeren, gebruikt u [az-implementatiegroep valideren](/cli/azure/group/deployment).

```azurecli-interactive
az deployment group validate \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

Als er geen fouten worden gedetecteerd, retourneert de opdracht informatie over de testimplementatie. Let er met name op dat de **foutwaarde** null is.

```output
{
  "error": null,
  "properties": {
      ...
```

Als er een fout wordt gedetecteerd, retourneert de opdracht een foutbericht. Als u bijvoorbeeld een onjuiste waarde doorgeeft voor de SKU van het opslagaccount, wordt de volgende fout geretourneerd:

```output
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

Als er een syntaxisfout in uw sjabloon is, wordt met de opdracht een fout geretourneerd die aangeeft dat de sjabloon niet kan worden ontwenoft. Het bericht geeft het regelnummer en de positie van de parsingfout aan.

```output
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

- Als u wilt terugdraaien naar een geslaagde implementatie wanneer er een fout optreedt, raadpleegt u [Rollback-on error naar succesvolle implementatie](rollback-on-error.md).
- Zie [Azure Resource Manager-implementatiemodi](deployment-modes.md)als u wilt opgeven hoe u resources in de resourcegroep verwerken, maar niet in de sjabloon bent gedefinieerd.
- Zie [De structuur en syntaxis van ARM-sjablonen](template-syntax.md)begrijpen als u wilt begrijpen hoe u parameters in uw sjabloon definiëren.
- Zie [Veelvoorkomende Azure-implementatiefouten oplossen met Azure Resource Manager](common-deployment-errors.md)voor tips over het oplossen van veelvoorkomende implementatiefouten in Azure.
- Zie [Privésjabloon implementeren met SAS-token voor](secure-template-with-sas-token.md)informatie over het implementeren van een sjabloon waarvoor een SAS-token vereist is.
- Zie [Azure Deployment Manager](deployment-manager-overview.md)als u uw service veilig wilt uitrollen naar meer dan één regio.
