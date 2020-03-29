---
title: Verificatie toevoegen op Android
description: Meer informatie over het gebruik van Azure App Service om gebruikers van uw Android-app te verifiëren bij identiteitsproviders zoals Google, Facebook, Twitter en Microsoft.
ms.assetid: 1fc8e7c1-6c3c-40f4-9967-9cf5e21fc4e1
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 705ebb5809840155e6bbf3f8eef091eb95f63e63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461637"
---
# <a name="add-authentication-to-your-android-app"></a>Verificatie toevoegen aan uw Android-app
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>Samenvatting
In deze zelfstudie voegt u verificatie toe aan het snelstartproject van de todolist op Android met behulp van een ondersteunde identiteitsprovider. Deze zelfstudie is gebaseerd op de zelfstudie [van Mobile Apps,] die u eerst moet voltooien.

## <a name="register-your-app-for-authentication-and-configure-azure-app-service"></a><a name="register"></a>Uw app registreren voor verificatie en Azure App Service configureren
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a><a name="redirecturl"></a>Uw app toevoegen aan de toegestane URL's voor omleiding

Veilige verificatie vereist dat u een nieuw URL-schema voor uw app definieert. Hierdoor kan het verificatiesysteem worden omgeleid naar uw app zodra het verificatieproces is voltooid. In deze zelfstudie gebruiken we de _URL-appname_ overal. U echter elk URL-schema gebruiken dat u kiest. Het moet uniek zijn voor uw mobiele applicatie. Ga als u de omleiding aan de serverzijde inschakelen:

1. Selecteer uw App-service in de [Azure-portal.]

2. Klik op de optie **Verificatie / Autorisatie.**

3. Voer in de toegestane externe `appname://easyauth.callback` **omleidings-URL's**.  De _appnaam_ in deze tekenreeks is het URL-schema voor uw mobiele toepassing.  Het moet de normale URL-specificatie voor een protocol volgen (gebruik alleen letters en cijfers en begin met een letter).  U moet een notitie maken van de tekenreeks die u kiest, omdat u uw mobiele toepassingscode op verschillende plaatsen moet aanpassen met het URL-schema.

4. Klik op **OK**.

5. Klik op **Opslaan**.

## <a name="restrict-permissions-to-authenticated-users"></a><a name="permissions"></a>Machtigingen beperken tot geverifieerde gebruikers
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* Open in Android Studio het project dat u hebt voltooid met de zelfstudie Aan de [slag met mobiele apps.] Klik **in** het menu Uitvoeren op **App uitvoeren**en controleer of een niet-afgehandelde uitzondering met een statuscode van 401 (Ongeautoriseerd) wordt verhoogd nadat de app is gestart.

     Deze uitzondering vindt plaats omdat de app probeert toegang te krijgen tot de back-end als een niet-geverifieerde gebruiker, maar de *TodoItem-tabel* vereist nu verificatie.

Vervolgens werkt u de app bij om gebruikers te verifiëren voordat u resources opvraagt via de back-end van Mobiele apps.

## <a name="add-authentication-to-the-app"></a>Verificatie toevoegen aan de app
[!INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]



## <a name="cache-authentication-tokens-on-the-client"></a><a name="cache-tokens"></a>Cacheverificatietokens op de client
[!INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Volgende stappen
Nu u deze basisverificatiezelfstudie hebt voltooid, u overwegen verder te gaan naar een van de volgende zelfstudies:

* [Voeg pushmeldingen toe aan je Android-app](app-service-mobile-android-get-started-push.md).
  Meer informatie over het configureren van de back-end van uw mobiele apps om Azure-meldingshubs te gebruiken om pushmeldingen te verzenden.
* [Offline synchronisatie inschakelen voor uw Android-app](app-service-mobile-android-get-started-offline-data.md).
  Meer informatie over het toevoegen van offline ondersteuning aan uw app met behulp van een back-end van Mobiele apps. Met offline synchronisatie kunnen gebruikers communiceren&mdash;met een mobiele app&mdash;die gegevens bekijkt, toevoegt of wijzigt, zelfs als er geen netwerkverbinding is.

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #cache-tokens
[Refresh expired tokens]: #refresh-tokens
[Next Steps]:#next-steps


<!-- URLs. -->
[Aan de slag met mobiele apps]: app-service-mobile-android-get-started.md
[Azure-portal]: https://portal.azure.com/
