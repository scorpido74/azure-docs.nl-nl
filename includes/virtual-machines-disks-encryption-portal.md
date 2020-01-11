---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/10/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 165a115f35810c1bfe463a571affb2e44ed74205
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75893693"
---
### <a name="portal"></a>Portal

Door door de klant beheerde sleutels voor uw schijven in te stellen, moet u resources in een bepaalde volg orde maken, als u deze voor de eerste keer uitvoert. Eerst moet u een Azure Key Vault maken en instellen.

#### <a name="setting-up-your-azure-key-vault"></a>Uw Azure Key Vault instellen

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) en zoek naar Key Vault
1. Zoek en selecteer **sleutel kluizen**.

![SSE-Key-Vault-Portal-Search. png](media/virtual-machines-disk-encryption-portal/sse-key-vault-portal-search.png)

> [!IMPORTANT]
> De Azure-sleutel kluis, de schijf versleutelings, de virtuele machine, de schijven en moment opnamen moeten allemaal in dezelfde regio en hetzelfde abonnement staan om te kunnen worden geïmplementeerd.

1. Selecteer **+ toevoegen** om een nieuw Key Vault te maken.
1. Een nieuwe resourcegroep maken
1. Voer een naam voor de sleutel kluis in, selecteer een regio en selecteer een prijs categorie.
1. Selecteer **controleren + maken**, Controleer uw keuzes en selecteer vervolgens **maken**.

![SSE-Create-a-key-Vault. png](media/virtual-machines-disk-encryption-portal/sse-create-a-key-vault.png)

1. Als uw sleutel kluis is geïmplementeerd, selecteert u deze.
1. Selecteer **sleutels** onder **instellingen**.
1. Selecteer **genereren/importeren**

![SSE-Key-Vault-generate-Settings. png](media/virtual-machines-disk-encryption-portal/sse-key-vault-generate-settings.png)

1. Zorg dat het **sleutel type** is ingesteld op **RSA** en **RSA-sleutel grootte** ingesteld op **2080**.
1. Vul de resterende selecties in zoals u wilt en selecteer vervolgens **maken**.

![SSE-Create-a-key-generate. png](media/virtual-machines-disk-encryption-portal/sse-create-a-key-generate.png)

#### <a name="setting-up-your-disk-encryption-set"></a>Uw schijf versleutelings instellen

Als u schijf versleutelings sets wilt maken en configureren, moet u de volgende koppeling gebruiken: https://aka.ms/diskencryptionsets. Het maken van de schijf versleuteling is nog niet beschikbaar in de open bare Azure Portal.

1. Open de [koppeling schijf versleutelings sets](https://aka.ms/diskencryptionsets).
1. Selecteer **+ toevoegen**.

![SSE-Create-Disk-Encryption-set. png](media/virtual-machines-disk-encryption-portal/sse-create-disk-encryption-set.png)

1. Selecteer uw resource groep, geef uw versleutelings set een naam en selecteer dezelfde regio als uw sleutel kluis.
1. Selecteer **sleutel kluis en sleutel**.
1. Selecteer de sleutel kluis en de sleutel die u eerder hebt gemaakt, evenals de versie.
1. Druk op **selecteren**.
1. Selecteer **controleren + maken** en vervolgens **maken**.

![SSE-Disk-enc-set-Blade-Key. png](media/virtual-machines-disk-encryption-portal/sse-disk-enc-set-blade-key.png)

1. Open de schijf versleutelings nadat deze is gemaakt en selecteer de waarschuwing die wordt weer gegeven.

![SSE-Disk-enc-alert-fix. png](media/virtual-machines-disk-encryption-portal/sse-disk-enc-alert-fix.png)

Er worden twee meldingen over de pop-up en geslaagd. Als u dit doet, kunt u de set gebruiken met uw sleutel kluis.

![Disk-enc-notification-success. png](media/virtual-machines-disk-encryption-portal/disk-enc-notification-success.png)

#### <a name="deploy-a-vm"></a>Een virtuele machine implementeren

Nu u de sleutel kluis en de schijf versleutelings hebt gemaakt en ingesteld, kunt u een virtuele machine implementeren met behulp van de versleuteling.
Het implementatie proces van de VM is vergelijkbaar met het standaard implementatieproces, maar de enige verschillen zijn dat u de virtuele machine in dezelfde regio als uw andere resources moet implementeren en u ervoor kiest om een door de klant beheerde sleutel te gebruiken.

1. Zoek naar **virtual machines** en selecteer **+ toevoegen** om een virtuele machine te maken.
1. Op het tabblad **basis** selecteert u dezelfde regio als uw schijf versleutelings en Azure Key Vault.
1. Vul op het tabblad **basis** de andere waarden in zoals u wilt.

![SSE-Create-a-VM-Region. png](media/virtual-machines-disk-encryption-portal/sse-create-a-vm-region.png)

1. Op het tabblad **schijven** selecteert u **versleuteling in rust met door de klant beheerde sleutel**.
1. Selecteer de schijf versleuteling die u hebt ingesteld in de vervolg keuzelijst **schijf versleuteling instellen** .
1. Breng de resterende selecties naar wens aan.

![SSE-Create-VM-Select-CMK-Encryption-set. png](media/virtual-machines-disk-encryption-portal/sse-create-vm-select-cmk-encryption-set.png)