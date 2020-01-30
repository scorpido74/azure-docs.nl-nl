---
title: Zelf studie-uitvoer toevoegen aan sjabloon
description: Voeg uitvoer toe aan uw Azure Resource Manager sjabloon om de syntaxis te vereenvoudigen.
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 381f9f54a95b6d457aa65c7e8ef6abe49fe9eeea
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2020
ms.locfileid: "76765733"
---
# <a name="tutorial-add-outputs-to-your-resource-manager-template"></a>Zelf studie: uitvoer toevoegen aan uw Resource Manager-sjabloon

In deze zelf studie leert u hoe u een waarde uit uw sjabloon kunt ophalen. U gebruikt uitvoer wanneer u een waarde van een geïmplementeerde resource nodig hebt. Het volt ooien van deze zelf studie duurt **zeven minuten** .

## <a name="prerequisites"></a>Vereisten

U wordt aangeraden de [zelf studie over variabelen](template-tutorial-add-variables.md)te volt ooien, maar dit is niet vereist.

U moet Visual Studio code hebben met de uitbrei ding Resource Manager tools en een Azure PowerShell of Azure CLI. Zie voor meer informatie [sjabloon hulpprogramma's](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Sjabloon controleren

Aan het einde van de vorige zelf studie had uw sjabloon de volgende JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.json":::

Er wordt een opslag account geïmplementeerd, maar er worden geen gegevens over het opslag account geretourneerd. Mogelijk moet u de eigenschappen van een nieuwe resource vastleggen zodat ze later beschikbaar zijn voor referentie.

## <a name="add-outputs"></a>Uitvoer toevoegen

U kunt uitvoer gebruiken om waarden te retour neren van de sjabloon. Het kan bijvoorbeeld nuttig zijn om de eind punten voor uw nieuwe opslag account op te halen.

In het volgende voor beeld wordt de wijziging in de sjabloon gemarkeerd om een uitvoer waarde toe te voegen. Kopieer het hele bestand en vervang de sjabloon door de inhoud ervan.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.json" range="1-53" highlight="47-52":::

Er zijn enkele belang rijke items die u kunt noteren over de uitvoer waarde die u hebt toegevoegd.

Het type van de geretourneerde waarde wordt ingesteld op **object**, wat betekent dat er een JSON-object wordt geretourneerd.

De functie [Reference](template-functions-resource.md#reference) wordt gebruikt om de runtime status van het opslag account op te halen. Als u de runtime status van een resource wilt ophalen, geeft u de naam of ID van een resource door. In dit geval gebruikt u dezelfde variabele die u hebt gebruikt om de naam van het opslag account te maken.

Ten slotte wordt de eigenschap **primaire** van het opslag account geretourneerd

## <a name="deploy-template"></a>Sjabloon implementeren

U bent klaar om de sjabloon te implementeren en de geretourneerde waarde te bekijken.

Als u de resource groep nog niet hebt gemaakt, raadpleegt u [resource groep maken](template-tutorial-create-first-template.md#create-resource-group). In het voor beeld wordt ervan uitgegaan dat u de **templateFile** -variabele hebt ingesteld op het pad naar het sjabloon bestand, zoals wordt weer gegeven in de [eerste zelf studie](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addoutputs `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-clitabazure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addoutputs \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

In de uitvoer voor de implementatie opdracht ziet u een object dat lijkt op het volgende:

```json
{
    "dfs": "https://storeluktbfkpjjrkm.dfs.core.windows.net/",
    "web": "https://storeluktbfkpjjrkm.z19.web.core.windows.net/",
    "blob": "https://storeluktbfkpjjrkm.blob.core.windows.net/",
    "queue": "https://storeluktbfkpjjrkm.queue.core.windows.net/",
    "table": "https://storeluktbfkpjjrkm.table.core.windows.net/",
    "file": "https://storeluktbfkpjjrkm.file.core.windows.net/"
}
```

## <a name="review-your-work"></a>Uw werk controleren

U hebt in de laatste zes zelf studies een hoop gemaakt. Laten we even eens kijken wat u hebt gedaan. U hebt een sjabloon gemaakt met para meters die u gemakkelijk kunt opgeven. De sjabloon kan in verschillende omgevingen worden gebruikt, omdat deze kan worden aangepast en de benodigde waarden dynamisch kunnen worden gemaakt. Het retourneert ook informatie over het opslag account dat u in uw script kunt gebruiken.

Nu gaan we kijken naar de resource groep en de implementatie geschiedenis.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).
1. Selecteer **resource groepen**in het menu links.
1. Selecteer de resource groep die u hebt geïmplementeerd.
1. Afhankelijk van de stappen die u hebt uitgevoerd, moet u ten minste één en mogelijk meerdere opslag accounts in de resource groep hebben.
1. U moet ook een aantal geslaagde implementaties hebben die worden weer gegeven in de geschiedenis. Selecteer deze koppeling.

   ![Implementaties selecteren](./media/template-tutorial-add-outputs/select-deployments.png)

1. U ziet al uw implementaties in de geschiedenis. Selecteer de implementatie met de naam **addoutputs**.

   ![Implementatie geschiedenis weer geven](./media/template-tutorial-add-outputs/show-history.png)

1. U kunt de invoer controleren.

   ![Invoer weer geven](./media/template-tutorial-add-outputs/show-inputs.png)

1. U kunt de uitvoer controleren.

   ![Uitvoer weer geven](./media/template-tutorial-add-outputs/show-outputs.png)

1. U kunt de sjabloon controleren.

   ![Sjabloon weer geven](./media/template-tutorial-add-outputs/show-template.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u verdergaat met de volgende zelf studie, hoeft u de resource groep niet te verwijderen.

Als u nu stopt, wilt u misschien de resources opschonen die u hebt geïmplementeerd door de resource groep te verwijderen.

1. Selecteer **Resourcegroep** in het linkermenu van Azure Portal.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep.
4. Selecteer **Resourcegroep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een retour waarde aan de sjabloon toegevoegd. In de volgende zelf studie leert u hoe u een sjabloon kunt exporteren en gedeelten van de geëxporteerde sjabloon in uw sjabloon gebruikt.

> [!div class="nextstepaction"]
> [Geëxporteerde sjabloon gebruiken](template-tutorial-export-template.md)
