---
title: Beveiligingsbesturingselementen voor Azure Event Hubs
description: In dit artikel vindt u een checklist met beveiligingscontroles voor de evaluatie van Azure Event Hubs (netwerk, identiteit, gegevensbescherming, enz.).
services: event-hubs
ms.service: event-hubs
author: spelluru
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: spelluru
ms.openlocfilehash: 0769e88eb72b5b347dd9ebf4b1634501ca54098e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76309503"
---
# <a name="security-controls-for-azure-event-hubs"></a>Beveiligingsbesturingselementen voor Azure Event Hubs

In dit artikel worden de beveiligingsbesturingselementen die zijn ingebouwd in Azure Event Hubs document.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Netwerk

| Beveiligingscontrole | Ja/Nee | Opmerkingen | Documentatie |
|---|---|--|--|
| Ondersteuning voor serviceeindpunten| Ja |  |  |
| Ondersteuning voor VNet-injectie| Nee | |  |
| Ondersteuning voor netwerkisolatie en firewalling| Ja |  |  |
| Ondersteuning voor gedwongen tunneling| Nee |  |  |

## <a name="monitoring--logging"></a>Controle & logboekregistratie

| Beveiligingscontrole | Ja/Nee | Opmerkingen| Documentatie |
|---|---|--|--|
| Azure-bewakingsondersteuning (logboekanalyses, app-inzichten, enz.)| Ja | |  |
| Logboekregistratie en audit van het controle- en beheervlak| Ja |  |  |
| Logboekregistratie en -audit van gegevensvliegtuigen| Ja |   |  |

## <a name="identity"></a>Identiteit

| Beveiligingscontrole | Ja/Nee | Opmerkingen| Documentatie |
|---|---|--|--|
| Authentication| Ja | | [Toegang tot Azure Event Hubs autoriseren,](authorize-access-event-hubs.md) [toegang tot Gebeurtenishubs-bronnen autoriseren met Azure Active Directory](authorize-access-azure-active-directory.md), Toegang tot bronnen voor [gebeurtenishubs toestaan met shared access-handtekeningen](authorize-access-shared-access-signature.md) |
| Autorisatie|  Ja | | [Een beheerde identiteit verifiëren met Azure Active Directory om toegang te krijgen tot Gebeurtenishubsresources](authenticate-managed-identity.md), [Een toepassing verifiëren met Azure Active Directory om toegang te krijgen tot Bronnen van Event Hubs](authenticate-application.md), Toegang tot Bronnen van [Gebeurtenishubs verifiëren met behulp van sas (Shared Access Signatures)](authenticate-shared-access-signature.md) |

## <a name="data-protection"></a>Gegevensbeveiliging

| Beveiligingscontrole | Ja/Nee | Opmerkingen | Documentatie |
|---|---|--|--|
| Server-side encryptie in rust: door Microsoft beheerde sleutels |  Ja | |  |
| Server-side encryptie in rust: door de klant beheerde sleutels (BYOK) | Ja. Beschikbaar voor speciale clusters. | Een door de klant beheerde sleutel in Azure KeyVault kan worden gebruikt om de gegevens in rust op een gebeurtenishub te versleutelen. | [Door de klant beheerde sleutels configureren voor het in rust versleutelen van Azure Event Hubs-gegevens met behulp van de Azure-portal](configure-customer-managed-key.md) |
| Versleuteling op kolomniveau (Azure Data Services)| N.v.t. | |  |
| Versleuteling tijdens het transport (zoals ExpressRoute-versleuteling, vnet-versleuteling en VNet-VNet-versleuteling)| Ja | |  |
| API-aanroepen versleuteld| Ja |  |  |

## <a name="configuration-management"></a>Configuratiebeheer

| Beveiligingscontrole | Ja/Nee | Opmerkingen| Documentatie |
|---|---|--|--|
| Ondersteuning voor configuratiebeheer (versiebeheer van configuratie, enz.)| Ja | |  |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ingebouwde beveiligingsbesturingselementen voor Azure-services](../security/fundamentals/security-controls.md).
