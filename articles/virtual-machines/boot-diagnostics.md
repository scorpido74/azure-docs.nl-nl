---
title: Diagnostische gegevens over Azure-opstarten
description: Overzicht van diagnostische gegevens over het opstarten van Azure en beheerde diagnostische gegevens over opstarten
services: virtual-machines
ms.service: virtual-machines
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: 9ffd5a6397fa01efcf9aece93333dcb5e5b418cc
ms.sourcegitcommit: 1a0dfa54116aa036af86bd95dcf322307cfb3f83
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/10/2020
ms.locfileid: "88042959"
---
# <a name="azure-boot-diagnostics"></a>Diagnostische gegevens over Azure-opstarten

Diagnostische gegevens over opstarten is een functie voor het opsporen van fouten in azure virtual machines (VM) die de diagnose van opstart fouten van VM'S mogelijk maakt. Met diagnostische gegevens over opstarten kan een gebruiker de status van de virtuele machine observeren terwijl deze opstart door seriële logboek gegevens en scherm afbeeldingen te verzamelen.

## <a name="boot-diagnostics-storage-account"></a>Opslag account voor diagnostische gegevens over opstarten
Diagnostische gegevens over opstarten zijn standaard ingeschakeld voor alle Vm's die zijn gemaakt met behulp van de Azure Portal en een beheerd opslag account gebruiken. Door gebruik te maken van een beheerd opslag account krijgen gebruikers een aanzienlijke verbetering in de implementatie tijd van de VM. Daarom raden wij klanten aan om diagnostische gegevens over opstarten te gebruiken met een beheerd opslag account voor alle virtuele machines.

> [!NOTE]
> Er worden geen kosten in rekening gebracht voor Azure-klanten wanneer wordt gebruikgemaakt van het gebruik van diagnostische gegevens over opstarten met een beheerd opslag account tot 1 oktober 2020.

Een alternatieve diagnose voor diagnostische gegevens over opstarten is het gebruik van een aangepast opslag account. Een gebruiker kan een nieuw opslag account maken of een bestaande gebruiken. Zie [overzicht van opslag](https://docs.microsoft.com/azure/storage/common/storage-account-overview)accounts voor meer informatie over aangepaste opslag accounts.

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-portal.png" alt-text="Scherm afbeelding om te laten zien hoe u Diagnostische gegevens over opstarten inschakelt":::

## <a name="boot-diagnostics-view"></a>Weer gave diagnostische gegevens over opstarten
De optie diagnostische gegevens over opstarten bevindt zich op de Blade van de virtuele machine onder het gedeelte *ondersteuning en probleem oplossing* in de Azure Portal. Als u Diagnostische gegevens over opstarten selecteert, wordt een scherm opname en seriële logboek informatie weer gegeven. Het seriële logboek bevat kernel-berichten en de scherm opname is een moment opname van de huidige status van uw virtuele machines. Op basis van als op de virtuele machine Windows of Linux wordt uitgevoerd, wordt bepaald hoe de verwachte scherm afbeelding eruitziet. Voor Windows krijgen gebruikers een bureaublad achtergrond en voor Linux te zien, zien gebruikers een aanmeldings prompt.

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-linux.png" alt-text="Scherm afbeelding van diagnostische gegevens over Linux-opstarten":::
:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-windows.png" alt-text="Scherm opname van Windows diagnostische gegevens over opstarten":::


## <a name="limitations"></a>Beperkingen
- Diagnostische gegevens over opstarten zijn alleen beschikbaar voor virtuele machines met Azure Resource Manager (ARM). 
- Diagnostische gegevens over het opstarten bieden geen ondersteuning voor Premium Storage-accounts. als een Premium Storage-account wordt gebruikt voor diagnostische gegevens over opstarten, treedt er een `StorageAccountTypeNotSupported` fout op bij het starten van de virtuele machine. 
- Azure Serial console biedt momenteel geen ondersteuning voor een beheerd opslag account voor diagnostische gegevens over opstarten.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de [Azure Serial console](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-overview) en over het gebruik van diagnostische gegevens over opstarten voor het [oplossen van problemen met virtuele machines in azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics).
