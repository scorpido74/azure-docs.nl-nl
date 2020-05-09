---
title: Tokens configureren-Azure Active Directory B2C | Microsoft Docs
description: Meer informatie over het configureren van de token levensduur en compatibiliteits instellingen in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a2b9cc0c69f0c559c9561a02de4192974ee7f521
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/08/2020
ms.locfileid: "82926184"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Tokens configureren in Azure Active Directory B2C

In dit artikel leert u hoe u de [levens duur en compatibiliteit van een token](tokens-overview.md) in Azure Active Directory B2C (Azure AD B2C) kunt configureren.

## <a name="prerequisites"></a>Vereisten

[Maak een gebruikers stroom](tutorial-create-user-flows.md) zodat gebruikers zich kunnen registreren en zich kunnen aanmelden bij uw toepassing.

## <a name="configure-jwt-token-lifetime"></a>De levens duur van het JWT-token configureren

U kunt de levens duur van het token configureren voor elke gebruikers stroom.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
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

Meer informatie over het [gebruik van toegangs tokens](access-tokens.md).



