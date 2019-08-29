---
title: Vergelijking op basis van functies van de Azure API Management-lagen | Microsoft Docs
description: In dit artikel worden API Management lagen vergeleken op basis van de functies die ze bieden.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/26/2018
ms.author: apimpm
ms.openlocfilehash: bc5eab6bb5044e474c386d3e5771d8517e6ba9cf
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072536"
---
# <a name="feature-based-comparison-of-the-azure-api-management-tiers"></a>Vergelijking op basis van functies van de Azure API Management-lagen

>[!IMPORTANT]
> Let op: de Developer-laag is voor niet-productie gebruik en evaluaties. Het biedt geen SLA. 

Elke API Management [prijs categorie](https://aka.ms/apimpricing) biedt een afzonderlijke set functies en per eenheids [capaciteit](api-management-capacity.md). De volgende tabel bevat een overzicht van de belangrijkste functies die beschikbaar zijn in elk van de lagen. Sommige functies werken mogelijk anders of hebben verschillende mogelijkheden, afhankelijk van de laag. In dergelijke gevallen worden de verschillen genoemd in de documentatie artikelen waarin deze afzonderlijke functies worden beschreven.

| Functie                                                                                      | Verbruik | Ontwikkelaar      | Basic          | Standard       | Premium        |
| -------------------------------------------------------------------------------------------- | ----------------------------- | -------------- | -------------- | -------------- | -------------- |
| Azure AD-integratie<sup>1</sup>                                                             | Nee                            | Ja            | Nee             | Ja            | Ja            |
| Ondersteuning voor Virtual Network (VNet)                                                               | Nee                            | Ja            | Nee             | Nee             | Ja            |
| Implementatie in meerdere regio's                                                                      | Nee                            | Nee             | Nee             | Nee             | Ja            |
| Meerdere aangepaste domeinnamen                                                                 | Nee                            | Nee             | Nee             | Nee             | Ja            |
| Ontwikkelaars Portal<sup>2</sup>                                                                 | Nee                            | Ja            | Ja            | Ja            | Ja            |
| Ingebouwde cache                                                                               | Nee                            | Ja            | Ja            | Ja            | Ja            |
| Ingebouwde analytics                                                                           | Nee                            | Ja            | Ja            | Ja            | Ja            |
| [SSL-instellingen](api-management-howto-manage-protocols-ciphers.md)                             | Ja                            | Ja            | Ja            | Ja            | Ja            |
| [Externe cache](https://aka.ms/apimbyoc)                                                    | Ja                           | Ja            | Ja            | Ja            | Ja            |
| [Verificatie van client certificaten](api-management-howto-mutual-certificates-for-clients.md) | Ja                | Ja            | Ja            | Ja            | Ja            |
| [Back-up en herstel](api-management-howto-disaster-recovery-backup-restore.md)               | Nee                            | Ja            | Ja            | Ja            | Ja            |
| [Beheer via git](api-management-configuration-repository-git.md)                        | Nee                            | Ja            | Ja            | Ja            | Ja            |
| API voor direct beheer                                                                        | Nee                            | Ja            | Ja            | Ja            | Ja            |
| Logboeken en metrische gegevens Azure Monitor                                                               | Nee                | Ja            | Ja            | Ja            | Ja            |
| Statisch IP-adres                                                               | Nee                | Ja            | Ja            | Ja            | Ja            |

<sup>1</sup> Hiermee maakt u het gebruik van Azure AD (en Azure AD B2C) als een id-provider voor gebruikers aanmelding op de ontwikkelaars Portal.<br/>
<sup>2</sup> inclusief gerelateerde functionaliteit, zoals gebruikers, groepen, problemen, toepassingen en e-mail sjablonen en-meldingen.<br/>
