---
title: Beveiligingsmaatregelen
description: Meer informatie over de beveiligings opties die worden gebruikt in de Azure Backup-service. Deze besturings elementen helpen de service om beveiligings problemen te voor komen, te detecteren en erop te reageren.
ms.topic: conceptual
ms.date: 09/23/2019
ms.openlocfilehash: 96899c0669f3063232c36ad3ae1fec76a90e0a5c
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92090859"
---
# <a name="security-controls-for-azure-backup"></a>Beveiligings controles voor Azure Backup

In dit artikel worden de beveiligings besturings elementen gedocumenteerd die zijn ingebouwd in Azure Backup.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Netwerk

| Beveiligings beheer | Ja/Nee | Notities | Documentatie
|---|---|--|--|
| Ondersteuning voor service-eind punten| Nee |  |  |
| Ondersteuning voor VNet-injectie| Nee |  |  |
| Ondersteuning voor netwerk isolatie en firewalling| Ja | |  |
| Ondersteuning voor geforceerde tunneling voor Azure-Vm's | Ja  |  |  |
| Ondersteuning voor geforceerde tunneling voor toepassingen die worden uitgevoerd in virtuele machines van Azure| Nee  |  |  |

## <a name="monitoring--logging"></a>& logboek registratie controleren

| Beveiligings beheer | Ja/Nee | Notities| Documentatie
|---|---|--|--|
| Ondersteuning voor Azure-bewaking (zoals log Analytics, app Insights)| Ja | Log Analytics wordt ondersteund via resource Logboeken. Zie [Azure backup beveiligde werk belastingen bewaken met log Analytics](backup-azure-diagnostics-mode-data-model.md)voor meer informatie. |  |
| Logboek registratie en controle op het vlak van controle en beheer| Ja | Alle door de klant geactiveerde acties van de Azure Portal worden vastgelegd in activiteiten Logboeken. |  |
| Logboek registratie en controle van het gegevens vlak| Nee | Azure Backup gegevens vlak kan niet rechtstreeks worden bereikt.  |  |

## <a name="identity"></a>Identiteit

| Beveiligings beheer | Ja/Nee | Notities| Documentatie
|---|---|--|--|
| Verificatie| Ja | Verificatie is via Azure Active Directory. |  |
| Autorisatie| Ja | De door klanten gemaakte en ingebouwde rollen van Azure worden gebruikt. Zie [toegangs beheer op basis van rollen gebruiken voor het beheren van Azure backup herstel punten](./backup-rbac-rs-vault.md)voor meer informatie. |  |

## <a name="data-protection"></a>Gegevensbeveiliging

| Beveiligings beheer | Ja/Nee | Notities | Documentatie
|---|---|--|--|
| Versleuteling aan server zijde op rest: door micro soft beheerde sleutels | Ja | Storage-service versleuteling gebruiken voor opslag accounts. |  |
| Versleuteling aan server zijde op rest: door de klant beheerde sleutels (BYOK) | Nee |  |  |
| Versleuteling op kolom niveau (Azure Data Services)| Nee |  |  |
| Versleuteling in transit (zoals ExpressRoute-versleuteling, in VNet-versleuteling en VNet-VNet versleuteling)| Nee | HTTPS gebruiken. |  |
| Versleutelde API-aanroepen| Ja |  |  |

## <a name="configuration-management"></a>Configuratiebeheer

| Beveiligings beheer | Ja/Nee | Notities| Documentatie
|---|---|--|--|
| Ondersteuning voor configuratie beheer (versie van configuratie, enzovoort)| Ja|  |  |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ingebouwde beveiligings controles in Azure-Services](../security/fundamentals/security-controls.md).
