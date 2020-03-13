---
title: Werken met de .NET back-end-server-SDK
description: Meer informatie over het werken met de .NET back-end-server-SDK voor Azure App Service Mobile Apps.
keywords: app service, Azure app service, mobiele app, mobiele service, schaal, schaalbaar, app-implementatie, implementatie van Azure-app
author: conceptdev
ms.assetid: 0620554f-9590-40a8-9f47-61c48c21076b
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.openlocfilehash: cafb0a7e2bf0fbce82448236a2da98079144121e
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79250138"
---
# <a name="work-with-the-net-backend-server-sdk-for-azure-mobile-apps"></a>Werken met de .NET back-end-server-SDK voor Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

In dit onderwerp wordt beschreven hoe u de .NET back-end server-SDK kunt gebruiken in Key Azure App Service Mobile Apps-scenario's. De Azure Mobile Apps SDK helpt u bij het werken met mobiele clients vanuit uw ASP.NET-toepassing.

> [!TIP]
> De [.NET Server SDK voor Azure Mobile apps][2] is open source op github. De opslag plaats bevat alle bron code, inclusief de volledige server SDK unit test suite en enkele voorbeeld projecten.
>
>

## <a name="reference-documentation"></a>Referentie documentatie
De referentie documentatie voor de server SDK bevindt zich hier: [Azure Mobile apps .net-referentie][1].

## <a name="create-app"></a>Procedure: een back-end voor een mobiele app van .NET maken
Als u een nieuw project start, kunt u een App Service-toepassing maken met behulp van de [Azure-portal] of Visual Studio. U kunt de App Service toepassing lokaal uitvoeren of het project publiceren naar uw mobiele app voor App Service op basis van de Cloud.

Als u mobiele mogelijkheden toevoegt aan een bestaand project, raadpleegt u de sectie [de SDK downloaden en initialiseren](#install-sdk) .

### <a name="create-a-net-backend-using-the-azure-portal"></a>Een .NET-back-end maken met behulp van de Azure Portal
Als u een App Service mobiele back-end wilt maken, volgt u de [zelf studie van Quick][3] start of volgt u deze stappen:

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

Ga terug naar de Blade *aan* de slag en kies **C#** als **back-uptaal**onder **een tabel-API maken**. Klik op **downloaden**, Extraheer de gecomprimeerde project bestanden naar uw lokale computer en open de oplossing in Visual Studio.

### <a name="create-a-net-backend-using-visual-studio-2017"></a>Een .NET-back-end maken met Visual Studio 2017

Installeer de Azure-workload via het Visual Studio-installatie programma om te publiceren naar Azure Mobile Apps project vanuit Visual Studio. Nadat u de SDK hebt geïnstalleerd, maakt u een ASP.NET-toepassing met behulp van de volgende stappen:

1. Open het dialoog venster **Nieuw project** (uit **bestand** > **Nieuw** > **project...** ).
2. Vouw **Visual C#**  uit en selecteer **Web**.
3. Selecteer **ASP.NET-webtoepassing (.NET Framework)** .
4. Vul de project naam in. Klik vervolgens op **OK**.
5. Selecteer **Azure Mobile App** in de lijst met sjablonen.
6. Klik op **OK** om de oplossing te maken.
7. Klik met de rechter muisknop op het project in de **Solution Explorer** en kies **publiceren...** en kies vervolgens **app service** als publicatie doel.
8. Volg de prompts om te verifiëren en kies een nieuwe of bestaande Azure App Service om te publiceren.

### <a name="create-a-net-backend-using-visual-studio-2015"></a>Een .NET-back-end maken met Visual Studio 2015

Installeer de [Azure SDK voor .net][4] (versie 2.9.0 of hoger) om een Azure Mobile apps-project in Visual Studio te maken. Nadat u de SDK hebt geïnstalleerd, maakt u een ASP.NET-toepassing met behulp van de volgende stappen:

1. Open het dialoog venster **Nieuw project** (uit **bestand** > **Nieuw** > **project...** ).
2. Vouw **sjablonen** > **Visual C#** uit en selecteer **Web**.
3. Selecteer **ASP.NET-webtoepassing**.
4. Vul de project naam in. Klik vervolgens op **OK**.
5. Onder *ASP.net 4.5.2-sjablonen*selecteert u **Azure Mobile App**. Controleer **de host in de Cloud** om een mobiele back-end te maken in de Cloud waarnaar u dit project kunt publiceren.
6. Klik op **OK**.

## <a name="install-sdk"></a>Procedure: de SDK downloaden en initialiseren
De SDK is beschikbaar op [NuGet.org]. Dit pakket bevat de basis functionaliteit die is vereist om aan de slag te gaan met de SDK. Als u de SDK wilt initialiseren, moet u acties uitvoeren op het object **HttpConfiguration** .

### <a name="install-the-sdk"></a>De SDK installeren
Als u de SDK wilt installeren, klikt u met de rechter muisknop op het server project in Visual Studio, selecteert u **NuGet-pakketten beheren**, zoekt u naar het [micro soft. Azure. Mobile. server] -pakket en klikt u op **installeren**.

### <a name="server-project-setup"></a>Het server project initialiseren
Een .NET-back-endserver wordt geïnitialiseerd, net als bij andere ASP.NET-projecten, door een OWIN-opstart klasse op te nemen. Zorg ervoor dat u de NuGet-pakket `Microsoft.Owin.Host.SystemWeb`hebt gerefereerd. Als u deze klasse wilt toevoegen in Visual Studio, klikt u met de rechter muisknop op uw server project en selecteert u >
**Nieuw item** **toevoegen** en vervolgens **Web** > **algemene** > **OWIN-opstart klasse**.  Een klasse wordt gegenereerd met het volgende kenmerk:

    [assembly: OwinStartup(typeof(YourServiceName.YourStartupClassName))]

Gebruik in de methode `Configuration()` van de opstart klasse OWIN een **HttpConfiguration** -object om de Azure Mobile apps-omgeving te configureren.
In het volgende voor beeld wordt het server project geïnitialiseerd zonder toegevoegde functies:

    // in OWIN startup class
    public void Configuration(IAppBuilder app)
    {
        HttpConfiguration config = new HttpConfiguration();

        new MobileAppConfiguration()
            // no added features
            .ApplyTo(config);

        app.UseWebApi(config);
    }

Als u afzonderlijke functies wilt inschakelen, moet u uitbreidings methoden aanroepen voor het object **MobileAppConfiguration** voordat u **ApplyTo**aanroept. De volgende code voegt bijvoorbeeld de standaard routes toe aan alle API-controllers met het kenmerk `[MobileAppController]` tijdens de initialisatie:

    new MobileAppConfiguration()
        .MapApiControllers()
        .ApplyTo(config);

De Quick Start van de server van de Azure Portal roept **UseDefaultConfiguration ()** aan. Dit is gelijk aan de volgende configuratie:

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

De gebruikte extensie methoden zijn:

* `AddMobileAppHomeController()` biedt de standaard introductie pagina van Azure Mobile Apps.
* `MapApiControllers()` biedt aangepaste API-mogelijkheden voor WebAPI-controllers die zijn voorzien van het kenmerk `[MobileAppController]`.
* `AddTables()` biedt een toewijzing van de `/tables`-eind punten aan tabel controllers.
* `AddTablesWithEntityFramework()` is een korte hand voor het toewijzen van de `/tables`-eind punten met behulp van Entity Framework-controllers.
* `AddPushNotifications()` biedt een eenvoudige methode voor het registreren van apparaten voor Notification Hubs.
* `MapLegacyCrossDomainController()` biedt standaard CORS-headers voor lokale ontwikkeling.

### <a name="sdk-extensions"></a>SDK-extensies
De volgende uitbreidings pakketten op basis van NuGet bieden diverse mobiele functies die door uw toepassing kunnen worden gebruikt. U schakelt uitbrei dingen in tijdens de initialisatie met behulp van het **MobileAppConfiguration** -object.

* [Micro soft. Azure. Mobile. server. Quick Start] start ondersteunt de basis Mobile apps setup. Wordt toegevoegd aan de configuratie door het aanroepen van de **UseDefaultConfiguration** -extensie methode tijdens de initialisatie. Deze uitbrei ding omvat de volgende uitbrei dingen: meldingen, verificatie, entiteit, tabellen, kruislings domein en Home-pakketten. Dit pakket wordt gebruikt door de Mobile Apps Snelstartgids die beschikbaar is op de Azure Portal.
* [Micro soft. Azure. Mobile. server. Home](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Home/) implementeert de standaard instelling voor *deze mobiele app* op de pagina voor de hoofdmap van de website. Voeg aan de configuratie toe door de **AddMobileAppHomeController** -extensie methode aan te roepen.
* [Micro soft. Azure. Mobile. server. Tables](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Tables/) bevat klassen voor het werken met gegevens en het instellen van de gegevens pijplijn. Voeg aan de configuratie toe door de **AddTables** -extensie methode aan te roepen.
* [Micro soft. Azure. Mobile. server. entity](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Entity/) maakt het Entity Framework toegang tot gegevens in de SQL database. Voeg aan de configuratie toe door de **AddTablesWithEntityFramework** -extensie methode aan te roepen.
* [Micro soft. Azure. Mobile. server. Authentication] maakt verificatie en het instellen van de OWIN-middleware die wordt gebruikt voor het valideren van tokens. Voeg aan de configuratie toe door de **AddAppServiceAuthentication** en **IAppBuilder**aan te roepen. **UseAppServiceAuthentication** -extensie methoden.
* [Micro soft. Azure. Mobile. server. meldingen] maakt push meldingen mogelijk en definieert een push registratie-eind punt. Voeg aan de configuratie toe door de **AddPushNotifications** -extensie methode aan te roepen.
* [Micro soft. Azure. Mobile. server. CrossDomain](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.CrossDomain/) maakt een controller die gegevens in verouderde webbrowsers van uw mobiele app verzendt. Voeg aan de configuratie toe door de **MapLegacyCrossDomainController** -extensie methode aan te roepen.
* [Micro soft. Azure. Mobile. server. login] biedt de methode AppServiceLoginHandler. Okta (), een statische methode die wordt gebruikt tijdens aangepaste verificatie scenario's.

## <a name="publish-server-project"></a>Procedure: het server project publiceren
In deze sectie wordt beschreven hoe u uw .NET-back-upproject publiceert vanuit Visual Studio. U kunt uw back-end-project ook implementeren met behulp van [Git](../app-service/deploy-local-git.md) of een van de andere beschik bare methoden.

1. Bouw in Visual Studio het project opnieuw op om NuGet-pakketten te herstellen.
2. Klik in Solution Explorer met de rechter muisknop op het project en vervolgens op **publiceren**. De eerste keer dat u publiceert, moet u een publicatie profiel definiëren. Wanneer u al een profiel hebt gedefinieerd, kunt u dit selecteren en klikt u op **publiceren**.
3. Als u wordt gevraagd om een publicatie doel te selecteren, klikt u op **Microsoft Azure App Service** > **volgende**en meldt u zich vervolgens aan met uw Azure-referenties.
   Met Visual Studio kunt u uw publicatie-instellingen rechtstreeks vanuit Azure downloaden en veilig opslaan.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-1.png)
4. Kies uw **abonnement**, selecteer **resource type** in **weer gave**, vouw de **mobiele app**uit en klik op de back-end van uw mobiele app en klik vervolgens op **OK**.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-2.png)
5. Controleer de informatie over het publicatie profiel en klik op **publiceren**.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-3.png)

    Wanneer de back-end van uw mobiele app is gepubliceerd, ziet u een landings pagina die het succes aangeeft.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-success.png)

## <a name="define-table-controller"></a>Procedure: een tabel controller definiëren
Definieer een tabel controller om een SQL-tabel beschikbaar te maken voor mobiele clients.  Voor het configureren van een tabel controller zijn drie stappen vereist:

1. Maak een DTO-klasse (Gegevensoverdracht object).
2. Configureer een tabel verwijzing in de mobiele DbContext-klasse.
3. Een tabel controller maken.

Een Gegevensoverdracht-object (DTO) is een C# plat object dat van `EntityData`overneemt.  Bijvoorbeeld:

    public class TodoItem : EntityData
    {
        public string Text { get; set; }
        public bool Complete {get; set;}
    }

De DTO wordt gebruikt voor het definiëren van de tabel in de SQL database.  Als u de database vermelding wilt maken, voegt u een `DbSet<>` eigenschap toe aan de DbContext die u gebruikt.  In de standaard project sjabloon voor Azure Mobile Apps heet de DbContext `Models\MobileServiceContext.cs`:

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

Als u de Azure SDK hebt geïnstalleerd, kunt u nu als volgt een controller voor de sjabloon tabel maken:

1. Klik met de rechter muisknop op de map controllers en selecteer > controller **toevoegen** **...** .
2. Selecteer de optie **Azure Mobile apps Table controller** en klik vervolgens op **toevoegen**.
3. In het dialoog venster **controller toevoegen** :
   * Selecteer in de vervolg keuzelijst **model klasse** uw nieuwe DTO.
   * Selecteer in de vervolg keuzelijst **DbContext** de DbContext-klasse voor mobiele services.
   * De naam van de controller wordt voor u gemaakt.
4. Klik op **Toevoegen**.

Het Quick Start-Server Project bevat een voor beeld van een eenvoudige **TodoItemController**.

### <a name="adjust-pagesize"></a>Procedure: de tabel paginerings grootte aanpassen
Standaard worden in azure Mobile Apps 50 records per aanvraag geretourneerd.  Paginering zorgt ervoor dat de client niet in staat is om de UI-thread of de server te lang te koppelen, waardoor een goede gebruikers ervaring wordt gegarandeerd. Als u de grootte van de tabel paginering wilt wijzigen, verhoogt u de ' toegestane query grootte ' en de pagina grootte van de client aan de server zijde ' toegestane query grootte ' wordt aangepast met behulp van het kenmerk `EnableQuery`:

    [EnableQuery(PageSize = 500)]

Zorg ervoor dat de PageSize gelijk is aan of groter is dan de grootte die door de client is aangevraagd.  Raadpleeg de specifieke client documentatie voor meer informatie over het wijzigen van de grootte van de client pagina.

## <a name="how-to-define-a-custom-api-controller"></a>Procedure: een aangepaste API-controller definiëren
De aangepaste API-controller biedt de meest eenvoudige functionaliteit voor de back-end van uw mobiele app door een eind punt weer te geven. U kunt een Mobile-specifieke API-controller registreren met het kenmerk [MobileAppController]. Met het kenmerk `MobileAppController` wordt de route geregistreerd, wordt de Mobile Apps JSON-serialisatiefunctie ingesteld en wordt [controle van de client versie](app-service-mobile-client-and-server-versioning.md)ingeschakeld.

1. Klik in Visual Studio met de rechter muisknop op de map controllers en klik vervolgens op **add** > **controller**, selecteer **Web API 2 controller&mdash;empty** en klik op **toevoegen**.
2. Geef een **naam**op voor de controller, zoals `CustomController`, en klik op **toevoegen**.
3. Voeg in het nieuwe controller-klassebestand de volgende instructie toe:

        using Microsoft.Azure.Mobile.Server.Config;
4. Pas het kenmerk **[MobileAppController]** toe op de definitie van de API-controller klasse, zoals in het volgende voor beeld:

        [MobileAppController]
        public class CustomController : ApiController
        {
              //...
        }
5. Voeg in App_Start/Startup.MobileApp.cs-bestand een aanroep toe aan de extensie methode **MapApiControllers** , zoals in het volgende voor beeld:

        new MobileAppConfiguration()
            .MapApiControllers()
            .ApplyTo(config);

U kunt ook de `UseDefaultConfiguration()` extensie methode gebruiken in plaats van `MapApiControllers()`. Een controller waarop geen **MobileAppControllerAttribute** is toegepast, kan nog steeds worden gebruikt door clients, maar is mogelijk niet correct geconsumeerd door clients die gebruikmaken van een mobiele app-client-SDK.

## <a name="how-to-work-with-authentication"></a>Procedure: werken met verificatie
Azure Mobile Apps gebruikt App Service verificatie/autorisatie om uw mobiele back-end te beveiligen.  In deze sectie wordt beschreven hoe u de volgende taken met betrekking tot authenticatie uitvoert in uw .NET back-endserver:

* [Procedure: verificatie toevoegen aan een server project](#add-auth)
* [Procedure: aangepaste verificatie gebruiken voor uw toepassing](#custom-auth)
* [Procedure: geverifieerde gebruikers gegevens ophalen](#user-info)
* [Procedure: gegevens toegang beperken voor gemachtigde gebruikers](#authorize)

### <a name="add-auth"></a>Procedure: verificatie toevoegen aan een server project
U kunt verificatie toevoegen aan uw server project door het **MobileAppConfiguration** -object uit te breiden en OWIN middleware te configureren. Wanneer u het pakket [Micro soft. Azure. Mobile. server. Quick Start] start installeert en de **UseDefaultConfiguration** -extensie methode aanroept, kunt u door gaan naar stap 3.

1. Installeer in Visual Studio het pakket [micro soft. Azure. Mobile. server. Authentication] .
2. Voeg in het project bestand Startup.cs de volgende regel code toe aan het begin van de **configuratie** methode:

        app.UseAppServiceAuthentication(config);

    Dit OWIN-middleware-onderdeel valideert tokens die zijn uitgegeven door de gekoppelde App Service gateway.
3. Voeg het kenmerk `[Authorize]` toe aan een controller of methode waarvoor verificatie is vereist.

Zie [verificatie toevoegen aan uw app](app-service-mobile-ios-get-started-users.md)voor meer informatie over het verifiëren van clients met de back-end van uw Mobile apps.

### <a name="custom-auth"></a>Procedure: aangepaste verificatie gebruiken voor uw toepassing
> [!IMPORTANT]
> Als u aangepaste verificatie wilt inschakelen, moet u eerst App Service-verificatie inschakelen zonder een provider voor uw App Service te selecteren in de Azure Portal. Hiermee schakelt u de WEBSITE_AUTH_SIGNING_KEY omgevings variabele in als deze wordt gehost.
> 
> 
> Als u een van de App Service verificatie/autorisatie providers niet wilt gebruiken, kunt u uw eigen aanmeldings systeem implementeren. Installeer het pakket [micro soft. Azure. Mobile. server. login] om te helpen bij het genereren van het verificatie token.  Geef uw eigen code op voor het valideren van gebruikers referenties. U kunt bijvoorbeeld controleren op gezouten en gehashte wacht woorden in een Data Base. In het onderstaande voor beeld is de `isValidAssertion()` methode (gedefinieerd elders) verantwoordelijk voor deze controles.

De aangepaste verificatie wordt weer gegeven door een ApiController te maken en `register` en `login` acties beschikbaar te stellen. De client moet een aangepaste gebruikers interface gebruiken om de gegevens van de gebruiker te verzamelen.  De gegevens worden vervolgens naar de API verzonden met een standaard HTTP POST-oproep. Zodra de server de bevestiging valideert, wordt er een token uitgegeven met behulp van de `AppServiceLoginHandler.CreateToken()` methode.  De ApiController **mag niet** gebruikmaken van het kenmerk `[MobileAppController]`.

Een voor beeld `login` actie:

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

In het vorige voor beeld zijn LoginResult en LoginResultUser serialiseerbare objecten die de vereiste eigenschappen weer geven. De client verwacht dat aanmeldings reacties worden geretourneerd als JSON-objecten van het formulier:

        {
            "authenticationToken": "<token>",
            "user": {
                "userId": "<userId>"
            }
        }

De `AppServiceLoginHandler.CreateToken()` methode bevat een *doel groep* en een *Issuer* -para meter. Beide para meters worden ingesteld op de URL van de hoofdmap van uw toepassing, met behulp van het HTTPS-schema. Op dezelfde manier moet u *secretKey* instellen als de waarde van de handtekening sleutel van uw toepassing. Distribueer de handtekening sleutel niet in een client omdat deze kan worden gebruikt om sleutels te licht en gebruikers te imiteren. U kunt de handtekening sleutel verkrijgen tijdens het gehost in App Service door te verwijzen naar de *WEBSITE\_AUTH\_ondertekening\_sleutel* omgevings variabele. Als dat nodig is in de context van een lokale fout opsporing, volgt u de instructies in de sectie [lokale fout opsporing met verificatie](#local-debug) om de sleutel op te halen en op te slaan als een toepassings instelling.

Het gepubliceerde token kan ook andere claims en een verval datum omvatten.  Het gepubliceerde token moet mini maal een subject (**Sub**)-claim bevatten.

U kunt de standaard client `loginAsync()` methode ondersteunen door de verificatie route te overbelasten.  Als de client `client.loginAsync('custom');` aanroept om zich aan te melden, moet uw route `/.auth/login/custom`zijn.  U kunt de route voor de aangepaste verificatie controller instellen met behulp van `MapHttpRoute()`:

    config.Routes.MapHttpRoute("custom", ".auth/login/custom", new { controller = "CustomAuth" });

> [!TIP]
> Met behulp van de `loginAsync()` aanpak zorgt u ervoor dat het verificatie token is gekoppeld aan elke volgende aanroep van de service.
>
>

### <a name="user-info"></a>Procedure: geverifieerde gebruikers gegevens ophalen
Wanneer een gebruiker wordt geverifieerd door App Service, hebt u toegang tot de toegewezen gebruikers-ID en andere informatie in uw .NET-back-end-code. De gebruikers informatie kan worden gebruikt om autorisatie beslissingen te nemen in de back-end. Met de volgende code wordt de gebruikers-ID opgehaald die aan een aanvraag is gekoppeld:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

De SID is afgeleid van de provider-specifieke gebruikers-ID en is statisch voor een bepaalde gebruiker en aanmeldings provider.  De SID is null voor ongeldige verificatie tokens.

Met App Service kunt u ook specifieke claims van uw aanmeldings provider aanvragen. Elke id-provider kan meer informatie geven met de SDK voor de ID-provider.  U kunt bijvoorbeeld de Facebook-Graph API voor vrienden-informatie gebruiken.  U kunt claims opgeven die worden aangevraagd op de Blade van de provider in de Azure Portal. Voor sommige claims is aanvullende configuratie met de ID-provider vereist.

Met de volgende code wordt de **GetAppServiceIdentityAsync** -extensie methode aangeroepen om de aanmeldings referenties op te halen, waaronder het toegangs token dat nodig is voor het uitvoeren van aanvragen voor de Facebook Graph API:

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

Voeg een using-instructie toe voor `System.Security.Principal` om de **GetAppServiceIdentityAsync** -extensie methode op te geven.

### <a name="authorize"></a>Procedure: gegevens toegang beperken voor gemachtigde gebruikers
In de vorige sectie hebt u geleerd hoe u de gebruikers-ID van een geverifieerde gebruiker kunt ophalen. U kunt de toegang tot gegevens en andere bronnen beperken op basis van deze waarde. U kunt bijvoorbeeld een kolom GebruikersID toevoegen aan tabellen en de query resultaten filteren op basis van de gebruikers-ID. Dit is een eenvoudige manier om geretourneerde gegevens alleen te beperken tot gemachtigde gebruikers. De volgende code retourneert alleen gegevens rijen wanneer de SID overeenkomt met de waarde in de kolom UserId in de tabel TodoItem:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

    // Only return data rows that belong to the current user.
    return Query().Where(t => t.UserId == sid);

De methode `Query()` retourneert een `IQueryable` die door LINQ kan worden gemanipuleerd voor het afhandelen van filters.

## <a name="how-to-add-push-notifications-to-a-server-project"></a>Procedure: Push meldingen toevoegen aan een server project
Voeg push meldingen toe aan uw server project door het **MobileAppConfiguration** -object uit te breiden en een notification hubs-client te maken.

1. Klik in Visual Studio met de rechter muisknop op het server project en klik op **NuGet-pakketten beheren**, zoek naar `Microsoft.Azure.Mobile.Server.Notifications`en klik vervolgens op **installeren**.
2. Herhaal deze stap om het `Microsoft.Azure.NotificationHubs`-pakket te installeren, met inbegrip van de Notification Hubs-client bibliotheek.
3. In App_Start/Startup.MobileApp.cs en voegt u tijdens de initialisatie een aanroep toe aan de extensie methode **AddPushNotifications ()** :

        new MobileAppConfiguration()
            // other features...
            .AddPushNotifications()
            .ApplyTo(config);
4. Voeg de volgende code toe waarmee een Notification Hubs-client wordt gemaakt:

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

U kunt nu de Notification Hubs-client gebruiken om Push meldingen naar geregistreerde apparaten te verzenden. Zie [Push meldingen toevoegen aan uw app](app-service-mobile-ios-get-started-push.md)voor meer informatie. Zie [Notification hubs Overview](../notification-hubs/notification-hubs-push-notification-overview.md)voor meer informatie over notification hubs.

## <a name="tags"></a>Procedure: gerichte Push inschakelen met behulp van Tags
Met Notification Hubs kunt u gerichte meldingen naar specifieke registraties verzenden met behulp van tags. Er worden automatisch diverse Tags gemaakt:

* De installatie-ID identificeert een specifiek apparaat.
* De gebruikers-id op basis van de geverifieerde SID identificeert een specifieke gebruiker.

U kunt toegang krijgen tot de installatie-ID vanuit de eigenschap **installationId** van de **mobileserviceclient te maken**.  In het volgende voor beeld ziet u hoe u een installatie-ID gebruikt om een tag toe te voegen aan een specifieke installatie in Notification Hubs:

    hub.PatchInstallation("my-installation-id", new[]
    {
        new PartialUpdateOperation
        {
            Operation = UpdateOperationType.Add,
            Path = "/tags",
            Value = "{my-tag}"
        }
    });

Labels die door de client worden verstrekt tijdens de registratie van push meldingen, worden door de back-end genegeerd tijdens het maken van de installatie. Als u een client in staat wilt stellen tags toe te voegen aan de installatie, moet u een aangepaste API maken waarmee labels worden toegevoegd met behulp van het voor gaande patroon.

Zie door [client toegevoegde Tags voor push meldingen][5] in het app service Mobile apps voltooide Snelstartgids voor een voor beeld.

## <a name="push-user"></a>Procedure: Push meldingen naar een geverifieerde gebruiker verzenden
Wanneer een geverifieerde gebruiker zich aanmeldt voor push meldingen, wordt er automatisch een label voor de gebruikers-ID toegevoegd aan de registratie. Met deze tag kunt u push meldingen verzenden naar alle apparaten die zijn geregistreerd door deze persoon. Met de volgende code wordt de SID van de gebruiker die de aanvraag maakt, opgehaald en wordt een sjabloon push melding verzonden naar elke apparaatregistratie voor die persoon:

    // Get the current user SID and create a tag for the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;
    string userTag = "_UserId:" + sid;

    // Build a dictionary for the template with the item message text.
    var notification = new Dictionary<string, string> { { "message", item.Text } };

    // Send a template notification to the user ID.
    await hub.SendTemplateNotificationAsync(notification, userTag);

Wanneer u registreert voor push meldingen van een geverifieerde client, moet u ervoor zorgen dat de verificatie is voltooid voordat u een registratie uitvoert. Zie voor meer informatie [pushen naar gebruikers][6] in het app service Mobile apps voltooide Snelstartgids voor .net-back-end.

## <a name="how-to-debug-and-troubleshoot-the-net-server-sdk"></a>Procedure: fouten opsporen in de .NET Server SDK en problemen oplossen
Azure App Service biedt verschillende technieken voor fout opsporing en probleem oplossing voor ASP.NET-toepassingen:

* [Een Azure App Service bewaken](../app-service/web-sites-monitor.md)
* [Diagnostische logboek registratie inschakelen in Azure App Service](../app-service/troubleshoot-diagnostic-logs.md)
* [Problemen met een Azure App Service in Visual Studio oplossen](../app-service/troubleshoot-dotnet-visual-studio.md)

### <a name="logging"></a>Logboekregistratie
U kunt schrijven naar App Service Diagnostische logboeken met behulp van de standaard ASP.NET tracering schrijven. Voordat u naar de logboeken kunt schrijven, moet u Diagnostische gegevens inschakelen in de back-end van uw mobiele app.

Diagnostische gegevens inschakelen en schrijven naar de logboeken:

1. Volg de stappen in [toepassings logboeken inschakelen (Windows)](../app-service/troubleshoot-diagnostic-logs.md#enable-application-logging-windows).
2. Voeg de volgende using-instructie toe aan het code bestand:

        using System.Web.Http.Tracing;
3. Maak als volgt een tracerings schrijver om te schrijven van de .NET-back-end naar de diagnostische logboeken:

        ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
        traceWriter.Info("Hello, World");
4. Publiceer uw server project opnieuw en open de back-end van de mobiele app om het codepad uit te voeren met de logboek registratie.
5. Down load en evalueer de logboeken, zoals beschreven in [logboek bestanden van Access](../app-service/troubleshoot-diagnostic-logs.md#access-log-files).

### <a name="local-debug"></a>Lokale fout opsporing met verificatie
U kunt uw toepassing lokaal uitvoeren om wijzigingen te testen voordat u ze naar de Cloud publiceert. Druk voor de meeste Azure Mobile Apps-back-ends in Visual Studio op *F5* . Er zijn echter enkele aanvullende overwegingen bij het gebruik van authenticatie.

U moet een mobiele app op de Cloud hebben met App Service verificatie/autorisatie is geconfigureerd en uw client moet het Cloud eindpunt opgeven dat is opgegeven als de alternatieve aanmeldings-host. Raadpleeg de documentatie voor uw client platform voor de specifieke stappen die vereist zijn.

Zorg ervoor dat op uw mobiele back-end [micro soft. Azure. Mobile. server. Authentication] is geïnstalleerd. Voeg vervolgens in de opstart klasse van uw toepassing de volgende toe, nadat `MobileAppConfiguration` is toegepast op uw `HttpConfiguration`:

        app.UseAppServiceAuthentication(new AppServiceAuthenticationOptions()
        {
            SigningKey = ConfigurationManager.AppSettings["authSigningKey"],
            ValidAudiences = new[] { ConfigurationManager.AppSettings["authAudience"] },
            ValidIssuers = new[] { ConfigurationManager.AppSettings["authIssuer"] },
            TokenHandler = config.GetAppServiceTokenHandler()
        });

In het voor gaande voor beeld moet u de toepassings instellingen *authAudience* en *authIssuer* in het bestand Web. config configureren op elk de URL van de hoofdmap van uw toepassing, met behulp van het HTTPS-schema. Op dezelfde manier moet u *authSigningKey* instellen als de waarde van de handtekening sleutel van uw toepassing.
De handtekening sleutel verkrijgen:

1. Navigeer naar uw app binnen het [Azure-portal]
2. Klik op **extra**, **kudu**en **Ga naar**.
3. Klik in de kudu-beheer site op **omgeving**.
4. Zoek de waarde voor *WEBSITE\_AUTH\_ondertekenen\_sleutel*.

Gebruik de handtekening sleutel voor de para meter *authSigningKey* in de configuratie van uw lokale toepassing.  Uw mobiele back-end is nu uitgerust met het valideren van tokens bij het lokaal uitvoeren, waarbij de client het token van het eind punt op de Cloud verkrijgt.

[1]: https://msdn.microsoft.com/library/azure/dn961176.aspx
[2]: https://github.com/Azure/azure-mobile-apps-net-server
[3]: app-service-mobile-ios-get-started.md
[4]: https://azure.microsoft.com/downloads/
[5]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#client-added-push-notification-tags
[6]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#push-to-users
[Azure-portal]: https://portal.azure.com
[NuGet.org]: https://www.nuget.org/
[Micro soft. Azure. Mobile. server]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/
[Micro soft. Azure. Mobile. server. Quick Start]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Quickstart/
[Micro soft. Azure. Mobile. server. Authentication]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/
[Micro soft. Azure. Mobile. server. login]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Login/
[Micro soft. Azure. Mobile. server. meldingen]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Notifications/
[MapHttpAttributeRoutes]: https://msdn.microsoft.com/library/dn479134(v=vs.118).aspx
