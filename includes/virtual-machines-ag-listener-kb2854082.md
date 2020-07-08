---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 28aab15dc67e051190e8d4e35e92240a56fe54a6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "67175983"
---
Als op servers op het cluster Windows Server 2008 R2 of Windows Server 2012 wordt uitgevoerd, moet u controleren of de hotfix [KB2854082](https://support.microsoft.com/kb/2854082) is geïnstalleerd op elk van de on-premises servers of Azure-vm's die deel uitmaken van het cluster. Voor alle servers of VM'S die zich in het cluster bevinden, maar niet in de beschikbaarheids groep, moet deze hotfix ook zijn geïnstalleerd.

Down load [KB2854082](https://support.microsoft.com/kb/2854082) in de extern bureau blad-sessie voor elk cluster knooppunt naar een lokale map. Installeer de hotfix vervolgens sequentieel op elk cluster knooppunt. Als de Cluster service momenteel op het cluster knooppunt wordt uitgevoerd, wordt de server opnieuw opgestart aan het einde van de installatie van de hotfix.

> [!WARNING]
> Het stoppen van de Cluster service of het opnieuw opstarten van de server is van invloed op de quorum status van het cluster en de beschikbaarheids groep. het cluster kan hierdoor mogelijk offline gaan. Als u de maximale Beschik baarheid van uw cluster tijdens de installatie wilt behouden, moet u het volgende doen:
> 
> * Het cluster bevindt zich in een optimale quorum status. 
> * Voordat u de hotfix op een knoop punt installeert, zijn alle cluster knooppunten online.
> * Voordat u de hotfix op een ander knoop punt in het cluster installeert, moet u ervoor zorgen dat de installatie van de hotfix op één knoop punt wordt uitgevoerd, inclusief het volledig opnieuw opstarten van de server.
> 
> 

