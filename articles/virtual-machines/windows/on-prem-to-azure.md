---
title: Migreren van AWS en andere platforms naar beheerde schijven in Azure
description: Vm's maken in Azure met behulp van VHD's die zijn geüpload vanuit andere clouds zoals AWS of andere virtualisatieplatforms en profiteer van Azure Managed Disks.
services: virtual-machines-windows
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 10/07/2017
ms.author: rogarana
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: dbce2969ccb508c2bf3ee33730d0b112caa45c9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243157"
---
# <a name="migrate-from-amazon-web-services-aws-and-other-platforms-to-managed-disks-in-azure"></a>Migreren van Amazon Web Services (AWS) en andere platforms naar beheerde schijven in Azure

U VHD-bestanden van AWS of on-premises virtualisatieoplossingen uploaden naar Azure om VM's te maken die profiteren van beheerde schijven. Azure Managed Disks hoeft niet meer te worden beheerd voor opslagaccounts voor Azure IaaS VM's. U hoeft alleen het type (Premium of Standard) en de grootte van de schijf op te geven die u nodig hebt, en Azure maakt en beheert de schijf voor u. 

U algemene en gespecialiseerde VHD's uploaden. 
- **Gegeneraliseerde VHD** - heeft al uw persoonlijke accountgegevens verwijderd met behulp van Sysprep. 
- **Gespecialiseerde VHD** - onderhoudt de gebruikersaccounts, toepassingen en andere statusgegevens van uw oorspronkelijke VM. 

> [!IMPORTANT]
> Voordat u vhd naar Azure uploadt, moet u [Een Windows VHD of VHDX voorbereiden volgen om te uploaden naar Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
>
>


| Scenario                                                                                                                         | Documentatie                                                                                                                       |
|----------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
| U hebt bestaande AWS EC2-exemplaren die u met beheerde schijven naar Azure VM's wilt migreren                              | [Een VM verplaatsen van Amazon Web Services (AWS) naar Azure](aws-to-azure.md)                           |
| U hebt een VM van een ander virtualisatieplatform dat u als afbeelding wilt gebruiken om meerdere Azure VM's te maken. | [Upload een gegeneraliseerde VHD en gebruik deze om een nieuwe virtuele machine in Azure te maken](upload-generalized-managed.md) |
| U hebt een uniek aangepaste VM die u wilt opnieuw maken in Azure.                                                      | [Upload een gespecialiseerde VHD naar Azure en maak een nieuwe VM](create-vm-specialized.md)         |


## <a name="overview-of-managed-disks"></a>Overzicht van beheerde schijven

Azure Managed Disks vereenvoudigt vm-beheer door dat opslagaccounts niet meer hoeven te worden beheerd. Beheerde schijven profiteren ook van een betere betrouwbaarheid van VM's in een beschikbaarheidsset. Het zorgt ervoor dat de schijven van verschillende VM's in een beschikbaarheidsset voldoende van elkaar zijn geïsoleerd om één storingspunt te voorkomen. Het plaatst automatisch schijven van verschillende VM's in een beschikbaarheidsset in verschillende opslagschaaleenheden (stempels) die de impact van afzonderlijke storingen in de opslagschaal-eenheid beperkt die zijn veroorzaakt door hardware- en softwarefouten.
Op basis van uw behoeften u kiezen uit vier soorten opslagopties. Zie ons artikel [Selecteer een schijftype](disks-types.md)voor meer informatie over de beschikbare schijftypen .

## <a name="plan-for-the-migration-to-managed-disks"></a>Plannen voor de migratie naar beheerde schijven

Met deze sectie u de beste beslissing nemen over VM- en schijftypen.

Als u van plan bent te migreren van niet-beheerde schijven naar beheerde schijven, moet u zich ervan bewust zijn dat gebruikers met de rol [Virtuele machineinzender](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) de VM-grootte niet kunnen wijzigen (omdat ze vooraf kunnen worden geconversiet). Dit komt omdat VM's met beheerde schijven vereisen dat de gebruiker de microsoft.compute/disks/write-machtiging op de OS-schijven moet hebben.

### <a name="location"></a>Locatie

Kies een locatie waar Azure Managed Disks beschikbaar zijn. Als u migreert naar Premium Managed Disks, moet u er ook voor zorgen dat Premium-opslag beschikbaar is in de regio waarnaar u wilt migreren. Zie [Azure Services by Region](https://azure.microsoft.com/regions/#services) voor actuele informatie over beschikbare locaties.

### <a name="vm-sizes"></a>Formaten van virtuele machines

Als u migreert naar Premium Managed Disks, moet u de grootte van de VM bijwerken naar de grootte van de virtuele opslag met Premium-opslag die beschikbaar is in het gebied waar VM zich bevindt. Bekijk de VM-formaten die geschikt zijn voor Premium Storage. De azure VM-groottespecificaties worden vermeld in [Grootte voor virtuele machines.](sizes.md)
Bekijk de prestatiekenmerken van virtuele machines die werken met Premium Storage en kies de meest geschikte VM-grootte die het beste bij uw werkbelasting past. Zorg ervoor dat er voldoende bandbreedte beschikbaar is op uw VM om het schijfverkeer te stimuleren.

### <a name="disk-sizes"></a>Schijfformaten

**Premium beheerde schijven**

Er zijn zeven soorten premium beheerde schijven die kunnen worden gebruikt met uw VM en elk heeft specifieke IPP's en doorvoerlimieten. Houd rekening met deze limieten bij het kiezen van het Premium-schijftype voor uw vm op basis van de behoeften van uw toepassing op het gebied van capaciteit, prestaties, schaalbaarheid en piekbelastingen.

| Type Premium-schijven  | P4    | P6    | P10   | P15   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|-------|
| Schijfgrootte           | 32 GB| 64 GB| 128 GB| 256 GB|512 GB | 1024 GB (1 TB)    | 2048 GB (2 TB)    | 4095 GB (4 TB)    | 
| IOP's per schijf       | 120   | 240   | 500   | 1100  |2300              | 5000              | 7500              | 7500              | 
| Doorvoer per schijf | 25 MB per seconde  | 50 MB per seconde  | 100 MB per seconde | 125 MB per seconde |150 MB per seconde | 200 MB per seconde | 250 MB per seconde | 250 MB per seconde |

**Standaard beheerde schijven**

Er zijn zeven soorten standaard beheerde schijven die met uw VM kunnen worden gebruikt. Elk van hen heeft een andere capaciteit, maar hebben dezelfde IOPS en doorvoer grenzen. Kies het type standaard beheerde schijven op basis van de capaciteitsbehoeften van uw toepassing.

| Standard-schijftype  | S4               | S6               | S10              | S15              | S20              | S30              | S40              | S50              | 
|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------| 
| Schijfgrootte           | 30 GB            | 64 GB            | 128 GB           | 256 GB           |512 GB           | 1024 GB (1 TB)   | 2048 GB (2 TB)    | 4095 GB (4 TB)   | 
| IOP's per schijf       | 500              | 500              | 500              | 500              |500              | 500              | 500             | 500              | 
| Doorvoer per schijf | 60 MB per seconde | 60 MB per seconde | 60 MB per seconde | 60 MB per seconde |60 MB per seconde | 60 MB per seconde | 60 MB per seconde | 60 MB per seconde | 

### <a name="disk-caching-policy"></a>Beleid voor het plaatsen van schijven 

**Premium beheerde schijven**

Standaard is schijfcachebeleid *Alleen-lezen* voor alle Premium-gegevensschijven en *Lees-schrijfvoor* de schijf van het Premium-besturingssysteem die aan de VM is gekoppeld. Deze configuratie-instelling wordt aanbevolen om de optimale prestaties voor de IOs van uw toepassing te bereiken. Schakel schijfcaches uit voor alleen schrijfzware of write-only gegevensschijven (zoals SQL Server-logboekbestanden), en schakel schijfcache uit, zodat u betere toepassingsprestaties bereiken.

### <a name="pricing"></a>Prijzen

Bekijk de [prijzen voor beheerde schijven](https://azure.microsoft.com/pricing/details/managed-disks/). De prijzen van Premium Managed Disks zijn gelijk aan de Premium Onbeheerde schijven. Maar de prijzen voor standaard beheerde schijven is anders dan standaard niet-beheerde schijven.


## <a name="next-steps"></a>Volgende stappen

- Voordat u vhd naar Azure uploadt, moet u [Een Windows VHD of VHDX voorbereiden volgen om te uploaden naar Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
