---
title: Beveiligingsmaatregelen
description: Een checklist met beveiligingsbesturingselementen voor de evaluatie van Azure SQL Database
services: sql-database
author: msmbaldwin
manager: rkalrin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: ce7f3eafa57cbd993be98f4a2da3d89cb312f9b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190698"
---
# <a name="security-controls-for-azure-sql-database"></a>Beveiligingsbesturingselementen voor Azure SQL Database

In dit artikel worden de beveiligingsbesturingselementen weergegeven die zijn ingebouwd in Azure SQL Database.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

SQL Database bevat zowel [één database](sql-database-single-index.yml) als [beheerde instantie.](sql-database-managed-instance.md) De volgende vermeldingen zijn van toepassing op beide aanbiedingen, behalve wanneer anders vermeld.

## <a name="network"></a>Netwerk

| Beveiligingscontrole | Ja/Nee | Opmerkingen |
|---|---|--|
| Ondersteuning voor serviceeindpunten| Ja | Geldt alleen voor [één database.](sql-database-single-index.yml) |
| Ondersteuning voor Azure Virtual Network-injectie| Ja | Geldt alleen voor [beheerde instantie.](sql-database-managed-instance.md) |
| Ondersteuning voor netwerkisolatie en firewall| Ja | Firewall op zowel databaseniveau als serverniveau. Netwerkisolatie wordt bijvoorbeeld alleen [beheerd.](sql-database-managed-instance.md) |
| Ondersteuning voor gedwongen tunneling| Ja | [Beheerd exemplaar](sql-database-managed-instance.md) via een [ExpressRoute](../expressroute/index.yml) VPN. |

## <a name="monitoring--logging"></a>Controle & logboekregistratie

| Beveiligingscontrole | Ja/Nee | Opmerkingen|
|---|---|--|
| Ondersteuning voor Azure-bewaking, zoals Log Analytics of Application Insights| Ja | SecureSphere, de SIEM-oplossing van Imperva, wordt ook ondersteund via [Azure Event Hubs-integratie](../event-hubs/index.yml) via [SQL auditing.](sql-database-auditing.md) |
| Logging en audit van controlevlak en beheer-vliegtuig| Ja | Ja voor sommige evenementen |
| Logboekregistratie en -audit | Ja | Via [SQL-audit](sql-database-auditing.md) |

## <a name="identity"></a>Identiteit

| Beveiligingscontrole | Ja/Nee | Opmerkingen|
|---|---|--|
| Authentication| Ja | Azure Active Directory (Azure AD) |
| Autorisatie| Ja | Geen |

## <a name="data-protection"></a>Gegevensbeveiliging

| Beveiligingscontrole | Ja/Nee | Opmerkingen |
|---|---|--|
| Server-side encryptie in rust: door Microsoft beheerde sleutels | Ja | Genaamd "encryptie-in-gebruik," zoals beschreven in het artikel [Always Encrypted](sql-database-always-encrypted.md). Server-side encryptie maakt gebruik van [transparante gegevensversleuteling.](transparent-data-encryption-azure-sql.md)|
| Versleuteling onderweg:<ul><li>Azure ExpressRoute-versleuteling</li><li>Versleuteling in een virtueel netwerk</li><li>Versleuteling tussen virtuele netwerken</ul>| Ja | HTTPS gebruiken. |
| Verwerking van versleutelingssleutels, zoals CMK of BYOK| Ja | Zowel service-managed als customer-managed key handling worden aangeboden. Dit laatste wordt aangeboden via [Azure Key Vault.](../key-vault/index.yml) |
| Versleuteling op kolomniveau die wordt geleverd door Azure-gegevensservices| Ja | Via [Always Encrypted](sql-database-always-encrypted.md). |
| Versleutelde API-aanroepen| Ja | HTTPS/TLS gebruiken. |

## <a name="configuration-management"></a>Configuratiebeheer

| Beveiligingscontrole | Ja/Nee | Opmerkingen|
|---|---|--|
| Ondersteuning voor configuratiebeheer, zoals versiebeheer van configuratie| Nee  | Geen |

## <a name="additional-security-controls-for-sql-database"></a>Extra besturingselementen voor SQL-database

| Beveiligingscontrole | Ja/Nee | Opmerkingen|
|---|---|--|
| Preventief: kwetsbaarheidsbeoordeling | Ja | Zie [SQL Vulnerability Assessment-service helpt u bij het identificeren van databasekwetsbaarheden.](sql-vulnerability-assessment.md) |
| Preventief: gegevensdetectie en -classificatie  | Ja | Zie [Azure SQL Database en SQL Data Warehouse-gegevensdetectie & classificatie](sql-database-data-discovery-and-classification.md). |
| Detectie: detectie van bedreigingen | Ja | Zie [Geavanceerde bedreigingsbeveiliging voor Azure SQL-database](sql-database-threat-detection-overview.md). |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ingebouwde beveiligingsbesturingselementen voor Azure-services](../security/fundamentals/security-controls.md).
