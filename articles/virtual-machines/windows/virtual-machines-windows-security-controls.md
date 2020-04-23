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
ms.openlocfilehash: ac1ed9ac25d65d0391175fc6d43b48048da74926
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82101583"
---
# <a name="security-controls-for-windows-virtual-machines"></a>Beveiligings controles voor Windows Virtual Machines

In dit artikel worden de beveiligings besturings elementen gedocumenteerd die zijn ingebouwd in Windows Virtual Machines.

[!INCLUDE [Security controls header](../../../includes/security-controls-header.md)]

## <a name="network"></a>Netwerk

| Beveiligings beheer | Ja/Nee | Opmerkingen |
|---|---|--|
| Ondersteuning voor service-eind punten| Ja | |
| Ondersteuning voor VNet-injectie| Ja | |
| Ondersteuning voor netwerk isolatie en firewalling| Ja |  |
| Ondersteuning voor geforceerde tunneling| Ja | Zie [geforceerde tunneling configureren met het Azure Resource Manager-implementatie model](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm). |

## <a name="monitoring--logging"></a>& logboek registratie controleren

| Beveiligings beheer | Ja/Nee | Opmerkingen|
|---|---|--|
| Ondersteuning voor Azure-bewaking (log Analytics, app Insights, enz.)| Ja | [Een virtuele Windows-machine bewaken en bijwerken in azure](tutorial-monitoring.md). |
| Logboek registratie en controle op het vlak van controle en beheer| Ja |  |
| Logboek registratie en controle van het gegevens vlak | Nee |  |

## <a name="identity"></a>Identiteit

| Beveiligings beheer | Ja/Nee | Opmerkingen|
|---|---|--|
| Verificatie| Ja |  |
| Autorisatie| Ja |  |

## <a name="data-protection"></a>Gegevensbeveiliging

| Beveiligings beheer | Ja/Nee | Opmerkingen |
|---|---|--|
| Versleuteling aan server zijde op rest: door micro soft beheerde sleutels | Ja | Zie [virtuele schijven op een Windows-VM versleutelen](/azure/virtual-machines/windows/disk-encryption-overview). |
| Versleuteling in transit (zoals ExpressRoute-versleuteling, in VNet-versleuteling en VNet-VNet-versleuteling)| Ja | Azure Virtual Machines ondersteunt [ExpressRoute](/azure/expressroute) -en VNet-versleuteling. Zie [in-transit versleuteling in vm's](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms). |
| Versleuteling aan server zijde op rest: door de klant beheerde sleutels (BYOK) | Ja | Door de klant beheerde sleutels is een ondersteund Azure-versleutelings scenario. Zie [Azure Encryption Overview](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms)(Engelstalig).|
| Versleuteling op kolom niveau (Azure Data Services)| N.v.t. | |
| Versleutelde API-aanroepen| Ja | Via HTTPS en TLS. |



## <a name="configuration-management"></a>Configuratiebeheer

| Beveiligings beheer | Ja/Nee | Opmerkingen|
|---|---|--|
| Ondersteuning voor configuratie beheer (versie van configuratie, enz.)| Ja |  | 

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ingebouwde beveiligings controles in Azure-Services](../../security/fundamentals/security-controls.md).
