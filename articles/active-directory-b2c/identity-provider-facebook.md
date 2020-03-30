---
title: Aanmelden en aanmelden met een Facebook-account
titleSuffix: Azure AD B2C
description: Geef aanmelden en aanmelden aan klanten met Facebook-accounts in uw toepassingen met Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cd0e19de88a6a65d72a2e7e19f7fca2a94d8da55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188270"
---
# <a name="set-up-sign-up-and-sign-in-with-a-facebook-account-using-azure-active-directory-b2c"></a>Aanmelden en aanmelden met een Facebook-account instellen met Azure Active Directory B2C

## <a name="create-a-facebook-application"></a>Een Facebook-toepassing maken

Als u een Facebook-account wilt gebruiken als [identiteitsprovider](authorization-code-flow.md) in Azure Active Directory B2C (Azure AD B2C), moet u een toepassing maken in uw tenant die deze vertegenwoordigt. Als je nog geen Facebook-account hebt, kun [https://www.facebook.com/](https://www.facebook.com/)je je aanmelden bij .

1. Meld je aan bij [Facebook voor ontwikkelaars](https://developers.facebook.com/) met je Facebook-accountreferenties.
1. Als je dit nog niet hebt gedaan, moet je je registreren als Facebook-ontwikkelaar. Selecteer hiervoor **aan** de slag in de rechterbovenhoek van de pagina, accepteer het beleid van Facebook en voltooi t.a.v. De registratiestappen.
1. Selecteer **Mijn apps** en maak vervolgens **App**.
1. Voer een **weergavenaam** en een geldige **e-mail met contactpersonen in**.
1. Selecteer **App-id maken**. Dit kan vereisen dat je het beleid van het Facebook-platform accepteert en een online beveiligingscontrole voltooit.
1. Selecteer **Basisinstellingen** > **Basic**.
1. Kies bijvoorbeeld `Business and Pages`een **rubriek.** Deze waarde is vereist door Facebook, maar wordt niet gebruikt voor Azure AD B2C.
1. Selecteer onder aan de pagina **Platform toevoegen**en selecteer **Vervolgens Website**.
1. Voer in **site-URL**de `https://your-tenant-name.b2clogin.com/` vervanging `your-tenant-name` in met de naam van uw tenant. Voer bijvoorbeeld `http://www.contoso.com`een URL in voor de **URL van het privacybeleid**. De beleids-URL is een pagina die u bijhoudt om privacygegevens voor uw toepassing te verstrekken.
1. Selecteer **Save changes**.
1. Kopieer boven aan de pagina de waarde van **app-id**.
1. Selecteer De waarde van **App-geheim** **weergeven** en kopiëren . Je gebruikt ze allebei om Facebook te configureren als identiteitsprovider in je tenant. **App Secret** is een belangrijke beveiligingsreferentie.
1. Selecteer het plusteken naast **PRODUCTEN**en selecteer **Instellen** onder **Facebook-aanmelding**.
1. Selecteer **Instellingen**onder **Facebook-aanmelding**.
1. Voer in **Valid OAuth redirect URI's**in `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Vervang `your-tenant-name` door de naam van uw huurder. Selecteer Wijzigingen onder aan de pagina **opslaan.**
1. Als u uw Facebook-toepassing beschikbaar wilt maken voor Azure AD B2C, selecteert u de statuskiezer rechtsboven op de pagina en schakelt u deze **in** om de toepassing openbaar te maken en selecteert u **Modus schakelen**.  Op dit punt moet de status veranderen van **ontwikkeling** naar **Live**.

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Een Facebook-account configureren als identiteitsprovider

1. Meld u als globale beheerder van de Azure AD B2C-tenant aan bij [Azure Portal](https://portal.azure.com/).
1. Zorg ervoor dat u de map met uw Azure AD B2C-tenant gebruikt door het **filter Directory + abonnement** in het bovenste menu te selecteren en de map te kiezen die uw tenant bevat.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer **identiteitsproviders**en selecteer **Vervolgens Facebook**.
1. Voer een **naam**in . Bijvoorbeeld, *Facebook*.
1. Voer voor de **client-id**de app-id in van de Facebook-toepassing die u eerder hebt gemaakt.
1. Voer **voor**het geheim van de client het app-geheim in dat u hebt opgenomen.
1. Selecteer **Opslaan**.
