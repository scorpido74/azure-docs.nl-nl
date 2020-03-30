---
title: Lokale partneraccounts synchroniseren met de cloud als B2B-gebruikers - Azure AD
description: Geef lokaal beheerde externe partners toegang tot zowel lokale als cloudbronnen met dezelfde referenties met Azure AD B2B-samenwerking.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/24/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: dcc8c0538bb3362818a4172dd42905fd72b19812
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74272602"
---
# <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources-using-azure-ad-b2b-collaboration"></a>Lokaal beheerde partneraccounts toegang verlenen tot cloudbronnen met Azure AD B2B-samenwerking

Vóór Azure Active Directory (Azure AD) hebben organisaties met on-premises identiteitssystemen traditioneel partneraccounts beheerd in hun on-premises directory. Wanneer u apps naar Azure AD verplaatst, wilt u in een dergelijke organisatie ervoor zorgen dat uw partners toegang hebben tot de resources die ze nodig hebben. Het maakt niet uit of de resources on-premises of in de cloud zijn. U wilt ook dat uw partnergebruikers dezelfde aanmeldingsreferenties kunnen gebruiken voor zowel on-premises als Azure AD-resources. 

Als u accounts maakt voor uw externe partners in uw on-premises directory (u maakt bijvoorbeeld een account met een aanmeldingsnaam van 'wmoran' voor een externe gebruiker met de naam Wendy Moran in uw partners.contoso.com domein), u deze accounts nu synchroniseren met de Wolk. U azure AD Connect gebruiken om de partneraccounts naar de cloud te synchroniseren als Azure AD B2B-gebruikers (dat wil zeggen gebruikers met UserType = Gast). Hierdoor hebben uw partnergebruikers toegang tot cloudbronnen met dezelfde referenties als hun lokale accounts, zonder dat ze meer toegang hebben dan ze nodig hebben. 

## <a name="identify-unique-attributes-for-usertype"></a>Unieke kenmerken voor UserType identificeren

Voordat u synchronisatie van het kenmerk UserType inschakelt, moet u eerst beslissen hoe u het kenmerk UserType ontlenen aan on-premises Active Directory. Met andere woorden, welke parameters in uw on-premises omgeving zijn uniek voor uw externe medewerkers? Bepaal een parameter die deze externe medewerkers onderscheidt van leden van uw eigen organisatie.

Twee gemeenschappelijke benaderingen hiervoor zijn:

- Wijs een ongebruikt on-premises Active Directory-kenmerk aan (bijvoorbeeld extensieAttribuut1) om te gebruiken als bronkenmerk. 
- U ook de waarde voor het kenmerk UserType afleiden van andere eigenschappen. U wilt bijvoorbeeld alle gebruikers als gast synchroniseren als het kenmerk Active Directory * \@* UserPrincipalName eindigt met het domein partners.contoso.com .
 
Zie Synchronisatie van [UserType inschakelen](../hybrid/how-to-connect-sync-change-the-configuration.md#enable-synchronization-of-usertype)voor gedetailleerde kenmerkvereisten. 

## <a name="configure-azure-ad-connect-to-sync-users-to-the-cloud"></a>Azure AD Connect configureren om gebruikers te synchroniseren met de cloud

Nadat u het unieke kenmerk hebt geïdentificeerd, u Azure AD Connect configureren om deze gebruikers naar de cloud te synchroniseren als Azure AD B2B-gebruikers (dat wil zeggen gebruikers met UserType = Gast). Vanuit autorisatieoogpunt zijn deze gebruikers niet te onderscheiden van B2B-gebruikers die zijn gemaakt via het Azure AD B2B-uitnodigingsproces voor samenwerking.

Zie [Synchronisatie van UserType inschakelen](../hybrid/how-to-connect-sync-change-the-configuration.md#enable-synchronization-of-usertype)voor implementatie-instructies.

## <a name="next-steps"></a>Volgende stappen

- [Azure Active Directory B2B-samenwerking voor hybride organisaties](hybrid-organizations.md)
- [B2B-gebruikers in Azure AD toegang verlenen tot uw on-premises toepassingen](hybrid-cloud-to-on-premises.md)
- Zie [Uw on-premises mappen integreren met Azure Active Directory](../hybrid/whatis-hybrid-identity.md)voor een overzicht van Azure AD Connect.

