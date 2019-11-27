---
title: Grote bestands shares inschakelen en maken-Azure Files
description: In dit artikel leert u hoe u grote bestands shares inschakelt en maakt.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 34b8af56a8f2f108b96ca07fa73f90bb9eb5bf13
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422727"
---
# <a name="how-to-enable-and-create-large-file-shares"></a>Grote bestands shares inschakelen en maken

Oorspronkelijk kunnen standaard bestands shares slechts tot 5 TiB worden geschaald, nu met grote bestands shares kan de schaal tot 100 TiB worden uitgebreid. Premium-bestands shares worden standaard tot 100 TiB geschaald. 

Als u naar 100 TiB wilt schalen met behulp van standaard bestands shares, moet u uw opslag account inschakelen voor het gebruik van grote bestands shares. U kunt een bestaand account inschakelen of een nieuw account maken voor het gebruik van grote bestands shares.

## <a name="prerequisites"></a>Vereisten

- Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.
- Als u van plan bent om Azure CLI te gebruiken, moet u ervoor zorgen dat u [de meest recente versie installeert](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
- Als u Azure PowerShell wilt gebruiken, moet u ervoor zorgen dat u [de meest recente versie installeert](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0).

## <a name="restrictions"></a>Beperkingen

Ondersteuning voor grote bestands shares ingeschakelde accounts ondersteunen LRS of ZRS. Voor nu kunnen grote bestands shares-accounts GZRS, GRS of RA-GRS niet ondersteunen. Het inschakelen van grote bestands shares voor een account is een onomkeerbaar proces, zodra het account is ingeschakeld, kan het niet worden geconverteerd naar GZRS, GRS of RA-GRS.

## <a name="create-a-new-storage-account"></a>Een nieuw opslagaccount maken

### <a name="portal"></a>Portal

Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Selecteer in de Azure-portal de optie **Alle services**. Typ in de lijst met resources **Opslagaccounts**. Als u begint te typen, wordt de lijst gefilterd op basis van uw invoer. Selecteer **Opslagaccounts**.
1. Kies in het venster **Opslagaccounts** dat wordt weergegeven de optie **Toevoegen**.
1. Selecteer het abonnement waarin u het opslagaccount wilt maken.
1. Selecteer **Nieuwe maken** onder het veld **Resourcegroep**. Voer een naam in voor de nieuwe resourcegroep, zoals in de volgende afbeelding wordt weergegeven.

    ![Schermafbeelding van het maken van een resourcegroep in de portal](media/storage-files-how-to-create-large-file-share/create-large-file-share.png)

1. Voer vervolgens een naam in voor het opslagaccount. De naam die u kiest, moet uniek zijn binnen Azure. Verder moet de naam 3 tot 24 tekens lang zijn en mag alleen cijfers en kleine letters bevatten.
1. Selecteer een locatie voor uw opslag account, Controleer of [het een van de regio's voor LF wordt ondersteund](storage-files-planning.md#regional-availability).
1. Stel de replicatie in op **lokaal redundante opslag** of **zone-redundante opslag**.
1. Laat deze velden ingesteld staan op de standaardwaarden:

   |Veld  |Waarde  |
   |---------|---------|
   |Implementatiemodel     |Resource Manager         |
   |Prestaties     |Standard         |
   |Soort account     |StorageV2 (general-purpose v2)         |
   |Toegangslaag     |Warm         |

1. Selecteer **Geavanceerd** en selecteer **ingeschakeld** voor **grote bestands shares**.
1. Selecteer **Beoordelen en maken** om uw opslagaccountinstellingen te bekijken en het account te maken.

    ![large-file-shares-Advanced-Enable. png](media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png)

1. Selecteer **Maken**.


### <a name="cli"></a>CLI

Zorg er eerst voor dat u [de meest recente versie installeert](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), zodat u deze functie kunt inschakelen.

Als u een opslag account wilt maken waarvoor grote bestands shares zijn ingeschakeld, vervangt u `<yourStorageAccountName>`, `<yourResourceGroup>`en `<yourDesiredRegion>` met uw waarden. vervolgens gebruikt u de volgende opdracht:

```azurecli-interactive
## This command creates a large file share enabled account, it will not support GZRS, GRS, or RA-GRS
az storage account create –name <yourStorageAccountName> -g <yourResourceGroup> -l <yourDesiredRegion> –sku Standard_LRS --kind StorageV2 –enable-large-file-share
```

### <a name="powershell"></a>PowerShell

Zorg er eerst voor dat u [de meest recente versie installeert](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0), zodat u deze functie kunt inschakelen.

Als u een opslag account wilt maken waarvoor grote bestands shares zijn ingeschakeld, vervangt u `<yourStorageAccountName>`, `<yourResourceGroup>`en `<yourDesiredRegion>` met uw waarden. vervolgens gebruikt u de volgende opdracht:

```PowerShell
## This command creates a large file share enabled account, it will not support GZRS, GRS, or RA-GRS
New-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -Location <yourDesiredRegion> -SkuName Standard_LRS -EnableLargeFileShare;
```

## <a name="enable-on-existing-account"></a>Inschakelen op bestaande account

### <a name="portal"></a>Portal

U kunt ook grote bestands shares inschakelen voor bestaande accounts. Als u dit doet, kan het account niet meer worden geconverteerd naar GZRS, GRS of RA-GRS. Deze keuze kan niet ongedaan worden gemaakt voor dit account.

1. Open de [Azure Portal](https://portal.azure.com) en navigeer naar het opslag account waarvoor u grote bestands shares wilt inschakelen.
1. Open het opslag account en selecteer **configuratie**.
1. Selecteer **ingeschakeld** op **grote bestands shares**en selecteer vervolgens opslaan.
1. Selecteer **overzicht** en selecteer **vernieuwen**.

![enable-large-file-shares-on-existing. png](media/storage-files-how-to-create-large-file-share/enable-large-file-shares-on-existing.png)

U hebt nu grote bestands shares ingeschakeld voor uw opslag account.

Als u het volgende fout bericht krijgt: "grote bestands shares zijn nog niet beschikbaar voor het account." U kunt even wachten, omdat uw regio waarschijnlijk in het midden van de implementatie wordt voltooid, of als u urgente behoeften hebt, kunt u contact met de ondersteuning maken.

### <a name="cli"></a>CLI

U kunt grote bestands shares inschakelen voor uw bestaande accounts. Als u dit doet, kan het account niet meer worden geconverteerd naar GZRS, GRS of RA-GRS. Deze keuze kan niet ongedaan worden gemaakt voor dit account.

Vervang `<yourStorageAccountName>` en `<yourResourceGroup>` in de volgende opdracht en gebruik deze om grote bestands shares in te scha kelen voor uw bestaande account:

```azurecli-interactive
az storage account update –name <yourStorageAccountName> -g <yourResourceGroup> –enable-large-file-share
```

### <a name="powershell"></a>PowerShell

U kunt grote bestands shares inschakelen voor uw bestaande accounts. Als u dit doet, kan het account niet meer worden geconverteerd naar GZRS, GRS of RA-GRS. Deze keuze kan niet ongedaan worden gemaakt voor dit account.

Vervang `<yourStorageAccountName>` en `<yourResourceGroup>` in de volgende opdracht en gebruik deze om grote bestands shares in te scha kelen voor uw bestaande account:

```PowerShell
Set-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -EnableLargeFileShare
```

## <a name="create-a-large-file-share"></a>Een grote bestands share maken

### <a name="portal"></a>Portal

Het maken van een grote bestands share is bijna identiek aan het maken van een standaard bestands share. Het belangrijkste verschil is dat u een quotum kunt instellen van Maxi maal 100 TiB.

1. Selecteer in uw opslag account **Bestands shares**.
1. Selecteer **+ Bestandsshare**.
1. Voer een naam in voor de bestands share en (optioneel) de gewenste quotum grootte, Maxi maal 100 TiB, en selecteer vervolgens **maken**. 

![large-file-shares-Create-share. png](media/storage-files-how-to-create-large-file-share/large-file-shares-create-share.png)

### <a name="cli"></a>CLI

Zodra u grote bestands shares hebt ingeschakeld in uw opslag account, kunt u bestands shares maken in dat account met hogere quota's. Vervang `<yourStorageAccountName>`, `<yourStorageAccountKey>`en `<yourFileShareName>` in de volgende opdracht met uw waarden. vervolgens kunt u deze gebruiken om een grote bestands share te maken:

```azurecli-interactive
az storage share create --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName>
```

### <a name="powershell"></a>PowerShell

Zodra u grote bestands shares hebt ingeschakeld in uw opslag account, kunt u bestands shares maken in dat account met hogere quota's. Vervang `<YourStorageAccountName>`, `<YourStorageAccountKey>`en `<YourStorageAccountFileShareName>` in de volgende opdracht met uw waarden. vervolgens kunt u deze gebruiken om een grote bestands share te maken:

```PowerShell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
New-AzStorageShare -Name $shareName -Context $ctx
```

## <a name="expand-existing-file-shares"></a>Bestaande bestands shares uitbreiden

### <a name="portal"></a>Portal

Zodra u grote bestands shares hebt ingeschakeld in uw opslag account, kunt u bestaande shares uitbreiden naar het hogere quotum.

1. Selecteer in uw opslag account **Bestands shares**.
1. Klik met de rechter muisknop op de bestands share en selecteer **quotum**.
1. Voer de gewenste grootte in en selecteer **OK**.

![Update-large-file-share-quota. png](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

### <a name="cli"></a>CLI

Zodra u grote bestands shares in uw opslag account hebt ingeschakeld, kunt u de bestaande bestands shares in dat account uitbreiden naar de hogere quota's. Vervang `<yourStorageAccountName>`, `<yourStorageAccountKey>`en `<yourFileShareName>` in de volgende opdracht met uw waarden. vervolgens kunt u de optie gebruiken om het quotum in te stellen op de maximum grootte:

```azurecli-interactive
az storage share update --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName> --quota 102400
```

### <a name="powershell"></a>PowerShell

Zodra u grote bestands shares in uw opslag account hebt ingeschakeld, kunt u de bestaande bestands shares in dat account uitbreiden naar de hogere quota's. Vervang `<YourStorageAccountName>`, `<YourStorageAccountKey>`en `<YourStorageAccountFileShareName>` in de volgende opdracht met uw waarden. vervolgens kunt u de optie gebruiken om het quotum in te stellen op de maximum grootte:

```PowerShell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
# update quota
Set-AzStorageShareQuota -ShareName $shareName -Context $ctx -Quota 102400
```

## <a name="next-steps"></a>Volgende stappen

* [Bestandsshare verbinden en koppelen - Windows](storage-how-to-use-files-windows.md)
* [Bestandsshare verbinden en koppelen - Linux](../storage-how-to-use-files-linux.md)
* [Bestandsshare verbinden en koppelen - Mac OS](storage-how-to-use-files-mac.md)