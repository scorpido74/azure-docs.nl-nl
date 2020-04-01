---
title: Google-verificatie configureren
description: Meer informatie over het configureren van Google-verificatie als identiteitsprovider voor uw App Service- of Azure-functie-app.
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.topic: article
ms.date: 09/02/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: bb6b8eebef3247cf2c39ed4b111296e1e0521a74
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437982"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-google-login"></a>Uw App Service- of Azure Functions-app configureren om Google-aanmelding te gebruiken

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

In dit onderwerp ziet u hoe u Azure App Service of Azure-functies configureert om Google als verificatieprovider te gebruiken.

Als u de procedure in dit onderwerp wilt voltooien, moet u een Google-account hebben met een geverifieerd e-mailadres. Als u een nieuw Google-account wilt maken, gaat u naar [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302).

## <a name="register-your-application-with-google"></a><a name="register"> </a>Uw aanvraag registreren bij Google

1. Volg de [Google-documentatie bij Google Sign-In voor apps aan de serverzijde](https://developers.google.com/identity/sign-in/web/server-side-flow) om een client-id en clientgeheim te maken. Het is niet nodig om codewijzigingen aan te brengen. Gebruik gewoon de volgende informatie:
    - Gebruik voor `https://<app-name>.azurewebsites.net` **geautoriseerde JavaScript Origins**de naam van uw app in * \<app-naam>*.
    - Voor **Authorized Redirect URI,** gebruik `https://<app-name>.azurewebsites.net/.auth/login/google/callback`.
1. Kopieer de app-id en de geheime waarden van app.

    > [!IMPORTANT]
    > Het app-geheim is een belangrijke beveiligingsreferentie. Deel dit geheim met niemand of distribueer het niet binnen een clienttoepassing.

## <a name="add-google-information-to-your-application"></a><a name="secrets"> </a>Google-gegevens toevoegen aan uw toepassing

1. Ga in de [Azure-portal]naar uw App Service-app.
1. Selecteer **Instellingenverificatie** > **/ Autorisatie**en controleer of verificatie van de **appservice** **is ingeschakeld**.
1. Selecteer **Google**en plak vervolgens de waarden App-id en App-geheim die u eerder hebt verkregen. Schakel alle scopes in die uw toepassing nodig heeft.
1. Selecteer **OK**.

   App Service biedt verificatie, maar beperkt de geautoriseerde toegang tot uw site-inhoud en API's niet. Zie [Gebruikers autoriseren of weigeren](app-service-authentication-how-to.md#authorize-or-deny-users)voor meer informatie.

1. (Optioneel) Als u de toegang tot de site alleen wilt beperken tot gebruikers die door Google zijn geverifieerd, stelt u **Actie in die moet worden uitgevoerd wanneer het verzoek niet is geverifieerd** aan **Google**. Wanneer u deze functionaliteit instelt, moet uw app worden geverifieerd. Het verwijst ook alle niet-geverifieerde verzoeken naar Google voor authenticatie.

    > [!CAUTION]
    > Het beperken van de toegang op deze manier is van toepassing op alle oproepen naar uw app, wat mogelijk niet wenselijk is voor apps met een openbaar beschikbare startpagina, zoals in veel toepassingen met één pagina. Voor dergelijke toepassingen kan **het toestaan van anonieme verzoeken (geen actie)** de voorkeur krijgen, zodat de app de verificatie zelf handmatig start. Zie [Verificatiestroom](overview-authentication-authorization.md#authentication-flow)voor meer informatie .

1. Selecteer **Opslaan**.

U bent nu klaar om Google te gebruiken voor verificatie in uw app.

## <a name="next-steps"></a><a name="related-content"> </a>Volgende stappen

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: https://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure Portal]: https://portal.azure.com/

