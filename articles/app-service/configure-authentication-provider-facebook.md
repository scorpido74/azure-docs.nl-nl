---
title: Facebook-verificatie configureren-Azure App Service
description: Meer informatie over het configureren van Facebook-verificatie voor uw App Service-app
services: app-service
documentationcenter: ''
author: mattchenderson
manager: syntaxc4
editor: ''
ms.assetid: b6b4f062-fcb4-47b3-b75a-ec4cb51a62fd
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/06/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: fb8497f3b9b887e2fd06b350bcc25ac8faaa7b43
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177010"
---
# <a name="configure-your-app-service-app-to-use-facebook-login"></a>Uw App Service-app configureren voor het gebruik van Facebook-aanmelding

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

In dit artikel wordt beschreven hoe u Azure App Service kunt configureren om Facebook als verificatie provider te gebruiken.

Voor het volt ooien van de procedure in dit artikel hebt u een Facebook-account nodig met een bevestigd e-mail adres en een mobiel telefoon nummer. Als u een nieuw Facebook-account wilt maken, gaat u naar [Facebook.com].

## <a name="register"> </a>Uw toepassing registreren bij Facebook

1. Ga naar de website van [Facebook-ontwikkel aars] en meld u aan met de referenties van uw Facebook-account.

   Als u geen Facebook-account voor ontwikkel aars hebt, selecteert u **aan de slag** en volgt u de registratie stappen.
1. **Mijn apps**selecteren  > **nieuwe app toevoegen**.
1. In het veld **weergave naam** :
   1. Typ een unieke naam voor uw app.
   1. Geef uw **contact-e-mail**.
   1. Selecteer **App-ID maken**.
   1. Voltooi de beveiligings controle.

   Het dash board voor ontwikkel aars voor uw nieuwe Facebook-app wordt geopend.
1. Selecteer **dash board** > **Facebook-aanmelding** >   > -**Web** **instellen**.
1. Selecteer in het navigatie venster links onder **Facebook-aanmelding**de optie **instellingen**.
1. Voer in het veld **geldige OAuth omleidings-uri's** `https://<app-name>.azurewebsites.net/.auth/login/facebook/callback` in. Vergeet niet om `<app-name>` te vervangen door de naam van uw Azure App Service-app.
1. Selecteer **Save changes**.
1. Selecteer in het linkerdeel venster **instellingen** > **basis**. 
1. In het veld **app-geheim** selecteert u **weer geven**. Kopieer de waarden van de **App-ID** en het **app-geheim**. U kunt deze later gebruiken om uw App Service-app te configureren in Azure.

   > [!IMPORTANT]
   > Het app-geheim is een belang rijke beveiligings referentie. Deel dit geheim niet met iemand of distribueer het in een client toepassing.
   >

1. Het Facebook-account dat u hebt gebruikt om de toepassing te registreren, is een beheerder van de app. Op dit moment kunnen alleen beheerders zich aanmelden bij deze toepassing.

   Als u andere Facebook-accounts wilt verifiëren, selecteert u **app controleren** en schakelt u de optie **\<your-App-name > openbaar** in om het grote publiek toegang tot de app te geven met behulp van Facebook-verificatie.

## <a name="secrets"> </a>Facebook-gegevens toevoegen aan uw toepassing

1. Meld u aan bij de [Azure-portal] en navigeer naar uw app service-app.
1. Selecteer **instellingen** > **verificatie/autorisatie**en zorg ervoor dat **app service-verificatie** is **ingeschakeld**.
1. Selecteer **Facebook**en plak vervolgens de waarden voor app-id en app-geheim die u eerder hebt verkregen. Schakel de scopes in die nodig zijn voor uw toepassing.
1. Selecteer **OK**.

   ![Scherm afbeelding van Facebook-instellingen voor mobiele apps][0]

    App Service biedt standaard verificatie, maar de toegang tot de inhoud en Api's van uw site wordt niet beperkt. U moet gebruikers in uw app-code autoriseren.
1. Beschrijving Als u de toegang wilt beperken tot gebruikers die zijn geverifieerd door Facebook, stelt **u actie in die moet worden uitgevoerd wanneer de aanvraag niet is geverifieerd** voor **Facebook**. Wanneer u deze functionaliteit instelt, vereist uw app dat alle aanvragen worden geverifieerd. Ook worden alle niet-geverifieerde aanvragen voor verificatie doorgestuurd naar Facebook.

   > [!CAUTION]
   > Het beperken van de toegang op deze manier is van toepassing op alle aanroepen naar uw app. Dit is mogelijk niet wenselijk voor apps met een openbaar beschik bare start pagina, zoals in veel toepassingen met één pagina. Voor dergelijke toepassingen kunt u **anonieme aanvragen (geen actie) toestaan** , zodat de verificatie zelf door de app hand matig wordt gestart. Zie voor meer informatie [verificatie stroom](overview-authentication-authorization.md#authentication-flow).

1. Selecteer **Opslaan**.

U bent nu klaar om Facebook te gebruiken voor verificatie in uw app.

## <a name="related-content"> </a>Volgende stappen

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Facebook-ontwikkel aars]: https://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: https://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure-portal]: https://portal.azure.com/
