---
title: Verificatie toevoegen aan Apache Cordova
description: Meer informatie over het gebruik van mobiele apps in Azure App Service om gebruikers van uw Apache Cordova-app te verifiëren bij identiteitsproviders zoals Google, Facebook, Twitter en Microsoft.
ms.assetid: 10dd6dc9-ddf5-423d-8205-00ad74929f0d
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 3714ce2a8098608851991115aa82afdc00d08a47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77459385"
---
# <a name="add-authentication-to-your-apache-cordova-app"></a>Verificatie toevoegen aan uw Apache Cordova-app
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>Samenvatting
In deze zelfstudie voegt u verificatie toe aan het snelstartproject van de todolist op Apache Cordova met behulp van een ondersteunde identiteitsprovider. Deze zelfstudie is gebaseerd op de zelfstudie [van Mobile Apps,] die u eerst moet voltooien.

## <a name="register-your-app-for-authentication-and-configure-the-app-service"></a><a name="register"></a>Uw app registreren voor verificatie en de App-service configureren
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

[Bekijk een video van vergelijkbare stappen](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-8-Azure-authentication)

## <a name="restrict-permissions-to-authenticated-users"></a><a name="permissions"></a>Machtigingen beperken tot geverifieerde gebruikers
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Nu u controleren of anonieme toegang tot uw backend is uitgeschakeld. In Visual Studio:

* Open het project dat u hebt gemaakt toen u de zelfstudie hebt [voltooid, Aan de slag met Mobiele Apps].
* Voer uw toepassing uit in de **Google Android Emulator.**
* Controleer of er een onverwachte verbindingsfout wordt weergegeven nadat de app is gestart.

Werk vervolgens de app bij om gebruikers te verifiëren voordat u resources opvraagt via de back-end van de mobiele app.

## <a name="add-authentication-to-the-app"></a><a name="add-authentication"></a>Verificatie toevoegen aan de app
1. Open uw project in **Visual** `www/index.html` Studio en open het bestand voor bewerking.
2. Zoek `Content-Security-Policy` de metatag in het hoofdgedeelte.  Voeg de OAuth-host toe aan de lijst met toegestane bronnen.

   | Provider | Naam VAN SDK-provider | OAuth-host |
   |:--- |:--- |:--- |
   | Azure Active Directory | Aad | https://login.microsoftonline.com |
   | Facebook | Facebook | https://www.facebook.com |
   | Google | Google | https://accounts.google.com |
   | Microsoft | microsoftaccount | https://login.live.com |
   | Twitter | Twitter | https://api.twitter.com |

    Een voorbeeld van Content-Security-Policy (geïmplementeerd voor Azure Active Directory) is als volgt:

        <meta http-equiv="Content-Security-Policy" content="default-src 'self'
            data: gap: https://login.microsoftonline.com https://yourapp.azurewebsites.net; style-src 'self'">

    Vervang `https://login.microsoftonline.com` de OAuth-host uit de vorige tabel.  Zie de [documentatie Content-Security-Policy]voor meer informatie over de metatag inhoud-beveiliging.

    Sommige verificatieproviders vereisen geen wijzigingen in het inhoudsbeveiligingsbeleid wanneer ze worden gebruikt op geschikte mobiele apparaten.  Er zijn bijvoorbeeld geen wijzigingen in het inhoudsbeveiligingsbeleid vereist bij het gebruik van Google-verificatie op een Android-apparaat.

3. Open `www/js/index.js` het bestand voor bewerken, `onDeviceReady()` zoek de methode en voeg onder de code voor het maken van de client de volgende code toe:

        // Login to the service
        client.login('SDK_Provider_Name')
            .then(function () {

                // BEGINNING OF ORIGINAL CODE

                // Create a table reference
                todoItemTable = client.getTable('todoitem');

                // Refresh the todoItems
                refreshDisplay();

                // Wire up the UI Event Handler for the Add Item
                $('#add-item').submit(addItemHandler);
                $('#refresh').on('click', refreshDisplay);

                // END OF ORIGINAL CODE

            }, handleError);

    Deze code vervangt de bestaande code waarmee de tabelverwijzing wordt gemaakt en de gebruikersinterface wordt vernieuwd.

    De methode login() start verificatie bij de provider. De methode login() is een async-functie die een JavaScript Promise retourneert.  De rest van de initialisatie wordt in het belofteantwoord geplaatst, zodat deze pas wordt uitgevoerd nadat de inlogmethode is voltooid.

4. Vervang in de code die `SDK_Provider_Name` u zojuist hebt toegevoegd, door de naam van uw inlogprovider. Gebruik bijvoorbeeld voor Azure Active `client.login('aad')`Directory .
5. Voer uw project uit.  Wanneer het project is geïnitialiseren, toont uw toepassing de Aanmeldingspagina Van OAuth voor de gekozen verificatieprovider.

## <a name="next-steps"></a><a name="next-steps"></a>Volgende stappen
* Meer informatie [over verificatie] met Azure App Service.
* Ga door met de zelfstudie door [pushmeldingen] toe te voegen aan je Apache Cordova-app.

Informatie over het gebruik van de SDK's.

* [Apache Cordova SDK]
* [ASP.NET Server SDK]
* [Node.js Server SDK]

<!-- URLs. -->
[Aan de slag met mobiele apps]: app-service-mobile-cordova-get-started.md
[Documentatie over inhoudsbeveiliging en beleid]: https://cordova.apache.org/docs/en/latest/guide/appdev/whitelist/index.html
[Pushmeldingen]: app-service-mobile-cordova-get-started-push.md
[Over verificatie]: app-service-mobile-auth.md
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
