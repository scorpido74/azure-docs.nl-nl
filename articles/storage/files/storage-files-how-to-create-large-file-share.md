---
title: Grote bestandsshares inschakelen en maken - Azure-bestanden
description: In dit artikel leert u hoe u grote bestandsshares in- en inschakelen en maken.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: bd7726d2bbf2830d18d78b5f0b0d7202b734124d
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537675"
---
# <a name="enable-and-create-large-file-shares"></a>Grote bestandsshares inschakelen en maken

Wanneer u grote bestandsshares inschakelt op uw opslagaccount, kunnen uw bestandsshares worden opgeschaald tot 100 TiB. U deze schaling inschakelen op uw bestaande opslagaccounts voor uw bestaande bestandsshares.

## <a name="prerequisites"></a>Vereisten

- Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.
- Als u de Azure CLI wilt gebruiken, [installeert u de nieuwste versie.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- Als u Azure PowerShell wilt gebruiken, [installeert u de nieuwste versie.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0)

## <a name="restrictions"></a>Beperkingen

Op dit moment u alleen lokaal redundante opslag (LRS) of zoneredundante opslag (ZRS) gebruiken op accounts met grote bestandsshare-enabled. U geen gebruik maken van geo-zoneredundante opslag (GZRS), georedundante opslag (GRS) of georedundante opslag voor leestoegang (RA-GRS).
Het inschakelen van grote bestandsshares op een account is een onomkeerbaar proces. Nadat u het hebt ingeschakeld, u uw account niet converteren naar GZRS, GRS of RA-GRS.

## <a name="create-a-new-storage-account"></a>Een nieuw opslagaccount maken

### <a name="portal"></a>Portal

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer **alle services**in de Azure-portal . 
1. Voer in de lijst met bronnen **opslagaccounts**in . Terwijl u typt, worden de lijstfilters op basis van uw invoer weergegeven. Selecteer **Opslagaccounts**.
1. Selecteer **Toevoegen**in het venster **Opslagaccounts** dat wordt weergegeven .
1. Selecteer het abonnement dat u gebruikt om het opslagaccount te maken.
1. Selecteer **Nieuwe maken** onder het veld **Resourcegroep**. Voer een naam in voor uw nieuwe resourcegroep.

    ![Schermafbeelding van het maken van een resourcegroep in de portal](media/storage-files-how-to-create-large-file-share/create-large-file-share.png)

1. Voer vervolgens een naam in voor het opslagaccount. De naam moet uniek zijn in Azure. De naam moet ook 3 tot 24 tekens in lengte, en het kan alleen nummers en kleine letters.
1. Selecteer een locatie voor uw opslagaccount.
1. Stel de replicatie in op **lokaal redundante opslag** of **zoneredundante opslag**.
1. Laat deze velden achter bij hun standaardwaarden:

   |Veld  |Waarde  |
   |---------|---------|
   |Implementatiemodel     |Resource Manager         |
   |Prestaties     |Standard         |
   |Soort account     |StorageV2 (general-purpose v2)         |
   |Toegangslaag     |Warm         |

1. Selecteer **Geavanceerd**en selecteer vervolgens de optie **ingeschakeld** rechts van **grote bestandsshares**.
1. Selecteer **Beoordelen en maken** om uw opslagaccountinstellingen te bekijken en het account te maken.

    ![Schermafbeelding met de optieknop 'ingeschakeld' op een nieuw opslagaccount in de Azure-portal](media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png)

1. Selecteer **Maken**.

### <a name="cli"></a>CLI

Installeer eerst [de nieuwste versie van de Azure CLI,](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) zodat u grote bestandsshares inschakelen.

Als u een opslagaccount wilt maken waarbij grote bestandsshares zijn ingeschakeld, gebruikt u de volgende opdracht. Vervang `<yourStorageAccountName>` `<yourResourceGroup>`, `<yourDesiredRegion>` en door uw gegevens.

```azurecli-interactive
## This command creates a large file share–enabled account. It will not support GZRS, GRS, or RA-GRS.
az storage account create --name <yourStorageAccountName> -g <yourResourceGroup> -l <yourDesiredRegion> --sku Standard_LRS --kind StorageV2 --enable-large-file-share
```

### <a name="powershell"></a>PowerShell

Installeer eerst [de nieuwste versie van PowerShell,](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0) zodat u grote bestandsshares inschakelen.

Als u een opslagaccount wilt maken waarbij grote bestandsshares zijn ingeschakeld, gebruikt u de volgende opdracht. Vervang `<yourStorageAccountName>` `<yourResourceGroup>`, `<yourDesiredRegion>` en door uw gegevens.

```powershell
## This command creates a large file share–enabled account. It will not support GZRS, GRS, or RA-GRS.
New-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -Location <yourDesiredRegion> -SkuName Standard_LRS -EnableLargeFileShare;
```

## <a name="enable-large-files-shares-on-an-existing-account"></a>Grote bestanden aandelen inschakelen op een bestaande account

U ook grote bestandsshares inschakelen op uw bestaande accounts. Als u grote bestandsshares inschakelt, u niet converteren naar GZRS, GRS of RA-GRS. Het inschakelen van grote bestandsshares is onomkeerbaar op deze opslagaccount.

### <a name="portal"></a>Portal

1. Open de [Azure-portal](https://portal.azure.com)en ga naar het opslagaccount waar u grote bestandsshares wilt inschakelen.
1. Open het opslagaccount en selecteer **Configuratie**.
1. Selecteer **Ingeschakeld** op **grote bestandsshares**en selecteer **Opslaan**.
1. Selecteer **Overzicht** en selecteer **Vernieuwen**.

![De optie ingeschakeld optie knop selecteren op een bestaand opslagaccount in de Azure-portal](media/storage-files-how-to-create-large-file-share/enable-large-file-shares-on-existing.png)

U hebt nu grote bestandsshares ingeschakeld op uw opslagaccount. Vervolgens moet u het quotum van bestaande aandelen bijwerken om te profiteren van de toegenomen capaciteit en schaal.

Als u het foutbericht 'Grote bestandsshares zijn nog niet beschikbaar voor het account' ontvangt, zit uw regio mogelijk midden in het voltooien van de implementatie. Neem contact op met ondersteuning als u dringend grote bestandsshares nodig hebt.

### <a name="cli"></a>CLI

Als u grote bestandsshares op uw bestaande account wilt inschakelen, gebruikt u de volgende opdracht. Vervang `<yourStorageAccountName>` `<yourResourceGroup>` en met uw gegevens.

```azurecli-interactive
az storage account update --name <yourStorageAccountName> -g <yourResourceGroup> --enable-large-file-share
```

### <a name="powershell"></a>PowerShell

Als u grote bestandsshares op uw bestaande account wilt inschakelen, gebruikt u de volgende opdracht. Vervang `<yourStorageAccountName>` `<yourResourceGroup>` en met uw gegevens.

```powershell
Set-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -EnableLargeFileShare
```

## <a name="create-a-large-file-share"></a>Een grote bestandsshare maken

Nadat u grote bestandsshares op uw opslagaccount hebt ingeschakeld, u bestandsshares maken in dat account met hogere quota. 

### <a name="portal"></a>Portal

Het maken van een grote bestandsshare is bijna identiek aan het maken van een standaard bestandsshare. Het belangrijkste verschil is dat u een quotum tot 100 TiB instellen.

1. Selecteer **Bestandsshares**in uw opslagaccount .
1. Selecteer ** + bestandsshare**.
1. Voer een naam in voor uw bestandsshare. U ook de gewenste quotumgrootte instellen, tot 100 TiB. Selecteer vervolgens **Maken**. 

![De gebruikersinterface van de Azure-portal met de vakken Naam en Quotum](media/storage-files-how-to-create-large-file-share/large-file-shares-create-share.png)

### <a name="cli"></a>CLI

Als u een grote bestandsshare wilt maken, gebruikt u de volgende opdracht. Vervang `<yourStorageAccountName>` `<yourStorageAccountKey>`, `<yourFileShareName>` en door uw gegevens.

```azurecli-interactive
az storage share create --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName>
```

### <a name="powershell"></a>PowerShell

Als u een grote bestandsshare wilt maken, gebruikt u de volgende opdracht. Vervang `<YourStorageAccountName>` `<YourStorageAccountKey>`, `<YourStorageAccountFileShareName>` en door uw gegevens.

```powershell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
New-AzStorageShare -Name $shareName -Context $ctx
```

## <a name="expand-existing-file-shares"></a>Bestaande bestandsshares uitbreiden

Nadat u grote bestandsshares op uw opslagaccount hebt ingeschakeld, u ook bestaande bestandsshares in dat account uitbreiden naar het hogere quotum. 

### <a name="portal"></a>Portal

1. Selecteer **Bestandsshares**in uw opslagaccount .
1. Klik met de rechtermuisknop op het delen van bestanden en selecteer **Vervolgens Quota**.
1. Voer de gewenste nieuwe maat in en selecteer **OK**.

![De Gebruikersinterface van azureportalmet quotum van bestaande bestandsshares](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

### <a name="cli"></a>CLI

Als u het quotum wilt instellen op de maximale grootte, gebruikt u de volgende opdracht. Vervang `<yourStorageAccountName>` `<yourStorageAccountKey>`, `<yourFileShareName>` en door uw gegevens.

```azurecli-interactive
az storage share update --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName> --quota 102400
```

### <a name="powershell"></a>PowerShell

Als u het quotum wilt instellen op de maximale grootte, gebruikt u de volgende opdracht. Vervang `<YourStorageAccountName>` `<YourStorageAccountKey>`, `<YourStorageAccountFileShareName>` en door uw gegevens.

```powershell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
# update quota
Set-AzStorageShareQuota -ShareName $shareName -Context $ctx -Quota 102400
```

## <a name="next-steps"></a>Volgende stappen

* [Een bestandsshare verbinden en koppelen op Windows](storage-how-to-use-files-windows.md)
* [Een bestandsshare verbinden en monteren op Linux](storage-how-to-use-files-linux.md)
* [Een bestandsshare verbinden en monteren op macOS](storage-how-to-use-files-mac.md)
