---
title: Sjabloonverwijzing gebruiken
description: Gebruik de Azure Resource Manager sjabloon verwijzing om een sjabloon te maken.
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: 12990238455046d837b175318225bb4f3d317706
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82185044"
---
# <a name="tutorial-utilize-the-resource-manager-template-reference"></a>Zelf studie: de Resource Manager-sjabloon verwijzing gebruiken

Meer informatie over het vinden van sjabloon schema-informatie en het maken van Azure Resource Manager-sjablonen (ARM).

In deze zelfstudie gebruikt u een basissjabloon uit Azure-snelstartsjablonen. U kunt de sjabloon aanpassen met behulp van de referentie documentatie voor sjablonen.

![Resource Manager-sjabloon referentie implementeren opslag account](./media/template-tutorial-use-template-reference/resource-manager-template-tutorial-deploy-storage-account.png)

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Een snelstartsjabloon openen
> * Inzicht in de sjabloon
> * De sjabloonverwijzing zoeken
> * De sjabloon bewerken
> * De sjabloon implementeren

Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u dit artikel wilt voltooien, hebt u het volgende nodig:

* Visual Studio Code met de extensie Resource Manager Tools. Zie [Visual Studio code gebruiken om arm-sjablonen te maken](use-vs-code-to-create-template.md).

## <a name="open-a-quickstart-template"></a>Een snelstartsjabloon openen

[Quick](https://azure.microsoft.com/resources/templates/) start-sjablonen van Azure is een opslag plaats voor arm-sjablonen. In plaats van een sjabloon helemaal vanaf de basis te maken, kunt u een voorbeeldsjabloon zoeken en aanpassen. De in deze snelstart gebruikte sjabloon wordt [Create a standard storage account](https://azure.microsoft.com/resources/templates/101-storage-account-create/) (Standaardopslagaccount maken) genoemd. De sjabloon definieert een Azure Storage-accountresource.

1. Selecteer **bestand**>**openen**in Visual Studio code.
1. Plak de volgende URL in **Bestandsnaam**:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

1. Selecteer **Openen** om het bestand te openen.
1. Selecteer **bestand**>**Opslaan als** om het bestand op te slaan als **azuredeploy. json** op de lokale computer.

## <a name="understand-the-schema"></a>Informatie over het schema

1. Vouw vanuit VS Code de sjabloon samen naar het hoofdniveau. U hebt de eenvoudigste structuur met de volgende elementen:

    ![Eenvoudigste structuur voor Resource Manager-sjabloon](./media/template-tutorial-use-template-reference/resource-manager-template-simplest-structure.png)

    * **$schema**: geef de locatie van het JSON-schemabestand op dat de versie van de sjabloontaal beschrijft.
    * **contentVersion**: geef een waarde op voor dit element om belangrijke wijzigingen in de sjabloon vast te leggen.
    * **parameters**: geef de waarden op die worden geleverd wanneer de implementatie wordt uitgevoerd om resource-implementatie aan te passen.
    * **variables**: geef de waarden op die worden gebruikt als JSON-fragmenten in de sjabloon voor het vereenvoudigen van sjabloontaalexpressies.
    * **resources**: geef de resourcetypen op die worden geïmplementeerd of bijgewerkt in een resourcegroep.
    * **outputs**: geef de waarden op die worden geretourneerd na de implementatie.

1. Vouw **Resources** uit. Er is een `Microsoft.Storage/storageAccounts`-resource gedefinieerd. De SKU-naam gebruikt een parameter waarde.  De para meter wordt **storageAccountType**genoemd.

    ![Definitie opslagaccount Resource Manager-sjabloon](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resource.png)

1. Vouw **para meters** uit om te zien hoe **storageAccountType** is gedefinieerd. De para meter heeft vier toegestane waarden. U vindt de andere toegestane waarden en vervolgens wijzigt u de parameter definitie.

    ![Resource Manager-sjabloon opslag account resources sku's](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resources-skus-old.png)

## <a name="find-the-template-reference"></a>De sjabloonverwijzing zoeken

1. Blader naar de [Naslag informatie voor Azure-sjablonen](https://docs.microsoft.com/azure/templates/).
1. In het vak **filteren op titel** voert u **opslag accounts**in en selecteert u de eerste **opslag accounts** onder **verwijzing > opslag**.

    ![Resource Manager-sjabloon verwijzing opslagaccount](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts.png)

    Een resource provider heeft doorgaans diverse API-versies:

    ![Resource Manager-sjabloon referentie opslag account-versies](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-versions.png)

1. Selecteer **alle resources** onder **opslag** in het linkerdeel venster. Op deze pagina worden de resource typen en versies van de opslag Resource provider vermeld. Het is raadzaam om de nieuwste API-versies te gebruiken voor de resource typen die in de sjabloon zijn gedefinieerd.

    ![Resource Manager-sjabloon referentie opslag account typen](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-types-versions.png)

1. Selecteer de nieuwste versie van het resource type **Storage account** . De meest recente versie is **2019-06-01** wanneer dit artikel is geschreven. Zorg ervoor dat deze versie overeenkomt met de versie die wordt gebruikt voor de bron van het opslag account in uw sjabloon. Als u de API-versie bijwerkt, controleert u of de resource definitie overeenkomt met de sjabloon verwijzing.

1. Op deze pagina vindt u de details van het resource type Storage account.  Het bevat bijvoorbeeld de toegestane waarden voor het **SKU** -object. Er zijn meer sku's dan wat wordt weer gegeven in de Quick Start-sjabloon die u eerder hebt geopend. U kunt de Quick Start-sjabloon aanpassen zodat alle beschik bare opslag typen worden toegevoegd.

    ![Resource Manager-sjabloon verwijzing naar opslag account-sku's](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-skus.png)

## <a name="edit-the-template"></a>De sjabloon bewerken

Voeg vanuit Visual Studio code de typen extra opslag accounts toe, zoals wordt weer gegeven in de volgende scherm afbeelding:

![Resources van Resource Manager-opslag account](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resources-skus.png)

## <a name="deploy-the-template"></a>De sjabloon implementeren

1. Meld u aan bij de [Azure Cloud shell](https://shell.azure.com)

1. Kies uw voorkeurs omgeving door **Power shell** of **bash** (voor CLI) in de linkerbovenhoek te selecteren.  U moet de shell opnieuw starten wanneer u overschakelt.

    ![Azure Portal Cloud Shell bestand uploaden](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. Selecteer **Upload/download files** en selecteer **Uploaden**. Zie de vorige schermafbeelding. Selecteer het bestand dat u in de vorige sectie hebt opgeslagen. Nadat het bestand is geüpload, kunt u de opdracht **ls** gebruiken en de **Cat** -opdracht om te controleren of het bestand is geüpload.

1. Voer de volgende opdrachten uit op de Cloud Shell. Selecteer het tabblad om de PowerShell-code of de CLI-code weer te geven.

    # <a name="cli"></a>[CLI](#tab/CLI)

    ```azurecli
    echo "Enter a project name that is used to generate resource group name:" &&
    read projectName &&
    echo "Enter the location (i.e. centralus):" &&
    read location &&
    resourceGroupName="${projectName}rg" &&
    az group create --name $resourceGroupName --location "$location" &&
    az deployment group create --resource-group $resourceGroupName --template-file "$HOME/azuredeploy.json" --parameters storageAccountType='Standard_RAGRS'
    ```

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json" -storageAccountType "Standard_RAGRS"
    ```

    ---

 Wanneer u de sjabloon implementeert, geeft u de para meter **storageAccountType** op met een toegevoegde waarde, bijvoorbeeld **Standard_RAGRS**. De implementatie mislukt als u de oorspronkelijke Quick Start-sjabloon gebruikt omdat **Standard_RAGRS** geen toegestane waarde is.

## <a name="clean-up-resources"></a>Resources opschonen

Schoon de geïmplementeerd Azure-resources, wanneer u deze niet meer nodig hebt, op door de resourcegroep te verwijderen.

1. Selecteer in de Azure Portal **resource groep** in het menu links.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep.  U ziet in totaal zes resources in de resourcegroep.
4. Selecteer **resource groep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u met sjabloonverwijzing een bestaande sjabloon kunt aanpassen. Voor informatie over hoe u meerdere exemplaren van een opslagaccount maakt, zie:

> [!div class="nextstepaction"]
> [Meerdere exemplaren maken](./template-tutorial-create-multiple-instances.md)
