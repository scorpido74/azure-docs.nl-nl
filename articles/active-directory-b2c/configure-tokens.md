---
title: Tokens configureren-Azure Active Directory B2C | Microsoft Docs
description: Meer informatie over het configureren van de token levensduur en compatibiliteits instellingen in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 49a5ff61e5f7a17005561e0729a9b0fcb0f954d4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85389561"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Tokens configureren in Azure Active Directory B2C

In dit artikel leert u hoe u de [levens duur en compatibiliteit van een token](tokens-overview.md) in Azure Active Directory B2C (Azure AD B2C) kunt configureren.

## <a name="prerequisites"></a>Vereisten

[Maak een gebruikers stroom](tutorial-create-user-flows.md) zodat gebruikers zich kunnen registreren en zich kunnen aanmelden bij uw toepassing.

## <a name="configure-jwt-token-lifetime"></a>De levens duur van het JWT-token configureren

U kunt de levens duur van het token configureren voor elke gebruikers stroom.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-Tenant bevat. Selecteer het filter **Directory + abonnement** in het bovenste menu en kies de map die uw Azure AD B2C Tenant bevat.
3. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
4. Selecteer **gebruikers stromen (beleid)**.
5. Open de gebruikers stroom die u eerder hebt gemaakt.
6. Selecteer **Eigenschappen**.
7. Pas de volgende eigenschappen aan de vereisten van uw toepassing toe, pas de **levens duur**van het token aan:

    ![Instellingen voor levens duur van tokens in de Azure Portal](./media/configure-tokens/token-lifetime.png)

8. Klik op **Opslaan**.

## <a name="configure-jwt-token-compatibility"></a>JWT-token compatibiliteit configureren

1. Selecteer **gebruikers stromen (beleid)**.
2. Open de gebruikers stroom die u eerder hebt gemaakt.
3. Selecteer **Eigenschappen**.
4. Pas de volgende eigenschappen aan de vereisten van uw toepassing aan onder **instellingen voor token compatibiliteit**:

    ![Instellingen voor token compatibiliteit van eigenschappen in de Azure Portal](./media/configure-tokens/token-compatibility.png)

5. Klik op **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [aanvragen van toegangs tokens](access-tokens.md).



