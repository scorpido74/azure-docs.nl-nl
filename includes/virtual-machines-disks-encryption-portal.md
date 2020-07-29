---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/23/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 950fd82e14902c3a7a94fddb44d50ac372ebe119
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/24/2020
ms.locfileid: "87177014"
---
[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](virtual-machines-disks-encryption-create-key-vault-portal.md)]

## <a name="deploy-a-vm"></a>Een virtuele machine implementeren

Nu u de sleutel kluis en de schijf versleutelings hebt gemaakt en ingesteld, kunt u een virtuele machine implementeren met behulp van de versleuteling.
Het implementatie proces van de VM is vergelijkbaar met het standaard implementatieproces, maar de enige verschillen zijn dat u de virtuele machine in dezelfde regio als uw andere resources moet implementeren en u ervoor kiest om een door de klant beheerde sleutel te gebruiken.

1. Zoek naar **virtual machines** en selecteer **+ toevoegen** om een virtuele machine te maken.
1. Selecteer op de Blade **basis** dezelfde regio als uw schijf versleutelings en Azure Key Vault.
1. Vul op de Blade **Basic** de andere waarden in zoals u wilt.

    ![Scherm afbeelding van het maken van de VM, waarbij de regio waarde is gemarkeerd.](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-vm-region.png)

1. Selecteer op **Disks** de Blade schijven **versleuteling in rust met een door de klant beheerde sleutel**.
1. Selecteer de schijf versleuteling die u hebt ingesteld in de vervolg keuzelijst **schijf versleuteling instellen** .
1. Breng de resterende selecties naar wens aan.

    ![Scherm afbeelding van de werk ervaring voor het maken van de virtuele machine, de Blade schijven. Met de vervolg keuzelijst schijf versleuteling instellen gemarkeerd.](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-vm-select-customer-managed-key-disk-encryption-set.png)

## <a name="enable-on-an-existing-disk"></a>Inschakelen op een bestaande schijf

> [!CAUTION]
> Als u schijf versleuteling wilt inschakelen op schijven die zijn gekoppeld aan een VM, moet u de virtuele machine stoppen.
    
1. Ga naar een virtuele machine die zich in dezelfde regio bevindt als een van de schijf versleutelings sets.
1. Open de virtuele machine en selecteer **stoppen**.

    ![Scherm afbeelding van de belangrijkste overlay voor uw voorbeeld-VM, met de knop stoppen gemarkeerd.](media/virtual-machines-disk-encryption-portal/server-side-encryption-stop-vm-to-encrypt-disk-fix.png)

1. Nadat de VM is gestopt, selecteert u **schijven** en selecteert u vervolgens de schijf die u wilt versleutelen.

    ![Scherm opname van uw voor beeld-VM, met de Blade schijven geopend. De besturingssysteem schijf is gemarkeerd als een voor beeld van een schijf die u kunt selecteren.](media/virtual-machines-disk-encryption-portal/server-side-encryption-existing-disk-select.png)

1. Selecteer **versleuteling** en selecteer **op rest versleuteling met een door de klant beheerde sleutel** en selecteer vervolgens de schijf versleuteling die u hebt ingesteld in de vervolg keuzelijst.
1. Selecteer **Opslaan**.

    ![Scherm opname van de besturingssysteem schijf van uw voor beeld. De versleutelings Blade is geopend. versleuteling in rust met een door de klant beheerde sleutel is geselecteerd, evenals uw voor beeld Azure Key Vault. Nadat u deze selecties hebt gemaakt, is de knop Opslaan geselecteerd.](media/virtual-machines-disk-encryption-portal/server-side-encryption-encrypt-existing-disk-customer-managed-key.png)

1. Herhaal dit proces voor alle andere schijven die zijn gekoppeld aan de virtuele machine die u wilt versleutelen.
1. Als uw schijven zijn overgeschakeld naar door de klant beheerde sleutels en er geen andere gekoppelde schijven zijn die u wilt versleutelen, kunt u de virtuele machine starten.
