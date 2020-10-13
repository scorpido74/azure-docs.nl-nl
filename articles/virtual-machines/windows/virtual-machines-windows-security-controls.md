---
title: Beveiligings controles voor Azure Windows Virtual Machines
description: Een controle lijst met beveiligings controles voor het evalueren van Azure Windows Virtual Machines
ms.service: virtual-machines
ms.subservice: security
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: mbaldwin
ms.openlocfilehash: 6ab6133faef4a6c7a8eb929e5f4cd1840e971a99
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87088339"
---
# <a name="security-controls-for-windows-virtual-machines"></a>Beveiligings controles voor Windows Virtual Machines

In dit artikel worden de beveiligings besturings elementen gedocumenteerd die zijn ingebouwd in Windows Virtual Machines.

[!INCLUDE [Security controls header](../../../includes/security-controls-header.md)]

## <a name="network"></a>Netwerk

| Beveiligings beheer | Ja/Nee | Notities |
|---|---|--|
| Ondersteuning voor service-eind punten| Ja | |
| Ondersteuning voor VNet-injectie| Ja | |
| Ondersteuning voor netwerk isolatie en firewalling| Ja |  |
| Ondersteuning voor geforceerde tunneling| Ja | Zie [geforceerde tunneling configureren met het Azure Resource Manager-implementatie model](../../vpn-gateway/vpn-gateway-forced-tunneling-rm.md). |

## <a name="monitoring--logging"></a>& logboek registratie controleren

| Beveiligings beheer | Ja/Nee | Notities|
|---|---|--|
| Ondersteuning voor Azure-bewaking (log Analytics, app Insights, enz.)| Ja | [Een virtuele Windows-machine bewaken en bijwerken in azure](./tutorial-monitor.md). |
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
| Versleuteling aan server zijde op rest: door micro soft beheerde sleutels | Ja | Zie [virtuele schijven op een Windows-VM versleutelen](./disk-encryption-overview.md). |
| Versleuteling in transit (zoals ExpressRoute-versleuteling, in VNet-versleuteling en VNet-VNet versleuteling)| Ja | Azure Virtual Machines ondersteunt [ExpressRoute](../../expressroute/index.yml) -en VNet-versleuteling. Zie [in-transit versleuteling in vm's](../../security/fundamentals/encryption-overview.md#in-transit-encryption-in-vms). |
| Versleuteling aan server zijde op rest: door de klant beheerde sleutels (BYOK) | Ja | Door de klant beheerde sleutels is een ondersteund Azure-versleutelings scenario. Zie [Azure Encryption Overview](../../security/fundamentals/encryption-overview.md#in-transit-encryption-in-vms)(Engelstalig).|
| Versleuteling op kolom niveau (Azure Data Services)| N.v.t. | |
| Versleutelde API-aanroepen| Ja | Via HTTPS en TLS. |



## <a name="configuration-management"></a>Configuratiebeheer

| Beveiligings beheer | Ja/Nee | Notities|
|---|---|--|
| Ondersteuning voor configuratie beheer (versie van configuratie, enz.)| Ja |  | 

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ingebouwde beveiligings controles in Azure-Services](../../security/fundamentals/security-controls.md).
