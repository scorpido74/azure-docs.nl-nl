---
title: 'Azure ExpressRoute: QoS-vereisten'
description: Deze pagina bevat gedetailleerde vereisten voor het configureren en beheren van QoS. Skype voor Bedrijven/spraakdiensten worden besproken.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: cherylmc
ms.openlocfilehash: debc5d91478d0a5c3cc16c7b09f5713ba09b467e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74080105"
---
# <a name="expressroute-qos-requirements"></a>QoS-vereisten voor ExpressRoute
Skype voor Bedrijven heeft verschillende workloads die allemaal een andere QoS-behandeling vereisen. Als u via ExpressRoute voice-services wilt gaan gebruiken, moet u voldoen aan de vereisten die hieronder worden beschreven.

![](./media/expressroute-qos/expressroute-qos.png)

> [!NOTE]
> QoS-vereisten zijn alleen van toepassing op de Microsoft-peering. De DSCP-waarden in uw netwerkverkeer dat is ontvangen op Openbare Azure-peering en Persoonlijke Azure-peering wordt ingesteld op 0. 
> 
> 

In de volgende tabel vindt u een lijst met DSCP-markeringen die worden gebruikt door Microsoft Teams en Skype voor Bedrijven. Raadpleeg [Managing QoS for Skype for Business](https://docs.microsoft.com/SkypeForBusiness/manage/network-management/qos/managing-quality-of-service-QoS) (QoS voor Skype voor Bedrijven beheren) voor meer informatie.

| **Verkeersklasse** | **Behandeling (DSCP-markering)** | **Microsoft Teams en Skype voor Bedrijven-workloads** |
| --- | --- | --- |
| **Spraak** |EF (46) |Skype / Microsoft Teams / Lync-spraak |
| **Interactieve** |AF41 (34) |Video, VBSS |
| |AF21 (18) |Apps delen | 
| **Standaard** |AF11 (10) |Bestandsoverdracht |
| |CS0 (0) |Overige |

* U moet de workloads classificeren en de juiste DSCP-waarden markeren. Volg [deze](https://docs.microsoft.com/SkypeForBusiness/manage/network-management/qos/configuring-port-ranges-for-your-skype-clients#configure-quality-of-service-policies-for-clients-running-on-windows-10) richtlijnen over het instellen van DSCP-markeringen in uw netwerk.
* U moet meerdere QoS-wachtrijen in uw netwerk configureren en ondersteunen. Voice moet een zelfstandige klasse zijn en de EF-behandeling krijgen die is opgegeven in [RFC 3246.](https://www.ietf.org/rfc/rfc3246.txt) 
* U kunt het wachtrijmechanisme, het congestiedetectiebeleid en de bandbreedtetoewijzing per verkeersklasse bepalen. De DSCP-markering voor workloads voor Skype voor Bedrijven moet echter behouden blijven. Als u DSCP-markeringen gebruikt die niet worden weergegeven, bijvoorbeeld AF31 (26), moet u deze DSCP-waarde wijzigen in 0 voordat u het pakket naar Microsoft verzendt. Microsoft verzendt alleen pakketten die zijn gemarkeerd met de DSCP-waarde die wordt weergegeven in de bovenstaande tabel. 

## <a name="next-steps"></a>Volgende stappen
* Raadpleeg de vereisten voor [Routering](expressroute-routing.md) en [NAT](expressroute-nat.md).
* Klik op de volgende koppelingen als u uw ExpressRoute-verbinding wilt configureren.
  
  * [Een ExpressRoute-circuit maken](expressroute-howto-circuit-classic.md)
  * [Routering configureren](expressroute-howto-routing-classic.md)
  * [Een VNet koppelen aan een ExpressRoute-circuit](expressroute-howto-linkvnet-classic.md)

