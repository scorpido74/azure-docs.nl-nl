---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 10/12/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 8eff9da82fdfa5749fd1c2bc04652d5c8ce8dfd2
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518051"
---
![Scherm opname van f i o-uitvoer met r = 22.8 k gemarkeerd.](media/vm-disk-performance/utilization-metrics-example/fio-output.jpg)

Het Standard_D8s_v3 kan een totaal van 28.600 IOPS opleveren. Door de metrische gegevens te gebruiken, gaan we kijken wat er gebeurt en hoe we de opslag-i/o-knel punt kunnen identificeren. Selecteer **metrische gegevens**in het linkerdeel venster:

![Scherm afbeelding met metrische gegevens gemarkeerd in het linkerdeel venster.](media/vm-disk-performance/utilization-metrics-example/metrics-menu.jpg)

Laten we eerst kijken naar onze in het **cache geheugen geplaatste IOPS percentage van verbruikte** gegevens:

![Scherm opname van V M in cache opgeslagen I. P S verbruikt percentage.](media/vm-disk-performance/utilization-metrics-example/vm-cached.jpg)

Met deze metrische gegevens wordt aangegeven dat 61% van de 16.000 IOPS die aan de in cache geplaatste IOPS op de VM zijn toegewezen, wordt gebruikt. Dit percentage betekent dat de opslag-i/o-bottleneck zich niet bevindt op de schijven die in de cache zijn opgeslagen omdat deze niet 100% zijn. Laten we nu eens kijken naar onze **virtuele machine ongecached aantal verbruikte IOPS percentage** voor waarde:

![Scherm opname van V M, niet in cache geplaatste I & 1 S verbruikt percentage.](media/vm-disk-performance/utilization-metrics-example/vm-uncached.jpg)

Deze waarde is 100%. Hiermee wordt aangegeven dat alle 12.800 IOPS die aan de niet in cache geplaatste IOPS op de VM zijn toegewezen, worden gebruikt. Eén manier om dit probleem op te lossen is het wijzigen van de grootte van de VM naar een grotere grootte die de extra IO kan afhandelen. Maar voordat we dat doen, kijken we naar de gekoppelde schijf om erachter te komen hoeveel IOPS er worden weer gegeven. Controleer de besturingssysteem schijf door te kijken naar het **percentage verbruikte schijf van het besturings systeem**:

![Scherm opname van de O S schijf I O P S verbruikt percentage.](media/vm-disk-performance/utilization-metrics-example/os-disk.jpg)

Deze metriek vertelt ons dat ongeveer 90% van de 5.000 IOPS die is ingericht voor deze P30-besturingssysteem schijf wordt gebruikt. Dit percentage betekent dat er geen knel punt is op de besturingssysteem schijf. Nu gaan we de gegevens schijven die zijn gekoppeld aan de virtuele machine controleren door te kijken naar de **gegevens schijf percentage verbruikte geconsumeerde**%:

![Scherm opname van de gegevens schijf I O P S verbruikt percentage.](media/vm-disk-performance/utilization-metrics-example/data-disks-no-splitting.jpg)

Met deze metrische gegevens wordt aangegeven dat het gemiddelde aantal verbruikte IOPS-percentages voor alle gekoppelde schijven ongeveer 42% is. Dit percentage wordt berekend op basis van de IOPS die worden gebruikt door de schijven en die niet worden bediend vanuit de host-cache. Laten we in deze metrische gegevens dieper inzoomen door *splitsingen* op deze metrische gegevens toe te passen en te splitsen door de LUN-waarde:

![Scherm opname van de gegevens schijf I O P S verbruikt percentage met splitsen.](media/vm-disk-performance/utilization-metrics-example/data-disks-splitting.jpg)

Met deze metrische gegevens wordt aangegeven dat aan de aan LUN 3 en 2 gekoppelde gegevensschijven ongeveer 85% van de ingerichte IOPS wordt gebruikt. Hier volgt een overzicht van hoe de IO eruit ziet vanuit de architectuur van de VM en de schijven:

![Diagram van het voor beeld van opslag van I/O-metrische gegevens.](media/vm-disk-performance/utilization-metrics-example/metrics-diagram.jpg)
