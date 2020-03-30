---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/30/2019
ms.author: zivr
ms.custom: include file
ms.openlocfilehash: 3215f5952daef053c94432bc8fdef15e1775047a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73171095"
---
Als u VM's in één regio plaatst, vermindert u de fysieke afstand tussen de instanties. Door ze binnen één beschikbaarheidszone te plaatsen, komen ze ook fysiek dichter bij elkaar. Naarmate de Azure-voetafdruk echter toeneemt, kan één beschikbaarheidszone meerdere fysieke datacenters omvatten, wat kan leiden tot een netwerklatentie die van invloed is op uw toepassing. 

Als u VM's zo dicht mogelijk wilt plaatsen en de laagst mogelijke latentie wilt bereiken, moet u deze implementeren in een plaatsingsgroep voor nabijheid.

Een plaatsingsgroep voor nabijheid is een logische groepering die wordt gebruikt om ervoor te zorgen dat Azure-rekenbronnen fysiek dicht bij elkaar staan. Plaatsingsgroepen voor nabijheidzijn handig voor workloads waarbij lage latentie een vereiste is.


- Lage latentie tussen stand-alone VM's.
- Lage latentie tussen VM's in één beschikbaarheidsset of een virtuele machineschaalset. 
- Lage latentie tussen stand-alone VM's, VM's in meerdere beschikbaarheidssets of meerdere schaalsets. U meerdere rekenresources in één plaatsingsgroep hebben om een toepassing met meerdere niveaus samen te brengen. 
- Lage latentie tussen meerdere toepassingslagen met verschillende hardwaretypen. Als u bijvoorbeeld de backend uitvoert met M-reeksen in een beschikbaarheidsset en de front-end op een Exemplaar uit de D-reeks, in een schaalset, in één plaatsingsgroep voor nabijheid.


![Afbeelding voor plaatsingsgroepen voor nabijheid](./media/virtual-machines-common-ppg/ppg.png)

## <a name="using-proximity-placement-groups"></a>Plaatsingsgroepen voor nabijheid gebruiken 

Een plaatsingsgroep voor nabijheid is een nieuw brontype in Azure. U moet er een maken voordat u deze gebruikt met andere bronnen. Eenmaal gemaakt, kan het worden gebruikt met virtuele machines, beschikbaarheid sets, of virtuele machine schaal sets. U geeft een nabijheidsplaatsingsgroep op bij het maken van rekenresources die de groeps-id voor nabijheidsplaatsingsplaatsing en opgeeft. 

U een bestaande resource ook verplaatsen naar een groep voor plaatsing in de nabijheid. Wanneer u een resource naar een plaatsingsgroep voor nabijheid verplaatst, moet u het item eerst stoppen (detoewijzen), omdat het mogelijk wordt opnieuw geïmplementeerd in een ander datacenter in de regio, zodat u voldoet aan de beperking van de colocatie. 

In het geval van beschikbaarheidssets en virtuele machineschaalsets moet u de groep voor nabijheidsplaatsing instellen op resourceniveau in plaats van op de afzonderlijke virtuele machines. 

Een plaatsingsgroep voor nabijheid is een colocatiebeperking in plaats van een vastkoppelingsmechanisme. Het is vastgemaakt aan een specifiek datacenter met de implementatie van de eerste bron om het te gebruiken. Zodra alle resources met behulp van de groep nabijheidsplaatsing zijn gestopt (deallocated) of verwijderd, wordt deze niet meer vastgemaakt. Daarom is het belangrijk om bij het gebruik van een plaatsingsgroep voor de nabijheid met meerdere VM-series, waar mogelijk alle vereiste typen vooraf in een sjabloon op te geven of een implementatiereeks te volgen die uw kansen op een succesvolle implementatie zal verbeteren. Als uw implementatie mislukt, start u de implementatie opnieuw met de VM-grootte die is mislukt als de eerste grootte die moet worden geïmplementeerd.


## <a name="best-practices"></a>Aanbevolen procedures 
- Gebruik voor de laagste latentie nabijheidsplaatsingsgroepen samen met versnelde netwerken. Zie [Een virtuele Linux-machine maken met accelerated networking](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) of een virtuele [Windows-machine maken met Accelerated Networking](/azure/virtual-network/create-vm-accelerated-networking-powershell?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)voor meer informatie.
- Implementeer alle VM-formaten in één sjabloon. Om te voorkomen dat de landing op hardware die niet alle VM SKU's en maten die u nodig hebt ondersteunt, opnemen alle van de toepassing lagen in een enkele sjabloon, zodat ze allemaal worden ingezet op hetzelfde moment.
- Als u uw implementatie scriptt met PowerShell, CLI of de `OverconstrainedAllocationRequest`SDK, u een toewijzingsfout krijgen. In dit geval moet u alle bestaande VM's stoppen/detoewijzen en de volgorde in het implementatiescript wijzigen om te beginnen met de VM SKU/sizes die zijn mislukt. 
- Wanneer u een bestaande plaatsingsgroep opnieuw gebruikt waaruit VM's zijn verwijderd, moet u wachten tot de verwijdering volledig is voltooid voordat u vm's eraan toevoegt.
- Als latentie uw eerste prioriteit is, plaatst u VM's in een nabijheidsplaatsingsgroep en de hele oplossing in een beschikbaarheidszone. Maar als tolerantie uw hoogste prioriteit is, spreidt u uw instanties over meerdere beschikbaarheidszones (één plaatsingsgroep kan geen zones overspannen).