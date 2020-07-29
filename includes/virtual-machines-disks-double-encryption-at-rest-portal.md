---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 8313259d5375bb19e7caa08a01a658707453d133
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/24/2020
ms.locfileid: "87177013"
---
## <a name="supported-regions"></a>Ondersteunde regio's

[!INCLUDE [virtual-machines-disks-double-encryption-at-rest-regions](virtual-machines-disks-double-encryption-at-rest-regions.md)]

## <a name="getting-started"></a>Aan de slag

1. Meld u aan bij de [Azure-portal](https://aka.ms/diskencryptionupdates).

    > [!IMPORTANT]
    > U moet de [beschik bare koppeling](https://aka.ms/diskencryptionupdates) gebruiken om toegang te krijgen tot de Azure Portal. Dubbele versleuteling bij rest is momenteel niet zichtbaar in de open bare Azure Portal zonder de koppeling te gebruiken.

1. Zoek en selecteer **schijf versleutelings sets**.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-disk-encryption-sets-search.png" alt-text="Scherm opname van de belangrijkste Azure Portal, schijf versleutelings sets worden gemarkeerd op de zoek balk.":::

1. Selecteer **+ Toevoegen**.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-add-disk-encryption-set.png" alt-text="Scherm afbeelding van de Blade schijf versleuteling instellen, + toevoegen is gemarkeerd.":::

1. Selecteer een van de ondersteunde regio's.
1. Voor **versleutelings type**selecteert u **dubbele versleuteling met door het platform beheerde en door de klant beheerde sleutels**.

    > [!NOTE]
    > Wanneer u een schijf versleutelings met een bepaald versleutelings type hebt gemaakt, kan deze niet meer worden gewijzigd. Als u een ander versleutelings type wilt gebruiken, moet u een nieuwe schijf versleutelings maken.

1. Vul de resterende gegevens in.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-create-disk-encryption-set-blade.png" alt-text="Scherm opname van de Blade voor het maken van schijf versleuteling, regio's en dubbele versleuteling met door het platform beheerde en door de klant beheerde sleutels worden gemarkeerd.":::

1. Selecteer een Azure Key Vault en een sleutel of maak indien nodig een nieuwe.

    > [!NOTE]
    > Als u een Key Vault exemplaar maakt, moet u de beveiliging voor voorlopig verwijderen en leegmaken inschakelen. Deze instellingen zijn verplicht wanneer u een Key Vault gebruikt voor het versleutelen van beheerde schijven, en u kunt voor komen dat gegevens verloren gaan als gevolg van onbedoeld verwijderen.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-select-key-vault.png" alt-text="Scherm opname van de Blade Key Vault maken.":::

1. Selecteer **Maken**.
1. Navigeer naar de schijf Encryption set die u hebt gemaakt en selecteer de fout die wordt weer gegeven. Hiermee configureert u de schijf versleuteling zo dat deze is ingesteld op werk.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-disk-set-error.png" alt-text="Scherm opname van de weer gegeven schijf versleuteling fout, de fout tekst is: als u een schijf, installatie kopie of moment opname aan deze schijf versleutelings wilt koppelen, moet u machtigingen verlenen aan de sleutel kluis.":::

    Er wordt een melding over de pop-up en geslaagd. Als u dit doet, kunt u de schijf versleuteling instellen met uw sleutel kluis.
    
    ![Scherm opname van geslaagde machtiging en roltoewijzing voor uw sleutel kluis.](media/virtual-machines-disk-encryption-portal/disk-encryption-notification-success.png)

1. Navigeer naar uw schijf.
1. Selecteer **versleuteling**.
1. Voor **versleutelings type**selecteert u **dubbele versleuteling met door het platform beheerde en door de klant beheerde sleutels**.
1. Selecteer uw schijf versleutelings.
1. selecteer **Opslaan**.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-enable-disk-blade.png" alt-text="Scherm afbeelding van de Blade versleuteling voor uw beheerde schijf, wordt het eerder versleutelings type gemarkeerd.":::

U hebt nu dubbele versleuteling ingeschakeld bij de rest van uw beheerde schijf.
