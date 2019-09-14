---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/22/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a416d1c6e813be558f034e15576c57efa6073788
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70968566"
---
**Uitgaande gegevens overdracht**: [Uitgaande gegevens overdracht](https://azure.microsoft.com/pricing/details/bandwidth/) (gegevens die uit de Azure-data centers gaan) zijn een facturering voor het gebruik van band breedte.

**Trans acties**: U wordt gefactureerd voor het aantal trans acties dat u uitvoert op een Standard-beheerde schijf. Voor standaard Ssd's wordt elke I/O-bewerking kleiner dan of gelijk aan 256 KiB van de door Voer beschouwd als één I/O-bewerking. I/O-bewerkingen die groter zijn dan 256 KiB door Voer, worden beschouwd als meerdere I/O's van grootte 256 KiB. Voor standaard Hdd's wordt elke IO-bewerking beschouwd als één trans actie, ongeacht de I/O-grootte.

Zie [Managed disks prijzen](https://azure.microsoft.com/pricing/details/managed-disks)voor gedetailleerde informatie over de prijzen voor Managed disks, waaronder transactie kosten.

### <a name="ultra-disk-vm-reservation-fee"></a>Kosten voor de reserve ring van ultra schijf-VM

Azure-Vm's hebben de mogelijkheid om aan te geven of ze compatibel zijn met ultra disks. Een ultra Disk-compatibele VM wijst toegewezen bandbreedte capaciteit toe tussen het Compute-VM-exemplaar en de schaal eenheid voor blok opslag om de prestaties te optimaliseren en de latentie te verminderen. Het toevoegen van deze mogelijkheid op de VM resulteert in een reserverings kosten die alleen worden opgelegd als u ultra Disk-functionaliteit op de virtuele machine hebt ingeschakeld zonder een ultra schijf aan de VM te koppelen. Wanneer een ultra schijf is gekoppeld aan de ultra Disk compatible VM, worden deze kosten niet toegepast. Deze kosten zijn per vCPU ingericht op de VM. 

> [!Note]
> Voor [beperkte core VM-grootten](../articles/virtual-machines/linux/constrained-vcpu.md)zijn de reserverings kosten gebaseerd op het werkelijke aantal vcpu's en niet de beperkte kernen. Voor Standard_E32-8s_v3 is de reserverings kosten gebaseerd op 32 kernen. 

Raadpleeg de [pagina met prijzen voor Azure](https://azure.microsoft.com/pricing/details/managed-disks/) disks voor Ultra Disk-prijs informatie.
