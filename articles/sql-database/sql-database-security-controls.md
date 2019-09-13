---
title: Beveiligings controles voor Azure SQL Database
description: Een controle lijst met beveiligings controles voor het evalueren van Azure SQL Database
services: sql-database
author: msmbaldwin
manager: rkalrin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 77ff55389bac53d8719d86b4ac77f281415af49f
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886607"
---
# <a name="security-controls-for-azure-sql-database"></a>Beveiligings controles voor Azure SQL Database

In dit artikel worden de beveiligings besturings elementen gedocumenteerd die zijn ingebouwd in Azure SQL Database.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

SQL Database bevatten zowel [één data base](sql-database-single-index.yml) als een [beheerd exemplaar](sql-database-managed-instance.md). De volgende vermeldingen zijn van toepassing op beide aanbiedingen, tenzij anders vermeld.

## <a name="network"></a>Netwerk

| Beveiligings beheer | Ja/Nee | Opmerkingen |
|---|---|--|
| Ondersteuning voor service-eind punten| Ja | Is alleen van toepassing op [één data base](sql-database-single-index.yml) . |
| Ondersteuning voor Azure Virtual Network injectie| Ja | Is alleen van toepassing op een [beheerd exemplaar](sql-database-managed-instance.md) . |
| Netwerk isolatie en firewall ondersteuning| Ja | Firewall op database niveau en op server niveau. Netwerk isolatie geldt alleen voor [beheerde exemplaren](sql-database-managed-instance.md) . |
| Ondersteuning voor geforceerde tunneling| Ja | [Beheerde instantie](sql-database-managed-instance.md) via een [ExpressRoute](../expressroute/index.yml) -VPN. |

## <a name="monitoring--logging"></a>& Logboek registratie controleren

| Beveiligings beheer | Ja/Nee | Opmerkingen|
|---|---|--|
| Ondersteuning voor Azure-bewaking, zoals Log Analytics of Application Insights| Ja | SecureSphere, de SIEM-oplossing van Imperva, wordt ook ondersteund via de integratie van [Azure Event hubs](../event-hubs/index.yml) via [SQL auditing](sql-database-auditing.md). |
| Logboek registratie en controle op het vlak van controle en beheer| Ja | Ja voor sommige gebeurtenissen alleen |
| Logboek registratie en controle op gegevens vlak | Ja | Via [SQL audit](sql-database-auditing.md) |

## <a name="identity"></a>Identiteit

| Beveiligings beheer | Ja/Nee | Opmerkingen|
|---|---|--|
| Authentication| Ja | Azure Active Directory (Azure AD) |
| Authorization| Ja | Geen |

## <a name="data-protection"></a>Gegevensbescherming

| Beveiligings beheer | Ja/Nee | Opmerkingen |
|---|---|--|
| Versleuteling aan server zijde bij rest: Door micro soft beheerde sleutels | Ja | Wordt ' versleuteling-in-gebruik ' genoemd, zoals beschreven in het artikel [Always encrypted](sql-database-always-encrypted.md). Versleuteling aan de server zijde maakt gebruik van [transparante gegevens versleuteling](transparent-data-encryption-azure-sql.md).|
| Versleuteling in transit:<ul><li>Azure ExpressRoute-versleuteling</li><li>Versleuteling in een virtueel netwerk</li><li>Versleuteling tussen virtuele netwerken</ul>| Ja | HTTPS gebruiken. |
| Versleutelings verwerking, zoals CMK of BYOK| Ja | Door service beheerde en door de klant beheerde sleutel verwerking worden aangeboden. Deze laatste wordt aangeboden via [Azure Key Vault](../key-vault/index.yml). |
| Versleuteling op kolom niveau van Azure Data Services| Ja | Via [Always encrypted](sql-database-always-encrypted.md). |
| Versleutelde API-aanroepen| Ja | HTTPS/SSL gebruiken. |

## <a name="configuration-management"></a>Configuratiebeheer

| Beveiligings beheer | Ja/Nee | Opmerkingen|
|---|---|--|
| Ondersteuning voor configuratie beheer, zoals versie beheer van de configuratie| Nee  | Geen |

## <a name="additional-security-controls-for-sql-database"></a>Aanvullende beveiligings controles voor SQL Database

| Beveiligings beheer | Ja/Nee | Opmerkingen|
|---|---|--|
| Preventieve: evaluatie van beveiligings problemen | Ja | Zie [SQL-evaluatie service voor beveiligings problemen helpt u bij het identificeren van een database kwets](sql-vulnerability-assessment.md)baarheid. |
| Preventief: gegevens detectie en-classificatie  | Ja | Zie [Azure SQL database en SQL Data Warehouse gegevens detectie & classificatie](sql-database-data-discovery-and-classification.md). |
| Detectie: detectie van bedreigingen | Ja | Zie [Advanced Threat Protection voor Azure SQL database](sql-database-threat-detection-overview.md). |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ingebouwde beveiligings controles in Azure-Services](../security/fundamentals/security-controls.md).
