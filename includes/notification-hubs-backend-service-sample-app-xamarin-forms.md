---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 40bc1c8c3d578e35b6689124f60f82d8ea85f0f2
ms.sourcegitcommit: e04a66514b21019f117a4ddb23f22c7c016da126
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/19/2020
ms.locfileid: "85112087"
---
### <a name="create-the-xamarinforms-solution"></a>De Xamarin.Forms-oplossing maken

1. Maak in **Visual Studio** een nieuwe **Xamarin.Forms**-oplossing met **Blank Forms Appp** als sjabloon en voer *PushDemo* in voor de **Projectnaam**.

    > [!NOTE]
    > Zorg er in het dialoogvenster **Uw Blank Forms App configureren** voor dat de **Organisatie-id** overeenkomt met de waarde die u eerder hebt gebruikt en dat zowel **Android** als **iOS**-doelen worden geselecteerd.

1. **Beheren** + **Klik** op de oplossing *PushDemo* en kies vervolgens **NuGet-pakketten bijwerken**.
1. **Beheren** + **Klik** op de oplossing *PushDemo* en kies vervolgens **NuGet-pakketten beheren**.
1. Zoek naar **Newtonsoft.Json** en zorg dat het is gecontroleerd.
1. Klik op **Pakketten toevoegen** en klik vervolgens op **Accepteren** wanneer u wordt gevraagd om de licentievoorwaarden te accepteren.
1. Bouw en voer de app uit op elk doelplatform (**Opdracht** + **Enter**) om de app-sjabloon op uw apparaat (apparaten) te testen.

### <a name="implement-the-cross-platform-components"></a>De platformoverschrijdende onderdelen implementeren

1. **Beheren** + **Klik** op het project **PushDemo.** , kies **Nieuwe map** in het menu **Toevoegen** en klik vervolgens op **Toevoegen** met *Modellen* als de **Naam van de map**.

1. **Beheren** + **Klik** in de map **Modellen** en kies vervolgens **Nieuw bestand...** vanuit het menu **Toevoegen**.

1. Selecteer **Algemeen** > **Lege klasse**, voer *DeviceInstallation.cs* in en voeg vervolgens de volgende implementatie toe.

    ```csharp
    using System.Collections.Generic;
    using Newtonsoft.Json;

    namespace PushDemo.Models
    {
        public class DeviceInstallation
        {
            [JsonProperty("installationId")]
            public string InstallationId { get; set; }

            [JsonProperty("platform")]
            public string Platform { get; set; }

            [JsonProperty("pushChannel")]
            public string PushChannel { get; set; }

            [JsonProperty("tags")]
            public List<string> Tags { get; set; } = new List<string>();
        }
    }
    ```

1. Voeg een **Lege opsomming** toe aan de map **Modellen** map met de naam *PushDemoAction.cs* met de volgende implementatie.

    ```csharp
    namespace PushDemo.Models
    {
        public enum PushDemoAction
        {
            ActionA,
            ActionB
        }
    }
    ```

1. Voeg een nieuwe map toe aan het project **PushDemo. iOS** met de naam *Services*. Voeg vervolgens een **Lege klasse** toe aan die map met de naam *ServiceContainer.cs* met de volgende implementatie.

     ```csharp
    using System;
    using System.Collections.Generic;

    namespace PushDemo.Services
    {
        public static class ServiceContainer
        {
            static readonly Dictionary<Type, Lazy<object>> services
                = new Dictionary<Type, Lazy<object>>();

            public static void Register<T>(Func<T> function)
                => services[typeof(T)] = new Lazy<object>(() => function());

            public static T Resolve<T>()
                => (T)Resolve(typeof(T));

            public static object Resolve(Type type)
            {
                {
                    if (services.TryGetValue(type, out var service))
                        return service.Value;

                    throw new KeyNotFoundException($"Service not found for type '{type}'");
                }
            }
        }
    }
    ```

    > [!NOTE]
    > Dit is een vereenvoudigde versie van de klasse [ServiceContainer](https://github.com/xamcat/mobcat-library/blob/master/MobCAT/ServiceContainer.cs) van de opslagplaats [ XamCAT](https://github.com/xamcat/mobcat-library). Deze wordt gebruikt als een lichtgewicht IoC (Inversion of Control)-container.

1. Voeg een **Lege interface** toe aan de map **Services** met de naam *IDeviceInstallationService.cs* en voeg vervolgens de volgende code toe.

    ```csharp
    using PushDemo.Models;

    namespace PushDemo.Services
    {
        public interface IDeviceInstallationService
        {
            string Token { get; set; }
            bool NotificationsSupported { get; }
            string GetDeviceId();
            DeviceInstallation GetDeviceInstallation(params string[] tags);
        }
    }
    ```

    > [!NOTE]
    > Deze interface wordt door elk doel later geïmplementeerd en gebootstrapped om de platformspecifieke functionaliteit en **DeviceInstallation**-informatie te bieden die vereist is voor de back-end-service.

1. Voeg nog een **Lege interface** toe aan de map **Services** met de naam *INotificationRegistrationService.cs* en voeg vervolgens de volgende code toe.  

    ```csharp
    using System.Threading.Tasks;

    namespace PushDemo.Services
    {
        public interface INotificationRegistrationService
        {
            Task DeregisterDeviceAsync();
            Task RegisterDeviceAsync(params string[] tags);
            Task RefreshRegistrationAsync();
        }
    }
    ```

    > [!NOTE]
    > Hiermee wordt de interactie tussen de client en de back-end-service afgehandeld.

1. Voeg nog een **Lege interface** toe aan de map **Services** met de naam *INotificationActionService.cs* en voeg vervolgens de volgende code toe.  

    ```csharp
    namespace PushDemo.Services
    {
        public interface INotificationActionService
        {
            void TriggerAction(string action);
        }
    }
    ```

    > [!NOTE]
    > Dit wordt gebruikt als een eenvoudig mechanisme om de verwerking van meldingsacties te centraliseren.

1. Voeg een **Lege interface** toe aan de map **Services** met de naam *IPushDemoNotificationActionService.cs* die is afgeleid van de *INotificationActionService* met de volgende implementatie.  

    ```csharp
    using System;
    using PushDemo.Models;

    namespace PushDemo.Services
    {
        public interface IPushDemoNotificationActionService : INotificationActionService
        {
            event EventHandler<PushDemoAction> ActionTriggered;
        }
    }
    ```

    > [!NOTE]
    > Dit type is specifiek voor de toepassing **PushDemo** en maakt gebruik van d opsomming **PushDemoAction** om de actie te identificeren die op zeer getypte wijze wordt geactiveerd.

1. Voeg een **Lege klasse** toe aan de map **Services** met de naam *NotificationRegistrationService.cs* en implementeer de *INotificationRegistrationService* met de volgende code.

    ```csharp
    using System;
    using System.Net.Http;
    using System.Text;
    using System.Threading.Tasks;
    using Newtonsoft.Json;
    using PushDemo.Models;
    using Xamarin.Essentials;

    namespace PushDemo.Services
    {
        public class NotificationRegistrationService : INotificationRegistrationService
        {
            const string CachedTagsKey = "cached_tags";
            const string RequestUrl = "api/notifications/installations";

            string _baseApiUrl;
            HttpClient _client;
            IDeviceInstallationService _deviceInstallationService;

            public NotificationRegistrationService(string baseApiUri, string apiKey)
            {
                _client = new HttpClient();
                _client.DefaultRequestHeaders.Add("Accept", "application/json");
                _client.DefaultRequestHeaders.Add("apikey", apiKey);

                _baseApiUrl = baseApiUri;
            }

            IDeviceInstallationService DeviceInstallationService
                => _deviceInstallationService ??
                    (_deviceInstallationService = ServiceContainer.Resolve<IDeviceInstallationService>());

            public Task DeregisterDeviceAsync()
            {
                var deviceId = DeviceInstallationService?.GetDeviceId();

                if (string.IsNullOrWhiteSpace(deviceId))
                    throw new Exception("Unable to resolve an ID for the device.");

                SecureStorage.Remove(CachedTagsKey);

                return SendAsync(HttpMethod.Delete, $"{RequestUrl}/{deviceId}");
            }

            public async Task RegisterDeviceAsync(params string[] tags)
            {
                var deviceInstallation = DeviceInstallationService?.GetDeviceInstallation(tags);

                if (deviceInstallation == null)
                    throw new Exception($"Unable to get device installation information.");

                if (string.IsNullOrWhiteSpace(deviceInstallation.InstallationId))
                    throw new Exception($"No {nameof(deviceInstallation.InstallationId)} value for {nameof(DeviceInstallation)}");

                if (string.IsNullOrWhiteSpace(deviceInstallation.Platform))
                    throw new Exception($"No {nameof(deviceInstallation.Platform)} value for {nameof(DeviceInstallation)}");

                if (string.IsNullOrWhiteSpace(deviceInstallation.PushChannel))
                    throw new Exception($"No {nameof(deviceInstallation.PushChannel)} value for {nameof(DeviceInstallation)}");

                await SendAsync<DeviceInstallation>(HttpMethod.Put, RequestUrl, deviceInstallation)
                    .ConfigureAwait(false);

                var serializedTags = JsonConvert.SerializeObject(tags);
                await SecureStorage.SetAsync(CachedTagsKey, serializedTags);
            }

            public async Task RefreshRegistrationAsync()
            {
                var serializedTags = await SecureStorage.GetAsync(CachedTagsKey)
                    .ConfigureAwait(false);

                if (string.IsNullOrWhiteSpace(serializedTags))
                    return;

                var tags = JsonConvert.DeserializeObject<string[]>(serializedTags);

                await RegisterDeviceAsync(tags);
            }

            async Task SendAsync<T>(HttpMethod requestType, string requestUri, T obj)
            {
                string serializedContent = null;

                await Task.Run(() => serializedContent = JsonConvert.SerializeObject(obj))
                    .ConfigureAwait(false);

                await SendAsync(requestType, requestUri, serializedContent);
            }

            async Task SendAsync(
                HttpMethod requestType,
                string requestUri,
                string jsonRequest = null)
            {
                var request = new HttpRequestMessage(requestType, new Uri($"{_baseApiUrl}{requestUri}"));

                if (jsonRequest != null)
                    request.Content = new StringContent(jsonRequest, Encoding.UTF8, "application/json");

                var response = await _client.SendAsync(request).ConfigureAwait(false);

                response.EnsureSuccessStatusCode();
            }
        }
    }
    ```

    > [!NOTE]
    > Het argument **apiKey** is alleen vereist als u ervoor hebt gekozen om de sectie [Clients verifiëren met API-sleutel](#authenticate-clients-using-an-api-key-optional) te voltooien.

1. Voeg een **Lege klasse** toe aan de map **Services** met de naam *PushDemoNotificationActionService.cs* en implementeer de *IPushDemoNotificationActionService* met de volgende code.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using PushDemo.Models;

    namespace PushDemo.Services
    {
        public class PushDemoNotificationActionService : IPushDemoNotificationActionService
        {
            readonly Dictionary<string, PushDemoAction> _actionMappings = new Dictionary<string, PushDemoAction>
            {
                { "action_a", PushDemoAction.ActionA },
                { "action_b", PushDemoAction.ActionB }
            };

            public event EventHandler<PushDemoAction> ActionTriggered = delegate { };

            public void TriggerAction(string action)
            {
                if (!_actionMappings.TryGetValue(action, out var pushDemoAction))
                    return;

                List<Exception> exceptions = new List<Exception>();

                foreach (var handler in ActionTriggered?.GetInvocationList())
                {
                    try
                    {
                        handler.DynamicInvoke(this, pushDemoAction);
                    }
                    catch (Exception ex)
                    {
                        exceptions.Add(ex);
                    }
                }

                if (exceptions.Any())
                    throw new AggregateException(exceptions);
            }
        }
    }
    ```

1. Voeg een **Lege klasse** toe aan het project **PushDemo** met de naam *Config.cs* met de volgende implementatie.  

    ```csharp
    namespace PushDemo
    {
        public static partial class Config
        {
            public static string ApiKey = "API_KEY";
            public static string BackendServiceEndpoint = "BACKEND_SERVICE_ENDPOINT";
        }
    }
    ```

    > [!NOTE]
    > Dit wordt gebruikt als een eenvoudige manier om geheimen uit broncodebeheer te houden. U kunt deze waarden vervangen als onderdeel van een geautomatiseerde build of ze overschrijven met een lokale gedeeltelijke klasse. U doet dit in de volgende stap.
    >
    > Het veld **ApiKey** is alleen vereist als u ervoor hebt gekozen om de sectie [Clients verifiëren met API-sleutel](#authenticate-clients-using-an-api-key-optional) te voltooien.

1. Voeg nog een **Lege klasse** toe aan het project **PushDemo**, deze keer met de naam *Config.local_secrets* met de volgende implementatie.  

    ```csharp
    namespace PushDemo
    {
        public static partial class Config
        {
            static Config()
            {
                ApiKey = "<your_api_key>";
                BackendServiceEndpoint = "<your_api_app_url>";
            }
        }
    }
    ```

    > [!NOTE]
    > Vervang de plaatsaanduiding voor de waarden door uw eigen. U moet hiermee rekening houden wanneer u de back-end-service hebt gemaakt. De URL van de **API-app** moet ``https://<api_app_name>.azurewebsites.net/`` zijn. Vergeet niet om ``*.local_secrets.*`` toe te voegen aan uw gitignore-bestand om te voorkomen dat dit bestand wordt doorgevoerd.
    >
    > Het veld **ApiKey** is alleen vereist als u ervoor hebt gekozen om de sectie [Clients verifiëren met API-sleutel](#authenticate-clients-using-an-api-key-optional) te voltooien.

1. Voeg een **Lege klasse** toe aan het project **PushDemo** met de naam *Bootstrap.cs* met de volgende implementatie.  

    ```csharp
    using System;
    using PushDemo.Services;

    namespace PushDemo
    {
        public static class Bootstrap
        {
            public static void Begin(Func<IDeviceInstallationService> deviceInstallationService)
            {
                ServiceContainer.Register(deviceInstallationService);

                ServiceContainer.Register<IPushDemoNotificationActionService>(()
                    => new PushDemoNotificationActionService());

                ServiceContainer.Register<INotificationRegistrationService>(()
                    => new NotificationRegistrationService(
                        Config.BackendServiceEndpoint,
                        Config.ApiKey));
            }
        }
    }
    ```

    > [!NOTE]
    > De methode **Beginnen** wordt aangeroepen door elk platform wanneer de app wordt gestart in een platformspecifieke implementatie van **IDeviceInstallationService**.
    >
    > Het constructorargument **NotificationRegistrationService** **apiKey** is alleen vereist als u ervoor hebt gekozen om de sectie [Clients verifiëren met API-sleutel](#authenticate-clients-using-an-api-key-optional) te voltooien.

### <a name="implement-the-cross-platform-ui"></a>De platformoverschrijdende UI implementeren

1. Open in het project **PushDemo** **MainPage. xaml** en vervang het besturingselement **StackLayout** door het volgende.

    ```xml
    <StackLayout VerticalOptions="EndAndExpand"  
                 HorizontalOptions="FillAndExpand"
                 Padding="20,40">
        <Button x:Name="RegisterButton"
                Text="Register"
                Clicked="RegisterButtonClicked" />
        <Button x:Name="DeregisterButton"
                Text="Deregister"
                Clicked="DeregisterButtonClicked" />
    </StackLayout>
    ```

1. Voeg nu in **MainPage.xaml.cs** een back-upveld **readonly** toe om een verwijzing naar de implementatie van **INotificationRegistrationService** op te slaan.

    ```csharp
    readonly INotificationRegistrationService _notificationRegistrationService;
    ```

1. Zet in de constructor **MainPage** de implementatie **INotificationRegistrationService** om met behulp van de **ServiceContainer** en wijs deze toe aan het back-upveld *_notificationRegistrationService_*.

    ```csharp
    public MainPage()
    {
        InitializeComponent();

        _notificationRegistrationService =
            ServiceContainer.Resolve<INotificationRegistrationService>();
    }
    ```

1. Implementeer de gebeurtenis-handlers voor de knoppen **RegisterButton** en **DeregisterButton**. **Geklikte** gebeurtenissen die de bijbehorende **Registeren**/**Registreren ongedaan maken** aanroepen.

    ```csharp
    void RegisterButtonClicked(object sender, EventArgs e)
        => _notificationRegistrationService.RegisterDeviceAsync().ContinueWith((task)
            => { ShowAlert(task.IsFaulted ?
                    task.Exception.Message :
                    $"Device registered"); });

    void DeregisterButtonClicked(object sender, EventArgs e)
        => _notificationRegistrationService.DeregisterDeviceAsync().ContinueWith((task)
            => { ShowAlert(task.IsFaulted ?
                    task.Exception.Message :
                    $"Device deregistered"); });

    void ShowAlert(string message)
        => MainThread.BeginInvokeOnMainThread(()
            => DisplayAlert("PushDemo", message, "OK").ContinueWith((task)
                => { if (task.IsFaulted) throw task.Exception; }));
    ```

1. Controleer nu in **App.xaml.cs** of er wordt verwezen naar de volgende naamruimten.

    ```csharp
    using PushDemo.Models;
    using PushDemo.Services;
    using Xamarin.Essentials;
    using Xamarin.Forms;
    ```

1. Implementeer de gebeurtenis-handler voor de gebeurtenis **IPushDemoNotificationActionService**  **ActionTriggered-gebeurtenis**.

    ```csharp
    void NotificationActionTriggered(object sender, PushDemoAction e)
        => ShowActionAlert(e);

    void ShowActionAlert(PushDemoAction action)
        => MainThread.BeginInvokeOnMainThread(()
            => MainPage?.DisplayAlert("PushDemo", $"{action} action received", "OK")
                .ContinueWith((task) => { if (task.IsFaulted) throw task.Exception; }));
    ```

1. In de constructor **App** moet u de implementatie **IPushNotificationActionService** omzetten met behulp van de **ServiceContainer** en u abonneren op de gebeurtenis **IPushDemoNotificationActionService** **ActionTriggered**.

    ```csharp
    public App()
    {
        InitializeComponent();

        ServiceContainer.Resolve<IPushDemoNotificationActionService>()
            .ActionTriggered += NotificationActionTriggered;

        MainPage = new MainPage();
    }
    ```

    > [!NOTE]
    > Dit is om aan te geven dat acties voor pushmeldingen zijn ontvangen of worden doorgegeven. Normaal gesproken gebeurt dit op de achtergrond om bijvoorbeeld naar een specifieke weergave te navigeren of om gegevens te vernieuwen in plaats van een waarschuwing te geven via de hoofdmap **Pagina**, **MainPage** in dit geval.
