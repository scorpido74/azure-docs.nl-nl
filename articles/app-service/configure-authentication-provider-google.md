---
title: Google-verificatie configureren-Azure App Service
description: Meer informatie over het configureren van Google-verificatie voor uw App Services-toepassing.
services: app-service
documentationcenter: ''
author: mattchenderson
manager: syntaxc4
editor: ''
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 4e28f4e330fa24476b717334dfc6d3265640c62a
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70088212"
---
# <a name="how-to-configure-your-app-service-application-to-use-google-login"></a>Uw App Service-toepassing configureren voor het gebruik van Google-aanmelding
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

In dit onderwerp wordt beschreven hoe u Azure App Service configureert om Google als verificatie provider te gebruiken.

Als u de procedure in dit onderwerp wilt volt ooien, moet u een Google-account hebben met een bevestigd e-mail adres. Als u een nieuw Google-account wilt maken, gaat u naar [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302).

## <a name="register"> </a>Uw toepassing registreren bij Google
1. Meld u aan bij de [Azure-portal]en navigeer naar uw toepassing. Kopieer de **URL**die u later gebruikt om uw Google-app te configureren.
2. Ga naar de website van [Google api's](https://go.microsoft.com/fwlink/p/?LinkId=268303) , Meld u aan met de referenties van uw Google-account, klik op **project maken**, geef een **project naam**op en klik vervolgens op **maken**.
3. Wanneer het project is gemaakt, selecteert u het. Klik in het project dashboard op **Ga naar api's Overview**(Engelstalig).
4. Selecteer **api's en services inschakelen**. Zoek naar **Google + API**en selecteer deze. Klik vervolgens op **inschakelen**.
5. Geef in het dialoog**venster**links in de linkernavigatiebalk de **aanmeldings gegevens** > op en selecteer vervolgens uw **e-mail adres**, voer een **product naam**in en klik op **Opslaan**.
6. Klik op het tabblad **referenties** op **referenties** > **OAuth-client-id**maken.
7. Selecteer webtoepassing in het scherm client-ID maken.
8. Plak de App Service **URL** die u eerder hebt gekopieerd naar de **geautoriseerde java script-oorsprong**en plak vervolgens de omleidings-URI in de omleidings- **URI**. De omleidings-URI is de URL van uw toepassing die is toegevoegd aan het pad, */.auth/login/Google/callback*. Bijvoorbeeld `https://contoso.azurewebsites.net/.auth/login/google/callback`. Zorg ervoor dat u het HTTPS-schema gebruikt. Klik vervolgens op **Maken**.
9. Noteer de waarden van de client-ID en het client geheim in het volgende scherm.

    > [!IMPORTANT]
    > Het client geheim is een belang rijke beveiligings referentie. Deel dit geheim niet met iemand of distribueer het in een client toepassing.


## <a name="secrets"> </a>Google-gegevens toevoegen aan uw toepassing
1. Ga terug naar uw toepassing in de [Azure-portal]. Klik op **instellingen**en vervolgens op **verificatie/autorisatie**.
2. Als de functie voor verificatie/autorisatie niet is ingeschakeld, schakelt u de switch in **op aan**.
3. Klik op **Google**. Plak de App-ID en de geheime waarden van de app die u eerder hebt verkregen en schakel desgewenst de scopes in die voor uw toepassing nodig zijn. Klik vervolgens op **OK**.
   
   ![][1]
   
   App Service biedt standaard verificatie, maar beperkt geen geautoriseerde toegang tot uw site-inhoud en Api's. U moet gebruikers in uw app-code autoriseren.
4. Beschrijving Om de toegang tot uw site te beperken tot alleen gebruikers die zijn geverifieerd door Google, stelt **u een actie in die moet worden uitgevoerd wanneer de aanvraag niet is geverifieerd** voor **Google**. Hiervoor moeten alle aanvragen worden geverifieerd en alle niet-geverifieerde aanvragen worden omgeleid naar Google voor authenticatie.

> [!CAUTION]
> Het beperken van de toegang op deze manier is van toepassing op alle aanroepen naar uw app. Dit is mogelijk niet wenselijk voor apps die een openbaar beschik bare start pagina willen, zoals in veel toepassingen met één pagina. Voor dergelijke toepassingen kunt u **anonieme aanvragen (geen actie) toestaan** voor keur, waarbij de app de aanmelding zelf hand matig start, zoals [hier](overview-authentication-authorization.md#authentication-flow)wordt beschreven.

5. Klik op **Opslaan**.

U bent nu klaar om Google te gebruiken voor verificatie in uw app.

## <a name="related-content"> </a>Gerelateerde inhoud
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: https://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure-portal]: https://portal.azure.com/

