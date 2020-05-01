---
title: Quick start voor het maken van een Azure Recovery Services kluis met behulp van een Azure Resource Manager sjabloon.
description: In deze Quick Start leert u hoe u een Azure Recovery Services kluis kunt maken met behulp van een Azure Resource Manager sjabloon.
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 04/29/2020
author: davidsmatlak
ms.author: v-dasmat
ms.openlocfilehash: 47c25ebd0fe18d470b04ccbcc85a8638c1ce0346
ms.sourcegitcommit: acc558d79d665c8d6a5f9e1689211da623ded90a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82598386"
---
# <a name="quickstart-create-a-recovery-services-vault-using-a-resource-manager-template"></a>Snelstartgids: een Recovery Services kluis maken met behulp van een resource manager-sjabloon

In deze Quick Start wordt beschreven hoe u een Recovery Services kluis instelt met behulp van een Azure Resource Manager sjabloon. De [Azure site Recovery](site-recovery-overview.md) -service draagt bij aan uw strategie voor bedrijfs continuïteit en herstel na nood gevallen (BCDR), zodat uw zakelijke toepassingen online blijven tijdens geplande en ongeplande uitval. Site Recovery beheert herstel na nood gevallen van on-premises machines en Azure virtual machines (VM), met inbegrip van replicatie, failover en herstel.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als u geen actief Azure-abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) maken voordat u begint.

## <a name="prerequisites"></a>Vereisten

Geen.

## <a name="create-a-recovery-services-vault"></a>Een Recovery Services-kluis maken

### <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze Quick Start wordt gebruikt, is afkomstig uit [Azure Quick](https://azure.microsoft.com/resources/templates/101-recovery-services-vault-create/)start-sjablonen.

:::code language="json" source="~/quickstart-templates/101-recovery-services-vault-create/azuredeploy.json" range="1-66" highlight="41-65":::

Er worden twee Azure-resources gedefinieerd in de sjabloon:

- [Micro soft. Recovery Services-kluizen](/azure/templates/microsoft.recoveryservices/2016-06-01/vaults): maakt de kluis.
- [Micro soft. Recovery Services/kluizen/backupstorageconfig](/rest/api/backup/backupresourcestorageconfigs): Hiermee configureert u de instellingen voor back-upredundantie van de kluis.

De sjabloon bevat optionele para meters voor de back-upconfiguratie van de kluis. De instellingen voor opslag redundantie zijn lokaal redundante opslag (LRS) of geografisch redundante opslag (GRS). Zie [opslag redundantie instellen](../backup/backup-create-rs-vault.md#set-storage-redundancy)voor meer informatie.

Zie [Azure Quick](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Recoveryservices&pageNumber=1&sort=Popular)start-sjablonen voor meer Azure Recovery Services-sjablonen.

### <a name="deploy-the-template"></a>De sjabloon implementeren

Voor het implementeren van de sjabloon zijn het **abonnement**, de **resource groep**en de **kluis naam** vereist.

1. Als u zich wilt aanmelden bij Azure en de sjabloon wilt openen, selecteert u de installatie kopie **implementeren naar Azure** .

   [![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-recovery-services-vault-create%2Fazuredeploy.json)

1. Selecteer of voer de volgende waarden in:

   :::image type="content" source="media/quickstart-create-vault-template/create-vault-template.png" alt-text="Sjabloon voor het maken van een Recovery Services kluis.":::

   - **Abonnement**: Selecteer uw Azure-abonnement.
   - **Resource groep**: Selecteer een bestaande groep of selecteer **nieuwe maken** om een groep toe te voegen.
   - **Locatie**: wordt standaard ingesteld op de locatie van de resource groep en is niet meer beschikbaar nadat een resource groep is geselecteerd.
   - **Kluis naam**: Geef een naam op voor de kluis.
   - **Opslag type wijzigen**: de standaard waarde is **False**. Selecteer **waar** alleen als u het opslag type van de kluis wilt wijzigen.
   - **Kluis opslag type**: de standaard waarde is **GloballyRedundant**. Als het opslag type is ingesteld op **True**, selecteert u **LocallyRedundant**.
   - **Locatie**: de functie `[resourceGroup().location]` wordt standaard ingesteld op de locatie van de resource groep. Als u de locatie wilt wijzigen, voert u een waarde in, bijvoorbeeld **westus**.
   - Schakel het selectie vakje **Ik ga akkoord met de bovenstaande voor waarden in**.

1. Selecteer de knop **aanschaffen** om te beginnen met de implementatie van de kluis. Na een geslaagde implementatie wordt er een melding weer gegeven.

   :::image type="content" source="media/quickstart-create-vault-template/deployment-success.png" alt-text="De kluis implementatie is voltooid.":::

## <a name="validate-the-deployment"></a>De implementatie valideren

Als u wilt bevestigen dat de kluis is gemaakt, gebruikt u Azure CLI of Azure PowerShell.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the resource group name:" &&
read resourceGroupName &&
echo "Enter the vault name:" &&
read vaultName &&
az backup vault show --name $vaultName --resource-group $resourceGroupName &&
az backup vault backup-properties show --name $vaultName --resource-group $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resouceGroupName = Read-Host -Prompt "Enter the resource group name"
$vaultName = Read-Host -Prompt "Enter the vault name"
$vaultBackupConfig = Get-AzRecoveryServicesVault -Name $vaultName
Get-AzRecoveryServicesVault -ResourceGroupName $resouceGroupName -Name $vaultName
Get-AzRecoveryServicesBackupProperty -Vault $vaultBackupConfig
Write-Host "Press [ENTER] to continue..."
```

---

De volgende uitvoer is een fragment van de gegevens van de kluis:

# <a name="cli"></a>[CLI](#tab/CLI)

```Output
"id": "/subscriptions/<Subscription Id>/resourceGroups/myResourceGroup
         /providers/Microsoft.RecoveryServices/vaults/myVault"
"location": "eastus"
"name": "myVault"
"resourceGroup": "myResourceGroup"

"storageModelType": "GeoRedundant"
"storageType": "GeoRedundant"
"type": "Microsoft.RecoveryServices/vaults/backupstorageconfig"
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```Output
Name              : myVault
Type              : Microsoft.RecoveryServices/vaults
Location          : eastus
ResourceGroupName : myResourceGroup
SubscriptionId    : <Subscription Id>

BackupStorageRedundancy
-----------------------
GeoRedundant
```

---

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent om de nieuwe resources te gebruiken, hoeft u geen actie te ondernemen. Als dat niet het geval is, kunt u de resource groep en de kluis die in deze Quick start is gemaakt, verwijderen. Als u de resource groep en de bijbehorende resources wilt verwijderen, gebruikt u Azure CLI of Azure PowerShell.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the resource group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resouceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resouceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een Recovery Services kluis gemaakt. Ga naar het volgende artikel Quick start voor meer informatie over herstel na nood gevallen.

> [!div class="nextstepaction"]
> [Herstel na noodgeval instellen](azure-to-azure-quickstart.md)
