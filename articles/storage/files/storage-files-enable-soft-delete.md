---
title: Voorlopig verwijderen inschakelen-Azure-bestands shares
description: Meer informatie over het inschakelen van zacht verwijderen (preview) in azure-bestands shares voor gegevens herstel en het voor komen van onopzettelijke verwijderingen.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 05/28/2020
ms.author: rogarana
ms.subservice: files
services: storage
ms.openlocfilehash: 2d2a000879a95f86a6cdda3324add5b692476eee
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590112"
---
# <a name="enable-soft-delete-on-azure-file-shares"></a>Zacht verwijderen inschakelen op Azure-bestands shares

Azure Storage biedt zacht verwijderen voor bestands shares (preview), zodat u uw gegevens eenvoudiger kunt herstellen wanneer deze per ongeluk worden verwijderd door een toepassing of een ander opslag account. Voor meer informatie over zacht verwijderen, Zie [How to onopzettelijk verwijderen van Azure-bestands shares](storage-files-prevent-file-share-deletion.md).

In de volgende secties ziet u hoe u met voorlopig verwijderen voor Azure-bestands shares kunt inschakelen en gebruiken voor een bestaand opslag account:

# <a name="portal"></a>[Portal](#tab/azure-portal)

## <a name="getting-started"></a>Aan de slag

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/).
1. Navigeer naar uw opslag account en selecteer **voorlopig verwijderen** onder **Bestands service**.
1. Selecteer **ingeschakeld** voor het **voorlopig verwijderen van de bestands share**.
1. Selecteer de **Bewaar periode voor bestands shares in dagen** en voer een nummer van uw keuze in.
1. Selecteer **Opslaan** om de instellingen voor het bewaren van gegevens te bevestigen.

:::image type="content" source="media/storage-how-to-recover-deleted-account/enable-soft-delete-files.png" alt-text="Scherm afbeelding van het deel venster instellingen voor voorlopig verwijderen van het opslag account. De sectie bestands shares markeren, scha kelen inschakelen, een Bewaar periode instellen en opslaan. Hiermee schakelt u tijdelijke verwijdering in voor alle bestands shares in uw opslag account.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="prerequisite"></a>Vereiste

Voorlopig verwijderen cmdlets zijn momenteel alleen beschikbaar in de versies [2.1.1-Preview](https://www.powershellgallery.com/packages/Az.Storage/2.1.1-preview) en [2.3.1-Preview](https://www.powershellgallery.com/packages/Az.Storage/2.3.1-preview) van de module AZ. storage. 

## <a name="getting-started"></a>Aan de slag

Als u zacht verwijderen wilt inschakelen, moet u de service-eigenschappen van een bestands client bijwerken. In het volgende voor beeld wordt zacht verwijderen ingeschakeld voor alle bestands shares in een opslag account:

```azurepowershell-interactive
$rgName = "yourResourceGroupName"
$accountName = "yourStorageAccountName"

Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $true -ShareRetentionDays 7
```

U kunt controleren of de functie voor voorlopig verwijderen is ingeschakeld en het Bewaar beleid weer geven met de volgende opdracht:

```azurepowershell-interactive
Get-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName
```
---

## <a name="restore-soft-deleted-file-share"></a>De tijdelijke verwijderde bestands share herstellen

# <a name="portal"></a>[Portal](#tab/azure-portal)

Een voorlopig verwijderde bestands share herstellen:

1. Navigeer naar uw opslag account en selecteer **Bestands shares**.
1. Schakel op de Blade bestands share de optie **Verwijderde shares weer geven** in om shares weer te geven die dynamisch zijn verwijderd.

    Hiermee worden de shares weer gegeven die zich momenteel in een **Verwijderde** staat bevindt.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/undelete-file-share.png" alt-text="Als de kolom Status, de kolom naast de kolom naam, is ingesteld op verwijderd, wordt de bestands share in de modus voorlopig verwijderd. En worden permanent verwijderd na de opgegeven Bewaar periode.":::

1. Selecteer de share en selecteer **verwijderen ongedaan**maken. Hierdoor wordt de share teruggezet.

    U kunt bevestigen dat de share is hersteld, omdat de status overschakelt naar **actief**.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/restored-file-share.png" alt-text="Als de kolom Status, de kolom naast de kolom naam, is ingesteld op actief, is de bestands share hersteld.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Tijdelijke Verwijder-cmdlets zijn beschikbaar in de versie 2.1.1-Preview van de module AZ. storage. Als u een voorlopig verwijderde bestands share wilt herstellen, gebruikt u de volgende opdracht:

```azurepowershell-interactive
Restore-AzRmStorageShare -ResourceGroupName $rgname -StorageAccountName $accountName -DeletedShareVersion 01D5E2783BDCDA97
```
---

## <a name="disable-soft-delete"></a>Tijdelijke verwijdering uitschakelen

Als u wilt stoppen met het gebruik van zacht verwijderen of als u een bestands share definitief wilt verwijderen, volgt u deze instructies:

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Navigeer naar uw opslag account en selecteer **voorlopig verwijderen** onder **instellingen**.
1. Selecteer onder **Bestands shares** **uitgeschakeld** voor **zacht verwijderen voor bestands shares**.
1. Selecteer **Opslaan** om de instellingen voor het bewaren van gegevens te bevestigen.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/disable-soft-delete-files.png" alt-text="Als u zacht verwijderen uitschakelt, kunt u de bestands shares in uw opslag account onmiddellijk en op uw gemak permanent verwijderen.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Tijdelijke Verwijder-cmdlets zijn beschikbaar in de versie 2.1.1-Preview van de module AZ. storage. U kunt de volgende opdracht gebruiken om de optie voor het voorlopig verwijderen van uw opslag account uit te scha kelen:

```azurepowershell-interactive
Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $false
```
---

## <a name="next-steps"></a>Volgende stappen

Zie het artikel [overzicht van moment opnamen van shares voor Azure files voor](storage-snapshots-files.md)meer informatie over een andere vorm van gegevens beveiliging en herstel.
