---
title: Beveiligings controles voor Azure Event Hubs
description: Een controle lijst met beveiligings controles voor het evalueren van Azure Event Hubs
services: event-hubs
ms.service: event-hubs
author: spelluru
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: spelluru
ms.openlocfilehash: ae357d25a37e188ed043aaa0ca750bb0e52d58da
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75903535"
---
# <a name="security-controls-for-azure-event-hubs"></a>Beveiligings controles voor Azure Event Hubs

In dit artikel worden de beveiligings besturings elementen gedocumenteerd die zijn ingebouwd in azure Event Hubs.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Netwerk

| Beveiligings beheer | Ja/Nee | Opmerkingen | Documentatie |
|---|---|--|--|
| Ondersteuning voor service-eind punten| Ja |  |  |
| Ondersteuning voor VNet-injectie| Nee | |  |
| Ondersteuning voor netwerk isolatie en firewalling| Ja |  |  |
| Ondersteuning voor geforceerde tunneling| Nee |  |  |

## <a name="monitoring--logging"></a>& Logboek registratie controleren

| Beveiligings beheer | Ja/Nee | Opmerkingen| Documentatie |
|---|---|--|--|
| Ondersteuning voor Azure-bewaking (log Analytics, app Insights, enz.)| Ja | |  |
| Logboek registratie en controle op het vlak van controle en beheer| Ja |  |  |
| Logboek registratie en controle van het gegevens vlak| Ja |   |  |

## <a name="identity"></a>Identity

| Beveiligings beheer | Ja/Nee | Opmerkingen| Documentatie |
|---|---|--|--|
| Authentication| Ja | | [Toegang tot Azure Event hubs autoriseren](authorize-access-event-hubs.md), [toegang tot Event hubs resources autoriseren met behulp van Azure Active Directory](authorize-access-azure-active-directory.md), [toegang verlenen tot Event hubs resources met behulp van Shared Access signatures](authorize-access-shared-access-signature.md) |
| Autorisatie|  Ja | | [Verifieer een beheerde identiteit met Azure Active Directory om toegang te krijgen tot Event hubs resources](authenticate-managed-identity.md), [een toepassing te verifiëren met Azure Active Directory om toegang te krijgen tot Event hubs bronnen, de](authenticate-application.md) [toegang tot Event hubs resources te verifiëren met behulp van Shared Access signatures (SAS)](authenticate-shared-access-signature.md) |

## <a name="data-protection"></a>Databeveiliging

| Beveiligings beheer | Ja/Nee | Opmerkingen | Documentatie |
|---|---|--|--|
| Versleuteling aan server zijde op rest: door micro soft beheerde sleutels |  Ja | |  |
| Versleuteling aan server zijde op rest: door de klant beheerde sleutels (BYOK) | Ja. Beschikbaar voor toegewezen clusters. | Een door de klant beheerde sleutel in azure-hoofd kluis kan worden gebruikt voor het versleutelen van de gegevens op een event hub in rust. | [Door de klant beheerde sleutels voor het versleutelen van Azure Event Hubs-gegevens op rest configureren met behulp van de Azure Portal](configure-customer-managed-key.md) |
| Versleuteling op kolom niveau (Azure Data Services)| N/A | |  |
| Versleuteling in transit (zoals ExpressRoute-versleuteling, in VNet-versleuteling en VNet-VNet-versleuteling)| Ja | |  |
| Versleutelde API-aanroepen| Ja |  |  |

## <a name="configuration-management"></a>Configuratiebeheer

| Beveiligings beheer | Ja/Nee | Opmerkingen| Documentatie |
|---|---|--|--|
| Ondersteuning voor configuratie beheer (versie van configuratie, enz.)| Ja | |  |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ingebouwde beveiligings controles in Azure-Services](../security/fundamentals/security-controls.md).
