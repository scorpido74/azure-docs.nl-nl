---
title: Zelf studie-Azure Resource Manager sjabloon maken & implementeren
description: Maak uw eerste Azure Resource Manager sjabloon. In de zelf studie vindt u informatie over de syntaxis van het sjabloon bestand en het implementeren van een opslag account.
author: mumian
manager: carmonmills
ms.service: azure-resource-manager
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 463f9a54648a9e6950f839bd856ccf42bc66dca0
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74074362"
---
# <a name="tutorial-create-and-deploy-your-first-azure-resource-manager-template"></a>Zelf studie: uw eerste Azure Resource Manager sjabloon maken en implementeren

In deze zelf studie leert u Azure Resource Manager sjablonen. U ziet hoe u een start sjabloon maakt en deze implementeert in Azure. Meer informatie over de structuur van de sjabloon en de hulpprogram ma's die u nodig hebt voor het werken met sjablonen. Het duurt ongeveer **12 minuten** om deze zelf studie te volt ooien, maar de werkelijke tijd is afhankelijk van het aantal hulpprogram ma's dat u moet installeren.

Deze zelf studie is het eerste van een reeks. Tijdens de voortgang van de reeks wijzigt u de stap-voor-stap voor het starten van de sjabloon totdat u alle kern onderdelen van een resource manager-sjabloon hebt bekeken. Deze elementen zijn de bouw stenen voor veel complexere sjablonen. We hopen dat u aan het einde van de serie bent uw eigen sjablonen te maken en u klaar bent om uw implementaties te automatiseren met sjablonen.

Zie [Azure Resource Manager-sjablonen](template-deployment-overview.md)als u meer wilt weten over de voor delen van het gebruik van sjablonen en waarom u de implementatie wilt automatiseren met sjablonen.

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="get-tools"></a>Hulp middelen ophalen

Laten we beginnen door ervoor te zorgen dat u beschikt over de hulpprogram ma's die u nodig hebt voor het maken en implementeren van sjablonen.

### <a name="editor"></a>Editor

Sjablonen zijn JSON-bestanden. Als u sjablonen wilt maken, hebt u een goede JSON-editor nodig. We raden Visual Studio code aan met de uitbrei ding Resource Manager-Hulpprogram Ma's. Als u deze hulpprogram ma's wilt installeren, raadpleegt u [Visual Studio code gebruiken om Azure Resource Manager sjablonen te maken](./resource-manager-tools-vs-code.md).

### <a name="command-line-deployment"></a>Opdracht regel implementatie

U hebt ook een Azure PowerShell of Azure CLI nodig om de sjabloon te implementeren. Zie voor de installatie-instructies:

- [Azure PowerShell installeren](/powershell/azure/install-az-ps)
- [Azure CLI installeren in Windows](/cli/azure/install-azure-cli-windows)
- [Azure CLI installeren in Linux](/cli/azure/install-azure-cli-linux)

Nadat u Azure PowerShell of Azure CLI hebt geïnstalleerd, moet u zich voor de eerste keer aanmelden. Zie [Aanmelden bij Power shell](/powershell/azure/install-az-ps#sign-in) of [Aanmelden-Azure cli](/cli/azure/get-started-with-azure-cli#sign-in)voor meer informatie.

U bent nu klaar om te beginnen met het leren over sjablonen.

## <a name="create-your-first-template"></a>Uw eerste sjabloon maken

1. Open Visual Studio code met de uitbrei ding Resource Manager-Hulpprogram Ma's geïnstalleerd.
1. Selecteer in het menu **bestand** de optie **nieuw bestand** om een nieuw bestand te maken.
1. Selecteer in het menu **bestand** de optie **Opslaan als**.
1. Geef het bestand de naam **azuredeploy** en selecteer de extensie van het **JSON** -bestand. De volledige naam van het bestand **azuredeploy. json**.
1. Sla het bestand op uw werk station op. Selecteer een pad dat u gemakkelijk kunt onthouden omdat u het pad later bij het implementeren van de sjabloon opgeeft.
1. Kopieer de volgende JSON en plak deze in het bestand:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": []
    }
    ```

    De VS code-omgeving ziet er als volgt uit:

    ![Resource Manager-sjabloon Visual Studio code eerste sjabloon](./media/template-tutorial-create-first-template/resource-manager-visual-studio-code-first-template.png)

    Met deze sjabloon worden geen resources geïmplementeerd. We beginnen met een lege sjabloon, zodat u vertrouwd kunt raken met de stappen voor het implementeren van een sjabloon, terwijl de kans op een probleem wordt geminimaliseerd.

    Het JSON-bestand bevat de volgende elementen:

    - **$schema**: Hiermee geeft u de locatie van het JSON-schema bestand op. Het schema bestand beschrijft de eigenschappen die beschikbaar zijn in een sjabloon. Het schema definieert bijvoorbeeld **resources** als een van de geldige eigenschappen voor een sjabloon. U hoeft niet de datum voor het schema 2015-01-01. Deze schema versie is up-to-date en bevat alle nieuwste functies. De schema datum is niet gewijzigd omdat er sinds de introductie geen wijzigingen zijn opgegaan.
    - **contentVersion**: Hiermee geeft u de versie van de sjabloon (bijvoorbeeld 1.0.0.0) op. U kunt een wille keurige waarde voor dit element opgeven. Gebruik deze waarde als u belang rijke wijzigingen in uw sjabloon wilt documenteren. Wanneer u resources implementeert met behulp van de sjabloon, kan deze waarde worden gebruikt om ervoor te zorgen dat de juiste sjabloon wordt gebruikt.
    - **resources**: bevat de resources die u wilt implementeren of bijwerken. Het is momenteel leeg, maar u voegt later resources toe.

1. Sla het bestand op.

Gefeliciteerd, u hebt uw eerste sjabloon gemaakt.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan met uw Azure-referenties om aan de slag te gaan met Azure PowerShell/Azure CLI.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az login
```

---
## <a name="create-resource-group"></a>Een resourcegroep maken

Wanneer u een sjabloon implementeert, geeft u een resource groep op die de resources zal bevatten. Voordat u de implementatie opdracht uitvoert, maakt u de resource groep met behulp van Azure CLI of Azure PowerShell. Selecteer de tabbladen in het volgende code gedeelte om te kiezen tussen Azure PowerShell en Azure CLI.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name myResourceGroup `
  -Location "Central US"
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group create \
  --name myResourceGroup \
  --location "Central US"
```

---

## <a name="deploy-template"></a>Sjabloon implementeren

Als u de sjabloon wilt implementeren, gebruikt u Azure CLI of Azure PowerShell. Gebruik de resource groep die u hebt gemaakt. Geef een naam op voor de implementatie, zodat u deze gemakkelijk kunt herkennen in de implementatie geschiedenis. Voor het gemak maakt u ook een variabele die het pad naar het sjabloon bestand opslaat. Met deze variabele kunt u de implementatie opdrachten eenvoudiger uitvoeren omdat u het pad niet telkens opnieuw hoeft te typen.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$templateFile = "{provide-the-path-to-the-template-file}"
New-AzResourceGroupDeployment `
  -Name blanktemplate `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
$templateFile="{provide-the-path-to-the-template-file}"
az group deployment create \
  --name blanktemplate \
  --resource-group myResourceGroup \
  --template-file $templateFile
```

---

De implementatie opdracht retourneert resultaten. Zoek `ProvisioningState` om te controleren of de implementatie is geslaagd.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

![Inrichtings status van Power shell-implementatie](./media/template-tutorial-create-first-template/resource-manager-deployment-provisioningstate.png)

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

![Inrichtings status van Azure CLI-implementatie](./media/template-tutorial-create-first-template/azure-cli-provisioning-state.png)

---

## <a name="verify-deployment"></a>Implementatie verifiëren

U kunt de implementatie controleren door de resource groep te verkennen van de Azure Portal.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).

1. Selecteer **resource groepen**in het menu links.

1. Selecteer in de laatste procedure de resource groep implementeren. De standaard naam is **myResourceGroup**. U ziet dat er geen resource is geïmplementeerd in de resource groep.

1. In de rechter bovenhoek van het overzicht wordt de status van de implementatie weer gegeven. Selecteer **1 geslaagd**.

   ![Implementatie status weer geven](./media/template-tutorial-create-first-template/deployment-status.png)

1. U ziet een overzicht van de implementatie voor de resource groep. Selecteer **blanktemplate**.

   ![Implementatie selecteren](./media/template-tutorial-create-first-template/select-from-deployment-history.png)

1. Er wordt een samen vatting van de implementatie weer gegeven. In dit geval is er geen partij om te zien, omdat er geen resources zijn geïmplementeerd. Verderop in deze reeks vindt u het handig om de samen vatting in de implementatie geschiedenis te bekijken. Aan de linkerkant ziet u de invoer, uitvoer en de sjabloon die wordt gebruikt tijdens de implementatie.

   ![Implementatie samenvatting weer geven](./media/template-tutorial-create-first-template/view-deployment-summary.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u verdergaat met de volgende zelf studie, hoeft u de resource groep niet te verwijderen.

Als u nu stopt, wilt u mogelijk de resource groep verwijderen.

1. Selecteer **Resourcegroep** in het linkermenu van Azure Portal.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep.
4. Selecteer **Resourcegroep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

U hebt een eenvoudige sjabloon gemaakt om te implementeren in Azure. In de volgende zelf studie voegt u een opslag account toe aan de sjabloon en implementeert u deze in uw resource groep.

> [!div class="nextstepaction"]
> [Resource toevoegen](template-tutorial-add-resource.md)
