---
title: Werken met de SDK .NET backendserver
description: Meer informatie over het werken met de .NET-backendserver SDK voor mobiele apps van Azure App Service.
keywords: app-service, azure-app-service, mobiele app, mobiele service, schaal, schaalbaar, app-implementatie, implementatie van Azure-apps
author: conceptdev
ms.assetid: 0620554f-9590-40a8-9f47-61c48c21076b
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.openlocfilehash: cafb0a7e2bf0fbce82448236a2da98079144121e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250138"
---
# <a name="work-with-the-net-backend-server-sdk-for-azure-mobile-apps"></a>Werken met de SDK .NET-backendserver voor Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

In dit onderwerp ziet u hoe u de SDK .NET-backendserver gebruiken in belangrijke azure app-apps voor mobiele apps. Met de Azure Mobile Apps SDK u werken met mobiele clients vanuit uw ASP.NET-toepassing.

> [!TIP]
> De [.NET-server SDK voor Azure Mobile Apps][2] is open source op GitHub. De repository bevat alle broncode inclusief de volledige server SDK unit test suite en een aantal voorbeeldprojecten.
>
>

## <a name="reference-documentation"></a>Referentiedocumentatie
De referentiedocumentatie voor de server SDK bevindt zich hier: [Azure Mobile Apps .NET Reference][1].

## <a name="how-to-create-a-net-mobile-app-backend"></a><a name="create-app"></a>Hoe: Een back-end voor .NET Mobile App maken
Als u een nieuw project start, u een App Service-toepassing maken met behulp van de [Azure-portal] of Visual Studio. U de app-servicetoepassing lokaal uitvoeren of het project publiceren naar uw mobiele app voor app-service in de cloud.

Als u mobiele mogelijkheden toevoegt aan een bestaand project, raadpleegt u de [sdk-sectie downloaden en initialiseren.](#install-sdk)

### <a name="create-a-net-backend-using-the-azure-portal"></a>Een .NET-backend maken met de Azure-portal
Als u een mobiele back-end van een app-service wilt maken, volgt u de [zelfstudie Snelstart][3] of volgt u de volgende stappen:

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

Terug in het blade *Aan de slag,* onder **Een tabel-API maken,** kies **C#** als uw **backendtaal**. Klik **op Downloaden,** haal de gecomprimeerde projectbestanden naar uw lokale computer en open de oplossing in Visual Studio.

### <a name="create-a-net-backend-using-visual-studio-2017"></a>Een .NET-backend maken met Visual Studio 2017

Installeer de Azure-werkbelasting via het Visual Studio Installer om te publiceren naar Azure Mobile Apps-project vanuit Visual Studio. Nadat u de SDK hebt geïnstalleerd, maakt u een ASP.NET toepassing met de volgende stappen:

1. Open het dialoogvenster **Nieuw project** (uit **Bestand** > **Nieuw** > **project...**).
2. Vouw **Visual C#uit** en selecteer **Web**.
3. Selecteer **ASP.NET webtoepassing (.NET Framework)**.
4. Vul de projectnaam in. Klik vervolgens op **OK**.
5. Selecteer **Azure Mobile App** in de lijst met sjablonen.
6. Klik op **OK** om de oplossing te maken.
7. Klik met de rechtermuisknop op het project in de **Solution Explorer** en kies **Publiceren...** en kies **vervolgens App Service** als publicatiedoel.
8. Volg de aanwijzingen om te verifiëren en kies een nieuwe of bestaande Azure App-service die u wilt publiceren.

### <a name="create-a-net-backend-using-visual-studio-2015"></a>Een .NET-backend maken met Visual Studio 2015

Installeer de [Azure SDK voor .NET][4] (versie 2.9.0 of hoger) om een Azure Mobile Apps-project te maken in Visual Studio. Nadat u de SDK hebt geïnstalleerd, maakt u een ASP.NET toepassing met de volgende stappen:

1. Open het dialoogvenster **Nieuw project** (uit **Bestand** > **Nieuw** > **project...**).
2. **Sjablonen** > **visual c#** uitvouwen en **web**selecteren .
3. Selecteer **ASP.NET-webtoepassing**.
4. Vul de projectnaam in. Klik vervolgens op **OK**.
5. Selecteer **Azure Mobile App**onder ASP.NET *4.5.2-sjablonen*. Controleer **Host in de cloud** om een mobiele backend te maken in de cloud waarnaar u dit project publiceren.
6. Klik op **OK**.

## <a name="how-to-download-and-initialize-the-sdk"></a><a name="install-sdk"></a>Hoe: De SDK downloaden en initialiseren
De SDK is beschikbaar op [NuGet.org]. Dit pakket bevat de basisfunctionaliteit die nodig is om aan de slag te gaan met de SDK. Als u de SDK wilt initialiseren, moet u acties uitvoeren op het object **HttpConfiguration.**

### <a name="install-the-sdk"></a>De SDK installeren
Als u de SDK wilt installeren, klikt u met de rechtermuisknop op het serverproject in Visual Studio, selecteert u **NuGet-pakketten beheren,** zoekt u naar het [pakket Microsoft.Azure.Mobile.Server] en klikt u op **Installeren**.

### <a name="initialize-the-server-project"></a><a name="server-project-setup"></a>Het serverproject initialiseren
Een .NET backend serverproject wordt geïnitialiseerd vergelijkbaar met andere ASP.NET projecten, door een OWIN-opstartklasse op te nemen. Zorg ervoor dat je naar `Microsoft.Owin.Host.SystemWeb`het NuGet-pakket hebt verwezen. Als u deze klasse wilt toevoegen in Visual Studio, klikt u met de rechtermuisknop op uw serverproject en selecteert u**Nieuw item** **toevoegen** >
en vervolgens de**klasse Owin Opstart**van **webalgemeen** > **General** > .  Een klasse wordt gegenereerd met het volgende kenmerk:

    [assembly: OwinStartup(typeof(YourServiceName.YourStartupClassName))]

Gebruik `Configuration()` in de methode van uw OWIN-opstartklasse een **http-configuratieobject** om de Azure Mobile Apps-omgeving te configureren.
In het volgende voorbeeld wordt het serverproject geïnitialt zonder toegevoegde functies:

    // in OWIN startup class
    public void Configuration(IAppBuilder app)
    {
        HttpConfiguration config = new HttpConfiguration();

        new MobileAppConfiguration()
            // no added features
            .ApplyTo(config);

        app.UseWebApi(config);
    }

Als u afzonderlijke functies wilt inschakelen, moet u extensiemethoden aanroepen op het object **MobileAppConfiguration** voordat u **ApplyTo**aanroept. Met de volgende code worden bijvoorbeeld de standaardroutes toegevoegd `[MobileAppController]` aan alle API-controllers die het kenmerk hebben tijdens de initialisatie:

    new MobileAppConfiguration()
        .MapApiControllers()
        .ApplyTo(config);

De server start snel vanaf de Azure-portal aanroepen **UseDefaultConfiguration()**. Dit komt overeen met de volgende instelling:

        new MobileAppConfiguration()
            .AddMobileAppHomeController()             // from the Home package
            .MapApiControllers()
            .AddTables(                               // from the Tables package
                new MobileAppTableConfiguration()
                    .MapTableControllers()
                    .AddEntityFramework()             // from the Entity package
                )
            .AddPushNotifications()                   // from the Notifications package
            .MapLegacyCrossDomainController()         // from the CrossDomain package
            .ApplyTo(config);

De gebruikte uitbreidingsmethoden zijn:

* `AddMobileAppHomeController()`biedt de standaardstartpagina van Azure Mobile Apps.
* `MapApiControllers()`biedt aangepaste API-mogelijkheden voor WebAPI-controllers die zijn versierd met het `[MobileAppController]` kenmerk.
* `AddTables()`biedt een toewijzing `/tables` van de eindpunten naar tabelcontrollers.
* `AddTablesWithEntityFramework()`is een afkorting voor `/tables` het in kaart brengen van de eindpunten met behulp van Entiteit Framework gebaseerde controllers.
* `AddPushNotifications()`biedt een eenvoudige methode voor het registreren van apparaten voor Notification Hubs.
* `MapLegacyCrossDomainController()`biedt standaard CORS-headers voor lokale ontwikkeling.

### <a name="sdk-extensions"></a>SDK-extensies
De volgende NuGet-gebaseerde uitbreidingspakketten bieden verschillende mobiele functies die door uw toepassing kunnen worden gebruikt. U schakelt extensies in tijdens de initialisatie met het object **MobileAppConfiguration.**

* [Microsoft.Azure.Mobile.Server.Quickstart] ondersteunt de basisinstallatie van mobiele apps. Toegevoegd aan de configuratie door tijdens de initialisatie de **extensiemethode UseDefaultConfiguration** aan te roepen. Deze extensie omvat volgende extensies: Meldingen, verificatie, entiteit, tabellen, cross-domein en Thuispakketten. Dit pakket wordt gebruikt door de Quickstart mobiele apps die beschikbaar zijn op de Azure-portal.
* [Microsoft.Azure.Mobile.Server.Home](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Home/) Implementeert de *standaardpagina waarop deze mobiele app actief is* voor de hoofdmap van de website. Toevoegen aan de configuratie door de **extensiemethode AddMobileAppHomeController** aan te roepen.
* [Microsoft.Azure.Mobile.Server.Tables](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Tables/) bevat klassen voor het werken met gegevens en het instellen van de gegevenspijplijn. Toevoegen aan de configuratie door de **extensiemethode Addtables** aan te roepen.
* [Met Microsoft.Azure.Mobile.Server.Entity](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Entity/) kan het Entiteitskader toegang krijgen tot gegevens in de SQL-database. Toevoegen aan de configuratie door de **extensiemethode AddTablesWithEntityFramework** aan te roepen.
* [Microsoft.Azure.Mobile.Server.Authentication] Hiermee u verificatie en het instellen van de OWIN-middleware instellen die wordt gebruikt om tokens te valideren. Toevoegen aan de configuratie door de **AddAppServiceAuthentication** en **IAppBuilder**te bellen. **Gebruik appServiceAuthentication-extensiemethoden.**
* [Microsoft.Azure.Mobile.Server.Notifications] Hiermee worden pushmeldingen mogelijk en wordt een eindpunt voor pushregistratie gedefinieerd. Toevoegen aan de configuratie door de **extensiemethode AddPushNotifications** aan te roepen.
* [Microsoft.Azure.Mobile.Server.CrossDomain](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.CrossDomain/) Hiermee maakt u een controller die gegevens aan oudere webbrowsers van uw mobiele app levert. Toevoegen aan de configuratie door de **extensiemethode MapLegacyCrossDomainController** aan te roepen.
* [Microsoft.Azure.Mobile.Server.Login] Biedt de methode AppServiceLoginHandler.CreateToken(), een statische methode die wordt gebruikt tijdens aangepaste verificatiescenario's.

## <a name="how-to-publish-the-server-project"></a><a name="publish-server-project"></a>Hoe: Het serverproject publiceren
In deze sectie ziet u hoe u uw .NET-backendproject publiceert vanuit Visual Studio. U uw backendproject ook implementeren met [Git](../app-service/deploy-local-git.md) of een van de andere methoden die daar beschikbaar zijn.

1. Bouw in Visual Studio het project opnieuw om NuGet-pakketten te herstellen.
2. Klik in Solution Explorer met de rechtermuisknop op het project en klik op **Publiceren**. De eerste keer dat u publiceert, moet u een publicatieprofiel definiëren. Wanneer u al een profiel hebt gedefinieerd, u het selecteren en op **Publiceren**klikken.
3. Als u wordt gevraagd een publicatiedoel te selecteren, klikt u op **Microsoft Azure App Service** > **Next**en vervolgens (indien nodig) aanmelden met uw Azure-referenties.
   Visual Studio downloadt en slaat uw publicatie-instellingen veilig rechtstreeks vanuit Azure op.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-1.png)
4. Kies uw **abonnement,** selecteer **Resourcetype** in **weergave,** vouw **mobiele app**uit en klik op de back-end van uw mobiele app en klik vervolgens op **OK**.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-2.png)
5. Controleer de profielgegevens van het publiceren en klik op **Publiceren**.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-3.png)

    Wanneer de backend van uw mobiele app is gepubliceerd, ziet u een bestemmingspagina die aangeeft dat ze succesvol zijn.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-success.png)

## <a name="how-to-define-a-table-controller"></a><a name="define-table-controller"></a>Hoe: Een tabelcontroller definiëren
Definieer een tabelcontroller om een SQL-tabel bloot te stellen aan mobiele clients.  Het configureren van een tabelcontroller vereist drie stappen:

1. Maak een klasse DTO (Data Transfer Object) (DTO).
2. Een tabelverwijzing configureren in de klasse Mobile DbContext.
3. Maak een tabelcontroller.

Een Object gegevensoverdracht (DTO) is een gewoon `EntityData`C#-object dat overerft van .  Bijvoorbeeld:

    public class TodoItem : EntityData
    {
        public string Text { get; set; }
        public bool Complete {get; set;}
    }

De DTO wordt gebruikt om de tabel in de SQL-database te definiëren.  Als u de databasevermelding `DbSet<>` wilt maken, voegt u een eigenschap toe aan de DbContext die u gebruikt.  In de standaardprojectsjabloon voor Azure Mobile Apps `Models\MobileServiceContext.cs`wordt de DbContext genoemd:

    public class MobileServiceContext : DbContext
    {
        private const string connectionStringName = "Name=MS_TableConnectionString";

        public MobileServiceContext() : base(connectionStringName)
        {

        }

        public DbSet<TodoItem> TodoItems { get; set; }

        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {
            modelBuilder.Conventions.Add(
                new AttributeToColumnAnnotationConvention<TableColumnAttribute, string>(
                    "ServiceColumnTable", (property, attributes) => attributes.Single().ColumnType.ToString()));
        }
    }

Als u de Azure SDK hebt geïnstalleerd, u nu als volgt een sjabloontabelcontroller maken:

1. Klik met de rechtermuisknop op de map Controllers en selecteer > **Controller** **toevoegen...**.
2. Selecteer de optie **Tabelcontroller azure mobiele apps** en klik op **Toevoegen**.
3. Ga als een leider in het dialoogvenster **Controller toevoegen:**
   * Selecteer in de vervolgkeuzelijst **Modelklasse** uw nieuwe DTO.
   * Selecteer in de vervolgkeuzelijst **DbContext** de klasse DbContext van mobiele service.
   * De naam Controller wordt voor u gemaakt.
4. Klik op**toevoegen**.

Het quickstart server project bevat een voorbeeld voor een eenvoudige **TodoItemController**.

### <a name="how-to-adjust-the-table-paging-size"></a><a name="adjust-pagesize"></a>Hoe: de grootte van de tabelpaging aanpassen
Azure Mobile Apps retourneert standaard 50 records per aanvraag.  Paging zorgt ervoor dat de client niet binden hun UI draad, noch de server te lang, zorgen voor een goede gebruikerservaring. Als u de grootte van de tabelpaging wilt wijzigen, verhoogt u de 'toegestane querygrootte' aan de `EnableQuery` serverzijde en de paginagrootte aan de clientzijde De "toegestane querygrootte" aan de serverzijde wordt aangepast met behulp van het kenmerk:

    [EnableQuery(PageSize = 500)]

Controleer of de PageSize dezelfde of groter is dan de door de client gevraagde grootte.  Raadpleeg de specifieke HOWTO-documentatie van de client voor meer informatie over het wijzigen van de grootte van de clientpagina.

## <a name="how-to-define-a-custom-api-controller"></a>How to: Een aangepaste API-controller definiëren
De aangepaste API-controller biedt de meest elementaire functionaliteit aan de back-end van uw mobiele app door een eindpunt bloot te stellen. U een mobiele API-controller registreren met het kenmerk [MobileAppController]. Het `MobileAppController` kenmerk registreert de route, stelt de JSON-serialisator van Mobile Apps in en schakelt [clientversiecontrole](app-service-mobile-client-and-server-versioning.md)in.

1. Klik in Visual Studio met de rechtermuisknop op de map Controllers en klik vervolgens op**Controller** **toevoegen,** > selecteer **Web API 2&mdash;Controller Leeg** en klik op **Toevoegen**.
2. Geef een **controllernaam** `CustomController`, zoals , en klik op **Toevoegen**.
3. Voeg in het bestand van de nieuwe klasse controller het volgende toe met een instructie:

        using Microsoft.Azure.Mobile.Server.Config;
4. Pas het kenmerk **[MobileAppController]** toe op de classificatiedefinitie van de API-controller, zoals in het volgende voorbeeld:

        [MobileAppController]
        public class CustomController : ApiController
        {
              //...
        }
5. Voeg in App_Start/Startup.MobileApp.cs-bestand een aanroep toe aan de extensiemethode **MapApiControllers,** zoals in het volgende voorbeeld:

        new MobileAppConfiguration()
            .MapApiControllers()
            .ApplyTo(config);

U de `UseDefaultConfiguration()` extensiemethode `MapApiControllers()`ook gebruiken in plaats van. Elke controller die niet **mobielAppControllerAttribute** heeft toegepast, kan nog steeds worden geopend door clients, maar kan niet correct worden verbruikt door clients die een SDK voor mobiele apps gebruiken.

## <a name="how-to-work-with-authentication"></a>Hoe: Werken met verificatie
Azure Mobile Apps maakt gebruik van App Service Authentication / Autorisatie om uw mobiele backend te beveiligen.  In deze sectie ziet u hoe u de volgende verificatiegerelateerde taken uitvoert in uw .NET-backendserverproject:

* [Hoe: Verificatie toevoegen aan een serverproject](#add-auth)
* [How to: Aangepaste verificatie gebruiken voor uw toepassing](#custom-auth)
* [How to: Geverifieerde gebruikersgegevens ophalen](#user-info)
* [Hoe: Gegevenstoegang voor geautoriseerde gebruikers beperken](#authorize)

### <a name="how-to-add-authentication-to-a-server-project"></a><a name="add-auth"></a>Hoe: Verificatie toevoegen aan een serverproject
U verificatie toevoegen aan uw serverproject door het **mobileappconfiguration-object** uit te breiden en OWIN middleware te configureren. Wanneer u het [Microsoft.Azure.Mobile.Server.Quickstart-pakket] installeert en de **extensieusedefaultconfiguratie-extensie** aanroept, u doorgaan met stap 3.

1. Installeer in Visual Studio het pakket [Microsoft.Azure.Mobile.Server.Authentication.]
2. Voeg in het Startup.cs-projectbestand de volgende coderegel toe aan het begin van de **methode Configuratie:**

        app.UseAppServiceAuthentication(config);

    Deze OWIN middleware-component valideert tokens die zijn uitgegeven door de bijbehorende App Service-gateway.
3. Voeg `[Authorize]` het kenmerk toe aan een controller of methode waarvoor verificatie vereist is.

Zie [Verificatie toevoegen aan uw app voor](app-service-mobile-ios-get-started-users.md)meer informatie over het verifiëren van clients naar de back-end van uw mobiele apps.

### <a name="how-to-use-custom-authentication-for-your-application"></a><a name="custom-auth"></a>How to: Aangepaste verificatie gebruiken voor uw toepassing
> [!IMPORTANT]
> Als u aangepaste verificatie wilt inschakelen, moet u eerst Verificatie van appservice inschakelen zonder een provider voor uw App-service in de Azure-portal te selecteren. Dit maakt de WEBSITE_AUTH_SIGNING_KEY omgevingsvariabele mogelijk wanneer deze wordt gehost.
> 
> 
> Als u geen gebruik wilt maken van een van de Aanbieders van Verificatie/Autorisatie van de App Service, u uw eigen inlogsysteem implementeren. Installeer het [pakket Microsoft.Azure.Mobile.Server.Login] om te helpen bij het genereren van verificatietokens.  Geef uw eigen code op voor het valideren van gebruikersreferenties. U bijvoorbeeld controleren op gezouten en gehashte wachtwoorden in een database. In het onderstaande `isValidAssertion()` voorbeeld is de methode (elders gedefinieerd) verantwoordelijk voor deze controles.

De aangepaste verificatie wordt weergegeven door het `register` `login` maken van een ApiController en bloot en acties. De client moet een aangepaste gebruikersinterface gebruiken om de informatie van de gebruiker te verzamelen.  De informatie wordt vervolgens naar de API verzonden met een standaard HTTP POST-oproep. Zodra de server de bewering valideert, `AppServiceLoginHandler.CreateToken()` wordt een token uitgegeven met behulp van de methode.  De ApiController **mag** `[MobileAppController]` het kenmerk niet gebruiken.

Een `login` voorbeeldactie:

        public IHttpActionResult Post([FromBody] JObject assertion)
        {
            if (isValidAssertion(assertion)) // user-defined function, checks against a database
            {
                JwtSecurityToken token = AppServiceLoginHandler.CreateToken(new Claim[] { new Claim(JwtRegisteredClaimNames.Sub, assertion["username"]) },
                    mySigningKey,
                    myAppURL,
                    myAppURL,
                    TimeSpan.FromHours(24) );
                return Ok(new LoginResult()
                {
                    AuthenticationToken = token.RawData,
                    User = new LoginResultUser() { UserId = userName.ToString() }
                });
            }
            else // user assertion was not valid
            {
                return this.Request.CreateUnauthorizedResponse();
            }
        }

In het voorgaande voorbeeld zijn LoginResult en LoginResultUser serializable objecten die vereiste eigenschappen blootstellen. De client verwacht dat inlogreacties worden geretourneerd als JSON-objecten van het formulier:

        {
            "authenticationToken": "<token>",
            "user": {
                "userId": "<userId>"
            }
        }

De `AppServiceLoginHandler.CreateToken()` methode bevat een *doelgroep* en een *parameter voor een emittent.* Beide parameters zijn ingesteld op de URL van uw toepassingshoofd, met behulp van het HTTPS-schema. Op dezelfde manier moet u *secretKey* instellen als de waarde van de ondertekeningssleutel van uw toepassing. Distribueer de ondertekeningssleutel niet in een client, omdat deze kan worden gebruikt om sleutels te slaan en gebruikers na te bootsen. U de ondertekeningssleutel verkrijgen terwijl u deze host in App Service door te verwijzen naar de variabele van de *omgeving van DE\_\_\_ONDERTEKENDSLEUTEL* van WEBSITE AUTH. Volg indien nodig in een lokale foutopsporingscontext de instructies in de sectie [Lokale foutopsporing met verificatie](#local-debug) om de sleutel op te halen en op te slaan als toepassingsinstelling.

Het uitgegeven token kan ook andere claims en een vervaldatum bevatten.  Minimaal moet het uitgegeven token een onderwerp **(sub)** claim bevatten.

U de `loginAsync()` standaardclientmethode ondersteunen door de verificatieroute te overbelasten.  Als de `client.loginAsync('custom');` klant belt om in `/.auth/login/custom`te loggen, moet uw route .  U de route voor de `MapHttpRoute()`aangepaste verificatiecontroller instellen met :

    config.Routes.MapHttpRoute("custom", ".auth/login/custom", new { controller = "CustomAuth" });

> [!TIP]
> Het `loginAsync()` gebruik van de aanpak zorgt ervoor dat het verificatietoken is gekoppeld aan elke volgende oproep aan de service.
>
>

### <a name="how-to-retrieve-authenticated-user-information"></a><a name="user-info"></a>How to: Geverifieerde gebruikersgegevens ophalen
Wanneer een gebruiker is geverifieerd door App Service, hebt u toegang tot de toegewezen gebruikersnaam en andere informatie in uw .NET-backendcode. De gebruikersinformatie kan worden gebruikt voor het nemen van autorisatiebeslissingen in de backend. Met de volgende code wordt de gebruikersnaam verkregen die aan een verzoek is gekoppeld:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

De SID is afgeleid van de provider-specifieke gebruikersnaam en is statisch voor een bepaalde gebruiker en login provider.  De SID is ongeldig voor ongeldige verificatietokens.

Met App Service u ook specifieke claims aanvragen bij uw inlogprovider. Elke identiteitsprovider kan meer informatie verstrekken via de identiteitsprovider SDK.  U bijvoorbeeld de Facebook Graph API gebruiken voor vriendeninformatie.  U claims opgeven die worden aangevraagd in het providerblad in de Azure-portal. Sommige claims vereisen een extra configuratie met de identiteitsprovider.

De volgende code roept de **GetAppServiceIdentityAsync-extensiemethode** aan om de inloggegevens te krijgen, waaronder het toegangstoken dat nodig is om verzoeken te doen tegen de Facebook Graph API:

    // Get the credentials for the logged-in user.
    var credentials =
        await this.User
        .GetAppServiceIdentityAsync<FacebookCredentials>(this.Request);

    if (credentials.Provider == "Facebook")
    {
        // Create a query string with the Facebook access token.
        var fbRequestUrl = "https://graph.facebook.com/me/feed?access_token="
            + credentials.AccessToken;

        // Create an HttpClient request.
        var client = new System.Net.Http.HttpClient();

        // Request the current user info from Facebook.
        var resp = await client.GetAsync(fbRequestUrl);
        resp.EnsureSuccessStatusCode();

        // Do something here with the Facebook user information.
        var fbInfo = await resp.Content.ReadAsStringAsync();
    }

Voeg een gebruiksinstructie toe om `System.Security.Principal` de **extensiemethode GetAppServiceIdentityAsync** op te geven.

### <a name="how-to-restrict-data-access-for-authorized-users"></a><a name="authorize"></a>Hoe: Gegevenstoegang voor geautoriseerde gebruikers beperken
In de vorige sectie hebben we laten zien hoe we de gebruikersnaam van een geverifieerde gebruiker kunnen ophalen. U de toegang tot gegevens en andere bronnen beperken op basis van deze waarde. Als u bijvoorbeeld een userId-kolom aan tabellen toevoegt en de queryresultaten filtert met de gebruikers-id, is dit een eenvoudige manier om geretourneerde gegevens alleen te beperken tot geautoriseerde gebruikers. Met de volgende code worden gegevensrijen alleen geretourneerd wanneer de SID overeenkomt met de waarde in de kolom UserId in de todoItem-tabel:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

    // Only return data rows that belong to the current user.
    return Query().Where(t => t.UserId == sid);

De `Query()` methode `IQueryable` retourneert een die kan worden gemanipuleerd door LINQ om filteren te verwerken.

## <a name="how-to-add-push-notifications-to-a-server-project"></a>Hoe: Pushmeldingen toevoegen aan een serverproject
Voeg pushmeldingen toe aan uw serverproject door het object **MobileAppConfiguration** uit te breiden en een Notification Hubs-client te maken.

1. Klik in Visual Studio met de rechtermuisknop op het serverproject en klik op **NuGet-pakketten beheren,** zoeken `Microsoft.Azure.Mobile.Server.Notifications`naar en klik vervolgens op **Installeren**.
2. Herhaal deze stap `Microsoft.Azure.NotificationHubs` om het pakket te installeren, inclusief de clientbibliotheek van Notification Hubs.
3. Voeg in App_Start/Startup.MobileApp.cs een oproep toe aan de **extensiemethode AddPushNotifications()** tijdens de initialisatie:

        new MobileAppConfiguration()
            // other features...
            .AddPushNotifications()
            .ApplyTo(config);
4. Voeg de volgende code toe waarmee een client Van Meldinghubs wordt gemaakt:

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings =
            config.GetMobileAppSettingsProvider().GetMobileAppSettings();

        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
            .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

U nu de client Meldinghubs gebruiken om pushmeldingen naar geregistreerde apparaten te verzenden. Zie [Add push notifications to your app](app-service-mobile-ios-get-started-push.md) (Pushmeldingen toevoegen aan uw app) voor meer informatie. Zie Overzicht van [meldingenhubs](../notification-hubs/notification-hubs-push-notification-overview.md)voor meer informatie over meldingshubs.

## <a name="how-to-enable-targeted-push-using-tags"></a><a name="tags"></a>Hoe: Gerichte push inschakelen met tags
Met Meldingenhubs u gerichte meldingen naar specifieke registraties verzenden met behulp van tags. Er worden automatisch verschillende tags gemaakt:

* De installatie-id identificeert een specifiek apparaat.
* De gebruikersnaam op basis van de geverifieerde SID identificeert een specifieke gebruiker.

De installatie-ID is toegankelijk vanuit de **eigenschap installationId** op de **MobileServiceClient.**  In het volgende voorbeeld ziet u hoe u een installatie-id gebruikt om een tag toe te voegen aan een specifieke installatie in Notification Hubs:

    hub.PatchInstallation("my-installation-id", new[]
    {
        new PartialUpdateOperation
        {
            Operation = UpdateOperationType.Add,
            Path = "/tags",
            Value = "{my-tag}"
        }
    });

Alle tags die door de client worden geleverd tijdens de registratie van pushmeldingen, worden genegeerd door de backend bij het maken van de installatie. Als u een client in staat wilt stellen tags aan de installatie toe te voegen, moet u een aangepaste API maken die tags toevoegt met behulp van het voorgaande patroon.

Zie [client-toegevoegde pushmeldingstags][5] in het voorbeeld van app-service mobiele apps die snel worden voltooid.

## <a name="how-to-send-push-notifications-to-an-authenticated-user"></a><a name="push-user"></a>Hoe: Pushmeldingen verzenden naar een geverifieerde gebruiker
Wanneer een geverifieerde gebruiker zich registreert voor pushmeldingen, wordt er automatisch een gebruikers-id-tag aan de registratie toegevoegd. Met deze tag u pushmeldingen verzenden naar alle apparaten die door die persoon zijn geregistreerd. De volgende code krijgt de SID van de gebruiker het maken van het verzoek en stuurt een template push-melding naar elke apparaat registratie voor die persoon:

    // Get the current user SID and create a tag for the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;
    string userTag = "_UserId:" + sid;

    // Build a dictionary for the template with the item message text.
    var notification = new Dictionary<string, string> { { "message", item.Text } };

    // Send a template notification to the user ID.
    await hub.SendTemplateNotificationAsync(notification, userTag);

Wanneer u zich registreert voor pushmeldingen van een geverifieerde client, moet u ervoor zorgen dat de verificatie is voltooid voordat u de registratie probeert. Zie [Push en gebruikers][6] in het voorbeeld van App Service Mobile Apps in QuickStart voor .NET backend voor meer informatie.

## <a name="how-to-debug-and-troubleshoot-the-net-server-sdk"></a>How to: Debuggen en de .NET Server SDK oplossen
Azure App Service biedt verschillende technieken voor foutopsporing en probleemoplossing voor ASP.NET toepassingen:

* [Een Azure App-service bewaken](../app-service/web-sites-monitor.md)
* [Diagnostische logboekregistratie inschakelen in Azure App-service](../app-service/troubleshoot-diagnostic-logs.md)
* [Problemen met een Azure App-service in Visual Studio oplossen](../app-service/troubleshoot-dotnet-visual-studio.md)

### <a name="logging"></a>Logboekregistratie
U diagnostische logboeken van App Service schrijven met behulp van de standaard ASP.NET tracewriting. Voordat u naar de logboeken schrijven, moet u diagnostische gegevens inschakelen in uw back-end van mobiele apps.

Ga als u de diagnose in en schrijf naar de logboeken:

1. Volg de stappen in [Toepassingslogboekregistratie inschakelen (Windows).](../app-service/troubleshoot-diagnostic-logs.md#enable-application-logging-windows)
2. Voeg de volgende vermelding toe in uw codebestand:

        using System.Web.Http.Tracing;
3. Maak een tracewriter om te schrijven vanuit de .NET backend naar de diagnostische logboeken, als volgt:

        ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
        traceWriter.Info("Hello, World");
4. Publiceer uw serverproject opnieuw en krijg toegang tot de back-end van de mobiele app om het codepad met de logboekregistratie uit te voeren.
5. Download en evalueer de logboeken, zoals beschreven in [Access-logboekbestanden.](../app-service/troubleshoot-diagnostic-logs.md#access-log-files)

### <a name="local-debugging-with-authentication"></a><a name="local-debug"></a>Lokale foutopsporing met verificatie
U uw toepassing lokaal uitvoeren om wijzigingen te testen voordat u ze publiceert in de cloud. Druk voor de meeste backends van Azure Mobile Apps op *F5* in Visual Studio. Er zijn echter enkele aanvullende overwegingen bij het gebruik van verificatie.

U moet een mobiele cloud-app hebben met Verificatie/Autorisatie van App Service geconfigureerd en uw client moet het cloudeindpunt hebben opgegeven als de alternatieve aanmeldingshost. Zie de documentatie voor uw klantplatform voor de specifieke stappen die nodig zijn.

Controleer of [microsoft.azure.mobile.server.authentication] is geïnstalleerd op uw mobiele backend. Voeg vervolgens in de OWIN-opstartklasse van `MobileAppConfiguration` uw toepassing het `HttpConfiguration`volgende toe, nadat deze is toegepast op uw:

        app.UseAppServiceAuthentication(new AppServiceAuthenticationOptions()
        {
            SigningKey = ConfigurationManager.AppSettings["authSigningKey"],
            ValidAudiences = new[] { ConfigurationManager.AppSettings["authAudience"] },
            ValidIssuers = new[] { ConfigurationManager.AppSettings["authIssuer"] },
            TokenHandler = config.GetAppServiceTokenHandler()
        });

In het voorgaande voorbeeld moet u de instellingen van de *authAudience-* en *authIssuer-toepassing* in uw Web.config-bestand configureren om elk de URL van uw toepassingshoofd te zijn, met behulp van het HTTPS-schema. Op dezelfde manier moet u *authSigningKey* instellen als de waarde van de ondertekeningssleutel van uw toepassing.
Ga als het gaat om het verkrijgen van de ondertekeningssleutel:

1. Navigeren naar uw app binnen de [Azure-portal]
2. Klik op **Extra,** **Kudu**, **Ga**.
3. Klik op de site Kudu Management op **Omgeving**.
4. Zoek de waarde voor *WEBSITE\_AUTH\_SIGNING\_KEY*.

Gebruik de ondertekeningssleutel voor de parameter *authSigningKey* in uw lokale toepassingsconfig.  Uw mobiele backend is nu uitgerust om tokens te valideren wanneer u lokaal wordt uitgevoerd, waarbij de client het token verkrijgt van het cloudgebaseerde eindpunt.

[1]: https://msdn.microsoft.com/library/azure/dn961176.aspx
[2]: https://github.com/Azure/azure-mobile-apps-net-server
[3]: app-service-mobile-ios-get-started.md
[4]: https://azure.microsoft.com/downloads/
[5]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#client-added-push-notification-tags
[6]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#push-to-users
[Azure-portal]: https://portal.azure.com
[NuGet.org]: https://www.nuget.org/
[Microsoft.Azure.Mobile.Server]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/
[Microsoft.Azure.Mobile.Server.Quickstart]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Quickstart/
[Microsoft.Azure.Mobile.Server.Verificatie]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/
[Microsoft.Azure.Mobile.Server.Aanmelding]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Login/
[Meldingen van Microsoft.Azure.Mobile.Server.]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Notifications/
[MapHttpAttributeRoutes]: https://msdn.microsoft.com/library/dn479134(v=vs.118).aspx
