---
title: Zelf studie-uitvoer toevoegen aan sjabloon
description: Voeg uitvoer toe aan uw Azure Resource Manager sjabloon om de syntaxis te vereenvoudigen.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 2ee1a2c7037bde68b7858b57a03c78bd2016ff1c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80743549"
---
# <a name="tutorial-add-outputs-to-your-arm-template"></a>Zelf studie: uitvoer toevoegen aan uw ARM-sjabloon

In deze zelf studie leert u hoe u een waarde uit uw Azure Resource Manager-sjabloon (ARM) kunt retour neren. U gebruikt uitvoer wanneer u een waarde van een geïmplementeerde resource nodig hebt. Het volt ooien van deze zelf studie duurt **zeven minuten** .

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

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addoutputs `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u deze implementatie opdracht wilt uitvoeren, moet u de [nieuwste versie](/cli/azure/install-azure-cli) van Azure cli hebben.

```azurecli
az deployment group create \
  --name addoutputs \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

In de uitvoer voor de implementatie opdracht ziet u een object dat lijkt op het volgende voor beeld, alleen als de uitvoer de JSON-indeling heeft:

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

> [!NOTE]
> Als de implementatie is mislukt, gebruikt u de switch **debug** met de implementatie opdracht om de logboeken voor fout opsporing weer te geven.  U kunt ook de **uitgebreide** switch gebruiken om de volledige logboeken voor fout opsporing weer te geven.

## <a name="review-your-work"></a>Uw werk controleren

U hebt in de laatste zes zelf studies een hoop gemaakt. Laten we even eens kijken wat u hebt gedaan. U hebt een sjabloon gemaakt met para meters die u gemakkelijk kunt opgeven. De sjabloon kan in verschillende omgevingen worden gebruikt, omdat deze kan worden aangepast en de benodigde waarden dynamisch kunnen worden gemaakt. Het retourneert ook informatie over het opslag account dat u in uw script kunt gebruiken.

Nu gaan we kijken naar de resource groep en de implementatie geschiedenis.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
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

1. Selecteer in de Azure Portal **resource groep** in het menu links.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep.
4. Selecteer **resource groep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een retour waarde aan de sjabloon toegevoegd. In de volgende zelf studie leert u hoe u een sjabloon kunt exporteren en gedeelten van de geëxporteerde sjabloon in uw sjabloon gebruikt.

> [!div class="nextstepaction"]
> [Geëxporteerde sjabloon gebruiken](template-tutorial-export-template.md)
