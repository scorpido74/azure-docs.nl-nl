---
title: Een Azure-bestandsshare maken
titleSuffix: Azure Files
description: Een Azure-bestands share maken met behulp van de Azure Portal, Power shell of de Azure CLI.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: ca4904940c7974e83445892b9597a9d3c36b44ee
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75452875"
---
# <a name="create-a-file-share"></a>Een bestandsshare maken
U kunt een share maken in Azure Files met behulp van de [Azure Portal](https://portal.azure.com/), de Azure Storage Power shell-cmdlets, de Azure Storage-client bibliotheken of de Azure Storage rest API. In dit artikel leert u het volgende:
- Een Azure-bestands share maken met behulp van de Azure Portal
- Een Azure-bestands share maken met behulp van Power shell
- Een Azure-bestands share maken met behulp van Azure CLI

## <a name="prerequisites"></a>Vereisten
Als u een Azure-bestands share wilt maken, kunt u een opslag account gebruiken dat al bestaat of [een nieuw Azure-opslag account maken](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Als u een Azure-bestands share wilt maken met Power shell, hebt u de account sleutel en de naam van uw opslag account nodig. U hebt een sleutel voor een opslag account nodig als u Power shell of de CLI wilt gebruiken.

> [!NOTE]
> Zie [grote bestands shares inschakelen](storage-files-how-to-create-large-file-share.md)als u bestands shares wilt maken die groter zijn dan 5 Tib.

## <a name="create-a-file-share-through-the-azure-portal"></a>Een bestandsshare maken via de Azure-portal

1. Ga naar het deel venster **opslag account** op de Azure Portal.
    ![deel venster opslag account](./media/storage-how-to-create-file-share/create-file-share-portal1.png)

1. Selecteer de knop **+ Bestands share** .
    ![de knop bestands share toevoegen](./media/storage-how-to-create-file-share/create-file-share-portal2.png)

1. Voer uw gegevens in voor **naam** en **quotum**.
    ![de naam en het quotum voor de nieuwe bestands share](./media/storage-how-to-create-file-share/create-file-share-portal3.png)

1. Bekijk uw nieuwe bestands share.
    ![de nieuwe bestands share in de gebruikers interface](./media/storage-how-to-create-file-share/create-file-share-portal4.png)

1. Upload een bestand.
    ![de knop uploaden in de navigatie balk](./media/storage-how-to-create-file-share/create-file-share-portal5.png)

1. Blader naar de bestands share en beheer uw mappen en bestanden.
    ![mappen weergave van bestands shares](./media/storage-how-to-create-file-share/create-file-share-portal6.png)

## <a name="create-a-file-share-through-powershell"></a>Een bestands share maken via Power shell

Down load en installeer de Azure PowerShell-cmdlets. Zie  [Azure PowerShell installeren en configureren](https://docs.microsoft.com/powershell/azure/overview) voor het installatiepunt en de installatie-instructies.

> [!Note]  
> Het is raadzaam om de nieuwste Azure PowerShell module te downloaden en te installeren, of om deze te upgraden naar.

1. Maak een nieuw opslagaccount.
    Een opslag account is een gedeelde opslag groep waarin u Azure-bestands shares en opslag resources, zoals blobs of wacht rijen, kunt implementeren.

    ```PowerShell
    $resourceGroup = "myresourcegroup"
    $storAcctName = "myuniquestorageaccount"
    $region = "westus2"
    $storAcct = New-AzStorageAccount -ResourceGroupName $resourceGroup -Name $storAcctName -SkuName Standard_LRS -Location $region -Kind StorageV2
    ```

1. Maak een nieuwe bestands share.        
    ```PowerShell
    $shareName = "myshare"
    $share = New-AzStorageShare -Context $storAcct.Context -Name $shareName
    ```

> [!Note]  
> De naam van de bestandsshare mag alleen uit kleine letters bestaan. Zie [shares, directory's, bestanden en meta gegevens benoemen en hiernaar verwijzen](https://msdn.microsoft.com/library/azure/dn167011.aspx)voor meer informatie over de naamgeving van bestands shares en bestanden.

## <a name="create-a-file-share-through-the-cli"></a>Een bestands share maken via de CLI

1. Down load en installeer de Azure CLI.
    Zie [de Azure cli installeren](https://docs.microsoft.com/cli/azure/install-azure-cli) en aan [de slag met Azure cli](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

1. Maak een connection string naar het opslag account waar u de share wilt maken.
    Vervang  ```<storage-account>``` en ```<resource_group>``` in het volgende voorbeeld door de naam en resourcegroep van uw opslagaccount:

   ```azurecli
    current_env_conn_string=$(az storage account show-connection-string -n <storage-account> -g <resource-group> --query 'connectionString' -o tsv)

    if [[ $current_env_conn_string == "" ]]; then  
        echo "Couldn't retrieve the connection string."
    fi
    ```

1. Maak de bestands share.
    ```azurecli
    az storage share create --name files --quota 2048 --connection-string $current_env_conn_string > /dev/null
    ```

## <a name="next-steps"></a>Volgende stappen

* [Een bestands share in Windows verbinden en koppelen](storage-how-to-use-files-windows.md)
* [Een bestands share op Linux koppelen en koppelen](storage-how-to-use-files-linux.md)
* [Verbinding maken en koppelen van een bestands share op macOS](storage-how-to-use-files-mac.md)

Raadpleeg de volgende koppelingen voor meer informatie over Azure Files:

* [Veelgestelde vragen over opslag bestanden](storage-files-faq.md)
* [Problemen met Azure Files oplossen in Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Problemen met Azure Files in Linux oplossen](storage-troubleshoot-linux-file-connection-problems.md)
