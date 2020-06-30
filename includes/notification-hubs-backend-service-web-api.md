---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: f0e83548d3ba3b353b471e2e3429a23421aec7b2
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/19/2020
ms.locfileid: "85095299"
---
### <a name="create-a-web-project"></a>Een webproject maken

1. In **Visual Studio** selecteert u **Bestand** > **Nieuwe oplossing**.

1. Selecteer **.NET Core** > **App** > **ASP.NET Core** > **API** > **Volgende**.
  
1. Selecteer in het dialoogvenster **Uw nieuwe ASP.NET Core Web-API configureren** het **Doel-framework** van **.NET Core 3.1**.

1. Voer *PushDemoApi* in voor de **Projectnaam** en selecteer **Maken**.

1. Start de foutopsporing (**Command** + **Enter**) om de sjabloon-app te testen.

    > [!NOTE]
    > De sjabloon-app is geconfigureerd voor het gebruik van de **WeatherForecastController** als de *launchUrl*. Dit wordt ingesteld in **eigenschappen** > **launchSettings.json**.  
    >
    > Als u het bericht **Ongeldig ontwikkelcertificaat gevonden** te zien krijgt:
    >
    > 1. Klik op **Ja** om het hulpprogramma 'dotnet dev-certs https' uit te voeren om dit op te lossen. Het hulpprogramma 'dotnet dev-certs https' vraagt u vervolgens om een wachtwoord in te voeren voor het certificaat en om het wachtwoord van uw sleutelhanger.
    >
    > 1. Klik op **Ja** wanneer u wordt gevraagd **Het nieuwe certificaat installeren en vertrouwen** en voer vervolgens het wachtwoord voor uw sleutelhanger in.

1. Vouw de map **Controllers** uit en verwijder vervolgens **WeatherForecastController.cs**.

1. Verwijder **WeatherForecast.cs**.

1. **Control** +  **-klik** op het project **PushDemoApi** en kies **Nieuw bestand...** in het menu **Toevoegen**.

1. Stel lokale configuratiewaarden in met het hulpprogramma [Geheime beheerder](https://docs.microsoft.com/aspnet/core/security/app-secrets?view=aspnetcore-3.1&tabs=linux#secret-manager). Als u de geheimen loskoppelt van de oplossing, zorgt u ervoor dat ze niet in broncodebeheer terechtkomen. Open **Terminal** en ga naar de map van het projectbestand en voer de volgende opdrachten uit:

    ```bash
    dotnet user-secrets init
    dotnet user-secrets set "NotificationHub:Name" <value>
    dotnet user-secrets set "NotificationHub:ConnectionString" <value>
    ```

    Vervang de waarden van de tijdelijke aanduiding door de naam van uw eigen meldingshub en waarden voor de verbindingstekenreeks. U hebt ze genoteerd in het gedeelte [een meldingshub maken](#create-a-notification-hub). Anders kunt u ze opzoeken in [Azure](https://portal.azure.com).

    **NotificationsHub:Name**:  
    Zie *Naam* in de samenvatting **Essentials** bovenaan **Overzicht**.  

    **NotificationHub:ConnectionString**:  
    Zie *DefaultFullSharedAccessSignature* in **Toegangsbeleid**

    > [!NOTE]
    > Voor productiescenario's kunt u opties zoals [Azure KeyVault](https://azure.microsoft.com/services/key-vault) bekijken om de verbindingstekenreeks veilig op te slaan. Ter vereenvoudiging worden de geheimen toegevoegd aan de toepassingsinstellingen van [Azure App Service](https://azure.microsoft.com/services/app-service/).

### <a name="authenticate-clients-using-an-api-key-optional"></a>Clients verifiëren met een API-sleutel (optioneel)

API-sleutels zijn niet zo veilig als tokens, maar zijn wel voldoende voor de doeleinden van deze zelfstudie. Een API-sleutel kan eenvoudig worden geconfigureerd via de [ASP.NET Middleware](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/?view=aspnetcore-3.1).

1. Voeg de **API-sleutel** toe aan de lokale configuratiewaarden.

    ```bash
    dotnet user-secrets set "Authentication:ApiKey" <value>
    ```

    > [!NOTE]
    > Vervang de waarde voor de tijdelijke aanduiding door uw eigen waarde en noteer deze.

1. **Control** +  **-klik** op het project **PushDemoApi**, kies **Nieuwe map** in het menu **Toevoegen**, klik vervolgens op **Toevoegen** met *Verificatie* als de **Mapnaam**.

1. **Control** +  **-klik** op de map **Verificatie** en kies vervolgens **Nieuw bestand...** in het menu **Toevoegen**.

1. Selecteer **Algemeen** > **Lege klasse**, voer *ApiKeyAuthOptions.cs* in voor de **Naam** en klik vervolgens op **Nieuw** om de volgende implementatie toe te voegen.

    ```csharp
    using Microsoft.AspNetCore.Authentication;

    namespace PushDemoApi.Authentication
    {
        public class ApiKeyAuthOptions : AuthenticationSchemeOptions
        {
            public const string DefaultScheme = "ApiKey";
            public string Scheme => DefaultScheme;
            public string ApiKey { get; set; }
        }
    }
    ```

1. Voeg nog een **Lege klasse** toe aan de map **Verificatie** met de naam *ApiKeyAuthHandler.cs* en voeg vervolgens de volgende implementatie toe.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Security.Claims;
    using System.Text.Encodings.Web;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Authentication;
    using Microsoft.Extensions.Logging;
    using Microsoft.Extensions.Options;

    namespace PushDemoApi.Authentication
    {
        public class ApiKeyAuthHandler : AuthenticationHandler<ApiKeyAuthOptions>
        {
            const string ApiKeyIdentifier = "apikey";

            public ApiKeyAuthHandler(
                IOptionsMonitor<ApiKeyAuthOptions> options,
                ILoggerFactory logger,
                UrlEncoder encoder,
                ISystemClock clock)
                : base(options, logger, encoder, clock) {}

            protected override Task<AuthenticateResult> HandleAuthenticateAsync()
            {
                string key = string.Empty;

                if (Request.Headers[ApiKeyIdentifier].Any())
                {
                    key = Request.Headers[ApiKeyIdentifier].FirstOrDefault();
                }
                else if (Request.Query.ContainsKey(ApiKeyIdentifier))
                {
                    if (Request.Query.TryGetValue(ApiKeyIdentifier, out var queryKey))
                        key = queryKey;
                }

                if (string.IsNullOrWhiteSpace(key))
                    return Task.FromResult(AuthenticateResult.Fail("No api key provided"));

                if (!string.Equals(key, Options.ApiKey, StringComparison.Ordinal))
                    return Task.FromResult(AuthenticateResult.Fail("Invalid api key."));

                var identities = new List<ClaimsIdentity> {
                    new ClaimsIdentity("ApiKeyIdentity")
                };

                var ticket = new AuthenticationTicket(
                    new ClaimsPrincipal(identities), Options.Scheme);

                return Task.FromResult(AuthenticateResult.Success(ticket));
            }
        }
    }
    ```

    > [!NOTE]
    > Een [Verificatiehandler](https://docs.microsoft.com/aspnet/core/security/authentication/?view=aspnetcore-3.1#authentication-handler) is een type dat het gedrag van een schema implementeert, in dit geval een aangepast API-sleutelschema.

1. Voeg nog een **Lege klasse** toe aan de map **Verificatie** met de naam *ApiKeyAuthenticationBuilderExtensions.cs* en voeg vervolgens de volgende implementatie toe.

    ```csharp
    using System;
    using Microsoft.AspNetCore.Authentication;

    namespace PushDemoApi.Authentication
    {
        public static class AuthenticationBuilderExtensions
        {
            public static AuthenticationBuilder AddApiKeyAuth(
                this AuthenticationBuilder builder,
                Action<ApiKeyAuthOptions> configureOptions)
            {
                return builder
                    .AddScheme<ApiKeyAuthOptions, ApiKeyAuthHandler>(
                        ApiKeyAuthOptions.DefaultScheme,
                        configureOptions);
            }
        }
    }
    ```

    > [!NOTE]
    > Met deze uitbreidingsmethode vereenvoudigt u de middleware-configuratiecode in **Startup.cs**, waardoor deze beter leesbaar en eenvoudiger te volgen wordt.

1. Werk in **Startup.cs**de methode **ConfigureServices** bij om de API-sleutelverificatie onder de aanroep van de methode **services.AddControllers** te configureren.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddControllers();

        services.AddAuthentication(options =>
        {
            options.DefaultAuthenticateScheme = ApiKeyAuthOptions.DefaultScheme;
            options.DefaultChallengeScheme = ApiKeyAuthOptions.DefaultScheme;
        }).AddApiKeyAuth(Configuration.GetSection("Authentication").Bind);
    }
    ```

1. Werk in **Startup.cs** de methode **Configureren** bij voor het aanroepen van de uitbreidingsmethoden **UseAuthentication** en **UseAuthorization** op de **IApplicationBuilder** van de app. Zorg ervoor dat deze methoden worden aangeroepen na **UseRouting** en vóór **app.UseEndpoints**.

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        app.UseHttpsRedirection();

        app.UseRouting();

        app.UseAuthentication();

        app.UseAuthorization();

        app.UseEndpoints(endpoints =>
        {
            endpoints.MapControllers();
        });
    }
    ```

    > [!NOTE]
    > Het aanroepen van **UseAuthentication** registreert de middleware die gebruikmaakt van de eerder geregistreerde verificatieschema's (van **ConfigureServices**). Dit moet worden aangeroepen vóór een middleware die afhankelijk is van gebruikers die worden geverifieerd.

### <a name="add-dependencies-and-configure-services"></a>Afhankelijkheden toevoegen en services configureren

ASP.NET Core ondersteunt het softwareontwerppatroon [dependency injection (DI)](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-3.1). Dit is een techniek voor het bereiken van [Inversion of Control (IoC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) tussen klassen en hun afhankelijkheden.  

Het gebruik van de meldingshub en de [Notification Hubs SDK voor back-endbewerkingen](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) is ingekapseld in een service. De service wordt geregistreerd en beschikbaar gesteld via een geschikte abstractie.

1. **Control** +  **-klik** op de map **Afhankelijkheden** en kies **NuGet-pakketten beheren...** .

1. Zoek naar **Microsoft.Azure.NotificationHubs** en controleer of deze is ingeschakeld.

1. Klik op **Pakketten toevoegen** en klik vervolgens op **Accepteren** wanneer u wordt gevraagd om de licentievoorwaarden te accepteren.

1. **Control** +  **-klik** op het project **PushDemoApi**, kies **Nieuwe map** in het menu **Toevoegen** en klik vervolgens op **Toevoegen** met *Modellen* als de **Mapnaam**.

1. **Beheren** + **Klik** in de map **Modellen** en kies vervolgens **Nieuw bestand...** vanuit het menu **Toevoegen**.

1. Selecteer **Algemeen** > **Lege klasse**, voer *PushTemplates.cs* in voor de **Naam** en klik vervolgens op **Nieuw** om de volgende implementatie toe te voegen.

    ```csharp
    namespace PushDemoApi.Models
    {
        public class PushTemplates
        {
            public class Generic
            {
                public const string Android = "{ \"notification\": { \"title\" : \"PushDemo\", \"body\" : \"$(alertMessage)\"}, \"data\" : { \"action\" : \"$(alertAction)\" } }";
                public const string iOS = "{ \"aps\" : {\"alert\" : \"$(alertMessage)\"}, \"action\" : \"$(alertAction)\" }";
            }

            public class Silent
            {
                public const string Android = "{ \"data\" : {\"message\" : \"$(alertMessage)\", \"action\" : \"$(alertAction)\"} }";
                public const string iOS = "{ \"aps\" : {\"content-available\" : 1, \"apns-priority\": 5, \"sound\" : \"\", \"badge\" : 0}, \"message\" : \"$(alertMessage)\", \"action\" : \"$(alertAction)\" }";
            }
        }
    }
    ```

    > [!NOTE]
    > Deze klasse bevat de meldingspayloads met tokens voor algemene en stille meldingen die vereist zijn voor dit scenario. De payloads worden gedefinieerd buiten de [Installatie](https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.installation?view=azure-dotnet) om experimenteren mogelijk te maken zonder dat bestaande installaties moeten worden bijgewerkt via de service. Het op deze manier afhandelen van wijzigingen in installaties valt buiten het bereik van deze zelfstudie. Voor productie kunt u [Aangepaste sjablonen](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-templates-cross-platform-push-messages) overwegen.

1. Selecteer **Algemeen** > **Lege klasse**, voer *DeviceInstallation.cs* in voor de **Naam** en klik vervolgens op **Nieuw** om de volgende implementatie toe te voegen.

    ```csharp
    using System.Collections.Generic;
    using System.ComponentModel.DataAnnotations;

    namespace PushDemoApi.Models
    {
        public class DeviceInstallation
        {
            [Required]
            public string InstallationId { get; set; }

            [Required]
            public string Platform { get; set; }

            [Required]
            public string PushChannel { get; set; }

            public IList<string> Tags { get; set; } = Array.Empty<string>();
        }
    }
    ```

1. Voeg nog een **Lege klasse** toe aan de map **Modellen** met de naam *NotificationRequest.cs* en voeg vervolgens de volgende implementatie toe.

    ```csharp
    using System;

    namespace PushDemoApi.Models
    {
        public class NotificationRequest
        {
            public string Text { get; set; }
            public string Action { get; set; }
            public string[] Tags { get; set; } = Array.Empty<string>();
            public bool Silent { get; set; }
        }
    }
    ```

1. Voeg nog een **Lege klasse** toe aan de map **Modellen** met de naam *NotificationHubOptions.cs* en voeg vervolgens de volgende implementatie toe.

    ```csharp
    using System.ComponentModel.DataAnnotations;

    namespace PushDemoApi.Models
    {
        public class NotificationHubOptions
        {
            [Required]
            public string Name { get; set; }

            [Required]
            public string ConnectionString { get; set; }
        }
    }
    ```

1. Voeg een nieuwe map toe aan het project **PushDemoApi** met de naam *Services*.

1. Voeg een **Lege interface** toe aan de map **Services** met de naam *INotificationService.cs* en voeg vervolgens de volgende implementatie toe.

    ```csharp
    using System.Threading.Tasks;
    using PushDemoApi.Models;

    namespace PushDemoApi.Services
    {
        public interface INotificationService
        {
            Task<bool> CreateOrUpdateInstallationAsync(DeviceInstallation deviceInstallation, CancellationToken token);
            Task<bool> DeleteInstallationByIdAsync(string installationId, CancellationToken token);
            Task<bool> RequestNotificationAsync(NotificationRequest notificationRequest, CancellationToken token);
        }
    }
    ```

1. Voeg een **Lege klasse** toe aan de map **Services** met de naam *NotificationHubsService.cs* en voeg vervolgens de volgende code toe om de interface **INotificationService** te implementeren:

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.NotificationHubs;
    using Microsoft.Extensions.Logging;
    using Microsoft.Extensions.Options;
    using PushDemoApi.Models;

    namespace PushDemoApi.Services
    {
        public class NotificationHubService : INotificationService
        {
            readonly NotificationHubClient _hub;
            readonly Dictionary<string, NotificationPlatform> _installationPlatform;
            readonly ILogger<NotificationHubService> _logger;

            public NotificationHubService(IOptions<NotificationHubOptions> options, ILogger<NotificationHubService> logger)
            {
                _logger = logger;
                _hub = NotificationHubClient.CreateClientFromConnectionString(
                    options.Value.ConnectionString,
                    options.Value.Name);

                _installationPlatform = new Dictionary<string, NotificationPlatform>
                {
                    { nameof(NotificationPlatform.Apns).ToLower(), NotificationPlatform.Apns },
                    { nameof(NotificationPlatform.Fcm).ToLower(), NotificationPlatform.Fcm }
                };
            }

            public async Task<bool> CreateOrUpdateInstallationAsync(DeviceInstallation deviceInstallation, CancellationToken token)
            {
                if (string.IsNullOrWhiteSpace(deviceInstallation?.InstallationId) ||
                    string.IsNullOrWhiteSpace(deviceInstallation?.Platform) ||
                    string.IsNullOrWhiteSpace(deviceInstallation?.PushChannel))
                    return false;

                var installation = new Installation()
                {
                    InstallationId = deviceInstallation.InstallationId,
                    PushChannel = deviceInstallation.PushChannel,
                    Tags = deviceInstallation.Tags
                };

                if (_installationPlatform.TryGetValue(deviceInstallation.Platform, out var platform))
                    installation.Platform = platform;
                else
                    return false;

                try
                {
                    await _hub.CreateOrUpdateInstallationAsync(installation, token);
                }
                catch
                {
                    return false;
                }

                return true;
            }

            public async Task<bool> DeleteInstallationByIdAsync(string installationId, CancellationToken token)
            {
                if (string.IsNullOrWhiteSpace(installationId))
                    return false;

                try
                {
                    await _hub.DeleteInstallationAsync(installationId, token);
                }
                catch
                {
                    return false;
                }

                return true;
            }

            public async Task<bool> RequestNotificationAsync(NotificationRequest notificationRequest, CancellationToken token)
            {
                if ((notificationRequest.Silent &&
                    string.IsNullOrWhiteSpace(notificationRequest?.Action)) ||
                    (!notificationRequest.Silent &&
                    (string.IsNullOrWhiteSpace(notificationRequest?.Text)) ||
                    string.IsNullOrWhiteSpace(notificationRequest?.Action)))
                    return false;

                var androidPushTemplate = notificationRequest.Silent ?
                    PushTemplates.Silent.Android :
                    PushTemplates.Generic.Android;

                var iOSPushTemplate = notificationRequest.Silent ?
                    PushTemplates.Silent.iOS :
                    PushTemplates.Generic.iOS;

                var androidPayload = PrepareNotificationPayload(
                    androidPushTemplate,
                    notificationRequest.Text,
                    notificationRequest.Action);

                var iOSPayload = PrepareNotificationPayload(
                    iOSPushTemplate,
                    notificationRequest.Text,
                    notificationRequest.Action);

                try
                {
                    if (notificationRequest.Tags.Length == 0)
                    {
                        // This will broadcast to all users registered in the notification hub
                        await SendPlatformNotificationsAsync(androidPayload, iOSPayload, token);
                    }
                    else if (notificationRequest.Tags.Length <= 20)
                    {
                        await SendPlatformNotificationsAsync(androidPayload, iOSPayload, notificationRequest.Tags, token);
                    }
                    else
                    {
                        var notificationTasks = notificationRequest.Tags
                            .Select((value, index) => (value, index))
                            .GroupBy(g => g.index / 20, i => i.value)
                            .Select(tags => SendPlatformNotificationsAsync(androidPayload, iOSPayload, tags, token));

                        await Task.WhenAll(notificationTasks);
                    }

                    return true;
                }
                catch (Exception e)
                {
                    _logger.LogError(e, "Unexpected error sending notification");
                    return false;
                }
            }

            string PrepareNotificationPayload(string template, string text, string action) => template
                .Replace("$(alertMessage)", text, StringComparison.InvariantCulture)
                .Replace("$(alertAction)", action, StringComparison.InvariantCulture);

            Task SendPlatformNotificationsAsync(string androidPayload, string iOSPayload, CancellationToken token)
            {
                var sendTasks = new Task[]
                {
                    _hub.SendFcmNativeNotificationAsync(androidPayload, token),
                    _hub.SendAppleNativeNotificationAsync(iOSPayload, token)
                };

                return Task.WhenAll(sendTasks);
            }

            Task SendPlatformNotificationsAsync(string androidPayload, string iOSPayload, IEnumerable<string> tags, CancellationToken token)
            {
                var sendTasks = new Task[]
                {
                    _hub.SendFcmNativeNotificationAsync(androidPayload, tags, token),
                    _hub.SendAppleNativeNotificationAsync(iOSPayload, tags, token)
                };

                return Task.WhenAll(sendTasks);
            }
        }
    }
    ```

    > [!NOTE]
    > De tagexpressie die is doorgegeven aan **SendTemplateNotificationAsync** is beperkt tot 20 tags. Het is beperkt tot 6 voor de meeste operators, maar de expressie bevat in dit geval alleen OR's (||). Als de aanvraag meer dan 20 tags bevat, moeten deze in meerdere aanvragen worden opgesplitst. Raadpleeg de documentatie [Routering en tagexpressie](https://msdn.microsoft.com/library/azure/Dn530749.aspx?f=255&MSPPError=-2147217396) voor meer informatie.

1. Werk in **Startup.cs** de methode **ConfigureServices** bij om de **NotificationHubsService** toe te voegen als een singleton-implementatie van **INotificationService**.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        ...

        services.AddSingleton<INotificationService, NotificationHubService>();

        services.AddOptions<NotificationHubOptions>()
            .Configure(Configuration.GetSection("NotificationHub").Bind)
            .ValidateDataAnnotations();
    }
    ```

### <a name="create-the-notifications-api"></a>De meldingen-API maken

1. **Control** +  **-klik** op de map **Controllers** en kies **Nieuw bestand...** in het menu **Toevoegen**.

1. Selecteer **ASP.NET Core** > **Web-API-controllerklasse**, voer *NotificationsController* in voor de **Naam** en klik vervolgens op **Nieuw**.

1. Voeg de volgende naamruimten toe aan de bovenkant van het bestand.

    ```csharp
    using System.ComponentModel.DataAnnotations;
    using System.Net;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Authorization;
    using Microsoft.AspNetCore.Mvc;
    using PushDemoApi.Models;
    using PushDemoApi.Services;
    ```

1. Werk de sjablooncontroller zo bij dat deze is afgeleid van **ControllerBase** en is voorzien van het kenmerk **ApiController**.

    ```cs
    [ApiController]
    [Route("api/[controller]")]
    public class NotificationsController : ControllerBase
    {
        // Templated methods here
    }
    ```

    > [!NOTE]
    > De basisklasse **Controller** biedt ondersteuning voor weergaven, maar dit is niet nodig in dit geval en **ControllerBase** kan in plaats daarvan worden gebruikt.

1. Als u ervoor hebt gekozen om het gedeelte [Clients met een API-sleutel verifiëren](#authenticate-clients-using-an-api-key-optional) te voltooien, moet u de **NotificationsController** voorzien van het kenmerk **Autoriseren**.

    ```cs
    [Authorize]
    ```

1. Werk de constructor bij om de geregistreerde instantie van **INotificationService** te accepteren als een argument en wijs deze toe aan een alleen-lezenlid.

    ```cs
    readonly INotificationService _notificationService;

    public NotificationsController(INotificationService notificationService)
    {
        _notificationService = notificationService;
    }
    ```

1. Wijzig in **launchSettings.json** (in de map **Eigenschappen**) de **launchUrl** van `weatherforecast` naar *api/meldingen*, zodat deze overeenkomt met de URL die is opgegeven in het kenmerk **RegistrationsController** **Route**.

1. Start de foutopsporing (**Command** + **Enter**) om te valideren dat de app werkt met de nieuwe **NotificationsController** en een status **401 Niet-geautoriseerd** retourneert.

    > [!NOTE]
    > Visual Studio start de app mogelijk niet automatisch in de browser. U gebruikt [Postman](https://www.postman.com/downloads) om de API vanaf dit moment te testen.

1. Stel op een nieuw **[Postman](https://www.postman.com/downloads)** -tab de aanvraag in op **GET** en voer het onderstaande adres in.

    ```bash
    https://localhost:5001/api/notifications
    ```

    > [!NOTE]
    > Het localhost-adres moet overeenkomen met de waarde **applicationUrl** die u kunt vinden in **Eigenschappen** > **launchSettings.json**. De standaardwaarde moet `https://localhost:5001;http://localhost:5000` zijn, maar dit is echter iets om te controleren als u een 404-antwoord ontvangt.

1. Als u ervoor hebt gekozen om het gedeelte [Clients verifiëren met behulp van een API-sleutel](#authenticate-clients-using-an-api-key-optional) te voltooien, moet u de aanvraagheaders zo configureren dat deze uw **apikey**-waarde bevatten.

   | Sleutel                            | Waarde                          |
   | ------------------------------ | ------------------------------ |
   | apikey                         | <your_api_key>                 |

1. Klik op de knop **Verzenden**.

    > [!NOTE]
    > U ontvangt een status **200 OK** met enige **JSON**-inhoud.
    >
    > Als u een waarschuwing voor **Verificatie van SSL-certificaat** ontvangt, kunt u de instelling verificatie van het SSL-certificaat voor aanvragen **[Postman](https://www.postman.com/downloads)** uitschakelen in **Instellingen**.

1. Vervang de methoden van de sjabloonklasse door de volgende code.

    ```csharp
    [HttpPut]
    [Route("installations")]
    [ProducesResponseType((int)HttpStatusCode.OK)]
    [ProducesResponseType((int)HttpStatusCode.BadRequest)]
    [ProducesResponseType((int)HttpStatusCode.UnprocessableEntity)]
    public async Task<IActionResult> UpdateInstallation(
        [Required]DeviceInstallation deviceInstallation)
    {
        var success = await _notificationService
            .CreateOrUpdateInstallationAsync(deviceInstallation, HttpContext.RequestAborted);

        if (!success)
            return new UnprocessableEntityResult();

        return new OkResult();
    }

    [HttpDelete()]
    [Route("installations/{installationId}")]
    [ProducesResponseType((int)HttpStatusCode.OK)]
    [ProducesResponseType((int)HttpStatusCode.BadRequest)]
    [ProducesResponseType((int)HttpStatusCode.UnprocessableEntity)]
    public async Task<ActionResult> DeleteInstallation(
        [Required][FromRoute]string installationId)
    {
        var success = await _notificationService
            .DeleteInstallationByIdAsync(installationId, CancellationToken.None);

        if (!success)
            return new UnprocessableEntityResult();

        return new OkResult();
    }

    [HttpPost]
    [Route("requests")]
    [ProducesResponseType((int)HttpStatusCode.OK)]
    [ProducesResponseType((int)HttpStatusCode.BadRequest)]
    [ProducesResponseType((int)HttpStatusCode.UnprocessableEntity)]
    public async Task<IActionResult> RequestPush(
        [Required]NotificationRequest notificationRequest)
    {
        if ((notificationRequest.Silent &&
            string.IsNullOrWhiteSpace(notificationRequest?.Action)) ||
            (!notificationRequest.Silent &&
            string.IsNullOrWhiteSpace(notificationRequest?.Text)))
            return new BadRequestResult();

        var success = await _notificationService
            .RequestNotificationAsync(notificationRequest, HttpContext.RequestAborted);

        if (!success)
            return new UnprocessableEntityResult();

        return new OkResult();
    }
    ```

### <a name="create-the-api-app"></a>De API-app maken

U maakt nu een [API-app](https://azure.microsoft.com/services/app-service/api/) in [Azure App Service](https://docs.microsoft.com/azure/app-service/) voor het hosten van de back-endservice.  

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Klik op **Een resource maken**, zoek naar en kies **API-app** en klik vervolgens op **Maken**.

1. Werk de volgende velden bij en klik vervolgens op **Maken**.

    **App-naam:**  
    Voer een globaal unieke naam in voor de **API-app**

    **Abonnement:**  
    Kies hetzelfde doel**abonnement** als waarin u de meldingshub hebt gemaakt.

    **Resourcegroep:**  
    Gebruik dezelfde **Resourcegroep** als waarin u de meldingshub hebt gemaakt.

    **App Service-plan/-locatie:**  
    Een nieuw **App Service-plan** maken  

    > [!NOTE]
    > Wijzig de standaardoptie in een abonnement dat **SSL**-ondersteuning bevat. Anders moet u de juiste stappen uitvoeren wanneer u met de mobiele app werkt om te voorkomen dat **http**-aanvragen worden geblokkeerd.

    **Application Insights:**  
    Behoud de voorgestelde optie (er wordt een nieuwe resource met die naam gemaakt) of kies een bestaande resource.

1. Wanneer de **API-app** is ingericht, gaat u naar die resource.

1. Noteer de **URL**-eigenschap in de samenvatting **Essentials** bovenaan het **Overzicht**. Deze URL is uw *back-endeindpunt* dat later in deze zelfstudie wordt gebruikt.

    > [!NOTE]
    > De URL maakt gebruik van de API-appnaam die u eerder hebt opgegeven, met de notatie `https://<app_name>.azurewebsites.net`.

1. Selecteer **Configuratie** in de lijst (onder **Instellingen**).  

1. Klik voor elk van de onderstaande instellingen op **Nieuwe toepassingsinstelling** om de **Naam** en een **Waarde** in te voeren en klik vervolgens op **OK**.

   | Naam                               | Waarde                          |
   | ---------------------------------- | ------------------------------ |
   | `Authentication:ApiKey`            | <api_key_value>                |
   | `NotificationHub:Name`             | <hub_name_value>               |
   | `NotificationHub:ConnectionString` | <hub_connection_string_value>  |

   > [!NOTE]
   > Dit zijn dezelfde instellingen die u eerder in de gebruikersinstellingen hebt gedefinieerd. U moet deze over kunnen kopiëren. De instelling **Authentication:ApiKey** is alleen vereist als u ervoor hebt gekozen om het gedeelte [Clients verifiëren met API-sleutel](#authenticate-clients-using-an-api-key-optional) te voltooien. Voor productiescenario's kunt u opties zoals [Azure KeyVault](https://azure.microsoft.com/services/key-vault) bekijken. Deze zijn in dit geval toegevoegd als toepassingsinstellingen voor eenvoud.

1. Zodra alle toepassingsinstellingen zijn toegevoegd, klikt u op **Opslaan** en **Doorgaan**.

### <a name="publish-the-backend-service"></a>De back-endservice publiceren

U gaat de app nu implementeren op de API-app zodat deze toegankelijk is vanaf alle apparaten.  

1. Wijzig uw configuratie van **Foutopsporing** in **Release** als u dit nog niet hebt gedaan.

1. **Control** +  **-klik** op het project **PushDemoApi** en kies **Publiceren naar Azure...** in het menu **Publiceren**.

1. Volg de verificatiestroom als u hierom wordt gevraagd. Gebruik het account dat u in het vorige gedeelte [De API-app maken](#create-the-api-app) hebt gebruikt.

1. Selecteer de **Azure App Service API-app** die u eerder hebt gemaakt in de lijst als uw publicatiedoel en klik vervolgens op **Publiceren**.

Nadat u de wizard hebt voltooid, wordt de app naar Azure gepubliceerd. Daarna wordt de app geopend. Noteer de **URL** als u dit nog niet hebt gedaan. Deze URL is uw *back-endeindpunt* dat later in deze zelfstudie wordt gebruikt.

### <a name="validating-the-published-api"></a>De gepubliceerde API valideren

1. Open in **[Postman](https://www.postman.com/downloads)** een nieuw tabblad, stel de aanvraag in op **POST** en voer het onderstaande adres in. Vervang de tijdelijke aanduiding door het basisadres dat u hebt genoteerd in het vorige gedeelte [De back-endservice publiceren](#publish-the-backend-service).

    ```csharp
    https://<app_name>.azurewebsites.net/api/notifications/installations
    ```

    > [!NOTE]
    > Het basisadres moet de indeling ``https://<app_name>.azurewebsites.net/`` hebben

1. Als u ervoor hebt gekozen om het gedeelte [Clients verifiëren met behulp van een API-sleutel](#authenticate-clients-using-an-api-key-optional) te voltooien, moet u de aanvraagheaders zo configureren dat deze uw **apikey**-waarde bevatten.

   | Sleutel                            | Waarde                          |
   | ------------------------------ | ------------------------------ |
   | apikey                         | <your_api_key>                 |

1. Kies de optie **onbewerkt** voor de **Hoofdtekst**, kies **JSON** in de lijst met indelingsopties en voeg vervolgens enkele tijdelijke aanduidingen voor **JSON**-content toe:

    ```json
    {}
    ```

1. Klik op **Verzenden**.

    > [!NOTE]
    > U ontvangt een status **400 Ongeldige aanvraag** van de service.

1. Voer de stappen 1-4 opnieuw uit, maar deze keer geeft u het eindpunt voor aanvragen op om te controleren of u hetzelfde antwoord **400 Ongeldige aanvraag** ontvangt.

    ```bash
    https://<app_name>.azurewebsites.net/api/notifications/requests
    ```

> [!NOTE]
> Het is nog niet mogelijk om de API te testen met geldige aanvraaggegevens, omdat hiervoor platformspecifieke informatie van de mobiele app van de client is vereist.
