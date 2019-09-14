---
title: Resource limieten voor Azure NetApp Files | Microsoft Docs
description: Hierin worden de limieten voor Azure NetApp Files resources beschreven, inclusief limieten voor NetApp-accounts, capaciteits Pools, volumes, moment opnamen en het overgedragen subnet.
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
ms.date: 08/07/2019
ms.author: b-juche
ms.openlocfilehash: 15d0a584d88045f6020162a88124cd9d6a4735bf
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70984008"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Resourcelimieten voor Azure NetApp Files

Informatie over resource limieten voor Azure NetApp Files helpt u bij het beheren van uw volumes.

## <a name="resource-limits"></a>Bronlimieten

In de volgende tabel worden resource limieten voor Azure NetApp Files beschreven:

|  Resource  |  Standaardlimiet  |  Aanpasbaar via ondersteunings aanvraag  |
|----------------|---------------------|--------------------------------------|
|  Aantal NetApp-accounts per Azure-abonnement   |  10    |  Ja   |
|  Aantal capaciteits Pools per NetApp-account   |    25     |   Ja   |
|  Aantal volumes per capaciteits pool     |    500   |    Ja     |
|  Aantal moment opnamen per volume       |    255     |    Nee        |
|  Aantal subnetten dat wordt gedelegeerd aan Azure NetApp Files (micro soft. NetApp/volumes) per Azure-Virtual Network    |   1   |    Nee    |
|  Aantal IP-adressen in een VNet (inclusief peered VNets) die toegang hebben tot Azure NetApp Files   |    1000   |    Ja   |
|  Minimum grootte van één capaciteits groep   |  4 TiB     |    Nee  |
|  Maximale grootte van één capaciteits groep    |  500 TiB   |   Nee   |
|  Minimum grootte van één volume    |    100 GiB    |    Nee    |
|  Maximale grootte van één volume     |    100 TiB    |    Nee       |
|  Maximum aantal bestanden (inodes) per volume     |    50.000.000    |    Nee    |    

## <a name="request-limit-increase"></a>Toename van aanvraag limiet 

U kunt een ondersteunings aanvraag voor Azure maken om de aanpas bare limieten van de bovenstaande tabel te verg Roten. 

Vanuit Azure Portal navigatie vlak: 

1. Klik op **Help en ondersteuning**.
2. Klik op **+ nieuwe ondersteunings aanvraag**.
3. Geef op het tabblad basis beginselen de volgende informatie op: 
    1. Type probleem: **Service-en abonnements limieten (quota's)** selecteren.
    2. Geabonneerd Selecteer het abonnement voor de resource dat u nodig hebt om het quotum te verg root.
    3. Type quotum: Opslag **selecteren: Azure NetApp Files limieten**.
    4. Klik op **Next: Oplossingen**.
4. Op het tabblad Details:
    1. Geef in het vak Beschrijving de volgende informatie op voor het bijbehorende resource type:

        |  Resource  |    Bovenliggende resources      |    Aangevraagde nieuwe limieten     |    Reden voor toename van quotum       |
        |----------------|------------------------------|---------------------------------|------------------------------------------|
        |  Account |  *Subscription ID*   |  *Aangevraagd nieuw Maxi maal **account** nummer*    |  *Welk scenario of use-case vraagt de aanvraag?*  |
        |  Groep    |  *Abonnements-ID, account-URI*  |  *Nieuw maximum **groeps** nummer aangevraagd*   |  *Welk scenario of use-case vraagt de aanvraag?*  |
        |  Volume  |  *Abonnements-ID, account-URI, groeps-URI*   |  *Nieuw maximum **volume** nummer aangevraagd*     |  *Welk scenario of use-case vraagt de aanvraag?*  |

    2. Geef de juiste ondersteunings methode op en geef uw contract informatie op.

    3. Klik op **Next: Bekijk + maken** om de aanvraag te maken. 


## <a name="next-steps"></a>Volgende stappen  

- [Informatie over de opslaghiërarchie van Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
- [Kosten model voor Azure NetApp Files](azure-netapp-files-cost-model.md)
