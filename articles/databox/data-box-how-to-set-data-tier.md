---
title: Gegevens verzenden naar hot, cold, archiveblob-laag via Azure Data Box/Azure Data Box Heavy
description: Beschrijft hoe u Azure Data Box of Azure Data Box Heavy gebruiken om gegevens naar een geschikte opslaglaag voor blokblobs te verzenden, zoals warm, koud of archief
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 05/24/2019
ms.author: alkohli
ms.openlocfilehash: 31178284313ad7dafb094b109a75d4550cabd39c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560368"
---
# <a name="use-azure-data-box-or-azure-data-box-heavy-to-send-data-to-appropriate-azure-storage-blob-tier"></a>Azure Data Box of Azure Data Box Heavy gebruiken om gegevens naar de juiste Azure Storage-bloblaag te verzenden

Azure Data Box verplaatst grote hoeveelheden gegevens naar Azure door u een eigen opslagapparaat te sturen. U vult het apparaat met gegevens en retourneert het. De gegevens uit gegevensvak worden geüpload naar een standaardlaag die is gekoppeld aan het opslagaccount. U de gegevens vervolgens verplaatsen naar een andere opslaglaag.

In dit artikel wordt beschreven hoe de gegevens die door Data Box worden geüpload, kunnen worden verplaatst naar een bloblaag voor hot, cold of archiveren. Dit artikel is van toepassing op zowel Azure Data Box als Azure Data Box Heavy.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="choose-the-correct-storage-tier-for-your-data"></a>De juiste opslaglaag voor uw gegevens kiezen

Azure-opslag stelt drie verschillende lagen in staat om gegevens op de meest kosteneffectieve manier op te slaan - Hot, Cold of Archive. Hot storage-laag is geoptimaliseerd voor het opslaan van gegevens die vaak worden geopend. Hot storage heeft hogere opslagkosten dan Cool en Archive storage, maar de laagste toegangskosten.

Koele opslaglaag is voor zelden geopende gegevens die minimaal 30 dagen moeten worden opgeslagen. De opslagkosten voor koudelagen zijn lager dan die van de hot storage-laag, maar de kosten voor gegevenstoegang zijn hoog in vergelijking met Hot-laag.

De Azure Archive-laag is offline en biedt de laagste opslagkosten, maar ook de hoogste toegangskosten. Deze laag is bedoeld voor gegevens die minimaal 180 dagen in archiefopslag blijven. Ga voor meer informatie over elk van deze lagen en het prijsmodel naar [Vergelijking van de opslaglagen](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers).

De gegevens uit de gegevensbox of gegevensvak zwaar worden geüpload naar een opslaglaag die is gekoppeld aan het opslagaccount. Wanneer u een opslagaccount maakt, u de toegangslaag opgeven als Hot of Cold. Afhankelijk van het toegangspatroon van uw werkbelasting en kosten, u deze gegevens verplaatsen van de standaardlaag naar een andere opslaglaag.

U mag alleen uw objectopslaggegevens in Blob-opslag- of GPv2-accounts (General Purpose) selecteren. General Purpose v1 (GPv1)-accounts bieden geen ondersteuning voor opslaglagen. Als u de juiste opslaglaag voor uw gegevens wilt kiezen, controleert u de overwegingen die worden beschreven in [Azure Blob-opslag: opslaglagen Premium, Hot, Cool en Archive.](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers)

## <a name="set-a-default-blob-tier"></a>Een standaardbloblaag instellen

De standaardbloblaag is opgegeven wanneer het opslagaccount wordt gemaakt in de Azure-portal. Zodra een opslagtype is geselecteerd als GPv2- of Blob-opslag, kan het kenmerk Access-laag worden opgegeven. Standaard is de laag Hot geselecteerd.

De lagen kunnen niet worden opgegeven als u een nieuw account probeert aan te maken bij het bestellen van een gegevensvak of gegevensvak zwaar. Nadat het account is gemaakt, u het account in de portal wijzigen om de standaardtoegangslaag in te stellen.

U maakt ook eerst een opslagaccount met het kenmerk voor de opgegeven toegangslaag. Selecteer bij het maken van de volgorde Gegevensvak of Gegevensvak Zwaar het bestaande opslagaccount. Ga voor meer informatie over het instellen van de standaardbloblaag tijdens het maken van opslagaccount naar [Een opslagaccount maken in Azure-portal](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=portal).

## <a name="move-data-to-a-non-default-tier"></a>Gegevens verplaatsen naar een niet-standaardlaag

Zodra de gegevens van het gegevensvakapparaat zijn geüpload naar de standaardlaag, u de gegevens verplaatsen naar een niet-standaardlaag. Er zijn twee manieren om deze gegevens naar een niet-standaardlaag te verplaatsen.

- **Azure Blob-opslaglevenscyclusbeheer** - U een op beleid gebaseerde benadering gebruiken om gegevens automatisch te lagen of aan het einde van de levenscyclus te verlopen. Ga voor meer informatie naar [De levenscyclus van Azure Blob-opslag beheren.](https://docs.microsoft.com/azure/storage/common/storage-lifecycle-managment-concepts)
- **Scripting** - U een gescripte benadering via Azure PowerShell gebruiken om gelaagde blobniveaus in te schakelen. U `SetBlobTier` de bewerking aanroepen om de laag op de blob in te stellen.

## <a name="use-azure-powershell-to-set-the-blob-tier"></a>Azure PowerShell gebruiken om de bloblaag in te stellen

In de volgende stappen wordt beschreven hoe u de bloblaag instellen op Archiveren met een Azure PowerShell-script.

1. Open een verhoogde Windows PowerShell-sessie. Zorg ervoor dat uw powershell 5.0 of hoger wordt uitgevoerd. Type:

   `$PSVersionTable.PSVersion`     

2. Meld u aan bij de Azure PowerShell. 

   `Login-AzAccount`  

3. Definieer de variabelen voor opslagaccount, toegangssleutel, container en de opslagcontext.

    ```powershell
    $StorageAccountName = "<enter account name>"
    $StorageAccountKey = "<enter account key>"
    $ContainerName = "<enter container name>"
    $ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    ```

4. Haal alle blobs in de container.

    `$blobs = Get-AzStorageBlob -Container "<enter container name>" -Context $ctx`
 
5. Stel de laag van alle blobs in de container in op Archiveren.

    ```powershell
    Foreach ($blob in $blobs) {
    $blob.ICloudBlob.SetStandardBlobTier("Archive")
    }
    ```

    Hieronder wordt een voorbeelduitvoer weergegeven:

    ```
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    PS C:\WINDOWS\system32> $PSVersionTable.PSVersion

    Major  Minor  Build  Revision
    -----  -----  -----  --------
    5      1      17763  134
    PS C:\WINDOWS\system32> Login-AzAccount

    Account          : gus@contoso.com
    SubscriptionName : MySubscription
    SubscriptionId   : subscription-id
    TenantId         : tenant-id
    Environment      : AzureCloud

    PS C:\WINDOWS\system32> $StorageAccountName = "mygpv2storacct"
    PS C:\WINDOWS\system32> $StorageAccountKey = "mystorageacctkey"
    PS C:\WINDOWS\system32> $ContainerName = "test"
    PS C:\WINDOWS\system32> $ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    PS C:\WINDOWS\system32> $blobs = Get-AzStorageBlob -Container "test" -Context $ctx
    PS C:\WINDOWS\system32> Foreach ($blob in $blobs) {
    >> $blob.ICloudBlob.SetStandardBlobTier("Archive")
    >> }
    PS C:\WINDOWS\system32>
    ```
   > [!TIP]
   > Als u wilt dat de gegevens worden gearchiveerd op inname, stelt u de standaardaccountlaag in op Hot. Als de standaardlaag Cool is, is er een boete voor vervroegde verwijdering van 30 dagen als de gegevens onmiddellijk naar Archive worden verplaatst.

## <a name="next-steps"></a>Volgende stappen

-  Meer informatie over het aanpakken van de [algemene scenario's voor gegevenslagen met levenscyclusbeleidsregels](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts#examples)

