---
title: Beveiligingsbesturingselementen voor Azure Key Vault
description: Een checklist met beveiligingscontroles voor de evaluatie van Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: cd6602f68b63e2c236e7f3905d33b88fbda36ed2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429861"
---
# <a name="security-controls-for-azure-key-vault"></a>Beveiligingsbesturingselementen voor Azure Key Vault

In dit artikel worden de beveiligingsbesturingselementen die zijn ingebouwd in Azure Key Vault, document. 

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="network"></a>Netwerk

| Beveiligingscontrole | Ja/Nee | Opmerkingen |
|---|---|--|
| Ondersteuning voor serviceeindpunten| Ja | VNet-serviceeindpunten (Virtual Network) gebruiken. |
| Ondersteuning voor VNet-injectie| Nee |  |
| Ondersteuning voor netwerkisolatie en firewalling| Ja | Met behulp van VNet firewall regels. |
| Ondersteuning voor gedwongen tunneling| Nee |  |

## <a name="monitoring--logging"></a>Controle & logboekregistratie

| Beveiligingscontrole | Ja/Nee | Opmerkingen|
|---|---|--|
| Azure-bewakingsondersteuning (logboekanalyses, app-inzichten, enz.)| Ja | Met behulp van Log Analytics. |
| Logboekregistratie en audit van controle-/beheervlak| Ja | Met behulp van Log Analytics. |
| Logboekregistratie en -audit van gegevensvliegtuigen| Ja | Met behulp van Log Analytics. |

## <a name="identity"></a>Identiteit

| Beveiligingscontrole | Ja/Nee | Opmerkingen|
|---|---|--|
| Verificatie| Ja | Verificatie gebeurt via Azure Active Directory. |
| Autorisatie| Ja | Met behulp van Key Vault Access Policy. |

## <a name="data-protection"></a>Gegevensbeveiliging

| Beveiligingscontrole | Ja/Nee | Opmerkingen |
|---|---|--|
| Server-side encryptie in rust: door Microsoft beheerde sleutels | Ja | Alle objecten zijn versleuteld. |
| Server-side encryptie in rust: door de klant beheerde sleutels (BYOK) | Ja | De klant controleert alle sleutels in zijn Key Vault. Wanneer back-sleutels (HSM) van de hardwarebeveiliging zijn opgegeven, beschermt een FIPS Level 2 HSM de sleutel, het certificaat of het geheim. |
| Versleuteling op kolomniveau (Azure Data Services)| N.v.t. |  |
| Versleuteling tijdens het transport (zoals ExpressRoute-versleuteling, vnet-versleuteling en VNet-VNet-versleuteling)| Ja | Alle communicatie gaat via versleutelde API-aanroepen |
| API-aanroepen versleuteld| Ja | HTTPS gebruiken. |

## <a name="access-controls"></a>Besturingselementen voor toegang

| Beveiligingscontrole | Ja/Nee | Opmerkingen|
|---|---|--|
| Toegangsbesturingselementen voor besturing/beheer | Ja | Toegangsbeheer op basis van rollen (RBAC) in Azure Resource Manager |
| Toegangsbesturingselementen voor gegevensvlakken (op elk serviceniveau) | Ja | Key Vault Access Policy |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ingebouwde beveiligingsbesturingselementen voor Azure-services](../../security/fundamentals/security-controls.md).