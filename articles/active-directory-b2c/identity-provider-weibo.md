---
title: Aanmelden en aanmelden met een Weibo-account
titleSuffix: Azure AD B2C
description: Geef aanmeldingen en aanmelding aan klanten met Weibo-accounts in uw toepassingen met Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e99b5717cdcc32d8fd138d1edf5fe1bd3283c70e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187896"
---
# <a name="set-up-sign-up-and-sign-in-with-a-weibo-account-using-azure-active-directory-b2c"></a>Aanmelden en aanmelden met een Weibo-account instellen met Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-weibo-application"></a>Een Weibo-toepassing maken

Als u een Weibo-account wilt gebruiken als identiteitsprovider in Azure Active Directory B2C (Azure AD B2C), moet u een toepassing maken in uw tenant die deze vertegenwoordigt. Als je nog geen Weibo-account hebt, kun [https://weibo.com/signup/signup.php?lang=en-us](https://weibo.com/signup/signup.php?lang=en-us)je je aanmelden op .

1. Meld u aan bij de [Weibo-ontwikkelaarsportal](https://open.weibo.com/) met uw Weibo-accountreferenties.
1. Selecteer na het aanmelden de weergavenaam in de rechterbovenhoek.
1. Selecteer in de vervolgkeuzelijst de optie informatie van ontwikkelaars.in the dropdown, select ??????????????????????????????????????????????????????????????????????????????????????????????????????????????????????? **编辑开发者信息**
1. Voer de vereiste gegevens in en selecteer **de informatie** (verzenden).
1. Voltooi het verificatieproces voor e-mail.
1. Ga naar de [pagina identiteitsverificatie](https://open.weibo.com/developers/identity/edit).
1. Voer de vereiste gegevens in en selecteer **de informatie** (verzenden).

### <a name="register-a-weibo-application"></a>Een Weibo-toepassing registreren

1. Ga naar de [nieuwe weibo-appregistratiepagina](https://open.weibo.com/apps/new).
1. Voer de benodigde toepassingsgegevens in.
1. Selecteer **??(maken).**
1. Kopieer de waarden **van App-sleutel** en **App-geheim**. U hebt beide nodig om de identiteitsprovider aan uw tenant toe te voegen.
1. Upload de benodigde foto's en voer de benodigde informatie in.
1. Selecteer **??????????(opslaan).**
1. Selecteer **de optie (geavanceerde** informatie).
1. Selecteer **(bewerken)** naast het veld voor OAuth2.0 **授权设置** (omleidings-URL).
1. Voer `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` voor OAuth2.0 **(redirect** URL). Als uw tenantnaam bijvoorbeeld contoso is, stelt `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`u de URL in als .
1. Selecteer **de selectie** (verzenden).

## <a name="configure-a-weibo-account-as-an-identity-provider"></a>Een Weibo-account configureren als identiteitsprovider

1. Meld u als globale beheerder van de Azure AD B2C-tenant aan bij [Azure Portal](https://portal.azure.com/).
1. Zorg ervoor dat u de map met uw Azure AD B2C-tenant gebruikt door het **filter Directory + abonnement** in het bovenste menu te selecteren en de map te kiezen die uw tenant bevat.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer **Identiteitsproviders**en selecteer **Vervolgens Weibo (Voorbeeld)**.
1. Voer een **naam**in . Bijvoorbeeld *Weibo*.
1. Voer voor de **client-id**de app-sleutel in van de Weibo-toepassing die u eerder hebt gemaakt.
1. Voer **voor**het geheim van de client het app-geheim in dat u hebt opgenomen.
1. Selecteer **Opslaan**.
