---
title: Een Load Balancer met een zonegebonden-frontend maken-Azure Portal
titleSuffix: Azure Load Balancer
description: Meer informatie over het maken van een Standard Load Balancer met zonegebonden frontend met de Azure Portal
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2018
ms.author: allensu
ms.openlocfilehash: 82a773b279780bc4eb784fa107d6b15bd0ff2672
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225343"
---
#  <a name="create-a-standard-load-balancer-with-zonal-frontend-using-azure-portal"></a>Een Standard Load Balancer met zonegebonden-frontend maken met behulp van Azure Portal

In dit artikel wordt beschreven hoe u een open bare [Standard Load Balancer](https://aka.ms/azureloadbalancerstandard) maakt met een zonegebonden front-end-IP-configuratie. Zie [Standard Load Balancer-en beschikbaarheids](load-balancer-standard-availability-zones.md)zones voor meer informatie over de werking van beschikbaarheids zones met Standard Load Balancer. 

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

> [!NOTE]
> Ondersteuning voor Beschikbaarheidszones is beschikbaar voor geselecteerde Azure-resources en regio's en groottefamilies van de virtuele machine. Zie [overzicht van Beschikbaarheidszones](https://docs.microsoft.com/azure/availability-zones/az-overview)voor meer informatie over hoe u aan de slag kunt gaan en welke Azure-resources,-regio's en-Series met VM-grootten u de beschikbaarheids zones wilt proberen. Voor ondersteuning kunt u vragen stellen op [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) of [een Azure-ondersteuningsticket openen](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="log-in-to-azure"></a>Meld u aan bij Azure. 

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="create-a-load-balancer-with-zonal-frontend-ip-address"></a>Een load balancer maken met zonegebonden frontend-IP-adres

1. Ga in een browser naar de Azure Portal: [https://portal.azure.com](https://portal.azure.com) en meld u aan met uw Azure-account.
2. Selecteer in de linkerbovenhoek van het scherm de optie **een resource maken** > **netwerk** > **Load Balancer.**
3. Klik op de pagina **Load Balancer maken** onder **naam** type **myLoadBalancer**.
4. Selecteer onder **Type** de optie **Openbaar**.
5. Selecteer onder SKU de optie **standaard**.
6. Klik op **Kies een openbaar IP-adres**, klik op **Nieuw**en op de pagina **openbaar IP-adres maken** onder naam typt u **myPublicIPZonal**, voor SKU, selecteer **standaard**, voor beschikbaarheids zone, selecteer **1**.
    
>[!NOTE] 
> De openbare IP die zijn gemaakt in deze stap is van de standaard-SKU standaard.

1. Klik voor **resource groep**op **nieuwe maken**en typ **myResourceGroupZLB** als de naam van de resource groep.
1. Selecteer bij **locatie**de optie **Europa-West**en klik vervolgens op **OK**. De load balancer begint vervolgens met de implementatie. Het duurt een aantal minuten voordat de implementatie is voltooid.

    ![Maak zone-redundante Standard Load Balancer met de Azure Portal](./media/load-balancer-get-started-internet-availability-zones-zonal-portal/load-balancer-zonal-frontend.png)


## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [Standard Load Balancer-en beschikbaarheids zones](load-balancer-standard-availability-zones.md).



