---
title: Vergelijking op basis van functies van de Azure API Management-lagen | Microsoft Documenten
description: In dit artikel worden API-beheerlagen vergeleken op basis van de functies die ze aanbieden.
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/01/2019
ms.author: apimpm
ms.openlocfilehash: 2e84138419986ef1033ab076b3745187812e91b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335891"
---
# <a name="feature-based-comparison-of-the-azure-api-management-tiers"></a>Vergelijking op basis van functies van de azure API-beheerlagen

Elke [prijscategorie](https://aka.ms/apimpricing) API Management biedt een aparte set functies en per [eenheidscapaciteit.](api-management-capacity.md) In de volgende tabel worden de belangrijkste functies samengevat die beschikbaar zijn in elk van de lagen. Sommige functies werken mogelijk anders of hebben verschillende mogelijkheden, afhankelijk van de laag. In dergelijke gevallen worden de verschillen genoemd in de documentatieartikelen waarin deze afzonderlijke kenmerken worden beschreven.

> [!IMPORTANT]
> Houd er rekening mee dat de projectontwikkelaarlaag is voor gebruiksaanvragen en evaluaties die niet worden gebruikt. Het biedt geen SLA.

| Functie                                                                                      | Verbruik | Developer | Basic | Standard | Premium |
| -------------------------------------------------------------------------------------------- | ----------- | --------- | ----- | -------- | ------- |
| Azure AD-integratie<sup>1</sup>                                                             | Nee          | Ja       | Nee    | Ja      | Ja     |
| Ondersteuning voor Virtual Network (VNet)                                                               | Nee          | Ja       | Nee    | Nee       | Ja     |
| Implementatie in meerdere regio's                                                                      | Nee          | Nee        | Nee    | Nee       | Ja     |
| Meerdere aangepaste domeinnamen                                                                 | Nee          | Nee        | Nee    | Nee       | Ja     |
| Ontwikkelaarsportal<sup>2</sup>                                                                 | Nee          | Ja       | Ja   | Ja      | Ja     |
| Ingebouwde cache                                                                               | Nee          | Ja       | Ja   | Ja      | Ja     |
| Ingebouwde analyses                                                                           | Nee          | Ja       | Ja   | Ja      | Ja     |
| [Zelf gehoste gateway](self-hosted-gateway-overview.md)<sup>3</sup>                           | Nee          | Ja       | Nee    | Nee       | Ja     |
| [TLS-instellingen](api-management-howto-manage-protocols-ciphers.md)                             | Ja         | Ja       | Ja   | Ja      | Ja     |
| [Externe cache](https://aka.ms/apimbyoc)                                                    | Ja         | Ja       | Ja   | Ja      | Ja     |
| [Verificatie van clientcertificaat](api-management-howto-mutual-certificates-for-clients.md) | Ja         | Ja       | Ja   | Ja      | Ja     |
| [Back-up maken en herstellen](api-management-howto-disaster-recovery-backup-restore.md)               | Nee          | Ja       | Ja   | Ja      | Ja     |
| [Beheer over Git](api-management-configuration-repository-git.md)                        | Nee          | Ja       | Ja   | Ja      | Ja     |
| API voor direct beheer                                                                        | Nee          | Ja       | Ja   | Ja      | Ja     |
| Azure Monitor-logboeken en -statistieken                                                               | Nee          | Ja       | Ja   | Ja      | Ja     |
| Statisch IP-adres                                                                                    | Nee          | Ja       | Ja   | Ja      | Ja     |

<sup>1</sup> Hiermee u Azure AD (en Azure AD B2C) gebruiken als identiteitsprovider voor gebruikersaanmelding op de ontwikkelaarsportal.<br/>
<sup>2</sup> Inclusief gerelateerde functionaliteit zoals gebruikers, groepen, problemen, toepassingen en e-mailsjablonen en meldingen.<br/>
<sup>3</sup> Beperkt tot één zelf gehoste gateway-implementatie met één gatewayknooppunt.<br/>
