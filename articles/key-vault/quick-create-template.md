---
title: Azure Quickstart - Een Azure-sleutelkluis en een geheim maken met azure resourcebeheersjabloon | Microsoft Documenten
description: Snel beginnen met het weergeven van het maken van Azure-sleutelkluizen en geheimen toevoegen aan de kluizen met behulp van azure resource manager-sjabloon.
services: key-vault
author: mumian
manager: dougeby
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc,subject-armqs
ms.date: 02/27/2020
ms.author: jgao
ms.openlocfilehash: 015ae2e8e36d4a563138051bce33f5d283bde789
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78297916"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-resource-manager-template"></a>Snelstart: een geheim instellen en ophalen uit Azure Key Vault met behulp van resourcebeheersjabloon

[Azure Key Vault](./key-vault-overview.md) is een cloudservice die een veilige winkel biedt voor geheimen, zoals sleutels, wachtwoorden, certificaten en andere geheimen. Deze quickstart richt zich op het proces van het implementeren van een Resource Manager-sjabloon om een sleutelkluis en een geheim te maken.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u dit artikel wilt voltooien, hebt u het volgende nodig:

* Uw Azure AD-gebruikersobject-id is in de sjabloon nodig om machtigingen te kunnen configureren. Met de volgende procedure wordt de object-id (GUID) opgehaald.

    1. Voer de volgende opdracht Azure PowerShell of Azure CLI uit door **Try it te**selecteren en plak het script vervolgens in het shell-deelvenster. Als u het script wilt plakken, klikt u met de rechtermuisknop op de shell en selecteert u **Plakken**.

        # <a name="cli"></a>[CLI](#tab/CLI)
        ```azurecli-interactive
        echo "Enter your email address that is used to sign in to Azure:" &&
        read upn &&
        az ad user show --id $upn --query "objectId" &&
        echo "Press [ENTER] to continue ..."
        ```

        # <a name="powershell"></a>[Powershell](#tab/PowerShell)
        ```azurepowershell-interactive
        $upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
        (Get-AzADUser -UserPrincipalName $upn).Id
        Write-Host "Press [ENTER] to continue..."
        ```

        ---

    2. Noteer de object-id. Je hebt het nodig in het volgende deel van deze quickstart.

## <a name="create-a-vault-and-a-secret"></a>Maak een kluis en een geheim

### <a name="review-the-template"></a>De sjabloon bekijken

De sjabloon die in deze quickstart wordt gebruikt, is afkomstig van [Azure Quickstart-sjablonen.](https://azure.microsoft.com/resources/templates/101-key-vault-create/)

:::code language="json" source="~/quickstart-templates/101-key-vault-create/azuredeploy.json" range="1-150" highlight="107-148":::

In de sjabloon worden twee Azure-resources gedefinieerd:

* [**Microsoft.KeyVault/vaults:**](/azure/templates/microsoft.keyvault/vaults)maak een Azure-sleutelkluis.
* [**Microsoft.KeyVault/vaults/secrets:**](/azure/templates/microsoft.keyvault/vaults/secrets)maak een sleutelkluisgeheim.

Meer Azure Key Vault sjabloon voorbeelden zijn [hier](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault)te vinden.

### <a name="deploy-the-template"></a>De sjabloon implementeren

1. Selecteer de volgende afbeelding om u aan te melden bij Azure en een sjabloon te openen. Met de sjabloon worden een sleutelkluis en een geheim gemaakt.

    [![Implementeren naar Azure](../media/template-deployments/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-key-vault-create%2Fazuredeploy.json)

2. Typ of selecteer de volgende waarden.

    ![Resource Manager-sjabloon, Key Vault-integratie, implementatieportal](./media/quick-create-template/create-key-vault-using-template-portal.png)

    Tenzij dit is opgegeven, gebruikt u de standaardwaarde om de sleutelkluis en een geheim te maken.

    * **Abonnement**: selecteer een Azure-abonnement.
    * **Resourcegroep**: selecteer **Nieuw maken,** voer een unieke naam in voor de resourcegroep en klik op **OK**.
    * **Locatie**: selecteer een locatie.  Bijvoorbeeld **VS - centraal**.
    * **Key Vault Name**: voer een naam in voor de sleutelkluis, die wereldwijd uniek moet zijn binnen de naamruimte .vault.azure.net. U hebt de naam in de volgende sectie nodig wanneer u de implementatie valideert.
    * **Tenant-id:** de sjabloonfunctie haalt automatisch uw tenant-id op.  Wijzig de standaardwaarde niet.
    * **Advertentie-id:** voer de azure AD-gebruikersobject-id in die u hebt opgehaald uit [Vereisten](#prerequisites).
    * **Geheime naam:** voer een naam in voor het geheim dat u opslaat in de sleutelkluis.  Bijvoorbeeld, **adminpassword**.
    * **Geheime waarde:** voer de geheime waarde in.  Als u een wachtwoord opslaat, wordt aanbevolen het gegenereerde wachtwoord te gebruiken dat u hebt gemaakt in Vereisten.
    * **Ik ga akkoord met de bovenstaande voorwaarden**: selecteer dit.
3. Selecteer **Aankoop**. Nadat de sleutelkluis is geïmplementeerd, ontvangt u een melding:

    ![Resource Manager-sjabloon, Key Vault-integratie, melding van portal implementeren](./media/quick-create-template/resource-manager-template-portal-deployment-notification.png)

De Azure-portal wordt gebruikt om de sjabloon te implementeren. Naast de Azure-portal u ook de Azure PowerShell-, Azure CLI- en REST-API gebruiken. Zie [Sjablonen implementeren](../azure-resource-manager/templates/deploy-powershell.md)voor meer informatie over andere implementatiemethoden.

## <a name="review-deployed-resources"></a>Geïmplementeerde resources controleren

U de Azure-portal gebruiken om de sleutelkluis en het geheim te controleren, of het volgende Azure CLI- of Azure PowerShell-script gebruiken om het gemaakte geheim weer te geven.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter your key vault name:" &&
read keyVaultName &&
az keyvault secret list --vault-name $keyVaultName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[Powershell](#tab/PowerShell)

```azurepowershell-interactive
$keyVaultName = Read-Host -Prompt "Enter your key vault name"
Get-AzKeyVaultSecret -vaultName $keyVaultName
Write-Host "Press [ENTER] to continue..."
```

---

De uitvoer lijkt op:

# <a name="cli"></a>[CLI](#tab/CLI)

![Resource Manager-sjabloon, Key Vault-integratie, validatie-uitvoer van portalen implementeren](./media/quick-create-template/resource-manager-template-portal-deployment-cli-output.png)

# <a name="powershell"></a>[Powershell](#tab/PowerShell)

![Resource Manager-sjabloon, Key Vault-integratie, validatie-uitvoer van portalen implementeren](./media/quick-create-template/resource-manager-template-portal-deployment-powershell-output.png)

---
## <a name="clean-up-resources"></a>Resources opschonen

Andere Key Vault-snelstarts en zelfstudies bouwen voort op deze snelstart. Als u van plan bent om verder te gaan met volgende snelstarts en zelfstudies, kunt u deze resources intact laten.
Als u die niet meer nodig hebt, verwijdert u de resourcegroep. Hierdoor worden ook de sleutelkluis en de gerelateerde resources verwijderd. Ga als lid van de brongroep met Azure CLI of Azure PowerShell:

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[Powershell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u een sleutelkluis en een geheim gemaakt met behulp van een Azure Resource Manager-sjabloon en de implementatie gevalideerd. Ga verder naar de onderstaande artikelen voor meer informatie over Key Vault en Azure Resource Manager.

- Lees een [overzicht van Azure Key Vault](key-vault-overview.md)
- Meer informatie over [Azure Resource Manager](../azure-resource-manager/management/overview.md)
- Meer informatie over [sleutels, geheimen en certificaten](about-keys-secrets-and-certificates.md)
- Aanbevolen [procedures voor Azure Key Vault bekijken](key-vault-best-practices.md)
