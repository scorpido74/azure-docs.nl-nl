---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/07/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e84a77629026bb8885a48b8ed928699825f07115
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77111241"
---
| **Leverancier** | **Apparaatfamilie** | **Firmware versie** |
| --- | --- | --- |
|Cisco | ISR| IOS 15,1 (preview-versie)|
|Cisco | ASA | ASA (*) RouteBased (IKEv2-geen BGP) voor ASA onder 9,8 |
|Cisco | ASA | ASA RouteBased (IKEv2-no BGP) voor ASA 9.8 + |
|Juniper | SRX_GA | 12. x|
|Juniper | SSG_GA | ScreenOS 6.2. x|
|Juniper | JSeries_GA | JunOS 12. x|
|Juniper | SRX | JunOS 12. x RouteBased BGP |
|Ubiquiti| EdgeRouter| EdgeOS v 1,10 x RouteBased VTI|
|Ubiquiti| EdgeRouter| EdgeOS v 1,10 x RouteBased BGP|

> [!NOTE]
> ( * ) Vereist: NarrowAzureTrafficSelectors (enable UsePolicyBasedTrafficSelectors Option) and CustomAzurePolicies (IKE/IPsec)
>
