---
title: Azure Analysis Services configureren voor VNet-gegevensbronnen | Microsoft Documenten
description: Meer informatie over het configureren van een Azure Analysis Services-server om een gateway te gebruiken voor gegevensbronnen op Azure Virtual Network (VNet).
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 384f4c2ee3021678c530aa5c98eaf4dbfd661e95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572266"
---
# <a name="use-gateway-for-data-sources-on-an-azure-virtual-network-vnet"></a>Gateway gebruiken voor gegevensbronnen op een Extern Netwerk (Azure Virtual Network)

In dit artikel wordt de eigenschap Azure Analysis Services **AlwaysUseGateway-server** beschreven voor gebruik wanneer gegevensbronnen zich op een [Azure Virtual Network (VNet) bevinden.](../virtual-network/virtual-networks-overview.md)

## <a name="server-access-to-vnet-data-sources"></a>Servertoegang tot VNet-gegevensbronnen

Als uw gegevensbronnen worden geopend via een VNet, moet uw Azure Analysis Services-server verbinding maken met die gegevensbronnen alsof ze on-premises zijn, in uw eigen omgeving. U de eigenschap **AlwaysUseGateway-server** configureren om de server op te geven om toegang te krijgen tot alle gegevensbronnen via een [on-premises gateway.](analysis-services-gateway.md) 

Azure SQL Database Managed Instance-gegevensbronnen worden uitgevoerd binnen Azure VNet met een privé-IP-adres. Als openbaar eindpunt is ingeschakeld op de instantie, is een gateway niet vereist. Als het openbare eindpunt niet is ingeschakeld, is een on-premises gegevensgateway vereist en moet de eigenschap AlwaysUseGateway op true zijn ingesteld.

> [!NOTE]
> Deze eigenschap is alleen effectief wanneer een [on-premises datagateway](analysis-services-gateway.md) is geïnstalleerd en geconfigureerd. De gateway kan op de VNet.

## <a name="configure-alwaysusegateway-property"></a>AlwaysUseGateway configureren, eigenschap

1. Selecteer **Geavanceerde eigenschappen (Alle) tonen**in SSMS > server > **Eigenschappen** > **algemeen**.
2. Selecteer in de **ASPaaS\AlwaysUseGateway**de optie **waar**.

    ![Altijd gateway-eigenschap gebruiken](media/analysis-services-vnet-gateway/aas-ssms-always-property.png)


## <a name="see-also"></a>Zie ook
[Verbinding maken met on-premises gegevensbronnen](analysis-services-gateway.md)   
[Een on-premises gegevensgateway installeren en configureren](analysis-services-gateway-install.md)   
[Azure Virtual Network (VNET)](../virtual-network/virtual-networks-overview.md)   

