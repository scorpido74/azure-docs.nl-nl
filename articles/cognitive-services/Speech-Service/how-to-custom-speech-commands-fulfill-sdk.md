---
title: Opdrachten uitvoeren vanuit een client met de Speech SDK
titleSuffix: Azure Cognitive Services
description: In dit artikel wordt uitgelegd hoe u aangepaste opdrachten voor activiteiten op een client met de spraak-SDK kunt afhandelen.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: donkim
ms.openlocfilehash: e109955774722da7f55defe1417de35ff202cce8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79367735"
---
# <a name="fulfill-commands-from-a-client-with-the-speech-sdk-preview"></a>Opdrachten uitvoeren vanuit een client met de Speech SDK (preview)

Als u taken wilt volt ooien met behulp van een aangepaste opdrachten toepassing, kunt u aangepaste payloads verzenden naar een verbonden client apparaat.

In dit artikel voert u de volgende handelingen uit:

- Een aangepaste JSON-nettolading definiëren en verzenden vanuit uw toepassing voor aangepaste opdrachten
- De aangepaste inhoud van de JSON-nettolading ontvangen en visualiseren vanuit een C# UWP Speech SDK-client toepassing

## <a name="prerequisites"></a>Vereisten

- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
- Een sleutel van een Azure-abonnement voor de speech-service
  - [Ontvang een gratis versie](get-started.md) of maak deze op de [Azure Portal](https://portal.azure.com)
- Een eerder gemaakte aangepaste opdrachten-app
  - [Snelstartgids: een aangepaste opdracht maken met para meters (preview)](./quickstart-custom-speech-commands-create-parameters.md)
- Een Speech SDK met ingeschakelde client toepassing
  - [Quick Start: verbinding maken met een aangepaste opdracht toepassing met de spraak-SDK (preview)](./quickstart-custom-speech-commands-speech-sdk.md)

## <a name="optional-get-started-fast"></a>Optioneel: snel aan de slag

In dit artikel wordt stapsgewijs beschreven hoe u een client toepassing kunt maken om te praten met uw toepassing voor aangepaste opdrachten. Als u liever meteen wilt beginnen, is de volledige, kant-en-klaar-assembly-bron code die in dit artikel wordt gebruikt, beschikbaar in de voor [beelden van spraak-SDK](https://aka.ms/csspeech/samples).

## <a name="fulfill-with-json-payload"></a>Voldoen aan JSON-nettolading

1. Open de eerder gemaakte toepassing voor aangepaste opdrachten vanuit de [Speech Studio](https://speech.microsoft.com/)
1. Controleer de sectie **voltooiings regels** om ervoor te zorgen dat u de eerder gemaakte regel hebt die terugkeert naar de gebruiker
1. Als u een Payload rechtstreeks naar de client wilt verzenden, maakt u een nieuwe regel met de actie activiteit verzenden

   > [!div class="mx-imgBorder"]
   > ![Voltooiings regel voor activiteit verzenden](media/custom-speech-commands/fulfill-sdk-completion-rule.png)

   | Instelling | Voorgestelde waarde | Beschrijving |
   | ------- | --------------- | ----------- |
   | Regelnaam | UpdateDeviceState | Een naam die het doel van de regel beschrijft |
   | Voorwaarden | Vereiste para meter `OnOff` -en`SubjectDevice` | Voor waarden die bepalen wanneer de regel kan worden uitgevoerd |
   | Acties | `SendActivity`(zie hieronder) | De actie die moet worden uitgevoerd wanneer de regel voorwaarde waar is |

   > [!div class="mx-imgBorder"]
   > ![Nettolading activiteit verzenden](media/custom-speech-commands/fulfill-sdk-send-activity-action.png)

   ```json
   {
     "type": "event",
     "name": "UpdateDeviceState",
     "state": "{OnOff}",
     "device": "{SubjectDevice}"
   }
   ```

## <a name="create-visuals-for-device-on-or-off-state"></a>Visualisaties maken voor het apparaat in-of uitschakelen

In [Quick Start: verbinding maken met een aangepaste opdracht toepassing met de Speech-SDK (preview)](./quickstart-custom-speech-commands-speech-sdk.md) u hebt een Speech SDK-client toepassing gemaakt `turn on the tv`die `turn off the fan`opdrachten verwerkt zoals,. Voeg nu enkele visuele elementen toe zodat u het resultaat van deze opdrachten kunt zien.

Voeg gelabelde vakken met tekst **in** of **uit** aan de hand van de volgende XML-code die is toegevoegd aan`MainPage.xaml.cs`

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

Nu u een JSON-nettolading hebt gemaakt, kunt u een verwijzing naar de [JSON.net](https://www.newtonsoft.com/json) -bibliotheek voor het afhandelen van deserialisatie toevoegen.

> [!div class="mx-imgBorder"]
> ![Nettolading activiteit verzenden](media/custom-speech-commands/fulfill-sdk-json-nuget.png)

In `InitializeDialogServiceConnector` Voeg het volgende toe aan `ActivityReceived` uw gebeurtenis-handler. Met de extra code wordt de payload geëxtraheerd uit de activiteit en wordt de visuele status van de TV of ventilator dienovereenkomstig gewijzigd.

```C#
connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
{
    NotifyUser($"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

    dynamic activity = JsonConvert.DeserializeObject(activityReceivedEventArgs.Activity);

    if(activity?.name == "SetDeviceState")
    {
        var state = activity?.state;
        var device = activity?.device;
        switch(device)
        {
            case "tv":
                State_TV.Text = state;
                break;
            case "fan":
                State_Fan.Text = state;
                break;
            default:
                NotifyUser($"Received request to set unsupported device {device} to {state}");
                break;
        }
    }

    if (activityReceivedEventArgs.HasAudio)
    {
        SynchronouslyPlayActivityAudio(activityReceivedEventArgs.Audio);
    }
};
```

## <a name="try-it-out"></a>Uitproberen

1. De toepassing starten
1. Selecteer microfoon inschakelen
1. Selecteer de knop spreken
1. Spreekt`turn on the tv`
1. De visuele status van de TV moet worden gewijzigd in aan

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Procedure: validaties toevoegen aan aangepaste opdracht parameters (preview-versie)](./how-to-custom-speech-commands-validations.md)
