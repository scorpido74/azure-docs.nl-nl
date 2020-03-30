---
title: Benchmarking van uw toepassing op Azure Disk Storage
description: Meer informatie over het proces van benchmarking van uw toepassing op Azure.
author: roygara
ms.author: rogarana
ms.date: 01/11/2019
ms.topic: conceptual
ms.service: virtual-machines-windows
ms.subservice: disks
ms.openlocfilehash: 77e542e9bff399e58b433286385864478bcb5076
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75719542"
---
# <a name="benchmarking-a-disk"></a>Benchmarking van een schijf

Benchmarking is het proces van het simuleren van verschillende workloads op uw toepassing en het meten van de toepassingsprestaties voor elke workload. Met behulp van de stappen beschreven in het [ontwerpen voor high performance artikel,](premium-storage-performance.md)hebt u de toepassingsprestatie-eisen verzameld. Door benchmarkingtools uit te voeren op de VM's die de toepassing hosten, u bepalen welke prestatieniveaus uw toepassing kan bereiken met Premium Storage. In dit artikel geven we u voorbeelden van het benchmarken van een Standaard DS14 VM die is uitgerust met Azure Premium Storage-schijven.

We hebben gebruik gemaakt van gemeenschappelijke benchmarking tools Iometer en FIO, voor Windows en Linux respectievelijk. Deze tools spawnen meerdere threads die een productie-achtige werkbelasting simuleren en meten de systeemprestaties. Met behulp van de hulpprogramma's u ook parameters configureren, zoals blokgrootte en wachtrijdiepte, die u normaal gesproken niet wijzigen voor een toepassing. Dit geeft u meer flexibiliteit om de maximale prestaties te leveren op een vm op grote schaal, ingericht met premium schijven voor verschillende typen toepassingsworkloads. Ga voor meer informatie over elke benchmarkingtool naar [Iometer](http://www.iometer.org/) en [FIO.](http://freecode.com/projects/fio)

Als u de onderstaande voorbeelden wilt volgen, maakt u een Standaard DS14-vm en koppelt u 11 Premium-opslagschijven aan de VM. Configureer van de 11 schijven 10 schijven met hostcaching als 'Geen' en streep deze in een volume dat NoCacheWrites heet. Configureer hostcaching als 'Alleenlezen' op de resterende schijf en maak een volume met de naam CacheReads met deze schijf. Met deze instelling u de maximale lees- en schrijfprestaties van een standaard DS14-vm zien. Ga voor gedetailleerde stappen over het maken van een DS14 VM met premium SSD's naar [Ontwerpen voor hoge prestaties.](premium-storage-performance.md)

[!INCLUDE [virtual-machines-disks-benchmarking](../../../includes/virtual-machines-managed-disks-benchmarking.md)]

## <a name="next-steps"></a>Volgende stappen

Ga naar ons artikel over [ontwerpen voor hoge prestaties.](premium-storage-performance.md)

In dat artikel maakt u een checklist die vergelijkbaar is met uw bestaande toepassing voor het prototype. Met behulp van Benchmarking-tools u de workloads simuleren en de prestaties van de prototypetoepassing meten. Op deze plaats u bepalen welke schijfaanbieding kan overeenkomen met of uw toepassingsprestatievereisten kan overtreffen. Vervolgens u dezelfde richtlijnen voor uw productietoepassing implementeren.