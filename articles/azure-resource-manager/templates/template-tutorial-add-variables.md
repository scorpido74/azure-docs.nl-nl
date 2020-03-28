---
title: Zelfstudie - variabele toevoegen aan sjabloon
description: Voeg variabelen toe aan uw Azure Resource Manager-sjabloon om de syntaxis te vereenvoudigen.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 7fa6c36b604d0e96b2192ceb6c5585afcade080b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371717"
---
# <a name="tutorial-add-variables-to-your-arm-template"></a>Zelfstudie: variabelen toevoegen aan uw ARM-sjabloon

In deze zelfstudie leert u hoe u een variabele toevoegt aan uw Azure Resource Manager-sjabloon (ARM). Variabelen vereenvoudigen uw sjablonen doordat u één keer een expressie schrijven en opnieuw gebruiken in de sjabloon. Deze tutorial duurt **7 minuten** om te voltooien.

## <a name="prerequisites"></a>Vereisten

We raden u aan de [zelfstudie over functies in](template-tutorial-add-functions.md)te vullen, maar dit is niet vereist.

U moet beschikken over Visual Studio Code met de extensie Hulpmiddelen voor ResourceBeheer en Azure PowerShell of Azure CLI. Zie [sjabloongereedschappen voor](template-tutorial-create-first-template.md#get-tools)meer informatie .

## <a name="review-template"></a>Sjabloon bekijken

Aan het einde van de vorige zelfstudie had uw sjabloon de volgende JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.json":::

De parameter voor de naam van het opslagaccount is moeilijk te gebruiken omdat u een unieke naam moet opgeven. Als je de eerdere tutorials in deze serie hebt voltooid, ben je waarschijnlijk moe van het raden van een unieke naam. U lost dit probleem op door een variabele toe te voegen die een unieke naam voor het opslagaccount construeert.

## <a name="use-variable"></a>Variabele gebruiken

In het volgende voorbeeld worden de wijzigingen belicht om een variabele aan uw sjabloon toe te voegen die een unieke naam van het opslagaccount maakt. Kopieer het hele bestand en vervang de sjabloon door de inhoud ervan.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.json" range="1-47" highlight="5-9,29-31,36":::

Merk op dat het een variabele met de naam **uniqueStorageName**bevat. Deze variabele gebruikt vier functies om een tekenreekswaarde te construeren.

Je bent al bekend met de [parameters](template-functions-deployment.md#parameters) functie, dus we zullen het niet onderzoeken.

U bent ook bekend met de functie [resourceGroup.](template-functions-resource.md#resourcegroup) In dit geval krijgt u de eigenschap **id in** plaats van de **locatieeigenschap,** zoals in de vorige zelfstudie wordt weergegeven. De eigenschap **id** retourneert de volledige id van de resourcegroep, inclusief de naam van de abonnements-id en de naam van de resourcegroep.

Met de functie [uniqueString](template-functions-string.md#uniquestring) wordt een hashwaarde van 13 tekens gewaaratwaard. De geretourneerde waarde wordt bepaald door de parameters die u doorgeeft. Voor deze zelfstudie gebruikt u de brongroep-id als invoer voor de hashwaarde. Dat betekent dat u deze sjabloon implementeren in verschillende resourcegroepen en een andere unieke tekenreekswaarde krijgen. U krijgt echter dezelfde waarde als u implementeert in dezelfde resourcegroep.

De [concatfunctie](template-functions-string.md#concat) neemt waarden en combineert ze. Voor deze variabele neemt deze de tekenreeks van de parameter en de tekenreeks van de functie uniqueString en combineert deze in één tekenreeks.

Met de parameter **StoragePrefix** u een voorvoegsel doorgeven waarmee u opslagaccounts identificeren. U uw eigen naamgevingsconventie maken die het gemakkelijker maakt om opslagaccounts na implementatie te identificeren vanuit een lange lijst met bronnen.

Tot slot wordt opgemerkt dat de opslagnaam nu is ingesteld op de variabele in plaats van op een parameter.

## <a name="deploy-template"></a>Sjabloon implementeren

Laten we de sjabloon implementeren. Het implementeren van deze sjabloon is eenvoudiger dan de vorige sjablonen omdat u alleen het voorvoegsel voor de opslagnaam opgeeft.

Zie [Resourcegroep maken](template-tutorial-create-first-template.md#create-resource-group)als u de resourcegroep niet hebt gemaakt. In het voorbeeld wordt ervan uitgegaan dat u de **variabele templateFile** hebt ingesteld op het pad naar het sjabloonbestand, zoals wordt weergegeven in de [eerste zelfstudie.](template-tutorial-create-first-template.md#deploy-template)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnamevariable `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u deze implementatieopdracht wilt uitvoeren, moet u over de [nieuwste versie](/cli/azure/install-azure-cli) van Azure CLI beschikken.

```azurecli
az deployment group create \
  --name addnamevariable \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

## <a name="verify-deployment"></a>Implementatie verifiëren

U de implementatie verifiëren door de brongroep te verkennen vanuit de Azure-portal.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer **Resourcegroepen**in het linkermenu .
1. Selecteer de resourcegroep die u hebt geïmplementeerd.
1. U ziet dat een opslagaccountbron is geïmplementeerd. De naam van het opslagaccount is **opslag** plus een reeks willekeurige tekens.

## <a name="clean-up-resources"></a>Resources opschonen

Als u doorgaat naar de volgende zelfstudie, hoeft u de brongroep niet te verwijderen.

Als u nu stopt, u de resources die u hebt geïmplementeerd, opschonen door de resourcegroep te verwijderen.

1. Selecteer **resourcegroep** in de linkermenu in de Azure-portal.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep.
4. Selecteer **Brongroep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een variabele toegevoegd die een unieke naam voor een opslagaccount maakt. In de volgende zelfstudie retourneert u een waarde van het geïmplementeerde opslagaccount.

> [!div class="nextstepaction"]
> [Uitvoer toevoegen](template-tutorial-add-outputs.md)
