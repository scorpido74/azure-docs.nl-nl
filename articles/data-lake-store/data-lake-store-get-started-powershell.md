---
title: Aan de slag met Azure Data Lake Storage Gen1 - PowerShell | Microsoft Documenten
description: Gebruik Azure PowerShell om een Azure Data Lake Storage Gen1-account te maken en basisbewerkingen uit te voeren.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: 42ddab6991b418af3e41da9966cdab69ded87461
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73837899"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-azure-powershell"></a>Aan de slag met Azure Data Lake Storage Gen1 met Azure PowerShell

> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [Powershell](data-lake-store-get-started-powershell.md)
> * [Azure-CLI](data-lake-store-get-started-cli-2.0.md)
>
>

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Meer informatie over het gebruik van Azure PowerShell om een Azure Data Lake Storage Gen1-account te maken en basisbewerkingen uit te voeren, zoals mappen maken, gegevensbestanden uploaden en downloaden, uw account verwijderen, enz. Zie Overzicht van Data Lake Storage Gen1 voor meer informatie over Data Lake Storage [Gen1.](data-lake-store-overview.md)

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 of hoger**. Zie [Azure PowerShell installeren en configureren](/powershell/azure/overview).

## <a name="authentication"></a>Authentication

In dit artikel wordt een eenvoudigere verificatiebenadering gebruikt met Data Lake Storage Gen1, waarbij u wordt gevraagd uw Azure-accountreferenties in te voeren. Het toegangsniveau tot Het Data Lake Storage Gen1-account- en bestandssysteem wordt vervolgens bepaald door het toegangsniveau van de ingelogde gebruiker. Er zijn echter andere benaderingen om te verifiëren met Data Lake Storage Gen1, die verificatie van eindgebruikers of service-to-service-verificatie zijn. Zie [Verificatie door eindgebruikers](data-lake-store-end-user-authenticate-using-active-directory.md) of [Service-to-serviceverificatie](data-lake-store-authenticate-using-active-directory.md) voor instructies en meer informatie over verificatie.

## <a name="create-a-data-lake-storage-gen1-account"></a>Een Data Lake Storage Gen1-account maken

1. Open een nieuw Windows PowerShell-venster via het bureaublad. Voer het volgende fragment in om u aan te melden bij uw Azure-account, stel het abonnement in en registreer de Data Lake Storage Gen1-provider. Wanneer u wordt gevraagd om in te loggen, moet u zich aanmelden als een van de beheerders/eigenaren van het abonnement:

    ```PowerShell
    # Log in to your Azure account
    Connect-AzAccount

    # List all the subscriptions associated to your account
    Get-AzSubscription

    # Select a subscription
    Set-AzContext -SubscriptionId <subscription ID>

    # Register for Azure Data Lake Storage Gen1
    Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"
    ```

1. Een Data Lake Storage Gen1-account is gekoppeld aan een Azure-brongroep. Begin met het maken van een resourcegroep.

    ```PowerShell
    $resourceGroupName = "<your new resource group name>"
    New-AzResourceGroup -Name $resourceGroupName -Location "East US 2"
    ```

    ![Een Azure-resourcegroep maken](./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "Een Azure-resourcegroep maken")

1. Maak een Data Lake Storage Gen1-account aan. De naam die u opgeeft, mag alleen kleine letters en cijfers bevatten.

    ```PowerShell
    $dataLakeStorageGen1Name = "<your new Data Lake Storage Gen1 account name>"
    New-AzDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStorageGen1Name -Location "East US 2"
    ```

    ![Een Data Lake Storage Gen1-account maken](./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "Een Data Lake Storage Gen1-account maken")

1. Controleer of het account is gemaakt.

    ```PowerShell
    Test-AzDataLakeStoreAccount -Name $dataLakeStorageGen1Name
    ```

    De uitvoer van de cmdlet moet **waar** zijn.

## <a name="create-directory-structures"></a>Directorystructuren maken

U mappen maken onder uw Data Lake Storage Gen1-account om gegevens te beheren en op te slaan.

1. Geef een hoofdmap op.

    ```PowerShell
    $myrootdir = "/"
    ```

1. Maak onder de opgegeven hoofdmap een nieuwe map met de naam **mynewdirectory**.

    ```PowerShell
    New-AzDataLakeStoreItem -Folder -AccountName $dataLakeStorageGen1Name -Path $myrootdir/mynewdirectory
    ```

1. Controleer of de nieuwe map is gemaakt.

    ```PowerShell
    Get-AzDataLakeStoreChildItem -AccountName $dataLakeStorageGen1Name -Path $myrootdir
    ```

    De uitvoer moet overeenkomen met de volgende schermafbeelding:

    ![Map verifiëren](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "Map verifiëren")

## <a name="upload-data"></a>Gegevens uploaden

U uw gegevens rechtstreeks op basisniveau uploaden naar Data Lake Storage Gen1 of naar een map die u in het account hebt gemaakt. In de codefragmenten in deze sectie ziet u hoe u voorbeeldgegevens uploadt naar de map (**mynewfolder**) die u in de voorgaande sectie hebt gemaakt.

Als u nog geen voorbeeldgegevens hebt om te uploaden, kunt u de map **Ambulance Data** uit de [Azure Data Lake Git-opslagplaats](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData) gebruiken. Download het bestand en sla het in een lokale map op uw computer op, bijvoorbeeld C:\sampledata\.

```PowerShell
Import-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
   -Path "C:\sampledata\vehicle1_09142014.csv" `
   -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv
```

## <a name="rename-download-and-delete-data"></a>Gegevens wijzigen, downloaden en verwijderen

Als u de naam van een bestand wilt wijzigen, gebruikt u de volgende opdracht:

```PowerShell
Move-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
    -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv `
    -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv
```

Gebruik de volgende opdracht als u een bestand wilt uploaden:

```PowerShell
Export-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
    -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv `
    -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"
```

Gebruik de volgende opdracht als u een bestand wilt verwijderen:

```PowerShell
Remove-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
    -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv
```

Wanneer dit wordt gevraagd, typt u **Y** om het item te verwijderen. Als u meer dan één bestand wilt verwijderen, kunt u alle paden, door komma's gescheiden, opgeven.

```PowerShell
Remove-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
    -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv
```

## <a name="delete-your-account"></a>Uw account verwijderen

Gebruik de volgende opdracht om uw Data Lake Storage Gen1-account te verwijderen.

```PowerShell
Remove-AzDataLakeStoreAccount -Name $dataLakeStorageGen1Name
```

Wanneer dit wordt gevraagd, typt u **Y** om het account te verwijderen.

## <a name="next-steps"></a>Volgende stappen

* [Richtlijnen voor prestatieafstemming voor het gebruik van PowerShell met Azure Data Lake Storage Gen1](data-lake-store-performance-tuning-powershell.md)
* [Azure Data Lake Storage Gen1 gebruiken voor vereisten voor big data](data-lake-store-data-scenarios.md)
* [Gegevens beveiligen in Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Azure Data Lake Analytics gebruiken met Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Azure HDInsight gebruiken met Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
