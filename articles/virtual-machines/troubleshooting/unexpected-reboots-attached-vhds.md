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
ms.openlocfilehash: d3b54941d38424e71ac800d2e750ac9bbc96cde9
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086870"
---
# <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Problemen met het opnieuw opstarten van Vm's met gekoppelde Vhd's oplossen

Als een virtuele machine (VM) van Azure een groot aantal gekoppelde Vhd's heeft die zich in hetzelfde opslag account bevinden, kunt u de schaalbaarheids doelen voor een afzonderlijk opslag account overschrijden, waardoor de VM onverwacht opnieuw wordt opgestart. Controleer de metrische gegevens van de minuut voor het opslag account (**TotalRequests**/**TotalIngress**/**TotalEgress**) voor pieken die de schaalbaarheids doelen voor een opslag account overschrijden. Bekijk de [metrische gegevens een toename in percentthrottlingerror aan](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#metrics-show-an-increase-in-PercentThrottlingError) voor hulp bij het bepalen of er beperking is opgetreden in uw opslag account.

Over het algemeen worden elke afzonderlijke invoer-of uitvoer bewerking op een VHD van een virtuele machine omgezet om **pagina** -of **put** -bewerkingen op de onderliggende pagina-BLOB te krijgen. Daarom kunt u de geschatte IOPS voor uw omgeving gebruiken om het aantal Vhd's te afstemmen dat u kunt hebben in één opslag account op basis van het specifieke gedrag van uw toepassing. Micro soft raadt aan om 40 of minder schijven te hebben in één opslag account. Zie [Azure Storage schaal baarheid en prestatie doelen](../../storage/common/storage-scalability-targets.md) voor meer informatie over de schaalbaarheids doelen voor opslag accounts, met name de totale aanvraag snelheid en de totale band breedte voor het type opslag account dat u gebruikt.

Als u de schaalbaarheids doelen voor uw opslag account overschrijdt, plaatst u uw Vhd's in meerdere opslag accounts om de activiteit in elk afzonderlijk account te verminderen.
