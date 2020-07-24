---
title: Beveiligings controles voor Azure Virtual Machine Scale Sets
description: Een controle lijst met beveiligings controles voor het evalueren van Azure Virtual Machine Scale Sets
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: security
ms.date: 09/05/2019
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 4480ad425d9a3953fd5779f99d27b5b6b037e61e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87029413"
---
# <a name="security-controls-for-azure-virtual-machine-scale-sets"></a>Beveiligings controles voor Azure Virtual Machine Scale Sets

In dit artikel worden de beveiligings besturings elementen gedocumenteerd die zijn ingebouwd in azure Virtual Machine Scale Sets.

[!INCLUDE [Security controls header](../../includes/security-controls-header.md)]

## <a name="network"></a>Netwerk

| Beveiligings beheer | Ja/Nee | Opmerkingen |
|---|---|--|
| Ondersteuning voor service-eind punten| Yes | |
| Ondersteuning voor VNet-injectie| Yes | |
| Ondersteuning voor netwerk isolatie en firewalling| Yes |  |
| Ondersteuning voor geforceerde tunneling| Yes | Zie [geforceerde tunneling configureren met het Azure Resource Manager-implementatie model](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md). |

## <a name="monitoring--logging"></a>& logboek registratie controleren

| Beveiligings beheer | Ja/Nee | Opmerkingen|
|---|---|--|
| Ondersteuning voor Azure-bewaking (log Analytics, app Insights, enz.)| Yes | Zie [een virtuele Linux-machine bewaken en bijwerken in azure](../virtual-machines/linux/tutorial-monitor.md) en [een virtuele Windows-machine bewaken en bijwerken in azure](../virtual-machines/windows/tutorial-monitor.md). |
| Logboek registratie en controle op het vlak van controle en beheer| Yes |  |
| Logboek registratie en controle van het gegevens vlak | No |  |

## <a name="identity"></a>Identiteit

| Beveiligings beheer | Ja/Nee | Opmerkingen|
|---|---|--|
| Verificatie| Yes |  |
| Autorisatie| Yes |  |

## <a name="data-protection"></a>Gegevensbeveiliging

| Beveiligings beheer | Ja/Nee | Opmerkingen |
|---|---|--|
| Versleuteling aan server zijde op rest: door micro soft beheerde sleutels | Yes | Zie [Azure Disk Encryption voor Virtual Machine Scale sets](disk-encryption-overview.md). |
| Versleuteling in transit (zoals ExpressRoute-versleuteling, in VNet-versleuteling en VNet-VNet-versleuteling)| Yes | Azure Virtual Machines ondersteunt [ExpressRoute](../expressroute/index.yml) -en VNet-versleuteling. Zie [in-transit versleuteling in vm's](../security/fundamentals/encryption-overview.md#in-transit-encryption-in-vms). |
| Versleuteling aan server zijde op rest: door de klant beheerde sleutels (BYOK) | Yes | Door de klant beheerde sleutels is een ondersteund Azure-versleutelings scenario. Zie [Azure Disk Encryption voor Virtual Machine Scale sets](disk-encryption-overview.md)|
| Versleuteling op kolom niveau (Azure Data Services)| N.v.t. | |
| Versleutelde API-aanroepen| Yes | Via HTTPS en TLS. |

## <a name="configuration-management"></a>Configuratiebeheer

| Beveiligings beheer | Ja/Nee | Opmerkingen|
|---|---|--|
| Ondersteuning voor configuratie beheer (versie van configuratie, enz.)| Ja |  | 

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ingebouwde beveiligings controles in Azure-Services](../security/fundamentals/security-controls.md).
