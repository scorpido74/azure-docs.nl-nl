---
title: Google-verificatie configureren-Azure App Service
description: Meer informatie over het configureren van Google-verificatie voor uw App Service-app.
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
editor: ''
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/02/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: fcbb284a0807ef88c5f40a7c8b65398d45bf73d7
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232149"
---
# <a name="how-to-configure-your-app-service-application-to-use-google-login"></a>Uw App Service-toepassing configureren voor het gebruik van Google-aanmelding
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

In dit onderwerp wordt beschreven hoe u Azure App Service configureert om Google als verificatie provider te gebruiken.

Als u de procedure in dit onderwerp wilt volt ooien, moet u een Google-account hebben met een bevestigd e-mail adres. Als u een nieuw Google-account wilt maken, gaat u naar [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302).

## <a name="register"> </a>Uw toepassing registreren bij Google
1. Volg de Google-documentatie bij [Google-aanmelding voor apps aan de server zijde](https://developers.google.com/identity/sign-in/web/server-side-flow) om een client-id en client geheim te maken, met de volgende informatie (u hoeft geen code wijzigingen aan te brengen):
    - Voor **geautoriseerde java script**- `https://<app-name>.azurewebsites.net` oorsprong gebruikt u de naam van uw app in  *\<de app-naam >* .
    - Gebruik`https://<app-name>.azurewebsites.net/.auth/login/google/callback`voor geautoriseerde omleidings- **URI**.
1. Wanneer de client-ID en client geheimen zijn gemaakt, kopieert u hun waarden.

    > [!IMPORTANT]
    > Het client geheim is een belang rijke beveiligings referentie. Deel dit geheim niet met iemand of distribueer het in een client toepassing.


## <a name="secrets"> </a>Google-gegevens toevoegen aan uw toepassing
1. Ga in het [Azure-portal]naar uw app service-app. Selecteer in het menu links **verificatie/autorisatie**.
2. Als de functie voor verificatie/autorisatie niet is ingeschakeld, schakelt u de switch in **op aan**.
3. Klik op **Google**. Plak de App-ID en de geheime waarden van de app die u eerder hebt verkregen en schakel desgewenst de scopes in die voor uw toepassing nodig zijn. Klik vervolgens op **OK**.

   App Service biedt authenticatie, maar beperkt geen geautoriseerde toegang tot uw site-inhoud en Api's. Zie [gebruikers machtigen of weigeren](app-service-authentication-how-to.md#authorize-or-deny-users)voor meer informatie.
4. Beschrijving Om de toegang tot uw site te beperken tot alleen gebruikers die zijn geverifieerd door Google, stelt **u een actie in die moet worden uitgevoerd wanneer de aanvraag niet is geverifieerd** voor **Google**. Hiervoor moeten alle aanvragen worden geverifieerd en alle niet-geverifieerde aanvragen worden omgeleid naar Google voor authenticatie.

    > [!NOTE]
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

