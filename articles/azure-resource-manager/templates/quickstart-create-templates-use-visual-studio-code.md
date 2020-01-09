---
title: Sjabloon maken-Visual Studio code
description: Gebruik Visual Studio Code en de Azure Resource Manager-extensie voor hulpprogramma's om te werken met Resource Manager-sjablonen.
author: mumian
ms.date: 03/04/2019
ms.topic: quickstart
ms.author: jgao
ms.openlocfilehash: 9a829b0c84c397f297539cdb04b3ad027a18c834
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75473617"
---
# <a name="quickstart-create-azure-resource-manager-templates-by-using-visual-studio-code"></a>Snelstart: Azure Resource Manager-sjablonen maken met Visual Studio Code

Leer hoe u Visual Studio Code en de Azure Resource Manager Tools-extensie gebruikt om Azure Resource Manager-sjablonen te maken en te bewerken. U kunt Resource Manager-sjablonen maken in Visual Studio Code zonder de extensie, maar de extensie biedt opties voor automatisch aanvullen die het ontwikkelen van sjablonen eenvoudiger maken. Zie [overzicht van sjabloon implementatie](overview.md)voor meer informatie over de concepten van het implementeren en beheren van uw Azure-oplossingen.

In deze Quick Start implementeert u een opslag account:

![Resource Manager-sjabloon Quick Start Visual Studio-code diagram](./media/quickstart-create-templates-use-visual-studio-code/resource-manager-template-quickstart-vscode-diagram.png)

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u dit artikel wilt voltooien, hebt u het volgende nodig:

- [Visual Studio Code](https://code.visualstudio.com/).
- Extensie voor Azure Resource Manager-hulpprogramma's. Gebruik de volgende stappen om deze te installeren:

    1. Open Visual Studio Code.
    2. Druk op **Ctrl+Shift+X** om het deelvenster Extensies te openen
    3. Zoek **Azure Resource Manager-hulpprogramma’s** en selecteer **Installeren**.
    4. Selecteer **Opnieuw laden** om de installatie van de extensie te voltooien.

## <a name="open-a-quickstart-template"></a>Een snelstartsjabloon openen

In plaats van een sjabloon helemaal opnieuw te maken, opent u een sjabloon in [Azure-snelstartsjablonen](https://azure.microsoft.com/resources/templates/). Azure-snelstartsjablonen is een opslagplaats voor Resource Manager-sjablonen.

De in deze snelstart gebruikte sjabloon wordt [Create a standard storage account](https://azure.microsoft.com/resources/templates/101-storage-account-create/) (Standaardopslagaccount maken) genoemd. De sjabloon definieert een Azure Storage-accountresource.

1. Selecteer in Visual Studio Code **Bestand**>**Bestand openen**.
2. Plak de volgende URL in **Bestandsnaam**:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

3. Selecteer **Openen** om het bestand te openen.
4. Selecteer **Bestand**>**Opslaan als** om het bestand op uw lokale computer op te slaan als **azuredeploy.json**.

## <a name="edit-the-template"></a>De sjabloon bewerken

Als u wilt ervaren hoe u een sjabloon met behulp van Visual Studio Code bewerkt, voegt u een extra element toe aan de sectie `outputs` om de opslag-URI weer te geven.

1. Voeg één extra uitvoer toe aan de geëxporteerde sjabloon:

    ```json
    "storageUri": {
      "type": "string",
      "value": "[reference(variables('storageAccountName')).primaryEndpoints.blob]"
    }
    ```

    Als u klaar bent, ziet de uitvoersectie er als volgt uit:

    ```json
    "outputs": {
      "storageAccountName": {
        "type": "string",
        "value": "[variables('storageAccountName')]"
      },
      "storageUri": {
        "type": "string",
        "value": "[reference(variables('storageAccountName')).primaryEndpoints.blob]"
      }
    }
    ```

    Als u de code in Visual Studio Code hebt geplakt, kunt u het element **value** opnieuw typen om een idee te krijgen van de intelliSense-mogelijkheden van de extensie voor Resource Manager-hulpprogramma's.

    ![Visual Studio Code-intelliSense van Resource Manager-sjabloon](./media/quickstart-create-templates-use-visual-studio-code/resource-manager-templates-visual-studio-code-intellisense.png)

2. Selecteer **Bestand**>**Opslaan** om het bestand op te slaan.

## <a name="deploy-the-template"></a>De sjabloon implementeren

Er bestaan meerdere methoden voor het implementeren van sjablonen. Azure Cloud shell wordt gebruikt in deze Quick Start. Cloud shell ondersteunt zowel Azure CLI als Azure PowerShell. Gebruik de tabblad kiezer om te kiezen tussen CLI en Power shell.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. Meld u aan bij [Azure Cloud Shell](https://shell.azure.com)

2. Kies uw voorkeurs omgeving door **Power shell** of **bash**(CLI) te selecteren in de linkerbovenhoek.  U moet de shell opnieuw starten wanneer u overschakelt.

    # <a name="clitabcli"></a>[CLI](#tab/CLI)

    ![Cloud Shell CLI in Azure Portal](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-choose-cli.png)

    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)

    ![Azure Portal Cloud shell Power shell](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-choose-powershell.png)

    ---

3. Selecteer **Upload/download files** en selecteer **Uploaden**.

    # <a name="clitabcli"></a>[CLI](#tab/CLI)

    ![Bestand uploaden in Cloud Shell in Azure Portal](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-upload-file.png)

    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)

    ![Bestand uploaden in Cloud Shell in Azure Portal](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-upload-file-powershell.png)

    ---

    Selecteer het bestand dat u in de vorige sectie hebt opgeslagen. De standaardnaam is **azuredeploy.json**. Het sjabloonbestand moet toegankelijk zijn vanuit de shell.

    U kunt eventueel de **ls**-opdracht en de **cat**-opdracht uitvoeren om te controleren of het bestand is geüpload.

    # <a name="clitabcli"></a>[CLI](#tab/CLI)

    ![Cloud Shell-lijstbestand in Azure Portal](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-list-file.png)

    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)

    ![Cloud Shell-lijstbestand in Azure Portal](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-list-file-powershell.png)

    ---
4. Voer vanuit Cloud Shell de volgende opdrachten uit. Selecteer het tabblad om de PowerShell-code of de CLI-code weer te geven.

    # <a name="clitabcli"></a>[CLI](#tab/CLI)
    ```azurecli
    echo "Enter the Resource Group name:" &&
    read resourceGroupName &&
    echo "Enter the location (i.e. centralus):" &&
    read location &&
    az group create --name $resourceGroupName --location "$location" &&
    az group deployment create --resource-group $resourceGroupName --template-file "$HOME/azuredeploy.json"
    ```

    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)

    ```azurepowershell
    $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json"
    ```

    ---

    Werk de naam van de sjabloon bij als u het bestand met een andere naam dan **azuredeploy.json** opslaat.

    In de volgende schermafbeelding ziet u een voorbeeldimplementatie:

    # <a name="clitabcli"></a>[CLI](#tab/CLI)

    ![Sjabloon implementeren in Cloud Shell in Azure Portal](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-deploy-template.png)

    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)

    ![Sjabloon implementeren in Cloud Shell in Azure Portal](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-deploy-template-powershell.png)

    ---

    De naam van het opslagaccount en de URL van de opslag in de uitvoersectie zijn gemarkeerd op de schermafbeelding. U hebt de naam van het opslagaccount nodig in de volgende stap.

5. Voer de volgende CLI- of PowerShell-opdracht uit om het nieuwe opslagaccount weer te geven:

    # <a name="clitabcli"></a>[CLI](#tab/CLI)
    ```azurecli
    echo "Enter the Resource Group name:" &&
    read resourceGroupName &&
    echo "Enter the Storage Account name:" &&
    read storageAccountName &&
    az storage account show --resource-group $resourceGroupName --name $storageAccountName
    ```

    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)

    ```azurepowershell
    $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
    $storageAccountName = Read-Host -Prompt "Enter the Storage Account name"
    Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName
    ```

    ---

Zie voor meer informatie over het gebruik van Azure storage-accounts [Snelstart: blobs uploaden, downloaden, en noteren met behulp van de Azure Portal](../../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="clean-up-resources"></a>Resources opschonen

Schoon de geïmplementeerd Azure-resources, wanneer u deze niet meer nodig hebt, op door de resourcegroep te verwijderen.

1. Selecteer **Resourcegroep** in het linkermenu van Azure Portal.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep.  U ziet in totaal zes resources in de resourcegroep.
4. Selecteer **Resourcegroep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

De focus van deze snelstartgids ligt op het gebruik van Visual Studio Code om een bestaande sjabloon van de Azure-snelstartsjablonen te bewerken. U hebt ook geleerd hoe u de sjabloon kunt implementeren met CLI of PowerShell vanuit Azure Cloud Shell. De sjablonen van de Azure-snelstartsjablonen voldoen mogelijk niet volledig aan uw behoeften. Zie voor meer informatie over het ontwikkelen van sjablonen onze nieuwe zelf studie reeks voor beginners:

> [!div class="nextstepaction"]
> [Zelf studies voor beginners](./template-tutorial-create-first-template.md)
