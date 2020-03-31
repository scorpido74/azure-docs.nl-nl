---
title: Aanmelden en aanmelden met een Google-account instellen
titleSuffix: Azure AD B2C
description: Geef aanmeldingen en aanmelding aan klanten met Google-accounts in uw toepassingen met Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 48955caddb64069f897078f5e47066d9f11d119b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188137"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>Aanmelden en aanmelden met een Google-account instellen met Azure Active Directory B2C

## <a name="create-a-google-application"></a>Een Google-toepassing maken

Als u een Google-account wilt gebruiken als [identiteitsprovider](authorization-code-flow.md) in Azure Active Directory B2C (Azure AD B2C), moet u een toepassing maken in uw Google Developers Console. Als je nog geen Google-account hebt, [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp)kun je je aanmelden bij .

1. Meld u aan bij de [Google Developers Console](https://console.developers.google.com/) met uw Google-accountreferenties.
1. Selecteer in de linkerbovenhoek van de pagina de projectlijst en selecteer **Vervolgens Nieuw project**.
1. Voer een **projectnaam**in, selecteer **Maken**.
1. Zorg ervoor dat u het nieuwe project gebruikt door de vervolgkeuzelijst van het project linksboven in het scherm te selecteren, uw project op naam te selecteren en vervolgens **Openen**te selecteren .
1. Selecteer **het scherm OAuth-toestemming** in het linkermenu, selecteer **Extern**en selecteer **Vervolgens Maken**.
Voer een **naam** in voor uw toepassing. Voer *b2clogin.com* in de sectie **Geautoriseerde domeinen** in en selecteer **Opslaan**.
1. Selecteer **Referenties** in het linkermenu en selecteer **vervolgens Referenties** > **Oauth-client-id maken**.
1. Selecteer **Webtoepassing** **onder Toepassingstype**.
1. Voer een **naam** in `https://your-tenant-name.b2clogin.com` voor uw toepassing, voer `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` geautoriseerde **JavaScript-oorsprong in**en voer in geautoriseerde **omleidings-URI's in.** Vervang `your-tenant-name` door de naam van uw huurder. U moet alle kleine letters gebruiken bij het invoeren van uw tenantnaam, zelfs als de tenant is gedefinieerd met hoofdletters in Azure AD B2C.
1. Klik **op Maken**.
1. Kopieer de waarden van **client-id** en **clientgeheim**. U hebt beide nodig om Google te configureren als identiteitsprovider in uw tenant. **Clientgeheim** is een belangrijke beveiligingsreferentie.

## <a name="configure-a-google-account-as-an-identity-provider"></a>Een Google-account configureren als identiteitsprovider

1. Meld u als globale beheerder van de Azure AD B2C-tenant aan bij [Azure Portal](https://portal.azure.com/).
1. Zorg ervoor dat u de map met uw Azure AD B2C-tenant gebruikt door het **filter Directory + abonnement** in het bovenste menu te selecteren en de map te kiezen die uw tenant bevat.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer **Identiteitsproviders**en selecteer **Vervolgens Google**.
1. Voer een **naam**in . Bijvoorbeeld, *Google*.
1. Voer voor de **client-id**de client-id in van de Google-toepassing die u eerder hebt gemaakt.
1. Voer **voor**het geheim van de client het clientgeheim in dat u hebt opgenomen.
1. Selecteer **Opslaan**.
