---
title: Vergelijking op basis van functies van de Azure API Management-lagen | Microsoft Docs
description: In dit artikel worden API Management lagen vergeleken op basis van de functies die ze bieden.
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/15/2020
ms.author: apimpm
ms.openlocfilehash: 7ec89d985c025814d903ae9a16f9b0f5123b3571
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87056440"
---
# <a name="feature-based-comparison-of-the-azure-api-management-tiers"></a>Vergelijking op basis van functies van de Azure API Management-lagen

Elke API Management [prijs categorie](https://aka.ms/apimpricing) biedt een afzonderlijke set functies en per eenheids [capaciteit](api-management-capacity.md). De volgende tabel bevat een overzicht van de belangrijkste functies die beschikbaar zijn in elk van de lagen. Sommige functies werken mogelijk anders of hebben verschillende mogelijkheden, afhankelijk van de laag. In dergelijke gevallen worden de verschillen genoemd in de documentatie artikelen waarin deze afzonderlijke functies worden beschreven.

> [!IMPORTANT]
> Let op: de Developer-laag is voor niet-productie gebruik en evaluaties. Het biedt geen SLA.

| Functie                                                                                      | Verbruik | Developer | Basic | Standard | Premium |
| -------------------------------------------------------------------------------------------- | ----------- | --------- | ----- | -------- | ------- |
| Azure AD-integratie<sup>1</sup>                                                             | No          | Yes       | No    | Ja      | Ja     |
| Ondersteuning voor Virtual Network (VNet)                                                               | No          | Yes       | Nee    | Nee       | Yes     |
| Implementatie in meerdere regio's                                                                      | Nee          | Nee        | Nee    | Nee       | Yes     |
| Meerdere aangepaste domein namen                                                                 | Nee          | Nee        | Nee    | Nee       | Yes     |
| Ontwikkelaars Portal<sup>2</sup>                                                                 | No          | Ja       | Ja   | Ja      | Ja     |
| Ingebouwde cache                                                                               | No          | Ja       | Ja   | Ja      | Ja     |
| Ingebouwde analyse                                                                           | No          | Ja       | Ja   | Ja      | Ja     |
| [Zelf-hostende gateway](self-hosted-gateway-overview.md)<sup>3</sup>                           | No          | Yes       | Nee    | Nee       | Yes     |
| [TLS-instellingen](api-management-howto-manage-protocols-ciphers.md)                             | Ja         | Ja       | Ja   | Ja      | Ja     |
| [Externe cache](https://aka.ms/apimbyoc)                                                    | Ja         | Ja       | Ja   | Ja      | Ja     |
| [Verificatie van client certificaten](api-management-howto-mutual-certificates-for-clients.md) | Ja         | Ja       | Ja   | Ja      | Ja     |
| [Back-up en herstel](api-management-howto-disaster-recovery-backup-restore.md)               | No          | Ja       | Ja   | Ja      | Ja     |
| [Beheer via git](api-management-configuration-repository-git.md)                        | No          | Ja       | Ja   | Ja      | Ja     |
| API voor direct beheer                                                                        | No          | Ja       | Ja   | Ja      | Ja     |
| Logboeken en metrische gegevens Azure Monitor                                                               | Ja         | Ja       | Ja   | Ja      | Ja     |
| Statisch IP-adres                                                                                    | Nee          | Ja       | Ja   | Ja      | Ja     |

<sup>1</sup> Hiermee maakt u het gebruik van Azure AD (en Azure AD B2C) als een id-provider voor gebruikers aanmelding op de ontwikkelaars Portal.<br/>
<sup>2</sup> inclusief gerelateerde functionaliteit, zoals gebruikers, groepen, problemen, toepassingen en e-mail sjablonen en-meldingen.<br/>
<sup>3</sup> in de zelf-hostende gateways van het ontwikkelaars niveau zijn beperkt tot één gateway-knoop punt.<br/>
