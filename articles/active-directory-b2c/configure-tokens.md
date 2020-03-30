---
title: Tokens configureren - Azure Active Directory B2C | Microsoft Documenten
description: Meer informatie over het configureren van de tokenlevensduur en compatibiliteitsinstellingen in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 160898f8535d3dad4811af016ebca779b7ef43a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189614"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Tokens configureren in Azure Active Directory B2C

In dit artikel leert u hoe u de [levensduur en compatibiliteit van een token](tokens-overview.md) configureert in Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Vereisten

[Maak een gebruikersstroom](tutorial-create-user-flows.md) zodat gebruikers zich kunnen aanmelden en zich kunnen aanmelden bij uw toepassing.

## <a name="configure-token-lifetime"></a>Tokenlevensduur configureren

U de levensduur van het token configureren op elke gebruikersstroom.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-tenant bevat. Selecteer het **filter Directory + abonnement** in het bovenste menu en kies de map met uw Azure AD B2C-tenant.
3. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
4. Selecteer **Gebruikersstromen (beleidsregels)**.
5. Open de gebruikersstroom die u eerder hebt gemaakt.
6. Selecteer **Eigenschappen**.
7. Pas **onder Token levensduur**de volgende eigenschappen aan de behoeften van uw toepassing aan:

    ![Eigenschappeninstellingen voor een looptijd van token in de Azure-portal](./media/configure-tokens/token-lifetime.png)

8. Klik op **Opslaan**.

## <a name="configure-token-compatibility"></a>Tokencompatibiliteit configureren

1. Selecteer **Gebruikersstromen (beleidsregels)**.
2. Open de gebruikersstroom die u eerder hebt gemaakt.
3. Selecteer **Eigenschappen**.
4. Pas onder **Token-compatibiliteitsinstellingen**de volgende eigenschappen aan die aan de behoeften van uw toepassing voldoen:

    ![Eigenschappeninstellingen voor tokencompatibiliteit in de Azure-portal](./media/configure-tokens/token-compatibility.png)

5. Klik op **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [gebruik van toegangstokens](access-tokens.md).



