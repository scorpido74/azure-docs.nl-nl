---
author: sethmanheim
ms.service: service-bus
ms.topic: include
ms.date: 11/09/2018
ms.author: sethm
ms.openlocfilehash: 7fd161c90234d45a6751f173ba3685ee8c392c87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74260711"
---
## <a name="webapi-project"></a>WebAPI-project

1. Open in Visual Studio het **appbackend-project** dat u hebt gemaakt in de zelfstudie **Gebruikers melden.**
2. Vervang in Notifications.cs de hele **klasse Meldingen** door de volgende code. Zorg ervoor dat u de tijdelijke aanduidingen vervangt door uw verbindingstekenreeks (met volledige toegang) voor uw meldingshub en de hubnaam. U deze waarden verkrijgen via de [Azure-portal.](https://portal.azure.com) Deze module vertegenwoordigt nu de verschillende beveiligde meldingen die worden verzonden. In een volledige implementatie worden de meldingen opgeslagen in een database; voor eenvoud, in dit geval slaan we ze op in het geheugen.
   
   ```csharp
    public class Notification
    {
        public int Id { get; set; }
        public string Payload { get; set; }
        public bool Read { get; set; }
    }

    public class Notifications
    {
        public static Notifications Instance = new Notifications();

        private List<Notification> notifications = new List<Notification>();

        public NotificationHubClient Hub { get; set; }

        private Notifications() {
            Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}",     "{hub name}");
        }

        public Notification CreateNotification(string payload)
        {
            var notification = new Notification() {
            Id = notifications.Count,
            Payload = payload,
            Read = false
            };

            notifications.Add(notification);

            return notification;
        }

        public Notification ReadNotification(int id)
        {
            return notifications.ElementAt(id);
        }
    }
    ```

1. Vervang in NotificationsController.cs de code in de klassendefinitie **NotificationsController** door de volgende code. Dit onderdeel implementeert een manier voor het apparaat om de melding veilig op te halen en biedt ook een manier (ten behoeve van deze zelfstudie) om een veilige push naar uw apparaten te activeren. Houd er rekening mee dat we bij het verzenden van de melding naar de meldingshub alleen een onbewerkte melding sturen met de id van de melding (en geen daadwerkelijk bericht):
   
   ```csharp
    public NotificationsController()
    {
        Notifications.Instance.CreateNotification("This is a secure notification!");
    }

    // GET api/notifications/id
    public Notification Get(int id)
    {
        return Notifications.Instance.ReadNotification(id);
    }

    public async Task<HttpResponseMessage> Post()
    {
        var secureNotificationInTheBackend = Notifications.Instance.CreateNotification("Secure confirmation.");
        var usernameTag = "username:" + HttpContext.Current.User.Identity.Name;

        // windows
        var rawNotificationToBeSent = new Microsoft.Azure.NotificationHubs.WindowsNotification(secureNotificationInTheBackend.Id.ToString(),
                        new Dictionary<string, string> {
                            {"X-WNS-Type", "wns/raw"}
                        });
        await Notifications.Instance.Hub.SendNotificationAsync(rawNotificationToBeSent, usernameTag);

        // apns
        await Notifications.Instance.Hub.SendAppleNativeNotificationAsync("{\"aps\": {\"content-available\": 1}, \"secureId\": \"" + secureNotificationInTheBackend.Id.ToString() + "\"}", usernameTag);

        // gcm
        await Notifications.Instance.Hub.SendGcmNativeNotificationAsync("{\"data\": {\"secureId\": \"" + secureNotificationInTheBackend.Id.ToString() + "\"}}", usernameTag);

        return Request.CreateResponse(HttpStatusCode.OK);
    }
    ```

Houd er `Post` rekening mee dat de methode nu geen pop-upmelding verzendt. Het stuurt een onbewerkte melding die alleen de meldings-ID bevat, en geen gevoelige inhoud. Zorg er ook voor dat u commentaar geeft op de verzendbewerking voor de platforms waarvoor u geen referenties hebt geconfigureerd op uw meldingshub, omdat deze fouten zullen veroorzaken.

1. Nu zullen we deze app opnieuw implementeren op een Azure-website om deze toegankelijk te maken vanaf alle apparaten. Klik met de rechtermuisknop op het project **AppBackend** en selecteer **Publiceren**.
2. Selecteer Azure-website als uw publicatiedoel. Meld u aan met uw Azure-account en selecteer een bestaande of nieuwe website en noteer de eigenschap **bestemmings-URL** op het tabblad **Verbinding.** We zullen verwijzen naar deze URL als uw *backend eindpunt* later in deze tutorial. Klik op **Publish**.
