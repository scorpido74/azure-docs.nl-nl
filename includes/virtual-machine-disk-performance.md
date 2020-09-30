---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 09/25/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 9f5a1010959658e75dcc809b2ee1d6d9222af056
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91539987"
---
Dit artikel beschrijft de prestaties van de schijf en hoe deze werkt bij het combi neren van Azure Virtual Machines en Azure-schijven. Ook wordt beschreven hoe u knel punten kunt vaststellen voor uw schijf-i/o en de wijzigingen die u kunt aanbrengen om de prestaties te optimaliseren.

## <a name="how-does-disk-performance-work"></a>Hoe werkt de prestaties van de schijf?
Virtuele Azure-machines hebben IOPS-en doorvoer prestatie limieten op basis van het type en de grootte van de virtuele machine. Schijven en gegevens schijven van het besturings systeem, die aan virtuele machines kunnen worden gekoppeld, hebben hun eigen IOPs en doorvoer limieten. Wanneer uw toepassing die op uw virtuele machines wordt uitgevoerd, meer IOPS of door Voer aanvraagt dan wat is toegewezen aan de virtuele machine of de gekoppelde schijven, worden de prestaties van uw toepassing afgetopteerd. Als dit gebeurt, ondervindt de toepassing de beste prestaties en kan dit leiden tot een aantal negatieve gevolgen, zoals een verhoogde latentie. Laten we een paar voor beelden gebruiken om dit te versterken. Om deze voor beelden gemakkelijk te kunnen volgen, kijken we alleen naar IOPs, maar dezelfde logica is ook van toepassing op door voer.

## <a name="disk-io-capping"></a>Uitgetopte schijf-i/o
Instellen:
- Standard_D8s_v3 
    - IOPS niet in cache: 12.800
- E30-besturingssysteem schijf
    - IOPS: 500 
- 2 E30-gegevens schijven
    - IOPS: 500

![Limiet voor schijf niveau](media/vm-disk-performance/disk-level-throttling.jpg)

De toepassing die op de virtuele machine wordt uitgevoerd, maakt een aanvraag waarbij 10.000 IOPs voor de virtuele machine is vereist. Deze zijn allemaal toegestaan door de VM, omdat de Standard_D8s_v3 virtuele machine Maxi maal 12.800 IOPs kan uitvoeren. Deze 10.000 IOPs-aanvragen worden vervolgens onderverdeeld in drie verschillende aanvragen naar de verschillende schijven. 1.000 IOPs wordt gevraagd naar de besturingssysteem schijf en er worden 4.500 IOPs voor elke gegevens schijf aangevraagd. Aangezien alle schijven die zijn gekoppeld, E30 schijven zijn en alleen 500 IOPs kunnen afhandelen, worden ze weer gegeven met 500 IOPs. De prestaties van de toepassing worden vervolgens afgetopteerd door de gekoppelde schijven en kunnen alleen 1.500 IOPs verwerken. Het kan worden gebruikt om de prestaties van 10.000 IOPS te verbeteren, als er beter schijven worden uitgevoerd, zoals Premium-SSD P30-schijven.

## <a name="virtual-machine-io-capping"></a>Uitgetopte IO van virtuele machine
Instellen:
- Standard_D8s_v3 
    - IOPS niet in cache: 12.800
- P30-besturingssysteem schijf
    - IOPS: 5.000 
- 2 P30-gegevens schijven 
    - IOPS: 5.000

![Limiet voor virtuele-machine niveau](media/vm-disk-performance/vm-level-throttling.jpg)

De toepassing die op de virtuele machine wordt uitgevoerd, maakt een aanvraag waarvoor 15.000 IOPs is vereist. Helaas wordt de virtuele machine van Standard_D8s_v3 alleen ingericht voor de verwerking van 12.800 IOPs. De toepassing wordt daarom beperkt door de limieten van de virtuele machine en moet vervolgens de toegewezen 12.800 IOPs toewijzen. De aangevraagde 12.800-IOPs worden vervolgens onderverdeeld in drie verschillende aanvragen naar de verschillende schijven. 4.267 IOPs wordt gevraagd naar de besturingssysteem schijf en er worden 4.266 IOPs voor elke gegevens schijf aangevraagd. Aangezien alle schijven die zijn gekoppeld, P30-schijven zijn, die 5.000 IOPs kunnen verwerken, reageren ze op hun aangevraagde bedragen.