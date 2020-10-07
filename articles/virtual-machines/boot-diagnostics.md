---
title: Diagnostische gegevens over Azure-opstarten
description: Overzicht van diagnostische gegevens over het opstarten van Azure en beheerde diagnostische gegevens over opstarten
services: virtual-machines
ms.service: virtual-machines
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: a8879bed4160c7cd1bd74cb196ce271964e384f7
ms.sourcegitcommit: 5abc3919a6b99547f8077ce86a168524b2aca350
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91813233"
---
# <a name="azure-boot-diagnostics"></a>Diagnostische gegevens over Azure-opstarten

Diagnostische gegevens over opstarten is een functie voor het opsporen van fouten in azure virtual machines (VM) die de diagnose van opstart fouten van VM'S mogelijk maakt. Met diagnostische gegevens over opstarten kan een gebruiker de status van de virtuele machine observeren terwijl deze opstart door seriële logboek gegevens en scherm afbeeldingen te verzamelen.

## <a name="boot-diagnostics-storage-account"></a>Opslag account voor diagnostische gegevens over opstarten
Bij het maken van een virtuele machine in Azure Portal is diagnostische gegevens over opstarten standaard ingeschakeld. De aanbevolen diagnostische gegevens over het opstarten zijn het gebruik van een beheerd opslag account, omdat het de prestaties van een Azure-VM aanzienlijk verduidelijkt. Dit komt doordat er een Azure Managed Storage-account wordt gebruikt, waarbij de tijd die nodig is om een nieuw gebruikers opslag account te maken voor het opslaan van de diagnostische gegevens over opstarten wordt verwijderd.

Een alternatieve ervaring voor het opstarten van diagnostische gegevens is het gebruik van een door de gebruiker beheerd opslag account. Een gebruiker kan een nieuw opslag account maken of een bestaande gebruiken.

> [!IMPORTANT]
> Azure-klanten betalen niet voor de opslag kosten die zijn gekoppeld aan diagnostische gegevens over opstarten met behulp van een beheerd opslag account tot en met 2020 oktober.
>
> De gegevens-blobs van de opstart diagnose (met inbegrip van Logboeken en moment opnamen) worden opgeslagen in een beheerd opslag account. Klanten worden alleen in rekening gebracht op gebruikte GiBs door de blobs, niet op de ingerichte grootte van de schijf. De momentopname meters worden gebruikt voor de facturering van het beheerde opslag account. Omdat de beheerde accounts worden gemaakt op standaard-LRS of standaard-ZRS, worden klanten op $0,05/GB per maand gefactureerd voor de grootte van de blobs van de diagnostische gegevens. Zie [prijzen van Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/)voor meer informatie over deze prijzen. Klanten krijgen deze kosten te zien die zijn gekoppeld aan hun VM-resource-URI. 

## <a name="boot-diagnostics-view"></a>Weer gave diagnostische gegevens over opstarten
De optie diagnostische gegevens over opstarten bevindt zich op de Blade van de virtuele machine onder het gedeelte *ondersteuning en probleem oplossing* in de Azure Portal. Als u Diagnostische gegevens over opstarten selecteert, wordt een scherm opname en seriële logboek informatie weer gegeven. Het seriële logboek bevat kernel-berichten en de scherm opname is een moment opname van de huidige status van uw virtuele machines. Op basis van als op de virtuele machine Windows of Linux wordt uitgevoerd, wordt bepaald hoe de verwachte scherm afbeelding eruitziet. Voor Windows krijgen gebruikers een bureaublad achtergrond en voor Linux te zien, zien gebruikers een aanmeldings prompt.

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-linux.png" alt-text="Scherm afbeelding van diagnostische gegevens over Linux-opstarten":::
:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-windows.png" alt-text="Scherm afbeelding van diagnostische gegevens over Linux-opstarten":::


## <a name="limitations"></a>Beperkingen
- Diagnostische gegevens over opstarten zijn alleen beschikbaar voor Azure Resource Manager-Vm's. 
- Diagnostische gegevens over het opstarten bieden geen ondersteuning voor Premium Storage-accounts. als een Premium Storage-account wordt gebruikt voor diagnostische gegevens over opstarten, treedt er een `StorageAccountTypeNotSupported` fout op bij het starten van de virtuele machine. 
- Beheerde opslag accounts worden ondersteund in de Resource Manager API-versie 2020-06-01 en hoger.
- De seriële console van Azure is momenteel niet compatibel met een beheerd opslag account voor diagnostische gegevens over opstarten. Meer informatie over [Azure Serial console](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-overview).
- Diagnostische gegevens over opstarten met behulp van een opslag account beheren kunnen momenteel alleen worden toegepast via de Azure Portal. 

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de [Azure Serial console](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-overview) en over het gebruik van diagnostische gegevens over opstarten voor het [oplossen van problemen met virtuele machines in azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics).
