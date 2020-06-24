---
title: Web-eind punten instellen (preview-versie)
titleSuffix: Azure Cognitive Services
description: Web-eind punten instellen voor aangepaste opdrachten
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 5bdb77d27b01f576ca06aa5b6d3df0572b3b1ea6
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/24/2020
ms.locfileid: "85307557"
---
# <a name="set-up-web-endpoints"></a>Web-eind punten instellen

In dit artikel leert u hoe u Web-eind punten kunt instellen in een toepassing met aangepaste opdrachten waarmee u HTTP-aanvragen van een client toepassing kunt maken. U voert de volgende taken uit:

- Web-eind punten instellen in de toepassing aangepaste opdrachten
- Web-eind punten aanroepen in de toepassing aangepaste opdrachten
- Reactie van het web-eind punt ontvangen 
- De reactie van het web-eind punt integreren in een aangepaste JSON-Payload, verzenden en visualiseren vanuit een C# UWP Speech SDK-client toepassing

## <a name="prerequisites"></a>Vereisten
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
> * Een Azure-abonnements sleutel voor de speech-service: [Haal er gratis een](get-started.md) op en maak deze op de [Azure Portal](https://portal.azure.com)
> * Een eerder [gemaakte aangepaste opdrachten-app](quickstart-custom-commands-application.md)
> * Een Speech SDK met ingeschakelde client [-app: instructies: activiteit beëindigen voor client toepassing](./how-to-custom-commands-setup-speech-sdk.md)

## <a name="setup-web-endpoints"></a>Web-eind punten instellen

1. Open de toepassing voor aangepaste opdrachten die u eerder hebt gemaakt. 
1. Ga naar ' Web-eind punten ' en klik op ' nieuw web-eind punt '.

   > [!div class="mx-imgBorder"]
   > ![Nieuw web-eind punt](media/custom-commands/setup-web-endpoint-new-endpoint.png)

   | Instelling | Voorgestelde waarde | Beschrijving |
   | ------- | --------------- | ----------- |
   | Naam | UpdateDeviceState | Naam voor het eind punt van de website. |
   | URL | https://webendpointexample.azurewebsites.net/api/DeviceState | De URL van het eind punt waarop u wilt communiceren met uw aangepaste opdracht-app. |
   | Methode | POST | De toegestane interacties (zoals GET, POST) met uw eind punt.|
   | Kopteksten | Sleutel: app, waarde: een unieke naam voor uw app | De header-para meters die moeten worden meegenomen in de aanvraag header.|

    > [!NOTE]
    > - Het voor beeld-webeindpunt dat is gemaakt met behulp van [Azure function](https://docs.microsoft.com/azure/azure-functions/), waarmee de status van de apparaat-en ventilator wordt opgeslagen in de data base
    > - De voorgestelde header is alleen nodig voor het voorbeeld eindpunt
    > - In de praktijk kan het eind punt van het Web het eind punt zijn voor de [IOT-hub](https://docs.microsoft.com/azure/iot-hub/about-iot-hub) waarmee uw apparaten worden beheerd

1. Klik op **Opslaan**.

## <a name="call-web-endpoints"></a>Web-eind punten aanroepen

1. Ga naar de opdracht **TurnOnOff** , selecteer **ConfirmationResponse** onder voltooiings regel en selecteer vervolgens **een actie toevoegen**.
1. Onder **nieuw actie-type**selecteert u **Web-eind punt aanroepen**
1. Selecteer in **actie-eind punten bewerken**de optie **UpdateDeviceState**, het web-eind punt dat we hebben gemaakt.  
1. Plaats in **configuratie**de volgende waarden: 
   > [!div class="mx-imgBorder"]
   > ![Actie parameters voor web-eind punten aanroepen](media/custom-commands/setup-web-endpoint-edit-action-parameters.png)

   | Instelling | Voorgestelde waarde | Beschrijving |
   | ------- | --------------- | ----------- |
   | Eindpunten | UpdateDeviceState | Het web-eind punt dat u wilt aanroepen in deze actie. |
   | Queryparameters | item = {SubjectDevice} &&waarde = {OnOff} | De query parameters die moeten worden toegevoegd aan de URL van het webeindpunt.  |
   | Inhoud van hoofd tekst | N.v.t. | De inhoud van de hoofd tekst van de aanvraag. |

    > [!NOTE]
    > - De voorgestelde query parameters zijn alleen nodig voor het voorbeeld eindpunt

1. In **bij geslaagde actie om uit te voeren**, selecteert u **spraak antwoord verzenden**.
   
   > [!div class="mx-imgBorder"]
   > ![De actie Web-eind punten aanroepen bij een geslaagde poging](media/custom-commands/setup-web-endpoint-edit-action-on-success-send-response.png)

   | Instelling | Voorgestelde waarde | Beschrijving |
   | ------- | --------------- | ----------- |
   | Actie die moet worden uitgevoerd | Spraak antwoord verzenden | Actie die moet worden uitgevoerd als het eind punt voor de aanvraag aan webeindpunt slaagt |
   
   > [!NOTE]
   > - U kunt de velden in het HTTP-antwoord ook rechtstreeks openen met behulp van `{YourWebEndpointName.FieldName}` . Bijvoorbeeld: `{UpdateDeviceState.TV}`

1. Selecteer in **bij fout-actie om uit te voeren**de optie **spraak antwoord verzenden**
   > [!div class="mx-imgBorder"]
   > ![Actie voor het aanroepen van web-eind punten bij een fout](media/custom-commands/setup-web-endpoint-edit-action-on-fail.png)

   | Instelling | Voorgestelde waarde | Beschrijving |
   | ------- | --------------- | ----------- |
   | Actie die moet worden uitgevoerd | Spraak antwoord verzenden | Actie die moet worden uitgevoerd als het eind punt voor de aanvraag voor webeindpunt mislukt |

   > [!NOTE]
   > - `{WebEndpointErrorMessage}` is optioneel. U kunt deze verwijderen als u geen fout berichten wilt weer geven.
   > - Binnen het voor beeld van het eind punt sturen we http-antwoorden met gedetailleerde fout berichten voor veelvoorkomende fouten, zoals ontbrekende header-para meters. 

### <a name="try-it-out-in-test-portal"></a>Uitproberen in de test Portal
- Op geslaagde antwoorden \
Opslaan, trainen en testen
   > [!div class="mx-imgBorder"]
   > ![De actie Web-eind punten aanroepen bij een geslaagde poging](media/custom-commands/setup-web-endpoint-on-success-response.png)
- Fout bericht bij mislukken \
Een van de query parameters verwijderen, opslaan, opnieuw trainen en testen
   > [!div class="mx-imgBorder"]
   > ![De actie Web-eind punten aanroepen bij een geslaagde poging](media/custom-commands/setup-web-endpoint-on-fail-response.png)

## <a name="integrate-with-client-application"></a>Integreren met client toepassing

In de [procedure: activiteit verzenden naar client toepassing (preview)](./how-to-custom-commands-send-activity-to-client.md), hebt u een **activiteit verzenden aan client** toegevoegd. De activiteit wordt verzonden naar de client toepassing, ongeacht of de actie **Web-eind punt aanroepen** al dan niet is geslaagd.
In de meeste gevallen wilt u echter alleen een activiteit verzenden naar de client toepassing wanneer het aanroepen van het web-eind punt is geslaagd. In dit voor beeld is dit wanneer de status van het apparaat is bijgewerkt.

1. Verwijder de actie **activiteit verzenden naar client** die u eerder hebt toegevoegd.
1. Aanroepen van web-eind punt bewerken: 
    1. Zorg ervoor dat in **configuratie**de **query parameters**`item={SubjectDevice}&&value={OnOff}`
    1. Bij **geslaagd**, actie wijzigen in **uitvoeren** om **activiteit naar de client te verzenden**
    1. Kopieer de JSON hieronder naar de **inhoud** van de activiteit
   ```json
   {
     "type": "event",
     "name": "UpdateDeviceState",
     "state": "{OnOff}",
     "device": "{SubjectDevice}"
   }
   ```
    > [!div class="mx-imgBorder"]
    > ![Activiteit bij voltooiing verzenden](media/custom-commands/setup-web-endpoint-edit-action-on-success-send-activity.png)
   
U kunt nu alleen een activiteit verzenden naar de client wanneer het eind punt van de aanvraag aan het web is geslaagd.

### <a name="create-visuals-for-syncing-device-state"></a>Visualisaties maken voor het synchroniseren van de apparaatstatus
Voeg het volgende XML-bestand toe aan `MainPage.xaml` het `"EnableMicrophoneButton"` blok.

```xml
<Button x:Name="SyncDeviceStateButton" Content="Sync Device State"
        Margin="0,10,10,0" Click="SyncDeviceState_ButtonClicked"
        Height="35"/>
<Button x:Name="EnableMicrophoneButton" ......
        .........../>
```

### <a name="sync-device-state"></a>Status synchronisatie apparaat 

`MainPage.xaml.cs`Voeg in de verwijzing toe `using Windows.Web.Http;` . Voeg de volgende code toe aan de klasse `MainPage`. Met deze methode wordt een GET-aanvraag verzonden naar het voorbeeld eindpunt en wordt de huidige Apparaatstatus opgehaald voor uw app. Zorg ervoor dat `<your_app_name>` u wijzigt naar wat u in de **koptekst** hebt gebruikt in het aangepaste web-eind punt voor de opdracht

```C#
private async void SyncDeviceState_ButtonClicked(object sender, RoutedEventArgs e)
{
    //Create an HTTP client object
    var httpClient = new HttpClient();

    //Add a user-agent header to the GET request. 
    var your_app_name = "<your-app-name>";

    Uri endpoint = new Uri("https://webendpointexample.azurewebsites.net/api/DeviceState");
    var requestMessage = new HttpRequestMessage(HttpMethod.Get, endpoint);
    requestMessage.Headers.Add("app", $"{your_app_name}");

    try
    {
        //Send the GET request
        var httpResponse = await httpClient.SendRequestAsync(requestMessage);
        httpResponse.EnsureSuccessStatusCode();
        var httpResponseBody = await httpResponse.Content.ReadAsStringAsync();
        dynamic deviceState = JsonConvert.DeserializeObject(httpResponseBody);
        var TVState = deviceState.TV.ToString();
        var FanState = deviceState.Fan.ToString();
        await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
            CoreDispatcherPriority.Normal,
            () =>
            {
                State_TV.Text = TVState;
                State_Fan.Text = FanState;
            });
    }
    catch (Exception ex)
    {
        NotifyUser(
            $"Unable to sync device status: {ex.Message}");
    }
}
```

## <a name="try-it-out"></a>Uitproberen

1. De toepassing starten
1. Klik op status van apparaat synchroniseren. \
Als u de app met in de `turn on tv` vorige sectie hebt getest, ziet u dat de TV wordt weer gegeven als ' aan '.
    > [!div class="mx-imgBorder"]
    > ![Status synchronisatie apparaat](media/custom-commands/setup-web-endpoint-sync-device-state.png)
1. Selecteer microfoon inschakelen
1. Selecteer de knop spreken
1. Spreekt`turn on the fan`
1. De visuele status van de ventilator moet worden gewijzigd in aan
    > [!div class="mx-imgBorder"]
    > ![Ventilator inschakelen](media/custom-commands/setup-web-endpoint-turn-on-fan.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een CI/CD-proces voor de toepassing van uw aangepaste opdrachten inschakelen](./how-to-custom-commands-deploy-cicd.md)