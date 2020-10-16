---
title: Beveiligingsmaatregelen
description: Een controle lijst met ingebouwde beveiligings controles voor het evalueren van de Azure Resource Manager-service.
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: bb8742c38fae88dc1fd1fd1ec175b248f30df3a0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86054454"
---
# <a name="security-controls-for-azure-resource-manager"></a>Beveiligings controles voor Azure Resource Manager

In dit artikel worden de beveiligings besturings elementen gedocumenteerd die zijn ingebouwd in Azure Resource Manager.

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>Gegevensbeveiliging

| Beveiligings beheer | Ja/Nee | Notities |
|---|---|--|
| Versleuteling aan server zijde op rest: door micro soft beheerde sleutels | Ja |  |
| Versleuteling in transit (zoals ExpressRoute-versleuteling, in VNet-versleuteling en VNet-VNet versleuteling)| Ja | HTTPS/TLS. |
| Versleuteling aan server zijde op rest: door de klant beheerde sleutels (BYOK) | N.v.t. | Azure Resource Manager slaat geen klant inhoud op, alleen gegevens beheren. |
| Versleuteling op kolom niveau (Azure Data Services)| Ja | |
| Versleutelde API-aanroepen| Ja | |

## <a name="network"></a>Netwerk

| Beveiligings beheer | Ja/Nee | Notities |
|---|---|--|
| Ondersteuning voor service-eind punten| Nee | |
| Ondersteuning voor VNet-injectie| Ja | |
| Ondersteuning voor netwerk isolatie en firewalling| Nee |  |
| Ondersteuning voor geforceerde tunneling| Nee |  |

## <a name="monitoring--logging"></a>& logboek registratie controleren

| Beveiligings beheer | Ja/Nee | Notities|
|---|---|--|
| Ondersteuning voor Azure-bewaking (log Analytics, app Insights, enz.)| Nee | |
| Logboek registratie en controle op het vlak van controle en beheer| Ja | Met activiteiten logboeken worden alle schrijf bewerkingen (PUT, POST, DELETE) die zijn uitgevoerd op uw resources beschikbaar gesteld. Zie [activiteiten logboeken weer geven om acties op resources te controleren](view-activity-logs.md). |
| Logboek registratie en controle van het gegevens vlak| N.v.t. | |

## <a name="identity"></a>Identiteit

| Beveiligings beheer | Ja/Nee | Notities|
|---|---|--|
| Verificatie| Ja | [Azure Active Directory](../../active-directory/index.yml) gebaseerd.|
| Autorisatie| Ja | |

## <a name="configuration-management"></a>Configuratiebeheer

| Beveiligings beheer | Ja/Nee | Notities|
|---|---|--|
| Ondersteuning voor configuratie beheer (versie van configuratie, enz.)| Ja |  |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ingebouwde beveiligings controles in Azure-Services](../../security/fundamentals/security-controls.md).
