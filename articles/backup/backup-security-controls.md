---
title: Beveiligings controles voor Azure Backup
description: Een controle lijst met beveiligings controles voor het evalueren van Azure Backup
ms.reviewer: mbaldwin
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: dacurwin
ms.openlocfilehash: 6c4fbdf378b1981e28f493e5a4e232ebbc0bbda0
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886514"
---
# <a name="security-controls-for-azure-backup"></a>Beveiligings controles voor Azure Backup

In dit artikel worden de beveiligings besturings elementen gedocumenteerd die zijn ingebouwd in Azure Backup. 

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Netwerk

| Beveiligings beheer | Ja/Nee | Opmerkingen |
|---|---|--|
| Ondersteuning voor service-eind punten| Nee |  |
| Ondersteuning voor VNet-injectie| Nee |  |
| Ondersteuning voor netwerk isolatie en firewalling| Ja | Geforceerde tunneling wordt ondersteund voor VM-back-ups. Geforceerde tunneling wordt niet ondersteund voor werk belastingen die in Vm's worden uitgevoerd. |
| Ondersteuning voor geforceerde tunneling| Nee |  |

## <a name="monitoring--logging"></a>& Logboek registratie controleren

| Beveiligings beheer | Ja/Nee | Opmerkingen|
|---|---|--|
| Ondersteuning voor Azure-bewaking (log Analytics, app Insights, enz.)| Ja | Log Analytics wordt ondersteund via Diagnostische logboeken. Zie [Azure backup beveiligde werk belastingen bewaken met behulp van log Analytics](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/) voor meer informatie. |
| Logboek registratie en controle op het vlak van controle en beheer| Ja | Alle door de klant geactiveerde acties van de Azure Portal worden vastgelegd in activiteiten Logboeken. |
| Logboek registratie en controle van het gegevens vlak| Nee | Azure Backup gegevens vlak kan niet rechtstreeks worden bereikt.  |

## <a name="identity"></a>Identiteit

| Beveiligings beheer | Ja/Nee | Opmerkingen|
|---|---|--|
| Authentication| Ja | Verificatie is via Azure Active Directory. |
| Authorization| Ja | Door de klant gemaakte en ingebouwde RBAC-rollen worden gebruikt. Zie [Access Control op basis van rollen gebruiken om Azure backup herstel punten te beheren](/azure/backup/backup-rbac-rs-vault) voor meer informatie. |

## <a name="data-protection"></a>Gegevensbescherming

| Beveiligings beheer | Ja/Nee | Opmerkingen |
|---|---|--|
| Versleuteling aan server zijde bij rest: Door micro soft beheerde sleutels | Ja | Storage-service versleuteling gebruiken voor opslag accounts. |
| Versleuteling aan server zijde op rest: door de klant beheerde sleutels (BYOK) | Nee |  |
| Versleuteling op kolom niveau (Azure Data Services)| Nee |  |
| Versleuteling in transit (zoals ExpressRoute-versleuteling, in VNet-versleuteling en VNet-VNet-versleuteling)| Nee | HTTPS gebruiken. |
| Versleutelde API-aanroepen| Ja |  |

## <a name="configuration-management"></a>Configuratiebeheer

| Beveiligings beheer | Ja/Nee | Opmerkingen|
|---|---|--|
| Ondersteuning voor configuratie beheer (versie van configuratie, enz.)| Ja|  |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ingebouwde beveiligings controles in Azure-Services](../security/fundamentals/security-controls.md).