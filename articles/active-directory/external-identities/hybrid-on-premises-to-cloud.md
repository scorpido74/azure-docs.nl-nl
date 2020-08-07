---
title: Lokale partner accounts synchroniseren met de Cloud als B2B-gebruikers-Azure AD
description: Lokaal beheerde externe partners toegang geven tot zowel lokale als cloud resources met dezelfde referenties met Azure AD B2B-samen werking.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 04/24/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76b17391008160cfea9cbf029932d7081466cf3d
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87908619"
---
# <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources-using-azure-ad-b2b-collaboration"></a>Lokaal beheerde partner accounts toegang verlenen tot cloud resources met behulp van Azure AD B2B-samen werking

Voor Azure Active Directory (Azure AD) hebben organisaties met on-premises identiteits systemen traditioneel beheerde partner accounts in hun on-premises Directory. Wanneer u in een dergelijke organisatie apps gaat verplaatsen naar Azure AD, wilt u er zeker van zijn dat uw partners toegang hebben tot de resources die ze nodig hebben. Het is niet belang rijk of de bronnen on-premises of in de Cloud zijn. Daarnaast wilt u dat uw partner gebruikers dezelfde aanmeldings referenties kunnen gebruiken voor zowel on-premises als Azure AD-resources. 

Als u accounts voor uw externe partners in uw on-premises Directory maakt (u maakt bijvoorbeeld een account met de aanmeldings naam ' wmoran ' voor een externe gebruiker met de naam Wendy Moran in uw partners.contoso.com-domein), kunt u deze accounts nu synchroniseren met de Cloud. Met name kunt u Azure AD Connect de partner accounts synchroniseren met de Cloud als Azure AD B2B-gebruikers (dat wil zeggen, gebruikers met User type = gast). Hierdoor kunnen uw partner gebruikers toegang krijgen tot Cloud bronnen met dezelfde referenties als hun lokale accounts, zonder dat ze meer toegang hebben dan ze nodig hebben. 

## <a name="identify-unique-attributes-for-usertype"></a>Unieke kenmerken voor User type identificeren

Voordat u synchronisatie van het kenmerk User type inschakelt, moet u eerst bepalen hoe u het kenmerk User type van on-premises Active Directory afleiden. Met andere woorden, welke para meters in uw on-premises omgeving zijn uniek voor uw externe deel nemers? Bepaal een para meter die deze externe deel nemers onderscheidt van leden van uw eigen organisatie.

Er zijn twee veelvoorkomende benaderingen voor:

- Geef een niet-gebruikt on-premises Active Directory kenmerk (bijvoorbeeld extensionAttribute1) op om te gebruiken als bron kenmerk. 
- U kunt ook de waarde voor het kenmerk User type afleiden uit andere eigenschappen. U wilt bijvoorbeeld alle gebruikers synchroniseren als gast als hun on-premises Active Directory UserPrincipalName kenmerk eindigt met het domein * \@ partners.contoso.com*.
 
Zie [synchronisatie van User type inschakelen](../hybrid/how-to-connect-sync-change-the-configuration.md#enable-synchronization-of-usertype)voor gedetailleerde kenmerk vereisten. 

## <a name="configure-azure-ad-connect-to-sync-users-to-the-cloud"></a>Azure AD Connect configureren voor het synchroniseren van gebruikers naar de Cloud

Nadat u het unieke kenmerk hebt geïdentificeerd, kunt u Azure AD Connect configureren om deze gebruikers te synchroniseren met de Cloud als Azure AD B2B-gebruikers (dat wil zeggen, gebruikers met User type = gast). Vanuit een autorisatie punt van de weer gave zijn deze gebruikers niet te onderscheiden van B2B-gebruikers die zijn gemaakt via het uitnodigings proces van Azure AD B2B-samen werking.

Zie [synchronisatie van User type inschakelen](../hybrid/how-to-connect-sync-change-the-configuration.md#enable-synchronization-of-usertype)voor instructies voor de implementatie.

## <a name="next-steps"></a>Volgende stappen

- [Azure Active Directory B2B-samen werking voor hybride organisaties](hybrid-organizations.md)
- [B2B-gebruikers in azure AD toegang verlenen tot uw on-premises toepassingen](hybrid-cloud-to-on-premises.md)
- Zie [uw on-premises Directory's integreren met Azure Active Directory](../hybrid/whatis-hybrid-identity.md)voor een overzicht van Azure AD Connect.

