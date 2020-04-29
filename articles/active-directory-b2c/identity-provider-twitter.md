---
title: Registratie instellen en aanmelden met een Twitter-account
titleSuffix: Azure AD B2C
description: Bied u de mogelijkheid om u aan te melden en u aan te melden bij klanten met Twitter-accounts in uw toepassingen met Azure Active Directory B2C.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80051469"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>Registratie instellen en aanmelden met een Twitter-account met behulp van Azure Active Directory B2C

## <a name="create-an-application"></a>Een app maken

Als u Twitter als een id-provider in Azure AD B2C wilt gebruiken, moet u een Twitter-toepassing maken. Als u nog geen Twitter-account hebt, kunt u zich aanmelden bij [https://twitter.com/signup](https://twitter.com/signup).

1. Meld u aan bij de website van de [Twitter-ontwikkel aars](https://developer.twitter.com/en/apps) met de referenties van uw Twitter-account.
1. Selecteer **een app maken**.
1. Voer een **app-naam** en een **toepassings beschrijving**in.
1. Voer **Website URL** `https://your-tenant.b2clogin.com`in website-URL in. Vervang `your-tenant` door de naam van uw Tenant. Bijvoorbeeld `https://contosob2c.b2clogin.com`.
1. Voer `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-user-flow-Id/oauth1/authresp`in voor de **call back-URL**. Vervang `your-tenant` door de naam van uw Tenant naam en `your-user-flow-Id` met de id van uw gebruikers stroom. Bijvoorbeeld `b2c_1A_signup_signin_twitter`. U moet alle kleine letters gebruiken wanneer u uw Tenant naam en gebruikers stroom-ID invoert, zelfs als ze zijn gedefinieerd met hoofd letters in Azure AD B2C.
1. Lees en accepteer de voor waarden aan de onderkant van de pagina en selecteer vervolgens **maken**.
1. Op de **pagina app-Details** selecteert u **bewerken > Details bewerken**, schakelt u het selectie vakje **Aanmelden met Twitter inschakelen in**en selecteert u vervolgens **Opslaan**.
1. Selecteer **sleutels en tokens** en noteer de **CONSUMer-API-sleutel** en de geheime sleutel waarden voor de **Consumer-API** die u later wilt gebruiken.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Twitter configureren als een id-provider in uw Tenant

1. Meld u als globale beheerder van de Azure AD B2C-tenant aan bij [Azure Portal](https://portal.azure.com/).
1. Zorg ervoor dat u de map met uw Azure AD B2C-Tenant gebruikt door het filter **Directory + abonnement** te selecteren in het bovenste menu en de map te kiezen die uw Tenant bevat.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer **id-providers**en selecteer vervolgens **Twitter**.
1. Voer een **naam**in. Bijvoorbeeld *Twitter*.
1. Voer voor de **client-id**de Consumer-API-sleutel in van de Twitter-toepassing die u eerder hebt gemaakt.
1. Voer voor het **client geheim**de geheime sleutel van de consument-API in die u hebt geregistreerd.
1. Selecteer **Opslaan**.
