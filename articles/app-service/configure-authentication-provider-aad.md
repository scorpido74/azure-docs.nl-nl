---
title: Azure Active Directory authenticatie configureren-Azure App Service
description: Meer informatie over het configureren van Azure Active Directory verificatie voor uw App Service-app.
author: cephalin
services: app-service
documentationcenter: ''
manager: gwallace
editor: ''
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.service: app-service
ms.workload: web,mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/03/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 8b4b6549f9553773cc44c311f49befbb3eec9dc9
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/03/2019
ms.locfileid: "70233091"
---
# <a name="configure-your-app-service-app-to-use-azure-active-directory-sign-in"></a>Uw App Service-app configureren voor het gebruik van Azure Active Directory aanmelden

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

> [!NOTE]
> Op dit moment wordt AAD v2 (inclusief MSAL) niet ondersteund voor Azure App Service en Azure Functions.
>

In dit artikel wordt beschreven hoe u Azure App Service kunt configureren om Azure Active Directory als een verificatie provider te gebruiken.

Het is raadzaam om elke App Service-app te configureren met een eigen registratie, zodat deze een eigen machtigingen en toestemming heeft. U kunt ook afzonderlijke app-registraties voor afzonderlijke implementatie sleuven gebruiken. Dit voor komt dat het delen van machtigingen tussen omgevingen wordt voor komen, zodat een probleem in nieuwe code die u test, niet van invloed is op de productie.

## <a name="express"> </a>Configureren met Express-instellingen

1. Ga in het [Azure-portal]naar uw app service-app. Selecteer in het linkernavigatievenster de optie **verificatie/autorisatie**.
2. Als **verificatie/autorisatie** niet is ingeschakeld, selecteert u **aan**.
3. Selecteer **Azure Active Directory**en selecteer vervolgens **Express** onder **beheer modus**.
4. Selecteer **OK** om de app app service te registreren in azure Active Directory. Hiermee maakt u een nieuwe app-registratie. Als u in plaats daarvan een bestaande app-registratie wilt kiezen, klikt u op **een bestaande app selecteren** en zoekt u naar de naam van een eerder gemaakte app-registratie in uw Tenant. Klik op de app-registratie om deze te selecteren en klik op **OK**. Klik vervolgens op **OK** op de pagina instellingen Azure Active Directory.
App Service biedt standaard verificatie, maar beperkt geen geautoriseerde toegang tot uw site-inhoud en Api's. U moet gebruikers in uw app-code autoriseren.
5. Beschrijving Als u de toegang tot uw app wilt beperken tot alleen gebruikers die zijn geverifieerd door Azure Active Directory, stelt **u een actie in die moet worden uitgevoerd wanneer de aanvraag niet is geverifieerd** om u aan te **melden met Azure Active Directory**. Hiervoor moeten alle aanvragen worden geverifieerd en alle niet-geverifieerde aanvragen worden omgeleid naar Azure Active Directory voor authenticatie.

    > [!NOTE]
    > Het beperken van de toegang op deze manier is van toepassing op alle aanroepen naar uw app. Dit is mogelijk niet wenselijk voor apps die een openbaar beschik bare start pagina willen, zoals in veel toepassingen met één pagina. Voor dergelijke toepassingen kunt u **anonieme aanvragen (geen actie) toestaan** voor keur, waarbij de app de aanmelding zelf hand matig start, zoals [hier](overview-authentication-authorization.md#authentication-flow)wordt beschreven.
6. Klik op **Opslaan**.

## <a name="advanced"> </a>Configureren met geavanceerde instellingen

U kunt ook hand matig configuratie-instellingen opgeven als de Azure Active Directory Tenant die u wilt gebruiken, afwijkt van de Tenant waarmee u zich aanmeldt bij Azure. Als u de configuratie wilt volt ooien, moet u eerst een registratie in Azure Active Directory maken en vervolgens een aantal van de registratie gegevens opgeven aan App Service.

### <a name="register"> </a>Een app-registratie maken in azure AD voor uw app service-app

Wanneer u hand matig een app-registratie maakt, noteert u drie stukjes informatie die u later nodig hebt bij het configureren van uw App Service-app: de client-ID, de Tenant-ID en optioneel het client geheim en de URI van de toepassings-ID.

1. Ga in het [Azure-portal]naar uw app service app en noteer de **URL**van uw app. U gebruikt deze om de registratie van uw Azure Active Directory-app te configureren.
1. Selecteer in de [Azure-portal]in het menu links **Active Directory** > **app-registraties** > **nieuwe registratie**. 
1. Voer op de pagina **een toepassing registreren** een **naam** in voor de registratie van uw app.
1. In omleidings- **URI**selecteert u **Web** en typt u de URL van uw app service- `/.auth/login/aad/callback`app en voegt u het pad toe. Bijvoorbeeld `https://contoso.azurewebsites.net/.auth/login/aad/callback`. Selecteer vervolgens **Maken**.
1. Nadat de app-registratie is gemaakt, kopieert u de **toepassings-id** en de **Directory (Tenant)-ID** voor later.
1. Selecteer **huis stijl**. Typ in de URL van de **Start pagina**de URL van uw app service-app en selecteer **Opslaan**.
1. Selecteer **een API** > -**set**beschikbaar maken. Plak de URL van uw App Service-app en selecteer **Opslaan**.

    > [!NOTE]
    > Deze waarde is de **URI van de toepassings-id** van de app-registratie. Als u een front-end-web-app wilt gebruiken om toegang te krijgen tot een back-end-API, bijvoorbeeld en u wilt dat de back-end expliciet toegang tot de front-end verleent, hebt u de URI van de **toepassings-id** van de *front-end* nodig wanneer u de app service app-resource van de < C2 configureert >-back-end.
1. Selecteer **Een bereik toevoegen**. Typ *user_impersonation*in **Scope naam**. Typ in de tekst vakken de naam en beschrijving van het instemmings bereik dat gebruikers moeten zien op de pagina toestemming, zoals *toegang tot mijn app*. Wanneer u klaar bent, klikt u op **bereik toevoegen**.
1. Beschrijving Als u een client geheim wilt maken, selecteert u **Certificaten & geheimen** > **Nieuw client geheim** > **toevoegen**. Kopieer de waarde van het client geheim dat op de pagina wordt weer gegeven. Wanneer u de cursor verlaat, wordt deze niet meer weer gegeven.
1. Beschrijving Selecteer **verificatie** in het menu om meerdere **antwoord-url's**toe te voegen.

### <a name="secrets"> </a>Azure Active Directory gegevens toevoegen aan uw app service-app

1. Ga in het [Azure-portal]naar uw app service-app. Selecteer in het menu links **verificatie/autorisatie**. Als de functie voor verificatie/autorisatie niet is ingeschakeld, selecteert u **aan**. 
1. Beschrijving Standaard staat App Service verificatie niet-geverifieerde toegang tot uw app toe. Als u gebruikers verificatie wilt afdwingen, stelt **u de actie in die moet worden uitgevoerd wanneer de aanvraag niet is geverifieerd** om u aan te **melden met Azure Active Directory**.
1. Selecteer **Azure Active Directory**onder verificatie providers.
1. Selecteer in de **beheer modus** **geavanceerd** en configureer app service verificatie volgens de volgende tabel:

    |Veld|Description|
    |-|-|
    |Client-id| Gebruik de **toepassings-id (client)** van de app-registratie. |
    |ID van de verlener| Gebruik `https://login.microsoftonline.com/<tenant-id>`en  *Vervang\<Tenant-id >* met de **Directory-id (Tenant)** van de app-registratie. |
    |Client geheim (optioneel)| Gebruik het client geheim dat u hebt gegenereerd in de app-registratie.|
    |Toegestane tokendoelgroep| Als dit een *back-end-* app is en u verificatie tokens van een front-end-app wilt toestaan, voegt u de URI van de **toepassings-id** van de *front-end* toe. |
1. Selecteer **OK**en selecteer vervolgens **Opslaan**.

U bent nu klaar om Azure Active Directory te gebruiken voor verificatie in uw App Service-app.

## <a name="configure-a-native-client-application"></a>Een systeem eigen client toepassing configureren
U kunt systeem eigen clients registreren als u aanmeldingen wilt uitvoeren met behulp van een client bibliotheek, zoals de **Active Directory Authentication Library**.

1. Selecteer in de [Azure-portal]in het menu links **Active Directory** > **app-registraties** > **nieuwe registratie**. 
1. Voer op de pagina **een toepassing registreren** een **naam** in voor de registratie van uw app.
1. In omleidings- **URI**selecteert u **open bare client (mobiele & bureau blad)** en typt u de URL van uw `/.auth/login/aad/callback`app service app en voegt u het pad toe. Bijvoorbeeld `https://contoso.azurewebsites.net/.auth/login/aad/callback`. Selecteer vervolgens **Maken**.

    > [!NOTE]
    > Voor een Windows-toepassing gebruikt u in plaats daarvan de SID van het [pakket](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) als de URI.
1. Wanneer de app-registratie is gemaakt, kopieert u de waarde van de **toepassings-id (client)** .
1. Selecteer in het menu links de **optie API-machtigingen** > **een machtiging** > **mijn api's**toevoegen.
1. Selecteer de app-registratie die u eerder hebt gemaakt voor uw App Service-app. Als u de app-registratie niet ziet, controleert u of u het bereik **user_impersonation** hebt toegevoegd in [een app-registratie in azure AD maken voor uw app service-app](#register).
1. Selecteer **user_impersonation** en klik op **machtigingen toevoegen**.

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
