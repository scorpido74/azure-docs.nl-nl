---
title: Beveiligings controles voor Azure Key Vault
description: Een controle lijst met beveiligings controles voor het evalueren van Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: d7f92a309a3d0b5d8e85f1e270d5590f46496a77
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886636"
---
# <a name="security-controls-for-azure-key-vault"></a>Beveiligings controles voor Azure Key Vault

In dit artikel worden de beveiligings besturings elementen gedocumenteerd die zijn ingebouwd in Azure Key Vault. 

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Netwerk

| Beveiligings beheer | Ja/Nee | Opmerkingen |
|---|---|--|
| Ondersteuning voor service-eind punten| Ja | Het gebruik van de service-eind punten van Virtual Network (VNet). |
| Ondersteuning voor VNet-injectie| Nee |  |
| Ondersteuning voor netwerk isolatie en firewalling| Ja | Met VNet-firewall regels. |
| Ondersteuning voor geforceerde tunneling| Nee |  |

## <a name="monitoring--logging"></a>& Logboek registratie controleren

| Beveiligings beheer | Ja/Nee | Opmerkingen|
|---|---|--|
| Ondersteuning voor Azure-bewaking (log Analytics, app Insights, enz.)| Ja | Log Analytics gebruiken. |
| Logboek registratie en controle van beheer/beheer vlak| Ja | Log Analytics gebruiken. |
| Logboek registratie en controle van het gegevens vlak| Ja | Log Analytics gebruiken. |

## <a name="identity"></a>Identiteit

| Beveiligings beheer | Ja/Nee | Opmerkingen|
|---|---|--|
| Authentication| Ja | Verificatie is via Azure Active Directory. |
| Authorization| Ja | Key Vault toegangs beleid gebruiken. |

## <a name="data-protection"></a>Gegevensbescherming

| Beveiligings beheer | Ja/Nee | Opmerkingen |
|---|---|--|
| Versleuteling aan server zijde bij rest: Door micro soft beheerde sleutels | Ja | Alle objecten zijn versleuteld. |
| Versleuteling aan server zijde op rest: door de klant beheerde sleutels (BYOK) | Ja | De klant beheert alle sleutels in hun Key Vault. Wanneer er ondersteunde sleutels voor de Hardware Security module (HSM) worden opgegeven, wordt de sleutel, het certificaat of het geheim beschermd door een HSM met FIPS Level 2. |
| Versleuteling op kolom niveau (Azure Data Services)| N/A |  |
| Versleuteling in transit (zoals ExpressRoute-versleuteling, in VNet-versleuteling en VNet-VNet-versleuteling)| Ja | Alle communicatie via versleutelde API-aanroepen |
| Versleutelde API-aanroepen| Ja | HTTPS gebruiken. |

## <a name="access-controls"></a>Besturingselementen voor toegang

| Beveiligings beheer | Ja/Nee | Opmerkingen|
|---|---|--|
| Toegangs beheer voor het besturings element/beheer vlak | Ja | Toegangsbeheer op basis van rollen (RBAC) in Azure Resource Manager |
| Toegangs beheer voor gegevens vlak (op elk service niveau) | Ja | Toegangs beleid Key Vault |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ingebouwde beveiligings controles in Azure-Services](../security/fundamentals/security-controls.md).