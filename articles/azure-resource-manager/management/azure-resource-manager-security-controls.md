---
title: Beveiligingsmaatregelen
description: Een checklist met ingebouwde beveiligingsbesturingselementen voor de evaluatie van de Azure Resource Manager-service.
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: d0a0625153e428a0d261e52d40b31ef5142eddfd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75485622"
---
# <a name="security-controls-for-azure-resource-manager"></a>Beveiligingsbesturingselementen voor Azure Resource Manager

In dit artikel worden de beveiligingsbesturingselementen die zijn ingebouwd in Azure Resource Manager, document.

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>Gegevensbeveiliging

| Beveiligingscontrole | Ja/Nee | Opmerkingen |
|---|---|--|
| Server-side encryptie in rust: door Microsoft beheerde sleutels | Ja |  |
| Versleuteling tijdens het transport (zoals ExpressRoute-versleuteling, vnet-versleuteling en VNet-VNet-versleuteling)| Ja | HTTPS/TLS. |
| Server-side encryptie in rust: door de klant beheerde sleutels (BYOK) | N.v.t. | Azure Resource Manager slaat geen klantinhoud op, alleen beheergegevens. |
| Versleuteling op kolomniveau (Azure Data Services)| Ja | |
| API-aanroepen versleuteld| Ja | |

## <a name="network"></a>Netwerk

| Beveiligingscontrole | Ja/Nee | Opmerkingen |
|---|---|--|
| Ondersteuning voor serviceeindpunten| Nee | |
| Ondersteuning voor VNet-injectie| Ja | |
| Ondersteuning voor netwerkisolatie en firewalling| Nee |  |
| Ondersteuning voor gedwongen tunneling| Nee |  |

## <a name="monitoring--logging"></a>Controle & logboekregistratie

| Beveiligingscontrole | Ja/Nee | Opmerkingen|
|---|---|--|
| Azure-bewakingsondersteuning (logboekanalyses, app-inzichten, enz.)| Nee | |
| Logboekregistratie en audit van het controle- en beheervlak| Ja | Activiteitslogboeken onthullen alle schrijfbewerkingen (PUT, POST, DELETE) die op uw resources zijn uitgevoerd; zie [Activiteitslogboeken weergeven om acties op resources te controleren](view-activity-logs.md). |
| Logboekregistratie en -audit van gegevensvliegtuigen| N.v.t. | |

## <a name="identity"></a>Identiteit

| Beveiligingscontrole | Ja/Nee | Opmerkingen|
|---|---|--|
| Authentication| Ja | [Azure Active Directory](/azure/active-directory) op basis van.|
| Autorisatie| Ja | |

## <a name="configuration-management"></a>Configuratiebeheer

| Beveiligingscontrole | Ja/Nee | Opmerkingen|
|---|---|--|
| Ondersteuning voor configuratiebeheer (versiebeheer van configuratie, enz.)| Ja |  |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ingebouwde beveiligingsbesturingselementen voor Azure-services](../../security/fundamentals/security-controls.md).