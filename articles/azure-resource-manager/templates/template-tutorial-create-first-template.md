---
title: Zelfstudie - & deploysjabloon maken
description: Maak uw eerste Azure Resource Manager-sjabloon. In de zelfstudie leert u over de syntaxis van het sjabloonbestand en hoe u een opslagaccount implementeert.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: ace76b9a13f44c14e348a0338ca01dd6b3948ce3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369928"
---
# <a name="tutorial-create-and-deploy-your-first-arm-template"></a>Zelfstudie: Uw eerste ARM-sjabloon maken en implementeren

In deze zelfstudie wordt u kennis gemaakt met ARM-sjablonen (Azure Resource Manager). Het toont u hoe u een startersjabloon maakt en implementeert deze in Azure. U leert over de structuur van de sjabloon en de tools die u nodig hebt voor het werken met sjablonen. Het duurt ongeveer **12 minuten** om deze tutorial te voltooien, maar de werkelijke tijd zal variëren op basis van hoeveel tools je nodig hebt om te installeren.

Deze tutorial is de eerste van een serie. Naarmate u door de reeks heen gaat, wijzigt u de startsjabloon stap voor stap totdat u alle kernonderdelen van een ARM-sjabloon hebt verkend. Deze elementen zijn de bouwstenen voor veel complexere sjablonen. We hopen dat u aan het einde van de serie zeker bent van het maken van uw eigen sjablonen en klaar bent om uw implementaties te automatiseren met sjablonen.

Zie [Azure Resource Manager-sjablonen](overview.md)als u meer wilt weten over de voordelen van het gebruik van sjablonen en waarom u de implementatie met sjablonen moet automatiseren.

Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="get-tools"></a>Tools oppakken

Laten we beginnen met ervoor te zorgen dat u de tools hebt die u nodig hebt om sjablonen te maken en te implementeren.

### <a name="editor"></a>Editor

Sjablonen zijn JSON-bestanden. Om sjablonen te maken, hebt u een goede JSON-editor nodig. We raden Visual Studio Code aan met de extensie Hulpmiddelen voor Resource Manager. Zie [Visual Studio Code gebruiken om ARM-sjablonen te maken](use-vs-code-to-create-template.md)als u deze hulpprogramma's wilt installeren.

### <a name="command-line-deployment"></a>Opdrachtregelimplementatie

U hebt ook Azure PowerShell of Azure CLI nodig om de sjabloon te implementeren. Als u Azure CLI gebruikt, moet u de nieuwste versie hebben. Zie voor de installatie-instructies:

- [Azure PowerShell installeren](/powershell/azure/install-az-ps)
- [Azure CLI installeren in Windows](/cli/azure/install-azure-cli-windows)
- [Azure CLI op Linux installeren](/cli/azure/install-azure-cli-linux)

Nadat u Azure PowerShell of Azure CLI hebt geïnstalleerd, moet u zich voor de eerste keer aanmelden. Zie Aanmelden [- PowerShell](/powershell/azure/install-az-ps#sign-in) of [Aanmelden voor](/cli/azure/get-started-with-azure-cli#sign-in)hulp voor hulp.

Oké, je bent klaar om te beginnen met het leren over sjablonen.

## <a name="create-your-first-template"></a>Uw eerste sjabloon maken

1. Open Visual Studio Code met de extensie Hulpmiddelen voor ResourceBeheer geïnstalleerd.
1. Selecteer **Nieuw** **bestand** in het menu Bestand om een nieuw bestand te maken.
1. Selecteer **File** **Opslaan als**.
1. Geef het bestand **een naam en** selecteer de **JSON-bestandsextensie.** De volledige naam van het bestand **azuredeploy.json**.
1. Sla het bestand op in uw werkstation. Selecteer een pad dat gemakkelijk te onthouden is omdat u dat pad later opgeeft bij het implementeren van de sjabloon.
1. Kopieer en plak de volgende JSON in het bestand:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": []
    }
    ```

    Zo ziet uw VS Code-omgeving eruit:

    ![Resource Manager-sjabloon visual studiocode eerste sjabloon](./media/template-tutorial-create-first-template/resource-manager-visual-studio-code-first-template.png)

    Deze sjabloon implementeert geen resources. We beginnen met een lege sjabloon, zodat u vertrouwd raken met de stappen om een sjabloon te implementeren terwijl de kans op iets misgaat wordt geminimaliseerd.

    Het JSON-bestand heeft de volgende elementen:

    - **$schema**: Hiermee geeft u de locatie van het JSON-schemabestand op. Het schemabestand beschrijft de eigenschappen die beschikbaar zijn in een sjabloon. Het schema definieert bijvoorbeeld **resources** als een van de geldige eigenschappen voor een sjabloon. Maak je geen zorgen dat de datum voor het schema 2019-04-01 is. Deze schemaversie is up-to-date en bevat alle nieuwste functies. De schemadatum is niet gewijzigd omdat er sinds de introductie geen wijzigingen zijn doorgevoerd.
    - **inhoudVersie**: hiermee geeft u de versie van de sjabloon op (zoals 1.0.0.0). U elke waarde voor dit element bieden. Gebruik deze waarde om belangrijke wijzigingen in uw sjabloon vast te leggen. Bij het implementeren van resources met behulp van de sjabloon kan deze waarde worden gebruikt om ervoor te zorgen dat de juiste sjabloon wordt gebruikt.
    - **bronnen:** bevat de resources die u wilt implementeren of bijwerken. Momenteel is het leeg, maar u voegt later resources toe.

1. Sla het bestand op.

Gefeliciteerd, je hebt je eerste sjabloon gemaakt.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Als u wilt beginnen te werken met Azure PowerShell/Azure CLI, meldt u zich aan met uw Azure-referenties.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
az login
```

---
## <a name="create-resource-group"></a>Een resourcegroep maken

Wanneer u een sjabloon implementeert, geeft u een resourcegroep op die de bronnen bevat. Maak de resourcegroep met Azure CLI of Azure PowerShell voordat u de opdracht Implementatie uitvoert. Selecteer de tabbladen in de volgende codesectie om te kiezen tussen Azure PowerShell en Azure CLI. De CLI-voorbeelden in dit artikel zijn geschreven voor de Bash-shell.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name myResourceGroup `
  -Location "Central US"
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
az group create \
  --name myResourceGroup \
  --location "Central US"
```

---

## <a name="deploy-template"></a>Sjabloon implementeren

Als u de sjabloon wilt implementeren, gebruikt u Azure CLI of Azure PowerShell. Gebruik de resourcegroep die u hebt gemaakt. Geef een naam aan de implementatie, zodat u deze gemakkelijk identificeren in de implementatiegeschiedenis. Maak voor het gemak ook een variabele die het pad opslaat naar het sjabloonbestand. Deze variabele maakt het eenvoudiger voor u om de implementatieopdrachten uit te voeren omdat u het pad niet elke keer opnieuw hoeft te typen wanneer u implementeert.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell
$templateFile = "{provide-the-path-to-the-template-file}"
New-AzResourceGroupDeployment `
  -Name blanktemplate `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u deze implementatieopdracht wilt uitvoeren, moet u over de [nieuwste versie](/cli/azure/install-azure-cli) van Azure CLI beschikken.

```azurecli
templateFile="{provide-the-path-to-the-template-file}"
az deployment group create \
  --name blanktemplate \
  --resource-group myResourceGroup \
  --template-file $templateFile
```

---

De opdracht Implementatie retourneert resultaten. Zoek `ProvisioningState` naar om te zien of de implementatie is geslaagd.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

![PowerShell-status voor het inrichten van powerShell-implementatie](./media/template-tutorial-create-first-template/resource-manager-deployment-provisioningstate.png)

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

![Azure CLI-implementatieinrichtingsstatus](./media/template-tutorial-create-first-template/azure-cli-provisioning-state.png)

---

## <a name="verify-deployment"></a>Implementatie verifiëren

U de implementatie verifiëren door de brongroep te verkennen vanuit de Azure-portal.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Selecteer **Resourcegroepen**in het linkermenu .

1. Selecteer de resourcegroep die in de laatste procedure wordt geïmplementeerd. De standaardnaam is **myResourceGroup**. U ziet geen resource die wordt geïmplementeerd binnen de resourcegroep.

1. Let op rechtsboven in het overzicht, de status van de implementatie wordt weergegeven. Selecteer **1 Geslaagd**.

   ![Implementatiestatus weergeven](./media/template-tutorial-create-first-template/deployment-status.png)

1. U ziet een geschiedenis van implementatie voor de resourcegroep. Selecteer **lege sjabloon**.

   ![Implementatie selecteren](./media/template-tutorial-create-first-template/select-from-deployment-history.png)

1. U ziet een samenvatting van de implementatie. In dit geval is er niet veel te zien omdat er geen resources zijn geïmplementeerd. Later in deze serie u het misschien nuttig vinden om het overzicht in de implementatiegeschiedenis te bekijken. Opmerking aan de linkerkant u ingangen, uitgangen en de sjabloon die tijdens de implementatie wordt gebruikt, weergeven.

   ![Implementatieoverzicht weergeven](./media/template-tutorial-create-first-template/view-deployment-summary.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u doorgaat naar de volgende zelfstudie, hoeft u de brongroep niet te verwijderen.

Als u nu stopt, u de brongroep verwijderen.

1. Selecteer **resourcegroep** in de linkermenu in de Azure-portal.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep.
4. Selecteer **Brongroep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

U hebt een eenvoudige sjabloon gemaakt die u wilt implementeren in Azure. In de volgende zelfstudie voegt u een opslagaccount toe aan de sjabloon en implementeert u deze in uw brongroep.

> [!div class="nextstepaction"]
> [Bron toevoegen](template-tutorial-add-resource.md)
