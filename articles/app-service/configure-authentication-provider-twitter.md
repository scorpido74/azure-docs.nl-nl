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
ms.openlocfilehash: f7154da76b41198c208d02b8c563ba26ff8101a1
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70983600"
---
# <a name="how-to-configure-your-app-service-application-to-use-twitter-login"></a>Uw App Service-toepassing configureren voor het gebruik van Twitter-aanmelding
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

In dit onderwerp wordt beschreven hoe u Azure App Service configureert om Twitter als verificatie provider te gebruiken.

Als u de procedure in dit onderwerp wilt volt ooien, moet u een Twitter-account hebben met een bevestigd e-mail adres en telefoon nummer. Als u een nieuw Twitter-account wilt maken, gaat u naar <a href="https://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">Twitter.com</a>.

## <a name="register"> </a>Uw toepassing registreren bij Twitter
1. Meld u aan bij de [Azure-portal]en navigeer naar uw toepassing. Kopieer uw **URL**. U gebruikt deze om uw Twitter-app te configureren.
2. Ga naar de website van de [Twitter-ontwikkel aars] , Meld u aan met de referenties van uw Twitter-account en klik op **nieuwe app maken**.
3. Typ de **naam** en een **Beschrijving** voor de nieuwe app. Plak de **URL** van uw toepassing voor de waarde van de **website** . Typ vervolgens de URL van uw App Service-app in het URL-adres van de **call back**en voeg het pad `/.auth/login/twitter/callback`toe. Bijvoorbeeld `https://contoso.azurewebsites.net/.auth/login/twitter/callback`. Zorg ervoor dat u het HTTPS-schema gebruikt.
4. Lees en accepteer de voor waarden aan de onderkant van de pagina. Klik vervolgens op **uw Twitter-toepassing maken**. De details van de toepassing worden weer gegeven.
5. Klik op het tabblad **instellingen** , Schakel **toestaan dat deze toepassing wordt gebruikt om u aan te melden met Twitter**en klik vervolgens op **instellingen bijwerken**.
6. Selecteer het tabblad **sleutels en toegangs tokens** . Noteer de waarden van de **consument sleutel (API-sleutel)** en het **geheim van de consument (API-geheim)** .
   
   > [!NOTE]
   > Het consument geheim is een belang rijke beveiligings referentie. Deel dit geheim niet met iemand of distribueer het met uw app.
   > 
   > 

## <a name="secrets"> </a>Twitter-gegevens toevoegen aan uw toepassing
1. Ga terug naar uw toepassing in de [Azure-portal]. Klik op **instellingen**en vervolgens op **verificatie/autorisatie**.
2. Als de functie voor verificatie/autorisatie niet is ingeschakeld, schakelt u de switch in **op aan**.
3. Klik op **Twitter**. Plak de App-ID en de geheime waarden van de app die u eerder hebt verkregen. Klik vervolgens op **OK**.
   
   ![][1]
   
   App Service biedt standaard verificatie, maar beperkt geen geautoriseerde toegang tot uw site-inhoud en Api's. U moet gebruikers in uw app-code autoriseren.
4. Beschrijving Als u de toegang tot uw site wilt beperken tot alleen gebruikers die zijn geverifieerd door Twitter, stelt **u actie in die moet worden uitgevoerd wanneer de aanvraag niet is geverifieerd** voor **Twitter**. Hiervoor moeten alle aanvragen worden geverifieerd en alle niet-geverifieerde aanvragen worden omgeleid naar Twitter voor authenticatie.

> [!NOTE]
> Het beperken van de toegang op deze manier is van toepassing op alle aanroepen naar uw app. Dit is mogelijk niet wenselijk voor apps die een openbaar beschik bare start pagina willen, zoals in veel toepassingen met één pagina. Voor dergelijke toepassingen kunt u **anonieme aanvragen (geen actie) toestaan** voor keur, waarbij de app de aanmelding zelf hand matig start, zoals [hier](overview-authentication-authorization.md#authentication-flow)wordt beschreven.

5. Klik op **Opslaan**.

U bent nu klaar om Twitter te gebruiken voor verificatie in uw app.

## <a name="related-content"> </a>Gerelateerde inhoud
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Twitter-ontwikkel aars]: https://go.microsoft.com/fwlink/p/?LinkId=268300
[Azure-portal]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
