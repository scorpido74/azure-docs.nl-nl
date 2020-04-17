---
title: Beveiligingsbesturingselementen voor Azure Windows Virtual Machines
description: Een checklist met beveiligingscontroles voor de evaluatie van Azure Windows Virtual Machines
services: virtual-machines
ms.service: virtual-machines
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: mbaldwin
ms.openlocfilehash: 569c989e8876bb7213d1469c7a70da99aa0b1e9c
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81455339"
---
# <a name="security-controls-for-windows-virtual-machines"></a>Beveiligingsbesturingselementen voor virtuele Windows-machines

In dit artikel worden de beveiligingsbesturingselementen die zijn ingebouwd in Virtuele Windows-machines, document.

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
| Azure-bewakingsondersteuning (logboekanalyses, app-inzichten, enz.)| Ja | [Een virtuele Windows-machine in Azure bewaken en bijwerken.](tutorial-monitoring.md) |
| Logboekregistratie en audit van het controle- en beheervlak| Ja |  |
| Logboekregistratie en -audit van gegevensvliegtuigen | Nee |  |

## <a name="identity"></a>Identiteit

| Beveiligingscontrole | Ja/Nee | Opmerkingen|
|---|---|--|
| Verificatie| Ja |  |
| Autorisatie| Ja |  |

## <a name="data-protection"></a>Gegevensbeveiliging

| Beveiligingscontrole | Ja/Nee | Opmerkingen |
|---|---|--|
| Server-side encryptie in rust: door Microsoft beheerde sleutels | Ja | Zie [Virtuele schijven versleutelen op een Windows-vm](/azure/virtual-machines/windows/disk-encryption-overview). |
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
