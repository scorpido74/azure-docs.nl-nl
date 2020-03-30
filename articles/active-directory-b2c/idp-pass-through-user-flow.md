---
title: Een toegangstoken doorgeven via een gebruikersstroom aan uw app
titleSuffix: Azure AD B2C
description: Meer informatie over het doorgeven van een toegangstoken voor OAuth 2.0-identiteitsproviders als claim in een gebruikersstroom in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 312d093548b6e3cf3654f45d7610e8fc474a87b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187777"
---
# <a name="pass-an-access-token-through-a-user-flow-to-your-application-in-azure-active-directory-b2c"></a>Een toegangstoken doorgeven via een gebruikersstroom aan uw toepassing in Azure Active Directory B2C

Een [gebruikersstroom](user-flow-overview.md) in Azure Active Directory B2C (Azure AD B2C) biedt gebruikers van uw toepassing de mogelijkheid om zich aan te melden of zich aan te melden bij een identiteitsprovider. Wanneer de reis begint, ontvangt Azure AD B2C een [toegangstoken](tokens-overview.md) van de identiteitsprovider. Azure AD B2C gebruikt dat token om informatie over de gebruiker op te halen. U schakelt een claim in uw gebruikersstroom in om het token door te geven aan de toepassingen die u registreert in Azure AD B2C.

Azure AD B2C ondersteunt momenteel alleen het doorgeven van het toegangstoken van [OAuth 2.0-identiteitsproviders,](authorization-code-flow.md) waaronder [Facebook](identity-provider-facebook.md) en [Google.](identity-provider-google.md) Voor alle andere identiteitsproviders wordt de claim leeg geretourneerd.

## <a name="prerequisites"></a>Vereisten

* Uw toepassing moet een [v2-gebruikersstroom gebruiken.](user-flow-versions.md)
* Uw gebruikersstroom is geconfigureerd met een OAuth 2.0-identiteitsprovider.

## <a name="enable-the-claim"></a>De claim inschakelen

1. Meld u als globale beheerder van de Azure AD B2C-tenant aan bij [Azure Portal](https://portal.azure.com/).
2. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-tenant bevat. Selecteer het **filter Directory + abonnement** in het bovenste menu en kies de map met uw tenant.
3. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
4. Selecteer **Gebruikersstromen (beleidsregels)** en selecteer vervolgens de gebruikersstroom. bijvoorbeeld **B2C_1_signupsignin1**.
5. Selecteer **Toepassingsclaims**.
6. Schakel de **claim van Access Token van de identiteitsprovider** in.

    ![De claim voor Access Token van de identiteitsprovider inschakelen](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-app-claim.png)

7. Klik **op Opslaan** om de gebruikersstroom op te slaan.

## <a name="test-the-user-flow"></a>De gebruikersstroom testen

Wanneer u uw toepassingen in Azure AD B2C test, kan het handig `https://jwt.ms` zijn om het Azure AD B2C-token terug te laten komen om de claims in de toepassing ervan te bekijken.

1. Selecteer op de pagina Overzicht van de gebruikersstroom de optie **Gebruikersstroom uitvoeren**.
2. Selecteer **voor Toepassing**uw toepassing die u eerder hebt geregistreerd. Als u het token in het onderstaande `https://jwt.ms`voorbeeld wilt zien, moet de URL voor **beantwoorden** worden weergegeven .
3. Klik **op Gebruikersstroom uitvoeren**en meld u aan met uw accountgegevens. U ziet het toegangstoken van de identiteitsprovider in de **idp_access_token** claim.

    U ziet iets dat lijkt op het volgende voorbeeld:

    ![Gedecodeerd token in jwt.ms met idp_access_token blok gemarkeerd](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-token.PNG)

## <a name="next-steps"></a>Volgende stappen

Meer informatie vindt u in het [overzicht van Azure AD B2C-tokens](tokens-overview.md).
