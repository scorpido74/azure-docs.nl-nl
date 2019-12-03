---
title: Micro soft-verificatie configureren
description: Meer informatie over het configureren van micro soft-account verificatie als een id-provider voor uw App Service-app.
ms.assetid: ffbc6064-edf6-474d-971c-695598fd08bf
ms.topic: article
ms.date: 08/08/2019
ms.custom: seodec18
ms.openlocfilehash: f9158a4094b7d2ec148c2cae85decb3ad959b7c3
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671926"
---
# <a name="configure-your-app-service-app-to-use-microsoft-account-login"></a>Uw App Service-app configureren voor het gebruik van aanmelding met een micro soft-account

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

In dit onderwerp wordt beschreven hoe u Azure App Service kunt configureren voor gebruik van micro soft-account als verificatie provider. 

## <a name="register-microsoft-account"> </a>Uw app registreren bij micro soft-account

1. Ga naar [**app-registraties**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) in het Azure Portal. Meld u indien nodig aan met uw Microsoft-account.
1. Selecteer **nieuwe registratie**en voer vervolgens een toepassings naam in.
1. Selecteer in de **omleidings-Uri's** **Web**en voer vervolgens `https://<app-domain-name>/.auth/login/microsoftaccount/callback supply the endpoint for your application`in. Vervang *\<app-domain name >* door de domein naam van uw app.  Bijvoorbeeld `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`. Zorg ervoor dat u het HTTPS-schema in de URL gebruikt.

1. Selecteer **Registreren**.
1. Kopieer de **toepassings-id (client)** . U hebt deze later nodig.
1. Selecteer in het linkerdeel venster **certificaten & geheimen** > **Nieuw client geheim**. Voer een beschrijving in, selecteer de geldigheids duur en selecteer **toevoegen**.
1. Kopieer de waarde die wordt weer gegeven op de pagina **certificaten & geheimen** . Nadat u de pagina verlaat, wordt deze niet meer weer gegeven.

    > [!IMPORTANT]
    > Het wacht woord is een belang rijke beveiligings referentie. Deel het wacht woord niet met iemand of distribueer het in een client toepassing.

## <a name="secrets"> </a>Micro soft-account gegevens toevoegen aan uw app service-toepassing

1. Ga naar uw toepassing in de [Azure-portal].
1. Selecteer **instellingen** > **verificatie/autorisatie**en controleer of app service- **verificatie** is **ingeschakeld**.
1. Selecteer onder **verificatie providers**de optie **micro soft-account**. Plak de client-ID en het client geheim die u eerder hebt verkregen. Schakel de scopes in die nodig zijn voor uw toepassing.
1. Selecteer **OK**.

   App Service biedt verificatie, maar beperkt de toegang tot uw site-inhoud en Api's niet. U moet gebruikers in uw app-code autoriseren.

1. Beschrijving Als u de toegang tot Microsoft-account gebruikers wilt beperken, stelt **u een actie in die moet worden uitgevoerd wanneer de aanvraag niet is geverifieerd** voor **aanmelding bij het micro soft-account**. Wanneer u deze functionaliteit instelt, vereist uw app dat alle aanvragen worden geverifieerd. Ook worden alle niet-geverifieerde aanvragen omgeleid naar Microsoft-account voor authenticatie.

   > [!CAUTION]
   > Het beperken van de toegang op deze manier is van toepassing op alle aanroepen naar uw app. Dit is mogelijk niet wenselijk voor apps met een openbaar beschik bare start pagina, zoals in veel toepassingen met één pagina. Voor dergelijke toepassingen kunt u **anonieme aanvragen (geen actie) toestaan** , zodat de verificatie zelf door de app hand matig wordt gestart. Zie voor meer informatie [verificatie stroom](overview-authentication-authorization.md#authentication-flow).

1. Selecteer **Opslaan**.

U bent nu klaar om micro soft-account te gebruiken voor verificatie in uw app.

## <a name="related-content"> </a>Volgende stappen

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure-portal]: https://portal.azure.com/
