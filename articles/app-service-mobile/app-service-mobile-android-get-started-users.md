---
title: Verificatie toevoegen op Android
description: Meer informatie over het gebruik van Azure App Service voor het verifiëren van gebruikers van uw Android-app met id-providers zoals Google, Facebook, Twitter en micro soft.
ms.assetid: 1fc8e7c1-6c3c-40f4-9967-9cf5e21fc4e1
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 705ebb5809840155e6bbf3f8eef091eb95f63e63
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461637"
---
# <a name="add-authentication-to-your-android-app"></a>Verificatie toevoegen aan uw Android-app
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>Samenvatting
In deze zelf studie voegt u verificatie toe aan het ToDoList Quick start-project op Android met behulp van een ondersteunde ID-provider. Deze zelf studie is gebaseerd op de zelf studie [aan de slag met Mobile apps] , die u eerst moet volt ooien.

## <a name="register"></a>Uw app registreren voor verificatie en Azure App Service configureren
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Uw app toevoegen aan de toegestane externe omleidings-Url's

Voor beveiligde verificatie moet u een nieuw URL-schema definiëren voor uw app. Hierdoor kan het verificatie systeem terugkeren naar uw app nadat het verificatie proces is voltooid. In deze zelf studie gebruiken we het URL-schema _AppName_ in. U kunt echter elk gewenst URL-schema gebruiken. Deze moet uniek zijn voor uw mobiele toepassing. De omleiding aan de server zijde inschakelen:

1. Selecteer uw App Service in de [Azure Portal].

2. Klik op de menu optie voor **verificatie/autorisatie** .

3. In de **toegestane externe omleidings-url's**voert u `appname://easyauth.callback`in.  De _AppName_ in deze teken reeks is het URL-schema voor uw mobiele toepassing.  De standaard URL-specificatie voor een protocol moet volgen (alleen letters en cijfers gebruiken en beginnen met een letter).  U moet een notitie maken van de teken reeks die u kiest, omdat u de code van uw mobiele toepassing moet aanpassen aan het URL-schema op verschillende locaties.

4. Klik op **OK**.

5. Klik op **Opslaan**.

## <a name="permissions"></a>Machtigingen voor geverifieerde gebruikers beperken
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* Open in Android Studio het project dat u hebt voltooid met de zelf studie aan de [Aan de slag met Mobile Apps]. Klik in het menu **uitvoeren** op **app uitvoeren**en controleer of er een niet-verwerkte uitzonde ring met de status code 401 (niet toegestaan) wordt gegenereerd nadat de app is gestart.

     Deze uitzonde ring treedt op omdat de app probeert toegang te krijgen tot de back-end als een niet-geverifieerde gebruiker, maar voor de tabel *TodoItem* is nu verificatie vereist.

Vervolgens werkt u de app bij om gebruikers te verifiëren voordat ze een aanvraag indienen bij de Mobile Apps back-end.

## <a name="add-authentication-to-the-app"></a>Verificatie toevoegen aan de app
[!INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]



## <a name="cache-tokens"></a>Verificatie-tokens in de cache op de client
[!INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Volgende stappen
Nu u deze basis verificatie-zelf studie hebt voltooid, kunt u door gaan met een van de volgende zelf studies:

* [Push meldingen toevoegen aan uw Android-app](app-service-mobile-android-get-started-push.md).
  Meer informatie over het configureren van uw Mobile Apps back-end voor het gebruik van Azure notification hubs voor het verzenden van push meldingen.
* [Offline synchronisatie inschakelen voor uw Android-app](app-service-mobile-android-get-started-offline-data.md).
  Meer informatie over het toevoegen van offline ondersteuning aan uw app met behulp van een Mobile Apps back-end. Met offline synchronisatie kunnen gebruikers met een mobiele app communiceren&mdash;gegevens weer geven, toevoegen of wijzigen&mdash;zelfs wanneer er geen netwerk verbinding is.

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #cache-tokens
[Refresh expired tokens]: #refresh-tokens
[Next Steps]:#next-steps


<!-- URLs. -->
[Aan de slag met Mobile Apps]: app-service-mobile-android-get-started.md
[Azure Portal]: https://portal.azure.com/
