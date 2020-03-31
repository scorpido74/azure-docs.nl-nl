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
ms.openlocfilehash: 2303d36e93cecfca03894a8b0e55458c03b13d78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73412959"
---
**Uitgaande gegevensoverdrachten**: [Uitgaande gegevensoverdrachten](https://azure.microsoft.com/pricing/details/bandwidth/) (gegevens die uitgaan van Azure-datacenters) vereisen facturering voor bandbreedtegebruik.

**Transacties:** er worden kosten in rekening gebracht voor het aantal transacties dat u uitvoert op een standaard beheerde schijf. Voor standaard SSD's wordt elke I/O-bewerking die minder dan of gelijk is aan 256 KiB-doorvoer beschouwd als één I/O-bewerking. I/O-bewerkingen groter dan 256 KiB-doorvoer worden beschouwd als meerdere I/O's van maat 256 KiB. Voor standaard HDD's wordt elke IO-bewerking beschouwd als één transactie, ongeacht de I/O-grootte.

Zie [Managed Disks Pricing](https://azure.microsoft.com/pricing/details/managed-disks)voor gedetailleerde informatie over de prijzen voor beheerde schijven, inclusief transactiekosten.

### <a name="ultra-disk-vm-reservation-fee"></a>Vm-reserveringskosten voor ultraschijven

Azure VM's hebben de mogelijkheid om aan te geven of ze compatibel zijn met ultraschijven. Een vm met ultraschijfcompatibele kent specifieke bandbreedtecapaciteit toe tussen de compute VM-instantie en de blokopslagschaaleenheid om de prestaties te optimaliseren en de latentie te verminderen. Het toevoegen van deze mogelijkheid aan de VM resulteert in een reserveringskosten die alleen worden opgelegd als u ultraschijfmogelijkheden op de VM hebt ingeschakeld zonder er een ultraschijf aan te koppelen. Wanneer een ultraschijf is aangesloten op de vm met ultraschijfcompatibel, wordt deze kosten niet toegepast. Deze kosten zijn per vCPU op de VM. 

> [!Note]
> Voor [beperkte vm-formaten](../articles/virtual-machines/linux/constrained-vcpu.md)is de reserveringsvergoeding gebaseerd op het werkelijke aantal vCPU's en niet op de beperkte kernen. Voor Standard_E32-8s_v3 wordt de reserveringsvergoeding gebaseerd op 32 cores. 

Raadpleeg de [prijspagina azure disks](https://azure.microsoft.com/pricing/details/managed-disks/) voor details over de prijsmet hoge schijf.

### <a name="azure-disk-reservation"></a>Azure-schijfreservering

Schijfreservering is de optie om een jaar schijfopslag vooraf aan te schaffen tegen een korting, waardoor uw totale kosten worden verlaagd. Wanneer u een schijfreservering koopt, selecteert u een specifieke Schijf-SKU in een doelgebied, bijvoorbeeld 10 P30 -premium-SSD's in de regio Oost-VS 2 voor een periode van één jaar. De reserveringservaring is vergelijkbaar met vm-exemplaren (reserved virtual machine). U VM- en schijfreserveringen bundelen om uw besparingen te maximaliseren. Azure Disks Reservering biedt voorlopig een jaar verbintenisplan voor premium SSD SKU's van P30 (1TiB) tot P80 (32 TiB) in alle productieregio's. Zie de [prijspagina voor gereserveerde](https://azure.microsoft.com/pricing/details/managed-disks/)schijven voor meer informatie over de prijzen voor gereserveerde schijven.