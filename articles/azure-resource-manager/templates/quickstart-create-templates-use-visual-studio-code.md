---
title: Sjabloon maken-Visual Studio code
description: Gebruik Visual Studio Code en de Azure Resource Manager-extensie voor hulpprogramma's om te werken met Resource Manager-sjablonen.
author: neilpeterson
ms.date: 04/17/2020
ms.topic: quickstart
ms.author: nepeters
ms.openlocfilehash: cd107db5220a96d75092a94736e060ae46672926
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81686618"
---
# <a name="quickstart-create-azure-resource-manager-templates-with-visual-studio-code"></a>Snelstartgids: Azure Resource Manager sjablonen maken met Visual Studio code

De Azure Resource Manager-Hulpprogram Ma's voor Visual Studio code bieden taal ondersteuning, resource fragmenten en automatisch aanvullen van resources. Met deze hulpprogram ma's kunt u Azure Resource Manager sjablonen maken en valideren. In deze Quick Start gebruikt u de extensie om een volledig nieuwe Azure Resource Manager sjabloon te maken. U hebt nu de uitbreidings mogelijkheden, zoals de ARM-sjabloon fragmenten, validatie, voltooiing en ondersteuning voor het parameter bestand.

Als u deze Snelstartgids wilt volt ooien, hebt u [Visual Studio code](https://code.visualstudio.com/)nodig, waarop de [uitbrei ding Azure Resource Manager-hulpprogram ma's](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) is geïnstalleerd. U hebt ook de [Azure cli](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) of de [module Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.7.0) moet zijn geïnstalleerd en geverifieerd.

Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="create-an-arm-template"></a>Een ARM-sjabloon maken

Maak en open met Visual Studio code een nieuw bestand met de naam *azuredeploy. json*. Voer `arm` in de code-editor, waarmee Azure Resource Manager fragmenten worden gestart voor het steigeren van een arm-sjabloon.

Selecteer `arm!` deze optie om een sjabloon bereik te maken voor een implementatie van een Azure-resource groep.

![Afbeelding van Azure Resource Manager steiger](./media/quickstart-create-templates-use-visual-studio-code/1.png)

Met dit fragment maakt u de basis bouwstenen voor een ARM-sjabloon.

![Afbeelding van een volledig gesteigerde ARM-sjabloon](./media/quickstart-create-templates-use-visual-studio-code/2.png)

U ziet dat de Visual Studio code language-modus is gewijzigd van *JSON* in *Azure Resource Manager sjabloon*. De uitbrei ding bevat een taal server die specifiek is voor ARM-sjablonen die een ARM sjabloon-specifieke validatie, voltooiing en andere taal services bieden.

![Afbeelding met Azure Resource Manager als de Visual Studio code language-modus](./media/quickstart-create-templates-use-visual-studio-code/3.png)

## <a name="add-an-azure-resource"></a>Een Azure-resource toevoegen

De uitbrei ding bevat fragmenten voor veel Azure-resources. Deze fragmenten kunnen worden gebruikt om eenvoudig resources aan uw sjabloon implementatie toe te voegen.

Plaats de cursor in het blok met sjabloon **resources** , typ `storage`in en selecteer het fragment voor *arm-opslag* .

![Afbeelding met een resource die wordt toegevoegd aan de ARM-sjabloon](./media/quickstart-create-templates-use-visual-studio-code/4.png)

Met deze actie wordt een opslag resource aan de sjabloon toegevoegd.

![Afbeelding van een Azure Storage bron in een ARM-sjabloon](./media/quickstart-create-templates-use-visual-studio-code/5.png)

De **Tab** -toets kan worden gebruikt om te tab via Configureer bare eigenschappen van het opslag account.

![Afbeelding die laat zien hoe de tab-toets kan worden gebruikt om te navigeren door de resource configuratie](./media/quickstart-create-templates-use-visual-studio-code/6.png)

## <a name="completion-and-validation"></a>Voltooiing en validatie

Een van de krach tigste mogelijkheden van de uitbrei ding is de integratie met Azure-schema's. Azure-schema's bieden de uitbrei ding met validatie en resource-bewuste voltooiings mogelijkheden. We gaan het opslag account wijzigen om validatie en voltooiing in actie te zien. 

Werk eerst het type opslag account bij naar een ongeldige waarde, zoals `megaStorage`. U ziet dat met deze actie een waarschuwing wordt `megaStorage` gegenereerd die aangeeft dat dit geen geldige waarde is.

![Installatie kopie met een ongeldige opslag configuratie](./media/quickstart-create-templates-use-visual-studio-code/7.png)

Als u de voltooiings mogelijkheden wilt `megaStorage`gebruiken, verwijdert u de cursor binnen de dubbele aanhalings tekens `ctrl`  +  `space`en drukt u op. Met deze actie wordt een voltooiings lijst met geldige waarden weer gegeven.

![Afbeelding van de automatische aanvulling van de extensie](./media/quickstart-create-templates-use-visual-studio-code/8.png)

## <a name="add-template-parameters"></a>Sjabloon parameters toevoegen

Maak nu een para meter en gebruik deze om de naam van het opslag account op te geven.

Plaats de cursor in het blok para meters, voeg een regel terugloop `par`toe, typ en selecteer `arm-param-value` vervolgens het fragment. Met deze actie wordt een generieke para meter aan de sjabloon toegevoegd.

![Afbeelding met een para meter die wordt toegevoegd aan de ARM-sjabloon](./media/quickstart-create-templates-use-visual-studio-code/9.png)

Werk de naam van de para meter `storageAccountName` bij naar en de `Storage Account Name`beschrijving in.

![Afbeelding van de voltooide para meter in een ARM-sjabloon](./media/quickstart-create-templates-use-visual-studio-code/10.png)

Namen van Azure Storage-accounts hebben een minimum lengte van drie tekens en een maximum van 24. Voeg zowel `minLength` als `maxLength` aan de para meter toe en geef de juiste waarden op.

![Afbeelding met minLength en maxLength die wordt toegevoegd aan een ARM-sjabloon parameter](./media/quickstart-create-templates-use-visual-studio-code/11.png)

Werk nu bij de opslag resource de eigenschap name bij om de para meter te gebruiken. Als u dit wilt doen, verwijdert u de huidige naam. Voer een dubbel aanhalings teken en een vier `[`kant haakje openen in, die een lijst met arm-sjabloon functies produceert. Selecteer *para meters* in de lijst. 

![Installatie kopie met automatische aanvulling bij het gebruik van para meters in ARM-sjabloon bronnen](./media/quickstart-create-templates-use-visual-studio-code/12.png)

Als u één aanhalings teken `'` in de ronde haakjes invoert, wordt een lijst met alle gedefinieerde para meters in de sjabloon gegenereerd, in dit geval *storageAccountName*. Selecteer de para meter.

![Afbeelding van de voltooide para meter in een ARM-sjabloon resource](./media/quickstart-create-templates-use-visual-studio-code/13.png)

## <a name="create-a-parameter-file"></a>Een parameter bestand maken

Met een ARM-sjabloon parameter bestand kunt u omgevings-specifieke parameter waarden opslaan en deze waarden in als een groep tijdens de implementatie door geven. U kunt bijvoorbeeld een parameter bestand hebben met waarden die specifiek zijn voor een test omgeving en een andere voor een productie omgeving.

Met deze extensie kunt u eenvoudig een parameter bestand maken op basis van uw bestaande sjablonen. Als u dit wilt doen, klikt u met de rechter muisknop op de sjabloon in `Select/Create Parameter File`de code-editor en selecteert u.

![Afbeelding van het rechtermuisknop proces voor het maken van een parameter bestand op basis van een ARM-sjabloon](./media/quickstart-create-templates-use-visual-studio-code/14.png)

`New`  >  Selecteer `All Parameters` > een naam en locatie voor het parameter bestand te selecteren.

![Afbeelding van het dialoog venster naam en bestand opslaan bij het maken van een parameter bestand van een ARM-sjabloon](./media/quickstart-create-templates-use-visual-studio-code/15.png)

Met deze actie wordt een nieuw parameter bestand gemaakt en toegewezen aan de sjabloon waaruit het is gemaakt. U kunt in de status balk van Visual Studio code de huidige toewijzing van sjabloon/para meters weer geven en wijzigen terwijl de sjabloon wordt geselecteerd.

![](./media/quickstart-create-templates-use-visual-studio-code/16.png)

Nu het parameter bestand is toegewezen aan de sjabloon, valideert de uitbrei ding zowel de sjabloon als het parameter bestand samen. Als u deze validatie in de praktijk wilt zien, voegt u een waarde van `storageAccountName` twee tekens toe aan de para meter in het parameter bestand en slaat u het bestand op.

![Afbeelding met een ongeldig sjabloon vanwege een probleem met het parameter bestand](./media/quickstart-create-templates-use-visual-studio-code/17.png)

Ga terug naar de ARM-sjabloon en u ziet dat er een fout is gegenereerd die aangeeft dat de waarde niet voldoet aan de criteria van de para meter.

![Afbeelding met een geldige ARM-sjabloon](./media/quickstart-create-templates-use-visual-studio-code/18.png)

Werk de waarde bij naar iets wat van toepassing is, sla het bestand op en ga terug naar de sjabloon. U ziet dat de fout voor de para meter is opgelost.

## <a name="deploy-the-template"></a>De sjabloon implementeren

Open de geïntegreerde Visual Studio code-Terminal met `ctrl`  +  ```` ` ```` behulp van de toetscombinatie en gebruik de module Azure CLI of Azure PowerShell om de sjabloon te implementeren.

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

Wanneer de Azure-resources niet meer nodig zijn, gebruikt u de Azure CLI-of Azure PowerShell-module om de Quick Start-resource groep te verwijderen.

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
