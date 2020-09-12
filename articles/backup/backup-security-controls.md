---
title: Beveiligingsmaatregelen
description: Meer informatie over de beveiligings opties die worden gebruikt in de Azure Backup-service. Deze besturings elementen helpen de service om beveiligings problemen te voor komen, te detecteren en erop te reageren.
ms.topic: conceptual
ms.date: 09/23/2019
ms.openlocfilehash: 7ff3ff5c1b024a228778b0214e67239d3c8ab721
ms.sourcegitcommit: 9c262672c388440810464bb7f8bcc9a5c48fa326
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/03/2020
ms.locfileid: "89418752"
---
# <a name="security-controls-for-azure-backup"></a>Beveiligings controles voor Azure Backup

In dit artikel worden de beveiligings besturings elementen gedocumenteerd die zijn ingebouwd in Azure Backup.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Netwerk

| Beveiligings beheer | Ja/Nee | Notities | Documentatie
|---|---|--|--|
| Ondersteuning voor service-eind punten| No |  |  |
| Ondersteuning voor VNet-injectie| No |  |  |
| Ondersteuning voor netwerk isolatie en firewalling| Yes | |  |
| Ondersteuning voor geforceerde tunneling voor Azure-Vm's | Yes  |  |  |
| Ondersteuning voor geforceerde tunneling voor toepassingen die worden uitgevoerd in virtuele machines van Azure| No  |  |  |

## <a name="monitoring--logging"></a>& logboek registratie controleren

| Beveiligings beheer | Ja/Nee | Notities| Documentatie
|---|---|--|--|
| Ondersteuning voor Azure-bewaking (zoals log Analytics, app Insights)| Yes | Log Analytics wordt ondersteund via resource Logboeken. Zie [Azure backup beveiligde werk belastingen bewaken met log Analytics](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/)voor meer informatie. |  |
| Logboek registratie en controle op het vlak van controle en beheer| Yes | Alle door de klant geactiveerde acties van de Azure Portal worden vastgelegd in activiteiten Logboeken. |  |
| Logboek registratie en controle van het gegevens vlak| No | Azure Backup gegevens vlak kan niet rechtstreeks worden bereikt.  |  |

## <a name="identity"></a>Identiteit

| Beveiligings beheer | Ja/Nee | Notities| Documentatie
|---|---|--|--|
| Verificatie| Yes | Verificatie is via Azure Active Directory. |  |
| Autorisatie| Yes | De door klanten gemaakte en ingebouwde rollen van Azure worden gebruikt. Zie [Access Control op rollen gebaseerd gebruiken om Azure backup herstel punten te beheren](./backup-rbac-rs-vault.md)voor meer informatie. |  |

## <a name="data-protection"></a>Gegevensbeveiliging

| Beveiligings beheer | Ja/Nee | Notities | Documentatie
|---|---|--|--|
| Versleuteling aan server zijde op rest: door micro soft beheerde sleutels | Yes | Storage-service versleuteling gebruiken voor opslag accounts. |  |
| Versleuteling aan server zijde op rest: door de klant beheerde sleutels (BYOK) | No |  |  |
| Versleuteling op kolom niveau (Azure Data Services)| No |  |  |
| Versleuteling in transit (zoals ExpressRoute-versleuteling, in VNet-versleuteling en VNet-VNet-versleuteling)| No | HTTPS gebruiken. |  |
| Versleutelde API-aanroepen| Yes |  |  |

## <a name="configuration-management"></a>Configuratiebeheer

| Beveiligings beheer | Ja/Nee | Notities| Documentatie
|---|---|--|--|
| Ondersteuning voor configuratie beheer (versie van configuratie, enzovoort)| Ja|  |  |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ingebouwde beveiligings controles in Azure-Services](../security/fundamentals/security-controls.md).
