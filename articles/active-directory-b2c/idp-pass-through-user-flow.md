---
title: Geef een toegangs token door via een gebruikers stroom naar uw toepassing-Azure Active Directory B2C
description: Meer informatie over het door geven van een toegangs token voor OAuth 2,0-id-providers als een claim in een gebruikers stroom in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a1a9ad0c615e50db746548a1ed5b557fe6fdea5a
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064285"
---
# <a name="pass-an-access-token-through-a-user-flow-to-your-application-in-azure-active-directory-b2c"></a>Geef een toegangs token door via een gebruikers stroom naar uw toepassing in Azure Active Directory B2C

Een [gebruikers stroom](active-directory-b2c-reference-policies.md) in Azure Active Directory B2C (Azure AD B2C) biedt gebruikers van uw toepassing de mogelijkheid zich aan te melden of zich aan te melden met een id-provider. Wanneer de rit wordt gestart, ontvangt Azure AD B2C een [toegangs token](active-directory-b2c-reference-tokens.md) van de ID-provider. Azure AD B2C gebruikt dat token om informatie over de gebruiker op te halen. U schakelt een claim in uw gebruikers stroom in om het token door te geven aan de toepassingen die u registreert in Azure AD B2C.

Azure AD B2C biedt momenteel alleen ondersteuning voor het door geven van het toegangs token van [OAuth 2,0](active-directory-b2c-reference-oauth-code.md) -id-providers, zoals [Facebook](active-directory-b2c-setup-fb-app.md) en [Google](active-directory-b2c-setup-goog-app.md). Voor alle andere id-providers wordt de claim leeg geretourneerd.

## <a name="prerequisites"></a>Vereisten

* Uw toepassing moet een v2- [gebruikers stroom](user-flow-versions.md)gebruiken.
* Uw gebruikers stroom is geconfigureerd met een OAuth 2,0-ID-provider.

## <a name="enable-the-claim"></a>Claim inschakelen

1. Meld u als globale beheerder van de Azure AD B2C-tenant aan bij [Azure Portal](https://portal.azure.com/).
2. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-Tenant bevat. Selecteer het filter **Directory + abonnement** in het bovenste menu en kies de map die uw Tenant bevat.
3. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
4. Selecteer **gebruikers stromen (beleid)** en selecteer vervolgens uw gebruikers stroom. Bijvoorbeeld **B2C_1_signupsignin1**.
5. Selecteer **Toepassingsclaims**.
6. Schakel de claim van het **toegangs token** van de identiteits provider in.

    ![De claim van het toegangs token van de identiteits provider inschakelen](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-app-claim.png)

7. Klik op **Opslaan** om de gebruikers stroom op te slaan.

## <a name="test-the-user-flow"></a>De gebruikers stroom testen

Bij het testen van uw toepassingen in azure AD B2C kan het nuttig zijn om het Azure AD B2C-token te `https://jwt.ms` retour neren om de claims erin te controleren.

1. Selecteer op de pagina overzicht van de gebruikers stroom de optie **gebruikers stroom uitvoeren**.
2. Selecteer voor **toepassing**de toepassing die u eerder hebt geregistreerd. Voor een overzicht van het token in het onderstaande voor beeld moet de antwoord `https://jwt.ms`- **URL** worden weer gegeven.
3. Klik op **gebruikers stroom uitvoeren**en meld u aan met uw account referenties. U ziet het toegangs token van de ID-provider in de claim **idp_access_token** .

    Het volgende voor beeld ziet er ongeveer uit:

    ![Gedecodeer token in jwt.ms met idp_access_token-blok gemarkeerd](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-token.PNG)

## <a name="next-steps"></a>Volgende stappen

Meer informatie vindt u in het [overzicht van Azure AD B2C](active-directory-b2c-reference-tokens.md)-tokens.
