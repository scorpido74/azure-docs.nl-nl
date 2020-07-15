---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 5e75c5d5510f596eb7911cae0310e60b6bef67bf
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86146477"
---
### <a name="send-a-test-notification"></a>Een testmelding verzenden

1. Een nieuw tabblad openen in [Postman](https://www.postman.com/downloads/).

1. Stel de aanvraag in op **POST** en voer het volgende adres in:

    ```xml
    https://<app_name>.azurewebsites.net/api/notifications/requests
    ```

1. Als u ervoor hebt gekozen om het gedeelte [Clients verifiëren met behulp van een API-sleutel](#authenticate-clients-using-an-api-key-optional) te voltooien, moet u de aanvraagheaders zo configureren dat deze uw **apikey**-waarde bevatten.

   | Sleutel                            | Waarde                          |
   | ------------------------------ | ------------------------------ |
   | apikey                         | <your_api_key>                 |

1. Kies de optie **onbewerkt** voor de **Hoofdtekst**, kies **JSON** in de lijst met indelingsopties en voeg vervolgens enkele tijdelijke aanduidingen voor **JSON**-content toe:

    ```json
    {
        "text": "Message from Postman!",
        "action": "action_a"
    }
    ```

1. Selecteer de knop **Code**. Deze bevindt zich onder de knop **Opslaan** in de rechterbovenhoek van het venster. De aanvraag moet er ongeveer uitzien als in het volgende voorbeeld wanneer deze wordt weergegeven voor **HTML** (afhankelijk van of u een **apikey**-header hebt opgenomen):

    ```html
    POST /api/notifications/requests HTTP/1.1
    Host: https://<app_name>.azurewebsites.net
    apikey: <your_api_key>
    Content-Type: application/json

    {
        "text": "Message from backend service",
        "action": "action_a"
    }
    ```

1. Voer de **PushDemo**-toepassing uit op een of beide doelplatforms (**Android** en **iOS**).

    > [!NOTE]
    > Als u test op **Android**, controleert uw toepassing of u niet wordt uitgevoerd in **fouten opsporen**, of als de app is geïmplementeerd door de toepassing uit te voeren, dwingt u de app te sluiten en start u deze opnieuw vanaf het startprogramma.

1. Tik in de **PushDemo**-app op de knop **Registreren**.

1. Ga terug naar **[Postman](https://www.postman.com/downloads)** , sluit het venster **Codefragmenten genereren** (als u dit nog niet hebt gedaan) en klik vervolgens op de knop **Verzenden**.

1. Controleer of u een **200 OK** antwoord hebt ontvangen in **[Postman](https://www.postman.com/downloads)** en of de waarschuwing wordt weergegeven in de app met **ActieA-actie ontvangen**.  

1. Sluit de **PushDemo**-app en klik opnieuw op de knop **Verzenden** in **[Postman](https://www.postman.com/downloads)** .

1. Controleer of u weer een **200 OK**-antwoord hebt ontvangen in **[Postman](https://www.postman.com/downloads)** . Controleer of er een melding wordt weergegeven in het systeemvak voor de **PushDemo**-app met het juiste bericht.

1. Tik op de melding om te bevestigen dat de app wordt geopend en de waarschuwing **ActieA-actie ontvangen** wordt weergegeven.

1. Nadat u terug bent in **[Postman](https://www.postman.com/downloads)** , wijzigt u de vorige aanvraagbody om een stille melding te verzenden, waarin *actie-b* wordt opgegeven in plaats van *actie_a* voor de waarde **actie**.

    ```json
    {
        "action": "action_b",
        "silent": true
    }
    ```

1. Als de app nog is geopend, klikt u op de knop **Verzenden** in **[Postman](https://www.postman.com/downloads)** .

1. Controleer of u een **200 OK** antwoord hebt ontvangen in **[Postman](https://www.postman.com/downloads)** en of de waarschuwing wordt weergegeven in de app met **ActieB-actie ontvangen** in plaats van met **ActieA-actie ontvangen**.

1. Sluit de **PushDemo**-app en klik opnieuw op de knop **Verzenden** in **[Postman](https://www.postman.com/downloads)** .

1. Controleer of u een **200 OK**-antwoord hebt ontvangen in **[Postman](https://www.postman.com/downloads)** en of de melding op de achtergrond niet wordt weergegeven in het systeemvak.
