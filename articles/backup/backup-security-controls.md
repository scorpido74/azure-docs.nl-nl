---
title: Beveiligingsmaatregelen
description: Meer informatie over de beveiligings opties die worden gebruikt in de Azure Backup-service. Deze besturings elementen helpen de service om beveiligings problemen te voor komen, te detecteren en erop te reageren.
ms.topic: conceptual
ms.date: 09/23/2019
ms.openlocfilehash: 7d8fd98467e975c6151d09779ab450810ea32c6e
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86165563"
---
# <a name="security-controls-for-azure-backup"></a>Beveiligings controles voor Azure Backup

In dit artikel worden de beveiligings besturings elementen gedocumenteerd die zijn ingebouwd in Azure Backup.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Netwerk

| Beveiligings beheer | Ja/Nee | Opmerkingen | Documentatie
|---|---|--|--|
| Ondersteuning voor service-eind punten| Nee |  |  |
| Ondersteuning voor VNet-injectie| Nee |  |  |
| Ondersteuning voor netwerk isolatie en firewalling| Ja | Geforceerde tunneling wordt ondersteund voor VM-back-ups. Geforceerde tunneling wordt niet ondersteund voor werk belastingen die in Vm's worden uitgevoerd. |  |
| Ondersteuning voor geforceerde tunneling| Nee |  |  |

## <a name="monitoring--logging"></a>& logboek registratie controleren

| Beveiligings beheer | Ja/Nee | Opmerkingen| Documentatie
|---|---|--|--|
| Ondersteuning voor Azure-bewaking (log Analytics, app Insights, enz.)| Ja | Log Analytics wordt ondersteund via resource Logboeken. Zie [Azure backup beveiligde werk belastingen bewaken met log Analytics](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/)voor meer informatie. |  |
| Logboek registratie en controle op het vlak van controle en beheer| Ja | Alle door de klant geactiveerde acties van de Azure Portal worden vastgelegd in activiteiten Logboeken. |  |
| Logboek registratie en controle van het gegevens vlak| Nee | Azure Backup gegevens vlak kan niet rechtstreeks worden bereikt.  |  |

## <a name="identity"></a>Identiteit

| Beveiligings beheer | Ja/Nee | Opmerkingen| Documentatie
|---|---|--|--|
| Verificatie| Ja | Verificatie is via Azure Active Directory. |  |
| Autorisatie| Ja | Door de klant gemaakte en ingebouwde RBAC-rollen worden gebruikt. Zie [Access Control op rollen gebaseerd gebruiken om Azure backup herstel punten te beheren](/azure/backup/backup-rbac-rs-vault)voor meer informatie. |  |

## <a name="data-protection"></a>Gegevensbeveiliging

| Beveiligings beheer | Ja/Nee | Opmerkingen | Documentatie
|---|---|--|--|
| Versleuteling aan server zijde op rest: door micro soft beheerde sleutels | Ja | Storage-service versleuteling gebruiken voor opslag accounts. |  |
| Versleuteling aan server zijde op rest: door de klant beheerde sleutels (BYOK) | Nee |  |  |
| Versleuteling op kolom niveau (Azure Data Services)| Nee |  |  |
| Versleuteling in transit (zoals ExpressRoute-versleuteling, in VNet-versleuteling en VNet-VNet-versleuteling)| Nee | HTTPS gebruiken. |  |
| Versleutelde API-aanroepen| Ja |  |  |

## <a name="configuration-management"></a>Configuratiebeheer

| Beveiligings beheer | Ja/Nee | Opmerkingen| Documentatie
|---|---|--|--|
| Ondersteuning voor configuratie beheer (versie van configuratie, enz.)| Ja|  |  |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ingebouwde beveiligings controles in Azure-Services](../security/fundamentals/security-controls.md).
