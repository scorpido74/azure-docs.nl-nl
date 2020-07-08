---
title: Beveiligings controles voor Azure Service Fabric
description: Meer informatie over beveiligings controles voor Azure Service Fabric. Bevat een controle lijst met ingebouwde beveiligings controles.
author: msmbaldwin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: a8bb49e20ec5812a4882966c6918cf2bd59f36a0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "75645426"
---
# <a name="security-controls-for-azure-service-fabric"></a>Beveiligings controles voor Azure Service Fabric

In dit artikel worden de beveiligings besturings elementen gedocumenteerd die zijn ingebouwd in azure Service Fabric. 

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Netwerk

| Beveiligings beheer | Ja/Nee | Notities |
|---|---|--|
| Ondersteuning voor service-eind punten| Yes |  |
| Ondersteuning voor VNet-injectie| Yes |  |
| Ondersteuning voor netwerk isolatie en firewalling| Yes | Met behulp van netwerk beveiligings groepen (NSG). |
| Ondersteuning voor geforceerde tunneling| Yes | Azure-netwerken bieden geforceerde tunneling. |

## <a name="monitoring--logging"></a>& logboek registratie controleren

| Beveiligings beheer | Ja/Nee | Notities|
|---|---|--|
| Ondersteuning voor Azure-bewaking (log Analytics, app Insights, enz.)| Yes | Azure-controle ondersteuning en ondersteuning van derden gebruiken. |
| Logboek registratie en controle op het vlak van controle en beheer| Yes | Alle bewerkingen van het besturings vlak worden uitgevoerd via processen voor controle en goed keuringen. |
| Logboek registratie en controle van het gegevens vlak| N.v.t. | De klant is eigenaar van het cluster.  |

## <a name="identity"></a>Identiteit

| Beveiligings beheer | Ja/Nee | Notities|
|---|---|--|
| Verificatie| Yes | Verificatie is via Azure Active Directory. |
| Autorisatie| Yes | Identiteits-en toegangs beheer (IAM) voor aanroepen via SFRP. Aanroepen rechtstreeks naar het eind punt van het cluster ondersteunen twee rollen: gebruiker en beheerder. De klant kan de Api's toewijzen aan een van beide rollen. |

## <a name="data-protection"></a>Gegevensbescherming

| Beveiligings beheer | Ja/Nee | Notities |
|---|---|--|
| Versleuteling aan server zijde op rest: door micro soft beheerde sleutels | Yes | De klant is eigenaar van het cluster en de schaalset voor virtuele machines waarop het cluster is gebouwd. Azure Disk Encryption kan worden ingeschakeld voor de schaalset van de virtuele machine. |
| Versleuteling aan server zijde op rest: door de klant beheerde sleutels (BYOK) | Yes | De klant is eigenaar van het cluster en de schaalset voor virtuele machines waarop het cluster is gebouwd. Azure Disk Encryption kan worden ingeschakeld voor de schaalset van de virtuele machine. |
| Versleuteling op kolom niveau (Azure Data Services)| N.v.t. |  |
| Versleuteling in transit (zoals ExpressRoute-versleuteling, in VNet-versleuteling en VNet-VNet-versleuteling)| Yes |  |
| Versleutelde API-aanroepen| Yes | Service Fabric-API-aanroepen worden gedaan via Azure Resource Manager. Er is een geldig JSON-webtoken (JWT) vereist. |

## <a name="configuration-management"></a>Configuratiebeheer

| Beveiligings beheer | Ja/Nee | Notities|
|---|---|--|
| Ondersteuning voor configuratie beheer (versie van configuratie, enz.)| Ja | |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ingebouwde beveiligings controles in Azure-Services](../security/fundamentals/security-controls.md).