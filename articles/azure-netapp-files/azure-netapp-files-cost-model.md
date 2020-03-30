---
title: Kostenmodel voor Azure NetApp-bestanden | Microsoft Documenten
description: Beschrijft het kostenmodel voor Azure NetApp-bestanden voor het beheren van kosten vanuit de service.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: b-juche
ms.openlocfilehash: aea783b818550b8219e1a0498256280f61f678e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70995105"
---
# <a name="cost-model-for-azure-netapp-files"></a>Kostenmodel voor Azure NetApp Files 

Als u inzicht geeft in het kostenmodel voor Azure NetApp Files, u uw uitgaven vanuit de service beheren.

## <a name="calculation-of-capacity-consumption"></a>Berekening van het capaciteitsverbruik

Azure NetApp Files wordt gefactureerd op ingerichte opslagcapaciteit.  De ingerichte capaciteit wordt toegewezen door capaciteitspools te creëren.  Capaciteitspools worden gefactureerd op basis van $/provisioned-GiB/month in stappen per uur. De minimale grootte voor een enkele capaciteitspool is 4 TiB en capaciteitspools kunnen vervolgens in stappen van 1-TiB worden uitgebreid. Volumes worden gemaakt binnen capaciteitspools.  Aan elk volume wordt een quotum toegewezen dat afneemt van de capaciteit met zwembaden. Het quotum dat aan volumes kan worden toegewezen varieert van minimaal 100 GiB tot maximaal 100 TiB.  

Voor een actief volume is het capaciteitsverbruik ten opzichte van quota gebaseerd op logische (effectieve) capaciteit.

Als het werkelijke capaciteitsverbruik van een volume hoger is dan het opslagquotum, kan het volume blijven groeien. Schrijven is nog steeds toegestaan zolang de werkelijke volumegrootte lager is dan de systeemlimiet (100 TiB).  

De totale gebruikte capaciteit in een capaciteitsgroep ten opzichte van de ingerichte hoeveelheid is de som van het grootste deel van het toegewezen quotum of het werkelijke verbruik van alle hoeveelheden binnen de pool: 

   ![Berekening van de totale gebruikte capaciteit](../media/azure-netapp-files/azure-netapp-files-total-used-capacity.png)

Het onderstaande diagram illustreert deze concepten.  
* We hebben een capaciteitspool met 4 TiB van ingerichte capaciteit.  De pool bevat drie delen.  
    * Deel 1 krijgt een quotum van 2 TiB en heeft 800 GiB aan consumptie.  
    * Deel 2 krijgt een quotum van 1 TiB en heeft 100 GiB verbruik.  
    * Deel 3 krijgt een quotum van 500 GiB, maar heeft 800 GiB aan consumptie (overschrijding).  
* De capaciteitspool wordt gemeten voor 4 TiB capaciteit (de ingerichte hoeveelheid).  
    3.8 TiB van capaciteit wordt verbruikt (2 TiB en 1 TiB van quota van volumes 1 en 2, en 800 GiB van daadwerkelijk verbruik voor volume 3). En 200 GiB van de capaciteit blijft.

   ![Capaciteitspool met drie volumes](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-three-vols.png)

## <a name="overage-in-capacity-consumption"></a>Overschrijding van het capaciteitsverbruik  

Wanneer de totale gebruikte capaciteit van een pool de ingerichte capaciteit overschrijdt, zijn gegevensschrijfbewerkingen nog steeds toegestaan.  Als de gebruikte capaciteit van de pool na de respijtperiode (een uur) nog steeds groter is dan de ingerichte capaciteit, wordt de omvang van de pool automatisch verhoogd in stappen van 1 TiB totdat de ingerichte capaciteit groter is dan de totale gebruikte capaciteit.  Bijvoorbeeld, in de bovenstaande afbeelding, als volume 3 blijft groeien en het werkelijke verbruik 1,2 TiB bereikt, wordt de pool na de respijtperiode automatisch aangepast tot 5 TiB.  Het resultaat is dat de ingerichte poolcapaciteit (5 TiB) de gebruikte capaciteit overschrijdt (4,2 TiB).  

## <a name="manual-changes-of-the-pool-size"></a>Handmatige wijzigingen van de grootte van de groep  

U de grootte van het zwembad handmatig vergroten of verkleinen. De volgende beperkingen zijn echter van toepassing:
* Service minimum- en maximumlimieten  
    Zie het artikel over [resourcelimieten](azure-netapp-files-resource-limits.md).
* Een verhoging van 1-TiB na de eerste 4-TiB minimumaankoop
* Een minimale factureringsverhoging van één uur
* De ingerichte poolgrootte mag niet lager zijn dan de totale gebruikte capaciteit in de pool.

## <a name="behavior-of-maximum-size-pool-overage"></a>Gedrag van maximale zwembadoverschrijding   

De maximale grootte van een capaciteitsgroep die u maken of het formaat ervan wijzigen, is 500 TiB.  Wanneer de totale gebruikte capaciteit in een capaciteitspool meer dan 500 TiB bedraagt, zullen de volgende situaties optreden:
* Gegevens schrijven zal nog steeds worden toegestaan (als het volume lager is dan het systeem maximum van 100 TiB).
* Na de respijtperiode van één uur wordt de pool automatisch aangepast in stappen van 1-TiB, totdat de in de totale gebruikte capaciteit van de pool groter is dan de totale gebruikte capaciteit.
* De extra ingerichte en gefactureerde poolcapaciteit van meer dan 500 TiB kan niet worden gebruikt om volumequota toe te wijzen. Het kan ook niet worden gebruikt om de prestaties QoS grenzen uit te breiden.  
    Bekijk [serviceniveaus](azure-netapp-files-service-levels.md) over prestatielimieten en QoS-grootte.

Het onderstaande diagram illustreert deze concepten:
* We hebben een capaciteitspool met een Premium-opslaglaag en een capaciteit van 500 TiB. De pool bevat negen delen.
    * De volumes 1 tot en met 8 krijgen een quotum van elk 60 TiB.  De totale gebruikte capaciteit bedraagt 480 TiB.  
        Elk volume heeft een QoS-limiet van 3,75 GiB/s doorvoer (60 TiB * 64 MiB/s).  
    * Deel 9 krijgt een quotum van 20 TiB.  
        Deel 9 heeft een QoS-limiet van 1,25 GiB/s doorvoer (20 TiB * 64 MiB/s).
* Deel 9 is een overschrijdingsscenario. Het heeft 25 TiB van het werkelijke verbruik.  
    * Na de respijtperiode van een uur wordt de capaciteitspool aangepast tot 505 TiB.  
        Dat wil zeggen, de totale gebruikte capaciteit = 8 * 60-TiB quotum voor de volumes 1 tot en met 8, en 25 TiB van het werkelijke verbruik voor volume 9.
    * De gefactureerde capaciteit is 505 TiB.
    * Het volumequotum voor volume 9 kan niet worden verhoogd (omdat het totale toegewezen quotum voor de groep niet hoger mag zijn dan 500 TiB).
    * Extra QoS-doorvoer kan niet worden toegewezen (omdat de totale QoS voor de pool nog steeds is gebaseerd op 500 TiB).

   ![Capaciteitspool met negen volumes](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-nine-vols.png)

## <a name="capacity-consumption-of-snapshots"></a>Capaciteitsverbruik van momentopnamen 

Het capaciteitsverbruik van momentopnamen in Azure NetApp-bestanden wordt in rekening gebracht ten opzichte van het quotum van het bovenliggende volume.  Als gevolg hiervan deelt het dezelfde factureringsratio als de capaciteitspool waartoe het volume behoort.  In tegenstelling tot het actieve volume wordt het momentopnameverbruik echter gemeten op basis van de verbruikte incrementele capaciteit.  Azure NetApp Files snapshots zijn verschillend van aard. Afhankelijk van de wijzigingssnelheid van de gegevens verbruiken de momentopnamen vaak veel minder capaciteit dan de logische capaciteit van het actieve volume. Stel dat u een momentopname hebt van een 500-GiB-volume dat slechts 10 GiB-verschillende gegevens bevat. De capaciteit die wordt in rekening gebracht tegen het volumequotum voor die momentopname zou 10 GiB zijn, niet 500 GiB. 

## <a name="next-steps"></a>Volgende stappen

* [Prijspagina Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/)
* [Serviceniveau's voor Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Resourcelimieten voor Azure NetApp Files](azure-netapp-files-resource-limits.md)
