---
title: Twitter-verificatie configureren-Azure App Service
description: Meer informatie over het configureren van Twitter-verificatie voor uw App Service-app.
services: app-service
documentationcenter: ''
author: mattchenderson
manager: syntaxc4
editor: ''
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 02008b7dc1609a5f28ac6ba2a582933a96428198
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176962"
---
# <a name="configure-your-app-service-app-to-use-twitter-login"></a>Uw App Service-app configureren voor het gebruik van Twitter-aanmelding

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

In dit artikel wordt beschreven hoe u Azure App Service configureert om Twitter als verificatie provider te gebruiken.

Voor het volt ooien van de procedure in dit artikel hebt u een Twitter-account nodig met een bevestigd e-mail adres en telefoon nummer. Als u een nieuw Twitter-account wilt maken, gaat u naar [Twitter.com].

## <a name="register"> </a>Uw toepassing registreren bij Twitter

1. Meld u aan bij de [Azure-portal] en ga naar uw toepassing. Kopieer uw **URL**. U gebruikt deze om uw Twitter-app te configureren.
1. Ga naar de website van de [Twitter-ontwikkel aars] , Meld u aan met de referenties van uw Twitter-account en selecteer **nieuwe app maken**.
1. Voer een **naam** en een **Beschrijving** in voor uw nieuwe app. Plak de **URL** van uw toepassing in het veld **website** . Voer in het veld **call back-URL** de URL van uw app service-app in en voeg het pad toe `/.auth/login/aad/callback`. Bijvoorbeeld `https://contoso.azurewebsites.net/.auth/login/twitter/callback`. Zorg ervoor dat u het HTTPS-schema gebruikt.
1. Lees en accepteer de voor waarden aan de onderkant van de pagina. Selecteer **uw Twitter-toepassing maken**. De details van de toepassing worden weer gegeven.
1. Selecteer het tabblad **instellingen** , Schakel **toestaan dat deze toepassing wordt gebruikt om u aan te melden met Twitter**, en selecteer vervolgens **Update-instellingen**.
1. Selecteer het tabblad **sleutels en toegangs tokens** .

   Noteer deze waarden:
   - Gebruikers sleutel (API-sleutel)
   - Consument geheim (API-geheim)

   > [!NOTE]
   > Het consument geheim is een belang rijke beveiligings referentie. Deel dit geheim niet met iemand of distribueer het met uw app.

## <a name="secrets"> </a>Twitter-gegevens toevoegen aan uw toepassing

1. Ga naar uw toepassing in de [Azure-portal].
1. Selecteer **instellingen** > **verificatie/autorisatie**en zorg ervoor dat **app service-verificatie** is **ingeschakeld**.
1. Selecteer **Twitter**.
1. Plak de `API Key`-en `API Secret`-waarden die u eerder hebt verkregen.
1. Selecteer **OK**.

   ![Scherm afbeelding van Twitter-instellingen voor mobiele apps][1]

   App Service biedt standaard verificatie, maar beperkt geen geautoriseerde toegang tot uw site-inhoud en Api's. U moet gebruikers in uw app-code autoriseren.

1. Beschrijving Als u de toegang tot uw site wilt beperken tot alleen gebruikers die zijn geverifieerd door Twitter, stelt **u actie in die moet worden uitgevoerd wanneer de aanvraag niet is geverifieerd** voor **Twitter**. Wanneer u deze functionaliteit instelt, vereist uw app dat alle aanvragen worden geverifieerd. Ook worden alle niet-geverifieerde aanvragen omgeleid naar Twitter voor authenticatie.

   > [!CAUTION]
   > Het beperken van de toegang op deze manier is van toepassing op alle aanroepen naar uw app. Dit is mogelijk niet wenselijk voor apps met een openbaar beschik bare start pagina, zoals in veel toepassingen met één pagina. Voor dergelijke toepassingen kunt u **anonieme aanvragen (geen actie) toestaan** , zodat de verificatie zelf door de app hand matig wordt gestart. Zie voor meer informatie [verificatie stroom](overview-authentication-authorization.md#authentication-flow).

1. Selecteer **Opslaan**.

U bent nu klaar om Twitter te gebruiken voor verificatie in uw app.

## <a name="related-content"> </a>Volgende stappen

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Twitter-ontwikkel aars]: https://go.microsoft.com/fwlink/p/?LinkId=268300
[twitter.com]: https://go.microsoft.com/fwlink/p/?LinkID=268287
[Azure-portal]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
