---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/07/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c5ea31217bd3088ec123281b36f8578b08ea25b2
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/24/2020
ms.locfileid: "87136133"
---
## <a name="restrictions"></a>Beperkingen

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](virtual-machines-disks-encryption-at-host-restrictions.md)]

### <a name="supported-regions"></a>Ondersteunde regio's

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](virtual-machines-disks-encryption-at-host-regions.md)]

### <a name="supported-vm-sizes"></a>Ondersteunde VM-grootten

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](virtual-machines-disks-encryption-at-host-suported-sizes.md)]

## <a name="prerequisites"></a>Vereisten

Als u versleuteling wilt gebruiken op de host voor uw Vm's of virtuele-machine schaal sets, moet u de functie inschakelen voor uw abonnement. Stuur een e-mail naar encryptionAtHost@microsoft. com met uw abonnement-Id's om de functie in te scha kelen voor uw abonnementen.

Meld u aan bij de Azure Portal met behulp [van de beschik bare koppeling](https://aka.ms/diskencryptionupdates).

> [!IMPORTANT]
> U moet de [beschik bare koppeling](https://aka.ms/diskencryptionupdates) gebruiken om toegang te krijgen tot de Azure Portal. Versleuteling op host is momenteel niet zichtbaar in de open bare Azure Portal zonder de koppeling te gebruiken.

### <a name="create-an-azure-key-vault-and-disk-encryption-set"></a>Een Azure Key Vault-en schijf versleutelings maken

Zodra de functie is ingeschakeld, moet u een Azure Key Vault en een schijf versleutelings instellen, als u dat nog niet hebt gedaan.

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](virtual-machines-disks-encryption-create-key-vault-portal.md)]

## <a name="deploy-a-vm"></a>Een virtuele machine implementeren

U moet een nieuwe VM implementeren om versleuteling op de host in te scha kelen en deze kan niet worden ingeschakeld op bestaande Vm's.

1. Zoek naar **virtual machines** en selecteer **+ toevoegen** om een virtuele machine te maken.
1. Maak een nieuwe virtuele machine, selecteer een geschikte regio en een ondersteunde VM-grootte.
1. Vul de andere waarden op de Blade **basis** naar wens in en ga vervolgens door naar de Blade **schijven** .

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-basic-blade.png" alt-text="voorbeeld tekst":::

1. Selecteer op **Disks** de Blade schijven **Ja** voor **versleuteling op host**.
1. Breng de resterende selecties naar wens aan.

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-disk-blade.png" alt-text="voorbeeld tekst":::

1. Voltooi het implementatie proces van de VM en maak selecties die passen bij uw omgeving.

U hebt nu een virtuele machine geïmplementeerd met versleuteling op de host ingeschakeld, alle gekoppelde schijven worden versleuteld met versleuteling op de host.