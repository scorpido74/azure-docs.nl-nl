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
ms.openlocfilehash: 14e74bfbcd087ccc1d8c5f2f10a8e44ed37cce84
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92016552"
---
![Menu metrische gegevens](media/vm-disk-performance/utilization-metrics-example/fio-output.jpg)

Het Standard_D8s_v3 kan echter een totaal van 28.600 IOPs opleveren, met behulp van de metrische gegevens kunnen onderzoeken wat er gebeurt en de opslag-i/o-bottleneck identificeren. Ga eerst naar het menu aan de rechter kant van de knop metrische gegevens en selecteer dit:

![Menu metrische gegevens](media/vm-disk-performance/utilization-metrics-example/metrics-menu.jpg)

Laten we eerst kijken naar onze in het **cache geheugen geplaatste IOPS percentage van verbruikte** gegevens:

![Percentage verbruikte IOPS in cache geheugen](media/vm-disk-performance/utilization-metrics-example/vm-cached.jpg)

Deze metriek vertelt ons van de 16.000 IOPs toegewezen aan de IOPs in de cache op de VM, 61% wordt gebruikt. Dit betekent dat de opslag-i/o-bottleneck zich niet bevindt op de schijven die in de cache zijn opgeslagen omdat deze niet 100% zijn. Laten we nu eens kijken naar onze **virtuele machine ongecached IOPS verbruikd percentage** metrische gegevens:

![VM ongecached percentage verbruikte IOPS](media/vm-disk-performance/utilization-metrics-example/vm-uncached.jpg)

Deze waarde is 100%, waardoor alle 12.800-IOPs die aan de niet in cache geplaatste IOPs op de VM zijn toegewezen, worden gebruikt. Eén manier om dit probleem op te lossen is door het wijzigen van de grootte van de VM naar een grotere grootte die de extra IO kan afhandelen. Maar voordat we dat doen, gaan we kijken naar de schijf die is gekoppeld om te zien hoeveel IOPs er worden weer gegeven. Laten we eerst kijken naar de besturingssysteem schijf door te kijken naar het **percentage verbruikte schijf van het besturings systeem**:

![Verbruikte percentage van IOPS-schijf van besturings systeem](media/vm-disk-performance/utilization-metrics-example/os-disk.jpg)

Met deze metriek wordt aangegeven dat het 5.000 IOPs dat is ingericht voor deze P30-besturingssysteem schijf, ongeveer 90% van het bestand wordt gebruikt. Dit betekent dat u hier geen bottleneck kunt vinden op de besturingssysteem schijf. Nu gaan we kijken naar de gegevens schijven die zijn gekoppeld aan de virtuele machine door te kijken naar de **gegevens schijf geconsumeerd percentage van IOPS**:

![Percentage verbruikte gegevens schijf (IOPS)](media/vm-disk-performance/utilization-metrics-example/data-disks-no-splitting.jpg)

Deze metriek vertelt ons dat het gemiddelde aantal verbruikte IOPs-percentages voor alle gekoppelde schijven ongeveer 42% is. Dit percentage wordt berekend op basis van de IOPs die worden gebruikt door de schijven en worden niet vanuit de host-cache geleverd. Laten we in deze metrische gegevens dieper inzoomen om te zien hoe u een **splitsing** toepast op deze metrische gegevens en splitst door de LUN-waarde:

![Percentage van IOPS verbruikte gegevens schijf met splitsen](media/vm-disk-performance/utilization-metrics-example/data-disks-splitting.jpg)

Deze metriek vertelt ons de gegevens schijven die zijn gekoppeld aan LUN 3 en 2 worden gebruikt rond 85% van hun ingerichte IOPs. Hier volgt een overzicht van de werking van de IO in de architectuur van de VM en de schijven:

![Voorbeeld diagram van metrische opslag-i/o](media/vm-disk-performance/utilization-metrics-example/metrics-diagram.jpg)
