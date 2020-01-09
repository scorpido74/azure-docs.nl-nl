---
title: Azure AD-verificatie configureren
description: Meer informatie over het configureren van Azure Active Directory-verificatie als een id-provider voor uw App Service-app.
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.topic: article
ms.date: 09/03/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: b85c68b19a44021710dbc9143e255600b43b2cba
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/06/2020
ms.locfileid: "75666139"
---
# <a name="configure-your-app-service-app-to-use-azure-ad-login"></a>Uw App Service-app configureren voor het gebruik van Azure AD-aanmelding

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

In dit artikel wordt beschreven hoe u Azure App Service configureert om Azure Active Directory (Azure AD) als verificatie provider te gebruiken.

> [!NOTE]
> Op dit moment worden Azure App Service en Azure Functions alleen ondersteund door Azure AD v 1.0. Ze worden niet ondersteund door de [micro soft Identity platform v 2.0](https://docs.microsoft.com/azure/active-directory/develop/v2-overview), waaronder micro soft-verificatie bibliotheken (MSAL).

Volg deze aanbevolen procedures bij het instellen van uw app en verificatie:

- Geef elke App Service-app eigen machtigingen en toestemming.
- Configureer elke App Service-app met een eigen registratie.
- Vermijd het delen van machtigingen tussen omgevingen door afzonderlijke app-registraties voor afzonderlijke implementatie sleuven te gebruiken. Bij het testen van nieuwe code kunt u deze procedure gebruiken om te voor komen dat problemen van invloed zijn op de productie-app.

## <a name="express"> </a>Configureren met Express-instellingen

1. In de [Azure Portal]zoekt en selecteert u **app Services**en selecteert u vervolgens uw app.
1. Selecteer in het linkerdeel venster onder **instellingen** **verificatie/autorisatie** en zorg ervoor dat **app service-verificatie** is **ingeschakeld**.
1. Selecteer **Azure Active Directory**en selecteer vervolgens in **beheer modus** **Express**.
1. Selecteer **OK** om de app app service te registreren in azure Active Directory. Er wordt een nieuwe app-registratie gemaakt.

   Als u in plaats daarvan een bestaande app-registratie wilt kiezen:

   1. Kies **een bestaande app selecteren** en zoek vervolgens naar de naam van een eerder gemaakte app-registratie in uw Tenant.
   1. Selecteer de app-registratie en selecteer vervolgens **OK**.
   1. Selecteer **OK** op de pagina instellingen Azure Active Directory.

   App Service biedt standaard verificatie, maar beperkt geen geautoriseerde toegang tot uw site-inhoud en Api's. U moet gebruikers in uw app-code autoriseren.
1. Beschrijving Als u app-toegang alleen wilt beperken voor gebruikers die zijn geverifieerd door Azure Active Directory, stelt **u een actie in die moet worden uitgevoerd wanneer de aanvraag niet is geverifieerd** om u aan te **melden met Azure Active Directory**. Wanneer u deze functionaliteit instelt, vereist uw app dat alle aanvragen worden geverifieerd. Ook worden alle niet-geverifieerde omgeleid naar Azure Active Directory voor authenticatie.

    > [!CAUTION]
    > Het beperken van de toegang op deze manier is van toepassing op alle aanroepen naar uw app. Dit is mogelijk niet wenselijk voor apps met een openbaar beschik bare start pagina, zoals in veel toepassingen met één pagina. Voor dergelijke toepassingen kunt u **anonieme aanvragen (geen actie)** de voor keur geven, waarbij de app de aanmelding zelf hand matig start. Zie voor meer informatie [verificatie stroom](overview-authentication-authorization.md#authentication-flow).
1. Selecteer **Opslaan**.

## <a name="advanced"> </a>Configureren met geavanceerde instellingen

U kunt app-instellingen hand matig configureren als u een Azure AD-Tenant wilt gebruiken die afwijkt van het account dat u gebruikt om u aan te melden bij Azure. Als u deze aangepaste configuratie wilt volt ooien, moet u het volgende doen:

1. Een registratie maken in azure AD.
1. Geef een aantal van de registratie gegevens op App Service.

### <a name="register"> </a>Een app-registratie maken in azure AD voor uw app service-app

U hebt de volgende informatie nodig wanneer u uw App Service-app configureert:

- Client-id
- Tenant-id
- Client geheim (optioneel)
- URI voor de toepassings-ID

Voer de volgende stappen uit:

1. Meld u aan bij de [Azure Portal], zoek en selecteer **app Services**en selecteer vervolgens uw app. Noteer de **URL**van uw app. U gebruikt deze om de registratie van uw Azure Active Directory-app te configureren.
1. Selecteer **Azure Active Directory** > **app-registraties** > **nieuwe registratie**.
1. Voer op de pagina **een toepassing registreren** een **naam** in voor de registratie van uw app.
1. In **omleidings-URI**selecteert u **Web** en voert u de URL van uw app service-app in en voegt u het pad toe `/.auth/login/aad/callback`. Bijvoorbeeld `https://contoso.azurewebsites.net/.auth/login/aad/callback`. 
1. Selecteer **Maken**.
1. Nadat de app-registratie is gemaakt, kopieert u de **toepassings-id** en de **Directory (Tenant)-ID** voor later.
1. Selecteer **huis stijl**. Voer de URL van uw App Service-app in op de URL van de **Start pagina**en selecteer **Opslaan**.
1. Selecteer **een API beschikbaar** maken > **instellen**. Plak de URL van uw App Service-app en selecteer **Opslaan**.

   > [!NOTE]
   > Deze waarde is de **URI van de toepassings-id** van de app-registratie. Als uw web-app toegang tot een API in de Cloud vereist, hebt u de URI van de **toepassings-id** van de web-app nodig wanneer u de Cloud app Service Resource configureert. U kunt dit bijvoorbeeld gebruiken als u wilt dat de Cloud service expliciet toegang tot de Web-App verleent.

1. Selecteer **Een bereik toevoegen**.
   1. Voer *user_impersonation*in **Scope naam**in.
   1. Voer in de tekst vakken de naam en beschrijving van het toestemming bereik in die gebruikers op de pagina toestemming moeten zien. Voer bijvoorbeeld *toegang tot mijn app*in. 
   1. Selecteer **bereik toevoegen**.
1. Beschrijving Als u een client geheim wilt maken, selecteert u **certificaten & geheimen** > **nieuw client geheim** > **toevoegen**. Kopieer de waarde van het client geheim dat op de pagina wordt weer gegeven. Het wordt niet meer weer gegeven.
1. Beschrijving Selecteer **verificatie**om meerdere **antwoord-url's**toe te voegen.

### <a name="secrets"> </a>Azure Active Directory gegevens toevoegen aan uw app service-app

1. In de [Azure Portal]zoekt en selecteert u **app Services**en selecteert u vervolgens uw app. 
1. Selecteer in het linkerdeel venster onder **instellingen**de optie **verificatie/autorisatie** en zorg ervoor dat **app service authenticatie** is **ingeschakeld**.
1. Beschrijving Standaard staat App Service verificatie niet-geverifieerde toegang tot uw app toe. Als u gebruikers verificatie wilt afdwingen, stelt **u de actie in die moet worden uitgevoerd wanneer de aanvraag niet is geverifieerd** om u aan te **melden met Azure Active Directory**.
1. Selecteer **Azure Active Directory**onder verificatie providers.
1. Selecteer in de **beheer modus** **geavanceerd** en configureer app service verificatie volgens de volgende tabel:

    |Veld|Beschrijving|
    |-|-|
    |Client-id| Gebruik de **toepassings-id (client)** van de app-registratie. |
    |ID van de verlener| Gebruik `https://login.microsoftonline.com/<tenant-id>`en vervang *\<Tenant-id >* door de Directory-id **(Tenant)** van de app-registratie. |
    |Client geheim (optioneel)| Gebruik het client geheim dat u hebt gegenereerd in de app-registratie.|
    |Toegestane token doel groepen| Als dit een Cloud-of server-app is en u verificatie tokens van een web-app wilt toestaan, voegt u hier de URI voor de **toepassings-id** van de web-app toe. |

    > [!NOTE]
    > De geconfigureerde **client-id** wordt *altijd* impliciet beschouwd als een toegestane doel groep, ongeacht hoe u de **toegestane token doel groepen**hebt geconfigureerd.
1. Selecteer **OK**en selecteer vervolgens **Opslaan**.

U bent nu klaar om Azure Active Directory te gebruiken voor verificatie in uw App Service-app.

## <a name="configure-a-native-client-application"></a>Een systeem eigen client toepassing configureren

U kunt systeem eigen clients registreren om verificatie toe te staan met behulp van een client bibliotheek, zoals de **Active Directory Authentication Library**.

1. Selecteer in de [Azure Portal] **Active Directory** > **app-registraties** > **nieuwe registratie**.
1. Voer op de pagina **een toepassing registreren** een **naam** in voor de registratie van uw app.
1. In **omleidings-URI**selecteert u **open bare client (mobiele & bureau blad)** en voert u de URL van uw app service app in en voegt u het pad `/.auth/login/aad/callback`toe. Bijvoorbeeld `https://contoso.azurewebsites.net/.auth/login/aad/callback`.
1. Selecteer **Maken**.

    > [!NOTE]
    > Voor een Windows-toepassing gebruikt u in plaats daarvan de SID van het [pakket](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) als de URI.
1. Nadat de app-registratie is gemaakt, kopieert u de waarde van de **toepassings-id (client)** .
1. Selecteer **API-machtigingen** > een machtiging > **mijn Api's** **toe te voegen** .
1. Selecteer de app-registratie die u eerder hebt gemaakt voor uw App Service-app. Als u de app-registratie niet ziet, zorg er dan voor dat u het **user_impersonation** bereik hebt toegevoegd in [een app-registratie in azure AD maken voor uw app service-app](#register).
1. Selecteer **user_impersonation**en selecteer vervolgens **machtigingen toevoegen**.

U hebt nu een systeem eigen client toepassing geconfigureerd die toegang heeft tot uw App Service-app.

## <a name="related-content"> </a>Volgende stappen

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

[Azure Portal]: https://portal.azure.com/
[alternative method]:#advanced
