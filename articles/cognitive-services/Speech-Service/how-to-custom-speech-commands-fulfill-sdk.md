---
title: Opdrachten uitvoeren vanuit een client met de Speech SDK
titleSuffix: Azure Cognitive Services
description: In dit artikel wordt uitgelegd hoe u aangepaste opdrachten voor activiteiten op een client met de spraak-SDK kunt afhandelen.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: trbye
ms.openlocfilehash: f11f5f3c2ad4c9f0241d34edeb664f739f88d15c
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871793"
---
# <a name="fulfill-commands-from-a-client-with-the-speech-sdk-preview"></a>Opdrachten uitvoeren vanuit een client met de Speech SDK (preview)

Als u taken wilt volt ooien met behulp van een aangepaste opdrachten toepassing, kunt u aangepaste payloads verzenden naar een verbonden client apparaat.

In dit artikel voert u de volgende handelingen uit:

- Een aangepaste JSON-nettolading definiëren en verzenden vanuit uw toepassing voor aangepaste opdrachten
- De aangepaste inhoud van de JSON-nettolading ontvangen en visualiseren vanuit een C# UWP Speech SDK-client toepassing

## <a name="prerequisites"></a>Vereisten
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
> * Een Azure-abonnements sleutel voor de speech-service: [Haal er gratis een](get-started.md) op en maak deze op de [Azure Portal](https://portal.azure.com)
> * Een eerder gemaakte aangepaste opdrachten-app: [Snelstartgids: een aangepaste opdracht maken met para meters (preview)](./quickstart-custom-speech-commands-create-parameters.md)
> * Een Speech SDK met ingeschakelde client toepassing: [Quick Start: verbinding maken met een aangepaste opdracht toepassing met de spraak-SDK (preview)](./quickstart-custom-speech-commands-speech-sdk.md)

## <a name="optional-get-started-fast"></a>Optioneel: snel aan de slag

In dit artikel wordt stapsgewijs beschreven hoe u een client toepassing kunt maken om te praten met uw toepassing voor aangepaste opdrachten. Als u liever meteen wilt beginnen, is de volledige, kant-en-klaar-assembly-bron code die in dit artikel wordt gebruikt, beschikbaar in de voor [beelden van spraak-SDK](https://aka.ms/csspeech/samples).

## <a name="fulfill-with-json-payload"></a>Voldoen aan JSON-nettolading

1. Open de toepassing voor aangepaste opdrachten die u eerder hebt gemaakt op basis van [Snelstartgids: een aangepaste opdracht met para meters maken](./quickstart-custom-speech-commands-create-parameters.md)
1. Controleer de sectie **voltooiings regels** om ervoor te zorgen dat u de eerder gemaakte regel hebt die terugkeert naar de gebruiker
1. Als u een Payload rechtstreeks naar de client wilt verzenden, maakt u een nieuwe regel met de actie activiteit verzenden

   > [!div class="mx-imgBorder"]
   > ![Voltooiings regel voor activiteit verzenden](media/custom-speech-commands/fulfill-sdk-completion-rule.png)

   | Instelling | Voorgestelde waarde | Beschrijving |
   | ------- | --------------- | ----------- |
   | Regelnaam | UpdateDeviceState | Een naam die het doel van de regel beschrijft |
   | Voorwaarden | Vereiste para meter `OnOff` -en`SubjectDevice` | Voor waarden die bepalen wanneer de regel kan worden uitgevoerd |
   | Acties | `SendActivity`(zie hieronder) | De actie die moet worden uitgevoerd wanneer de regel voorwaarde waar is |

1. Kopieer de JSON hieronder naar **activiteit inhoud**
   ```json
   {
     "type": "event",
     "name": "UpdateDeviceState",
     "state": "{OnOff}",
     "device": "{SubjectDevice}"
   }
   ```
   > [!div class="mx-imgBorder"]
   > ![Nettolading activiteit verzenden](media/custom-speech-commands/fulfill-sdk-send-activity-action.png)

## <a name="create-visuals-for-device-on-or-off-state"></a>Visualisaties maken voor het apparaat in-of uitschakelen

In [Quick Start: verbinding maken met een aangepaste opdracht toepassing met de Speech SDK](./quickstart-custom-speech-commands-speech-sdk.md), u hebt een Speech SDK-client toepassing gemaakt die `turn on the tv`opdrachten `turn off the fan`verwerkt zoals,. Als er visuele elementen zijn toegevoegd, kunt u het resultaat van deze opdrachten bekijken.

Voeg gelabelde vakken met tekst **in** of **uit** aan de hand van de volgende XML-code die is toegevoegd aan`MainPage.xaml`

```xml
<StackPanel Orientation="Horizontal" HorizontalAlignment="Center" Margin="20">
    <Grid x:Name="Grid_TV" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="TV" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_TV" Text="Off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
    <Grid x:Name="Grid_Fan" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="Fan" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_Fan" Text="Off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
</StackPanel>
```

## <a name="handle-customizable-payload"></a>Aanpas bare nettolading verwerken
### <a name="add-reference-libraries"></a>Referentie bibliotheken toevoegen

Omdat u een JSON-nettolading hebt gemaakt, moet u een verwijzing naar de [JSON.net](https://www.newtonsoft.com/json) -bibliotheek toevoegen om de deserialisatie te verwerken.
- Rechts-client uw oplossing.
- Kies **NuGet-pakketten beheren voor oplossing**, selecteer **installeren** 
- Zoek naar **Newton soft. json** in de update lijst, werk **micro soft. NetCore. UniversalWindowsPlatform** bij naar de nieuwste versie

> [!div class="mx-imgBorder"]
> ![Nettolading activiteit verzenden](media/custom-speech-commands/fulfill-sdk-json-nuget.png)

Voeg in ' MainPage. xaml. cs '
- `using Newtonsoft.Json;` 
- `using Windows.ApplicationModel.Core;`

### <a name="handle-received-payload"></a>Ingang ontvangen nettolading

Vervang `InitializeDialogServiceConnector`in de gebeurtenis `ActivityReceived` -handler door de volgende code. De gewijzigde `ActivityReceived` gebeurtenis-handler haalt de payload op uit de activiteit en wijzigt de visuele status van de TV of ventilator dienovereenkomstig.

```C#
connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
{
    NotifyUser($"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

    dynamic activity = JsonConvert.DeserializeObject(activityReceivedEventArgs.Activity);
    var name = activity?.name != null ? activity.name.ToString() : string.Empty;

    if (name.Equals("UpdateDeviceState"))
    {
        Debug.WriteLine("Here");
        var state = activity?.device != null ? activity.state.ToString() : string.Empty;
        var device = activity?.device != null ? activity.device.ToString() : string.Empty;

        if (state.Equals("on") || state.Equals("off"))
        {
            switch (device)
            {
                case "tv":
                    await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
                        CoreDispatcherPriority.Normal, () => { State_TV.Text = state; });
                    break;
                case "fan":
                    await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
                        CoreDispatcherPriority.Normal, () => { State_Fan.Text = state; });
                    break;
                default:
                    NotifyUser($"Received request to set unsupported device {device} to {state}");
                    break;
            }
        }
        else { 
            NotifyUser($"Received request to set unsupported state {state}");
        }
    }

    if (activityReceivedEventArgs.HasAudio)
    {
        SynchronouslyPlayActivityAudio(activityReceivedEventArgs.Audio);
    }
};
```

## <a name="try-it-out"></a>Beleid uitproberen

1. De toepassing starten
1. Selecteer microfoon inschakelen
1. Selecteer de knop spreken
1. Spreekt`turn on the tv`
1. De visuele status van de TV moet worden gewijzigd in aan
   > [!div class="mx-imgBorder"]
   > ![Nettolading activiteit verzenden](media/custom-speech-commands/fulfill-sdk-turn-on-tv.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Procedure: validaties toevoegen aan aangepaste opdracht parameters (preview-versie)](./how-to-custom-speech-commands-validations.md)
