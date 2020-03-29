---
title: Schaalbaarheidsdoelen voor blob-opslagaccounts met premium pagina
titleSuffix: Azure Storage
description: Een blob-opslagaccount met een premium prestatiepagina is geoptimaliseerd voor lees-/schrijfbewerkingen. Dit type opslagaccount maakt een back-to-meen van een onbeheerde schijf voor een virtuele Azure-machine.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: e134c69f5d602cb4369e9410e3e2b9d3478b11a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76756246"
---
# <a name="scalability-and-performance-targets-for-premium-page-blob-storage-accounts"></a>Schaalbaarheids- en prestatiedoelen voor blob-opslagaccounts met premium pagina's

[!INCLUDE [storage-scalability-intro-include](../../../includes/storage-scalability-intro-include.md)]

## <a name="scale-targets-for-premium-page-blob-accounts"></a>Doelen schalen voor blobaccounts met premium pagina's

Een blob-opslagaccount met premium prestaties is geoptimaliseerd voor lees-/schrijfbewerkingen. Dit type opslagaccount maakt een back-to-meen van een onbeheerde schijf voor een virtuele Azure-machine.

> [!NOTE]
> Microsoft raadt aan om beheerde schijven te gebruiken met Virtuele Azure-machines (VM's) indien mogelijk. Zie overzicht van Azure [Disk Storage voor Windows VM's voor](../../virtual-machines/windows/managed-disks-overview.md)meer informatie over beheerde schijven.

Premium paginablob-opslagaccounts hebben de volgende schaalbaarheidsdoelen:

| Totale accountcapaciteit                            | Totale bandbreedte voor een lokaal redundantopslagaccount                     |
| ------------------------------------------------- | --------------------------------------------------------------------------- |
| Schijfcapaciteit: 4 TB (individuele schijf)/ 35 TB (cumulatief totaal van alle schijven) <br>Momentopnamecapaciteit: 10 TB | Tot 50 gigabit per seconde voor inbound<sup>1</sup> + outbound<sup>2</sup> |

<sup>1</sup> Alle gegevens (aanvragen) die naar een opslagaccount worden verzonden

<sup>2</sup> Alle gegevens (antwoorden) die worden ontvangen van een opslagaccount

Een premium paginablobaccount is een account voor algemene doeleinden dat is geconfigureerd voor premium prestaties. V2-opslagaccounts voor algemene doeleinden worden aanbevolen.

Als u premium paginablob-opslagaccounts gebruikt voor niet-beheerde schijven en uw toepassing de schaalbaarheidsdoelen van één opslagaccount overschrijdt, raadt Microsoft u aan te migreren naar beheerde schijven. Zie overzicht van Azure [Disk Storage voor Windows VM's](../../virtual-machines/windows/managed-disks-overview.md) of [Azure Disk Storage voor Linux VM's voor](../../virtual-machines/linux/managed-disks-overview.md)meer informatie over beheerde schijven.

Als u niet migreren naar beheerde schijven, bouwt u uw toepassing om meerdere opslagaccounts te gebruiken en uw gegevens over die opslagaccounts te verdelen. Als u bijvoorbeeld 51 TB-schijven wilt koppelen aan meerdere VM's, spreidt u deze over twee opslagaccounts. 35 TB is de limiet voor één premium opslagaccount. Zorg ervoor dat één premium prestatieopslagaccount nooit meer dan 35 TB aan ingerichte schijven heeft.

## <a name="see-also"></a>Zie ook

- [Schaalbaarheid en prestatiedoelen voor standaardopslagaccounts](../common/scalability-targets-standard-account.md)
- [Schaalbaarheidsdoelen voor premium block blob-opslagaccounts](../blobs/scalability-targets-premium-block-blobs.md)
- [Azure-abonnementslimieten en -quota](../../azure-resource-manager/management/azure-subscription-service-limits.md)
