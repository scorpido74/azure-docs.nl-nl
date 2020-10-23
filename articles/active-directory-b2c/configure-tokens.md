---
title: Tokens configureren-Azure Active Directory B2C | Microsoft Docs
description: Meer informatie over het configureren van de token levensduur en compatibiliteits instellingen in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 67bc9d6b35d4841999721a00592a6bbe23bff10f
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92340220"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Tokens configureren in Azure Active Directory B2C

In dit artikel leert u hoe u de [levens duur en compatibiliteit van een token](tokens-overview.md) in Azure Active Directory B2C (Azure AD B2C) kunt configureren.

## <a name="prerequisites"></a>Vereisten

[Maak een gebruikersstroom](tutorial-create-user-flows.md) zodat gebruikers zich bij uw toepassing kunnen registreren en aanmelden.

## <a name="configure-jwt-token-lifetime"></a>De levens duur van het JWT-token configureren

U kunt de levens duur van het token configureren voor elke gebruikers stroom.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-Tenant bevat. Selecteer het filter **Map + Abonnement** in het bovenste menu en kies de map die uw Azure AD B2C-tenant bevat.
3. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
4. Selecteer **gebruikers stromen (beleid)**.
5. Open de gebruikers stroom die u eerder hebt gemaakt.
6. Selecteer **Eigenschappen**.
7. Pas de volgende eigenschappen aan de vereisten van uw toepassing toe, pas de **levens duur**van het token aan:

    ![Instellingen voor levens duur van tokens in de Azure Portal](./media/configure-tokens/token-lifetime.png)

8. Klik op **Opslaan**.

> [!NOTE]
> Toepassingen met één pagina die gebruikmaken van de autorisatie code stroom met PKCE, hebben altijd een vernieuwings token van 24 uur. Meer [informatie over de beveiligings implicaties van vernieuwings tokens in de browser](../active-directory/develop/reference-third-party-cookies-spas.md#security-implications-of-refresh-tokens-in-the-browser).

## <a name="configure-jwt-token-compatibility"></a>JWT-token compatibiliteit configureren

1. Selecteer **gebruikers stromen (beleid)**.
2. Open de gebruikers stroom die u eerder hebt gemaakt.
3. Selecteer **Eigenschappen**.
4. Pas de volgende eigenschappen aan de vereisten van uw toepassing aan onder **instellingen voor token compatibiliteit**:

    ![Instellingen voor token compatibiliteit van eigenschappen in de Azure Portal](./media/configure-tokens/token-compatibility.png)

5. Klik op **Opslaan**.

## <a name="provide-optional-claims-to-your-app"></a>Optionele claims voor uw app opgeven

De toepassings claims zijn waarden die worden geretourneerd naar de toepassing. Werk de stroom van de gebruiker bij zodat deze de gewenste claims bevat.

1. Selecteer **gebruikers stromen (beleid)**.
1. Open de gebruikers stroom die u eerder hebt gemaakt.
1. Selecteer **Toepassingsclaims**.
1. Kies de claims en kenmerken die u wilt terugsturen naar uw toepassing.
1. Klik op **Opslaan**.


## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [aanvragen van toegangs tokens](access-tokens.md).



