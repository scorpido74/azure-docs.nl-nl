---
title: Een multitenant daemon bouwen die gebruik maakt van het eindpunt van het Microsoft-identiteitsplatform
description: Lees in deze zelfstudie hoe u een ASP.NET web-API aanroept die is beveiligd door Azure Active Directory vanaf een WPF-toepassing (Windows Desktop). De WPF-client verifieert een gebruiker, vraagt een toegangstoken aan en roept de web-API aan.
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
ms.date: 12/10/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.openlocfilehash: 635b12cc2ffc4d318eaaa74fffc17e4ce4d58c0b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79129963"
---
# <a name="tutorial-build-a-multitenant-daemon-that-uses-the-microsoft-identity-platform-endpoint"></a>Zelfstudie: Een daemon met meerdere tenant's bouwen die het eindpunt van het Microsoft-identiteitsplatform gebruikt

In deze zelfstudie leert u hoe u het Microsoft-identiteitsplatform gebruiken om toegang te krijgen tot de gegevens van zakelijke klanten van Microsoft in een langlopend, niet-interactief proces. De voorbeelddaemon gebruikt de [OAuth2-clientreferenties om](v2-oauth2-client-creds-grant-flow.md) een toegangstoken te verkrijgen. De daemon gebruikt het token vervolgens om [Microsoft Graph](https://graph.microsoft.io) aan te roepen en toegang te krijgen tot organisatiegegevens.

> [!div class="checklist"]
> * Een daemon-app integreren met het Microsoft-identiteitsplatform
> * Toepassingsmachtigingen rechtstreeks aan de app verlenen door een beheerder
> * Een toegangstoken krijgen om de Microsoft Graph API aan te roepen
> * Roep de Microsoft Graph API aan.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

De app is gebouwd als een ASP.NET MVC-toepassing. Het maakt gebruik van de OWIN OpenID Connect middleware om gebruikers aan te melden.  

De component "daemon" in dit voorbeeld `SyncController.cs`is een API-controller. Wanneer de controller wordt aangeroepen, wordt een lijst met gebruikers in de Azure Active Directory-tenant (Azure AD) van de klant opgehaald in Microsoft Graph. `SyncController.cs`wordt geactiveerd door een AJAX-oproep in de webapplicatie. Het gebruikt de [Microsoft Authentication Library (MSAL) voor .NET](msal-overview.md) om een toegangstoken voor Microsoft Graph te verkrijgen.

>[!NOTE]
> Als u nieuw bent op het Microsoft-identiteitsplatform, raden we u aan om snel te beginnen met de [.NET Core daemon.](quickstart-v2-netcore-daemon.md)

## <a name="scenario"></a>Scenario

Omdat de app een multitenant-app is voor zakelijke klanten van Microsoft, moet deze een manier bieden voor klanten om de toepassing te "aanmelden" of de toepassing te 'verbinden' met hun bedrijfsgegevens. Tijdens de verbindingsstroom verleent een bedrijfsbeheerder eerst *toepassingsmachtigingen* rechtstreeks aan de app, zodat deze op een niet-interactieve manier toegang heeft tot bedrijfsgegevens, zonder de aanwezigheid van een aangemelde gebruiker. De meerderheid van de logica in dit voorbeeld laat zien hoe u deze verbindingsstroom bereiken met behulp van het eindpunt voor [beheerderstoestemming](v2-permissions-and-consent.md#using-the-admin-consent-endpoint) van het identiteitsplatform.

![Topologie](./media/tutorial-v2-aspnet-daemon-webapp/topology.png)

Lees voor meer informatie over de concepten die in dit voorbeeld worden gebruikt de documentatie van het [clientreferentiesprotocol voor het eindpunt van het identiteitsplatform.](v2-oauth2-client-creds-grant-flow.md)

## <a name="prerequisites"></a>Vereisten

Als u het voorbeeld in deze snelle start wilt uitvoeren, hebt u het volgende nodig:

- [Visual Studio 2017 of 2019](https://visualstudio.microsoft.com/downloads/).
- Een Azure AD-tenant. Zie [Een Azure AD-tenant aanschaffen](quickstart-create-new-tenant.md)voor meer informatie.
- Een of meer gebruikersaccounts in uw Azure AD-tenant. Dit voorbeeld werkt niet met een Microsoft-account (voorheen Windows Live-account). Als u zich hebt aangemeld bij de [Azure-portal](https://portal.azure.com) met een Microsoft-account en nog nooit een gebruikersaccount in uw directory hebt gemaakt, moet u dat nu doen.

## <a name="clone-or-download-this-repository"></a>Klonen of download deze opslagplaats

Voer de opdracht in vanuit uw shell- of opdrachtregel:

```Shell
git clone https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2.git
```

Of [download het voorbeeld in een zip-bestand.](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/archive/master.zip)

## <a name="register-your-application"></a>Uw toepassing registreren

Dit voorbeeld heeft één project. Als u de toepassing wilt registreren bij uw Azure AD-tenant, u het als:

- Volg de stappen in [Het voorbeeld registreren met uw Azure Active Directory-tenant](#register-your-application) en [Configureer het voorbeeld om uw Azure AD-tenant te gebruiken.](#choose-the-azure-ad-tenant)
- PowerShell-scripts gebruiken die:
  - *Maak automatisch* de Azure AD-toepassingen en gerelateerde objecten (wachtwoorden, machtigingen, afhankelijkheden) voor u.
  - De configuratiebestanden van de Visual Studio-projecten wijzigen.

Als u de automatisering wilt gebruiken:

1. Voer PowerShell uit en ga naar de hoofdmap van de gekloonde map.
1. Voer deze opdracht uit:

   ```PowerShell
   Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
   ```

1. Voer het script uit om uw Azure AD-toepassing te maken en configureer de code van de voorbeeldtoepassing dienovereenkomstig:

   ```PowerShell
   .\AppCreationScripts\Configure.ps1
   ```

   Andere manieren om scripts uit te voeren worden beschreven in [scripts voor het maken van apps.](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/blob/master/AppCreationScripts/AppCreationScripts.md)

1. Open de Visual Studio-oplossing en selecteer **Start** om de code uit te voeren.

Als u de automatisering niet wilt gebruiken, gebruikt u de stappen in de volgende secties.

### <a name="choose-the-azure-ad-tenant"></a>De Azure AD-tenant kiezen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Als uw account zich in meer dan één Azure AD-tenant bevindt, selecteert u uw profiel in het menu boven aan de pagina en selecteert u **De map Schakelen**.
1. Wijzig uw portalsessie in de gewenste Azure AD-tenant.

### <a name="register-the-client-app-dotnet-web-daemon-v2"></a>Registreer de client-app (dotnet-web-daemon-v2)

1. Ga naar de pagina [App-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) in het Microsoft-identiteitsplatform voor ontwikkelaars.
1. Selecteer **Nieuwe registratie**.
1. Wanneer de pagina **Een toepassing registreren** verschijnt, voert u de registratiegegevens van de toepassing in:
   - Voer in de sectie **Naam** een betekenisvolle toepassingsnaam in die wordt weergegeven aan gebruikers van de app. Voer bijvoorbeeld **dotnet-web-daemon-v2**in.
   - Selecteer **accounts in een organisatiemap in**de sectie Ondersteunde **accounttypen** .
   - Selecteer **web** in het keuzekader voor het omleiden van **URI (optioneel)** en voer de volgende omleidings-URI's in:
       - **https://localhost:44316/**
       - **https://localhost:44316/Account/GrantPermissions**
          
     Als er meer dan twee omleidings-URI's zijn, moet u deze later toevoegen via het tabblad **Verificatie,** nadat de app is gemaakt.
1. Selecteer **Registreren** om de toepassing te maken.
1. Zoek op de **pagina Overzicht** van de app de waarde van de **id-toepassing (client)** en leg deze vast voor later. U hebt het nodig om het configuratiebestand visual studio voor dit project te configureren.
1. Selecteer in de lijst met pagina’s voor de app de optie **Verificatie**. Daarna kunt u het volgende doen:
   - Stel in de sectie **Geavanceerde instellingen** de URL **van Aanmelding** in op **https://localhost:44316/Account/EndSession**.
   - Selecteer **access-tokens** en **ID-tokens**in de sectie **Geavanceerde instellingen** > **Impliciete subsidie.** Voor dit voorbeeld moet de [impliciete subsidiestroom](v2-oauth2-implicit-grant-flow.md) worden ingeschakeld om de gebruiker aan te melden en een API aan te roepen.
1. Selecteer **Opslaan**.
1. Selecteer in de pagina **Certificaten & geheimen** in de sectie **Clientgeheimen** de optie **Nieuw clientgeheim**. Daarna kunt u het volgende doen:

   1. Voer een sleutelbeschrijving in (bijvoorbeeld **app-geheim),**
   1. Selecteer een sleutelduur van **1 jaar**, In **2 jaar**of nooit **verloopt**.
   1. Selecteer de knop **Add**. 
   1. Wanneer de sleutelwaarde wordt weergegeven, kopieert en slaat u deze op een veilige locatie op. U hebt deze sleutel later nodig om het project in Visual Studio te configureren. Het zal niet opnieuw worden weergegeven of opvraagbaar op een andere manier.
1. Selecteer **API-machtigingen**in de lijst met pagina's voor de app . Daarna kunt u het volgende doen:
   1. Selecteer de knop **Een machtiging toevoegen**.
   1. Controleer of het tabblad **Microsoft-API's** is geselecteerd.
   1. Selecteer **Microsoft Graph**in de sectie **Veelgebruikte Microsoft-API's** .
   1. Controleer in de sectie **Toepassingsmachtigingen** of de juiste machtigingen zijn geselecteerd: **User.Read.All.**
   1. Selecteer de knop **Machtigingen toevoegen.**

## <a name="configure-the-sample-to-use-your-azure-ad-tenant"></a>Het voorbeeld configureren om uw Azure AD-tenant te gebruiken

In de volgende stappen is **ClientID** hetzelfde als 'application ID' of **AppId.**

Open de oplossing in Visual Studio om de projecten te configureren.

### <a name="configure-the-client-project"></a>Het clientproject configureren

Als u de installatiescripts hebt gebruikt, zijn de volgende wijzigingen voor u toegepast.

1. Open het **bestand UserSync\Web.Config.**
1. Zoek de app-toets **ida:ClientId**. Vervang de bestaande waarde door de toepassings-id van de **dotnet-web-daemon-v2-toepassing** die is gekopieerd van de Azure-portal.
1. Zoek de app-toets **ida:ClientSecret**. Vervang de bestaande waarde door de sleutel die u hebt opgeslagen tijdens het maken van de **dotnet-web-daemon-v2-app** in de Azure-portal.

## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

Maak de oplossing schoon, herbouw de oplossing, voer de UserSync-toepassing uit en meld u aan als beheerder in uw Azure AD-tenant. Als u geen Azure AD-tenant hebt om te testen, u [deze instructies volgen](quickstart-create-new-tenant.md) om er een te krijgen.

Wanneer u zich aanmeldt, vraagt de app u eerst om toestemming om u aan te melden en uw gebruikersprofiel te lezen. Met deze toestemming kan de app ervoor zorgen dat u een zakelijke gebruiker bent.

![Toestemming van de gebruiker](./media/tutorial-v2-aspnet-daemon-webapp/firstconsent.png)

De app probeert vervolgens een lijst met gebruikers te synchroniseren vanuit uw Azure AD-tenant, via Microsoft Graph. Als dit niet het kan, wordt u (de tenantbeheerder) gevraagd uw tenant met de app te verbinden.

De app vraagt vervolgens om toestemming om de lijst met gebruikers in uw tenant te lezen.

![toestemming van de beheerder](./media/tutorial-v2-aspnet-daemon-webapp/adminconsent.PNG)

Nadat u toestemming hebt verleend, bent u afgemeld via de app. Deze afmelding zorgt ervoor dat bestaande toegangstokens voor Microsoft Graph uit de tokencache worden verwijderd. Wanneer u zich opnieuw aanmeldt, heeft het nieuwe token dat is verkregen de benodigde machtigingen om naar Microsoft Graph te bellen.


Wanneer u de toestemming verleent, kan de app op elk gewenst moment een query voor gebruikers opvragen. U dit verifiëren door de knop **Gebruikers synchroniseren te** selecteren en de lijst met gebruikers te vernieuwen. Probeer een gebruiker toe te voegen of te verwijderen en de lijst opnieuw te synchroniseren. (Maar houd er rekening mee dat de app alleen de eerste pagina met gebruikers synchroniseert.)

## <a name="about-the-code"></a>Over de code

De relevante code voor dit voorbeeld bevindt zich in de volgende bestanden:

- **App_Start\Startup.Auth.cs**, **Controllers\AccountController.cs**: Eerste aanmelding. In het bijzonder hebben de acties op de controller een **kenmerk Autoriseren,** waardoor de gebruiker zich moet aanmelden. De toepassing gebruikt de [autorisatiecodestroom](v2-oauth2-auth-code-flow.md) om de gebruiker aan te melden.
- **Controllers\SyncController.cs:** De lijst met gebruikers synchroniseren met de lokale opslag in het geheugen.
- **Controllers\UserController.cs:** De lijst met gebruikers weergeven vanuit het lokale opslagarchief in het geheugen.
- **Controllers\AccountController.cs**: Machtigingen van de tenantbeheerder verkrijgen met behulp van het eindpunt voor beheerderstoestemming.

## <a name="re-create-the-sample-app"></a>De voorbeeld-app opnieuw maken

1. Maak in Visual Studio een nieuw **Visual C#ASP.NET** **Web Application (.NET Framework)** project. 
1. Kies in het volgende scherm de **MVC-projectsjabloon.** Voeg ook map- en kernverwijzingen toe voor **Web API,** omdat u later een web-API-controller toevoegt. Laat de gekozen verificatiemodus van het project als standaard: **geen verificatie**.
1. Selecteer het project in het venster **Solution Explorer** en selecteer de **F4-toets.** 
1. Stel **SSL ingeschakeld** in de projecteigenschappen in op **True.** Let op de informatie in **SSL URL**. U hebt het nodig bij het configureren van de registratie van deze toepassing in de Azure-portal.
1. Voeg de volgende ASP.NET OWIN middleware NuGet-pakketten toe: 
   - Microsoft.Owin.Security.ActiveDirectory
   - Microsoft.Owin.Security.Cookies
   - Microsoft.Owin.Host.SystemWeb
   - Microsoft.IdentityModel.Protocol.Extensions
   - Microsoft.Owin.Security.OpenIdConnect
   - Microsoft.Identity.Client 
1. In de **map App_Start:**
   1. Een klasse maken die **Startup.Auth.cs**wordt genoemd. 
   1. Verwijderen **. App_Start** van de naamruimtenaam. 
   1. Vervang de code voor de klasse **Opstarten** door de code uit hetzelfde bestand van de voorbeeld-app.       
   Zorg ervoor dat u de hele klasse definitie te nemen. De definitie verandert van opstarten van **de openbare klasse** naar het opstarten van een openbare **deelklasse.**
1. Los **in Startup.Auth.cs**ontbrekende verwijzingen op door instructies toe **te** voegen zoals voorgesteld door Visual Studio IntelliSense.
1. Klik met de rechtermuisknop op het project, selecteer **Toevoegen**en selecteer **Klasse**.
1. Voer **OWIN**in in het zoekvak . **Owin Startup klasse** wordt weergegeven als een selectie. Selecteer deze en geef de klasse **Startup.cs**een naam.
1. Vervang **in Startup.cs**de code voor de klasse **Opstarten** door de code uit hetzelfde bestand van de voorbeeld-app. Houd er nogmaals rekening mee dat de definitie verandert van het opstarten van **de openbare klasse** naar het opstarten van een gedeeltelijke openbare **klasse**.
1. Voeg in de map **Modellen** een nieuwe klasse toe die **MsGraphUser.cs**. Vervang de implementatie door de inhoud van het bestand met dezelfde naam uit het voorbeeld.
1. Een nieuwe **MVC 5-controller toevoegen - Lege** instantie genaamd **AccountController**. Vervang de implementatie door de inhoud van het bestand met dezelfde naam uit het voorbeeld.
1. Een nieuwe **MVC 5-controller toevoegen - Lege** instantie genaamd **UserController**. Vervang de implementatie door de inhoud van het bestand met dezelfde naam uit het voorbeeld.
1. Een nieuwe **Web API 2-controller toevoegen - Lege** instantie genaamd **SyncController**. Vervang de implementatie door de inhoud van het bestand met dezelfde naam uit het voorbeeld.
1. Voeg voor de gebruikersinterface in de map **Weergaven\Account** drie **lege (zonder model) weergaven toe met** de naam **GrantPermissions**, **Index**en **UserMismatch**. Toevoegen en een met de naam **Index** toevoegen in de map **Weergaven\Gebruiker.** Vervang de implementatie door de inhoud van het bestand met dezelfde naam uit het voorbeeld.
1. Update **\_Shared Layout.cshtml** en **Home\Index.cshtml** om de verschillende weergaven correct aan elkaar te koppelen.

## <a name="deploy-the-sample-to-azure"></a>Het voorbeeld implementeren in Azure

Dit project heeft web-app en web API projecten. Als u ze wilt implementeren op Azure-websites, neemt u voor elk van deze stappen de volgende stappen:

1. Maak een Azure-website.
1. Publiceer de web-app en web API's op de website.
1. Update clients om de website te bellen in plaats van IIS Express.

### <a name="create-and-publish-dotnet-web-daemon-v2-to-an-azure-website"></a>Dotnet-web-daemon-v2 maken en publiceren naar een Azure-website

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer **Een resource maken** in de linkerbovenhoek.
1. Selecteer **Web** > **Web App**en geef uw website een naam. Noem het bijvoorbeeld **dotnet-web-daemon-v2-contoso.azurewebsites.net**.
1. Selecteer de gegevens voor **abonnements-,** **resourcegroep-** en **app-serviceabonnement en -locatie**. **OS** is **Windows,** en **Publiceren** is **Code**.
1. Selecteer **Maken** en wachten tot de app-service is gemaakt.
1. Wanneer u de melding **Implementatie hebt uitgevoerd,** selecteert u **Ga naar resource om** naar de nieuw gemaakte app-service te gaan.
1. Nadat de website is gemaakt, vindt u deze in het **dashboard** en selecteert u deze om het **scherm Overzicht van** de app-service te openen.
1. Download het publicatieprofiel op het tabblad **Overzicht** van de app-service door de koppeling **Publicatieprofiel ophalen** te selecteren en op te slaan. U andere implementatiemechanismen gebruiken, zoals het implementeren vanuit bronbeheer.
1. Overschakelen naar Visual Studio en vervolgens:
   1. Ga naar het **dotnet-web-daemon-v2** project. 
   1. Klik met de rechtermuisknop op het project in Solution Explorer en selecteer **Publiceren**.
   1. Selecteer **Profiel importeren** op de onderste balk en importeer het publicatieprofiel dat u eerder hebt gedownload.
1. Selecteer **Configureren**.
1. Werk op het tabblad **Verbinding** de bestemmings-URL bij zodat deze 'https' gebruikt. Gebruik bijvoorbeeld [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net). Selecteer **Volgende**.
1. Controleer op het tabblad **Instellingen** of **Organisatieverificatie inschakelen** is gewist.  
1. Selecteer **Opslaan**. Selecteer **Publiceren** op het hoofdscherm.

Visual Studio publiceert het project en opent automatisch een browser naar de URL van het project. Als u de standaardwebpagina van het project ziet, is de publicatie geslaagd.

### <a name="update-the-azure-ad-tenant-application-registration-for-dotnet-web-daemon-v2"></a>De azure AD-tenanttoepassingsregistratie bijwerken voor dotnet-web-daemon-v2

1. Ga terug naar [Azure Portal](https://portal.azure.com).
1. Selecteer in het linkerdeelvenster de **Azure Active Directory-service** en selecteer **App-registraties**.
1. Selecteer de **dotnet-web-daemon-v2** applicatie.
1. Werk **op** de pagina Verificatie voor uw toepassing de **URL-velden Afmelden** bij met het adres van uw service. Gebruik bijvoorbeeld [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net).
1. Werk in het menu **Branding** de **URL van** de startpagina bij naar het adres van uw service. Gebruik bijvoorbeeld [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net).
1. Sla de configuratie op.
1. Voeg dezelfde URL toe aan de lijst met waarden van het menu **Verificatie** > **omleiding seinen.** Als u meerdere omleidings-URL's hebt, moet u ervoor zorgen dat er een nieuw item is dat de URI van de app-service gebruikt voor elke omleidings-URL.

## <a name="clean-up-resources"></a>Resources opschonen
Verwijder, wanneer u niet meer nodig bent, het app-object dat u hebt gemaakt in de stap [Uw toepassing registreren.](#register-your-application)  Als u de toepassing wilt verwijderen, volgt u de instructies in [Een door u of uw organisatie geschreven toepassing verwijderen.](quickstart-remove-app.md#remove-an-application-authored-by-you-or-your-organization)

## <a name="get-help"></a>Help opvragen

Gebruik [Stack Overflow](http://stackoverflow.com/questions/tagged/msal) om ondersteuning te krijgen van de community.
Stel eerst uw vragen over Stack Overflow en blader door bestaande problemen om te zien of iemand uw vraag al eerder heeft gesteld.
Zorg ervoor dat uw vragen of opmerkingen worden getagd met 'adal', 'msal' en 'dotnet'.

Als u een bug in het voorbeeld vindt, u het probleem op [GitHub-problemen aande orde stellen.](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/issues)

Als u een bug in MSAL.NET vindt, u het probleem op [MSAL.NET GitHub-problemen aan de orde stellen.](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues)

Als u een aanbeveling wilt geven, gaat u naar de [pagina Gebruikersspraak](https://feedback.azure.com/forums/169401-azure-active-directory).

## <a name="next-steps"></a>Volgende stappen
Meer informatie over de verschillende [verificatiestromen en toepassingsscenario's](authentication-flows-app-scenarios.md) die het Microsoft-identiteitsplatform ondersteunt.

Zie voor meer informatie de volgende conceptuele documentatie:

- [Tenancy in Azure Active Directory](single-and-multi-tenant-apps.md)
- [Inzicht in ervaringen met Azure AD-toepassingtoestemming](application-consent-experience.md)
- [Elke Azure Active Directory-gebruiker aanmelden met het patroon van de multitenant-toepassing](howto-convert-app-to-be-multi-tenant.md)
- [Inzicht in toestemming van gebruiker en beheerder](howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent)
- [Toepassings- en service-principal-objecten in Azure Active Directory](app-objects-and-service-principals.md)
- [Snelstart: een toepassing registreren bij het Microsoft-identiteitsplatform](quickstart-register-app.md)
- [Snelstart: een clienttoepassing configureren om toegang te krijgen tot web-API's](quickstart-configure-app-access-web-apis.md)
- [Een token voor een toepassing met clientreferentiestromen aanschaffen](msal-client-applications.md)

Zie de [.NET Core daemon-snelstart](quickstart-v2-netcore-daemon.md)voor een eenvoudigere multitenant console daemon.
