---
author: sethmanheim
ms.service: service-bus
ms.topic: include
ms.date: 11/09/2018
ms.author: sethm
ms.openlocfilehash: 7fd161c90234d45a6751f173ba3685ee8c392c87
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "74260711"
---
## <a name="webapi-project"></a>WebAPI-project

1. Open in Visual Studio het **project appbackend** -project dat u hebt gemaakt in de zelf studie **gebruikers melden** .
2. Vervang in Notifications.cs de volledige **meldingen** klasse door de volgende code. Zorg ervoor dat u de tijdelijke aanduidingen vervangt door de connection string (met volledige toegang) voor uw notification hub en de naam van de hub. U kunt deze waarden verkrijgen via de [Azure Portal](https://portal.azure.com). Deze module bevat nu de verschillende beveiligde meldingen die worden verzonden. In een volledige implementatie worden de meldingen opgeslagen in een Data Base. ter vereenvoudiging: in dit geval slaan we ze op in het geheugen.
   
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

1. Vervang in NotificationsController.cs de code in de **Notifications controller** -klassedefinitie door de volgende code. Dit onderdeel implementeert een manier waarop het apparaat de melding veilig kan ophalen en biedt ook een manier (in het kader van deze zelf studie) om een beveiligde push naar uw apparaten te activeren. Houd er rekening mee dat bij het verzenden van de melding naar de notification hub alleen een onbewerkte melding met de ID van de melding (en geen echt bericht) wordt verzonden:
   
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

Houd er rekening mee dat de `Post` methode nu geen pop-upmelding verzendt. Er wordt een onbewerkte melding verzonden die alleen de meldings-ID en geen gevoelige inhoud bevat. Zorg er ook voor dat u de verzend bewerking bijwerkt voor de platforms waarvoor u geen referenties hebt geconfigureerd op uw notification hub, omdat deze fouten zullen veroorzaken.

1. Nu gaan we deze app opnieuw implementeren op een Azure-website om deze toegankelijk te maken vanaf alle apparaten. Klik met de rechtermuisknop op het project **AppBackend** en selecteer **Publiceren**.
2. Selecteer de Azure-website als uw publicatie doel. Meld u aan met uw Azure-account en selecteer een bestaande of nieuwe website en noteer de eigenschap **doel-URL** op het tabblad **verbinding** . Verderop in deze zelf studie wordt naar deze URL verwezen als *back-end-eind punt* . Klik op **Publish**.
