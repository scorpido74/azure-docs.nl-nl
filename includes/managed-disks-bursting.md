---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/29/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 84736b7f1dcdf8b186fddbced5dd773e008c0dd2
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887430"
---
Schijfbarsten wordt ondersteund voor premium SSD's. Bursting wordt ondersteund op alle premium SSD-schijfformaten <= 512 GiB (P20 of lager). Deze schijfformaten ondersteunen barsten op basis van de beste inspanning en maken gebruik van een kredietsysteem om barsten te beheren. Credits accumuleren in een burst bucket wanneer schijfverkeer lager is dan het ingerichte prestatiedoel voor hun schijfgrootte en verbruiken credits wanneer het verkeer buiten het doel barst. Schijfverkeer wordt bijgehouden tegen zowel IOPS als bandbreedte in het beoogde doel. Schijfbarsten omzeilt geen beperkingen van de virtuele machine (VM) op IOPS of doorvoer.

Schijfbarsten is standaard ingeschakeld op nieuwe implementaties van de schijfformaten die het ondersteunen. Bestaande schijfformaten kunnen, als ze schijfbarsten ondersteunen, het bursten via een van de volgende methoden inschakelen:

- Maak de schijf los en bevestig deze opnieuw.
- Stop en start de VM.

## <a name="burst-states"></a>Burst-statussen

Alle burst toepasselijke schijfformaten beginnen met een volledige burst-tebucket wanneer de schijf is aangesloten op een virtuele machine. De maximale duur van het barsten wordt bepaald door de grootte van de burst credit emmer. U alleen ongebruikte credits verzamelen tot de grootte van de kredietemmer. Op elk moment van de tijd, uw schijf burst credit emmer kan worden in een van de volgende drie staten: 

- Accruing, wanneer het schijfverkeer minder dan het ingerichte prestatiedoel gebruikt. U krediet verzamelen als schijfverkeer hoger is dan IOPS of bandbreedtedoelen of beide. U nog steeds IO-credits verzamelen wanneer u volledige schijfbandbreedte verbruikt, vice versa.  

- Dalend wanneer het schijfverkeer meer gebruikt dan het beoogde prestatiedoel. Het burst-verkeer zal onafhankelijk credits van IOPS of bandbreedte verbruiken. 

- Constant blijven, wanneer het schijfverkeer precies op het beoogde prestatiedoel ligt. 

De schijfformaten die bursting-ondersteuning bieden, samen met de burst-specificaties, worden samengevat in de onderstaande tabel.

## <a name="regional-availability"></a>Regionale beschikbaarheid

Schijfbarsten is beschikbaar in alle regio's in de openbare cloud.

## <a name="disk-sizes"></a>Schijfformaten

[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

## <a name="example-scenarios"></a>Voorbeeldscenario 's

Om u een beter idee te geven van hoe dit werkt, volgen hier een paar voorbeeldscenario's:

- Een veelvoorkomend scenario dat kan profiteren van het barsten van de schijf is sneller VM opstarten en applicatie te starten op OS-schijven. Neem een Linux VM met een 8 GiB OS afbeelding als voorbeeld. Als we een P2-schijf als de OS-schijf gebruiken, is het beoogde doel 120 IOPS en 25 MiB. Wanneer VM wordt gestart, wordt er een leespiek naar de OS-schijf weergegeven die de opstartbestanden laadt. Met de introductie van barsten, u lezen op de maximale burst snelheid van 3500 IOPS en 170 MiB, het versnellen van de laadtijd met ten minste 6x. Na het opstarten van de VM is het verkeersniveau op de OS-schijf meestal laag, omdat de meeste gegevensbewerkingen door de toepassing tegen de aangesloten gegevensschijven zijn. Als het verkeer onder het ingerichte doel ligt, verzamelt u credits.

- Als u een externe virtuele bureaublad-omgeving host, neemt het leesverkeer naar de OS-schijf aanzienlijk toe wanneer een actieve gebruiker een toepassing als AutoCAD start. In dit geval verbruikt burst-verkeer geaccumuleerde credits, zodat u verder gaan dan het ingerichte doel en de toepassing veel sneller starten.

- Een P1 schijf heeft een ingerichte doelstelling van 120 IOPS en 25 MiB. Als het werkelijke verkeer op de schijf was 100 IOPS en 20 MiB in de afgelopen 1 seconde interval, dan is de ongebruikte 20 IOs en 5 MB worden bijgeschreven op de burst emmer van de schijf. Credits in de burst bucket kunnen later worden gebruikt wanneer het verkeer het ingerichte doel overschrijdt, tot de maximale burst limiet. De maximale burst-limiet definieert het plafond van schijfverkeer, zelfs als u burst-credits hebt om van te consumeren. In dit geval, zelfs als u 10.000 IOs in de kredietemmer hebt, kan een P1-schijf niet meer uitgeven dan de maximale burst van 3.500 IO per seconde.  
