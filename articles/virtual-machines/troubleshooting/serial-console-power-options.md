---
title: Energie opties voor Azure seriële console | Microsoft Docs
description: VM-energie opties beschikbaar in de seriële console van Azure
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "75451203"
---
# <a name="power-options-available-from-the-azure-serial-console"></a>Beschik bare energie opties in de Azure seriële console

De seriële console van Azure biedt verschillende krachtige hulpprogram ma's voor energie beheer op uw VM of virtuele-machine schaalset. Deze opties voor energie beheer zijn mogelijk verwarrend voor sommige. Dit is een overzicht van elk hulp programma en de beoogde gebruiks voorbeeld.

Functie seriële console | Description | Use-case
:----------------------|:------------|:---------
VM opnieuw opstarten | De virtuele machine of het exemplaar van de VM-schaalset is zonder problemen opnieuw opgestart. Deze bewerking is hetzelfde als het aanroepen van de herstart-functie die beschikbaar is op de pagina overzicht. | In de meeste gevallen moet deze optie uw eerste hulp programma zijn bij het opnieuw starten van de virtuele machine. Uw seriële console verbinding heeft een korte onderbreking en wordt automatisch hervat zodra de virtuele machine opnieuw is opgestart.
VM opnieuw instellen | Een geforceerde-energie cyclus van uw VM of virtuele-machine schaalset door het Azure-platform. | Deze optie wordt gebruikt om het besturings systeem onmiddellijk opnieuw op te starten, ongeacht de huidige status. Omdat deze bewerking niet kan worden uitgevoerd, is er een risico dat er gegevens verloren gaan of beschadigd raken. Er is geen onderbreking in de seriële console verbinding, wat nuttig kan zijn voor het vroeg in de opstart tijd verzenden van opdrachten (bijvoorbeeld het ophalen van GRUB op een virtuele Linux-machine of in een veilige modus in een Windows-VM).
SysRq-opnieuw opstarten (b) | Een systeem aanvraag om het opnieuw opstarten van een gast af te dwingen. | Deze functie is alleen van toepassing op Linux-besturings systemen en vereist [dat SYSRQ is ingeschakeld](./serial-console-nmi-sysrq.md#system-request-sysrq) in het besturings systeem. Als het besturings systeem correct is geconfigureerd voor SysRq, wordt met deze opdracht de computer opnieuw opgestart.
NMI (niet-maskeer bare interrupt) | Een interrupt-opdracht, die wordt geleverd aan het besturings systeem | Deze bewerking is beschikbaar voor virtuele [Windows](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) -en [Linux](./serial-console-nmi-sysrq.md#non-maskable-interrupt-nmi) -machines en vereist dat NMI is ingeschakeld. Het verzenden van een NMI zorgt er meestal voor dat het besturings systeem vastloopt. U kunt uw besturings systeem zo configureren dat er een dump bestand wordt gemaakt en vervolgens opnieuw wordt opgestart wanneer de NMI wordt ontvangen. Dit kan nuttig zijn bij fout opsporing op laag niveau.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over de [Azure Serial console voor Linux vm's](./serial-console-linux.md)
* Meer informatie over de [Azure Serial console voor Windows-vm's](./serial-console-windows.md)