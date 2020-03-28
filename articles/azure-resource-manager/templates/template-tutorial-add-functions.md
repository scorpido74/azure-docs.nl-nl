---
title: Zelfstudie - sjabloonfuncties toevoegen
description: Voeg sjabloonfuncties toe aan uw Azure Resource Manager-sjabloon om waarden te construeren.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 6bb95d5a8022c6ac94dd8b7c777c65ff5e20617a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369921"
---
# <a name="tutorial-add-template-functions-to-your-arm-template"></a>Zelfstudie: sjabloonfuncties toevoegen aan uw ARM-sjabloon

In deze zelfstudie leert u hoe u [sjabloonfuncties](template-functions.md) toevoegt aan uw Azure Resource Manager-sjabloon (ARM). U gebruikt functies om waarden dynamisch te construeren. Naast deze door het systeem geleverde sjabloonfuncties u ook [door de gebruiker gedefinieerde functies](./template-user-defined-functions.md)maken. Deze tutorial duurt **7 minuten** om te voltooien.

## <a name="prerequisites"></a>Vereisten

We raden u aan de [zelfstudie over parameters in](template-tutorial-add-parameters.md)te vullen, maar dit is niet vereist.

U moet beschikken over Visual Studio Code met de extensie Hulpmiddelen voor ResourceBeheer en Azure PowerShell of Azure CLI. Zie [sjabloongereedschappen voor](template-tutorial-create-first-template.md#get-tools)meer informatie .

## <a name="review-template"></a>Sjabloon bekijken

Aan het einde van de vorige zelfstudie had uw sjabloon de volgende JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.json":::

De locatie van de opslagaccount is hardgecodeerd naar **Oost-VS.** Het is echter mogelijk dat u het opslagaccount moet implementeren in andere regio's. U wordt opnieuw geconfronteerd met een probleem van uw sjabloon ontbreekt flexibiliteit. U een parameter voor locatie toevoegen, maar het zou geweldig zijn als de standaardwaarde logischer is dan alleen een hard-coded waarde.

## <a name="use-function"></a>Functie gebruiken

Als u de vorige zelfstudie in deze serie hebt voltooid, hebt u al een functie gebruikt. Wanneer u **'[parameters('storageName')]]** hebt toegevoegd, hebt u de functie [parameters](template-functions-deployment.md#parameters) gebruikt. De haakjes geven aan dat de syntaxis in de haakjes een [sjabloonexpressie](template-expressions.md)is. Resourcebeheer lost de syntaxis op in plaats van deze als een letterlijke waarde te behandelen.

Functies voegen flexibiliteit toe aan uw sjabloon door waarden dynamisch op te halen tijdens de implementatie. In deze zelfstudie gebruikt u een functie om de locatie te krijgen van de resourcegroep die u gebruikt voor implementatie.

In het volgende voorbeeld worden de wijzigingen markeert om een parameter toe te voegen die **locatie**wordt genoemd.  De standaardwaarde parameter roept de functie [resourceGroep aan.](template-functions-resource.md#resourcegroup) Met deze functie wordt een object geretourneerd met informatie over de resourcegroep die wordt gebruikt voor implementatie. Een van de eigenschappen op het object is een locatieeigenschap. Wanneer u de standaardwaarde gebruikt, heeft de locatie van het opslagaccount dezelfde locatie als de brongroep. De resources in een resourcegroep hoeven niet dezelfde locatie te delen. U ook een andere locatie bieden wanneer dat nodig is.

Kopieer het hele bestand en vervang de sjabloon door de inhoud ervan.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.json" range="1-44" highlight="24-27,34":::

## <a name="deploy-template"></a>Sjabloon implementeren

In de vorige zelfstudies hebt u een opslagaccount in Oost-VS gemaakt, maar uw brongroep is gemaakt in Centraal-VS. Voor deze zelfstudie wordt uw opslagaccount gemaakt in dezelfde regio als de resourcegroep. Gebruik de standaardwaarde voor locatie, zodat u die parameterwaarde niet hoeft op te geven. U moet een nieuwe naam opgeven voor het opslagaccount omdat u een opslagaccount maakt op een andere locatie. Gebruik bijvoorbeeld **store2** als voorvoegsel in plaats van **store1**.

Zie [Resourcegroep maken](template-tutorial-create-first-template.md#create-resource-group)als u de resourcegroep niet hebt gemaakt. In het voorbeeld wordt ervan uitgegaan dat u de **variabele templateFile** hebt ingesteld op het pad naar het sjabloonbestand, zoals wordt weergegeven in de [eerste zelfstudie.](template-tutorial-create-first-template.md#deploy-template)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addlocationparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{new-unique-name}"
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u deze implementatieopdracht wilt uitvoeren, moet u over de [nieuwste versie](/cli/azure/install-azure-cli) van Azure CLI beschikken.

```azurecli
az deployment group create \
  --name addlocationparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={new-unique-name}
```

---

## <a name="verify-deployment"></a>Implementatie verifiëren

U de implementatie verifiëren door de brongroep te verkennen vanuit de Azure-portal.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer **Resourcegroepen**in het linkermenu .
1. Selecteer de resourcegroep die u hebt geïmplementeerd.
1. U ziet dat een opslagaccountbron is geïmplementeerd en dezelfde locatie heeft als de resourcegroep.

## <a name="clean-up-resources"></a>Resources opschonen

Als u doorgaat naar de volgende zelfstudie, hoeft u de brongroep niet te verwijderen.

Als u nu stopt, u de resources die u hebt geïmplementeerd, opschonen door de resourcegroep te verwijderen.

1. Selecteer **resourcegroep** in de linkermenu in de Azure-portal.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep.
4. Selecteer **Brongroep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een functie gebruikt bij het definiëren van de standaardwaarde voor een parameter. In deze zelfstudiereeks blijft u functies gebruiken. Aan het einde van de reeks voegt u functies toe aan elk gedeelte van de sjabloon.

> [!div class="nextstepaction"]
> [Variabelen toevoegen](template-tutorial-add-variables.md)
