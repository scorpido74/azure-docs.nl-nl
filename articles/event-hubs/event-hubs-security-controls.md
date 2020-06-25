---
title: Beveiligings controles voor Azure Event Hubs
description: Dit artikel bevat een controle lijst met beveiligings controles voor het evalueren van Azure-Event Hubs (netwerk, identiteit, gegevens beveiliging, enz.).
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: da20778f1e24372e445d635e675df6484905f195
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/24/2020
ms.locfileid: "85315390"
---
# <a name="security-controls-for-azure-event-hubs"></a>Beveiligings controles voor Azure Event Hubs

In dit artikel worden de beveiligings besturings elementen gedocumenteerd die zijn ingebouwd in azure Event Hubs.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Netwerk

| Beveiligings beheer | Ja/Nee | Notities | Documentatie |
|---|---|--|--|
| Ondersteuning voor service-eind punten| Yes |  |  |
| Ondersteuning voor VNet-injectie| No | |  |
| Ondersteuning voor netwerk isolatie en firewalling| Yes |  |  |
| Ondersteuning voor geforceerde tunneling| No |  |  |

## <a name="monitoring--logging"></a>& logboek registratie controleren

| Beveiligings beheer | Ja/Nee | Notities| Documentatie |
|---|---|--|--|
| Ondersteuning voor Azure-bewaking (log Analytics, app Insights, enz.)| Yes | |  |
| Logboek registratie en controle op het vlak van controle en beheer| Yes |  |  |
| Logboek registratie en controle van het gegevens vlak| Yes |   |  |

## <a name="identity"></a>Identiteit

| Beveiligings beheer | Ja/Nee | Notities| Documentatie |
|---|---|--|--|
| Verificatie| Yes | | [Toegang tot Azure Event hubs autoriseren](authorize-access-event-hubs.md), [toegang tot Event hubs resources autoriseren met behulp van Azure Active Directory](authorize-access-azure-active-directory.md), [toegang verlenen tot Event hubs resources met behulp van Shared Access signatures](authorize-access-shared-access-signature.md) |
| Autorisatie|  Yes | | [Verifieer een beheerde identiteit met Azure Active Directory om toegang te krijgen tot Event hubs resources](authenticate-managed-identity.md), [een toepassing te verifiëren met Azure Active Directory om toegang te krijgen tot Event hubs bronnen, de](authenticate-application.md) [toegang tot Event hubs resources te verifiëren met behulp van Shared Access signatures (SAS)](authenticate-shared-access-signature.md) |

## <a name="data-protection"></a>Gegevensbeveiliging

| Beveiligings beheer | Ja/Nee | Notities | Documentatie |
|---|---|--|--|
| Versleuteling aan server zijde op rest: door micro soft beheerde sleutels |  Yes | |  |
| Versleuteling aan server zijde op rest: door de klant beheerde sleutels (BYOK) | Ja. Beschikbaar voor toegewezen clusters. | Een door de klant beheerde sleutel in azure-hoofd kluis kan worden gebruikt voor het versleutelen van de gegevens op een event hub in rust. | [Door de klant beheerde sleutels voor het versleutelen van Azure Event Hubs-gegevens op rest configureren met behulp van de Azure Portal](configure-customer-managed-key.md) |
| Versleuteling op kolom niveau (Azure Data Services)| N.v.t. | |  |
| Versleuteling in transit (zoals ExpressRoute-versleuteling, in VNet-versleuteling en VNet-VNet-versleuteling)| Yes | |  |
| Versleutelde API-aanroepen| Yes |  |  |

## <a name="configuration-management"></a>Configuratiebeheer

| Beveiligings beheer | Ja/Nee | Notities| Documentatie |
|---|---|--|--|
| Ondersteuning voor configuratie beheer (versie van configuratie, enz.)| Ja | |  |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ingebouwde beveiligings controles in Azure-Services](../security/fundamentals/security-controls.md).
