---
title: Een subnet delegeren aan Azure NetApp Files | Microsoft Docs
description: Hierin wordt beschreven hoe u een subnet kunt delegeren aan Azure NetApp Files.
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
ms.topic: how-to
ms.date: 07/28/2020
ms.author: b-juche
ms.openlocfilehash: 14c97fdea57fa50faf8b73275ec406ea36fbf552
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87420393"
---
# <a name="delegate-a-subnet-to-azure-netapp-files"></a>Een subnet delegeren aan Azure NetApp Files 

U dient een subnet te delegeren aan Azure NetApp Files.   Als u een volume maakt, dient u het gedelegeerde subnet op te geven.

## <a name="considerations"></a>Overwegingen
* De wizard voor het maken van een nieuw subnet gaat standaard over in een /24-netwerkmasker, dat 251 beschikbare IP-adressen onderhoudt. Het gebruik van een/28-netwerk masker, dat voorziet in elf bruikbare IP-adressen, is voldoende voor de service.
* In elk virtueel Azure-netwerk (VNet) kan er slechts één subnet aan Azure NetApp Files worden gedelegeerd.   
   Met Azure kunt u meerdere gedelegeerde subnetten in een VNet maken.  Pogingen om een nieuw volume te maken, mislukken echter als u meer dan één overgedragen subnet gebruikt.  
   U kunt slechts één gedelegeerd subnet in een VNet hebben. Een NetApp-account kan volumes implementeren in meerdere VNets, elk met een eigen overgedragen subnet.  
* U kunt geen netwerkbeveiligingsgroep of service-eindpunt in het gedelegeerde subnet toewijzen. Als u dit doet, mislukt het delegeren van het subnet.
* Toegang tot een volume vanuit een globaal gekoppeld virtueel netwerk wordt momenteel niet ondersteund.
* Het maken van door de [gebruiker gedefinieerde aangepaste routes](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes) op VM-subnetten met een adres voorvoegsel (bestemming) naar een subnet dat is overgedragen aan Azure NetApp files wordt niet ondersteund. Dit heeft gevolgen voor de VM-connectiviteit.

## <a name="steps"></a>Stappen 
1.  Ga in de Azure-portal naar de blade **Virtuele netwerken** en selecteer het virtuele netwerk dat u voor Azure NetApp Files wilt gebruiken.    

1. Selecteer **Subnetten** in de blade Virtueel netwerk en klik op de knop **+Subnet**. 

1. Maak een nieuw subnet dat u gebruikt voor Azure NetApp Files door de volgende verplichte velden in te vullen op de pagina Subnet toevoegen:
    * **Naam**: Geef de naam van het subnet op.
    * **Adres bereik**: Geef het IP-adres bereik op.
    * **Subnet-overdracht**: Selecteer **micro soft. NetApp/volumes**. 

      ![Delegatie van subnet](../media/azure-netapp-files/azure-netapp-files-subnet-delegation.png)
    
U kunt ook een subnet maken en delegeren als u [een volume maakt voor Azure NetApp Files](azure-netapp-files-create-volumes.md). 

## <a name="next-steps"></a>Volgende stappen  
* [Een volume maken voor Azure NetApp Files](azure-netapp-files-create-volumes.md)
* Meer informatie over [Integratie van virtuele netwerken voor Azure-services](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)


