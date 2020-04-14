---
title: Sjabloon maken - Visual Studio Code
description: Gebruik Visual Studio Code en de Azure Resource Manager-extensie voor hulpprogramma's om te werken met Resource Manager-sjablonen.
author: mumian
ms.date: 04/13/2020
ms.topic: quickstart
ms.author: jgao
ms.openlocfilehash: 96e57146fb6bb17cbb8bb5975371e07b66f3ec8b
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255087"
---
# <a name="quickstart-create-arm-templates-by-using-visual-studio-code"></a>Snelstart: ARM-sjablonen maken met Visual Studio Code

Meer informatie over het gebruik van Visual Studio-code en de Azure Resource Manager Tools-extensie om ARM-sjablonen (Azure Resource Manager) te maken en te bewerken. U ARM-sjablonen maken in Visual Studio Code zonder de extensie, maar de extensie biedt opties voor automatisch aanvullen die de ontwikkeling van sjablonen vereenvoudigen. Zie overzicht van de implementatie van sjablonen voor het implementeren en beheren van de concepten die zijn gekoppeld aan het implementeren en beheren van uw [Azure-oplossingen.](overview.md)

In deze quickstart implementeert u een opslagaccount:

![Resource Manager-sjabloon start snel het visual studiocodediagram](./media/quickstart-create-templates-use-visual-studio-code/resource-manager-template-quickstart-vscode-diagram.png)

Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u dit artikel wilt voltooien, hebt u het volgende nodig:

- [Visual Studio Code](https://code.visualstudio.com/).
- Extensie voor Azure Resource Manager-hulpprogramma's. Gebruik de volgende stappen om deze te installeren:

    1. Open Visual Studio Code.
    2. Druk op **Ctrl+Shift+X** om het deelvenster Extensies te openen
    3. Zoek **Azure Resource Manager-hulpprogramma’s** en selecteer **Installeren**.
    4. Selecteer **Opnieuw laden** om de installatie van de extensie te voltooien.

## <a name="open-a-quickstart-template"></a>Een snelstartsjabloon openen

In plaats van een sjabloon helemaal opnieuw te maken, opent u een sjabloon in [Azure-snelstartsjablonen](https://azure.microsoft.com/resources/templates/). Azure Quickstart-sjablonen is een opslagplaats voor ARM-sjablonen.

De in deze snelstart gebruikte sjabloon wordt [Create a standard storage account](https://azure.microsoft.com/resources/templates/101-storage-account-create/) (Standaardopslagaccount maken) genoemd. De sjabloon definieert een Azure Storage-accountresource.

1. Selecteer **Bestand**>**openen bestand**in Visual Studio-code .
2. Plak de volgende URL in **Bestandsnaam**:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

3. Selecteer **Openen** om het bestand te openen.
4. Selecteer **Bestand**>**opslaan als** om het bestand op te slaan als **azuredeploy.json** op uw lokale computer.

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

    Als u de code hebt gekopieerd en geplakt in Visual Studio Code, probeert u het **waardeelement** opnieuw te typen om de IntelliSense-mogelijkheid van de extensie Hulpmiddelen voor Resourcebeheer te ervaren.

    ![Visual Studio Code-intelliSense van Resource Manager-sjabloon](./media/quickstart-create-templates-use-visual-studio-code/resource-manager-templates-visual-studio-code-intellisense.png)

2. Selecteer **Bestand**>**opslaan** om het bestand op te slaan.

## <a name="deploy-the-template"></a>De sjabloon implementeren

Er bestaan meerdere methoden voor het implementeren van sjablonen. Azure Cloud Shell wordt gebruikt in deze quickstart. De Cloud Shell ondersteunt zowel Azure CLI als Azure PowerShell. Gebruik de tabkiezer om te kiezen tussen CLI en PowerShell.

1. Aanmelden bij de [Azure Cloud Shell](https://shell.azure.com)

2. Kies uw gewenste omgeving door **PowerShell** of **Bash**(CLI) in de linkerbovenhoek te selecteren.  U moet de shell opnieuw starten wanneer u overschakelt.

    # <a name="cli"></a>[CLI](#tab/CLI)

    ![Azure-portal Cloud Shell CLI](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-choose-cli.png)

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ![Azure-portal Cloud Shell PowerShell](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-choose-powershell.png)

    ---

3. Selecteer **Upload/download files** en selecteer **Uploaden**.

    # <a name="cli"></a>[CLI](#tab/CLI)

    ![Uploadbestand azure-portal Cloud Shell](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-upload-file.png)

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ![Uploadbestand azure-portal Cloud Shell](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-upload-file-powershell.png)

    ---

    Selecteer het bestand dat u in de vorige sectie hebt opgeslagen. De standaardnaam is **azuredeploy.json**. Het sjabloonbestand moet toegankelijk zijn vanuit de shell.

    U kunt eventueel de **ls**-opdracht en de **cat**-opdracht uitvoeren om te controleren of het bestand is geüpload.

    # <a name="cli"></a>[CLI](#tab/CLI)

    ![Azure portal Cloud Shell-lijstbestand](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-list-file.png)

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ![Azure portal Cloud Shell-lijstbestand](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-list-file-powershell.png)

    ---
4. Voer vanuit de Cloud Shell de volgende opdrachten uit. Selecteer het tabblad om de PowerShell-code of de CLI-code weer te geven. Geef een projectnaam op die wordt gebruikt om een naam van de resourcegroep te genereren.  De naam van de resourcegroep is de projectnaam **met rg** toegevoegd.

    # <a name="cli"></a>[CLI](#tab/CLI)

    ```azurecli
    echo "Enter a project name that is used to generate resource group name:" &&
    read projectName &&
    echo "Enter the location (i.e. centralus):" &&
    read location &&
    resourceGroupName="${projectName}rg" &&
    az group create --name $resourceGroupName --location "$location" &&
    az deployment group create --resource-group $resourceGroupName --template-file "$HOME/azuredeploy.json" &&
    echo "Press [ENTER] to continue ..."
    ```

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json"
    Write-Host "Press [ENTER] to continue ..."
    ```

    ---

    Werk de naam van de sjabloon bij als u het bestand met een andere naam dan **azuredeploy.json** opslaat.

    In de volgende schermafbeelding ziet u een voorbeeldimplementatie:

    # <a name="cli"></a>[CLI](#tab/CLI)

    ![Azure portal Cloud Shell-implementatiesjabloon](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-deploy-template.png)

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ![Azure portal Cloud Shell-implementatiesjabloon](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-deploy-template-powershell.png)

    ---

    De naam van het opslagaccount en de URL van de opslag in de uitvoersectie zijn gemarkeerd op de schermafbeelding. U hebt de naam van het opslagaccount nodig in de volgende stap.

5. Voer de volgende CLI- of PowerShell-opdracht uit om het nieuwe opslagaccount weer te geven:

    # <a name="cli"></a>[CLI](#tab/CLI)

    ```azurecli
    echo "Enter the Resource Group name:" &&
    read resourceGroupName &&
    echo "Enter the Storage Account name:" &&
    read storageAccountName &&
    az storage account show --resource-group $resourceGroupName --name $storageAccountName &&
    echo "Press [ENTER] to continue ..."
    ```

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ```azurepowershell
    $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
    $storageAccountName = Read-Host -Prompt "Enter the Storage Account name"
    Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName
    Write-Host "Press [ENTER] to continue ..."
    ```

    ---

Zie voor meer informatie over het gebruik van Azure storage-accounts [Snelstart: blobs uploaden, downloaden, en noteren met behulp van de Azure Portal](../../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="clean-up-resources"></a>Resources opschonen

Schoon de geïmplementeerd Azure-resources, wanneer u deze niet meer nodig hebt, op door de resourcegroep te verwijderen.

1. Selecteer **resourcegroep** in de linkermenu in de Azure-portal.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep. De naam van de resourcegroep is de projectnaam **met rg** toegevoegd. U ziet een bron voor opslagaccount in de resourcegroep.
4. Selecteer **Brongroep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

De focus van deze snelstartgids ligt op het gebruik van Visual Studio Code om een bestaande sjabloon van de Azure-snelstartsjablonen te bewerken. U hebt ook geleerd hoe u de sjabloon implementeert met CLI of PowerShell vanuit de Azure Cloud Shell. De sjablonen van de Azure-snelstartsjablonen voldoen mogelijk niet volledig aan uw behoeften. Zie onze nieuwe beginnerszelfstudiereeks voor meer informatie over sjabloonontwikkeling:

> [!div class="nextstepaction"]
> [Zelfstudies voor beginners](./template-tutorial-create-first-template.md)
