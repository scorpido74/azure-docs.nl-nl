---
title: Authenticatie toevoegen op Apache Cordova met Mobile Apps | Microsoft Docs
description: Meer informatie over het gebruik van Mobile Apps in Azure App Service om gebruikers van uw Apache Cordova-app te verifiëren via verschillende id-providers, waaronder Google, Facebook, Twitter en micro soft.
services: app-service\mobile
documentationcenter: javascript
author: elamalani
manager: crdun
editor: ''
ms.assetid: 10dd6dc9-ddf5-423d-8205-00ad74929f0d
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: c42cf2c944457b6b738578f738cd338977df8185
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388816"
---
# <a name="add-authentication-to-your-apache-cordova-app"></a>Verificatie toevoegen aan uw Apache Cordova-app
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> Visual Studio App Center ondersteunt end-to-end-services en geïntegreerde services die een centrale rol spelen bij het ontwikkelen van mobiele apps. Ontwikkelaars kunnen services **bouwen**, **testen** en **distribueren** om een CI/CD-pijplijn (continue integratie en continue levering) in te stellen. Zodra de app is geïmplementeerd, kunnen ontwikkelaars de status en het gebruik van hun app controleren met behulp van de **analyseservice** en de **diagnoseservice** en communiceren met gebruikers met behulp van de **pushservice**. Ontwikkelaars kunnen ook gebruikmaken van **Auth** voor het verifiëren van gebruikers en van **Data** Service voor het persistent maken en synchroniseren van app-gegevens in de cloud.
>
> Als u Cloud Services wilt integreren in uw mobiele toepassing, meldt u zich aan bij [app Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) vandaag.

## <a name="summary"></a>Samenvatting
In deze zelf studie voegt u verificatie toe aan het ToDoList Quick start-project op Apache Cordova met behulp van een ondersteunde ID-provider. Deze zelf studie is gebaseerd op de zelf studie [aan de slag met Mobile apps] , die u eerst moet volt ooien.

## <a name="register"></a>Registreer uw app voor verificatie en configureer de App Service
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

[Bekijk een video van vergelijkbare stappen](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-8-Azure-authentication)

## <a name="permissions"></a>Machtigingen voor geverifieerde gebruikers beperken
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

U kunt nu controleren of anonieme toegang tot uw back-end is uitgeschakeld. In Visual Studio:

* Open het project dat u hebt gemaakt tijdens het volt ooien van de zelf studie aan de [Aan de slag met Mobile Apps].
* Voer uw toepassing uit in de **Google Android-Emulator**.
* Controleer of er een onverwachte verbindings fout wordt weer gegeven nadat de app is gestart.

Werk vervolgens de app bij om gebruikers te verifiëren voordat ze resources aanvragen bij de back-end van de mobiele app.

## <a name="add-authentication"></a>Verificatie toevoegen aan de app
1. Open uw project in **Visual Studio**en open vervolgens het `www/index.html`-bestand om het te bewerken.
2. Zoek de meta-tag `Content-Security-Policy` in het hoofd gedeelte.  Voeg de OAuth-host toe aan de lijst met toegestane bronnen.

   | Provider | Naam van de SDK-provider | OAuth-host |
   |:--- |:--- |:--- |
   | Azure Active Directory | Aad | https://login.microsoftonline.com |
   | Facebook | Facebook | https://www.facebook.com |
   | Google | Google | https://accounts.google.com |
   | Microsoft | microsoftaccount | https://login.live.com |
   | Twitter | Twitter | https://api.twitter.com |

    Een voor beeld van Content-Security-Policy (geïmplementeerd voor Azure Active Directory) is als volgt:

        <meta http-equiv="Content-Security-Policy" content="default-src 'self'
            data: gap: https://login.microsoftonline.com https://yourapp.azurewebsites.net; style-src 'self'">

    Vervang `https://login.microsoftonline.com` door de OAuth-host uit de voor gaande tabel.  Zie de [documentatie voor Content-Security-Policy]voor meer informatie over de META code voor Content-Security-Policy.

    Voor sommige verificatie providers zijn geen wijzigingen in de beveiliging van inhoud vereist wanneer deze op de juiste mobiele apparaten worden gebruikt.  Er zijn bijvoorbeeld geen wijzigingen in de beveiliging van het beleid vereist bij het gebruik van Google-verificatie op een Android-apparaat.

3. Open het bestand `www/js/index.js` om het te bewerken, zoek de `onDeviceReady()`-methode en voeg onder de client aanmaak code de volgende code toe:

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

    Met deze code wordt de bestaande code die de tabel verwijzing maakt, vervangen en wordt de gebruikers interface vernieuwd.

    De methode login () start de verificatie met de provider. De methode login () is een async-functie die een Java script-belofte retourneert.  De rest van de initialisatie wordt binnen de Promise-respons geplaatst, zodat deze pas wordt uitgevoerd als de methode login () is voltooid.

4. Vervang `SDK_Provider_Name` door de naam van uw aanmeldings provider in de code die u zojuist hebt toegevoegd. Gebruik bijvoorbeeld voor Azure Active Directory `client.login('aad')`.
5. Voer uw project uit.  Wanneer de initialisatie van het project is voltooid, wordt in uw toepassing de OAuth-aanmeldings pagina voor de gekozen verificatie provider weer gegeven.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie [over verificatie] met Azure app service.
* Ga door met de zelf studie door [Pushmeldingen] toe te voegen aan uw Apache Cordova-app.

Informatie over het gebruik van de SDK's.

* [Apache Cordova SDK]
* [ASP.NET Server SDK]
* [Node.js Server SDK]

<!-- URLs. -->
[Aan de slag met Mobile Apps]: app-service-mobile-cordova-get-started.md
[Documentatie voor Content-Security-Policy]: https://cordova.apache.org/docs/en/latest/guide/appdev/whitelist/index.html
[Pushmeldingen]: app-service-mobile-cordova-get-started-push.md
[Over verificatie]: app-service-mobile-auth.md
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
