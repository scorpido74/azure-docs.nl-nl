---
title: Beveiligingscontroles voor Azure Linux Virtual Machines - Linux
description: Een checklist met beveiligingscontroles voor de evaluatie van Azure Linux Virtual Machines
services: virtual-machines
ms.service: virtual-machines
documentationcenter: ''
author: msmbaldwin
manager: rkarlin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 300acaf4a9d2a11ef107e19df99452c909257d54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190579"
---
# <a name="security-controls-for-linux-virtual-machines"></a>Beveiligingscontroles voor Virtuele Linux-machines

In dit artikel worden de beveiligingsbesturingselementen die zijn ingebouwd in Virtuele Linux-machines.

[!INCLUDE [Security controls header](../../../includes/security-controls-header.md)]

## <a name="network"></a>Netwerk

| Beveiligingscontrole | Ja/Nee | Opmerkingen |
|---|---|--|
| Ondersteuning voor serviceeindpunten| Ja | |
| Ondersteuning voor VNet-injectie| Ja | |
| Ondersteuning voor Netwerkisolatie en Firewalling| Ja |  |
| Ondersteuning voor gedwongen tunneling| Ja | Zie [Geforceerde tunneling configureren met behulp van het Azure Resource Manager-implementatiemodel](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm). |

## <a name="monitoring--logging"></a>Controle & logboekregistratie

| Beveiligingscontrole | Ja/Nee | Opmerkingen|
|---|---|--|
| Azure-bewakingsondersteuning (logboekanalyses, app-inzichten, enz.)| Ja | Zie [Een virtuele Linux-machine in Azure controleren en bijwerken.](/azure/virtual-machines/linux/tutorial-monitoring) |
| Logboekregistratie en audit van het controle- en beheervlak| Ja |  |
| Logboekregistratie en -audit van gegevensvliegtuigen | Nee |  |

## <a name="identity"></a>Identiteit

| Beveiligingscontrole | Ja/Nee | Opmerkingen|
|---|---|--|
| Authentication| Ja |  |
| Autorisatie| Ja |  |

## <a name="data-protection"></a>Gegevensbeveiliging

| Beveiligingscontrole | Ja/Nee | Opmerkingen |
|---|---|--|
| Server-side encryptie in rust: door Microsoft beheerde sleutels | Ja | Zie [Azure Disk Encryption voor Linux VM's](disk-encryption-overview.md). |
| Versleuteling tijdens het transport (zoals ExpressRoute-versleuteling, vnet-versleuteling en VNet-VNet-versleuteling)| Ja | Azure Virtual Machines ondersteunt [ExpressRoute-](/azure/expressroute) en VNet-versleuteling. Zie [Versleuteling in transit in VM's](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms). |
| Server-side encryptie in rust: door de klant beheerde sleutels (BYOK) | Ja | Door de klant beheerde sleutels is een ondersteund Azure-versleutelingsscenario; zie [overzicht van Azure-versleuteling](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms).|
| Versleuteling op kolomniveau (Azure Data Services)| N.v.t. | |
| API-aanroepen versleuteld| Ja | Via HTTPS en TLS. |

## <a name="configuration-management"></a>Configuratiebeheer

| Beveiligingscontrole | Ja/Nee | Opmerkingen|
|---|---|--|
| Ondersteuning voor configuratiebeheer (versiebeheer van configuratie, enz.)| Ja |  | 

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ingebouwde beveiligingsbesturingselementen voor Azure-services](../../security/fundamentals/security-controls.md).
