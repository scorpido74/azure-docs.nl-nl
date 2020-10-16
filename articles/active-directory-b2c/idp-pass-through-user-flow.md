---
title: Een toegangs token door geven via een gebruikers stroom naar uw app
titleSuffix: Azure AD B2C
description: Meer informatie over het door geven van een toegangs token voor OAuth 2,0-id-providers als een claim in een gebruikers stroom in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/17/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5b834dda926b7da1241a325e1453143eccafaf30
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87488768"
---
# <a name="pass-an-access-token-through-a-user-flow-to-your-application-in-azure-active-directory-b2c"></a>Geef een toegangs token door via een gebruikers stroom naar uw toepassing in Azure Active Directory B2C

Een [gebruikers stroom](user-flow-overview.md) in Azure Active Directory B2C (Azure AD B2C) biedt gebruikers van uw toepassing de mogelijkheid zich aan te melden of zich aan te melden met een id-provider. Wanneer de rit wordt gestart, ontvangt Azure AD B2C een [toegangs token](tokens-overview.md) van de ID-provider. Azure AD B2C gebruikt dat token om informatie over de gebruiker op te halen. U schakelt een claim in uw gebruikers stroom in om het token door te geven aan de toepassingen die u registreert in Azure AD B2C.

Azure AD B2C biedt momenteel alleen ondersteuning voor het door geven van het toegangs token van [OAuth 2,0](authorization-code-flow.md) -id-providers, zoals [Facebook](identity-provider-facebook.md) en [Google](identity-provider-google.md). Voor alle andere id-providers wordt de claim leeg geretourneerd.

## <a name="prerequisites"></a>Vereisten

* Uw toepassing moet een [aanbevolen gebruikers stroom](user-flow-versions.md)gebruiken.
* Uw gebruikers stroom is geconfigureerd met een OAuth 2,0-ID-provider.

## <a name="enable-the-claim"></a>Claim inschakelen

1. Meld u als globale beheerder van de Azure AD B2C-tenant aan bij [Azure Portal](https://portal.azure.com/).
2. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-Tenant bevat. Selecteer het filter **Directory + abonnement** in het bovenste menu en kies de map die uw Tenant bevat.
3. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
4. Selecteer **gebruikers stromen (beleid)** en selecteer vervolgens uw gebruikers stroom. Bijvoorbeeld **B2C_1_signupsignin1**.
5. Selecteer **Toepassingsclaims**.
6. Schakel de claim van het **toegangs token van de identiteits provider** in.

    ![De claim van het toegangs token van de identiteits provider inschakelen](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-app-claim.png)

7. Klik op **Opslaan** om de gebruikers stroom op te slaan.

## <a name="test-the-user-flow"></a>De gebruikersstroom testen

Bij het testen van uw toepassingen in Azure AD B2C kan het nuttig zijn om het Azure AD B2C-token te retour neren om `https://jwt.ms` de claims erin te controleren.

1. Selecteer op de pagina overzicht van de gebruikers stroom de optie **gebruikers stroom uitvoeren**.
2. Selecteer voor **toepassing**de toepassing die u eerder hebt geregistreerd. Voor een overzicht van het token in het onderstaande voor beeld moet de **antwoord-URL** worden weer gegeven `https://jwt.ms` .
3. Klik op **gebruikers stroom uitvoeren**en meld u aan met uw account referenties. U ziet het toegangs token van de ID-provider in de **idp_access_token** claim.

    Het volgende voor beeld ziet er ongeveer uit:

    ![Gedecodeer token in jwt.ms met idp_access_token blok gemarkeerd](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-token.PNG)

## <a name="next-steps"></a>Volgende stappen

Meer informatie vindt u in het [overzicht van Azure AD B2C-tokens](tokens-overview.md).
