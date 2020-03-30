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
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: b-juche
ms.openlocfilehash: b83f530549ffa43789963fd0c95b4982f5289356
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80054468"
---
# <a name="delegate-a-subnet-to-azure-netapp-files"></a>Een subnet delegeren aan Azure NetApp Files 

U dient een subnet te delegeren aan Azure NetApp Files.   Als u een volume maakt, dient u het gedelegeerde subnet op te geven.

## <a name="considerations"></a>Overwegingen
* De wizard voor het maken van een nieuw subnet gaat standaard over in een /24-netwerkmasker, dat 251 beschikbare IP-adressen onderhoudt. Een /28-netwerkmasker, dat 16 bruikbare IP-adressen onderhoudt, is voldoende voor de service.
* In elk virtueel Azure-netwerk (VNet) kan er slechts één subnet aan Azure NetApp Files worden gedelegeerd.   
   Met Azure u meerdere gedelegeerde subnetten maken in een VNet.  Pogingen om een nieuw volume te maken mislukken echter als u meer dan één gedelegeerd subnet gebruikt.
* U kunt geen netwerkbeveiligingsgroep of service-eindpunt in het gedelegeerde subnet toewijzen. Als u dit doet, mislukt het delegeren van het subnet.
* Toegang tot een volume van een virtueel netwerk met een wereldwijde peered wordt momenteel niet ondersteund.
* Het maken van door de gebruiker gedefinieerde aangepaste routes op [VM-subnetten](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes) met adresvoorvoegsel (bestemming) naar een subnet dat is gedelegeerd aan Azure NetApp-bestanden, wordt niet ondersteund. Dit heeft gevolgen voor vm-connectiviteit.

## <a name="steps"></a>Stappen 
1.  Ga in de Azure-portal naar de blade **Virtuele netwerken** en selecteer het virtuele netwerk dat u voor Azure NetApp Files wilt gebruiken.    

1. Selecteer **Subnetten** in de blade Virtueel netwerk en klik op de knop **+Subnet**. 

1. Maak een nieuw subnet dat u gebruikt voor Azure NetApp Files door de volgende verplichte velden in te vullen op de pagina Subnet toevoegen:
    * **Naam:** Geef de subnetnaam op.
    * **Adresbereik:** geef het IP-adresbereik op.
    * **Subnetdelegatie**: Selecteer **Microsoft.NetApp/volumes**. 

      ![Delegatie van subnet](../media/azure-netapp-files/azure-netapp-files-subnet-delegation.png)
    
U kunt ook een subnet maken en delegeren als u [een volume maakt voor Azure NetApp Files](azure-netapp-files-create-volumes.md). 

## <a name="next-steps"></a>Volgende stappen  
* [Een volume maken voor Azure NetApp Files](azure-netapp-files-create-volumes.md)
* Meer informatie over [Integratie van virtuele netwerken voor Azure-services](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)


