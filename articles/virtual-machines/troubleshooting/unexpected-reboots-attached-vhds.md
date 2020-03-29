---
title: Onverwachte reboots van VM's oplossen met gekoppelde VHD's op Azure VM's | Microsoft Documenten
description: Hoe onverwachte reboots van VM's op te lossen.
keywords: ssh-verbinding geweigerd, ssh-fout, azure ssh, SSH-verbinding mislukt
services: virtual-machines
author: genlin
manager: dcscontentpm
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines
ms.topic: article
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 3a06db1afd130d936af868d0d20632c3ec4fbfd2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75358523"
---
# <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Onverwachte reboots van VM's met gekoppelde VHD's oplossen

Als een Azure Virtual Machine (VM) een groot aantal gekoppelde VHD's heeft die zich in hetzelfde opslagaccount bevinden, u de schaalbaarheidsdoelen voor een afzonderlijk opslagaccount overschrijden, waardoor de VM onverwacht opnieuw wordt opgestart. Controleer de minutenstatistieken voor het opslagaccount (**TotalRequests**/**TotalIngress**/**TotalEgress**) op pieken die de schaalbaarheidsdoelen voor een opslagaccount overschrijden. Zie [Statistieken tonen een toename van PercentThrottlingError](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#metrics-show-an-increase-in-PercentThrottlingError) voor hulp bij het bepalen of throttling heeft plaatsgevonden op uw opslagaccount.

In het algemeen vertaalt elke afzonderlijke invoer- of uitvoerbewerking op een VHD van een virtuele machine naar **Pagina ophalen** of **Pagina-bewerkingen op** de onderliggende paginablob plaatsen. Daarom u de geschatte IOPS voor uw omgeving gebruiken om af te stemmen hoeveel VHD's u hebben in één opslagaccount op basis van het specifieke gedrag van uw toepassing. Microsoft raadt aan om 40 of minder schijven in één opslagaccount te hebben. Zie [Schaalbaarheidsdoelen voor standaardopslagaccounts voor](../../storage/common/scalability-targets-standard-account.md)meer informatie over schaalbaarheidsdoelen voor standaardopslagaccounts. Zie [Schaalbaarheidsdoelen voor blob-opslagaccounts voor premium](../../storage/blobs/scalability-targets-premium-page-blobs.md)pagina's voor meer informatie over schaalbaarheidsdoelen voor blob-opslagaccounts met blobs .

Als u de schaalbaarheidsdoelstellingen voor uw opslagaccount overschrijdt, plaatst u uw VHD's in meerdere opslagaccounts om de activiteit in elk afzonderlijk account te verminderen.
