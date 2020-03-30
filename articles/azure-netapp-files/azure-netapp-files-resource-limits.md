---
title: Resourcelimieten voor Azure NetApp-bestanden | Microsoft Documenten
description: Beschrijft limieten voor Azure NetApp Files-bronnen en hoe u resourcelimiet aanvragen.
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
ms.date: 3/13/2020
ms.author: b-juche
ms.openlocfilehash: 36b2d50722a1840e461d6907f440d859c7c82117
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408824"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Resourcelimieten voor Azure NetApp Files

Als u inzicht geeft in resourcelimieten voor Azure NetApp Files, u uw volumes beheren.

## <a name="resource-limits"></a>Bronlimieten

In de volgende tabel worden resourcelimieten voor Azure NetApp-bestanden beschreven:

|  Resource  |  Standaardlimiet  |  Instelbaar via ondersteuningsverzoek  |
|----------------|---------------------|--------------------------------------|
|  Aantal NetApp-accounts per Azure-regio   |  10    |  Ja   |
|  Aantal capaciteitspools per NetApp-account   |    25     |   Ja   |
|  Aantal volumes per capaciteitspool     |    500   |    Ja     |
|  Aantal momentopnamen per volume       |    255     |    Nee        |
|  Aantal subnetten dat is gedelegeerd aan Azure NetApp-bestanden (Microsoft.NetApp/volumes) per Azure Virtual Network    |   1   |    Nee    |
|  Aantal gebruikte IP's in een VNet (inclusief onmiddellijk peered VNets) met Azure NetApp-bestanden   |    1000   |    Ja   |
|  Minimumgrootte van een enkele capaciteitsgroep   |  4 TiB     |    Nee  |
|  Maximale grootte van een enkele capaciteitsgroep    |  500 TiB   |   Nee   |
|  Minimale grootte van één volume    |    100 GiB    |    Nee    |
|  Maximale grootte van één volume     |    100 TiB    |    Nee    |
|  Maximale grootte van één bestand     |    16 TiB    |    Nee    |    
|  Maximale grootte van één map      |    320 MB    |    Nee    |    
|  Maximum aantal bestanden ([maxfiles](#maxfiles)) per volume     |    100 miljoen    |    Ja    |    

Zie [Veelgestelde vragen over capaciteitsbeheer voor](azure-netapp-files-faqs.md#capacity-management-faqs)meer informatie.

## <a name="maxfiles-limits"></a>Maxfiles limieten<a name="maxfiles"></a> 

Azure NetApp-bestandenvolumes hebben een limiet die *maxfiles*wordt genoemd. De maxfiles limiet is het aantal bestanden dat een volume kan bevatten. De maximumbestandenlimiet voor een Azure NetApp-bestandenvolume wordt geïndexeerd op basis van de grootte (quotum) van het volume. De maxfiles limiet voor een volume toeneemt of daalt met het tarief van 20 miljoen bestanden per TiB van ingerichte volumegrootte. 

De service past dynamisch de maxfileslimiet aan voor een volume op basis van de ingerichte grootte. Een volume dat in eerste instantie is geconfigureerd met een grootte van 1 TiB zou bijvoorbeeld een maxfileslimiet van 20 miljoen hebben. Latere wijzigingen in de grootte van het volume zouden resulteren in een automatische aanpassing van de maxfileslimiet op basis van de volgende regels: 

|    Volumegrootte (quotum)     |  Automatische aanpassing van de maxfileslimiet    |
|----------------------------|-------------------|
|    < 1 TiB                 |    20 miljoen     |
|    >= 1 TiB maar < 2 TiB    |    40 miljoen     |
|    >= 2 TiB maar < 3 TiB    |    60 miljoen     |
|    >= 3 TiB maar < 4 TiB    |    80 miljoen     |
|    >= 4 TiB                |    100 miljoen    |

Voor elke volumegrootte u een [ondersteuningsverzoek](#limit_increase) starten om de maximumbestandenlimiet te verhogen tot boven de 100 miljoen.

## <a name="request-limit-increase"></a>Verhoging van de aanvraaglimiet<a name="limit_increase"></a> 

U een Azure-ondersteuningsaanvraag maken om de aanpasbare limieten uit de bovenstaande tabel te verhogen. 

Vanuit het navigatievlak van Azure-portal: 

1. Klik op **Help + ondersteuning**.
2. Klik **op + Nieuw ondersteuningsverzoek**.
3. Geef op het tabblad Basisbeginselen de volgende informatie op: 
    1. Probleemtype: **Service- en abonnementslimieten selecteren (quota)**.
    2. Abonnementen: selecteer het abonnement voor de resource waarvoor het quotum moet worden verhoogd.
    3. Quotumtype: **Selecteer Opslag: azure NetApp-bestandenlimieten**.
    4. Klik **op Volgende: Oplossingen**.
4. Ga als het tabblad Details:
    1. Geef in het vak Beschrijving de volgende gegevens voor het overeenkomstige brontype:

        |  Resource  |    Bovenliggende bronnen      |    Nieuwe limieten aangevraagd     |    Reden voor de verhoging van de quota       |
        |----------------|------------------------------|---------------------------------|------------------------------------------|
        |  Account |  *Subscription ID*   |  *Nieuw maximum **rekeningnummer** aangevraagd*    |  *Welk scenario of gebruiksaanvraag heeft de aanvraag gevraagd?*  |
        |  Pool    |  *Abonnements-ID, Account URI*  |  *Nieuw **maximumpoolnummer** aangevraagd*   |  *Welk scenario of gebruiksaanvraag heeft de aanvraag gevraagd?*  |
        |  Volume  |  *Abonnements-ID, Account URI, Pool URI*   |  *Nieuw **maximumvolumenummer** aangevraagd*     |  *Welk scenario of gebruiksaanvraag heeft de aanvraag gevraagd?*  |
        |  Maxfiles  |  *Abonnement-ID, Account URI, Pool URI, Volume URI*   |  *Nieuw maximum **maxfiles-nummer** aangevraagd*     |  *Welk scenario of gebruiksaanvraag heeft de aanvraag gevraagd?*  |    

    2. Geef de juiste ondersteuningsmethode op en geef uw contractgegevens op.

    3. Klik op **Volgende: Controleren + maken** om de aanvraag te maken. 


## <a name="next-steps"></a>Volgende stappen  

- [Informatie over de opslaghiërarchie van Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
- [Kostenmodel voor Azure NetApp Files](azure-netapp-files-cost-model.md)
