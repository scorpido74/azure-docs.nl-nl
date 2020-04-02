---
title: Microsoft-verificatie configureren
description: Meer informatie over het configureren van Microsoft-accountverificatie als identiteitsprovider voor uw App Service- of Azure-functie-app.
ms.assetid: ffbc6064-edf6-474d-971c-695598fd08bf
ms.topic: article
ms.date: 08/08/2019
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: a78208ba592f86400e9b06b15d8a76923dda736f
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519885"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-microsoft-account-login"></a>Uw App Service- of Azure-functie-app configureren om aantemelding voor Microsoft-account te gebruiken

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

In dit onderwerp ziet u hoe u Azure App Service of Azure-functies configureert om AAD te gebruiken om persoonlijke aanmeldingen van Microsoft-accounten te ondersteunen.

> [!NOTE]
> Zowel persoonlijke Microsoft-accounts als organisatieaccounts maken gebruik van de AAD-identiteitsprovider. Op dit moment is het niet mogelijk om deze identiteitsprovider te configureren om beide soorten aanmeldingen te ondersteunen.

## <a name="register-your-app-with-microsoft-account"></a><a name="register-microsoft-account"> </a>Uw app registreren met Microsoft-account

1. Ga naar [**App-registraties**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) in de Azure-portal. Meld u indien nodig aan met uw Microsoft-account.
1. Selecteer **Nieuwe registratie**en voer vervolgens een toepassingsnaam in.
1. Selecteer **accounts in een organisatiemap (Azure AD-map - Multitenant) en persoonlijke Microsoft-accounts (bijvoorbeeld Skype, Xbox) onder** Ondersteunde **accounttypen.**
1. Selecteer in **URI's omleiden** `https://<app-domain-name>/.auth/login/aad/callback` **en**voer . Vervang * \<app-domeinnaam->* door de domeinnaam van uw app.  Bijvoorbeeld `https://contoso.azurewebsites.net/.auth/login/aad/callback`. Zorg ervoor dat u het HTTPS-schema in de URL gebruikt.

1. Selecteer **Registreren**.
1. Kopieer de **id van toepassing (client**). U hebt deze later nodig.
1. Selecteer certificaten & **geheimen** > in het linkerdeelvenster.**New client secret** Voer een beschrijving in, selecteer de geldigheidsduur en selecteer **Toevoegen**.
1. Kopieer de waarde die wordt weergegeven op de pagina **Certificaten & secrets.** Nadat u de pagina hebt verlaten, wordt deze niet meer weergegeven.

    > [!IMPORTANT]
    > De client geheime waarde (wachtwoord) is een belangrijke beveiligingsreferentie. Deel het wachtwoord met niemand of distribueer het niet binnen een clienttoepassing.

## <a name="add-microsoft-account-information-to-your-app-service-application"></a><a name="secrets"> </a>Microsoft-accountgegevens toevoegen aan uw App Service-toepassing

1. Ga naar uw toepassing in de [Azure-portal.]
1. Selecteer **Instellingenverificatie** > **/ Autorisatie**en controleer of verificatie van de **appservice** **is ingeschakeld**.
1. Selecteer Azure **Active Directory**onder **Verificatieproviders**. Selecteer **Geavanceerd** onder **beheermodus**. Plak in de applicatie (client) ID en client geheim dat u eerder verkregen. Gebruiken **https://login.microsoftonline.com/9188040d-6c67-4c5b-b112-36a304b66dad/v2.0** voor het **veld Url van de uitgever.**
1. Selecteer **OK**.

   App Service biedt verificatie, maar beperkt de geautoriseerde toegang tot uw site-inhoud en API's niet. U moet gebruikers autoriseren in uw app-code.

1. (Optioneel) Als u de toegang tot Microsoft-accountgebruikers wilt beperken, stelt u **Actie in die moet worden uitgevoerd wanneer het verzoek niet is geverifieerd** om zich aan te melden bij Azure Active **Directory.** Wanneer u deze functionaliteit instelt, moet uw app worden geverifieerd. Het leidt ook alle niet-geverifieerde verzoeken om AAD te gebruiken voor authenticatie. Houd er rekening mee dat omdat u de **url van** uw uitgever hebt geconfigureerd om de microsoft-accounttenant te gebruiken, alleen persoonlijke acccounts zich met succes zullen verifiëren.

   > [!CAUTION]
   > Het beperken van de toegang op deze manier is van toepassing op alle oproepen naar uw app, wat mogelijk niet wenselijk is voor apps met een openbaar beschikbare startpagina, zoals in veel toepassingen met één pagina. Voor dergelijke toepassingen kan **het toestaan van anonieme verzoeken (geen actie)** de voorkeur krijgen, zodat de app de verificatie zelf handmatig start. Zie [Verificatiestroom](overview-authentication-authorization.md#authentication-flow)voor meer informatie .

1. Selecteer **Opslaan**.

U bent nu klaar om Microsoft-account te gebruiken voor verificatie in uw app.

## <a name="next-steps"></a><a name="related-content"> </a>Volgende stappen

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure Portal]: https://portal.azure.com/
