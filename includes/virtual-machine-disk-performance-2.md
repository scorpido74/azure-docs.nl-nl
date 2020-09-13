---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 07/07/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 65f6c239f34775efff6a2ea2e399064a7702606a
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2020
ms.locfileid: "89664146"
---
![Documentatie voor Dsv3](media/vm-disk-performance/dsv3-documentation.jpg)

De maximale door Voer van een schijf **in cache** is de standaard opslag limiet die de virtuele machine kan verwerken. De maximale doorvoer limiet voor opslag **in de cache** is een afzonderlijke limiet bij het inschakelen van de caching van hosts. Het inschakelen van de caching van hosts kan worden uitgevoerd bij het maken van de virtuele machine en het koppelen van schijven. U kunt ook aanpassen om de schijven in de cache in of uit te scha kelen op een bestaande virtuele machine:

![Host-caching](media/vm-disk-performance/host-caching.jpg)

De caching van de host kan worden aangepast aan de vereisten van uw werk belasting voor elke schijf. U kunt instellen dat de cache voor de host alleen-lezen is voor werk belastingen die alleen lees bewerkingen en lezen/schrijven voor werk belastingen die een evenwicht hebben met lees-en schrijf bewerkingen. Als uw werk belasting niet aan een van deze patronen voldoet, kunt u de caching van de host niet gebruiken. 

We gaan verder met een voor beeld met onze Standard_D8s_v3 virtuele machine. Met uitzonde ring van deze tijd gaan we caching van host inschakelen op de schijven en nu is de IOPS-limiet van de VM 16.000 IOPS. Gekoppeld aan de VM zijn drie onderliggende P30-schijven die 5.000 IOPS kunnen verwerken.

Instellen:
- Standard_D8s_v3 
    - IOPS in cache: 16.000
    - IOPS niet in cache: 12.800
- P30-besturingssysteem schijf 
    - IOPS: 5.000
    - Host-caching ingeschakeld 
- 2 P30-gegevens schijven
    - IOPS: 5.000
    - Host-caching ingeschakeld

![Voor beeld van host-caching](media/vm-disk-performance/host-caching-example-without-remote.jpg)

De toepassing die gebruikmaakt van deze Standard_D8s_v3 virtuele machine waarvoor caching is ingeschakeld, maakt nu een aanvraag voor 15.000 IOPS. Deze aanvragen worden opgedeeld als 5.000 IOPS voor elke onderliggende schijf die is gekoppeld en er treedt geen prestatie limiet op.

## <a name="combined-uncached-and-cached-limits"></a>Gecombineerde en in cache opgeslagen limieten

De cache limieten van een virtuele machine zijn gescheiden van de limieten voor de cache. Dit betekent dat u de opslag in de host kunt inschakelen op schijven die zijn gekoppeld aan een virtuele machine, terwijl het niet mogelijk is caching op andere schijven in te scha kelen om ervoor te zorgen dat uw virtuele machines een totale bewerkings-IO krijgen van de limiet van de cache plus de limiet voor niet-cache geheugen. Laten we een voor beeld van dit doen om te versterken hoe deze limieten samen werken en we gaan verder met de Standard_D8s_v3 virtuele machine en Premium-schijven die zijn gekoppeld aan de configuratie.

Instellen:
- Standard_D8s_v3 
    - IOPS in cache: 16.000
    - IOPS niet in cache: 12.800
- P30-besturingssysteem schijf 
    - IOPS: 5.000
    - Host-caching ingeschakeld 
- 2 P30-gegevens schijven X 2
    - IOPS: 5.000
    - Host-caching ingeschakeld
- 2 P30-gegevens schijven X 2
    - IOPS: 5.000
    - Host-caching is uitgeschakeld

![Voor beeld van host-caching met externe opslag](media/vm-disk-performance/host-caching-example-with-remote.jpg)

De toepassing die op Standard_D8s_v3 virtuele machine wordt uitgevoerd, maakt nu een aanvraag voor 25.000 IOPS. Deze aanvraag wordt opgedeeld als 5.000 IOPS voor elke onderliggende schijf die is gekoppeld, waarbij 3 van die schijven gebruikmaakt van host caching en 2 van de schijven. Aangezien de 3 het gebruik van de host-caching binnen de limieten van 16.000 in de cache plaatsvindt, worden deze aanvragen voltooid en worden er geen opslag prestaties in beslag genomen. Omdat de twee schijven die geen gebruik maken van host-caching binnen de limieten van 12.800 in de cache liggen, worden deze aanvragen ook voltooid en wordt er geen sprake is van onbedoeldheid.

## <a name="metrics-for-disk-performance"></a>Metrische gegevens voor schijf prestaties
We hebben metrische gegevens over Azure die inzicht bieden in hoe uw virtuele machines en schijven worden uitgevoerd. Deze metrische gegevens kunnen visueel worden weer gegeven via de Azure Portal of kunnen worden opgehaald via een API-aanroep. Metrische gegevens worden berekend over een interval van één minuut. De volgende metrische gegevens zijn beschikbaar om inzicht te krijgen in de prestaties van VM en schijf-i/o en door Voer:
- **Wachtrij diepte van besturings systeem** : het aantal openstaande i/o-aanvragen dat wacht op het lezen van of schrijven naar de besturingssysteem schijf.
- De **besturingssysteem schijf gelezen bytes per seconde** : het aantal bytes dat in een seconde wordt gelezen van de besturingssysteem schijf.
- **Lees bewerkingen van de besturingssysteem schijf per seconde** : het aantal invoer bewerkingen dat in een seconde van de besturingssysteem schijf wordt gelezen.
- **Schrijf bewerkingen op de besturingssysteem schijf per seconde** : het aantal bytes dat is geschreven in een seconde van de besturingssysteem schijf.
- **Schrijf bewerkingen op de besturingssysteem schijf per seconde** : het aantal uitvoer bewerkingen dat is geschreven in een seconde van de besturingssysteem schijf.
- **Diepte van de gegevens schijf wachtrij** : het aantal openstaande io-aanvragen dat wacht op het lezen van of schrijven naar de gegevens schijf (en).
- **Gegevens schijf gelezen bytes per seconde** : het aantal bytes dat is gelezen in een seconde van de gegevens schijf (s).
- **Lees bewerkingen op de gegevens schijf per seconde** : het aantal invoer bewerkingen dat is gelezen in een seconde van een of meer gegevens schijven.
- **Geschreven bytes per seconde van gegevens schijf** : het aantal bytes dat is geschreven in een seconde van de gegevens schijf (s).
- **Schrijf bewerkingen op de gegevens schijf per seconde** : het aantal uitvoer bewerkingen dat is geschreven in een tweede van een of meer gegevens schijven.
- **Gelezen bytes per seconde** : het totaal aantal bytes dat is gelezen in een seconde van alle schijven die zijn gekoppeld aan een virtuele machine.
- **Lees bewerkingen op de schijf per seconde** : het aantal invoer bewerkingen dat in een seconde wordt gelezen van alle schijven die zijn gekoppeld aan een virtuele machine.
- **Geschreven bytes per seconde** : het aantal bytes dat in een seconde wordt geschreven van alle schijven die zijn gekoppeld aan een virtuele machine.
- **Schrijf bewerkingen op de schijf per seconde** : het aantal uitvoer bewerkingen dat is geschreven in een tweede van alle schijven die zijn gekoppeld aan een virtuele machine.

## <a name="storage-io-utilization-metrics"></a>Metrische gegevens over gebruik van i/o-opslag
Metrische gegevens die helpen bij het vaststellen van de oorzaak van schijf-i/o-limiet:
- **Percentage gebruikte IOPS van gegevens schijf** : het percentage dat is berekend door de gegevens schijf IOPS is voltooid via de ingerichte gegevens schijf IOPS. Als dit bedrag ten 100% bedraagt, wordt er in de toepassing IO afgetopteerd van de IOPS-limiet van uw gegevens schijf.
- **Percentage gebruikte band breedte** van de gegevens schijf-het percentage dat is berekend door de door Voer van de gegevens schijf is voltooid voor de door Voer van de ingerichte gegevens schijf. Als dit bedrag ten 100% bedraagt, wordt er door de toepassing die wordt uitgevoerd, IO-begrensd van de bandbreedte limiet van uw gegevens schijf.
- **Percentage van aantal geconsumeerde besturingssysteem schijf** : het percentage dat is berekend door de schijf-IOPS van het besturings systeem, is voltooid voor de IOPS van de ingerichte schijf. Als dit bedrag op 100% is, wordt er in de toepassing IO afgetoptd van de IOPS-limiet van de besturingssysteem schijf.
- **Percentage gebruikte band breedte OS** -schijf: het percentage dat is berekend door de door Voer van de besturingssysteem schijf is voltooid voor de door Voer van de ingerichte besturingssysteem schijf. Als dit bedrag ten 100% bedraagt, wordt er door de toepassing die wordt uitgevoerd, een IO-beperking van de bandbreedte limiet van uw besturings systeem gebruikt.