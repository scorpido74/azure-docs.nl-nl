---
title: Aanmelden en aanmelden met een Twitter-account
titleSuffix: Azure AD B2C
description: Geef aanmeldingen en aanmelding aan klanten met Twitter-accounts in uw toepassingen met Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 62a283efb93987d3c4a6564c9b25d2031c269559
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80051469"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>Aanmelden en aanmelden met een Twitter-account instellen met Azure Active Directory B2C

## <a name="create-an-application"></a>Een app maken

Als u Twitter wilt gebruiken als identiteitsprovider in Azure AD B2C, moet u een Twitter-toepassing maken. Als je nog geen Twitter-account hebt, kun [https://twitter.com/signup](https://twitter.com/signup)je je aanmelden bij .

1. Meld u aan bij de [website van Twitter Developers](https://developer.twitter.com/en/apps) met uw Twitter-accountreferenties.
1. Selecteer **Een app maken**.
1. Voer een **app-naam** en een **toepassingsbeschrijving in**.
1. Voer in **website-URL**. `https://your-tenant.b2clogin.com` Vervang `your-tenant` door de naam van uw huurder. Bijvoorbeeld `https://contosob2c.b2clogin.com`.
1. Voer voor de URL `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-user-flow-Id/oauth1/authresp`van **Callback**in . Vervang `your-tenant` door de naam van `your-user-flow-Id` uw tenantnaam en door de id van uw gebruikersstroom. Bijvoorbeeld `b2c_1A_signup_signin_twitter`. U moet alle kleine letters gebruiken bij het invoeren van uw tenantnaam en gebruikersstroom-id, zelfs als ze zijn gedefinieerd met hoofdletters in Azure AD B2C.
1. Lees en accepteer de voorwaarden onder aan de pagina en selecteer **Vervolgens Maken**.
1. Selecteer op de pagina **App-details** de optie **Details bewerken > bewerken,** schakel het selectievakje Aanmelden **met Twitter inschakelen in**en selecteer **Opslaan**.
1. Selecteer **sleutels en tokens** en neem de Consumer **API-sleutel** en de **geheime sleutelwaarden voor** de Consumer API op die later moeten worden gebruikt.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Twitter configureren als identiteitsprovider in uw tenant

1. Meld u als globale beheerder van de Azure AD B2C-tenant aan bij [Azure Portal](https://portal.azure.com/).
1. Zorg ervoor dat u de map met uw Azure AD B2C-tenant gebruikt door het **filter Directory + abonnement** in het bovenste menu te selecteren en de map te kiezen die uw tenant bevat.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer **Identiteitsproviders**en selecteer **Vervolgens Twitter**.
1. Voer een **naam**in . Bijvoorbeeld, *Twitter*.
1. Voer voor de **client-id**de API-sleutel van de consument in van de Twitter-toepassing die u eerder hebt gemaakt.
1. Voer **voor**het geheim van de client de geheime sleutel van de Consumer API in die u hebt opgenomen.
1. Selecteer **Opslaan**.
