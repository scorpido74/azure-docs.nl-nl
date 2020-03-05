---
title: 'Azure-Snelstartgids: een Azure-sleutel kluis en een geheim maken met behulp van Azure Resource Manager sjabloon | Microsoft Docs'
description: Quick Start laat zien hoe u Azure-sleutel kluizen maakt en geheimen aan de kluizen kunt toevoegen met behulp van Azure Resource Manager sjabloon.
services: key-vault
author: mumian
manager: dougeby
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc
ms.date: 02/27/2020
ms.author: jgao
ms.openlocfilehash: ce9be980ecdee74f783342f79c0f4b424b88e523
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2020
ms.locfileid: "78267764"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-resource-manager-template"></a>Snelstartgids: een geheim instellen en ophalen uit Azure Key Vault met Resource Manager-sjabloon

[Azure Key Vault](./key-vault-overview.md) is een Cloud service die een veilig archief voor geheimen levert, zoals sleutels, wacht woorden, certificaten en andere geheimen. Deze Snelstartgids is gericht op het proces van het implementeren van een resource manager-sjabloon om een sleutel kluis en een geheim te maken.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u dit artikel wilt voltooien, hebt u het volgende nodig:

* Uw Azure AD-gebruikersobject-id is in de sjabloon nodig om machtigingen te kunnen configureren. Met de volgende procedure wordt de object-id (GUID) opgehaald.

    1. Voer de volgende Azure PowerShell of de Azure **cli-opdracht uit door het te**selecteren en vervolgens het script in het deel venster shell te plakken. Als u het script wilt plakken, klikt u met de rechter muisknop op de shell en selecteert u vervolgens **Plakken**.

        # <a name="cli"></a>[CLI](#tab/CLI)
        ```azurecli-interactive
        echo "Enter your email address that is used to sign in to Azure:" &&
        read upn &&
        az ad user show --id $upn --query "objectId" &&
        echo "Press [ENTER] to continue ..."
        ```

        # <a name="powershell"></a>[PowerShell](#tab/PowerShell)
        ```azurepowershell-interactive
        $upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
        (Get-AzADUser -UserPrincipalName $upn).Id
        Write-Host "Press [ENTER] to continue..."
        ```

        ---

    2. Noteer de object-id. U hebt deze nodig in de volgende sectie van deze Snelstartgids.

## <a name="create-a-vault-and-a-secret"></a>Een kluis en een geheim maken

### <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze Quick Start wordt gebruikt, is afkomstig uit [Azure Quick](https://azure.microsoft.com/resources/templates/101-key-vault-create/)start-sjablonen.

:::code language="json" source="~/quickstart-templates/101-key-vault-create/azuredeploy.json" range="1-150" highlight="107-148":::

Er worden twee Azure-resources gedefinieerd in de sjabloon:

* [**Micro soft. sleutel kluis/kluizen**](/azure/templates/microsoft.keyvault/vaults): een Azure-sleutel kluis maken.
* [**Micro soft. sleutel kluis/kluizen/geheimen**](/azure/templates/microsoft.keyvault/vaults/secrets): Maak een geheim voor sleutel kluis.

Meer Azure Key Vault sjabloon voorbeelden vindt u [hier](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault).

### <a name="deploy-the-template"></a>De sjabloon implementeren

1. Selecteer de volgende afbeelding om u aan te melden bij Azure en een sjabloon te openen. Met de sjabloon worden een sleutelkluis en een geheim gemaakt.

    [![Implementeren in Azure](../media/template-deployments/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-key-vault-create%2Fazuredeploy.json)

2. Typ of selecteer de volgende waarden.

    ![Resource Manager-sjabloon, integratie van Key Vault, portal implementeren](./media/quick-create-template/create-key-vault-using-template-portal.png)

    Tenzij deze is opgegeven, gebruikt u de standaard waarde om de sleutel kluis en een geheim te maken.

    * **Abonnement**: selecteer een Azure-abonnement.
    * **Resource groep**: Selecteer **nieuwe maken**, voer een unieke naam in voor de resource groep en klik vervolgens op **OK**.
    * **Locatie**: selecteer een locatie.  Bijvoorbeeld **VS - centraal**.
    * **Key Vault naam**: Voer een naam in voor de sleutel kluis die wereld wijd uniek moet zijn binnen de. Vault.Azure.net-naam ruimte. U hebt de naam in de volgende sectie nodig wanneer u de implementatie valideert.
    * **Tenant-id**: de sjabloon functie haalt automatisch uw Tenant-id op.  Wijzig de standaard waarde niet.
    * **AD-gebruikers-id**: Voer uw Azure AD-gebruikers object-id in die u hebt opgehaald uit de [vereisten](#prerequisites).
    * **Geheime naam**: Voer een naam in voor het geheim dat u opslaat in de sleutel kluis.  Bijvoorbeeld **AdminPassword**.
    * **Geheime waarde**: Voer de geheime waarde in.  Als u een wacht woord opslaat, wordt u aangeraden het gegenereerde wacht woord te gebruiken dat u hebt gemaakt in vereisten.
    * **Ik ga akkoord met de bovenstaande voorwaarden**: selecteer dit.
3. Selecteer **Aankoop**. Nadat de sleutel kluis is geïmplementeerd, ontvangt u een melding:

    ![Resource Manager-sjabloon, integratie van Key Vault, portal-melding implementeren](./media/quick-create-template/resource-manager-template-portal-deployment-notification.png)

De Azure Portal wordt gebruikt voor het implementeren van de sjabloon. Naast de Azure Portal, kunt u ook de Azure PowerShell, Azure CLI en REST API gebruiken. Zie voor meer informatie over andere implementatie methoden [sjablonen implementeren](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Geïmplementeerde resources controleren

U kunt de Azure Portal gebruiken om de sleutel kluis en het geheim te controleren, of het volgende Azure CLI-of Azure PowerShell-script gebruiken om het gemaakte geheim weer te geven.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter your key vault name:" &&
read keyVaultName &&
az keyvault secret list --vault-name $keyVaultName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$keyVaultName = Read-Host -Prompt "Enter your key vault name"
Get-AzKeyVaultSecret -vaultName $keyVaultName
Write-Host "Press [ENTER] to continue..."
```

---

De uitvoer ziet er ongeveer als volgt uit:

# <a name="cli"></a>[CLI](#tab/CLI)

![Resource Manager-sjabloon, integratie van Key Vault, portal validatie-uitvoer implementeren](./media/quick-create-template/resource-manager-template-portal-deployment-cli-output.png)

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

![Resource Manager-sjabloon, integratie van Key Vault, portal validatie-uitvoer implementeren](./media/quick-create-template/resource-manager-template-portal-deployment-powershell-output.png)

---
## <a name="clean-up-resources"></a>Resources opschonen

Andere Key Vault-snelstarts en zelfstudies bouwen voort op deze snelstart. Als u van plan bent om verder te gaan met volgende snelstarts en zelfstudies, kunt u deze resources intact laten.
Als u die niet meer nodig hebt, verwijdert u de resourcegroep. Hierdoor worden ook de sleutelkluis en de gerelateerde resources verwijderd. De resource groep verwijderen met behulp van Azure CLI of Azure PowerShell:

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een sleutel kluis en een geheim gemaakt met behulp van een Azure Resource Manager sjabloon en de implementatie gevalideerd. Ga verder met de volgende artikelen voor meer informatie over Key Vault en Azure Resource Manager.

- Een [overzicht van Azure Key Vault](key-vault-overview.md) lezen
- Meer informatie over [Azure Resource Manager](../azure-resource-manager/management/overview.md)
- Meer informatie over [sleutels, geheimen en certificaten](about-keys-secrets-and-certificates.md)
- [Azure Key Vault aanbevolen procedures](key-vault-best-practices.md) controleren
