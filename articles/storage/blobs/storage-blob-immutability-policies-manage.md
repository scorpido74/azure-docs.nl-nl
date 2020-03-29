---
title: Onveranderlijkbeleid voor Blob-opslag instellen en beheren - Azure Storage
description: Meer informatie over het gebruik van WORM-ondersteuning (Write Once, Read Many) voor Blob-opslag (object) om gegevens op te slaan in een niet-uitwissende, niet-aanpasbare status voor een opgegeven interval.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 05a155584f0cb69191883cb82b3db0af435ccc12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970101"
---
# <a name="set-and-manage-immutability-policies-for-blob-storage"></a>Onveranderlijkbeleid voor Blob-opslag instellen en beheren

Onveranderlijke opslag voor Azure Blob-opslag stelt gebruikers in staat om bedrijfskritieke gegevensobjecten op te slaan in een WORM-status (Write Once, Read Many). Deze status maakt de gegevens niet-wissend en niet-aanpasbaar voor een door de gebruiker opgegeven interval. Voor de duur van het retentieinterval kunnen blobs worden gemaakt en gelezen, maar kunnen ze niet worden gewijzigd of verwijderd. Onveranderlijke opslag is beschikbaar voor v2- en Blob-opslagaccounts voor algemene doeleinden in alle Azure-regio's.

In dit artikel ziet u hoe u onveranderlijkbeleid en wettelijke voorschriften voor gegevens in Blob-opslag instellen en beheren met behulp van de Azure-portal, PowerShell of Azure CLI. Zie [Bedrijfskritieke blobgegevens opslaan met onveranderlijke opslag](storage-blob-immutable-storage.md)voor meer informatie over onveranderlijke opslag.

## <a name="set-retention-policies-and-legal-holds"></a>Bewaarbeleid en wettelijke voorschriften instellen

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Maak een nieuwe container of selecteer een bestaande container om de blobs op te slaan die u in onveranderbare toestand wilt bewaren. De container moet zich in een v2- of Blob-opslagaccount voor algemene doeleinden begeven.

2. Selecteer **Toegangsbeleid** in de containerinstellingen. Selecteer vervolgens **Beleid toevoegen** onder **Onveranderlijke blobopslag**.

    ![Containerinstellingen in de portal](media/storage-blob-immutability-policies-manage/portal-image-1.png)

3. Als u tijdgebaseerde retentie wilt inschakelen, selecteert u **Tijdgebaseerde retentie** in het vervolgkeuzemenu.

    !["Time-based retention" geselecteerd onder "Beleidstype"](media/storage-blob-immutability-policies-manage/portal-image-2.png)

4. Voer het bewaarinterval in dagen in (acceptabele waarden zijn 1 tot 146000 dagen).

    ![Vak 'Bewaarperiode bijwerken'](media/storage-blob-immutability-policies-manage/portal-image-5-retention-interval.png)

    De oorspronkelijke status van het beleid is ontgrendeld, zodat u de functie testen en wijzigingen in het beleid aanbrengen voordat u het vergrendelt. Het vergrendelen van het beleid is essentieel voor de naleving van regelgeving zoals SEC 17a-4.

5. Sluit het beleid af. Klik met de rechtermuisknop op de ellips (**...**) en het volgende menu wordt weergegeven met extra acties:

    !["Slotbeleid" op het menu](media/storage-blob-immutability-policies-manage/portal-image-4-lock-policy.png)

6. Selecteer **Vergrendelingsbeleid** en bevestig het slot. Het beleid is nu vergrendeld en kan niet worden verwijderd, alleen uitbreidingen van het bewaarinterval zijn toegestaan. Blob-verwijderingen en overschrijvingen zijn niet toegestaan. 

    !['Vergrendelingsbeleid' bevestigen in het menu](media/storage-blob-immutability-policies-manage/portal-image-5-lock-policy.png)

7. Als u juridische ruimen wilt inschakelen, selecteert u **Beleid toevoegen**. Selecteer **Juridische blokkering** in het vervolgkeuzemenu.

    !["Juridische greep" op het menu onder "Type Beleid"](media/storage-blob-immutability-policies-manage/portal-image-legal-hold-selection-7.png)

8. Maak een juridische greep met een of meer tags.

    ![Vak 'Naam tag' onder het type beleid](media/storage-blob-immutability-policies-manage/portal-image-set-legal-hold-tags.png)

9. Als u een wettelijke blokkering wilt wissen, verwijdert u de toegepaste wettelijke blokkeringstag.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

De functie is opgenomen in `az storage container immutability-policy` de `az storage container legal-hold`volgende opdrachtgroepen: en . Ren `-h` erop om de commando's te zien.

### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

De Az.Storage-module ondersteunt onveranderlijke opslag.  Voer de volgende stappen uit om de functie in te schakelen:

1. Zorg ervoor dat u de nieuwste versie `Install-Module PowerShellGet –Repository PSGallery –Force`van PowerShellGet hebt geïnstalleerd: .
2. Verwijder elke eerdere installatie van Azure PowerShell.
3. Azure PowerShell `Install-Module Az –Repository PSGallery –AllowClobber`installeren: .

Het volgende voorbeeld PowerShell-script is ter referentie. Dit script maakt een nieuw opslagaccount en een nieuwe container. Vervolgens wordt u weergegeven hoe u juridische houdt instelt, maakt en vergrendelt u een op tijd gebaseerd bewaarbeleid (ook wel een onveranderlijk beleid genoemd) en breidt u het bewaarinterval uit.

Maak eerst een Azure Storage-account:

```powershell
$resourceGroup = "<Enter your resource group>"
$storageAccount = "<Enter your storage account name>"
$container = "<Enter your container name>"
$location = "<Enter the storage account location>"

# Log in to Azure
Connect-AzAccount
Register-AzResourceProvider -ProviderNamespace "Microsoft.Storage"

# Create your Azure resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Create your Azure storage account
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup -StorageAccountName `
    $storageAccount -SkuName Standard_ZRS -Location $location -Kind StorageV2

# Create a new container using the context
$container = New-AzStorageContainer -Name $container -Context $account.Context

# List the containers in the account
Get-AzStorageContainer -Context $account.Context

# Remove a container
Remove-AzStorageContainer -Name $container -Context $account.Context
```

Set en duidelijke juridische houdt:

```powershell
# Set a legal hold
Add-AzRmStorageContainerLegalHold -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -Name $container -Tag <tag1>,<tag2>,...

# Clear a legal hold
Remove-AzRmStorageContainerLegalHold -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -Name $container -Tag <tag3>
```

Tijdgebaseerde onveranderlijkheidsbeleid maken of bijwerken:

```powershell
# Create a time-based immutablity policy
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container -ImmutabilityPeriod 10
```

Onveranderlijkbeleid ophalen:

```powershell
# Get an immutability policy
Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container
```

Beleid voor onveranderlijkheid `-Force` vergrendelen (toevoegen om de prompt te verwijderen):

```powershell
# Lock immutability policies
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container
Lock-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container `
    -Etag $policy.Etag
```

Onveranderlijkbeleid uitbreiden:

```powershell
# Extend immutability policies
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container

Set-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy `
    $policy -ImmutabilityPeriod 11 -ExtendPolicy
```

Een ontgrendeld onveranderlijk beleid `-Force` verwijderen (toevoegen om de prompt te verwijderen):

```powershell
# Remove an unlocked immutability policy
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container

Remove-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy
```

---

## <a name="enabling-allow-protected-append-blobs-writes"></a>Beveiligde schrijfbewerkingen toestaan toestaan

### <a name="portal"></a>[Portal](#tab/azure-portal)

![Aanvullende schrijfbewerkingen toestaan](media/storage-blob-immutability-policies-manage/immutable-allow-additional-append-writes.png)

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

De functie is opgenomen in `az storage container immutability-policy` de `az storage container legal-hold`volgende opdrachtgroepen: en . Ren `-h` erop om de commando's te zien.

### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```powershell
# Create an immutablity policy with appends allowed
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container -ImmutabilityPeriod 10 -AllowProtectedAppendWrite $true
```

---

## <a name="next-steps"></a>Volgende stappen

[Bedrijfskritieke blobgegevens opslaan met onveranderlijke opslag](storage-blob-immutable-storage.md)
