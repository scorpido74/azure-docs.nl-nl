---
title: Registratiebeheer
description: In dit onderwerp wordt uitgelegd hoe u apparaten registreren met meldingshubs om pushmeldingen te ontvangen.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: fd0ee230-132c-4143-b4f9-65cef7f463a1
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 03/17/2020
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 04/08/2019
ms.openlocfilehash: 00de9c803ef796eda8da609a4009e0a8cfcb3664
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455364"
---
# <a name="registration-management"></a>Registratiebeheer

In dit onderwerp wordt uitgelegd hoe u apparaten registreren met meldingshubs om pushmeldingen te ontvangen. Het onderwerp beschrijft registraties op een hoog niveau en introduceert vervolgens de twee belangrijkste patronen voor het registreren van apparaten: het rechtstreeks registreren van het apparaat naar de meldingshub en registreren via een backend van de toepassing.

## <a name="what-is-device-registration"></a>Wat is apparaatregistratie

Apparaatregistratie met een meldingshub wordt uitgevoerd met **behulp van een registratie** of **installatie.**

### <a name="registrations"></a>Registraties

Een registratie koppelt de Platform Notification Service (PNS) handgreep voor een apparaat met tags en eventueel een sjabloon. De PNS-handgreep kan een ChannelURI, apparaattoken of FCM-registratie-id zijn. Tags worden gebruikt om meldingen door te sturen naar de juiste set apparaatgrepen. Zie [Expressies routeren en taggen](notification-hubs-tags-segment-push-message.md)voor meer informatie . Sjablonen worden gebruikt om transformatie per registratie te implementeren. Zie [Sjablonen](notification-hubs-templates-cross-platform-push-messages.md) voor meer informatie.

> [!NOTE]
> Azure Notification Hubs ondersteunt maximaal 60 tags per apparaat.

### <a name="installations"></a>Installaties

Een installatie is een verbeterde registratie die een zak push gerelateerde eigenschappen bevat. Het is de nieuwste en beste aanpak voor het registreren van uw apparaten. Het wordt echter nog niet ondersteund door client-side .NET SDK[(Notification Hub SDK voor backend-bewerkingen).](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)  Dit betekent dat als u zich registreert vanaf het clientapparaat zelf, u de [NOTIFICATION Hubs REST API-benadering](/rest/api/notificationhubs/create-overwrite-installation) moet gebruiken om installaties te ondersteunen. Als u een backendservice gebruikt, u Notification Hub SDK gebruiken [voor backendbewerkingen.](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)

De volgende zijn enkele belangrijke voordelen van het gebruik van installaties:

- Het maken of bijwerken van een installatie is volledig idempotent. Dus je het opnieuw proberen zonder zorgen over dubbele registraties.
- Het installatiemodel ondersteunt een`$InstallationId:{INSTALLATION_ID}`speciale tagindeling ( ) waarmee u een melding rechtstreeks naar het specifieke apparaat verzenden. Als de code van de app bijvoorbeeld `joe93developer` een installatie-id van dit specifieke apparaat instelt, `$InstallationId:{joe93developer}` kan een ontwikkelaar dit apparaat targeten wanneer hij een melding naar de tag verzendt. Hierdoor u een specifiek apparaat targeten zonder dat u extra codering hoeft te doen.
- Met behulp van installaties u ook gedeeltelijke registratie-updates uitvoeren. De gedeeltelijke update van een installatie wordt aangevraagd met een PATCH-methode met behulp van de [JSON-Patch-standaard.](https://tools.ietf.org/html/rfc6902) Dit is handig als u tags op de registratie wilt bijwerken. U hoeft niet de volledige registratie naar beneden te halen en vervolgens alle vorige tags opnieuw te verzenden.

Een installatie kan de volgende eigenschappen bevatten. Zie Een installatie met REST API of [Installatie-eigenschappen](/dotnet/api/microsoft.azure.notificationhubs.installation) [maken of overschrijven](/rest/api/notificationhubs/create-overwrite-installation) voor een volledige lijst van de installatie-eigenschappen.

```json
// Example installation format to show some supported properties
{
    installationId: "",
    expirationTime: "",
    tags: [],
    platform: "",
    pushChannel: "",
    ………
    templates: {
        "templateName1" : {
            body: "",
            tags: [] },
        "templateName2" : {
            body: "",
            // Headers are for Windows Store only
            headers: {
                "X-WNS-Type": "wns/tile" }
            tags: [] }
    },
    secondaryTiles: {
        "tileId1": {
            pushChannel: "",
            tags: [],
            templates: {
                "otherTemplate": {
                    bodyTemplate: "",
                    headers: {
                        ... }
                    tags: [] }
            }
        }
    }
}
```

> [!NOTE]
> Standaard verlopen registraties en installaties niet.

Registraties en installaties moeten voor elk apparaat/kanaal een geldige PNS-handgreep bevatten. Omdat PNS-handgrepen alleen kunnen worden verkregen in een client-app op het apparaat, is één patroon om direct op dat apparaat te registreren met de client-app. Aan de andere kant vereisen beveiligingsoverwegingen en bedrijfslogica met betrekking tot tags mogelijk dat u apparaatregistratie in de back-end van de app beheert.

> [!NOTE]
> De Installaties API ondersteunt de Baidu-service niet (hoewel de Registratie-API dat wel doet). 

### <a name="templates"></a>Sjablonen

Als u [Sjablonen](notification-hubs-templates-cross-platform-push-messages.md)wilt gebruiken, bevat de apparaatinstallatie ook alle sjablonen die aan dat apparaat zijn gekoppeld in een JSON-indeling (zie voorbeeld hierboven). De sjabloonnamen helpen verschillende sjablonen voor hetzelfde apparaat te targeten.

Elke sjabloonnaam wordt toegewezen aan een sjabloonbody en een optionele set tags. Bovendien kan elk platform extra sjablooneigenschappen hebben. Voor Windows Store (met WNS) en Windows Phone 8 (met MPNS) kan een extra set kopteksten deel uitmaken van de sjabloon. In het geval van APN's u een eigenschap voor vervaldatum instellen op een constante of op een sjabloonexpressie. Zie [een installatie met rest-onderwerp maken of overschrijven voor](/rest/api/notificationhubs/create-overwrite-installation) een volledige lijst van de installatie-eigenschappen.

### <a name="secondary-tiles-for-windows-store-apps"></a>Secundaire tegels voor Windows Store-apps

Voor Windows Store-clienttoepassingen is het verzenden van meldingen naar secundaire tegels hetzelfde als ze naar de primaire toepassing verzenden. Dit wordt ook ondersteund in installaties. Secundaire tegels hebben een andere ChannelUri, die de SDK op uw client-app transparant hanteert.

Het woordenboek SecondaryTiles gebruikt dezelfde TileId die wordt gebruikt om het object SecondaryTiles in uw Windows Store-app te maken. Net als bij de primaire ChannelUri, Kanuris van secundaire tegels kan veranderen op elk moment. Om de installaties in de meldingshub bijgewerkt te houden, moet het apparaat ze vernieuwen met de huidige ChannelUris van de secundaire tegels.

## <a name="registration-management-from-the-device"></a>Registratiebeheer vanaf het apparaat

Bij het beheren van apparaatregistratie vanuit client-apps is de backend alleen verantwoordelijk voor het verzenden van meldingen. Client-apps houden PNS-handvatten up-to-date en registreren tags. De volgende afbeelding illustreert dit patroon.

![Registratie vanaf apparaat](./media/notification-hubs-registration-management/notification-hubs-registering-on-device.png)

Het apparaat haalt eerst de PNS-handgreep op bij de PNS en registreert vervolgens rechtstreeks met de meldingshub. Nadat de registratie is geslaagd, kan de back-end van de app een melding sturen die op die registratie is gericht. Zie [Routerings- en tagexpressies](notification-hubs-tags-segment-push-message.md)voor meer informatie over het verzenden van meldingen.

In dit geval gebruikt u alleen luisterrechten om vanaf het apparaat toegang te krijgen tot uw meldingshubs. Zie [Beveiliging voor](notification-hubs-push-notification-security.md)meer informatie.

Registreren vanaf het apparaat is de eenvoudigste methode, maar het heeft een aantal nadelen:

- Een client-app kan de tags alleen bijwerken wanneer de app actief is. Als een gebruiker bijvoorbeeld twee apparaten heeft die tags registreren die betrekking hebben op sportteams, wanneer het eerste apparaat zich registreert voor een extra tag (bijvoorbeeld Seahawks), ontvangt het tweede apparaat de meldingen over de Seahawks pas als de app op het tweede apparaat een tweede keer uitgevoerd. Meer in het algemeen, wanneer tags worden beïnvloed door meerdere apparaten, het beheer van tags vanaf de backend is een wenselijke optie.
- Aangezien apps kunnen worden gehackt, het beveiligen van de registratie op specifieke tags vereist extra zorg, zoals uitgelegd in het artikel [Beveiliging](notification-hubs-push-notification-security.md).

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-an-installation"></a>Voorbeeldcode om u te registreren met een meldingshub van een apparaat met behulp van een installatie

Op dit moment wordt dit alleen ondersteund met de [Notification Hubs REST API.](/rest/api/notificationhubs/create-overwrite-installation)

U de PATCH-methode ook gebruiken met de [JSON-Patch-standaard](https://tools.ietf.org/html/rfc6902) voor het bijwerken van de installatie.

```csharp
class DeviceInstallation
{
    public string installationId { get; set; }
    public string platform { get; set; }
    public string pushChannel { get; set; }
    public string[] tags { get; set; }
}

private async Task<HttpStatusCode> CreateOrUpdateInstallationAsync(DeviceInstallation deviceInstallation,
        string hubName, string listenConnectionString)
{
    if (deviceInstallation.installationId == null)
        return HttpStatusCode.BadRequest;

    // Parse connection string (https://msdn.microsoft.com/library/azure/dn495627.aspx)
    ConnectionStringUtility connectionSaSUtil = new ConnectionStringUtility(listenConnectionString);
    string hubResource = "installations/" + deviceInstallation.installationId + "?";
    string apiVersion = "api-version=2015-04";

    // Determine the targetUri that we will sign
    string uri = connectionSaSUtil.Endpoint + hubName + "/" + hubResource + apiVersion;

    //=== Generate SaS Security Token for Authorization header ===
    // See, https://msdn.microsoft.com/library/azure/dn495627.aspx
    string SasToken = connectionSaSUtil.getSaSToken(uri, 60);

    using (var httpClient = new HttpClient())
    {
        string json = JsonConvert.SerializeObject(deviceInstallation);

        httpClient.DefaultRequestHeaders.Add("Authorization", SasToken);

        var response = await httpClient.PutAsync(uri, new StringContent(json, System.Text.Encoding.UTF8, "application/json"));
        return response.StatusCode;
    }
}

var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

string installationId = null;
var settings = ApplicationData.Current.LocalSettings.Values;

// If we have not stored an installation ID in application data, create and store as application data.
if (!settings.ContainsKey("__NHInstallationId"))
{
    installationId = Guid.NewGuid().ToString();
    settings.Add("__NHInstallationId", installationId);
}

installationId = (string)settings["__NHInstallationId"];

var deviceInstallation = new DeviceInstallation
{
    installationId = installationId,
    platform = "wns",
    pushChannel = channel.Uri,
    //tags = tags.ToArray<string>()
};

var statusCode = await CreateOrUpdateInstallationAsync(deviceInstallation, 
                    "<HUBNAME>", "<SHARED LISTEN CONNECTION STRING>");

if (statusCode != HttpStatusCode.Accepted)
{
    var dialog = new MessageDialog(statusCode.ToString(), "Registration failed. Installation Id : " + installationId);
    dialog.Commands.Add(new UICommand("OK"));
    await dialog.ShowAsync();
}
else
{
    var dialog = new MessageDialog("Registration successful using installation Id : " + installationId);
    dialog.Commands.Add(new UICommand("OK"));
    await dialog.ShowAsync();
}
```

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration"></a>Voorbeeldcode om u te registreren met een meldingshub vanaf een apparaat met behulp van een registratie

Met deze methoden wordt een registratie gemaakt of bijgewerkt voor het apparaat waarop ze worden aangeroepen. Dit betekent dat om de handgreep of de tags bij te werken, u de volledige registratie moet overschrijven. Vergeet niet dat registraties van voorbijgaande aard zijn, dus je moet altijd een betrouwbare winkel hebben met de huidige tags die een specifiek apparaat nodig heeft.

```csharp
// Initialize the Notification Hub
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(listenConnString, hubName);

// The Device ID from the PNS
var pushChannel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

// If you are registering from the client itself, then store this registration ID in device
// storage. Then when the app starts, you can check if a registration ID already exists or not before
// creating.
var settings = ApplicationData.Current.LocalSettings.Values;

// If we have not stored a registration ID in application data, store in application data.
if (!settings.ContainsKey("__NHRegistrationId"))
{
    // make sure there are no existing registrations for this push handle (used for iOS and Android)    
    string newRegistrationId = null;
    var registrations = await hub.GetRegistrationsByChannelAsync(pushChannel.Uri, 100);
    foreach (RegistrationDescription registration in registrations)
    {
        if (newRegistrationId == null)
        {
            newRegistrationId = registration.RegistrationId;
        }
        else
        {
            await hub.DeleteRegistrationAsync(registration);
        }
    }

    newRegistrationId = await hub.CreateRegistrationIdAsync();

    settings.Add("__NHRegistrationId", newRegistrationId);
}

string regId = (string)settings["__NHRegistrationId"];

RegistrationDescription registration = new WindowsRegistrationDescription(pushChannel.Uri);
registration.RegistrationId = regId;
registration.Tags = new HashSet<string>(YourTags);

try
{
    await hub.CreateOrUpdateRegistrationAsync(registration);
}
catch (Microsoft.WindowsAzure.Messaging.RegistrationGoneException e)
{
    settings.Remove("__NHRegistrationId");
}
```

## <a name="registration-management-from-a-backend"></a>Registratiebeheer vanaf een backend

Voor het beheren van registraties vanaf de backend moet u extra code schrijven. De app van het apparaat moet de bijgewerkte PNS-greep naar de backend voorzien telkens wanneer de app wordt gestart (samen met tags en sjablonen) en de backend moet deze greep op de meldingshub bijwerken. De volgende foto illustreert dit ontwerp.

![Registratiebeheer](./media/notification-hubs-registration-management/notification-hubs-registering-on-backend.png)

De voordelen van het beheren van registraties vanaf de backend zijn de mogelijkheid om tags te wijzigen in registraties, zelfs wanneer de bijbehorende app op het apparaat inactief is, en om de client-app te verifiëren voordat u een tag toevoegt aan de registratie.

### <a name="example-code-to-register-with-a-notification-hub-from-a-backend-using-an-installation"></a>Voorbeeldcode om u te registreren met een meldingshub vanaf een backend met behulp van een installatie

Het clientapparaat krijgt nog steeds zijn PNS-handgreep en relevante installatie-eigenschappen zoals voorheen en roept een aangepaste API aan op de backend die de registratie kan uitvoeren en tags kan autoriseren enz. De backend kan gebruikmaken van de [Notification Hub SDK voor backend-bewerkingen.](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)

U de PATCH-methode ook gebruiken met de [JSON-Patch-standaard](https://tools.ietf.org/html/rfc6902) voor het bijwerken van de installatie.

```csharp
// Initialize the Notification Hub
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(listenConnString, hubName);

// Custom API on the backend
public async Task<HttpResponseMessage> Put(DeviceInstallation deviceUpdate)
{

    Installation installation = new Installation();
    installation.InstallationId = deviceUpdate.InstallationId;
    installation.PushChannel = deviceUpdate.Handle;
    installation.Tags = deviceUpdate.Tags;

    switch (deviceUpdate.Platform)
    {
        case "mpns":
            installation.Platform = NotificationPlatform.Mpns;
            break;
        case "wns":
            installation.Platform = NotificationPlatform.Wns;
            break;
        case "apns":
            installation.Platform = NotificationPlatform.Apns;
            break;
        case "fcm":
            installation.Platform = NotificationPlatform.Fcm;
            break;
        default:
            throw new HttpResponseException(HttpStatusCode.BadRequest);
    }


    // In the backend we can control if a user is allowed to add tags
    //installation.Tags = new List<string>(deviceUpdate.Tags);
    //installation.Tags.Add("username:" + username);

    await hub.CreateOrUpdateInstallationAsync(installation);

    return Request.CreateResponse(HttpStatusCode.OK);
}
```

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration-id"></a>Voorbeeldcode om u te registreren met een meldingshub vanaf een apparaat met een registratie-id

Vanuit uw back-end van de app u basis-CRUDS-bewerkingen uitvoeren op registraties. Bijvoorbeeld:

```csharp
var hub = NotificationHubClient.CreateClientFromConnectionString("{connectionString}", "hubName");

// create a registration description object of the correct type, e.g.
var reg = new WindowsRegistrationDescription(channelUri, tags);

// Create
await hub.CreateRegistrationAsync(reg);

// Get by ID
var r = await hub.GetRegistrationAsync<RegistrationDescription>("id");

// update
r.Tags.Add("myTag");

// update on hub
await hub.UpdateRegistrationAsync(r);

// delete
await hub.DeleteRegistrationAsync(r);
```

De backend moet gelijktijdigheid tussen registratieupdates verwerken. Service Bus biedt optimistische gelijktijdigheidscontrole voor registratiebeheer. Op HTTP-niveau wordt dit geïmplementeerd met het gebruik van ETag voor registratiebeheerbewerkingen. Deze functie wordt transparant gebruikt door Microsoft SDKs, die een uitzondering maken als een update om gelijktijdigheidsredenen wordt afgewezen. De back-end van de app is verantwoordelijk voor het afhandelen van deze uitzonderingen en het opnieuw proberen van de update indien nodig.
