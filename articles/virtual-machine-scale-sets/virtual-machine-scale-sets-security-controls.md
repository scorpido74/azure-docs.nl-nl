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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87029413"
---
# <a name="security-controls-for-azure-virtual-machine-scale-sets"></a>Beveiligings controles voor Azure Virtual Machine Scale Sets

In dit artikel worden de beveiligings besturings elementen gedocumenteerd die zijn ingebouwd in azure Virtual Machine Scale Sets.

[!INCLUDE [Security controls header](../../includes/security-controls-header.md)]

## <a name="network"></a>Netwerk

| Beveiligings beheer | Ja/Nee | Notities |
|---|---|--|
| Ondersteuning voor service-eind punten| Ja | |
| Ondersteuning voor VNet-injectie| Ja | |
| Ondersteuning voor netwerk isolatie en firewalling| Ja |  |
| Ondersteuning voor geforceerde tunneling| Ja | Zie [geforceerde tunneling configureren met het Azure Resource Manager-implementatie model](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md). |

## <a name="monitoring--logging"></a>& logboek registratie controleren

| Beveiligings beheer | Ja/Nee | Notities|
|---|---|--|
| Ondersteuning voor Azure-bewaking (log Analytics, app Insights, enz.)| Ja | Zie [een virtuele Linux-machine bewaken en bijwerken in azure](../virtual-machines/linux/tutorial-monitor.md) en [een virtuele Windows-machine bewaken en bijwerken in azure](../virtual-machines/windows/tutorial-monitor.md). |
| Logboek registratie en controle op het vlak van controle en beheer| Ja |  |
| Logboek registratie en controle van het gegevens vlak | Nee |  |

## <a name="identity"></a>Identiteit

| Beveiligings beheer | Ja/Nee | Notities|
|---|---|--|
| Verificatie| Ja |  |
| Autorisatie| Ja |  |

## <a name="data-protection"></a>Gegevensbeveiliging

| Beveiligings beheer | Ja/Nee | Notities |
|---|---|--|
| Versleuteling aan server zijde op rest: door micro soft beheerde sleutels | Ja | Zie [Azure Disk Encryption voor Virtual Machine Scale sets](disk-encryption-overview.md). |
| Versleuteling in transit (zoals ExpressRoute-versleuteling, in VNet-versleuteling en VNet-VNet versleuteling)| Ja | Azure Virtual Machines ondersteunt [ExpressRoute](../expressroute/index.yml) -en VNet-versleuteling. Zie [in-transit versleuteling in vm's](../security/fundamentals/encryption-overview.md#in-transit-encryption-in-vms). |
| Versleuteling aan server zijde op rest: door de klant beheerde sleutels (BYOK) | Ja | Door de klant beheerde sleutels is een ondersteund Azure-versleutelings scenario. Zie [Azure Disk Encryption voor Virtual Machine Scale sets](disk-encryption-overview.md)|
| Versleuteling op kolom niveau (Azure Data Services)| N.v.t. | |
| Versleutelde API-aanroepen| Ja | Via HTTPS en TLS. |

## <a name="configuration-management"></a>Configuratiebeheer

| Beveiligings beheer | Ja/Nee | Notities|
|---|---|--|
| Ondersteuning voor configuratie beheer (versie van configuratie, enz.)| Ja |  | 

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ingebouwde beveiligings controles in Azure-Services](../security/fundamentals/security-controls.md).
