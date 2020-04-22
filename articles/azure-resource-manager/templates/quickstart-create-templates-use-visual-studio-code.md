---
title: Sjabloon maken - Visual Studio Code
description: Gebruik Visual Studio Code en de Azure Resource Manager-extensie voor hulpprogramma's om te werken met Resource Manager-sjablonen.
author: neilpeterson
ms.date: 04/17/2020
ms.topic: quickstart
ms.author: nepeters
ms.openlocfilehash: cd107db5220a96d75092a94736e060ae46672926
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686618"
---
# <a name="quickstart-create-azure-resource-manager-templates-with-visual-studio-code"></a>Snelstart: Azure Resource Manager-sjablonen maken met Visual Studio-code

De Azure Resource Manager Tools for Visual Studio Code bieden taalondersteuning, resourcefragmenten en automatisch aanvullen van bronnen. Met deze hulpprogramma's u Azure Resource Manager-sjablonen maken en valideren. In deze quickstart gebruikt u de extensie om een Azure Resource Manager-sjabloon helemaal opnieuw te maken. Daarbij ervaart u de uitbreidingsmogelijkheden, zoals ARM-sjabloonfragmenten, validatie, voltooiingen en ondersteuning van parameterbestanden.

Om deze quickstart te voltooien, hebt u [Visual Studio Code](https://code.visualstudio.com/)nodig, met de azure resource manager-extensie voor [hulpprogramma's](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) geïnstalleerd. U hebt ook de [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) of de [Azure PowerShell-module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.7.0) geïnstalleerd en geverifieerd.

Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="create-an-arm-template"></a>Een ARM-sjabloon maken

Maak en open met Visual Studio Code een nieuw bestand met de naam *azuredeploy.json*. Voer `arm` de codeeditor in, die Azure Resource Manager-fragmenten initieert voor het uitzetten van een ARM-sjabloon.

Selecteer `arm!` om een sjabloon te maken die is geschikt voor een implementatie van een Azure-brongroep.

![Afbeelding met azure resource manager-steigers](./media/quickstart-create-templates-use-visual-studio-code/1.png)

Dit fragment maakt de basisbouwstenen voor een ARM-sjabloon.

![Afbeelding met een volledig steigerende ARM-sjabloon](./media/quickstart-create-templates-use-visual-studio-code/2.png)

De taalmodus Visual Studio Code is gewijzigd van *JSON* naar *Azure Resource Manager-sjabloon*. De extensie bevat een taalserver die specifiek is voor ARM-sjablonen en die ARM-sjabloonspecifieke validatie, voltooiing en andere taalservices biedt.

![Afbeelding met Azure Resource Manager als de taalmodus Visual Studio Code](./media/quickstart-create-templates-use-visual-studio-code/3.png)

## <a name="add-an-azure-resource"></a>Een Azure-bron toevoegen

De extensie bevat fragmenten voor veel Azure-bronnen. Deze fragmenten kunnen worden gebruikt om eenvoudig resources toe te voegen aan de implementatie van uw sjabloon.

Plaats de cursor in het blok `storage` **sjabloonbronnen,** typ in en selecteer het *armopslagfragment.*

![Afbeelding met een resource die wordt toegevoegd aan de ARM-sjabloon](./media/quickstart-create-templates-use-visual-studio-code/4.png)

Met deze actie wordt een opslagbron aan de sjabloon toegevoegd.

![Afbeelding met een Azure Storage-bron in een ARM-sjabloon](./media/quickstart-create-templates-use-visual-studio-code/5.png)

De **tabtoets** kan worden gebruikt om het tabblad door configureerbare eigenschappen op het opslagaccount te gebruiken.

![Afbeelding die laat zien hoe de tabtoets kan worden gebruikt om door resourceconfiguratie te navigeren](./media/quickstart-create-templates-use-visual-studio-code/6.png)

## <a name="completion-and-validation"></a>Voltooiing en validatie

Een van de krachtigste mogelijkheden van de extensie is de integratie met Azure-schema's. Azure-schema's bieden de extensie validatie- en resourcebewuste voltooiingsmogelijkheden. Laten we het opslagaccount aanpassen om validatie en voltooiing in actie te zien. 

Werk eerst de opslagaccountsoort bij naar `megaStorage`een ongeldige waarde, zoals . Deze actie geeft een waarschuwing weer `megaStorage` die aangeeft dat dit geen geldige waarde is.

![Afbeelding met een ongeldige opslagconfiguratie](./media/quickstart-create-templates-use-visual-studio-code/7.png)

Als u de voltooiingsmogelijkheden wilt gebruiken, `megaStorage`verwijdert u de `ctrl`  +  `space`cursor in de dubbele aanhalingstekens en drukt u op . Deze actie bevat een voltooiingslijst met geldige waarden.

![Afbeelding met automatisch aanvullen van extensie](./media/quickstart-create-templates-use-visual-studio-code/8.png)

## <a name="add-template-parameters"></a>Sjabloonparameters toevoegen

Maak nu een parameter en gebruik deze om de naam van het opslagaccount op te geven.

Plaats de cursor in het parametersblok, `par`voeg een vervoerretour toe, typ en selecteer vervolgens het `arm-param-value` fragment. Met deze actie wordt een algemene parameter aan de sjabloon toegevoegd.

![Afbeelding met een parameter die wordt toegevoegd aan de ARM-sjabloon](./media/quickstart-create-templates-use-visual-studio-code/9.png)

Werk de naam van `storageAccountName` de parameter `Storage Account Name`bij en de beschrijving naar .

![Afbeelding met de voltooide parameter in een ARM-sjabloon](./media/quickstart-create-templates-use-visual-studio-code/10.png)

Azure-opslagaccountnamen hebben een minimale lengte van 3 tekens en een maximum van 24 tekens. Voeg `minLength` beide `maxLength` toe en aan de parameter en geef de juiste waarden.

![Afbeelding met minLengte en maxLengte die worden toegevoegd aan een armsjabloonparameter](./media/quickstart-create-templates-use-visual-studio-code/11.png)

Werk nu op de opslagbron de eigenschap naam bij om de parameter te gebruiken. Verwijder hiervoor de huidige naam. Voer een dubbele offerte en `[`een vierkante haak openen in, die een lijst met ARM-sjabloonfuncties produceert. Selecteer *parameters* in de lijst. 

![Afbeelding met automatische voltooiing bij het gebruik van parameters in ARM-sjabloonbronnen](./media/quickstart-create-templates-use-visual-studio-code/12.png)

Als u `'` één aanhalingsteken in de ronde haakjes invoert, wordt een lijst weergegeven met alle parameters die in de sjabloon zijn gedefinieerd, in dit geval *storageAccountName*. Selecteer de parameter.

![Afbeelding met voltooide parameter in een ARM-sjabloonbron](./media/quickstart-create-templates-use-visual-studio-code/13.png)

## <a name="create-a-parameter-file"></a>Een parameterbestand maken

Met een arm-sjabloonparameterbestand u omgevingsspecifieke parameterwaarden opslaan en deze waarden als groep doorgeven tijdens de implementatietijd. U bijvoorbeeld een parameterbestand hebben met waarden die specifiek zijn voor een testomgeving en een andere voor een productieomgeving.

De extensie maakt het eenvoudig om een parameterbestand te maken op basis van uw bestaande sjablonen. Klik hiervoor met de rechtermuisknop op de sjabloon `Select/Create Parameter File`in de codeeditor en selecteer .

![Afbeelding met het rechtsklikproces voor het maken van een parameterbestand op basis van een ARM-sjabloon](./media/quickstart-create-templates-use-visual-studio-code/14.png)

`New`  >  Selecteer `All Parameters` > Selecteer een naam en locatie voor het parameterbestand.

![Afbeelding met de naam en het dialoogvenster Bestand opslaan bij het maken van een parametersbestand op basis van een ARM-sjabloon](./media/quickstart-create-templates-use-visual-studio-code/15.png)

Met deze actie wordt een nieuw parameterbestand gemaakt en wordt het toegewezen aan de sjabloon waaruit het is gemaakt. U de huidige sjabloon/parameterbestandstoewijzing in de statusbalk van de Visual Studio Code bekijken en wijzigen terwijl de sjabloon is geselecteerd.

![](./media/quickstart-create-templates-use-visual-studio-code/16.png)

Nu het parameterbestand is toegewezen aan de sjabloon, valideert de extensie zowel het sjabloon- als het parameterbestand samen. Als u deze validatie in de praktijk wilt `storageAccountName` bekijken, voegt u een waarde met twee tekens toe aan de parameter in het parameterbestand en slaat u het bestand op.

![Afbeelding met een ongeldig verklaringssjabloon vanwege het probleem met het parameterbestand](./media/quickstart-create-templates-use-visual-studio-code/17.png)

Navigeer terug naar de ARM-sjabloon en merk op dat er een fout is opgetreden die aangeeft dat de waarde niet aan de parametercriteria voldoet.

![Afbeelding met een geldige ARM-sjabloon](./media/quickstart-create-templates-use-visual-studio-code/18.png)

Werk de waarde bij naar iets dat geschikt is, sla het bestand op en navigeer terug naar de sjabloon. Merk op dat de fout op de parameter is opgelost.

## <a name="deploy-the-template"></a>De sjabloon implementeren

Open de geïntegreerde Visual Studio `ctrl`  +  ```` ` ```` Code-terminal met de toetscombinatie en gebruik de Azure CLI- of Azure PowerShell-module om de sjabloon te implementeren.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az group create --name arm-vscode --location eastus

az deployment group create --resource-group arm-vscode --template-file azuredeploy.json --parameters azuredeploy.parameters.json
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
New-AzResourceGroup -Name arm-vscode -Location eastus

New-AzResourceGroupDeployment -ResourceGroupName arm-vscode -TemplateFile ./azuredeploy.json -TemplateParameterFile ./azuredeploy.parameters.json
```
---

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer de Azure-resources niet meer nodig zijn, gebruikt u de Azure CLI- of Azure PowerShell-module om de snelstartbrongroep te verwijderen.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az group delete --name arm-vscode
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
Remove-AzResourceGroup -Name arm-vscode
```
---

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudies voor beginners](./template-tutorial-create-first-template.md)
