---
title: Azure Active Directory authenticatie configureren-Azure App Service
description: Meer informatie over het configureren van Azure Active Directory verificatie voor uw App Services-toepassing.
author: mattchenderson
services: app-service
documentationcenter: ''
manager: syntaxc4
editor: ''
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/20/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 41084c3532e29b3a52c121d48226c5a45857d5dc
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70088236"
---
# <a name="configure-your-app-service-app-to-use-azure-active-directory-sign-in"></a>Uw App Service-app configureren voor het gebruik van Azure Active Directory aanmelden

> [!NOTE]
> Op dit moment wordt AAD v2 (inclusief MSAL) niet ondersteund voor Azure-app Services en Azure Functions. Ga terug naar updates.
>

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

In dit artikel wordt beschreven hoe u Azure-app Services configureert om Azure Active Directory als een verificatie provider te gebruiken.

## <a name="express"> </a>Configureren met Express-instellingen

1. Ga in het [Azure-portal]naar uw app service-app. Selecteer in het linkernavigatievenster de optie **verificatie/autorisatie**.
2. Als **verificatie/autorisatie** niet is ingeschakeld, selecteert u **aan**.
3. Selecteer **Azure Active Directory**en selecteer vervolgens **Express** onder **beheer modus**.
4. Selecteer **OK** om de app app service te registreren in azure Active Directory. Hiermee maakt u een nieuwe app-registratie. Als u in plaats daarvan een bestaande app-registratie wilt kiezen, klikt u op **een bestaande app selecteren** en zoekt u naar de naam van een eerder gemaakte app-registratie in uw Tenant.
   Klik op de app-registratie om deze te selecteren en klik op **OK**. Klik vervolgens op **OK** op de pagina instellingen Azure Active Directory.
   App Service biedt standaard verificatie, maar beperkt geen geautoriseerde toegang tot uw site-inhoud en Api's. U moet gebruikers in uw app-code autoriseren.
5. Beschrijving Als u de toegang tot uw site wilt beperken tot alleen gebruikers die zijn geverifieerd door Azure Active Directory, stelt **u een actie in die moet worden uitgevoerd wanneer de aanvraag niet is geverifieerd** om u aan te **melden met Azure Active Directory**. Hiervoor moeten alle aanvragen worden geverifieerd en alle niet-geverifieerde aanvragen worden omgeleid naar Azure Active Directory voor authenticatie.

> [!CAUTION]
> Het beperken van de toegang op deze manier is van toepassing op alle aanroepen naar uw app. Dit is mogelijk niet wenselijk voor apps die een openbaar beschik bare start pagina willen, zoals in veel toepassingen met één pagina. Voor dergelijke toepassingen kunt u **anonieme aanvragen (geen actie) toestaan** voor keur, waarbij de app de aanmelding zelf hand matig start, zoals [hier](overview-authentication-authorization.md#authentication-flow)wordt beschreven.

6. Klik op **Opslaan**.

## <a name="advanced"> </a>Configureren met geavanceerde instellingen

U kunt ook hand matig configuratie-instellingen opgeven. Dit is de aanbevolen oplossing als de Azure Active Directory Tenant die u wilt gebruiken, afwijkt van de Tenant waarmee u zich aanmeldt bij Azure. Als u de configuratie wilt volt ooien, moet u eerst een registratie in Azure Active Directory maken en vervolgens een aantal van de registratie gegevens opgeven aan App Service.

### <a name="register"> </a>Uw app service-app registreren bij Azure Active Directory

1. Meld u aan bij de [Azure-portal]en navigeer naar uw app service-app. Kopieer de **URL**van uw app. U gebruikt deze om de registratie van uw Azure Active Directory-app te configureren.
2. Ga naar **Active Directory**, selecteer de **app-registraties**en klik vervolgens op **nieuwe toepassings registratie** aan de bovenkant om een nieuwe app-registratie te starten. 
3. Voer op de pagina **maken** een **naam** in voor de registratie van uw app, selecteer het type **Web-app/API** en plak de URL van de toepassing (uit stap 1) in het vak **voor de aanmeldings-URL** . Klik vervolgens om te **maken**.
4. In een paar seconden ziet u de nieuwe app-registratie die u zojuist hebt gemaakt.
5. Zodra de app-registratie is toegevoegd, klikt u op de naam van de app-registratie, klikt u op **instellingen** bovenaan en klikt u vervolgens op **Eigenschappen** . 
6. Plak in het vak **App-ID-URI** in de toepassings-URL (uit stap 1), ook in de URL van de **Start pagina** plakken in de toepassings-URL (uit stap 1) en klik vervolgens op **Opslaan** .
7. Klik nu op de **antwoord-url's**, bewerk de **antwoord-URL**, plak de URL van de toepassing (uit stap 1) en voeg deze toe aan het einde van de URL, */.auth/login/Aad/callback* ( `https://contoso.azurewebsites.net/.auth/login/aad/callback`bijvoorbeeld). Klik op **Opslaan**.

   > [!NOTE]
   > U kunt dezelfde app-registratie voor meerdere domeinen gebruiken door extra **antwoord-url's**toe te voegen. Zorg ervoor dat u elk App Service exemplaar modelt met een eigen registratie, zodat het een eigen machtigingen en toestemming heeft. U kunt ook gebruikmaken van afzonderlijke app-registraties voor afzonderlijke site sleuven. Dit is om te voor komen dat machtigingen tussen omgevingen worden gedeeld, zodat een fout in nieuwe code die u test, niet van invloed is op de productie.
    
8. Kopieer de **toepassings-id** voor de app op dit moment. Bewaar deze voor later gebruik. U hebt deze nodig om uw App Service-app te configureren.
9. Sluit de pagina **geregistreerde app** . Klik bovenaan op de pagina **app-registraties** op de knop met **eind punten** en kopieer vervolgens de URL van het **WS-Federation-aanmeldings eindpunt** , maar verwijder de `/wsfed` laatste uit de URL. Het eind resultaat moet er als `https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000`volgt uitzien. De domein naam kan verschillend zijn voor een soevereine Cloud. Dit dient als de URL van de uitgever voor later.

### <a name="secrets"> </a>Azure Active Directory gegevens toevoegen aan uw app service-app

1. Ga terug naar uw App Service app in de [Azure-portal]. Klik op **verificatie/autorisatie**. Als de functie voor verificatie/autorisatie niet is ingeschakeld, schakelt u de switch in **op aan**. Klik op **Azure Active Directory**onder verificatie providers om uw app te configureren.

    Beschrijving App Service biedt standaard verificatie, maar beperkt geen geautoriseerde toegang tot uw site-inhoud en Api's. U moet gebruikers in uw app-code autoriseren. Stel de **actie in die moet worden uitgevoerd wanneer de aanvraag niet is geverifieerd** om u aan te **melden met Azure Active Directory**. Deze optie vereist dat alle aanvragen worden geverifieerd en alle niet-geverifieerde aanvragen worden omgeleid naar Azure Active Directory voor verificatie.
2. Klik in de Active Directory verificatie configuratie op **Geavanceerd** onder **beheer modus**. Plak de toepassings-ID in het vak client-ID (uit stap 8) en plak de URL (uit stap 9) in de waarde van de uitgever-URL. Klik vervolgens op **OK**.
3. Klik op de pagina configuratie van Active Directory-verificatie op **Opslaan**.

U bent nu klaar om Azure Active Directory te gebruiken voor verificatie in uw App Service-app.

## <a name="configure-a-native-client-application"></a>Een systeem eigen client toepassing configureren
U kunt systeem eigen clients registreren. Dit biedt meer controle over machtigingen toewijzen. U hebt dit nodig als u aanmeldingen wilt uitvoeren met behulp van een client bibliotheek, zoals de **Active Directory Authentication Library**.

1. Navigeer naar **Azure Active Directory** in het [Azure-portal].
2. Selecteer in de linkernavigatiebalk **app-registraties**. Klik bovenaan op **nieuwe app-registratie** .
4. Voer op de pagina **maken** een **naam** in voor de registratie van uw app. Selecteer **systeem eigen** in het **toepassings type**.
5. Voer in het vak **URI omleiden** het */.auth/login/done* -eind punt van uw site in met behulp van het HTTPS-schema. Deze waarde moet gelijk zijn aan *https://contoso.azurewebsites.net/.auth/login/done* . Als u een Windows-toepassing maakt, gebruikt u in plaats daarvan de [pakket-sid](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) als de URI.
5. Klik op **Create**.
6. Zodra de app-registratie is toegevoegd, selecteert u deze om deze te openen. Zoek de **toepassings-id** en noteer deze waarde.
7. Klik op **alle instellingen** > **vereiste machtigingen** > **toevoegen** > **een API selecteren**.
8. Typ de naam van de App Service-app die u eerder hebt geregistreerd om ernaar te zoeken. Selecteer deze en klik vervolgens op **selecteren**.
9. Selecteer **toegangs \<app_name >** . Klik vervolgens op **Selecteren**. Klik vervolgens op **Gereed**.

U hebt nu een systeem eigen client toepassing geconfigureerd die toegang heeft tot uw App Service-app.

## <a name="related-content"> </a>Gerelateerde inhoud

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-webapp-url.png
[1]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app_registration.png
[2]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-create.png
[3]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-config-appidurl.png
[4]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-config-replyurl.png
[5]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-endpoints.png
[6]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-endpoints-fedmetadataxml.png
[7]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-webapp-auth.png
[8]: ./media/configure-authentication-provider-aad/app-service-webapp-auth-config.png



<!-- URLs. -->

[Azure-portal]: https://portal.azure.com/
[alternative method]:#advanced
