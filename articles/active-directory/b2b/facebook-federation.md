---
title: Facebook toevoegen als een id-provider-Azure AD
description: Met Facebook communiceren om externe gebruikers (gasten) in te scha kelen voor aanmelding bij uw Azure AD-apps met hun eigen Facebook-accounts.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: a92fbd254f223e2c7eb70a4e86bb7e904294395e
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597707"
---
# <a name="add-facebook-as-an-identity-provider-for-external-identities"></a>Facebook toevoegen als een id-provider voor externe identiteiten

U kunt Facebook toevoegen aan uw Self-service voor het registreren van gebruikers (preview), zodat gebruikers zich met hun eigen Facebook-accounts kunnen aanmelden bij uw toepassingen. Als u gebruikers wilt toestaan zich aan te melden met Facebook, moet u eerst [selfservice registratie inschakelen](self-service-sign-up-user-flow.md) voor uw Tenant. Nadat u Facebook als een id-provider hebt toegevoegd, stelt u een gebruikers stroom voor de toepassing in en selecteert u Facebook als een van de aanmeldings opties.

## <a name="create-an-app-in-the-facebook-developers-console"></a>Een app maken in de Facebook-ontwikkelaars console

Als u een Facebook-account wilt gebruiken als een [ID-provider](identity-providers.md), moet u een toepassing maken in de Facebook Developers-console. Als u nog geen Facebook-account hebt, kunt u zich aanmelden bij [https://www.facebook.com/](https://www.facebook.com) .

> [!NOTE]  
> Gebruik de volgende Url's in de stappen 9 en 16 hieronder.
> - Voer de URL van de **site** in `https://login.microsoftonline.com` .
> - Voer in voor **geldige OAuth omleidings-uri's** `https://login.microsoftonline.com/te/<tenant-id>/oauth2/authresp` . U vindt de `<tenant-ID>` Blade overzicht van Azure Active Directory.


1. Meld u aan bij [Facebook voor ontwikkel aars](https://developers.facebook.com/) met de referenties van uw Facebook-account.
2. Als u dit nog niet hebt gedaan, moet u zich registreren als Facebook-ontwikkelaar. U doet dit door aan de rechter bovenhoek van de pagina aan de **slag te gaan** , het beleid van Facebook te accepteren en de registratie stappen te volt ooien.
3. Selecteer **mijn apps** en maak vervolgens een **app**.
4. Voer een **weergave naam** in en geef een geldig **e-mail adres voor de contact persoon op**.
5. Selecteer **App-ID maken**. Hiervoor moet u mogelijk het Facebook-platform beleid accepteren en een online beveiligings controle volt ooien.
6. Selecteer **instellingen**  >  **basis**.
7. Kies een **categorie**, bijvoorbeeld Business en Pages. Deze waarde is vereist voor Facebook, maar wordt niet gebruikt voor Azure AD.
8. Selecteer onder aan de pagina **platform toevoegen**en selecteer vervolgens **website**.
9. Voer in **site-URL**de juiste URL in (hierboven aangegeven).
10. Voer in het **URL-privacybeleid**de URL in voor de pagina waar u privacy-informatie voor uw toepassing onderhoudt, bijvoorbeeld `http://www.contoso.com` .
11. Selecteer **Save changes**.
12. Kopieer de waarde van de **App-ID**aan de bovenkant van de pagina.
13. Selecteer **weer geven** en kopieer de waarde van het **app-geheim**. U kunt beide gebruiken om Facebook te configureren als een id-provider in uw Tenant. **App-geheim** is een belang rijke beveiligings referentie.
14. Selecteer het plus teken naast **producten**en selecteer vervolgens **instellen** onder **Facebook-aanmelding**.
15. Selecteer onder **Facebook-aanmelding**de optie **instellingen**.
16. In **geldige OAuth omleidings-uri's**voert u de juiste URL in (hierboven aangegeven).
17. Selecteer **wijzigingen opslaan** onder aan de pagina.
18. Als u uw Facebook-toepassing beschikbaar wilt maken voor Azure AD, selecteert u in de rechter bovenhoek van de pagina de status kiezer en schakelt u deze **in** om de toepassing openbaar te maken en vervolgens **Switch modus**te selecteren. Op dit moment moet de status worden gewijzigd van **ontwikkeling** naar **Live**.
    
## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Een Facebook-account configureren als een id-provider

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als globale beheerder van uw Azure AD-Tenant.
2. Onder **Azure-Services**selecteert u **Azure Active Directory**.
3. Selecteer in het linkermenu **externe identiteiten**.
4. Selecteer **alle id-providers**en selecteer vervolgens **Facebook**.
5. Voer voor de **client-id**de **App-ID** in van de Facebook-toepassing die u eerder hebt gemaakt.
6. Voer voor het **client geheim**het **app-geheim** in dat u hebt vastgelegd.

   ![Scherm afbeelding van de pagina Social ID-provider toevoegen](media/facebook-federation/add-social-identity-provider-page.png)

7. Selecteer **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

- [Externe gebruikers uitnodigen voor samen werking](add-users-administrator.md)
- [Een self-service aanmelding toevoegen aan een app](self-service-sign-up-user-flow.md)
