---
title: Power Options voor Azure Serial Console | Microsoft Documenten
description: VM-stroomopties beschikbaar in de Azure Serial Console
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 8/6/2019
ms.author: alsin
ms.openlocfilehash: 11c2549d7282bae5654ede1ac34d63a43cd0f059
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451203"
---
# <a name="power-options-available-from-the-azure-serial-console"></a>Power Options beschikbaar vanaf de Azure Serial Console

De Azure Serial Console biedt verschillende krachtige hulpprogramma's voor energiebeheer op uw VM- of virtuele machineschaalset. Deze opties voor energiebeheer kunnen voor sommigen verwarrend zijn, dus dit is een overzicht van elk gereedschap en de beoogde use case.

Seriële consolefunctie | Beschrijving | Aanvraag gebruiken
:----------------------|:------------|:---------
VM opnieuw starten | Een sierlijke herstart van uw VM- of virtuele machineschaalsetinstantie. Deze bewerking is hetzelfde als het aanroepen van de functie opnieuw starten die beschikbaar is op de pagina Overzicht. | In de meeste gevallen moet deze optie uw eerste hulpmiddel zijn bij een poging om uw VM opnieuw op te starten. Uw seriële consoleverbinding wordt kort onderbroken en wordt automatisch hervat zodra de VM opnieuw is opgestart.
VM opnieuw instellen | Een krachtige krachtcyclus van uw VM- of virtuele machineschaal die is ingesteld door het Azure-platform. | Deze optie wordt gebruikt om uw besturingssysteem onmiddellijk opnieuw op te starten, ongeacht de huidige status. Aangezien deze bewerking niet sierlijk is, bestaat het risico op gegevensverlies of corruptie. Er is geen onderbreking in de Serial Console-verbinding, wat handig kan zijn voor het verzenden van opdrachten vroeg in de opstarttijd (bijvoorbeeld om GRUB te vinden op een Linux-VM of veilige modus in een Windows-vm).
SysRq - Reboot (b) | Een systeemverzoek om een gast opnieuw op te starten. | Deze functie is alleen van toepassing op Linux-besturingssystemen en vereist [dat SysRq is ingeschakeld](./serial-console-nmi-sysrq.md#system-request-sysrq) in het besturingssysteem. Als het besturingssysteem goed is geconfigureerd voor SysRq, wordt het besturingssysteem met deze opdracht opnieuw opgestart.
NMI (Niet-maskerbare onderbreking) | Een interrupt opdracht, die zal worden geleverd aan het besturingssysteem | Deze bewerking is beschikbaar voor zowel [Windows-](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) als Linux-VM's en vereist dat NMI is ingeschakeld. [Linux](./serial-console-nmi-sysrq.md#non-maskable-interrupt-nmi) Het verzenden van een NMI zal meestal leiden tot uw besturingssysteem te crashen. U uw besturingssysteem configureren om een dumpbestand te maken en vervolgens opnieuw starten bij het ontvangen van het NMI, wat handig kan zijn bij foutopsporing op laag niveau.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over de [Azure Serial Console voor Linux VM's](./serial-console-linux.md)
* Meer informatie over de [Azure Serial Console voor Windows VM's](./serial-console-windows.md)