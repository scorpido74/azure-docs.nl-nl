---
title: Zelfstudie - uitvoer toevoegen aan sjabloon
description: Voeg uitvoer toe aan uw Azure Resource Manager-sjabloon om de syntaxis te vereenvoudigen.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 2ee1a2c7037bde68b7858b57a03c78bd2016ff1c
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743549"
---
# <a name="tutorial-add-outputs-to-your-arm-template"></a>Zelfstudie: uitvoer toevoegen aan uw ARM-sjabloon

In deze zelfstudie leert u hoe u een waarde retourneert uit uw Azure Resource Manager-sjabloon (ARM). U gebruikt uitvoer wanneer u een waarde uit een geïmplementeerde resource nodig hebt. Deze tutorial duurt **7 minuten** om te voltooien.

## <a name="prerequisites"></a>Vereisten

We raden u aan de [zelfstudie over variabelen in](template-tutorial-add-variables.md)te vullen, maar dit is niet vereist.

U moet beschikken over Visual Studio Code met de extensie Hulpmiddelen voor ResourceBeheer en Azure PowerShell of Azure CLI. Zie [sjabloongereedschappen voor](template-tutorial-create-first-template.md#get-tools)meer informatie .

## <a name="review-template"></a>Sjabloon bekijken

Aan het einde van de vorige zelfstudie had uw sjabloon de volgende JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.json":::

Het implementeert een opslagaccount, maar het geeft geen informatie over het opslagaccount. Mogelijk moet u eigenschappen van een nieuwe bron vastleggen, zodat deze later beschikbaar zijn voor referentie.

## <a name="add-outputs"></a>Uitvoer toevoegen

U uitvoer gebruiken om waarden uit de sjabloon terug te sturen. Het kan bijvoorbeeld handig zijn om de eindpunten voor uw nieuwe opslagaccount op te halen.

In het volgende voorbeeld wordt de wijziging in uw sjabloon benadrukt om een uitvoerwaarde toe te voegen. Kopieer het hele bestand en vervang de sjabloon door de inhoud ervan.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.json" range="1-53" highlight="47-52":::

Er zijn een aantal belangrijke items op te merken over de output waarde die u toegevoegd.

Het type geretourneerde waarde is ingesteld op **object,** wat betekent dat een JSON-object wordt geretourneerd.

Het maakt gebruik van de [referentiefunctie](template-functions-resource.md#reference) om de runtime-status van het opslagaccount te krijgen. Als u de runtime-status van een resource wilt krijgen, geeft u de naam of id van een resource door. In dit geval gebruikt u dezelfde variabele die u hebt gebruikt om de naam van het opslagaccount te maken.

Ten slotte retourneert het de eigenschap **primaire eindpunten** van het opslagaccount

## <a name="deploy-template"></a>Sjabloon implementeren

U bent klaar om de sjabloon te implementeren en de geretourneerde waarde te bekijken.

Zie [Resourcegroep maken](template-tutorial-create-first-template.md#create-resource-group)als u de resourcegroep niet hebt gemaakt. In het voorbeeld wordt ervan uitgegaan dat u de **variabele templateFile** hebt ingesteld op het pad naar het sjabloonbestand, zoals wordt weergegeven in de [eerste zelfstudie.](template-tutorial-create-first-template.md#deploy-template)

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

Als u deze implementatieopdracht wilt uitvoeren, moet u over de [nieuwste versie](/cli/azure/install-azure-cli) van Azure CLI beschikken.

```azurecli
az deployment group create \
  --name addoutputs \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

In de uitvoer voor de opdracht implementatie ziet u een object dat vergelijkbaar is met het volgende voorbeeld alleen als de uitvoer in JSON-indeling is:

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
> Als de implementatie is mislukt, gebruikt u de **foutopsporingsschakelaar** met de opdracht implementatie om de foutopsporingslogboeken weer te geven.  U ook de **verbose-schakelaar** gebruiken om de volledige foutopsporingslogboeken weer te geven.

## <a name="review-your-work"></a>Bekijk uw werk

Je hebt veel gedaan in de laatste zes tutorials. Laten we even de tijd nemen om te bekijken wat je hebt gedaan. U hebt een sjabloon gemaakt met parameters die eenvoudig te bieden zijn. De sjabloon is herbruikbaar in verschillende omgevingen omdat het aanpassingsmogelijkheden biedt en dynamisch de benodigde waarden creëert. Het retourneert ook informatie over het opslagaccount dat u in uw script gebruiken.

Laten we nu eens kijken naar de resourcegroep en de implementatiegeschiedenis.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer **Resourcegroepen**in het linkermenu .
1. Selecteer de resourcegroep die u hebt geïmplementeerd.
1. Afhankelijk van de stappen die u hebt gezet, moet u ten minste één en misschien meerdere opslagaccounts in de resourcegroep hebben.
1. U moet ook verschillende succesvolle implementaties in de geschiedenis hebben. Selecteer die koppeling.

   ![Implementaties selecteren](./media/template-tutorial-add-outputs/select-deployments.png)

1. Je ziet al je implementaties in de geschiedenis. Selecteer de implementatie met de naam **addoutputs**.

   ![Implementatiegeschiedenis weergeven](./media/template-tutorial-add-outputs/show-history.png)

1. U de ingangen bekijken.

   ![Ingangen weergeven](./media/template-tutorial-add-outputs/show-inputs.png)

1. U de uitgangen bekijken.

   ![Uitvoer weergeven](./media/template-tutorial-add-outputs/show-outputs.png)

1. U de sjabloon bekijken.

   ![Sjabloon weergeven](./media/template-tutorial-add-outputs/show-template.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u doorgaat naar de volgende zelfstudie, hoeft u de brongroep niet te verwijderen.

Als u nu stopt, u de resources die u hebt geïmplementeerd, opschonen door de resourcegroep te verwijderen.

1. Selecteer **resourcegroep** in de linkermenu in de Azure-portal.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep.
4. Selecteer **Brongroep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een retourwaarde aan de sjabloon toegevoegd. In de volgende zelfstudie leert u hoe u een sjabloon exporteert en delen van die geëxporteerde sjabloon in uw sjabloon gebruikt.

> [!div class="nextstepaction"]
> [Geëxporteerde sjabloon gebruiken](template-tutorial-export-template.md)
