---
title: Beveiligingsmaatregelen
description: Meer informatie over de beveiligingsbesturingselementen die worden gebruikt in de Azure Backup-service. Deze besturingselementen helpen de service beveiligingsproblemen te voorkomen, op te sporen en erop te reageren.
ms.topic: conceptual
ms.date: 09/23/2019
ms.openlocfilehash: 0e3f5ce942ea8aef9bf5eb98883ae1e72a7ab239
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74172143"
---
# <a name="security-controls-for-azure-backup"></a>Beveiligingsbesturingselementen voor Azure Backup

In dit artikel worden de beveiligingsbesturingselementen die zijn ingebouwd in Azure Backup, documenteer.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Netwerk

| Beveiligingscontrole | Ja/Nee | Opmerkingen | Documentatie
|---|---|--|
| Ondersteuning voor serviceeindpunten| Nee |  |  |
| Ondersteuning voor VNet-injectie| Nee |  |  |
| Ondersteuning voor netwerkisolatie en firewalling| Ja | Gedwongen tunneling wordt ondersteund voor VM-back-up. Gedwongen tunneling wordt niet ondersteund voor workloads die in VM's worden uitgevoerd. |  |
| Ondersteuning voor gedwongen tunneling| Nee |  |  |

## <a name="monitoring--logging"></a>Controle & logboekregistratie

| Beveiligingscontrole | Ja/Nee | Opmerkingen| | Documentatie
|---|---|--|
| Azure-bewakingsondersteuning (logboekanalyses, app-inzichten, enz.)| Ja | Log Analytics wordt ondersteund via resourcelogs. Zie [Beveiligde workloads](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/)met azure backup met behulp van Log Analytics voor meer informatie. |  |
| Logboekregistratie en audit van het controle- en beheervlak| Ja | Alle acties die door klanten vanuit de Azure-portal worden geactiveerd, worden vastgelegd in activiteitslogboeken. |  |
| Logboekregistratie en -audit van gegevensvliegtuigen| Nee | Azure Backup-gegevensvlak is niet rechtstreeks bereikbaar.  |  |

## <a name="identity"></a>Identiteit

| Beveiligingscontrole | Ja/Nee | Opmerkingen| | Documentatie
|---|---|--|
| Authentication| Ja | Verificatie gebeurt via Azure Active Directory. |  |
| Autorisatie| Ja | Klant gemaakte en ingebouwde RBAC rollen worden gebruikt. Zie [Toegangsbeheer op basis van rollen gebruiken om herstelpunten voor Azure Backup te beheren](/azure/backup/backup-rbac-rs-vault)voor meer informatie. |  |

## <a name="data-protection"></a>Gegevensbeveiliging

| Beveiligingscontrole | Ja/Nee | Opmerkingen | | Documentatie
|---|---|--|
| Server-side encryptie in rust: door Microsoft beheerde sleutels | Ja | Met behulp van opslagserviceversleuteling voor opslagaccounts. |  |
| Server-side encryptie in rust: door de klant beheerde sleutels (BYOK) | Nee |  |  |
| Versleuteling op kolomniveau (Azure Data Services)| Nee |  |  |
| Versleuteling tijdens het transport (zoals ExpressRoute-versleuteling, vnet-versleuteling en VNet-VNet-versleuteling)| Nee | HTTPS gebruiken. |  |
| API-aanroepen versleuteld| Ja |  |  |

## <a name="configuration-management"></a>Configuratiebeheer

| Beveiligingscontrole | Ja/Nee | Opmerkingen| | Documentatie
|---|---|--|
| Ondersteuning voor configuratiebeheer (versiebeheer van configuratie, enz.)| Ja|  |  |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ingebouwde beveiligingsbesturingselementen voor Azure-services](../security/fundamentals/security-controls.md).
