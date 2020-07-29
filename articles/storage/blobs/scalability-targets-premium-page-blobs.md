---
title: Schaalbaarheids doelen voor Premium-pagina-Blob Storage-accounts
titleSuffix: Azure Storage
description: Een pagina voor het Blob-opslag account voor Premium-prestaties is geoptimaliseerd voor lees-en schrijf bewerkingen. Dit type opslag account maakt een back-up van een onbeheerde schijf voor een virtuele machine van Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: e134c69f5d602cb4369e9410e3e2b9d3478b11a0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "76756246"
---
# <a name="scalability-and-performance-targets-for-premium-page-blob-storage-accounts"></a>Schaalbaarheids-en prestatie doelen voor Premium-pagina-Blob Storage-accounts

[!INCLUDE [storage-scalability-intro-include](../../../includes/storage-scalability-intro-include.md)]

## <a name="scale-targets-for-premium-page-blob-accounts"></a>Schaal doelen voor Premium-pagina-BLOB-accounts

Een pagina-Blob-opslag account voor Premium-prestaties is geoptimaliseerd voor lees-en schrijf bewerkingen. Dit type opslag account maakt een back-up van een onbeheerde schijf voor een virtuele machine van Azure.

> [!NOTE]
> Micro soft raadt u aan om Managed disks te gebruiken met Azure virtual machines (Vm's), indien mogelijk. Zie [overzicht van Azure Disk Storage voor Windows-vm's](../../virtual-machines/windows/managed-disks-overview.md)voor meer informatie over beheerde schijven.

Voor Premium-pagina-Blob Storage-accounts gelden de volgende schaalbaarheids doelen:

| Totale account capaciteit                            | Totale band breedte voor een lokaal redundante opslag account                     |
| ------------------------------------------------- | --------------------------------------------------------------------------- |
| Schijf capaciteit: 4 TB (afzonderlijke schijf)/35 TB (cumulatief totaal van alle schijven) <br>Moment opname capaciteit: 10 TB | Maxi maal 50 gigabits per seconde voor inkomend<sup>1</sup> + uitgaand<sup>2</sup> |

<sup>1</sup> alle gegevens (aanvragen) die worden verzonden naar een opslag account

<sup>2</sup> alle gegevens (antwoorden) die zijn ontvangen van een opslag account

Een Premium-pagina-BLOB-account is een algemeen account dat is geconfigureerd voor Premium-prestaties. V2-opslag accounts voor algemeen gebruik worden aanbevolen.

Als u opslag accounts voor Premium-pagina-blobs gebruikt voor onbeheerde schijven en uw toepassing de schaalbaarheids doelen van één opslag account overschrijdt, raadt micro soft aan om te migreren naar beheerde schijven. Zie [overzicht van Azure Disk Storage voor Windows-vm's](../../virtual-machines/windows/managed-disks-overview.md) of [Azure Disk Storage overzicht voor Linux-vm's](../../virtual-machines/linux/managed-disks-overview.md)voor meer informatie over beheerde schijven.

Als u niet kunt migreren naar Managed disks, bouwt u uw toepassing voor het gebruik van meerdere opslag accounts en partitioneert u uw gegevens in deze opslag accounts. Als u bijvoorbeeld schijven van 51 TB wilt aansluiten op meerdere Vm's, moet u deze verdelen over twee opslag accounts. 35 TB is de limiet voor één Premium-opslag account. Zorg ervoor dat één Premium-prestatie opslag account nooit meer dan 35 TB aan ingerichte schijven heeft.

## <a name="see-also"></a>Zie tevens

- [Schaalbaarheids-en prestatie doelen voor standaard opslag accounts](../common/scalability-targets-standard-account.md)
- [Schaalbaarheids doelen voor Premium Block Blob Storage-accounts](../blobs/scalability-targets-premium-block-blobs.md)
- [Limieten en quota voor Azure-abonnementen](../../azure-resource-manager/management/azure-subscription-service-limits.md)
