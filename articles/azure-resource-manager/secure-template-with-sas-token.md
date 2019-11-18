---
title: Sjabloon veilig implementeren met SAS-token
description: Resources implementeren in azure met een Azure Resource Manager sjabloon die wordt beveiligd met een SAS-token. Toont Azure PowerShell en Azure CLI.
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: 8138563211cae12cc38f0e737be49b32204dc243
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150315"
---
# <a name="deploy-private-resource-manager-template-with-sas-token"></a>Persoonlijke Resource Manager-sjablonen met SAS-token implementeren

Als uw sjabloon zich in een opslag account bevindt, kunt u de toegang tot de sjabloon beperken om te voor komen dat deze openbaar wordt weer gegeven. U opent een beveiligde sjabloon door een SAS-token (Shared Access Signature) te maken voor de sjabloon en dat token tijdens de implementatie op te geven. In dit artikel wordt uitgelegd hoe u Azure PowerShell of Azure CLI gebruikt voor het implementeren van een sjabloon met een SAS-token.

## <a name="create-storage-account-with-secured-container"></a>Een opslag account maken met een beveiligde container

Met het volgende script maakt u een opslag account en een container waarvoor open bare toegang is uitgeschakeld.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location "Central US"
New-AzStorageAccount `
  -ResourceGroupName ExampleGroup `
  -Name {your-unique-name} `
  -Type Standard_LRS `
  -Location "Central US"
Set-AzCurrentStorageAccount `
  -ResourceGroupName ExampleGroup `
  -Name {your-unique-name}
New-AzStorageContainer `
  -Name templates `
  -Permission Off
```

# <a name="azure-clitabazure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
az group create \
  --name "ExampleGroup" \
  --location "Central US"
az storage account create \
    --resource-group ExampleGroup \
    --location "Central US" \
    --sku Standard_LRS \
    --kind Storage \
    --name {your-unique-name}
connection=$(az storage account show-connection-string \
    --resource-group ExampleGroup \
    --name {your-unique-name} \
    --query connectionString)
az storage container create \
    --name templates \
    --public-access Off \
    --connection-string $connection
```

---

## <a name="upload-template-to-storage-account"></a>Sjabloon uploaden naar het opslag account

U bent nu klaar om uw sjabloon te uploaden naar het opslag account. Geef het pad op naar de sjabloon die u wilt gebruiken.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Set-AzStorageBlobContent `
  -Container templates `
  -File c:\Templates\azuredeploy.json
```

# <a name="azure-clitabazure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
az storage blob upload \
    --container-name templates \
    --file azuredeploy.json \
    --name azuredeploy.json \
    --connection-string $connection
```

---

## <a name="provide-sas-token-during-deployment"></a>SAS-token opgeven tijdens de implementatie

Als u een persoonlijke sjabloon in een opslag account wilt implementeren, genereert u een SAS-token en neemt u deze op in de URI voor de sjabloon. Stel de verloop tijd zo in dat er voldoende tijd is om de implementatie te volt ooien.

> [!IMPORTANT]
> De blob die de sjabloon bevat, is alleen toegankelijk voor de eigenaar van het account. Wanneer u echter een SAS-token voor de BLOB maakt, is de BLOB toegankelijk voor iedereen met die URI. Als een andere gebruiker de URI onderschept, kan die gebruiker toegang krijgen tot de sjabloon. Een SAS-token is een goede manier om de toegang tot uw sjablonen te beperken, maar u mag geen gevoelige gegevens zoals wacht woorden rechtstreeks in de sjabloon toevoegen.
>

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
# get the URI with the SAS token
$templateuri = New-AzStorageBlobSASToken `
  -Container templates `
  -Blob azuredeploy.json `
  -Permission r `
  -ExpiryTime (Get-Date).AddHours(2.0) -FullUri

# provide URI with SAS token during deployment
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri $templateuri
```

# <a name="azure-clitabazure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group ExampleGroup \
    --name {your-unique-name} \
    --query connectionString)
token=$(az storage blob generate-sas \
    --container-name templates \
    --name azuredeploy.json \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
    --container-name templates \
    --name azuredeploy.json \
    --output tsv \
    --connection-string $connection)
az group deployment create \
  --resource-group ExampleGroup \
  --template-uri $url?$token
```

---

Zie voor een voor beeld van het gebruik van een SAS-token met gekoppelde sjablonen [gekoppelde sjablonen gebruiken met Azure Resource Manager](resource-group-linked-templates.md).


## <a name="next-steps"></a>Volgende stappen
* Zie [resources implementeren met Resource Manager-sjablonen en Azure PowerShell](resource-group-template-deploy.md)voor een inleiding tot het implementeren van sjablonen.
* Zie voor een volledig voorbeeld script dat een sjabloon implementeert, [Resource Manager-sjabloon script implementeren](resource-manager-samples-powershell-deploy.md)
* Zie [ontwerp sjablonen](resource-group-authoring-templates.md#parameters)voor het definiëren van para meters in de sjabloon.
