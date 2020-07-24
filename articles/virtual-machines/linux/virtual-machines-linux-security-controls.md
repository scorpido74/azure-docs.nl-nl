---
title: Beveiligings controles voor Azure Linux Virtual Machines-Linux
description: Een controle lijst met beveiligings controles voor het evalueren van Azure Linux Virtual Machines
services: virtual-machines
ms.service: virtual-machines
documentationcenter: ''
author: msmbaldwin
manager: rkarlin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 132f696dd1298384c302eeadc264e857d1edff96
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87080060"
---
# <a name="security-controls-for-linux-virtual-machines"></a>Beveiligings controles voor Linux Virtual Machines

In dit artikel worden de beveiligings besturings elementen gedocumenteerd die zijn ingebouwd in Linux Virtual Machines.

[!INCLUDE [Security controls header](../../../includes/security-controls-header.md)]

## <a name="network"></a>Netwerk

| Beveiligings beheer | Ja/Nee | Opmerkingen |
|---|---|--|
| Ondersteuning voor service-eind punten| Yes | |
| Ondersteuning voor VNet-injectie| Yes | |
| Ondersteuning voor netwerk isolatie en firewalling| Yes |  |
| Ondersteuning voor geforceerde tunneling| Yes | Zie [geforceerde tunneling configureren met het Azure Resource Manager-implementatie model](../../vpn-gateway/vpn-gateway-forced-tunneling-rm.md). |

## <a name="monitoring--logging"></a>& logboek registratie controleren

| Beveiligings beheer | Ja/Nee | Opmerkingen|
|---|---|--|
| Ondersteuning voor Azure-bewaking (log Analytics, app Insights, enz.)| Yes | Zie [een virtuele Linux-machine bewaken en bijwerken in azure](./tutorial-monitor.md). |
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
| Versleuteling aan server zijde op rest: door micro soft beheerde sleutels | Yes | Zie [Azure Disk Encryption voor Linux-vm's](disk-encryption-overview.md). |
| Versleuteling in transit (zoals ExpressRoute-versleuteling, in VNet-versleuteling en VNet-VNet-versleuteling)| Yes | Azure Virtual Machines ondersteunt [ExpressRoute](../../expressroute/index.yml) -en VNet-versleuteling. Zie [in-transit versleuteling in vm's](../../security/fundamentals/encryption-overview.md#in-transit-encryption-in-vms). |
| Versleuteling aan server zijde op rest: door de klant beheerde sleutels (BYOK) | Yes | Door de klant beheerde sleutels is een ondersteund Azure-versleutelings scenario. Zie [Azure Encryption Overview](../../security/fundamentals/encryption-overview.md#in-transit-encryption-in-vms)(Engelstalig).|
| Versleuteling op kolom niveau (Azure Data Services)| N.v.t. | |
| Versleutelde API-aanroepen| Yes | Via HTTPS en TLS. |

## <a name="configuration-management"></a>Configuratiebeheer

| Beveiligings beheer | Ja/Nee | Opmerkingen|
|---|---|--|
| Ondersteuning voor configuratie beheer (versie van configuratie, enz.)| Ja |  | 

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ingebouwde beveiligings controles in Azure-Services](../../security/fundamentals/security-controls.md).
