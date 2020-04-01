---
title: Facebook-verificatie configureren
description: Meer informatie over het configureren van Facebook-verificatie als identiteitsprovider voor uw App Service- of Azure-functie-app.
ms.assetid: b6b4f062-fcb4-47b3-b75a-ec4cb51a62fd
ms.topic: article
ms.date: 06/06/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 9a2d390a5647ed90284730e9186e981b8e699d10
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80438008"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-facebook-login"></a>Uw App-service- of Azure-functie-app configureren om Facebook-aanmelding te gebruiken

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

In dit artikel ziet u hoe u Azure App Service of Azure-functies configureert om Facebook als verificatieprovider te gebruiken.

Om de procedure in dit artikel te voltooien, heb je een Facebook-account nodig met een geverifieerd e-mailadres en een mobiel telefoonnummer. Als u een nieuw Facebook-account wilt maken, gaat u naar [facebook.com].

## <a name="register-your-application-with-facebook"></a><a name="register"> </a>Registreer je sollicitatie bij Facebook

1. Ga naar de website [van Facebook Developers] en meld u aan met uw Facebook-accountreferenties.

   Als je geen Facebook-account voor ontwikkelaars hebt, selecteer je **Aan de slag** en volg je de registratiestappen.
1. Selecteer **Mijn apps** > **Nieuwe app toevoegen**.
1. In het veld **Weergavenaam:**
   1. Typ een unieke naam voor uw app.
   1. Geef uw **e-mail met contactpersonen op.**
   1. Selecteer **App-id maken**.
   1. Voltooi de veiligheidscontrole.

   Het ontwikkelaarsdashboard voor je nieuwe Facebook-app wordt geopend.
1. Selecteer **Dashboard** > **Facebook Aanmeldingsweb** > **instellen** > **Web**.
1. Selecteer **Instellingen**in de linkernavigatie onder **Facebook Login**.
1. Voer in het veld **Valid OAuth redirect URIs** invoeren. `https://<app-name>.azurewebsites.net/.auth/login/facebook/callback` Vergeet niet `<app-name>` om te vervangen door de naam van uw Azure App Service-app.
1. Selecteer **Save changes**.
1. Selecteer In het linkerdeelvenster de optie **Instellingen** > **Basis**. 
1. Selecteer in het veld **App-geheim** de optie **Weergeven**. Kopieer de waarden van **App ID** en **App Secret**. U gebruikt ze later om uw App Service-app in Azure te configureren.

   > [!IMPORTANT]
   > Het app-geheim is een belangrijke beveiligingsreferentie. Deel dit geheim met niemand of distribueer het niet binnen een clienttoepassing.
   >

1. Het Facebook-account dat u hebt gebruikt om de toepassing te registreren, is een beheerder van de app. Op dit moment kunnen alleen beheerders zich aanmelden bij deze toepassing.

   Als u andere Facebook-accounts wilt verifiëren, selecteert u **App-revisie** en schakelt ** \<u uw-app-naam> openbaar** maken om het grote publiek toegang te geven tot de app met behulp van Facebook-verificatie.

## <a name="add-facebook-information-to-your-application"></a><a name="secrets"> </a>Facebook-gegevens toevoegen aan je toepassing

1. Meld u aan bij de [Azure-portal] en navigeer naar uw App Service-app.
1. Selecteer **Instellingenverificatie** > **/ Autorisatie**en controleer of verificatie van de **appservice** **is ingeschakeld**.
1. Selecteer **Facebook**en plak vervolgens de waarden App-id en App-geheim die u eerder hebt verkregen. Schakel alle scopes in die uw toepassing nodig heeft.
1. Selecteer **OK**.

   ![Schermafbeelding van Facebook-instellingen voor mobiele apps][0]

    App Service biedt standaard verificatie, maar beperkt de geautoriseerde toegang tot uw site-inhoud en API's niet. U moet gebruikers autoriseren in uw app-code.
1. (Optioneel) Als u de toegang alleen wilt beperken tot gebruikers die door Facebook zijn geverifieerd, stelt u **Actie in die moet worden uitgevoerd wanneer het verzoek niet is geverifieerd** naar **Facebook.** Wanneer u deze functionaliteit instelt, moet uw app worden geverifieerd. Het verwijst ook alle niet-geverifieerde verzoeken naar Facebook voor authenticatie.

   > [!CAUTION]
   > Het beperken van de toegang op deze manier is van toepassing op alle oproepen naar uw app, wat mogelijk niet wenselijk is voor apps met een openbaar beschikbare startpagina, zoals in veel toepassingen met één pagina. Voor dergelijke toepassingen kan **het toestaan van anonieme verzoeken (geen actie)** de voorkeur krijgen, zodat de app de verificatie zelf handmatig start. Zie [Verificatiestroom](overview-authentication-authorization.md#authentication-flow)voor meer informatie .

1. Selecteer **Opslaan**.

Je bent nu klaar om Facebook te gebruiken voor verificatie in je app.

## <a name="next-steps"></a><a name="related-content"> </a>Volgende stappen

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Facebook-ontwikkelaars]: https://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: https://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure Portal]: https://portal.azure.com/
