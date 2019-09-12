---
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 07/10/2019
ms.author: mbaldwin
ms.openlocfilehash: 520fd50b2b0864f43c08687f05de377679b36d84
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886893"
---
## <a name="preventative"></a>Preventie

| Beveiligings beheer | Ja/Nee | Opmerkingen |
|---|---|--|
| Versleuteling bij rest (zoals versleuteling aan server zijde, versleuteling aan server zijde met door de klant beheerde sleutels en andere versleutelings functies) | Ja | Zie [een virtuele Linux-machine versleutelen in azure](/azure/virtual-machines/linux/encrypt-disks) en [virtuele schijven op een Windows-VM](/azure/virtual-machines/windows/encrypt-disks)versleutelen. |
| Versleuteling in transit (zoals ExpressRoute-versleuteling, in VNet-versleuteling en VNet-VNet-versleuteling)| Ja | Azure Virtual Machines ondersteunt [ExpressRoute](/azure/expressroute) -en VNet-versleuteling. Zie [in-transit versleuteling in vm's](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms). |
| Verwerking van versleutelings sleutels (CMK, BYOK, enz.)| Ja | Door de klant beheerde sleutels is een ondersteund Azure-versleutelings scenario. Zie [Azure Encryption Overview](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms)(Engelstalig).|
| Versleuteling op kolom niveau (Azure Data Services)| N/A | |
| Versleutelde API-aanroepen| Ja | Via HTTPS en SSL. |

## <a name="network-segmentation"></a>Netwerk segmentatie

| Beveiligings beheer | Ja/Nee | Opmerkingen |
|---|---|--|
| Ondersteuning voor service-eind punten| Ja | |
| Ondersteuning voor VNet-injectie| Ja | . |
| Ondersteuning voor netwerk isolatie en firewalling| Ja |  |
| Ondersteuning voor geforceerde tunneling| Ja | Zie [geforceerde tunneling configureren met het Azure Resource Manager-implementatie model](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm). |

## <a name="detection"></a>Detectie

| Beveiligings beheer | Ja/Nee | Opmerkingen|
|---|---|--|
| Ondersteuning voor Azure-bewaking (log Analytics, app Insights, enz.)| Ja | Zie [een virtuele Linux-machine bewaken en bijwerken in azure](/azure/virtual-machines/linux/tutorial-monitoring) en [een virtuele Windows-machine bewaken en bijwerken in azure](/azure/virtual-machines/windows/tutorial-monitoring). |

## <a name="identity-and-access-management"></a>Identiteits- en toegangsbeheer

| Beveiligings beheer | Ja/Nee | Opmerkingen|
|---|---|--|
| Authentication| Ja |  |
| Authorization| Ja |  |


## <a name="audit-trail"></a>Audittrail

| Beveiligings beheer | Ja/Nee | Opmerkingen|
|---|---|--|
| Logboek registratie en controle op het vlak van controle en beheer| Ja |  |
| Logboek registratie en controle van het gegevens vlak | Nee |  |

## <a name="configuration-management"></a>Configuratiebeheer

| Beveiligings beheer | Ja/Nee | Opmerkingen|
|---|---|--|
| Ondersteuning voor configuratie beheer (versie van configuratie, enz.)| Ja |  | 
