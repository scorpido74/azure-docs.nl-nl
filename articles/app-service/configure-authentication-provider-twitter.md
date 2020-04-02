---
title: Twitter-verificatie configureren
description: Meer informatie over het configureren van Twitter-verificatie als identiteitsprovider voor uw App Service- of Azure-functie-app.
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.topic: article
ms.date: 02/28/2020
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: 11c913b12b4dcb7d2a5ffa532064b347b82904ef
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519916"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-twitter-login"></a>Uw App Service- of Azure Functions-app configureren om Twitter-aanmelding te gebruiken

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

In dit artikel ziet u hoe u Azure App Service of Azure-functies configureert om Twitter als verificatieprovider te gebruiken.

Om de procedure in dit artikel te voltooien, heb je een Twitter-account nodig met een geverifieerd e-mailadres en telefoonnummer. Als u een nieuw Twitter-account wilt maken, gaat u naar [twitter.com].

## <a name="register-your-application-with-twitter"></a><a name="register"> </a>Registreer uw aanvraag bij Twitter

1. Meld u aan bij de [Azure-portal] en ga naar uw toepassing. Kopieer uw **URL**. Je gebruikt het om je Twitter-app te configureren.
1. Ga naar de website [van Twitter Developers,] meld u aan met uw Twitter-accountreferenties en selecteer **Een app maken.**
1. Voer de **naam van** de app en de **toepassingsbeschrijving** voor uw nieuwe app in. Plak de **URL** van uw toepassing in het veld Url van de **website.** Voer in de sectie **Callback-URL's** de HTTPS-URL van `/.auth/login/twitter/callback`uw App Service-app in en sluit het pad toe. Bijvoorbeeld `https://contoso.azurewebsites.net/.auth/login/twitter/callback`.
1. Typ onder aan de pagina ten minste 100 tekens in **Vertel ons hoe deze app wordt gebruikt**en selecteer Vervolgens **Maken**. Klik **nogmaals op Maken** in de pop-up. De toepassingsgegevens worden weergegeven.
1. Selecteer het tabblad **Sleutels en Toegangstokens.**

   Noteer deze waarden:
   - API-sleutel
   - API-geheime sleutel

   > [!NOTE]
   > De API-geheime sleutel is een belangrijke beveiligingsreferentie. Deel dit geheim met niemand of deel het niet met uw app.

## <a name="add-twitter-information-to-your-application"></a><a name="secrets"> </a>Twitter-informatie toevoegen aan uw toepassing

1. Ga naar uw toepassing in de [Azure-portal.]
1. Selecteer **Instellingenverificatie** > **/ Autorisatie**en controleer of verificatie van de **appservice** **is ingeschakeld**.
1. Selecteer **Twitter**.
1. Plak de `API key` `API secret key` waarden en waarden die u eerder hebt verkregen.
1. Selecteer **OK**.

   ![Schermafbeelding van de Twitter-instellingen voor mobiele apps][1]

   App Service biedt standaard verificatie, maar beperkt de geautoriseerde toegang tot uw site-inhoud en API's niet. U moet gebruikers autoriseren in uw app-code.

1. (Optioneel) Als u de toegang tot uw site wilt beperken tot alleen gebruikers die door Twitter zijn geverifieerd, stelt u **actie in die moet worden uitgevoerd wanneer het verzoek niet is geverifieerd** op **Twitter.** Wanneer u deze functionaliteit instelt, moet uw app worden geverifieerd. Het verwijst ook alle niet-geverifieerde verzoeken naar Twitter voor authenticatie.

   > [!CAUTION]
   > Het beperken van de toegang op deze manier is van toepassing op alle oproepen naar uw app, wat mogelijk niet wenselijk is voor apps met een openbaar beschikbare startpagina, zoals in veel toepassingen met één pagina. Voor dergelijke toepassingen kan **het toestaan van anonieme verzoeken (geen actie)** de voorkeur krijgen, zodat de app de verificatie zelf handmatig start. Zie [Verificatiestroom](overview-authentication-authorization.md#authentication-flow)voor meer informatie .

1. Selecteer **Opslaan**.

U bent nu klaar om Twitter te gebruiken voor verificatie in uw app.

## <a name="next-steps"></a><a name="related-content"> </a>Volgende stappen

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Twitter-ontwikkelaars]: https://go.microsoft.com/fwlink/p/?LinkId=268300
[twitter.com]: https://go.microsoft.com/fwlink/p/?LinkID=268287
[Azure Portal]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
