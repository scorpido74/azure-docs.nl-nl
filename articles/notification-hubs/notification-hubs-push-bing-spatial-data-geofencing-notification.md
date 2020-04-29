---
title: Push meldingen verzenden met de ruimtelijke gegevens van Azure Notification Hubs en Bing | Microsoft Docs
description: In deze zelfstudie leert u hoe u locatiegebaseerde pushmeldingen kunt verzenden met Azure Notification Hubs en ruimtelijke Bing-gegevens.
services: notification-hubs
documentationcenter: windows
keywords: Push meldingen, Push meldingen
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: f41beea1-0d62-4418-9ffc-c9d70607a1b7
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 9f8697332d34fc044c805db6c6595fdf067ed054
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80127189"
---
# <a name="tutorial-send-location-based-push-notifications-with-notification-hubs-and-bing-spatial-data"></a>Zelf studie: Push meldingen op basis van locaties verzenden met Notification Hubs en ruimtelijke gegevens van Bing

In deze zelfstudie leert u hoe u locatiegebaseerde pushmeldingen kunt verzenden met Azure Notification Hubs en ruimtelijke Bing-gegevens.

In deze zelfstudie voert u de volgende stappen uit:

> [!div class="checklist"]
> * De gegevensbron instellen
> * De UWP-toepassing instellen
> * De back-end instellen
> * Pushmeldingen testen in de UWP-app (Universal Windows Platform)

## <a name="prerequisites"></a>Vereisten

* **Azure-abonnement**. Als u nog geen abonnement op Azure hebt, [Maak dan een gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.
* [Visual Studio 2015 Update 1](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx) of hoger ([Community Edition](https://go.microsoft.com/fwlink/?LinkId=691978&clcid=0x409)).
* Meest recente versie van de [Azure SDK](https://azure.microsoft.com/downloads/).
* [Bing Maps Dev Center-account](https://www.bingmapsportal.com/) (u kunt gratis een account maken en dit koppelen aan uw Microsoft-account).

## <a name="set-up-the-data-source"></a>De gegevensbron instellen

1. Meld u aan bij [Bing Maps Dev Center](https://www.bingmapsportal.com/).
2. Selecteer **Data sources** in de bovenste navigatiebalk en selecteer vervolgens **Manage Data Sources**.

    ![](./media/notification-hubs-geofence/bing-maps-manage-data.png)
3. Als u nog geen gegevensbron hebt, ziet u een koppeling voor het maken van een gegevensbron. Selecteer **Upload data as a data source**. U kunt ook het menu gegevens **bronnen** > **uploaden** gebruiken.

    ![](./media/notification-hubs-geofence/bing-maps-create-data.png)
4. Een bestand `NotificationHubsGeofence.pipe` op de harde schijf maken met de volgende inhoud: in deze zelf studie gebruikt u een voor beeld van een pipe dat een gebied van het San Francisco afgebakend:

    ```text
    Bing Spatial Data Services, 1.0, TestBoundaries
    EntityID(Edm.String,primaryKey)|Name(Edm.String)|Longitude(Edm.Double)|Latitude(Edm.Double)|Boundary(Edm.Geography)
    1|SanFranciscoPier|||POLYGON ((-122.389825 37.776598,-122.389438 37.773087,-122.381885 37.771849,-122.382186 37.777022,-122.389825 37.776598))
    ```

    Het pipe-bestand vertegenwoordigt deze entiteit:

    ![](./media/notification-hubs-geofence/bing-maps-geofence.png)
5. Voer op de pagina **Upload a data source** de volgende acties uit:
   1. Selecteer **pipe** bij **Data format**.
   2. Bladeren naar en selecteer het bestand `NotificationHubGeofence.pipe` dat u in de vorige stap hebt gemaakt.
   3. Selecteer de knop **Upload**.

      > [!NOTE]
      > Mogelijk wordt u gevraagd om een nieuwe sleutel op te geven als **hoofdsleutel**; deze verschilt van de **querysleutel**. Maak een nieuwe sleutel via het dashboard en vernieuw de pagina voor het uploaden van gegevensbronnen.
6. Wanneer u het gegevensbestand hebt geüpload, moet u niet vergeten de gegevensbron te publiceren. **Gegevens bronnen** -> selecteren**gegevens bronnen beheren** , zoals u eerder hebt gedaan.
7. Selecteer de gegevensbron in de lijst en kies **Publish** in de kolom **Actions**.

    ![](./media/notification-hubs-geofence/publish-button.png)
8. Ga naar het tabblad **Published Data Sources** en controleer of uw gegevensbron in de lijst staat.

    ![](./media/notification-hubs-geofence/bing-maps-published-data.png)
9. Selecteer **bewerken**. U ziet (in een oogopslag) welke locaties u met de gegevens hebt geïntroduceerd.

    ![](./media/notification-hubs-geofence/bing-maps-data-details.png)

    Op dit moment worden in de portal de grenzen van de geofence die u hebt gemaakt, niet weergegeven. U hoeft alleen nog te bevestigen dat de opgegeven locatie zich in de juiste omgeving bevindt.
10. U hebt nu voldaan aan alle vereisten voor de gegevensbron. Voor meer informatie over de aanvraag-URL voor de API-aanroep kiest u in het Bing Kaarten-ontwikkelaarscentrum de optie **Gegevensbronnen** en selecteert u **Gegevensbroninformatie**.

    ![](./media/notification-hubs-geofence/bing-maps-data-info.png)

    De waarde bij **Query URL** is het eindpunt waarop u query’s kunt uitvoeren om te controleren of het apparaat zich momenteel binnen de grenzen van een locatie bevindt of niet. Als u deze controle wilt uitvoeren, moet u een GET-aanroep uitvoeren voor de query-URL, waarbij u de volgende parameters toevoegt:

    ```text
    ?spatialFilter=intersects(%27POINT%20LONGITUDE%20LATITUDE)%27)&$format=json&key=QUERY_KEY
    ```

    Bing Kaarten voert automatisch de berekeningen uit om te zien of het apparaat zich binnen de geofence bevindt. Wanneer u de aanvraag hebt uitgevoerd via een browser (of cURL), ontvangt u een standaard JSON-antwoord:

    ![](./media/notification-hubs-geofence/bing-maps-json.png)

    Dit antwoord ontvangt u alleen als het punt zich binnen de opgegeven grenzen bevindt. Als dat niet het geval is, ontvangt u een lege bucket **results**:

    ![](./media/notification-hubs-geofence/bing-maps-nores.png)

## <a name="set-up-the-uwp-application"></a>De UWP-toepassing instellen

1. Open in Visual Studio een nieuw project van het type **Lege app (Universeel Windows)**.

    ![](./media/notification-hubs-geofence/notification-hubs-create-blank-app.png)

    Wanneer het maken van het project is voltooid, beschikt u over de basis voor de app. We gaan nu alles instellen voor de infra structuur voor geografische omheining. Omdat u Bing-services gaat gebruiken voor deze oplossing, is er een openbaar REST-API-eindpunt waarmee query’s kunnen worden uitgevoerd op specifieke locatiekaders:

    ```text
    http://spatial.virtualearth.net/REST/v1/data/
    ```
    Geef de volgende parameters op om hiermee aan de slag te gaan:

   * De **gegevensbron-id** en de **gegevensbronnaam**. In de Bing Kaarten-API bevatten gegevensbronnen verschillende gerangschikte metagegevens, zoals locaties en bedrijfsuren.  
   * **Naam van de entiteit**: de entiteit die u als verwijzingspunt wilt gebruiken voor de melding.
   * **Bing Maps API Key**: dit is de sleutel die u eerder hebt verkregen tijdens het maken van het Bing Dev Center-account.

     Nu de gegevensbron klaar is, kunt u aan de slag met de UWP-toepassing.
2. Schakel locatieservices in voor uw toepassing. Open het bestand `Package.appxmanifest` in **Solution Explorer**.

    ![](./media/notification-hubs-geofence/vs-package-manifest.png)
3. Ga in het venster met eigenschappen van het pakket naar het tabblad **Capabilities** en selecteer **Location**.

    ![](./media/notification-hubs-geofence/vs-package-location.png)
4. Maak een nieuwe map in de oplossing met de naam `Core`. Voeg er een nieuw bestand aan toe met de naam `LocationHelper.cs`:

    ![](./media/notification-hubs-geofence/vs-location-helper.png)

    De klasse `LocationHelper` bevat code voor het opvragen van de locatie van de gebruiker via de systeem-API:

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Windows.Devices.Geolocation;

    namespace NotificationHubs.Geofence.Core
    {
        public class LocationHelper
        {
            private static readonly uint AppDesiredAccuracyInMeters = 10;

            public async static Task<Geoposition> GetCurrentLocation()
            {
                var accessStatus = await Geolocator.RequestAccessAsync();
                switch (accessStatus)
                {
                    case GeolocationAccessStatus.Allowed:
                        {
                            Geolocator geolocator = new Geolocator { DesiredAccuracyInMeters = AppDesiredAccuracyInMeters };

                            return await geolocator.GetGeopositionAsync();
                        }
                    default:
                        {
                            return null;
                        }
                }
            }

        }
    }
    ```

    Zie[de locatie van de gebruiker ophalen](https://msdn.microsoft.com/library/windows/apps/mt219698.aspx)voor meer informatie over het ophalen van de locatie van de gebruiker in UWP-apps.
5. Als u wilt controleren of het ophalen van de locatie werkt, opent u de codezijde van de hoofdpagina (`MainPage.xaml.cs`). Maak een nieuwe gebeurtenis-handler voor de gebeurtenis `Loaded` in de constructor `MainPage`.

    ```csharp
    public MainPage()
    {
        this.InitializeComponent();
        this.Loaded += MainPage_Loaded;
    }
    ```

    U implementeert de gebeurtenis-handler als volgt:

    ```csharp
    private async void MainPage_Loaded(object sender, RoutedEventArgs e)
    {
        var location = await LocationHelper.GetCurrentLocation();

        if (location != null)
        {
            Debug.WriteLine(string.Concat(location.Coordinate.Longitude,
                " ", location.Coordinate.Latitude));
        }
    }
    ```
6. Voer de toepassing uit en geef deze toegang tot uw locatie.

    ![](./media/notification-hubs-geofence/notification-hubs-location-access.png)
7. Wanneer de toepassing is gestart, ziet u de coördinaten in het venster **Uitvoer**:

    ![](./media/notification-hubs-geofence/notification-hubs-location-output.png)

    Nu u weet dat de locatie kan worden opgehaald, kunt u de geladen gebeurtenis-handler verwijderen, omdat u deze niet meer gebruikt.
8. De volgende stap is het vastleggen van locatiewijzigingen. Voeg in de klasse `LocationHelper` de gebeurtenis-handler voor `PositionChanged` toe:

    ```csharp
    geolocator.PositionChanged += Geolocator_PositionChanged;
    ```

    De implementatie toont de locatiecoördinaten in het venster **Output**:

    ```csharp
    private static async void Geolocator_PositionChanged(Geolocator sender, PositionChangedEventArgs args)
    {
        await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
        {
            Debug.WriteLine(string.Concat(args.Position.Coordinate.Longitude, " ", args.Position.Coordinate.Latitude));
        });
    }
    ```

## <a name="set-up-the-backend"></a>De back-end instellen

1. Download het [.NET-back-endvoorbeeld via GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/NotifyUsers).
2. Wanneer het downloaden is voltooid, opent u de map `NotifyUsers` en opent u vervolgens het bestand `NotifyUsers.sln` in Visual Studio.
3. Stel het project `AppBackend` in als **StartUp Project** open het.

    ![](./media/notification-hubs-geofence/vs-startup-project.png)

    Het project is al geconfigureerd voor het versturen van pushmeldingen naar doelapparaten. U hoeft dus nog maar twee dingen te doen: de juiste verbindingsreeks opgeven voor de Notification Hub en grensidentificatie toevoegen zodat de melding alleen wordt verzonden wanneer de gebruiker zich binnen de geofence bevindt.

4. Als u de verbindingsreeks wilt configureren, opent u `Notifications.cs` in de map `Models`. De functie `NotificationHubClient.CreateClientFromConnectionString` bevat de informatie over de Notification Hub die u ook kunt inzien in [Azure Portal](https://portal.azure.com) (op de pagina **Toegangsbeleid** in **Instellingen**). Sla het bijgewerkte configuratiebestand op.
5. Maak een model voor het Bing Kaarten-API-resultaat. De eenvoudigste manier om dat te doen is door de `Models` map te openen en**klasse** **toevoegen** > te kiezen. Noem deze `GeofenceBoundary.cs`. Vervolgens kopieert u de JSON uit het API-antwoord dat u in de eerste sectie hebt verkregen. Gebruik in Visual Studio **bewerken** > **Plakken speciaal** > **Plakken JSON als klassen**.

    Zo zorgt u ervoor dat het object op de juiste manier wordt gedeserialiseerd. De resulterende klasseset moet er ongeveer als volgt uitzien:

    ```csharp
    namespace AppBackend.Models
    {
        public class Rootobject
        {
            public D d { get; set; }
        }

        public class D
        {
            public string __copyright { get; set; }
            public Result[] results { get; set; }
        }

        public class Result
        {
            public __Metadata __metadata { get; set; }
            public string EntityID { get; set; }
            public string Name { get; set; }
            public float Longitude { get; set; }
            public float Latitude { get; set; }
            public string Boundary { get; set; }
            public string Confidence { get; set; }
            public string Locality { get; set; }
            public string AddressLine { get; set; }
            public string AdminDistrict { get; set; }
            public string CountryRegion { get; set; }
            public string PostalCode { get; set; }
        }

        public class __Metadata
        {
            public string uri { get; set; }
        }
    }
    ```
6. Open vervolgens `Controllers` > `NotificationsController.cs`. Werk de POST-aanroep bij om rekening te houden met de lengte- en breedtegraad van de bestemming. Dit doet u door de tekenreeksen `latitude` en `longitude` toe te voegen aan de functiehandtekening.

    ```csharp
    public async Task<HttpResponseMessage> Post(string pns, [FromBody]string message, string to_tag, string latitude, string longitude)
    ```
7. Maak een nieuwe klasse in het project met de naam `ApiHelper.cs`. Deze klasse gebruikt u om verbinding te maken met Bing om te controleren op grensoverschrijdingen. Implementeer de functie `IsPointWithinBounds` zoals wordt weergegeven in de volgende code:

    ```csharp
    public class ApiHelper
    {
        public static readonly string ApiEndpoint = "{YOUR_QUERY_ENDPOINT}?spatialFilter=intersects(%27POINT%20({0}%20{1})%27)&$format=json&key={2}";
        public static readonly string ApiKey = "{YOUR_API_KEY}";

        public static bool IsPointWithinBounds(string longitude,string latitude)
        {
            var json = new WebClient().DownloadString(string.Format(ApiEndpoint, longitude, latitude, ApiKey));
            var result = JsonConvert.DeserializeObject<Rootobject>(json);
            if (result.d.results != null && result.d.results.Count() > 0)
            {
                return true;
            }
            else
            {
                return false;
            }
        }
    }
    ```

    > [!IMPORTANT]
    > Zorg ervoor dat u het API-eindpunt vervangt door de query-URL die u eerder hebt verkregen via het Bing Dev Center (dit geldt ook voor de API-sleutel).

    Als er resultaten zijn op basis van de query, bevindt het opgegeven punt zich binnen de grenzen van de geofence. In dat geval wordt `true` geretourneerd. Als er geen resultaten zijn, wordt via Bing een melding weergegeven dat het punt zich buiten het kader bevindt, waardoor `false` wordt geretourneerd.
8. Voeg in `NotificationsController.cs` een controle toe vóór de switch-instructie:

    ```csharp
    if (ApiHelper.IsPointWithinBounds(longitude, latitude))
    {
        switch (pns.ToLower())
        {
            case "wns":
                //// Windows 8.1 / Windows Phone 8.1
                var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

                // Windows 10 specific Action Center support
                toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

                break;
        }
    }
    ```

## <a name="test-push-notifications-in-the-uwp-app"></a>Pushmeldingen testen in de UWP-app

1. In de UWP-app moet u nu meldingen kunnen testen. Maak in de klasse `LocationHelper` een nieuwe functie (`SendLocationToBackend`):

    ```csharp
    public static async Task SendLocationToBackend(string pns, string userTag, string message, string latitude, string longitude)
    {
        var POST_URL = "http://localhost:8741/api/notifications?pns=" +
            pns + "&to_tag=" + userTag + "&latitude=" + latitude + "&longitude=" + longitude;

        using (var httpClient = new HttpClient())
        {
            try
            {
                await httpClient.PostAsync(POST_URL, new StringContent("\"" + message + "\"",
                    System.Text.Encoding.UTF8, "application/json"));
            }
            catch (Exception ex)
            {
                Debug.WriteLine(ex.Message);
            }
        }
    }
    ```

    > [!NOTE]
    > Stel `POST_URL` in op de locatie van de geïmplementeerde webtoepassing. Het is nu ook mogelijk om deze lokaal uit te voeren, maar terwijl u aan het implementeren van een open bare versie werkt, moet u deze hosten met een externe provider.
2. Registreer de UWP-app voor pushmeldingen. Kies in Visual Studio de **project** > **Store** > -**App koppelen aan de Store**.

    ![](./media/notification-hubs-geofence/vs-associate-with-store.png)
3. Wanneer u zich hebt aangemeld bij uw ontwikkelaarsaccount, selecteert u een bestaande app of maakt u een nieuwe app. Koppel het pakket daaraan.
4. Ga naar het Dev Center en open de app die u hebt gemaakt. Kies **Services** > **Push meldingen** > **Live Services-site**.

    ![](./media/notification-hubs-geofence/ms-live-services.png)
5. Op de site bekijkt u het **toepassingsgeheim** en de **pakket-SID**. U hebt beide nodig in de Azure portal: Open uw notification hub, kies **instellingen** > **Notification Services** > **Windows (WNS)** en voer de gegevens in de vereiste velden in.

    ![](./media/notification-hubs-geofence/notification-hubs-wns.png)
6. Kies **Opslaan**.
7. Open **Verwijzingen** in **Solution Explorer** en selecteer **NuGet-pakketten beheren**. Voeg een verwijzing naar de **beheerde Microsoft Azure Service Bus-bibliotheek** toe. Zoek hiervoor naar `WindowsAzure.Messaging.Managed` en voeg deze vermelding toe aan uw project.

    ![](./media/notification-hubs-geofence/vs-nuget.png)
8. Voor testdoeleinden maakt u de gebeurtenis-handler voor `MainPage_Loaded` opnieuw. Voeg er nu dit codefragment aan toe:

    ```csharp
    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    var hub = new NotificationHub("HUB_NAME", "HUB_LISTEN_CONNECTION_STRING");
    var result = await hub.RegisterNativeAsync(channel.Uri);

    // Displays the registration ID so you know it was successful
    if (result.RegistrationId != null)
    {
        Debug.WriteLine("Reg successful.");
    }
    ```

    Hiermee wordt de app geregistreerd bij de Notification Hub. U bent er nu klaar voor!
9. Voeg in `LocationHelper`, binnen de handler `Geolocator_PositionChanged`, een testcodefragment toe waardoor de locatie geforceerd in de geofence wordt geplaatst:

    ```csharp
    await LocationHelper.SendLocationToBackend("wns", "TEST_USER", "TEST", "37.7746", "-122.3858");
    ```

10. Omdat u niet de echte coördinaten doorgeeft (die misschien niet binnen de grenzen liggen op dat moment) en omdat u vooraf gedefinieerde testwaarden gebruikt, wordt tijdens het bijwerken een melding weergegeven:

    ![](./media/notification-hubs-geofence/notification-hubs-test-notification.png)

## <a name="next-steps"></a>Volgende stappen

Er zijn een aantal stappen die u mogelijk moet uitvoeren om ervoor te zorgen dat de oplossing klaar is voor productie.

1. U moet er ten eerste voor zorgen dat de geofences dynamisch zijn. Hiervoor moet u extra bewerkingen uitvoeren met de Bing-API, zodat u nieuwe grenzen kunt uploaden in de bestaande gegevensbron. Zie voor meer informatie de Engelstalige documentatie over de [Bing Spatial Data Services-API](https://msdn.microsoft.com/library/ff701734.aspx).
2. Ten tweede is het raadzaam de geofences met [tags](notification-hubs-tags-segment-push-message.md) als doel te definiëren omdat u er zeker van wilt zijn dat er aan de juiste deelnemers wordt geleverd.

In de oplossing in deze zelfstudie wordt een scenario beschreven waarin u mogelijk veel verschillende doelplatforms hebt; daarom hebben we geofencing niet beperkt tot systeemspecifieke mogelijkheden. Los daarvan is het wel zo dat het universele Windows-platform [direct na aanschaf](https://msdn.microsoft.com/windows/uwp/maps-and-location/set-up-a-geofence) mogelijkheden biedt voor het detecteren van geofences.
