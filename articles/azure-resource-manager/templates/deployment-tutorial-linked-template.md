---
title: Zelf studie-een gekoppelde sjabloon implementeren
description: Meer informatie over het implementeren van een gekoppelde sjabloon
ms.date: 03/13/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 177a994450b6ffe5489a8c95c3b484521fd9b77b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80672921"
---
# <a name="tutorial-deploy-a-linked-template"></a>Zelf studie: een gekoppelde sjabloon implementeren

In de [vorige zelf studies](./deployment-tutorial-local-template.md)hebt u geleerd hoe u een sjabloon kunt implementeren die is opgeslagen op de lokale computer. Als u complexe oplossingen wilt implementeren, kunt u een sjabloon in veel sjablonen opsplitsen en deze sjablonen implementeren via een hoofd sjabloon. In deze zelf studie leert u hoe u een hoofd sjabloon implementeert die de verwijzing naar een gekoppelde sjabloon bevat. Wanneer de hoofd sjabloon wordt geïmplementeerd, wordt de implementatie van de gekoppelde sjabloon geactiveerd. U leert ook hoe u de gekoppelde sjabloon kunt opslaan en beveiligen door gebruik te maken van SAS-tokens. Het duurt ongeveer **12 minuten** om te volt ooien.

## <a name="prerequisites"></a>Vereisten

U wordt aangeraden om de vorige zelf studie te volt ooien, maar dit is niet vereist.

## <a name="review-template"></a>Sjabloon controleren

In de vorige zelf studies implementeert u een sjabloon waarmee u een opslag account, App Service plan en web-app maakt. De gebruikte sjabloon is:

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/local-template/azuredeploy.json":::

## <a name="create-a-linked-template"></a>Een gekoppelde sjabloon maken

U kunt de bron van het opslag account in een gekoppelde sjabloon scheiden:

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/linkedStorageAccount.json":::

De volgende sjabloon is de hoofd sjabloon.  Het gemarkeerde **micro soft. resources/Deployments-** object laat zien hoe u een gekoppelde sjabloon aanroept. De gekoppelde sjabloon kan niet worden opgeslagen als een lokaal bestand of een bestand dat alleen beschikbaar is op uw lokale netwerk. U kunt alleen een URI-waarde opgeven die *http* of *https*bevat. Resource Manager moet toegang hebben tot de sjabloon. U kunt ook de gekoppelde sjabloon in een opslag account plaatsen en de URI voor dat item gebruiken. De URI wordt door gegeven aan de sjabloon met behulp van een para meter. Bekijk de definitie van de gemarkeerde para meter.

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/azuredeploy.json" highlight="27-32,40-58":::

Sla een kopie van de hoofd sjabloon op op uw lokale computer met de extensie. json, bijvoorbeeld azuredeploy. json. U hoeft geen kopie van de gekoppelde sjabloon op te slaan.  De gekoppelde sjabloon wordt gekopieerd van een GitHub-opslag plaats naar een opslag account.

## <a name="store-the-linked-template"></a>De gekoppelde sjabloon opslaan

Met het volgende Power shell-script maakt u een opslag account, maakt u een container en kopieert u de gekoppelde sjabloon uit een GitHub-opslag plaats naar de container. Een kopie van de gekoppelde sjabloon wordt opgeslagen in [github](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json).

Selecteer **try-it** om de Cloud shell te openen, selecteer **kopiëren** om het Power shell-script te kopiëren en klik met de rechter muisknop op het deel venster shell om het script te plakken:

> [!IMPORTANT]
> Namen van opslag accounts moeten tussen de 3 en 24 tekens lang zijn en mogen alleen cijfers en kleine letters bevatten. De naam moet uniek zijn. In de sjabloon is de naam van het opslag account de project naam ' Store ' toegevoegd en de project naam moet tussen de 3 en 11 tekens lang zijn. De project naam moet dus voldoen aan de naam vereisten voor het opslag account en mag uit minder dan 11 tekens bestaan.

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

Als u een persoonlijke sjabloon in een opslag account wilt implementeren, genereert u een SAS-token en neemt u deze op in de URI voor de sjabloon. Stel de verloop tijd zo in dat er voldoende tijd is om de implementatie te volt ooien. De blob die de sjabloon bevat, is alleen toegankelijk voor de eigenaar van het account. Wanneer u echter een SAS-token voor de BLOB maakt, is de BLOB toegankelijk voor iedereen met die URI. Als een andere gebruiker de URI onderschept, kan die gebruiker toegang krijgen tot de sjabloon. Een SAS-token is een goede manier om de toegang tot uw sjablonen te beperken, maar u mag geen gevoelige gegevens zoals wacht woorden rechtstreeks in de sjabloon toevoegen.

Als u de resource groep nog niet hebt gemaakt, raadpleegt u [resource groep maken](./deployment-tutorial-local-template.md#create-resource-group).

> [!NOTE]
> In de onderstaande Azure CLI-code is datum parameter-d een ongeldig argument in macOS. MacOS-gebruikers, om 2 uur toe te voegen aan de huidige tijd in de Terminal op macOS, moet u-v + 2H gebruiken.

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

Opschonen van de resources die u hebt geïmplementeerd door de resource groep te verwijderen.

1. Selecteer in de Azure Portal **resource groep** in het menu links.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep.
4. Selecteer **resource groep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

U hebt geleerd hoe u een gekoppelde sjabloon implementeert. In de volgende zelf studie leert u hoe u een DevOp-pijp lijn kunt maken voor het implementeren van een sjabloon.

> [!div class="nextstepaction"]
> [Een pijplijn maken](./deployment-tutorial-pipeline.md)
