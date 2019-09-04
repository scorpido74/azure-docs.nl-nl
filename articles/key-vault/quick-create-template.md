---
title: 'Azure-Snelstartgids: een Azure-sleutel kluis en een geheim maken met behulp van Azure Resource Manager sjabloon | Microsoft Docs'
description: Quick Start laat zien hoe u Azure-sleutel kluizen maakt en geheimen aan de kluizen kunt toevoegen met behulp van Azure Resource Manager sjabloon.
services: key-vault
author: mumian
manager: dougeby
tags: azure-resource-manager
ms.service: key-vault
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/03/2019
ms.author: jgao
ms.openlocfilehash: 13ac8b9f6d82cc8b0060f87237ef3140cb245379
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259209"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-resource-manager-template"></a>Quickstart: Een geheim instellen en ophalen uit Azure Key Vault met behulp van Resource Manager-sjabloon

[Azure Key Vault](./key-vault-overview.md) is een Cloud service die een veilig archief voor geheimen levert, zoals sleutels, wacht woorden, certificaten en andere geheimen. Deze Snelstartgids is gericht op het proces van het implementeren van een resource manager-sjabloon om een sleutel kluis en een geheim te maken. Zie de [documentatie van Resource Manager](/azure/azure-resource-manager/) en de [sjabloon verwijzing](/azure/templates/microsoft.keyvault/allversions)voor meer informatie over het ontwikkelen van Resource Manager-sjablonen.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u dit artikel wilt voltooien, hebt u het volgende nodig:

* Uw Azure AD-gebruikersobject-id is in de sjabloon nodig om machtigingen te kunnen configureren. Met de volgende procedure wordt de object-id (GUID) opgehaald.

    1. Voer de volgende Azure PowerShell of de Azure **cli-opdracht uit door het te**selecteren en vervolgens het script in het deel venster shell te plakken. Als u het script wilt plakken, klikt u met de rechter muisknop op de shell en selecteert u vervolgens **Plakken**. 

        ```azurecli-interactive
        echo "Enter your email address that is used to sign in to Azure:" &&
        read upn &&
        az ad user show --upn-or-object-id $upn --query "objectId" 
        ```

        ```azurepowershell-interactive
        $upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
        (Get-AzADUser -UserPrincipalName $upn).Id
        ```

    2. Noteer de object-id. U hebt deze nodig in de volgende sectie van deze Snelstartgids.

## <a name="create-a-vault-and-a-secret"></a>Een kluis en een geheim maken

De sjabloon die in deze Quick Start wordt gebruikt, is afkomstig uit [Azure Quick](https://azure.microsoft.com/resources/templates/101-key-vault-create/)start-sjablonen. Meer Azure Key Vault sjabloon voorbeelden vindt u [hier](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault).

1. Selecteer de volgende afbeelding om u aan te melden bij Azure en een sjabloon te openen. Met de sjabloon worden een sleutelkluis en een geheim gemaakt.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-key-vault-create%2Fazuredeploy.json"><img src="./media/quick-create-template/deploy-to-azure.png" alt="deploy to azure"/></a>

2. Typ of selecteer de volgende waarden.  

    ![Resource Manager-sjabloon - sleutelkluisintegratie - portalimplementatie](./media/quick-create-template/create-key-vault-using-template-portal.png)

    Tenzij deze is opgegeven, gebruikt u de standaard waarde om de sleutel kluis en een geheim te maken.

    * **Abonnement**: selecteer een Azure-abonnement.
    * **Resource groep**: Selecteer **nieuwe maken**, voer een unieke naam in voor de resource groep en klik vervolgens op **OK**. 
    * **Locatie**: selecteer een locatie.  Bijvoorbeeld **US - centraal**.
    * **Key Vault naam**: Voer een naam in voor de sleutel kluis die wereld wijd uniek moet zijn binnen de. Vault.Azure.net-naam ruimte.  
    * **Tenant-id**: met de sjabloonfunctie wordt uw tenant-id automatisch opgehaald.  Wijzig de standaard waarde niet.
    * **AD-gebruikers-id**: Voer uw Azure AD-gebruikers object-id in die u hebt opgehaald uit de [vereisten](#prerequisites).
    * **Geheime naam**: Voer een naam in voor het geheim dat u opslaat in de sleutel kluis.  Bijvoorbeeld **AdminPassword**.
    * **Geheime waarde**: Voer de geheime waarde in.  Als u een wacht woord opslaat, wordt u aangeraden het gegenereerde wacht woord te gebruiken dat u hebt gemaakt in vereisten.
    * **Ik ga akkoord met de bovenstaande voorwaarden**: Selecteren.
3. Selecteer **Aankoop**.

## <a name="validate-the-deployment"></a>De implementatie valideren

U kunt de Azure Portal gebruiken om de sleutel kluis en het geheim te controleren, of het volgende Azure CLI-of Azure PowerShell-script gebruiken om het gemaakte geheim weer te geven.

```azurecli-interactive
echo "Enter your key vault name:" &&
read keyVaultName &&
az keyvault secret list --vault-name $keyVaultName
```

```azurepowershell-interactive
$keyVaultName = Read-Host -Prompt "Enter your key vault name"
Get-AzKeyVaultSecret -vaultName $keyVaultName
```

## <a name="clean-up-resources"></a>Resources opschonen

Andere Key Vault-snelstarts en zelfstudies bouwen voort op deze snelstart. Als u van plan bent om verder te gaan met volgende snelstarts en zelfstudies, kunt u deze resources intact laten.
Als u die niet meer nodig hebt, verwijdert u de resourcegroep. Hierdoor worden ook de sleutelkluis en de gerelateerde resources verwijderd. De resource groep verwijderen met behulp van Azure CLI of Azure Power shell:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName 
```
```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName 
```

## <a name="next-steps"></a>Volgende stappen

In deze Snelstartgids hebt u een sleutel kluis en een geheim gemaakt met behulp van een Azure Resource Manager-sjabloon en de implementatie gevalideerd. Ga verder met de volgende artikelen voor meer informatie over Key Vault en Azure Resource Manager.

- Een [overzicht van Azure Key Vault](key-vault-overview.md) lezen
- Meer informatie over [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)
- Meer informatie over [sleutels, geheimen en certificaten](about-keys-secrets-and-certificates.md)
- [Azure Key Vault aanbevolen procedures](key-vault-best-practices.md) controleren