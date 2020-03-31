---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 28aab15dc67e051190e8d4e35e92240a56fe54a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67175983"
---
Als er vervolgens servers op het cluster worden uitgevoerd met Windows Server 2008 R2 of Windows Server 2012, moet u controleren of de hotfix [KB2854082](https://support.microsoft.com/kb/2854082) is geïnstalleerd op elk van de on-premises servers of Azure VM's die deel uitmaken van het cluster. Elke server of VM die zich in het cluster bevindt, maar niet in de beschikbaarheidsgroep, moet deze hotfix ook hebben geïnstalleerd.

Download [KB2854082](https://support.microsoft.com/kb/2854082) in de externe desktopsessie voor elk van de clusterknooppunten naar een lokale map. Installeer vervolgens de hotfix op elk clusterknooppunt opeenvolgend. Als de clusterservice momenteel op het clusterknooppunt wordt uitgevoerd, wordt de server opnieuw gestart aan het einde van de hotfix-installatie.

> [!WARNING]
> Het stoppen van de clusterservice of het opnieuw opstarten van de server heeft invloed op de quorumstatus van uw cluster en de beschikbaarheidsgroep en dit kan ertoe leiden dat uw cluster offline gaat. Als u de hoge beschikbaarheid van uw cluster tijdens de installatie wilt behouden, moet u ervoor zorgen dat:
> 
> * Het cluster bevindt zich in een optimale quorumstatus. 
> * Voordat u de hotfix op een knooppunt installeert, zijn alle clusterknooppunten online.
> * Voordat u de hotfix op een ander knooppunt in het cluster installeert, moet u toestaan dat de hotfix-installatie op één knooppunt wordt voltooid, inclusief het opnieuw opstarten van de server.
> 
> 

