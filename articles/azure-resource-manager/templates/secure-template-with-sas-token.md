---
title: Sjabloon veilig implementeren met SAS-token
description: Resources implementeren in Azure met een Azure Resource Manager-sjabloon die wordt beschermd door een SAS-token. Toont Azure PowerShell en Azure CLI.
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: 42eaae316d4fd0575102323933f849a3058228a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156392"
---
# <a name="deploy-private-arm-template-with-sas-token"></a>Private ARM-sjabloon implementeren met SAS-token

Wanneer uw Arm-sjabloon (Azure Resource Manager) zich in een opslagaccount bevindt, u de toegang tot de sjabloon beperken om te voorkomen dat deze openbaar wordt gemaakt. U krijgt toegang tot een beveiligde sjabloon door een SAS-token (Shared Access Signature) voor de sjabloon te maken en dat token tijdens de implementatie te verstrekken. In dit artikel wordt uitgelegd hoe u Azure PowerShell of Azure CLI gebruiken om een sjabloon met een SAS-token te implementeren.

## <a name="create-storage-account-with-secured-container"></a>Opslagaccount maken met beveiligde container

Met het volgende script wordt een opslagaccount en een container gemaakt waarbij de openbare toegang is uitgeschakeld.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

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

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

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

## <a name="upload-template-to-storage-account"></a>Sjabloon uploaden naar opslagaccount

Nu u uw sjabloon uploaden naar het opslagaccount. Geef het pad op naar de sjabloon die u wilt gebruiken.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell-interactive
Set-AzStorageBlobContent `
  -Container templates `
  -File c:\Templates\azuredeploy.json
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
az storage blob upload \
    --container-name templates \
    --file azuredeploy.json \
    --name azuredeploy.json \
    --connection-string $connection
```

---

## <a name="provide-sas-token-during-deployment"></a>SAS-token verstrekken tijdens implementatie

Als u een privésjabloon wilt implementeren in een opslagaccount, genereert u een SAS-token en neemt u deze op in de URI voor de sjabloon. Stel de vervaldatum in om voldoende tijd te hebben om de implementatie te voltooien.

> [!IMPORTANT]
> De blob met de sjabloon is alleen toegankelijk voor de eigenaar van het account. Wanneer u echter een SAS-token voor de blob maakt, is de blob toegankelijk voor iedereen met die URI. Als een andere gebruiker de URI onderschept, heeft die gebruiker toegang tot de sjabloon. Een SAS-token is een goede manier om de toegang tot uw sjablonen te beperken, maar u moet gevoelige gegevens zoals wachtwoorden niet rechtstreeks in de sjabloon opnemen.
>

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

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

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

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
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri $url?$token
```

---

Zie [Gekoppelde sjablonen gebruiken met Azure Resource Manager](linked-templates.md)voor een voorbeeld van het gebruik van een SAS-token met gekoppelde sjablonen.


## <a name="next-steps"></a>Volgende stappen
* Zie [Resources implementeren met ARM-sjablonen en Azure PowerShell](deploy-powershell.md)voor een inleiding tot het implementeren van sjablonen.
* Zie [Sjablonen ontwerpen](template-syntax.md#parameters)als u parameters in sjabloon wilt definiëren.
