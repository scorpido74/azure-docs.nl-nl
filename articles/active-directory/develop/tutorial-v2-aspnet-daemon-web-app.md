---
title: Een multi tenant-daemon bouwen die gebruikmaakt van het micro soft Identity platform-eind punt
description: In deze zelf studie leert u hoe u een ASP.NET-Web-API aanroept die wordt beveiligd door Azure Active Directory van een Windows Desktop-toepassing (WPF). De WPF-client verifieert een gebruiker, vraagt een toegangs token aan en roept de Web-API aan.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 12/10/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.openlocfilehash: b9c40d93c48bcf5959b5d9651510ce6076eb789e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82201751"
---
# <a name="tutorial-build-a-multitenant-daemon-that-uses-the-microsoft-identity-platform-endpoint"></a>Zelf studie: een multi tenant-daemon bouwen die gebruikmaakt van het micro soft Identity platform-eind punt

In deze zelf studie leert u hoe u het micro soft-identiteits platform kunt gebruiken voor toegang tot de gegevens van micro soft-zakelijke klanten in een langlopend, niet-interactief proces. De voor beeld-daemon gebruikt de referenties van de [OAuth2-client](v2-oauth2-client-creds-grant-flow.md) om een toegangs token te verkrijgen. De daemon gebruikt vervolgens het token om [Microsoft Graph](https://graph.microsoft.io) te roepen en toegang te krijgen tot de organisatie gegevens.

> [!div class="checklist"]
> * Een daemon-app integreren met het micro soft Identity-platform
> * Toepassings machtigingen rechtstreeks aan de app toekennen door een beheerder
> * Een toegangs Token ophalen om de Microsoft Graph-API aan te roepen
> * Roep de Microsoft Graph-API aan.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

De app is gebouwd als een ASP.NET MVC-toepassing. Het maakt gebruik van de OWIN OpenID Connect Connect middleware om gebruikers aan te melden.

Het onderdeel daemon in dit voor beeld is een API- `SyncController.cs`controller. Wanneer de controller wordt aangeroepen, wordt deze opgehaald in een lijst met gebruikers in de Tenant van de Azure Active Directory van de klant (Azure AD) van Microsoft Graph. `SyncController.cs`wordt geactiveerd door een AJAX-aanroep in de webtoepassing. Er wordt gebruikgemaakt [van de micro soft Authentication Library (MSAL) voor .net](msal-overview.md) om een toegangs token voor Microsoft Graph te verkrijgen.

>[!NOTE]
> Als u geen ervaring hebt met het micro soft-identiteits platform, raden we u aan om te beginnen met de [Snelstartgids voor .net core daemon](quickstart-v2-netcore-daemon.md).

## <a name="scenario"></a>Scenario

Omdat de app een multi tenant-app voor zakelijke klanten van micro soft is, moet deze een manier bieden waarop klanten zich kunnen aanmelden of de toepassing met hun bedrijfs gegevens kunnen verbinden. Tijdens de verbindings stroom verleent een bedrijfs beheerder eerst *toepassings machtigingen* rechtstreeks aan de app, zodat deze toegang heeft tot Bedrijfs gegevens op een niet-interactieve manier zonder de aanwezigheid van een aangemelde gebruiker. Het meren deel van de logica in dit voor beeld laat zien hoe deze verbindings stroom kan worden gerealiseerd met behulp van het [afstemmings](v2-permissions-and-consent.md#using-the-admin-consent-endpoint) eindpunt van de beheerder van het identiteits platform.

![Topologie](./media/tutorial-v2-aspnet-daemon-webapp/topology.png)

Lees voor meer informatie over de concepten die in dit voor beeld worden gebruikt, de documentatie over het [client referentie protocol voor het eind punt van het identiteits platform](v2-oauth2-client-creds-grant-flow.md).

## <a name="prerequisites"></a>Vereisten

Als u het voor beeld in deze Quick Start wilt uitvoeren, hebt u het volgende nodig:

- [Visual Studio 2017 of 2019](https://visualstudio.microsoft.com/downloads/).
- Een Azure AD-tenant. Zie [een Azure AD-Tenant verkrijgen](quickstart-create-new-tenant.md)voor meer informatie.
- Een of meer gebruikers accounts in uw Azure AD-Tenant. Dit voor beeld werkt niet met een Microsoft-account (voorheen Windows Live-account). Als u bent aangemeld bij de [Azure Portal](https://portal.azure.com) met een Microsoft-account en u hebt nooit een gebruikers account in uw directory gemaakt, moet u dat nu doen.

## <a name="clone-or-download-this-repository"></a>Klonen of download deze opslagplaats

Voer de volgende opdracht uit vanaf de shell of de opdracht regel:

```Shell
git clone https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2.git
```

Of [down load het voor beeld in een zip-bestand](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/archive/master.zip).

## <a name="register-your-application"></a>Uw toepassing registreren

Dit voor beeld heeft één project. Als u de toepassing wilt registreren bij uw Azure AD-Tenant, kunt u het volgende doen:

- Volg de stappen in [het voor beeld registreren bij uw Azure Active Directory-Tenant](#register-your-application) en [het voor beeld configureren voor het gebruik van uw Azure AD-Tenant](#choose-the-azure-ad-tenant).
- Gebruik Power shell-scripts die:
  - Maak *automatisch* de Azure AD-toepassingen en gerelateerde objecten (wacht woorden, machtigingen, afhankelijkheden) voor u.
  - Wijzig de configuratie bestanden van het Visual Studio-project.

Als u de automatisering wilt gebruiken:

1. Voer Power shell uit in Windows en ga naar de hoofdmap van de gekloonde map.
1. Voer deze opdracht uit:

   ```PowerShell
   Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
   ```

1. Voer het script uit om uw Azure AD-toepassing te maken en de code van de voorbeeld toepassing dienovereenkomstig te configureren:

   ```PowerShell
   .\AppCreationScripts\Configure.ps1
   ```

   Andere manieren om scripts uit te voeren, worden beschreven in scripts voor het [maken van apps](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/blob/master/AppCreationScripts/AppCreationScripts.md).

1. Open de Visual Studio-oplossing en selecteer **starten** om de code uit te voeren.

Als u de automatisering niet wilt gebruiken, volgt u de stappen in de volgende secties.

### <a name="choose-the-azure-ad-tenant"></a>De Azure AD-Tenant kiezen

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Als uw account zich in meer dan één Azure AD-Tenant bevindt, selecteert u uw profiel in het menu boven aan de pagina en selecteert u vervolgens **map activeren**.
1. Wijzig uw portal sessie in de gewenste Azure AD-Tenant.

### <a name="register-the-client-app-dotnet-web-daemon-v2"></a>De client-app registreren (DotNet-Web-daemon-v2)

1. Ga naar de pagina [app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) in het micro soft Identity-platform voor ontwikkel aars.
1. Selecteer **nieuwe registratie**.
1. Wanneer de pagina **Een toepassing registreren** verschijnt, voert u de registratiegegevens van de toepassing in:
   - Voer in de sectie **naam** een zinvolle toepassings naam in die wordt weer gegeven voor gebruikers van de app. Voer bijvoorbeeld **DotNet-Web-daemon-v2**in.
   - Selecteer in de sectie **ondersteunde account typen** de optie **accounts in elke organisatie Directory**.
   - Selecteer in de sectie de **omleidings-URI (optioneel)** **Web** in de keuze lijst met invoervak en voer de volgende omleidings-uri's in:
       - **https://localhost:44316/**
       - **https://localhost:44316/Account/GrantPermissions**

     Als er meer dan twee omleidings-Uri's zijn, moet u deze later toevoegen **nadat de app** is gemaakt.
1. Selecteer **Registreren** om de toepassing te maken.
1. Zoek op de **overzichts** pagina van de app naar de waarde van de **toepassings-id** en noteer deze voor later. U hebt deze nodig voor het configureren van het Visual Studio-configuratie bestand voor dit project.
1. Selecteer in de lijst met pagina’s voor de app de optie **Verificatie**. Daarna kunt u het volgende doen:
   - Stel in het gedeelte **Geavanceerde instellingen** de **afmeldings-URL** in op **https://localhost:44316/Account/EndSession**.
   - Selecteer **toegangs tokens** en **id-tokens**in de sectie **Geavanceerde instellingen** > **impliciete toekenning** . Voor dit voor beeld moet de [impliciete toekennings stroom](v2-oauth2-implicit-grant-flow.md) zijn ingeschakeld om de gebruiker aan te melden en een API aan te roepen.
1. Selecteer **Opslaan**.
1. Selecteer op de pagina **certificaten & geheimen** in de sectie **client geheimen** de optie **Nieuw client geheim**. Daarna kunt u het volgende doen:

   1. Voer een beschrijving van de sleutel in (bijvoorbeeld **app-geheim**),
   1. Selecteer een sleutel duur van ofwel **in één jaar**, **in twee jaar**of **nooit verloopt**.
   1. Selecteer de knop **Add**.
   1. Wanneer de sleutel waarde wordt weer gegeven, kopieert u deze op een veilige locatie en slaat u deze op. U hebt deze sleutel later nodig voor het configureren van het project in Visual Studio. Het wordt niet meer weer gegeven of kan op een andere manier worden opgehaald.
1. Selecteer in de lijst met pagina's voor de app **API-machtigingen**. Daarna kunt u het volgende doen:
   1. Selecteer de knop **Een machtiging toevoegen**.
   1. Zorg ervoor dat het tabblad **micro soft-api's** is geselecteerd.
   1. Selecteer **Microsoft Graph**in de sectie **veelgebruikte micro soft-api's** .
   1. Controleer in de sectie **toepassings machtigingen** of de juiste machtigingen zijn geselecteerd: **gebruiker. Read. all**.
   1. Selecteer de knop **machtigingen toevoegen** .

## <a name="configure-the-sample-to-use-your-azure-ad-tenant"></a>Het voor beeld configureren voor het gebruik van uw Azure AD-Tenant

In de volgende stappen is **ClientID** hetzelfde als ' toepassings-id ' of **AppId**.

Open de oplossing in Visual Studio om de projecten te configureren.

### <a name="configure-the-client-project"></a>Het client project configureren

Als u de installatie scripts hebt gebruikt, zijn de volgende wijzigingen voor u toegepast.

1. Open het **UserSync\Web.config** -bestand.
1. Zoek de app-sleutel **IDA: ClientId**. Vervang de bestaande waarde door de toepassings-ID van de **DotNet-Web-daemon-v2-** toepassing die is gekopieerd van de Azure Portal.
1. Zoek de app-sleutel **IDA: ClientSecret**. Vervang de bestaande waarde door de sleutel die u hebt opgeslagen tijdens het maken van de **DotNet-Web-daemon-v2-** app in de Azure Portal.

## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

Reinig de oplossing, bouw de oplossing opnieuw op, voer de toepassing UserSync uit en meld u aan als beheerder in uw Azure AD-Tenant. Als u geen Azure AD-Tenant voor het testen hebt, kunt u [deze instructies volgen](quickstart-create-new-tenant.md) om er een te krijgen.

Wanneer u zich aanmeldt, vraagt de app u eerst om toestemming om u aan te melden en uw gebruikers profiel te lezen. Met deze toestemming kan de app ervoor zorgen dat u een zakelijke gebruiker bent.

![Toestemming van de gebruiker](./media/tutorial-v2-aspnet-daemon-webapp/firstconsent.png)

De app probeert vervolgens via Microsoft Graph een lijst met gebruikers te synchroniseren vanuit uw Azure AD-Tenant. Als dat niet het geval is, wordt u (de Tenant beheerder) gevraagd om uw Tenant met de app te verbinden.

De app vraagt vervolgens om toestemming om de lijst met gebruikers in uw Tenant te lezen.

![toestemming van de beheerder](./media/tutorial-v2-aspnet-daemon-webapp/adminconsent.PNG)

Nadat u toestemming hebt verleend, bent u afgemeld bij de app. Deze afmelding zorgt ervoor dat alle bestaande toegangs tokens voor Microsoft Graph uit de token cache worden verwijderd. Wanneer u zich opnieuw aanmeldt, heeft het nieuwe token dat is verkregen de benodigde machtigingen om Microsoft Graph te maken.


Wanneer u de machtiging verleent, kan de app op elk gewenst moment een query voor gebruikers uitvoeren. U kunt dit controleren door de knop **gebruikers synchroniseren** te selecteren en de lijst met gebruikers te vernieuwen. Probeer een gebruiker toe te voegen of te verwijderen en de lijst opnieuw te synchroniseren. (Maar houd er rekening mee dat de app alleen de eerste pagina van gebruikers synchroniseert.)

## <a name="about-the-code"></a>Over de code

De relevante code voor dit voor beeld bevindt zich in de volgende bestanden:

- **App_Start \Startup.auth.cs**, **Controllers\AccountController.cs**: eerste aanmelding. Met name de acties op de controller hebben een kenmerk **autoriseren** , waardoor de gebruiker zich kan aanmelden. De toepassing gebruikt de [autorisatie code stroom](v2-oauth2-auth-code-flow.md) om u aan te melden bij de gebruiker.
- **Controllers\SyncController.cs**: de lijst met gebruikers wordt gesynchroniseerd met de lokale opslag in het geheugen.
- **Controllers\UserController.cs**: de lijst met gebruikers wordt weer gegeven in de lokale opslag in het geheugen.
- **Controllers\AccountController.cs**: de machtigingen van de Tenant beheerder ophalen met behulp van het eind punt voor de beheerder.

## <a name="re-create-the-sample-app"></a>De voor beeld-app opnieuw maken

1. Maak in Visual Studio een nieuw **Visual C#** **ASP.net Web Application (.NET Framework)-** project.
1. Kies in het volgende scherm de **MVC** -project sjabloon. Voeg ook map-en kern verwijzingen voor **Web-API**toe, omdat u later een web-API-controller gaat toevoegen. De gekozen verificatie modus van het project als standaard laten: **geen verificatie**.
1. Selecteer het project in het **Solution Explorer** venster en selecteer de toets **F4** .
1. Stel in de project eigenschappen **SSL ingeschakeld** in op **waar**. Noteer de informatie in **SSL-URL**. U hebt deze nodig bij het configureren van de registratie van deze toepassing in de Azure Portal.
1. Voeg de volgende ASP.NET OWIN middleware NuGet-pakketten toe:
   - Micro soft. Owin. Security. ActiveDirectory
   - Microsoft.Owin.Security.Cookies
   - Microsoft.Owin.Host.SystemWeb
   - Micro soft. Identity model. Protocol. Extensions
   - Microsoft.Owin.Security.OpenIdConnect
   - Micro soft. Identity. client
1. In de map **App_Start** :
   1. Maak een klasse met de naam **Startup.auth.cs**.
   1. Verwijderen **. App_Start** uit de naam van de naam ruimte.
   1. Vervang de code voor de klasse **Startup** door de code uit hetzelfde bestand van de voor beeld-app.
   Zorg ervoor dat u de volledige klassedefinitie uitvoert. De definitie wordt gewijzigd van het opstarten van de **open bare klasse** naar een **open bare gedeeltelijke klasse.**
1. In **Startup.auth.cs**kunt u ontbrekende verwijzingen oplossen door **using** instructies toe te voegen zoals voorgesteld door Visual Studio IntelliSense.
1. Klik met de rechter muisknop op het project, selecteer **toevoegen**en selecteer vervolgens **klasse**.
1. Typ **OWIN**in het zoekvak. De **opstart klasse OWIN** wordt weer gegeven als een selectie. Selecteer de klasse en geef deze de naam **Startup.cs**.
1. Vervang in **Startup.cs**de code voor de klasse **Startup** door de code uit hetzelfde bestand van de voor beeld-app. U ziet dat de definitie van het opstarten van de **open bare klasse** wordt gewijzigd in een **open bare gedeeltelijke klasse**.
1. Voeg in de map **modellen** een nieuwe klasse toe met de naam **MsGraphUser.cs**. Vervang de implementatie door de inhoud van het bestand met dezelfde naam uit het voor beeld.
1. Voeg een nieuwe **MVC 5-controller-lege instantie met** de naam **AccountController**. Vervang de implementatie door de inhoud van het bestand met dezelfde naam uit het voor beeld.
1. Voeg een nieuwe **MVC 5-controller-lege instantie met** de naam **UserController**. Vervang de implementatie door de inhoud van het bestand met dezelfde naam uit het voor beeld.
1. Een nieuwe **Web-API 2-controller toevoegen-leeg exemplaar met** de naam **SyncController**. Vervang de implementatie door de inhoud van het bestand met dezelfde naam uit het voor beeld.
1. Voor de gebruikers interface, in de map **Views\Account** , voegt u **drie lege (zonder model) weer gaven** -instanties met de naam **GrantPermissions**, **index**en **UserMismatch**. Voeg een benoemde **index** toe aan de map **Views\User** . Vervang de implementatie door de inhoud van het bestand met dezelfde naam uit het voor beeld.
1. Werk **de\_gedeelde layout. cshtml** en **Home\Index.cshtml** bij om de verschillende weer gaven op de juiste manier te koppelen.

## <a name="deploy-the-sample-to-azure"></a>Het voor beeld implementeren in azure

Dit project bevat Web-app-en Web-API-projecten. Als u deze wilt implementeren op Azure websites, voert u de volgende stappen uit:

1. Maak een Azure-website.
1. Publiceer de web-app en Web-Api's op de website.
1. Werk clients bij om de website aan te roepen in plaats van IIS Express.

### <a name="create-and-publish-dotnet-web-daemon-v2-to-an-azure-website"></a>DotNet-Web-daemon-v2 maken en publiceren naar een Azure-website

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer **Een resource maken** in de linkerbovenhoek.
1. Selecteer **Web** > -**Web-app**en geef uw website een naam. Noem het bijvoorbeeld **DotNet-Web-daemon-v2-contoso.azurewebsites.net**.
1. Selecteer de gegevens voor het **abonnement**, de **resource groep**en het **app service-plan en de locatie**. Het **besturings systeem** is **Windows**en de **publicatie** is **code**.
1. Selecteer **maken** en wacht totdat de app service is gemaakt.
1. Wanneer u de melding **implementatie geslaagd** krijgt, selecteert u **naar resource gaan** om naar de zojuist gemaakte app-service te gaan.
1. Nadat de website is gemaakt, vindt u deze in het **dash board** en selecteert u deze om het **overzichts** scherm van de app-service te openen.
1. Op het tabblad **overzicht** van de app service downloadt u het publicatie profiel door de koppeling **publicatie profiel ophalen** te selecteren en op te slaan. U kunt andere implementatie mechanismen gebruiken, zoals implementeren vanuit broncode beheer.
1. Ga naar Visual Studio en klik vervolgens:
   1. Ga naar het **DotNet-Web-daemon-v2-** project.
   1. Klik met de rechter muisknop op het project in Solution Explorer en selecteer vervolgens **publiceren**.
   1. Selecteer **profiel importeren** op de onderste balk en importeer het publicatie profiel dat u eerder hebt gedownload.
1. Selecteer **Configureren**.
1. Werk op het tabblad **verbinding** de doel-URL bij, zodat deze ' https ' gebruikt. Gebruik bijvoorbeeld `https://dotnet-web-daemon-v2-contoso.azurewebsites.net`. Selecteer **Next**.
1. Zorg ervoor dat **organisatie-verificatie inschakelen** is uitgeschakeld op het tabblad **instellingen** .
1. Selecteer **Opslaan**. Selecteer **publiceren** op het hoofd scherm.

Visual Studio publiceert het project en opent automatisch een browser naar de URL van het project. Als u de standaard webpagina van het project ziet, is de publicatie geslaagd.

### <a name="update-the-azure-ad-tenant-application-registration-for-dotnet-web-daemon-v2"></a>De registratie van de Azure AD-Tenant toepassing bijwerken voor dotnet-Web-daemon-v2

1. Ga terug naar [Azure Portal](https://portal.azure.com).
1. Selecteer in het linkerdeel venster de **Azure Active Directory** -service en selecteer vervolgens **app-registraties**.
1. Selecteer de toepassing **DotNet-Web-daemon-v2** .
1. Werk op de pagina **verificatie** voor uw toepassing de velden voor de **afmeldings-URL** bij met het adres van uw service. Gebruik [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net)bijvoorbeeld.
1. Werk in het **huisstijl** menu de URL van de **Start pagina** bij naar het adres van uw service. Gebruik [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net)bijvoorbeeld.
1. Sla de configuratie op.
1. Voeg dezelfde URL toe in de lijst met waarden van het menu uri's voor het**omleiden** van **authenticatie** > . Als u meerdere omleidings-Url's hebt, moet u ervoor zorgen dat er een nieuwe vermelding is die de URI van de app service gebruikt voor elke omleidings-URL.

## <a name="clean-up-resources"></a>Resources opschonen
Wanneer u deze niet meer nodig hebt, verwijdert u het app-object dat u hebt gemaakt in de stap [uw toepassing registreren](#register-your-application) .  Als u de toepassing wilt verwijderen, volgt u de instructies in [een toepassing verwijderen die door u of uw organisatie is gemaakt](quickstart-remove-app.md#remove-an-application-authored-by-you-or-your-organization).

## <a name="get-help"></a>Help opvragen

Gebruik [stack overflow](http://stackoverflow.com/questions/tagged/msal) om ondersteuning van de community te verkrijgen.
Stel eerst uw vragen over Stack Overflow en blader door bestaande problemen om te zien of iemand uw vraag eerder heeft gesteld.
Zorg ervoor dat uw vragen of opmerkingen zijn gelabeld met ' adal ', ' msal ' en ' DotNet '.

Als u een bug vindt in het voor beeld, verhoogt u het probleem op [github-problemen](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/issues).

Als u een bug vindt in MSAL.NET, verhoogt u het probleem op [MSAL.net github-problemen](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues).

Ga naar de [pagina gebruikers spraak](https://feedback.azure.com/forums/169401-azure-active-directory)om een aanbeveling te geven.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over de verschillende [verificatie stromen en toepassings scenario's](authentication-flows-app-scenarios.md) die door het micro soft-identiteits platform worden ondersteund.

Zie de volgende conceptuele documentatie voor meer informatie:

- [Pacht in Azure Active Directory](single-and-multi-tenant-apps.md)
- [Inzicht in ervaringen met Azure AD-toepassingtoestemming](application-consent-experience.md)
- [Meld u aan bij een Azure Active Directory gebruiker met het toepassings patroon multi tenant](howto-convert-app-to-be-multi-tenant.md)
- [Toestemming van gebruiker en beheerder](howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent)
- [Toepassings- en service-principal-objecten in Azure Active Directory](app-objects-and-service-principals.md)
- [Snelstartgids: een toepassing registreren bij het micro soft Identity-platform](quickstart-register-app.md)
- [Snelstartgids: een client toepassing configureren voor toegang tot Web-Api's](quickstart-configure-app-access-web-apis.md)
- [Een token voor een toepassing met client referentie stromen ophalen](msal-client-applications.md)

Zie de [Snelstartgids voor .net core daemon](quickstart-v2-netcore-daemon.md)voor een eenvoudiger multi tenant console daemon-toepassing.
