---
title: Een capaciteitspool instellen voor Azure NetApp Files | Microsoft Docs
description: In dit artikel wordt beschreven hoe u een capaciteitspool instelt, zodat u hierin volumes kunt maken.
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
ms.date: 04/02/2020
ms.author: b-juche
ms.openlocfilehash: d76af4901103b0eed8cd1cffac744f8fb41d9689
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85483496"
---
# <a name="set-up-a-capacity-pool"></a>Een capaciteitspool instellen

Als u een capaciteitspool instelt, kunt u hierin volumes maken.  

## <a name="before-you-begin"></a>Voordat u begint 

U moet al een NetApp-account hebben gemaakt.   

[Een NetApp-account maken](azure-netapp-files-create-netapp-account.md)

## <a name="steps"></a>Stappen 

1. Ga naar de beheerblade voor uw NetApp-account en klik vervolgens op **Capaciteitspools** in het navigatiedeelvenster.  
    
    ![Navigeren naar capaciteitspool](../media/azure-netapp-files/azure-netapp-files-navigate-to-capacity-pool.png)

2. Klik op **+ Pools toevoegen** om een nieuwe capaciteitspool te maken.   
    Het venster Nieuwe capaciteitspool wordt weergegeven.

3. Geef de volgende informatie op voor de nieuwe capaciteitspool:  
   * **Naam**  
     Geef de naam op voor de capaciteitspool.  
     De naam van de capaciteitspool moet uniek zijn voor elk NetApp-account.

   * **Service niveau**   
     In dit veld worden de doelprestaties voor de capaciteitspool weergegeven.  
     Geef het service niveau voor de capaciteits groep op: [**Ultra**](azure-netapp-files-service-levels.md#Ultra), [**Premium**](azure-netapp-files-service-levels.md#Premium)of [**Standard**](azure-netapp-files-service-levels.md#Standard).

   * **Size**     
     Geef de grootte op van de capaciteitspool die u aanschaft.        
     De minimale grootte van de capaciteitspool is 4 TiB. U kunt een pool maken met een grootte die een veelvoud is van 4 TiB.   
      
     ![Nieuwe capaciteitspool](../media/azure-netapp-files/azure-netapp-files-new-capacity-pool.png)

4. Klik op **OK**.

## <a name="next-steps"></a>Volgende stappen 

- [Serviceniveau's voor Azure NetApp Files](azure-netapp-files-service-levels.md)
- Bekijk de [pagina met prijzen van Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/) voor informatie over de prijs van verschillende serviceniveaus
- [Een subnet delegeren aan Azure NetApp Files](azure-netapp-files-delegate-subnet.md)
