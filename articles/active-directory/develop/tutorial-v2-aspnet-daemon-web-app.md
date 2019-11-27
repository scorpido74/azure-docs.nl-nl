---
title: Een ASP.NET-Web-API aanroepen die wordt beveiligd door Azure AD-micro soft Identity platform
description: In deze Quick Start leert u hoe u een ASP.NET-Web-API aanroept die wordt beveiligd door Azure Active Directory van een Windows Desktop-toepassing (WPF). De WPF-client verifieert een gebruiker, vraagt een toegangs token aan en roept de Web-API aan.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.collection: M365-identity-device-management
ms.openlocfilehash: c558d45702498e6c1164d7ee1731e80ff195349e
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74328414"
---
# <a name="build-a-multi-tenant-daemon-with-the-microsoft-identity-platform-endpoint"></a>Een multi tenant-daemon bouwen met het micro soft Identity platform-eind punt

In deze zelf studie leert u hoe u het micro soft-identiteits platform kunt gebruiken voor toegang tot de gegevens van micro soft-zakelijke klanten in een langlopend, niet-interactief proces. De voor beeld-daemon gebruikt de referenties van de [OAuth2-client](v2-oauth2-client-creds-grant-flow.md) om een toegangs token te verkrijgen. dit wordt vervolgens gebruikt om de [Microsoft Graph](https://graph.microsoft.io) aan te roepen en de organisatie gegevens te openen.

De app is gebouwd als een ASP.NET MVC-toepassing en maakt gebruik van de OWIN OpenID Connect Connect middleware om gebruikers aan te melden.  Het "daemon"-onderdeel in dit voor beeld is een API-controller, die, wanneer aangeroepen, een lijst met gebruikers in de Azure AD-Tenant van de klant van Microsoft Graph ophaalt.  Deze `SyncController.cs` wordt geactiveerd door een AJAX-aanroep in de webtoepassing en gebruikt de [micro soft Authentication Library (MSAL) voor .net](msal-overview.md) om een toegangs token voor Microsoft Graph te verkrijgen.

Raadpleeg de [Snelstartgids voor .net core daemon](quickstart-v2-netcore-daemon.md)voor een eenvoudiger console daemon-toepassing.

## <a name="scenario"></a>Scenario

Omdat de app een multi tenant-app is die is bedoeld voor gebruik door een micro soft Business-klant, moet deze een manier bieden waarop klanten zich kunnen aanmelden of de toepassing met hun bedrijfs gegevens kunnen verbinden.  Tijdens de verbindings stroom verleent een bedrijfs beheerder eerst **toepassings machtigingen** rechtstreeks aan de app, zodat deze toegang heeft tot Bedrijfs gegevens op een niet-interactieve manier zonder de aanwezigheid van een aangemelde gebruiker.  Het meren deel van de logica in dit voor beeld laat zien hoe u deze verbindings stroom kunt gebruiken met het toestemming-eind punt voor het identiteits platform [beheerder](v2-permissions-and-consent.md#using-the-admin-consent-endpoint) .

![Topologie](./media/tutorial-v2-aspnet-daemon-webapp/topology.png)

Voor meer informatie over de concepten die in dit voor beeld worden gebruikt, raadpleegt u de [documentatie voor Identity platform endpoint client credentials protocol](v2-oauth2-client-creds-grant-flow.md).

## <a name="prerequisites"></a>Vereisten

Als u het voor beeld in deze Quick Start wilt uitvoeren, hebt u het volgende nodig:

- [Visual Studio 2017 of 2019](https://visualstudio.microsoft.com/downloads/)
- Een Azure Active Directory-Tenant (Azure AD). Zie [een Azure AD-Tenant verkrijgen](quickstart-create-new-tenant.md) voor meer informatie over het verkrijgen van een Azure AD-Tenant
- Een of meer gebruikers accounts in uw Azure AD-Tenant. Dit voor beeld werkt niet met een Microsoft-account (voorheen Windows Live-account). Daarom moet u dit nu doen als u bent aangemeld bij de [Azure Portal](https://portal.azure.com) met een Microsoft-account en nog nooit een gebruikers account in uw adres lijst hebt gemaakt.

## <a name="clone-or-download-this-repository"></a>Deze opslag plaats klonen of downloaden

Vanuit de shell of de opdracht regel:

```Shell
git clone https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2.git
```

Of [down load het voor beeld in een zip-bestand](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/archive/master.zip).

## <a name="register-the-sample-application-with-your-azure-ad-tenant"></a>De voorbeeld toepassing registreren bij uw Azure AD-Tenant

Er bevindt zich één project in dit voor beeld. Als u dit wilt registreren, kunt u het volgende doen:

- Volg de stappen om [het voor beeld te registreren bij uw Azure Active Directory-Tenant](#register-the-sample-application-with-your-azure-ad-tenant) en [het voor beeld te configureren voor het gebruik van uw Azure AD-Tenant](#choose-the-azure-ad-tenant-for-the-applications)
- Of gebruik Power shell-scripts die:
  - Maakt **automatisch** de Azure AD-toepassingen en gerelateerde objecten (wacht woorden, machtigingen, afhankelijkheden) voor u
  - Wijzig de configuratie bestanden van het Visual Studio-project.

Als u deze automatisering wilt gebruiken:

1. In Windows, voert u Power shell uit en navigeert u naar de hoofdmap van de gekloonde map.
1. In Power shell uitvoeren:

   ```PowerShell
   Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
   ```

1. Voer het script uit om uw Azure AD-toepassing te maken en de code van de voorbeeld toepassing dienovereenkomstig te configureren.
1. In Power shell uitvoeren:

   ```PowerShell
   .\AppCreationScripts\Configure.ps1
   ```

   > Andere manieren om de scripts uit te voeren, worden beschreven in scripts voor het [maken van apps](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/blob/master/AppCreationScripts/AppCreationScripts.md)

1. Open de Visual Studio-oplossing en klik op Start om de code uit te voeren.

Als u deze automatisering niet wilt gebruiken, volgt u de onderstaande stappen.

### <a name="choose-the-azure-ad-tenant-for-the-applications"></a>De Azure AD-Tenant voor de toepassingen kiezen

Als eerste stap moet u het volgende doen:

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Als uw account zich in meer dan één Azure AD-Tenant bevindt, selecteert u uw profiel in de rechter bovenhoek in het menu boven aan de pagina en **gaat u naar de map**.
   Wijzig uw portal sessie in de gewenste Azure AD-Tenant.

### <a name="register-the-client-app-dotnet-web-daemon-v2"></a>De client-app registreren (DotNet-Web-daemon-v2)

1. Navigeer naar de pagina micro soft-identiteits platform voor ontwikkel aars [app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) .
1. Selecteer **nieuwe registratie**.
1. Wanneer de pagina **Een toepassing registreren** verschijnt, voert u de registratiegegevens van de toepassing in:
   - Voer in de sectie **Naam** een beschrijvende toepassingsnaam in die zichtbaar is voor gebruikers van de app. Bijvoorbeeld: `dotnet-web-daemon-v2`.
   - Selecteer in de sectie **ondersteunde account typen** de optie **accounts in elke organisatie Directory**.
   - Selecteer in de sectie de omleidings-URI (optioneel) **Web** in de keuze lijst met invoervak en voer de volgende omleidings-uri's in:
       - `https://localhost:44316/`
       - `https://localhost:44316/Account/GrantPermissions` als er meer dan een omleidings-Uri's zijn, moet u ze later toevoegen vanaf het tabblad **verificatie** nadat de app is gemaakt.
1. Selecteer **Registreren** om de toepassing te maken.
1. Zoek op de pagina app- **overzicht** de waarde van de **toepassing (client)** en noteer deze voor later. U hebt deze nodig voor het configureren van het Visual Studio-configuratie bestand voor dit project.
1. Selecteer in de lijst met pagina’s voor de app de optie **Verificatie**.
   - Stel in het gedeelte **Geavanceerde instellingen** de **afmeldings-URL** in op `https://localhost:44316/Account/EndSession`
   - Controleer in de sectie **Geavanceerde instellingen** | **impliciete toekenning** de **toegangs tokens** en **id-tokens** als voor dit voor beeld moet de [impliciete toekennings stroom](v2-oauth2-implicit-grant-flow.md) zijn ingeschakeld om u aan te melden bij de gebruiker en een API aan te roepen.
1. Selecteer **Opslaan**.
1. Kies op de pagina **certificaten & geheimen** in het gedeelte **client geheimen** de optie **Nieuw client geheim**:

   - Typ een beschrijving van de sleutel (bijvoorbeeld `app secret`),
   - Selecteer een sleutel duur van ofwel **in één jaar**, **in twee jaar**of **nooit verloopt**.
   - Wanneer u op de knop **toevoegen** klikt, wordt de waarde van de sleutel weer gegeven, gekopieerd en opgeslagen op een veilige locatie.
   - U hebt deze sleutel later nodig voor het configureren van het project in Visual Studio. Deze sleutel waarde wordt niet opnieuw weer gegeven en kan niet worden opgehaald op een andere manier, dus noteer deze zo snel als deze wordt weer gegeven in de Azure Portal.
1. Selecteer in de lijst met pagina's voor de app **API-machtigingen**
   - Klik op de knop **een machtiging toevoegen** en vervolgens op
   - Zorg ervoor dat het tabblad **micro soft-api's** is geselecteerd
   - Klik in de sectie *veelgebruikte micro soft-api's* op **Microsoft Graph**
   - Controleer in de sectie **toepassings machtigingen** of de juiste machtigingen zijn ingeschakeld: **gebruiker. Read. all**
   - Selecteer de knop **machtigingen toevoegen**

## <a name="configure-the-sample-to-use-your-azure-ad-tenant"></a>Het voor beeld configureren voor het gebruik van uw Azure AD-Tenant

In de onderstaande stappen is ClientID hetzelfde als ' toepassings-ID ' of ' AppId '.

Open de oplossing in Visual Studio om de projecten te configureren

### <a name="configure-the-client-project"></a>Het client project configureren

Als u de installatie scripts hebt gebruikt, zijn de volgende wijzigingen voor u toegepast.

1. Het `UserSync\Web.Config` bestand openen
1. Zoek de app-sleutel `ida:ClientId` en vervang de bestaande waarde door de Application ID (clientId) van de `dotnet-web-daemon-v2` toepassing die is gekopieerd van de Azure Portal.
1. Zoek de app-sleutel `ida:ClientSecret` en vervang de bestaande waarde door de sleutel die u hebt opgeslagen tijdens het maken van de `dotnet-web-daemon-v2`-app in de Azure Portal.

## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

Reinig de oplossing, bouw de oplossing opnieuw op en voer de toepassing UserSync uit en meld u aan als beheerder in uw Azure AD-Tenant.  Als u geen Azure AD-Tenant voor het testen hebt, kunt u [deze instructies volgen](quickstart-create-new-tenant.md) om er een te krijgen.

Wanneer u zich aanmeldt, vraagt de app u eerst om toestemming om u aan te melden & uw gebruikers profiel te lezen.  Met deze toestemming kan de toepassing ervoor zorgen dat u een zakelijke gebruiker bent.

![Toestemming van de gebruiker](./media/tutorial-v2-aspnet-daemon-webapp/firstconsent.png)

De toepassing probeert vervolgens via de Microsoft Graph een lijst met gebruikers te synchroniseren vanuit uw Azure AD-Tenant.  Als dat niet het geval is, wordt u (de Tenant beheerder) gevraagd om uw Tenant met de toepassing te verbinden.

De toepassing wordt vervolgens gevraagd om toestemming te geven voor het lezen van de lijst met gebruikers in uw Tenant.

![Beheerder toestemming](./media/tutorial-v2-aspnet-daemon-webapp/adminconsent.PNG)

**Nadat u toestemming hebt verleend, wordt u afgemeld bij de app**. Dit wordt gedaan om ervoor te zorgen dat alle bestaande toegangs tokens voor Graph uit de token cache worden verwijderd. Als u zich opnieuw aanmeldt, beschikt het nieuwe token over de benodigde machtigingen om naar MS Graph te kunnen aanroepen.
Wanneer u de machtiging verleent, kan de toepassing op elk gewenst moment een query voor gebruikers uitvoeren.  U kunt dit controleren door te klikken op de knop **gebruikers synchroniseren** op de pagina gebruikers, en de lijst met gebruikers vernieuwen.  Probeer een gebruiker toe te voegen of te verwijderen en de lijst opnieuw te synchroniseren (maar houd er rekening mee dat deze alleen de eerste pagina met gebruikers synchroniseert!).

## <a name="about-the-code"></a>Over de code

De relevante code voor dit voor beeld bevindt zich in de volgende bestanden:

- Eerste aanmelding: `App_Start\Startup.Auth.cs`, `Controllers\AccountController.cs`.  Met name de acties op de controller hebben een kenmerk autoriseren, waardoor de gebruiker zich kan aanmelden. De toepassing gebruikt de [autorisatie code stroom](v2-oauth2-auth-code-flow.md) om u aan te melden bij de gebruiker.
- De lijst met gebruikers synchroniseren met de lokale in-Memory Store: `Controllers\SyncController.cs`
- De lijst met gebruikers uit de lokale opslag in het geheugen weer geven: `Controllers\UserController.cs`
- Machtigingen van de Tenant beheerder ophalen met behulp van het uitstemmings eindpunt van de beheerder: `Controllers\AccountController.cs`

## <a name="recreate-this-sample-app"></a>Deze voor beeld-app opnieuw maken

1. Maak in Visual Studio een nieuw `Visual C#` `ASP.NET Web Application (.NET Framework)` project. Kies in het volgende scherm de sjabloon `MVC` project. Voeg ook map-en kern verwijzingen voor `Web API` toe, omdat u later een web-API-controller toevoegt.  De gekozen verificatie modus van het project behouden als standaard, dat wil zeggen, `No Authentication`".
2. Selecteer het project in het **Solution Explorer** venster en druk op de toets **F4** om project eigenschappen te halen. Stel in de project eigenschappen in dat **SSL is ingeschakeld** voor `True`. Noteer de **SSL-URL**. U hebt deze nodig bij het configureren van de registratie van deze toepassing in de Azure Portal.
3. Voeg de volgende ASP.Net OWIN middleware NuGets: `Microsoft.Owin.Security.ActiveDirectory`, `Microsoft.Owin.Security.Cookies` en `Microsoft.Owin.Host.SystemWeb`, `Microsoft.IdentityModel.Protocol.Extensions`, `Microsoft.Owin.Security.OpenIdConnect` en `Microsoft.Identity.Client`toe. 
4. Maak in de map `App_Start` een klasse `Startup.Auth.cs`. Verwijder `.App_Start` uit de naam van de naam ruimte.  Vervang de code voor de klasse `Startup` door de code uit hetzelfde bestand van de voor beeld-app.  Zorg ervoor dat u de volledige klassedefinitie uitvoert.  De definitie wordt gewijzigd van `public class Startup` naar `public partial class Startup`
5. In `Startup.Auth.cs` ontbrekende verwijzingen oplossen door `using`-instructies toe te voegen zoals voorgesteld door Visual Studio IntelliSense.
6. Klik met de rechter muisknop op het project, selecteer toevoegen, selecteer klasse en voer in het zoekvak ' OWIN ' in.  ' OWIN-opstart klasse ' wordt weer gegeven als een selectie. Selecteer de klasse en geef deze de naam `Startup.cs`.
7. Vervang in `Startup.cs`de code voor de klasse `Startup` door de code uit hetzelfde bestand van de voor beeld-app.  Noteer de definitie wijzigingen van `public class Startup` naar `public partial class Startup`.
8. Voeg in de map een nieuwe klasse toe met de naam `MsGraphUser.cs`.  Vervang de implementatie door de inhoud van het bestand met dezelfde naam uit het voor beeld.
9. Voeg een nieuwe **MVC 5-controller toe met** de naam `AccountController`. Vervang de implementatie door de inhoud van het bestand met dezelfde naam uit het voor beeld.
10. Voeg een nieuwe **MVC 5-controller toe met** de naam `UserController`. Vervang de implementatie door de inhoud van het bestand met dezelfde naam uit het voor beeld.
11. Voeg een nieuwe **Web API 2-controller toe,** die `SyncController`wordt genoemd. Vervang de implementatie door de inhoud van het bestand met dezelfde naam uit het voor beeld.
12. Voor de gebruikers interface, in de map `Views\Account`, voegt u drie **lege (zonder model) weer gaven** toe met de naam `GrantPermissions`, `Index` en `UserMismatch`, en één met de naam `Index` in de map `Views\User`. Vervang de implementatie door de inhoud van het bestand met dezelfde naam uit het voor beeld.
13. Werk de `Shared\_Layout.cshtml` en `Home\Index.cshtml` bij om de verschillende weer gaven op de juiste wijze samen te koppelen.

## <a name="deploy-this-sample-to-azure"></a>Dit voor beeld implementeren in azure

Dit project heeft WebApp/Web API-projecten. Als u deze wilt implementeren op Azure websites, hebt u voor elke site het volgende nodig:

- een Azure-website maken
- Publiceer de web-app/Web-Api's op de website en
- de client (s) bijwerken voor het aanroepen van de website in plaats van IIS Express.

### <a name="create-and-publish-the-dotnet-web-daemon-v2-to-an-azure-web-site"></a>De `dotnet-web-daemon-v2` maken en publiceren op een Azure-website

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Klik op `Create a resource` in de linkerbovenhoek, selecteer **Web** - --> web- **app**en geef uw website een naam, bijvoorbeeld `dotnet-web-daemon-v2-contoso.azurewebsites.net`.
1. Vervolgens selecteert u de `Subscription`, `Resource Group``App service plan and Location`. `OS` worden **Windows** en `Publish` **code**.
1. Klik op `Create` en wacht totdat de App Service zijn gemaakt.
1. Zodra u de melding `Deployment succeeded` hebt ontvangen, klikt u op `Go to resource` om naar de zojuist gemaakte app-service te gaan.
1. Zodra de website is gemaakt, zoekt u deze in het **dash board** en klikt u erop om **app Services** **overzichts** scherm te openen.
1. Op het tabblad **overzicht** van de app service downloadt u het publicatie profiel door te klikken op de koppeling **publicatie profiel ophalen** en op te slaan.  Andere implementatie mechanismen, zoals van broncode beheer, kunnen ook worden gebruikt.
1. Schakel over naar Visual Studio en ga naar het DotNet-Web-daemon-v2-project.  Klik met de rechter muisknop op het project in de Solution Explorer en selecteer **publiceren**.  Klik op **profiel importeren** op de onderste balk en importeer het publicatie profiel dat u eerder hebt gedownload.
1. Klik op **configureren** en werk in het `Connection tab`de doel-URL bij, zodat deze een `https` is in de URL van de start pagina, bijvoorbeeld [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net). Klik op **Volgende**.
1. Zorg ervoor dat `Enable Organizational Authentication` niet is geselecteerd op het tabblad instellingen.  Klik op **Opslaan**. Klik op het hoofd scherm op **publiceren** .
1. Visual Studio publiceert het project en opent automatisch een browser naar de URL van het project.  Als u de standaard webpagina van het project ziet, is de publicatie geslaagd.

### <a name="update-the-azure-ad-tenant-application-registration-for-dotnet-web-daemon-v2"></a>De registratie van de Azure AD-Tenant toepassing bijwerken voor `dotnet-web-daemon-v2`

1. Ga terug naar de [Azure Portal](https://portal.azure.com).
Selecteer in het navigatie deel venster aan de linkerkant de **Azure Active Directory** -service en selecteer vervolgens **app-registraties**.
1. Selecteer in het scherm resultd de `dotnet-web-daemon-v2`-toepassing.
1. In de **verificatie** | werk de pagina voor de afmeldings-URL voor uw toepassing bij met het adres van uw service, bijvoorbeeld [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net)
1. Werk in het *huisstijl* menu de URL van de **Start pagina**bij naar het adres van uw service, bijvoorbeeld [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net). Sla de configuratie op.
1. Voeg dezelfde URL toe in de lijst met waarden van het menu *Authentication-> omleidings-uri's* . Als u meerdere omleidings-url's hebt, moet u ervoor zorgen dat er een nieuwe vermelding met de URI van de app service wordt gebruikt voor elke omleidings-URL.

## <a name="community-help-and-support"></a>Help en ondersteuning van de Community

Gebruik [stack overflow](http://stackoverflow.com/questions/tagged/msal) om ondersteuning van de community te verkrijgen.
Stel uw vragen op Stack Overflow eerst en blader door bestaande problemen om te zien of iemand uw vraag eerder heeft gesteld.
Zorg ervoor dat uw vragen of opmerkingen zijn gelabeld met [`adal` `msal` `dotnet`].

Als u het voor beeld vindt en er een fout optreedt, kunt u het probleem verhogen over [github-problemen](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/issues).

Als u een bug vindt in MSAL.NET, verhoogt u het probleem op [MSAL.net github-problemen](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues).

Ga naar de volgende [pagina met gebruikers spraak](https://feedback.azure.com/forums/169401-azure-active-directory)om een aanbeveling te geven.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over de verschillende [verificatie stromen en toepassings scenario's](authentication-flows-app-scenarios.md) die door het micro soft-identiteits platform worden ondersteund.

Zie de volgende conceptuele documentatie voor meer informatie:

- [Pacht in Azure Active Directory](single-and-multi-tenant-apps.md)
- [Inzicht in ervaringen met Azure AD-toepassingtoestemming](application-consent-experience.md)
- [Procedure: aanmelden bij een Azure Active Directory gebruiker met het toepassings patroon met meerdere tenants](howto-convert-app-to-be-multi-tenant.md)
- [Toestemming van gebruiker en beheerder](howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent)
- [Toepassings-en Service-Principal-objecten in Azure Active Directory](app-objects-and-service-principals.md)
- [Snelstartgids: een toepassing registreren bij het micro soft Identity-platform](quickstart-register-app.md)
- [Snelstartgids: een client toepassing configureren voor toegang tot Web-Api's](quickstart-configure-app-access-web-apis.md)
- [Een token voor een toepassing met client referentie stromen ophalen](msal-client-applications.md)

Raadpleeg de [Snelstartgids voor .net core daemon](quickstart-v2-netcore-daemon.md)voor een eenvoudiger multi tenant console daemon-toepassing.
