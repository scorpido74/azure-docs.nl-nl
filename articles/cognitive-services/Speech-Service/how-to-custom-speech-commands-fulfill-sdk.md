---
title: Opdrachten van een client uitvoeren met de Speech SDK
titleSuffix: Azure Cognitive Services
description: In dit artikel leggen we uit hoe u aangepaste opdrachtenactiviteiten op een client afhandelen met de Speech SDK.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: donkim
ms.openlocfilehash: e109955774722da7f55defe1417de35ff202cce8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367735"
---
# <a name="fulfill-commands-from-a-client-with-the-speech-sdk-preview"></a>Opdrachten van een client uitvoeren met de Spraak-SDK (Voorbeeld)

Als u taken wilt voltooien met een toepassing Aangepaste opdrachten, u aangepaste payloads verzenden naar een verbonden clientapparaat.

In dit artikel:

- Een aangepaste JSON-payload definiëren en verzenden vanuit uw toepassing Aangepaste opdrachten
- De aangepaste JSON-payload-inhoud ontvangen en visualiseren van een C# UWP Speech SDK-clienttoepassing

## <a name="prerequisites"></a>Vereisten

- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
- Een Azure-abonnementssleutel voor spraakservice
  - [Gratis een gratis of](get-started.md) maak het op de [Azure-portal](https://portal.azure.com)
- Een eerder gemaakte app Aangepaste opdrachten
  - [Snelstart: een aangepaste opdracht maken met parameters (voorbeeld)](./quickstart-custom-speech-commands-create-parameters.md)
- Een clienttoepassing met spraak-SDK
  - [Snelstart: verbinding maken met een aangepaste opdrachttoepassing met de Spraak-SDK (Voorbeeld)](./quickstart-custom-speech-commands-speech-sdk.md)

## <a name="optional-get-started-fast"></a>Optioneel: snel aan de slag

In dit artikel wordt stap voor stap beschreven hoe u een clienttoepassing maken om met uw toepassing Aangepaste opdrachten te praten. Als u er liever in duikt, is de volledige, kant-en-klare broncode die in dit artikel wordt gebruikt, beschikbaar in de [Speech SDK Samples.](https://aka.ms/csspeech/samples)

## <a name="fulfill-with-json-payload"></a>Vervul met JSON payload

1. De eerder gemaakte toepassing Aangepaste opdrachten openen vanuit de [Spraakstudio](https://speech.microsoft.com/)
1. Controleer de sectie **Voltooiingsregels** om te controleren of u de eerder gemaakte regel hebt die op de gebruiker reageert
1. Als u een payload rechtstreeks naar de client wilt verzenden, maakt u een nieuwe regel met de actie Activiteit verzenden

   > [!div class="mx-imgBorder"]
   > ![Voltooiingsregel Activiteit verzenden](media/custom-speech-commands/fulfill-sdk-completion-rule.png)

   | Instelling | Voorgestelde waarde | Beschrijving |
   | ------- | --------------- | ----------- |
   | Regelnaam | UpdateDeviceState | Een naam die het doel van de regel beschrijft |
   | Voorwaarden | Vereiste parameter `OnOff` - en`SubjectDevice` | Voorwaarden die bepalen wanneer de regel kan worden uitgevoerd |
   | Acties | `SendActivity`(zie hieronder) | De actie die moet worden ondernomen wanneer de regelvoorwaarde waar is |

   > [!div class="mx-imgBorder"]
   > ![Activiteitspayload verzenden](media/custom-speech-commands/fulfill-sdk-send-activity-action.png)

   ```json
   {
     "type": "event",
     "name": "UpdateDeviceState",
     "state": "{OnOff}",
     "device": "{SubjectDevice}"
   }
   ```

## <a name="create-visuals-for-device-on-or-off-state"></a>Visuals maken voor apparaat in- of uitschakelen-status

In [Quickstart: Maak verbinding met een aangepaste opdrachttoepassing met de Speech SDK (Preview)](./quickstart-custom-speech-commands-speech-sdk.md) hebt u een Speech SDK-clienttoepassing gemaakt die opdrachten heeft verwerkt zoals `turn on the tv`, `turn off the fan`. Voeg nu wat visuals toe, zodat u het resultaat van deze opdrachten zien.

Gelabelde vakken toevoegen met tekst die **aan** of **uit** geeft met de volgende XML toegevoegd aan`MainPage.xaml.cs`

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

## <a name="handle-customizable-payload"></a>Omgaan met aanpasbare payload

Nu u een JSON-payload hebt gemaakt, u een verwijzing toevoegen naar de [JSON.NET](https://www.newtonsoft.com/json) bibliotheek om deserialisatie te verwerken.

> [!div class="mx-imgBorder"]
> ![Activiteitspayload verzenden](media/custom-speech-commands/fulfill-sdk-json-nuget.png)

Voeg `InitializeDialogServiceConnector` het volgende `ActivityReceived` toe aan uw gebeurtenishandler. De extra code zal de lading uit de activiteit halen en de visuele toestand van de tv of ventilator dienovereenkomstig veranderen.

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
1. Microfoon inschakelen selecteren
1. De knop Praten selecteren
1. Zeggen`turn on the tv`
1. De visuele toestand van de tv moet veranderen in "Aan"

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [How to: Validaties toevoegen aan aangepaste opdrachtparameters (voorbeeld)](./how-to-custom-speech-commands-validations.md)
