---
title: Problemen oplossen met het opnieuw opstarten van Vm's met gekoppelde Vhd's op virtuele Azure-machines | Microsoft Docs
description: Het oplossen van problemen met het opnieuw opstarten van Vm's.
keywords: SSH-verbinding geweigerd, SSH-fout, Azure SSH, SSH-verbinding is mislukt
services: virtual-machines
author: genlin
manager: dcscontentpm
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines
ms.topic: article
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 3a06db1afd130d936af868d0d20632c3ec4fbfd2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75358523"
---
# <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Problemen met het opnieuw opstarten van Vm's met gekoppelde Vhd's oplossen

Als een virtuele machine (VM) van Azure een groot aantal gekoppelde Vhd's heeft die zich in hetzelfde opslag account bevinden, kunt u de schaalbaarheids doelen voor een afzonderlijk opslag account overschrijden, waardoor de VM onverwacht opnieuw wordt opgestart. Controleer de metrische gegevens van de minuut voor het opslag account (**TotalRequests**/**TotalIngress**/**TotalEgress**) voor pieken die de schaalbaarheids doelen voor een opslag account overschrijden. Bekijk de [metrische gegevens een toename in percentthrottlingerror aan](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#metrics-show-an-increase-in-PercentThrottlingError) voor hulp bij het bepalen of er beperking is opgetreden in uw opslag account.

Over het algemeen worden elke afzonderlijke invoer-of uitvoer bewerking op een VHD van een virtuele machine omgezet om **pagina** -of **put** -bewerkingen op de onderliggende pagina-BLOB te krijgen. Daarom kunt u de geschatte IOPS voor uw omgeving gebruiken om het aantal Vhd's te afstemmen dat u kunt hebben in één opslag account op basis van het specifieke gedrag van uw toepassing. Micro soft raadt aan om 40 of minder schijven te hebben in één opslag account. Zie [schaalbaarheids doelen voor standaard opslag accounts](../../storage/common/scalability-targets-standard-account.md)voor meer informatie over schaalbaarheids doelen voor standaard opslag accounts. Zie [schaalbaarheids doelen voor Premium-pagina-Blob Storage-accounts](../../storage/blobs/scalability-targets-premium-page-blobs.md)voor meer informatie over schaalbaarheids doelen voor Premium-pagina-Blob-opslag accounts.

Als u de schaalbaarheids doelen voor uw opslag account overschrijdt, plaatst u uw Vhd's in meerdere opslag accounts om de activiteit in elk afzonderlijk account te verminderen.
