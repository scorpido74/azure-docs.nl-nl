---
title: Diagnostische gegevens over Azure-opstarten
description: Overzicht van diagnostische gegevens over het opstarten van Azure en beheerde diagnostische gegevens over opstarten
services: virtual-machines
ms.service: virtual-machines
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: 0b3e1b3bc296676c44eddf34b35a0d4e06d3b8c4
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2020
ms.locfileid: "90007329"
---
# <a name="azure-boot-diagnostics"></a>Diagnostische gegevens over Azure-opstarten

Diagnostische gegevens over opstarten is een functie voor het opsporen van fouten in azure virtual machines (VM) die de diagnose van opstart fouten van VM'S mogelijk maakt. Met diagnostische gegevens over opstarten kan een gebruiker de status van de virtuele machine observeren terwijl deze opstart door seriële logboek gegevens en scherm afbeeldingen te verzamelen.

## <a name="boot-diagnostics-storage-account"></a>Opslag account voor diagnostische gegevens over opstarten
Bij het maken van een virtuele machine in Azure Portal is diagnostische gegevens over opstarten standaard ingeschakeld. De aanbevolen diagnostische gegevens over het opstarten zijn het gebruik van een beheerd opslag account, omdat het de prestaties van een Azure-VM aanzienlijk verduidelijkt. Dit komt doordat er een Azure Managed Storage-account wordt gebruikt, waarbij de tijd die nodig is om een nieuw gebruikers opslag account te maken voor het opslaan van de diagnostische gegevens over opstarten wordt verwijderd.

Een alternatieve ervaring voor het opstarten van diagnostische gegevens is het gebruik van een door de gebruiker beheerd opslag account. Een gebruiker kan een nieuw opslag account maken of een bestaande gebruiken.

> [!IMPORTANT]
> Azure-klanten betalen niet voor de opslag kosten die zijn gekoppeld aan diagnostische gegevens over opstarten met behulp van een beheerd opslag account tot en met 2020 oktober.

## <a name="boot-diagnostics-view"></a>Weer gave diagnostische gegevens over opstarten
De optie diagnostische gegevens over opstarten bevindt zich op de Blade van de virtuele machine onder het gedeelte *ondersteuning en probleem oplossing* in de Azure Portal. Als u Diagnostische gegevens over opstarten selecteert, wordt een scherm opname en seriële logboek informatie weer gegeven. Het seriële logboek bevat kernel-berichten en de scherm opname is een moment opname van de huidige status van uw virtuele machines. Op basis van als op de virtuele machine Windows of Linux wordt uitgevoerd, wordt bepaald hoe de verwachte scherm afbeelding eruitziet. Voor Windows krijgen gebruikers een bureaublad achtergrond en voor Linux te zien, zien gebruikers een aanmeldings prompt.

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-linux.png" alt-text="Scherm afbeelding van diagnostische gegevens over Linux-opstarten":::
:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-windows.png" alt-text="Scherm opname van Windows diagnostische gegevens over opstarten":::


## <a name="limitations"></a>Beperkingen
- Diagnostische gegevens over opstarten zijn alleen beschikbaar voor Azure Resource Manager-Vm's. 
- Diagnostische gegevens over het opstarten bieden geen ondersteuning voor Premium Storage-accounts. als een Premium Storage-account wordt gebruikt voor diagnostische gegevens over opstarten, treedt er een `StorageAccountTypeNotSupported` fout op bij het starten van de virtuele machine. 
- Beheerde opslag accounts worden ondersteund in de Resource Manager API-versie 2020-06-01 en hoger.
- De seriële console van Azure is momenteel niet compatibel met een beheerd opslag account voor diagnostische gegevens over opstarten. Meer informatie over [Azure Serial console](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-overview).

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de [Azure Serial console](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-overview) en over het gebruik van diagnostische gegevens over opstarten voor het [oplossen van problemen met virtuele machines in azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics).
