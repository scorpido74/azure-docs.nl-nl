---
title: Include-bestand
description: Include-bestand
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/22/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c0627dd0833e3b20468eb5f50fbeb9fd9d9ae2b3
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/25/2020
ms.locfileid: "88864920"
---
**Uitgaande gegevens overdrachten**: [uitgaande gegevens overdracht](https://azure.microsoft.com/pricing/details/bandwidth/) (gegevens die uit de Azure-data centers worden verzonden) zijn er facturen voor bandbreedte gebruik.

**Trans acties**: u wordt gefactureerd voor het aantal trans acties dat u uitvoert op een Standard Managed disk. Voor standaard Ssd's wordt elke I/O-bewerking kleiner dan of gelijk aan 256 KiB van de door Voer beschouwd als één I/O-bewerking. I/O-bewerkingen die groter zijn dan 256 KiB door Voer, worden beschouwd als meerdere I/O's van grootte 256 KiB. Voor standaard Hdd's wordt elke IO-bewerking beschouwd als één trans actie, ongeacht de I/O-grootte.

Zie [Managed disks prijzen](https://azure.microsoft.com/pricing/details/managed-disks)voor gedetailleerde informatie over de prijzen voor Managed disks, waaronder transactie kosten.

### <a name="ultra-disk-vm-reservation-fee"></a>Kosten voor de reserve ring van ultra schijf-VM

Azure-Vm's hebben de mogelijkheid om aan te geven of ze compatibel zijn met ultra disks. Een ultra Disk-compatibele VM wijst toegewezen bandbreedte capaciteit toe tussen het Compute-VM-exemplaar en de schaal eenheid voor blok opslag om de prestaties te optimaliseren en de latentie te verminderen. Het toevoegen van deze mogelijkheid op de VM resulteert in een reserverings kosten die alleen worden opgelegd als u ultra Disk-functionaliteit op de virtuele machine hebt ingeschakeld zonder een ultra schijf aan de VM te koppelen. Wanneer een ultra schijf is gekoppeld aan de ultra Disk compatible VM, worden deze kosten niet toegepast. Deze kosten zijn per vCPU ingericht op de VM. 

> [!Note]
> Voor [beperkte core VM-grootten](../articles/virtual-machines/constrained-vcpu.md)zijn de reserverings kosten gebaseerd op het werkelijke aantal vcpu's en niet de beperkte kernen. Voor Standard_E32-8s_v3 zijn de reserverings kosten gebaseerd op 32 kernen. 

Raadpleeg de [pagina met prijzen voor Azure](https://azure.microsoft.com/pricing/details/managed-disks/) disks voor Ultra Disk-prijs informatie.

### <a name="azure-disk-reservation"></a>Azure-schijf reservering

Schijf reservering is de mogelijkheid om één jaar aan schijf opslag vooraf te kopen tegen een korting, waardoor de totale kosten worden verminderd. Wanneer u een schijf reservering aanschaft, selecteert u een specifieke schijf-SKU in een doel regio, bijvoorbeeld 10 P30 (1TiB) Premium Ssd's in de regio VS Oost 2 voor een periode van één jaar. De reserverings ervaring is vergelijkbaar met de gereserveerde exemplaren van virtuele machines (VM). U kunt virtuele machines en schijf reserveringen bundelen om uw besparingen te maximaliseren. De Azure Disk reservation biedt nu één jaar toezeggings plan voor Premium SSD Sku's van P30 (1TiB) tot P80 (32 TiB) in alle productie regio's. Zie de [pagina met prijzen voor Azure disks](https://azure.microsoft.com/pricing/details/managed-disks/)voor meer informatie over de prijzen voor gereserveerde schijven.