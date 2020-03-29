---
title: Aanmelden en aanmelden met een QQ-account instellen met Azure Active Directory B2C
description: Geef aanmeldingen en aanmelding aan klanten met QQ-accounts in uw toepassingen met Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b0f15124c64e5cca54112987d486ddadaca79452
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187984"
---
# <a name="set-up-sign-up-and-sign-in-with-a-qq-account-using-azure-active-directory-b2c"></a>Aanmelden en aanmelden met een QQ-account instellen met Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-qq-application"></a>Een QQ-toepassing maken

Als u een QQ-account wilt gebruiken als identiteitsprovider in Azure Active Directory B2C (Azure AD B2C), moet u een toepassing maken in uw tenant die deze vertegenwoordigt. Als je nog geen QQ-account hebt, kun [https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033)je je aanmelden bij .

### <a name="register-for-the-qq-developer-program"></a>Registreren voor het QQ-ontwikkelaarsprogramma

1. Meld u aan bij de [QQ-ontwikkelaarsportal](http://open.qq.com) met uw QQ-accountreferenties.
1. Ga na het [https://open.qq.com/reg](https://open.qq.com/reg) aanmelden naar om jezelf te registreren als ontwikkelaar.
1. Selecteer **??(individuele** ontwikkelaar).
1. Voer de vereiste informatie in en selecteer **de** volgende stap.
1. Voltooi het verificatieproces voor e-mail. U moet een paar dagen wachten om te worden goedgekeurd nadat u zich als ontwikkelaar hebt geregistreerd.

### <a name="register-a-qq-application"></a>Een QQ-toepassing registreren

1. Ga [https://connect.qq.com/index.html](https://connect.qq.com/index.html)naar.
1. Selecteer **het beheer van de** app.
1. Selecteer de optie **(app maken)** en voer de vereiste gegevens in.
1. Voer `https://your-tenant-name.b2clogin.com/your-tenant-name}.onmicrosoft.com/oauth2/authresp` de URL (callback) **in.** Als uw `tenant_name` contoso bijvoorbeeld is, stelt `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`u de URL in als .
1. Selecteer de app (app **maken).**
1. Selecteer op de bevestigingspagina het beheer **van de** app om terug te keren naar de pagina appbeheer.
1. Selecteer **de optie** (weergave) naast de app die u hebt gemaakt.
1. Selecteer **??**
1. Kopieer de **app-id** en **app-sleutel**. U hebt beide waarden nodig om de identiteitsprovider aan uw tenant toe te voegen.

## <a name="configure-qq-as-an-identity-provider"></a>QQ configureren als identiteitsprovider

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
1. Selecteer het pictogram **Directory + Abonnement** op de werkbalk van de portal en selecteer vervolgens de map met uw Azure AD B2C-tenant.
1. Zoek en selecteer **Azure AD B2C**in de Azure-portal .
1. Selecteer **Identiteitsproviders**en selecteer **QQ (Voorbeeld)**.
1. Voer een **naam**in . Bijvoorbeeld *QQ*.
1. Voer voor de **client-id**de APP-id in van de QQ-toepassing die u eerder hebt gemaakt.
1. Voer **voor**het geheim van de client de APP-sleutel in die u hebt opgenomen.
1. Selecteer **Opslaan**.
