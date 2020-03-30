---
title: Beperkingen van B2B-samenwerking - Azure Active Directory | Microsoft Documenten
description: Huidige beperkingen voor Azure Active Directory B2B-samenwerking
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: ffee01488ecf658ce02a20a41252aca19288667c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263359"
---
# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Beperkingen van Azure AD B2B-samenwerking
B2B-samenwerking (Azure Active Directory ( Azure AD) is momenteel onderworpen aan de beperkingen die in dit artikel worden beschreven.

## <a name="possible-double-multi-factor-authentication"></a>Mogelijke dubbele multi-factor authenticatie
Met Azure AD B2B u multi-factor authenticatie afdwingen bij de resourceorganisatie (de uitnodigende organisatie). De redenen voor deze aanpak zijn beschreven in [Voorwaardelijke toegang voor B2B-samenwerkingsgebruikers.](conditional-access.md) Als een partner al multi-factor authenticatie heeft ingesteld en afgedwongen, moeten hun gebruikers de verificatie mogelijk eenmaal uitvoeren in hun thuisorganisatie en vervolgens opnieuw in de jouwe.

## <a name="instant-on"></a>Direct-aan
In de B2B-samenwerkingsstromen voegen we gebruikers toe aan de directory en werken we deze dynamisch bij tijdens het inwisselen van uitnodigingen, app-toewijzing, enzovoort. De updates en schrijfbewerkingen vinden gewoonlijk plaats in één mapinstantie en moeten in alle instanties worden gerepliceerd. Replicatie wordt voltooid zodra alle instanties zijn bijgewerkt. Soms wanneer het object in één instantie wordt geschreven of bijgewerkt en de aanroep om dit object op te halen naar een andere instantie is, kunnen replicatielatencies optreden. Als dat gebeurt, vernieuwen of opnieuw proberen om te helpen. Als u het schrijven van een app met behulp van onze API, dan retries met een aantal back-off is een goede, defensieve praktijk om dit probleem te verlichten.

## <a name="azure-ad-directories"></a>Azure AD-mappen
Azure AD B2B is onderworpen aan azure AD-servicemaplimieten. Zie [Azure AD-servicelimieten en -beperkingen](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions)voor meer informatie over het aantal mappen dat een gebruiker of gastgebruiker kan maken en het aantal mappen waartoe een gebruiker of gastgebruiker kan behoren.

## <a name="national-clouds"></a>Nationale wolken
[Nationale clouds](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud) zijn fysiek geïsoleerde exemplaren van Azure. B2B-samenwerking wordt niet ondersteund over de landsgrenzen heen. Als uw Azure-tenant zich bijvoorbeeld in de openbare, globale cloud bevindt, u een gebruiker wiens account zich in een nationale cloud bevindt, niet uitnodigen. Als u met de gebruiker wilt samenwerken, vraagt u deze aan u om een ander e-mailadres of maakt u een gebruikersaccount voor leden in uw directory.

## <a name="azure-us-government-clouds"></a>Azure Us Government clouds
Binnen de Azure US Government-cloud wordt B2B-samenwerking momenteel alleen ondersteund tussen tenants die zich zowel in de Azure US Government-cloud bevinden als die beide B2B-samenwerking ondersteunen. Als u een gebruiker uitnodigt in een tenant die geen deel uitmaakt van de Azure US Government-cloud of die nog geen B2B-samenwerking ondersteunt, mislukt de uitnodiging of kan de gebruiker de uitnodiging niet inwisselen. Zie [Azure Active Directory Premium P1- en P2-variaties](https://docs.microsoft.com/azure/azure-government/documentation-government-services-securityandidentity#azure-active-directory-premium-p1-and-p2)voor meer informatie over andere beperkingen.

### <a name="how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant"></a>Hoe weet ik of B2B-samenwerking beschikbaar is in mijn Azure US Government-tenant?
Ga als volgt te werk om erachter te komen of uw Azure US Government-cloudtenant B2B-samenwerking ondersteunt:

1. Ga in een browser naar de volgende URL en vervang uw tenantnaam voor * &lt;tenantname:&gt;*

   `https://login.microsoftonline.com/<tenantname>/v2.0/.well-known/openid-configuration`

2. Vind `"tenant_region_scope"` in de JSON reactie:

   - Indien `"tenant_region_scope":"USGOV”` verschijnt, wordt B2B ondersteund.
   - Indien `"tenant_region_scope":"USG"` verschijnt, wordt B2B niet ondersteund.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen over Azure AD B2B-samenwerking:

- [Wat is Azure AD B2B-samenwerking?](what-is-b2b.md)
- [B2B-uitnodigingen voor samenwerking delegeren](delegate-invitations.md)

