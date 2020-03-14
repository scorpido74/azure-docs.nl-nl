---
title: 'Zelf studie: sjabloon exporteren uit de Azure Portal'
description: Meer informatie over het gebruik van een geëxporteerde sjabloon om de ontwikkeling van uw sjabloon te volt ooien.
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 95d54a0661f0a0cebdbfc225074be0ce0d83a5cc
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/14/2020
ms.locfileid: "79368890"
---
# <a name="tutorial-use-exported-template-from-the-azure-portal"></a>Zelf studie: geëxporteerde sjabloon uit het Azure Portal gebruiken

In deze zelfstudie reeks hebt u een sjabloon gemaakt voor het implementeren van een Azure-opslag account. In de volgende twee zelf studies voegt u een *app service-abonnement* en een *website*toe. In plaats van helemaal nieuwe sjablonen te maken, leert u hoe u sjablonen uit de Azure Portal exporteert en hoe u voorbeeld sjablonen uit de [Azure Quick](https://azure.microsoft.com/resources/templates/)start-sjablonen gebruikt. U kunt deze sjablonen aanpassen voor uw gebruik. Deze zelf studie is gericht op het exporteren van sjablonen en het aanpassen van het resultaat voor uw sjabloon. Het duurt ongeveer **14 minuten** om te volt ooien.

## <a name="prerequisites"></a>Vereisten

U wordt aangeraden de [zelf studie over uitvoer](template-tutorial-add-outputs.md)te volt ooien, maar dit is niet vereist.

U moet Visual Studio code hebben met de uitbrei ding Resource Manager tools en een Azure PowerShell of Azure CLI. Zie voor meer informatie [sjabloon hulpprogramma's](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Sjabloon controleren

Aan het einde van de vorige zelf studie had uw sjabloon de volgende JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.json":::

Deze sjabloon werkt goed voor het implementeren van opslag accounts, maar mogelijk wilt u er meer resources aan toevoegen. U kunt een sjabloon uit een bestaande resource exporteren om de JSON voor die resource snel te verkrijgen.

## <a name="create-app-service-plan"></a>Een App Service-plan maken

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer **Een resource maken**.
1. In **de Marketplace doorzoeken**voert u **app service plan**in en selecteert u vervolgens **app service plan**.  Selecteer geen **app service plan (klassiek)**
1. Selecteer **Maken**.
1. voer het volgende in:

    - **Abonnement**: selecteer uw Azure-abonnement.
    - **Resource groep**: Selecteer **nieuwe maken** en geef een naam op. Geef een andere naam op voor de resource groep dan het account dat u in deze zelfstudie reeks hebt gebruikt.
    - **Naam**: Voer een naam in voor het app service-plan.
    - **Besturings systeem**: Selecteer **Linux**.
    - **Regio**: Selecteer een Azure-locatie. Bijvoorbeeld **VS - centraal**.
    - **Prijs categorie**: als u kosten wilt besparen, wijzigt u de SKU in **Basic B1** (onder dev/test).

    ![Sjabloon portal voor het exporteren van Resource Manager-sjablonen](./media/template-tutorial-export-template/resource-manager-template-export.png)
1. Selecteer **controleren en maken**.
1. Selecteer **Maken**. Het duurt enkele minuten om de resource te maken.

## <a name="export-template"></a>Sjabloon exporteren

1. Selecteer **Ga naar resource**.

    ![Ga naar resource](./media/template-tutorial-export-template/resource-manager-template-export-go-to-resource.png)

1. Selecteer **sjabloon exporteren**.

    ![Sjabloon voor exporteren van Resource Manager-sjabloon](./media/template-tutorial-export-template/resource-manager-template-export-template.png)

   De functie sjabloon exporteren neemt de huidige status van een resource en genereert een sjabloon om deze te implementeren. Het exporteren van een sjabloon kan een handige manier zijn om snel de JSON te krijgen die u nodig hebt om een resource te implementeren.

1. Kopieer de definitie van **micro soft. web/server farms** en de parameter definitie naar uw sjabloon.

    ![Sjabloon export sjabloon geëxporteerde Resource Manager-sjabloon](./media/template-tutorial-export-template/resource-manager-template-exported-template.png)

> [!IMPORTANT]
> De geëxporteerde sjabloon is doorgaans uitgebreider dan u mogelijk wilt bij het maken van een sjabloon. Het SKU-object in de geëxporteerde sjabloon heeft bijvoorbeeld vijf eigenschappen. Deze sjabloon werkt, maar u kunt gewoon de eigenschap **name** gebruiken. U kunt beginnen met de geëxporteerde sjabloon en deze vervolgens aanpassen aan uw vereisten.

## <a name="revise-existing-template"></a>Bestaande sjabloon herzien

De geëxporteerde sjabloon biedt u de meeste benodigde JSON, maar u moet deze aanpassen voor uw sjabloon. Let vooral op verschillen in para meters en variabelen tussen uw sjabloon en de geëxporteerde sjabloon. Het export proces kent uiteraard niet de para meters en variabelen die u al hebt gedefinieerd in uw sjabloon.

In het volgende voor beeld worden de toevoegingen aan uw sjabloon gemarkeerd. Het bevat de geëxporteerde code plus enkele wijzigingen. Eerst wordt de naam van de para meter gewijzigd zodat deze overeenkomt met uw naam Conventie. Ten tweede wordt uw locatie parameter gebruikt voor de locatie van het app service-plan. Ten derde wordt de **naam** in het object **Properties** verwijderd, omdat deze waarde overbodig is met de eigenschap **name** op het niveau van de resource.

Kopieer het hele bestand en vervang de sjabloon door de inhoud ervan.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.json" range="1-77" highlight="28-31,50-69":::

## <a name="deploy-template"></a>Sjabloon implementeren

Gebruik Azure CLI of Azure PowerShell voor het implementeren van een sjabloon.

Als u de resource groep nog niet hebt gemaakt, raadpleegt u [resource groep maken](template-tutorial-create-first-template.md#create-resource-group). In het voor beeld wordt ervan uitgegaan dat u de **templateFile** -variabele hebt ingesteld op het pad naar het sjabloon bestand, zoals wordt weer gegeven in de [eerste zelf studie](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addappserviceplan `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name addappserviceplan \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

## <a name="verify-deployment"></a>Implementatie verifiëren

U kunt de implementatie controleren door de resource groep te verkennen van de Azure Portal.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer **resource groepen**in het menu links.
1. Selecteer de resource groep die u hebt geïmplementeerd.
1. De resource groep bevat een opslag account en een App Service plan.

## <a name="clean-up-resources"></a>Resources opschonen

Als u verdergaat met de volgende zelf studie, hoeft u de resource groep niet te verwijderen.

Als u nu stopt, wilt u misschien de resources opschonen die u hebt geïmplementeerd door de resource groep te verwijderen.

1. Selecteer **Resourcegroep** in het linkermenu van Azure Portal.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep.
4. Selecteer **Resourcegroep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

U hebt geleerd hoe u een sjabloon uit de Azure Portal exporteert en hoe u de geëxporteerde sjabloon gebruikt voor het ontwikkelen van sjablonen. U kunt ook de Azure Quick Start-sjablonen gebruiken om de ontwikkeling van sjablonen te vereenvoudigen.

> [!div class="nextstepaction"]
> [Sjablonen voor Azure Quick Start gebruiken](template-tutorial-quickstart-template.md)
