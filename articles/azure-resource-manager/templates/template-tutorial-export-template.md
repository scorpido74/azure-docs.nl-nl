---
title: Zelfstudie - Sjabloon exporteren vanuit de Azure-portal
description: Meer informatie over het gebruik van een geëxporteerde sjabloon om de ontwikkeling van uw sjabloon te voltooien.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 5a0d373fdf75f19c8fc1082593c15c14770f79c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369903"
---
# <a name="tutorial-use-exported-template-from-the-azure-portal"></a>Zelfstudie: Geëxporteerde sjabloon gebruiken vanuit de Azure-portal

In deze zelfstudiereeks hebt u een sjabloon gemaakt om een Azure-opslagaccount te implementeren. In de volgende twee zelfstudies voegt u een *App Service-abonnement* en een *website*toe. In plaats van helemaal opnieuw sjablonen te maken, leert u hoe u sjablonen exporteert vanuit de Azure-portal en hoe u voorbeeldsjablonen gebruiken uit de [Azure Quickstart-sjablonen.](https://azure.microsoft.com/resources/templates/) U past deze sjablonen aan voor uw gebruik. Deze zelfstudie richt zich op het exporteren van sjablonen en het aanpassen van het resultaat voor uw sjabloon. Het duurt ongeveer **14 minuten** om te voltooien.

## <a name="prerequisites"></a>Vereisten

We raden u aan de [zelfstudie over uitvoer te](template-tutorial-add-outputs.md)voltooien, maar dit is niet vereist.

U moet beschikken over Visual Studio Code met de extensie Hulpmiddelen voor ResourceBeheer en Azure PowerShell of Azure CLI. Zie [sjabloongereedschappen voor](template-tutorial-create-first-template.md#get-tools)meer informatie .

## <a name="review-template"></a>Sjabloon bekijken

Aan het einde van de vorige zelfstudie had uw sjabloon de volgende JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.json":::

Deze sjabloon werkt goed voor het implementeren van opslagaccounts, maar u wilt er misschien meer resources aan toevoegen. U een sjabloon exporteren vanuit een bestaande resource om snel de JSON voor die resource te krijgen.

## <a name="create-app-service-plan"></a>Een App Service-plan maken

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer **Een resource maken**.
1. Typ **app-serviceplan**en selecteer app-serviceabonnement in **De Marktplaats**zoeken en selecteer vervolgens **het app-serviceplan**.  Selecteer geen **App Service-abonnement (klassiek)**
1. Selecteer **Maken**.
1. Voer het volgende in:

    - **Abonnement:** selecteer uw Azure-abonnement.
    - **Resourcegroep:** selecteer **Nieuw maken** en geef vervolgens een naam op. Geef een andere naam van de resourcegroep op dan die u in deze zelfstudiereeks hebt gebruikt.
    - **Naam:** voer een naam in voor het App-serviceplan.
    - **Besturingssysteem**: selecteer **Linux**.
    - **Regio**: selecteer een Azure-locatie. Bijvoorbeeld **VS - centraal**.
    - **Prijscategorie**: om kosten te besparen, wijzigt u de SKU in **Basic B1** (onder Dev/Test).

    ![Sjabloonportal voor sjabloon voor resourcebeheer](./media/template-tutorial-export-template/resource-manager-template-export.png)
1. Selecteer **Controleren en maken**.
1. Selecteer **Maken**. Het duurt een paar ogenblikken om de bron te maken.

## <a name="export-template"></a>Sjabloon exporteren

1. Selecteer **Ga naar resource**.

    ![Ga naar resource](./media/template-tutorial-export-template/resource-manager-template-export-go-to-resource.png)

1. Selecteer **Sjabloon Exporteren**.

    ![Exportsjabloon ResourceManager](./media/template-tutorial-export-template/resource-manager-template-export-template.png)

   De functie exportsjabloon neemt de huidige status van een resource en genereert een sjabloon om deze te implementeren. Het exporteren van een sjabloon kan een handige manier zijn om snel de JSON te krijgen die u nodig hebt om een resource te implementeren.

1. Kopieer de **definitie van Microsoft.Web/serverfarms** en de parameterdefinitie naar uw sjabloon.

    ![Exportsjabloon voor resourcebeheersjabloon](./media/template-tutorial-export-template/resource-manager-template-exported-template.png)

> [!IMPORTANT]
> Doorgaans is de geëxporteerde sjabloon meer verbose dan u zou willen bij het maken van een sjabloon. Het SKU-object in de geëxporteerde sjabloon heeft bijvoorbeeld vijf eigenschappen. Deze sjabloon werkt, maar u gewoon de **eigenschap naam** gebruiken. U beginnen met de geëxporteerde sjabloon en deze vervolgens wijzigen zoals u wilt dat deze aan uw vereisten voldoet.

## <a name="revise-existing-template"></a>Bestaande sjabloon herzien

De geëxporteerde sjabloon geeft u het grootste deel van de JSON die u nodig hebt, maar u moet deze aanpassen voor uw sjabloon. Besteed bijzondere aandacht aan verschillen in parameters en variabelen tussen uw sjabloon en de geëxporteerde sjabloon. Het exportproces kent uiteraard niet de parameters en variabelen die u al in uw sjabloon hebt gedefinieerd.

In het volgende voorbeeld worden de toevoegingen aan uw sjabloon belicht. Het bevat de geëxporteerde code plus enkele wijzigingen. Ten eerste wordt de naam van de parameter gewijzigd die overeenkomt met uw naamgevingsconventie. Ten tweede gebruikt het uw locatieparameter voor de locatie van het app-serviceplan. Ten derde verwijdert het object de **naam** in het **eigenschappenobject** omdat deze waarde overbodig is met de **eigenschap naam** op resourceniveau.

Kopieer het hele bestand en vervang de sjabloon door de inhoud ervan.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.json" range="1-77" highlight="28-31,50-69":::

## <a name="deploy-template"></a>Sjabloon implementeren

Gebruik Azure CLI of Azure PowerShell om een sjabloon te implementeren.

Zie [Resourcegroep maken](template-tutorial-create-first-template.md#create-resource-group)als u de resourcegroep niet hebt gemaakt. In het voorbeeld wordt ervan uitgegaan dat u de **variabele templateFile** hebt ingesteld op het pad naar het sjabloonbestand, zoals wordt weergegeven in de [eerste zelfstudie.](template-tutorial-create-first-template.md#deploy-template)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addappserviceplan `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u deze implementatieopdracht wilt uitvoeren, moet u over de [nieuwste versie](/cli/azure/install-azure-cli) van Azure CLI beschikken.

```azurecli
az deployment group create \
  --name addappserviceplan \
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
1. De brongroep bevat een opslagaccount en een App Service-abonnement.

## <a name="clean-up-resources"></a>Resources opschonen

Als u doorgaat naar de volgende zelfstudie, hoeft u de brongroep niet te verwijderen.

Als u nu stopt, u de resources die u hebt geïmplementeerd, opschonen door de resourcegroep te verwijderen.

1. Selecteer **resourcegroep** in de linkermenu in de Azure-portal.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep.
4. Selecteer **Brongroep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

U hebt geleerd hoe u een sjabloon exporteert vanuit de Azure-portal en hoe u de geëxporteerde sjabloon gebruiken voor de ontwikkeling van uw sjabloon. U de Azure Quickstart-sjablonen ook gebruiken om de sjabloonontwikkeling te vereenvoudigen.

> [!div class="nextstepaction"]
> [Azure Quickstart-sjablonen gebruiken](template-tutorial-quickstart-template.md)
