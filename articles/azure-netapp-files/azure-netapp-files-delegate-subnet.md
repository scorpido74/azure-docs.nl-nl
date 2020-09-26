---
title: Een subnet delegeren aan Azure NetApp Files | Microsoft Docs
description: Meer informatie over het overdragen van een subnet aan Azure NetApp Files. Geef het overgedragen subnet op wanneer u een volume maakt.
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
ms.openlocfilehash: da7aa0889940c560df705e3c47f5ccb1960aee2c
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91361021"
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
* Het maken van door de [gebruiker gedefinieerde aangepaste routes](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes) op VM-subnetten met een adres voorvoegsel (bestemming) naar een subnet dat is overgedragen aan Azure NetApp files wordt niet ondersteund. Dit heeft gevolgen voor de VM-connectiviteit. Azure NetApp Files maakt een systeem route naar het overgedragen subnet. De route wordt weer gegeven in de **juiste routes** in de route tabel als u deze nodig hebt voor het oplossen van problemen.

## <a name="steps"></a>Stappen

1.  Ga naar de Blade **virtuele netwerken** in de Azure Portal en selecteer het virtuele netwerk dat u wilt gebruiken voor Azure NetApp files.    

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


