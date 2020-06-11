---
title: Beveiligingsmaatregelen
description: Een controle lijst met beveiligings controles voor het evalueren van Azure SQL Database
services: sql-database
author: msmbaldwin
manager: rkarlin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 97d2cd8e9ba51e4fc6ebab8459b04f4f37e876d3
ms.sourcegitcommit: eeba08c8eaa1d724635dcf3a5e931993c848c633
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84668420"
---
# <a name="security-controls-for-azure-sql-database-and-sql-managed-instance"></a>Beveiligings controles voor Azure SQL Database en SQL Managed instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

In dit artikel worden de beveiligings besturings elementen gedocumenteerd die zijn ingebouwd in Azure SQL Database en Azure SQL Managed instance.

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]



## <a name="network"></a>Netwerk

| Beveiligings beheer | Ja/Nee | Opmerkingen |
|---|---|--|
| Ondersteuning voor service-eind punten| Ja | Is alleen van toepassing op [SQL database](../index.yml) . |
| Ondersteuning voor Azure Virtual Network injectie| Ja | Is alleen van toepassing op een [SQL Managed instance](../managed-instance/sql-managed-instance-paas-overview.md) . |
| Netwerk isolatie en firewall ondersteuning| Ja | Firewall op database niveau en op server niveau. Netwerk isolatie is alleen voor [SQL Managed instance](../managed-instance/sql-managed-instance-paas-overview.md) . |
| Ondersteuning voor geforceerde tunneling| Ja | [SQL Managed instance](../managed-instance/sql-managed-instance-paas-overview.md) via een [ExpressRoute](../expressroute/../index.yml) -VPN. |

## <a name="monitoring--logging"></a>& logboek registratie controleren

| Beveiligings beheer | Ja/Nee | Opmerkingen|
|---|---|--|
| Ondersteuning voor Azure-bewaking, zoals Log Analytics of Application Insights| Ja | SecureSphere, de SIEM-oplossing van Imperva, wordt ook ondersteund via de integratie van [Azure Event hubs](../event-hubs/../index.yml) via [SQL auditing](../../azure-sql/database/auditing-overview.md). |
| Logboek registratie en controle op het vlak van controle en beheer| Ja | Ja voor sommige gebeurtenissen alleen |
| Logboek registratie en controle op gegevens vlak | Ja | Via [SQL audit](../../azure-sql/database/auditing-overview.md) |

## <a name="identity"></a>Identiteit

| Beveiligings beheer | Ja/Nee | Opmerkingen|
|---|---|--|
| Verificatie| Ja | Azure Active Directory (Azure AD) |
| Autorisatie| Ja | Geen |

## <a name="data-protection"></a>Gegevensbescherming

| Beveiligings beheer | Ja/Nee | Opmerkingen |
|---|---|--|
| Versleuteling aan server zijde op rest: door micro soft beheerde sleutels | Ja | Wordt ' versleuteling-in-gebruik ' genoemd, zoals beschreven in het artikel [Always encrypted](always-encrypted-certificate-store-configure.md). Versleuteling aan de server zijde maakt gebruik van [transparante gegevens versleuteling](transparent-data-encryption-tde-overview.md).|
| Versleuteling in transit:<ul><li>Azure ExpressRoute-versleuteling</li><li>Versleuteling in een virtueel netwerk</li><li>Versleuteling tussen virtuele netwerken</ul>| Ja | HTTPS gebruiken. |
| Versleutelings verwerking, zoals CMK of BYOK| Ja | Door service beheerde en door de klant beheerde sleutel verwerking worden aangeboden. Deze laatste wordt aangeboden via [Azure Key Vault](../key-vault/../index.yml). |
| Versleuteling op kolom niveau van Azure Data Services| Ja | Via [Always encrypted](always-encrypted-certificate-store-configure.md). |
| Versleutelde API-aanroepen| Ja | HTTPS/TLS gebruiken. |

## <a name="configuration-management"></a>Configuratiebeheer

| Beveiligings beheer | Ja/Nee | Opmerkingen|
|---|---|--|
| Ondersteuning voor configuratie beheer, zoals versie beheer van de configuratie| Nee  | Geen |

## <a name="additional-security-controls-for-sql-database"></a>Aanvullende beveiligings controles voor SQL Database

| Beveiligings beheer | Ja/Nee | Opmerkingen|
|---|---|--|
| Preventieve: evaluatie van beveiligings problemen | Ja | Zie [SQL-evaluatie service voor beveiligings problemen helpt u bij het identificeren van een database kwets](sql-vulnerability-assessment.md)baarheid. |
| Preventief: gegevens detectie en-classificatie  | Ja | Zie [Azure SQL database en SQL Data Warehouse gegevens detectie & classificatie](data-discovery-and-classification-overview.md). |
| Detectie: detectie van bedreigingen | Ja | Zie [Advanced Threat Protection voor Azure SQL database](threat-detection-overview.md). |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ingebouwde beveiligings controles in Azure-Services](../../security/fundamentals/security-controls.md).
