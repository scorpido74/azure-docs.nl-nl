---
title: Micro soft-verificatie configureren
description: Meer informatie over het configureren van micro soft-account verificatie als een id-provider voor uw App Service-app.
ms.assetid: ffbc6064-edf6-474d-971c-695598fd08bf
ms.topic: article
ms.date: 08/08/2019
ms.custom: seodec18
ms.openlocfilehash: 95c603d4a10eb0e4d0817e20755c0f9b36baa96f
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842330"
---
# <a name="configure-your-app-service-app-to-use-microsoft-account-login"></a>Uw App Service-app configureren voor het gebruik van aanmelding met een micro soft-account

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

In dit onderwerp wordt beschreven hoe u Azure App Service configureert om AAD te gebruiken voor het ondersteunen van persoonlijke Microsoft-account aanmeldingen.

> [!NOTE]
> Zowel persoonlijke micro soft-accounts als organisatie accounts gebruiken de AAD-ID-provider. Op dit moment is het niet mogelijk om deze id-provider zo te configureren dat beide typen logboeken worden ondersteund.

## <a name="register-microsoft-account"> </a>Uw app registreren bij micro soft-account

1. Ga naar [**app-registraties**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) in het Azure Portal. Meld u indien nodig aan met uw Microsoft-account.
1. Selecteer **nieuwe registratie**en voer vervolgens een toepassings naam in.
1. Onder **ondersteunde account typen**selecteert u **accounts in elke organisatie Directory (een Azure AD-adres lijst-multi tenant) en persoonlijke micro soft-accounts (bijvoorbeeld Skype, Xbox)**
1. Selecteer in de **omleidings-Uri's** **Web**en voer vervolgens `https://<app-domain-name>/.auth/login/aad/callback`in. Vervang *\<app-domain name >* door de domein naam van uw app.  Bijvoorbeeld `https://contoso.azurewebsites.net/.auth/login/aad/callback`. Zorg ervoor dat u het HTTPS-schema in de URL gebruikt.

1. Selecteer **Registreren**.
1. Kopieer de **toepassings-id (client)** . U hebt deze later nodig.
1. Selecteer in het linkerdeel venster **certificaten & geheimen** > **Nieuw client geheim**. Voer een beschrijving in, selecteer de geldigheids duur en selecteer **toevoegen**.
1. Kopieer de waarde die wordt weer gegeven op de pagina **certificaten & geheimen** . Nadat u de pagina verlaat, wordt deze niet meer weer gegeven.

    > [!IMPORTANT]
    > De waarde van het client geheim (wacht woord) is een belang rijke beveiligings referentie. Deel het wacht woord niet met iemand of distribueer het in een client toepassing.

## <a name="secrets"> </a>Micro soft-account gegevens toevoegen aan uw app service-toepassing

1. Ga naar uw toepassing in de [Azure Portal].
1. Selecteer **instellingen** > **verificatie/autorisatie**en controleer of app service- **verificatie** is **ingeschakeld**.
1. Selecteer **Azure Active Directory**onder **verificatie providers**. Selecteer **Geavanceerd** onder **beheer modus**. Plak de client-ID en het client geheim die u eerder hebt verkregen. Gebruik **https://login.microsoftonline.com/9188040d-6c67-4c5b-b112-36a304b66dad/v2.0** voor het URL-veld van de **certificaat verlener** .
1. Selecteer **OK**.

   App Service biedt verificatie, maar beperkt de toegang tot uw site-inhoud en Api's niet. U moet gebruikers in uw app-code autoriseren.

1. Beschrijving Als u de toegang tot Microsoft-account gebruikers wilt beperken, stelt **u een actie in die moet worden uitgevoerd wanneer de aanvraag niet is geverifieerd** om u aan te **melden met Azure Active Directory**. Wanneer u deze functionaliteit instelt, vereist uw app dat alle aanvragen worden geverifieerd. Ook worden alle niet-geverifieerde aanvragen voor authenticatie via AAD omgeleid. Omdat u de URL van uw **Uitgever** hebt geconfigureerd voor het gebruik van de Tenant van het micro soft-account, wordt alleen de persoonlijke acccounts geverifieerd.

   > [!CAUTION]
   > Het beperken van de toegang op deze manier is van toepassing op alle aanroepen naar uw app. Dit is mogelijk niet wenselijk voor apps met een openbaar beschik bare start pagina, zoals in veel toepassingen met één pagina. Voor dergelijke toepassingen kunt u **anonieme aanvragen (geen actie) toestaan** , zodat de verificatie zelf door de app hand matig wordt gestart. Zie voor meer informatie [verificatie stroom](overview-authentication-authorization.md#authentication-flow).

1. Selecteer **Opslaan**.

U bent nu klaar om micro soft-account te gebruiken voor verificatie in uw app.

## <a name="related-content"> </a>Volgende stappen

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure Portal]: https://portal.azure.com/
