---
title: Zelfstudie - Een gekoppelde sjabloon implementeren
description: Meer informatie over het implementeren van een gekoppelde sjabloon
ms.date: 03/13/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 70a09315b0947f41e7602e630460cb3e674a7bf8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80082242"
---
# <a name="tutorial-deploy-a-linked-template"></a>Zelfstudie: Een gekoppelde sjabloon implementeren

In de [vorige zelfstudies](./deployment-tutorial-local-template.md)hebt u geleerd hoe u een sjabloon implementeert die is opgeslagen in uw lokale computer. Als u complexe oplossingen wilt implementeren, u een sjabloon in veel sjablonen opsplitsen en deze sjablonen implementeren via een hoofdsjabloon. In deze zelfstudie leert u hoe u een hoofdsjabloon implementeert die de verwijzing naar een gekoppelde sjabloon bevat. Wanneer de hoofdsjabloon wordt geïmplementeerd, wordt de implementatie van de gekoppelde sjabloon geactiveerd. U leert ook hoe u de gekoppelde sjabloon opslaan en beveiligen met Behulp van SAS-token. Het duurt ongeveer **12 minuten** om te voltooien.

## <a name="prerequisites"></a>Vereisten

We raden u aan de vorige zelfstudie te voltooien, maar deze is niet vereist.

## <a name="review-template"></a>Sjabloon bekijken

In de vorige zelfstudies implementeert u een sjabloon waarmee een opslagaccount, app-serviceplan en web-app zijn gemaakt. De gebruikte sjabloon was:

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/local-template/azuredeploy.json":::

## <a name="create-a-linked-template"></a>Een gekoppelde sjabloon maken

U de bron van het opslagaccount scheiden in een gekoppelde sjabloon:

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/linkedStorageAccount.json":::

De volgende sjabloon is de hoofdsjabloon.  Het gemarkeerde object **Microsoft.Resources/implementaties** laat zien hoe u een gekoppelde sjabloon aanroept. De gekoppelde sjabloon kan niet worden opgeslagen als een lokaal bestand of een bestand dat alleen beschikbaar is op uw lokale netwerk. U alleen een URI-waarde opgeven die *http* of *https*bevat. Resourcemanager moet toegang hebben tot de sjabloon. Een optie is om uw gekoppelde sjabloon in een opslagaccount te plaatsen en de URI voor dat item te gebruiken. De URI wordt doorgegeven aan een sjabloon met behulp van een parameter. Zie de gemarkeerde parameterdefinitie.

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/azuredeploy.json" highlight="27-32,40-58":::

Sla een kopie van de hoofdsjabloon op uw lokale computer op met de .json-extensie, bijvoorbeeld azuredeploy.json. U hoeft geen kopie van de gekoppelde sjabloon op te slaan.  De gekoppelde sjabloon wordt gekopieerd van een GitHub-opslagplaats naar een opslagaccount.

## <a name="store-the-linked-template"></a>De gekoppelde sjabloon opslaan

Met het volgende PowerShell-script wordt een opslagaccount gemaakt, wordt een container gemaakt en wordt de gekoppelde sjabloon van een GitHub-opslagplaats naar de container gekopieerd. Een kopie van de gekoppelde sjabloon wordt opgeslagen in [GitHub.](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json)

Selecteer **Try-it** om de Cloud Shell te openen, selecteer **Kopiëren** om het PowerShell-script te kopiëren en klik met de rechtermuisknop op het deelvenster shell om het script te plakken:

> [!IMPORTANT]
> De namen van het opslagaccount moeten tussen de 3 en 24 tekens lang zijn en alleen getallen en kleine letters gebruiken. De naam moet uniek zijn. In de sjabloon is de naam van het opslagaccount de projectnaam met 'store' toegevoegd en moet de projectnaam tussen 3 en 11 tekens liggen. De projectnaam moet dus voldoen aan de vereisten voor de naam van het opslagaccount en heeft minder dan 11 tekens.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"

$resourceGroupName = $projectName + "rg"
$storageAccountName = $projectName + "store"
$containerName = "templates" # The name of the Blob container to be created.

$linkedTemplateURL = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json" # A completed linked template used in this tutorial.
$fileName = "linkedStorageAccount.json" # A file name used for downloading and uploading the linked template.

# Download the template
Invoke-WebRequest -Uri $linkedTemplateURL -OutFile "$home/$fileName"

# Create a resource group
New-AzResourceGroup -Name $resourceGroupName -Location $location

# Create a storage account
$storageAccount = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -Location $location `
    -SkuName "Standard_LRS"

$context = $storageAccount.Context

# Create a container
New-AzStorageContainer -Name $containerName -Context $context -Permission Container

# Upload the template
Set-AzStorageBlobContent `
    -Container $containerName `
    -File "$home/$fileName" `
    -Blob $fileName `
    -Context $context

Write-Host "Press [ENTER] to continue ..."
```

## <a name="deploy-template"></a>Sjabloon implementeren

Als u een privésjabloon wilt implementeren in een opslagaccount, genereert u een SAS-token en neemt u deze op in de URI voor de sjabloon. Stel de vervaldatum in om voldoende tijd te hebben om de implementatie te voltooien. De blob met de sjabloon is alleen toegankelijk voor de eigenaar van het account. Wanneer u echter een SAS-token voor de blob maakt, is de blob toegankelijk voor iedereen met die URI. Als een andere gebruiker de URI onderschept, heeft die gebruiker toegang tot de sjabloon. Een SAS-token is een goede manier om de toegang tot uw sjablonen te beperken, maar u moet gevoelige gegevens zoals wachtwoorden niet rechtstreeks in de sjabloon opnemen.

Zie [Resourcegroep maken](./deployment-tutorial-local-template.md#create-resource-group)als u de resourcegroep niet hebt gemaakt.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell

$projectName = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$templateFile = Read-Host -Prompt "Enter the main template file and path"

$resourceGroupName="${projectName}rg"
$storageAccountName="${projectName}store"
$containerName = "templates"
$fileName = "linkedStorageAccount.json" # A file name used for downloading and uploading the linked template.

$key = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName).Value[0]
$context = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $key

# Generate a SAS token
$linkedTemplateUri = New-AzStorageBlobSASToken `
    -Context $context `
    -Container $containerName `
    -Blob $fileName `
    -Permission r `
    -ExpiryTime (Get-Date).AddHours(2.0) `
    -FullUri

# Deploy the template
New-AzResourceGroupDeployment `
  -Name DeployLinkedTemplate `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile $templateFile `
  -projectName $projectName `
  -linkedTemplateUri $linkedTemplateUri `
  -verbose
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli

echo "Enter a project name that is used to generate resource names:"
read projectName
echo "Enter the main template file:"
read templateFile

resourceGroupName="${projectName}rg"
storageAccountName="${projectName}store"
containerName="templates"
fileName="linkedStorageAccount.json"

key=$(az storage account keys list -g $resourceGroupName -n $storageAccountName --query [0].value -o tsv)

linkedTemplateUri=$(az storage blob generate-sas \
  --account-name $storageAccountName \
  --account-key $key \
  --container-name $containerName \
  --name $fileName \
  --permissions r \
  --expiry `date -u -d "120 minutes" '+%Y-%m-%dT%H:%MZ'` \
  --full-uri)

linkedTemplateUri=$(echo $linkedTemplateUri | sed 's/"//g')
az deployment group create \
  --name DeployLinkedTemplate \
  --resource-group $resourceGroupName \
  --template-file $templateFile \
  --parameters projectName=$projectName linkedTemplateUri=$linkedTemplateUri \
  --verbose
```

---

## <a name="clean-up-resources"></a>Resources opschonen

Ruim de resources op die u hebt geïmplementeerd door de resourcegroep te verwijderen.

1. Selecteer **resourcegroep** in de linkermenu in de Azure-portal.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep.
4. Selecteer **Brongroep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

U hebt geleerd hoe u een gekoppelde sjabloon implementeert. In de volgende zelfstudie leert u hoe u een DevOp-pijplijn maakt om een sjabloon te implementeren.

> [!div class="nextstepaction"]
> [Een pijplijn maken](./deployment-tutorial-pipeline.md)
