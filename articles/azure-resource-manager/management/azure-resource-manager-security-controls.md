---
title: Beveiligingsmaatregelen
description: Een controle lijst met ingebouwde beveiligings controles voor het evalueren van de Azure Resource Manager-service.
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: bb8742c38fae88dc1fd1fd1ec175b248f30df3a0
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86054454"
---
# <a name="security-controls-for-azure-resource-manager"></a>Beveiligings controles voor Azure Resource Manager

In dit artikel worden de beveiligings besturings elementen gedocumenteerd die zijn ingebouwd in Azure Resource Manager.

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>Gegevensbescherming

| Beveiligings beheer | Ja/Nee | Notities |
|---|---|--|
| Versleuteling aan server zijde op rest: door micro soft beheerde sleutels | Yes |  |
| Versleuteling in transit (zoals ExpressRoute-versleuteling, in VNet-versleuteling en VNet-VNet-versleuteling)| Yes | HTTPS/TLS. |
| Versleuteling aan server zijde op rest: door de klant beheerde sleutels (BYOK) | N.v.t. | Azure Resource Manager slaat geen klant inhoud op, alleen gegevens beheren. |
| Versleuteling op kolom niveau (Azure Data Services)| Yes | |
| Versleutelde API-aanroepen| Yes | |

## <a name="network"></a>Netwerk

| Beveiligings beheer | Ja/Nee | Notities |
|---|---|--|
| Ondersteuning voor service-eind punten| No | |
| Ondersteuning voor VNet-injectie| Yes | |
| Ondersteuning voor netwerk isolatie en firewalling| No |  |
| Ondersteuning voor geforceerde tunneling| No |  |

## <a name="monitoring--logging"></a>& logboek registratie controleren

| Beveiligings beheer | Ja/Nee | Notities|
|---|---|--|
| Ondersteuning voor Azure-bewaking (log Analytics, app Insights, enz.)| No | |
| Logboek registratie en controle op het vlak van controle en beheer| Yes | Met activiteiten logboeken worden alle schrijf bewerkingen (PUT, POST, DELETE) die zijn uitgevoerd op uw resources beschikbaar gesteld. Zie [activiteiten logboeken weer geven om acties op resources te controleren](view-activity-logs.md). |
| Logboek registratie en controle van het gegevens vlak| N.v.t. | |

## <a name="identity"></a>Identiteit

| Beveiligings beheer | Ja/Nee | Notities|
|---|---|--|
| Verificatie| Yes | [Azure Active Directory](../../active-directory/index.yml) gebaseerd.|
| Autorisatie| Yes | |

## <a name="configuration-management"></a>Configuratiebeheer

| Beveiligings beheer | Ja/Nee | Notities|
|---|---|--|
| Ondersteuning voor configuratie beheer (versie van configuratie, enz.)| Ja |  |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ingebouwde beveiligings controles in Azure-Services](../../security/fundamentals/security-controls.md).
