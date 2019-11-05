---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: b1287f9c7e946c7b4d035b2ad6301947ffad3cea
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73522736"
---
# <a name="azure-premium-storage-design-for-high-performance"></a>Azure Premium-opslag: ontwerpen voor hoge prestaties

Dit artikel bevat richt lijnen voor het bouwen van toepassingen met hoge prestaties met behulp van Azure Premium Storage. U kunt de instructies in dit document gebruiken in combi natie met best practices voor prestaties die van toepassing zijn op technologieën die door uw toepassing worden gebruikt. Ter illustratie van de richt lijnen hebben we SQL Server uitgevoerd op Premium Storage als voor beeld in dit document gebruikt.

Hoewel we prestatie scenario's voor de opslaglaag in dit artikel adresseren, moet u de toepassingslaag optimaliseren. Als u bijvoorbeeld als host fungeert voor een share point-Farm in azure Premium Storage, kunt u de SQL Server-voor beelden in dit artikel gebruiken om de database server te optimaliseren. Daarnaast optimaliseert u de webserver en toepassings server van de share point-farm om de meeste prestaties te verkrijgen.

Dit artikel helpt u bij het beantwoorden van de volgende veelgestelde vragen over het optimaliseren van de prestaties van toepassingen op Azure Premium Storage.

* Hoe kunt u de prestaties van uw toepassingen meten?  
* Waarom worden de verwachte hoge prestaties niet weer gegeven?  
* Welke factoren zijn van invloed op de prestaties van uw toepassing op Premium Storage?  
* Hoe beïnvloeden deze factoren de prestaties van uw toepassing op Premium Storage?  
* Hoe optimaliseert u het aantal IOPS, de band breedte en de latentie?  

We hebben deze richt lijnen specifiek voor Premium Storage gegeven, omdat werk belastingen die worden uitgevoerd op Premium Storage uiterst prestaties gevoelig zijn. Er zijn voor beelden, waar van toepassing. U kunt ook enkele van deze richt lijnen Toep assen op toepassingen die worden uitgevoerd op IaaS-Vm's met standaard opslag schijven.