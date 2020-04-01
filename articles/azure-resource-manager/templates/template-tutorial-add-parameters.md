---
title: Zelfstudie - parameters toevoegen aan sjabloon
description: Voeg parameters toe aan uw Azure Resource Manager-sjabloon om deze herbruikbaar te maken.
author: mumian
ms.date: 03/31/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: de7ec961672db2f3120e00f1a42b33f71e7ab092
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437819"
---
# <a name="tutorial-add-parameters-to-your-arm-template"></a>Zelfstudie: parameters toevoegen aan uw ARM-sjabloon

In de [vorige zelfstudie](template-tutorial-add-resource.md)hebt u geleerd hoe u een opslagaccount aan de sjabloon toevoegen en implementeren. In deze zelfstudie leert u hoe u de armsjabloon (Azure Resource Manager) verbeteren door parameters toe te voegen. Deze tutorial duurt ongeveer **14 minuten** om te voltooien.

## <a name="prerequisites"></a>Vereisten

We raden u aan de [zelfstudie over resources in](template-tutorial-add-resource.md)te vullen, maar dit is niet vereist.

U moet beschikken over Visual Studio Code met de extensie Hulpmiddelen voor ResourceBeheer en Azure PowerShell of Azure CLI. Zie [sjabloongereedschappen voor](template-tutorial-create-first-template.md#get-tools)meer informatie .

## <a name="review-template"></a>Sjabloon bekijken

Aan het einde van de vorige zelfstudie had uw sjabloon de volgende JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.json":::

Je hebt misschien gemerkt dat er een probleem is met deze sjabloon. De naam van het opslagaccount is hardgecodeerd. U deze sjabloon alleen gebruiken om elke keer hetzelfde opslagaccount te implementeren. Als u een opslagaccount met een andere naam wilt implementeren, moet u een nieuwe sjabloon maken, wat uiteraard geen praktische manier is om uw implementaties te automatiseren.

## <a name="make-template-reusable"></a>Sjabloon herbruikbaar maken

Als u uw sjabloon herbruikbaar wilt maken, voegen we een parameter toe die u gebruiken om een naam van een opslagaccount door te geven. De gemarkeerde JSON in het volgende voorbeeld geeft aan wat er in uw sjabloon is gewijzigd. De parameter **storageName** wordt geïdentificeerd als een tekenreeks. De maximale lengte is ingesteld op 24 tekens om te voorkomen dat namen die te lang zijn.

Kopieer het hele bestand en vervang de sjabloon door de inhoud ervan.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-name/azuredeploy.json" range="1-26" highlight="4-10,15":::

## <a name="deploy-template"></a>Sjabloon implementeren

Laten we de sjabloon implementeren. In het volgende voorbeeld wordt de sjabloon geïmplementeerd met Azure CLI of PowerShell. U ziet dat u de naam van het opslagaccount opgeeft als een van de waarden in de opdracht implementatie. Geef voor de naam van het opslagaccount dezelfde naam op die u in de vorige zelfstudie hebt gebruikt.

Zie [Resourcegroep maken](template-tutorial-create-first-template.md#create-resource-group)als u de resourcegroep niet hebt gemaakt. In het voorbeeld wordt ervan uitgegaan dat u de **variabele templateFile** hebt ingesteld op het pad naar het sjabloonbestand, zoals wordt weergegeven in de [eerste zelfstudie.](template-tutorial-create-first-template.md#deploy-template)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnameparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u deze implementatieopdracht wilt uitvoeren, moet u over de [nieuwste versie](/cli/azure/install-azure-cli) van Azure CLI beschikken.

```azurecli
az deployment group create \
  --name addnameparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={your-unique-name}
```

---

## <a name="understand-resource-updates"></a>Informatie over resource-updates

In de vorige sectie hebt u een opslagaccount met dezelfde naam geïmplementeerd als u eerder had gemaakt. U vraagt zich misschien af hoe de resource wordt beïnvloed door de herschikking.

Als de resource al bestaat en er geen wijziging wordt gedetecteerd in de eigenschappen, wordt er geen actie ondernomen. Als de resource al bestaat en een eigenschap is gewijzigd, wordt de resource bijgewerkt. Als de bron niet bestaat, wordt deze gemaakt.

Deze manier van omgaan met updates betekent dat uw sjabloon alle resources kan bevatten die u nodig hebt voor een Azure-oplossing. U de sjabloon veilig opnieuw implementeren en weten dat resources alleen worden gewijzigd of gemaakt wanneer dat nodig is. Als u bijvoorbeeld bestanden aan uw opslagaccount hebt toegevoegd, u het opslagaccount opnieuw implementeren zonder deze bestanden te verliezen.

## <a name="customize-by-environment"></a>Aanpassen op omgeving

Met parameters kunt u de implementatie aanpassen door waarden op te geven die voor een specifieke omgeving zijn aangepast. U bijvoorbeeld verschillende waarden doorgeven op basis van de vraag of u implementeert in een omgeving voor ontwikkeling, testen en productie.

De vorige sjabloon heeft altijd een Standard_LRS-opslagaccount geïmplementeerd. U wilt misschien de flexibiliteit om verschillende SKU's te implementeren, afhankelijk van de omgeving. In het volgende voorbeeld worden de wijzigingen weergegeven om een parameter voor SKU toe te voegen. Kopieer het hele bestand en plak over uw sjabloon.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.json" range="1-40" highlight="10-23,32":::

De parameter **storageSKU** heeft een standaardwaarde. Deze waarde wordt gebruikt wanneer een waarde niet is opgegeven tijdens de implementatie. Het heeft ook een lijst van toegestane waarden. Deze waarden komen overeen met de waarden die nodig zijn om een opslagaccount te maken. U wilt niet dat gebruikers van uw sjabloon in SKU's worden doorgegeven die niet werken.

## <a name="redeploy-template"></a>Sjabloon opnieuw implementeren

Je bent klaar om weer in te zetten. Omdat de standaard SKU is ingesteld op **Standard_LRS,** hoeft u geen waarde voor die parameter op te geven.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name addskuparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={your-unique-name}
```

---

> [!NOTE]
> Als de implementatie is mislukt, gebruikt u de **foutopsporingsschakelaar** met de opdracht implementatie om de foutopsporingslogboeken weer te geven.  U ook de **verbose-schakelaar** gebruiken om de volledige foutopsporingslogboeken weer te geven.

Als u de flexibiliteit van uw sjabloon wilt zien, zetten we opnieuw in. Stel deze tijd de parameter SKU in op **Standard_GRS**. U een nieuwe naam opgeven om een ander opslagaccount aan te maken of dezelfde naam gebruiken om uw bestaande opslagaccount bij te werken. Beide opties werken.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name usenondefaultsku `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}" `
  -storageSKU Standard_GRS
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name usenondefaultsku \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageSKU=Standard_GRS storageName={your-unique-name}
```

---

Tot slot, laten we nog een test uitvoeren en zien wat er gebeurt als je slaagt in een SKU die niet een van de toegestane waarden is. In dit geval testen we het scenario waarin een gebruiker van uw sjabloon denkt dat **basic** een van de SKU's is.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name testskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}" `
  -storageSKU basic
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name testskuparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageSKU=basic storageName={your-unique-name}
```

---

De opdracht mislukt onmiddellijk met een foutbericht waarin staat welke waarden zijn toegestaan. Resourcemanager identificeert de fout voordat de implementatie wordt gestart.

## <a name="clean-up-resources"></a>Resources opschonen

Als u doorgaat naar de volgende zelfstudie, hoeft u de brongroep niet te verwijderen.

Als u nu stopt, u de resources die u hebt geïmplementeerd, opschonen door de resourcegroep te verwijderen.

1. Selecteer **resourcegroep** in de linkermenu in de Azure-portal.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep.
4. Selecteer **Brongroep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

U hebt de sjabloon die in de [eerste zelfstudie is](template-tutorial-create-first-template.md) gemaakt, verbeterd door parameters toe te voegen. In de volgende zelfstudie leert u over sjabloonfuncties.

> [!div class="nextstepaction"]
> [Sjabloonfuncties toevoegen](template-tutorial-add-functions.md)