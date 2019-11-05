---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 10/24/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c2ce39c40ad535b4a66349fcbb4529333218555a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512663"
---
Premium Ssd's ondersteunt bursting op elke schijf grootte < = 512 GiB (P20 of lager). Deze schijf grootten ondersteunen de burstisatie op basis van beste inspanningen en gebruiken een tegoed systeem voor het beheren van bursting. De tegoeden worden in een burst-Bucket verzameld wanneer het schijf verkeer lager is dan het ingerichte prestatie doel voor de schijf grootte en crediteert wanneer het verkeer groter wordt dan het doel. Schijf verkeer wordt getraceerd voor zowel IOPS als band breedte in het ingerichte doel.

Schijf bursting is standaard ingeschakeld voor nieuwe implementaties van de schijf grootten die dit ondersteunen. Bestaande schijf grootten, als deze schijf bursting ondersteunen, kunnen bursting op een van de volgende manieren inschakelen:

- Ontkoppel de schijf en koppel deze opnieuw.
- Stop de virtuele machine en start deze.

## <a name="burst-states"></a>Burst-statussen

Alle burst-toepasselijke schijf grootten beginnen met een volledige burst-credit Bucket wanneer de schijf is gekoppeld aan een virtuele machine. De maximale duur van bursting wordt bepaald door de grootte van de burst-credit Bucket. U kunt alleen ongebruikte tegoeden samen voegen tot de grootte van de credit Bucket. Op elk gewenst moment kan de credit Bucket van uw schijf een van de volgende drie statussen hebben: 

- Wanneer het schijf verkeer minder dan het ingerichte prestatie doel gebruikt. U kunt tegoed optellen als schijf verkeer buiten IOPS of bandbreedte doelen of beide. U kunt nog steeds IO-tegoeden verzamelen wanneer u de band breedte van de volledige schijf verbruikt, omgekeerd.  

- Weigeren, wanneer het schijf verkeer meer dan het ingerichte prestatie doel gebruikt. In het burst-verkeer wordt onafhankelijk van de tegoeden van IOPS of band breedte verbruikt. 

- De resterende constante wanneer het schijf verkeer precies op het ingerichte prestatie doel is. 

De schijf grootten die ondersteuning bieden voor bursting in combi natie met de burst-specificaties, worden in de onderstaande tabel samenvatten.

## <a name="regional-availability"></a>Regionale beschikbaarheid

Schijf bursting is momenteel alleen beschikbaar in de regio West-Centraal vs.

## <a name="disk-sizes"></a>Schijfformaten

[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

## <a name="example-scenarios"></a>Voorbeeldscenario's

Hier volgen enkele voor beelden van scenario's om u een beter idee te geven van hoe dit werkt:

- Een veelvoorkomend scenario dat kan profiteren van schijf bursting is sneller opstarten van de VM en het starten van toepassingen op besturingssysteem schijven. Neem als voor beeld een Linux-VM met een installatie kopie van 8 GiB-besturings systemen. Als we een P2-schijf als besturingssysteem schijf gebruiken, is het ingerichte doel 120 IOPS en 25 MBps. Wanneer de VM wordt gestart, is er een lees piek voor de besturingssysteem schijf die de opstart bestanden laadt. Met de introductie van bursting kunt u de maximale burst-snelheid van 3500 IOPS en 170 MBps lezen en de laad tijd versnellen door Mini maal 6x. Na het opstarten van de VM is het verkeers niveau op de besturingssysteem schijf meestal laag, omdat de meeste gegevens bewerkingen door de toepassing worden vergeleken met de gekoppelde gegevens schijven. Als het verkeer onder het ingerichte doel ligt, neemt u tegoed op.

- Als u een extern virtueel bureau blad-omgeving host, wordt het verkeer naar de besturingssysteem schijf aanzienlijk verbeterd wanneer een actieve gebruiker een toepassing start zoals AutoCAD. In dit geval verbruikt burst-verkeer geaccumuleerde tegoeden, zodat u verder kunt gaan dan het ingerichte doel en de toepassing veel sneller kan starten.

- Een P1-schijf heeft een ingericht doel van 120 IOPS en 25 MBps. Als het daad werkelijke verkeer op de schijf 100 IOPS en 20 MBps in het afgelopen interval van 1 seconde was, worden de niet-gebruikte 20 IOs-en 5 MB gecrediteerd naar de burst-Bucket van de schijf. Tegoeden in de burst-Bucket kunnen later worden gebruikt wanneer het verkeer het ingerichte doel overschrijdt, tot aan de maximum burst-limiet. De maximale burst-limiet bepaalt het maximale schijf verkeer, zelfs als u burst-tegoeden hebt om te gebruiken. In dit geval, zelfs als u 10.000 IOs in de credit Bucket hebt, kan een P1-schijf niet meer dan de maximale burst van 3.500 IO per seconde geven.  
