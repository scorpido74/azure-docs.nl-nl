---
title: Benchmarking van uw toepassing op Azure Disk Storage
description: Bekijk deze voor beelden van een benchmarking van een standaard DS14-VM die is ingericht met Azure Premium Storage-schijven.
author: roygara
ms.author: rogarana
ms.date: 01/11/2019
ms.topic: how-to
ms.service: virtual-machines-linux
ms.subservice: disks
ms.openlocfilehash: cc17ce49517a7af22dcc357fa5f050d28a8ed551
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91279685"
---
# <a name="benchmark-your-application-on-azure-disk-storage"></a>Bench Mark-toepassing op Azure Disk Storage

Benchmarking is het proces van het simuleren van verschillende werk belastingen in uw toepassing en het meten van de toepassings prestaties voor elke werk belasting. Gebruik de stappen die worden beschreven in het [artikel ontwerpen voor hoge prestaties](../premium-storage-performance.md). Door Bench Mark-hulpprogram ma's uit te voeren op de Vm's die de toepassing hosten, kunt u de prestatie niveaus bepalen die uw toepassing kan verzorgen met Premium Storage. In dit artikel bieden we u voor beelden van benchmarking van een standaard DS14-VM die is ingericht met Azure Premium Storage-schijven.

We hebben respectievelijk gebruikgemaakt van common Bench Mark-hulpprogram ma's Iometer en FIO voor Windows en Linux. Deze hulpprogram ma's starten meerdere threads die een productie simuleren, zoals workload, en meten de systeem prestaties. Met de hulpprogram ma's kunt u ook para meters configureren, zoals blok grootte en wachtrij diepte, die normaal gesp roken niet kunnen worden gewijzigd voor een toepassing. Dit biedt u meer flexibiliteit om de maximale prestaties van een grootschalige virtuele machine die is ingericht met Premium-schijven voor verschillende typen toepassings werkbelastingen te verzorgen. Ga naar [Iometer](http://www.iometer.org/) en [FIO](http://freecode.com/projects/fio)voor meer informatie over elk Bench Mark-hulp programma.

Als u de onderstaande voor beelden wilt volgen, maakt u een standaard DS14-VM en koppelt u elf Premium Storage schijven aan de virtuele machine. Van de 11 schijven configureert u 10 schijven met host-caching als ' geen ' en stript u deze in een volume met de naam NoCacheWrites. Configureer host-caching als ' ReadOnly ' op de resterende schijf en maak een volume met de naam CacheReads met deze schijf. Met deze installatie kunt u de maximale Lees-en schrijf prestaties van een standaard DS14-VM bekijken. Voor gedetailleerde stappen voor het maken van een DS14-VM met Premium-schijven gaat u naar [ontwerpen voor hoge prestaties](../premium-storage-performance.md).

[!INCLUDE [virtual-machines-disks-benchmarking](../../../includes/virtual-machines-managed-disks-benchmarking.md)]

## <a name="next-steps"></a>Volgende stappen

Ga naar ons artikel over het [ontwerpen voor hoge prestaties](../premium-storage-performance.md).

In dat artikel maakt u een controle lijst die vergelijkbaar is met uw bestaande toepassing voor het prototype. Met Bench Mark-hulpprogram ma's kunt u de werk belastingen simuleren en de prestaties meten op de prototype toepassing. Op die manier kunt u bepalen welke schijf aanbieding kan overeenkomen of de prestatie vereisten van uw toepassing overschrijden. Vervolgens kunt u dezelfde richt lijnen voor uw productie toepassing implementeren.