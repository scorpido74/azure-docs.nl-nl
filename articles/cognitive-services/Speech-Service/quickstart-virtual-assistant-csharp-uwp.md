---
title: 'Quickstart: Aangepaste spraak-eerste virtuele assistent (preview), C# (UWP)-spraak service'
titleSuffix: Azure Cognitive Services
description: In dit artikel maakt u een C# universeel Windows-platform-toepassing (UWP) met behulp van de Cognitive Services speech Software Development Kit (SDK). U verbindt uw client toepassing met een eerder gemaakte bot-Framework-bot die is geconfigureerd voor gebruik van het directe lijn spraak kanaal. De toepassing is gebouwd met het Speech SDK NuGet-pakket en micro soft Visual Studio 2019.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/19/2019
ms.author: travisw
ms.openlocfilehash: c676e98eb812a31d6fb8d7cc0f58929f803c868e
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/07/2019
ms.locfileid: "70382038"
---
# <a name="quickstart-create-a-voice-first-virtual-assistant-with-the-speech-sdk-uwp"></a>Quickstart: Maak een virtuele assistent met spraak-eerste met de spraak-SDK, UWP

Quick starts zijn ook beschikbaar voor [spraak herkenning](quickstart-csharp-uwp.md), [spraak synthese](quickstart-text-to-speech-csharp-uwp.md)en [spraak omzetting](quickstart-translate-speech-uwp.md).

In dit artikel ontwikkelt u een C# universeel Windows-platform-toepassing (UWP) met behulp van de spraak- [SDK](speech-sdk.md). Het programma maakt verbinding met een eerder bestemde en geconfigureerde bot om in te scha kelen op het maken van een virtuele assistent van de eerste plaats van de client toepassing. De toepassing is gebouwd met het [Speech SDK NuGet-pakket](https://aka.ms/csspeech/nuget) en micro soft Visual Studio 2019 (alle edities).

> [!NOTE]
> Het Universal Windows Platform stelt u in staat om apps te ontwikkelen die kunnen worden uitgevoerd op elk apparaat dat ondersteuning biedt voor Windows 10, met inbegrip van pc's, Xbox, Surface Hub en andere apparaten.

## <a name="prerequisites"></a>Vereisten

Voor deze snelstart zijn de volgende zaken vereist:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Een Azure-abonnements sleutel voor spraak Services. [Ontvang een gratis versie](get-started.md) of maak deze op de [Azure Portal](https://portal.azure.com).
* Een eerder gemaakte bot die is geconfigureerd met het [directe lijn spraak kanaal](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech).

  > [!NOTE]
  > Direct line Speech (preview) is momenteel beschikbaar in een subset van de regio's met spraak Services. Raadpleeg [de lijst met ondersteunde regio's voor de eerste virtuele assistenten voor spraak](regions.md#voice-first-virtual-assistants) en zorg ervoor dat uw resources in een van deze regio's worden geïmplementeerd.

## <a name="optional-get-started-fast"></a>Optioneel: Snel aan de slag

In deze Snelstartgids wordt stapsgewijs beschreven hoe u een client toepassing kunt maken om verbinding te maken met uw op spraak ingeschakelde bot. Als u de voor keur geeft aan de volledige, kant-en-klare bron code die in deze Quick Start wordt gebruikt, is beschikbaar in de [Speech SDK](https://aka.ms/csspeech/samples) -voor beelden in de map `quickstart`.

## <a name="create-a-visual-studio-project"></a>Een Visual Studio-project maken

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>Voorbeeldcode toevoegen

Voeg nu de XAML-code toe die de gebruikers interface van de toepassing definieert en voeg C# de code-behind-implementatie toe.

### <a name="xaml-code"></a>XAML-code

Eerst maakt u de gebruikers interface van de toepassing door de XAML-code toe te voegen:

1. Open `MainPage.xaml` in **Solution Explorer**.

1. Vervang in de XAML-weer gave van de ontwerp functie de volledige inhoud door het volgende code fragment:

    ```xml
    <Page
        x:Class="helloworld.MainPage"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:local="using:helloworld"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        mc:Ignorable="d"
        Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">

        <Grid>
            <StackPanel Orientation="Vertical" HorizontalAlignment="Center"  
                        Margin="20,50,0,0" VerticalAlignment="Center" Width="800">
                <Button x:Name="EnableMicrophoneButton" Content="Enable Microphone"  
                        Margin="0,0,10,0" Click="EnableMicrophone_ButtonClicked" 
                        Height="35"/>
                <Button x:Name="ListenButton" Content="Talk to your bot" 
                        Margin="0,10,10,0" Click="ListenButton_ButtonClicked" 
                        Height="35"/>
                <StackPanel x:Name="StatusPanel" Orientation="Vertical" 
                            RelativePanel.AlignBottomWithPanel="True" 
                            RelativePanel.AlignRightWithPanel="True" 
                            RelativePanel.AlignLeftWithPanel="True">
                    <TextBlock x:Name="StatusLabel" Margin="0,10,10,0" 
                               TextWrapping="Wrap" Text="Status:" FontSize="20"/>
                    <Border x:Name="StatusBorder" Margin="0,0,0,0">
                        <ScrollViewer VerticalScrollMode="Auto"  
                                      VerticalScrollBarVisibility="Auto" MaxHeight="200">
                            <!-- Use LiveSetting to enable screen readers to announce 
                                 the status update. -->
                            <TextBlock 
                                x:Name="StatusBlock" FontWeight="Bold" 
                                AutomationProperties.LiveSetting="Assertive"
                                MaxWidth="{Binding ElementName=Splitter, Path=ActualWidth}" 
                                Margin="10,10,10,20" TextWrapping="Wrap"  />
                        </ScrollViewer>
                    </Border>
                </StackPanel>
            </StackPanel>
            <MediaElement x:Name="mediaElement"/>
        </Grid>
    </Page>
    ```

De Ontwerpweergave is bijgewerkt om de gebruikers interface van de toepassing weer te geven.

### <a name="c-code-behind-source"></a>C#code-behind bron

Vervolgens voegt u de bron code achter toe, zodat de toepassing werkt zoals verwacht. De bron code achter bevat:

- `using`-instructies voor de `Speech`-en `Speech.Dialog`-naam ruimten
- Een eenvoudige implementatie om toegang tot de microfoon te garanderen, bekabeld tot een knop-handler
- Helpers van de Basic-gebruikers interface om berichten en fouten in de toepassing weer te geven
- Een overloop punt voor het pad van de initialisatie code dat later wordt ingevuld
- Een helper voor het afspelen van tekst naar spraak (zonder streaming-ondersteuning)
- Een lege knop-handler om te Luis teren die later wordt ingevuld

Voer de volgende stappen uit om de code-behind-bron toe te voegen:

1. Open in **Solution Explorer**het bron bestand code-behind `MainPage.xaml.cs`. (Deze wordt gegroepeerd onder `MainPage.xaml`.)

1. Vervang de inhoud van het bestand door het volgende code fragment:

    ```csharp
    using Microsoft.CognitiveServices.Speech;
    using Microsoft.CognitiveServices.Speech.Audio;
    using Microsoft.CognitiveServices.Speech.Dialog;
    using System;
    using System.Diagnostics;
    using System.IO;
    using System.Text;
    using Windows.Foundation;
    using Windows.Storage.Streams;
    using Windows.UI.Xaml;
    using Windows.UI.Xaml.Controls;
    using Windows.UI.Xaml.Media;

    namespace helloworld
    {
        public sealed partial class MainPage : Page
        {
            private DialogServiceConnector connector;

            private enum NotifyType
            {
                StatusMessage,
                ErrorMessage
            };

            public MainPage()
            {
                this.InitializeComponent();
            }

            private async void EnableMicrophone_ButtonClicked(
                object sender, RoutedEventArgs e)
            {
                bool isMicAvailable = true;
                try
                {
                    var mediaCapture = new Windows.Media.Capture.MediaCapture();
                    var settings = 
                        new Windows.Media.Capture.MediaCaptureInitializationSettings();
                    settings.StreamingCaptureMode = 
                        Windows.Media.Capture.StreamingCaptureMode.Audio;
                    await mediaCapture.InitializeAsync(settings);
                }
                catch (Exception)
                {
                    isMicAvailable = false;
                }
                if (!isMicAvailable)
                {
                    await Windows.System.Launcher.LaunchUriAsync(
                        new Uri("ms-settings:privacy-microphone"));
                }
                else
                {
                    NotifyUser("Microphone was enabled", NotifyType.StatusMessage);
                }
            }

            private void NotifyUser(
                string strMessage, NotifyType type = NotifyType.StatusMessage)
            {
                // If called from the UI thread, then update immediately.
                // Otherwise, schedule a task on the UI thread to perform the update.
                if (Dispatcher.HasThreadAccess)
                {
                    UpdateStatus(strMessage, type);
                }
                else
                {
                    var task = Dispatcher.RunAsync(
                        Windows.UI.Core.CoreDispatcherPriority.Normal, 
                        () => UpdateStatus(strMessage, type));
                }
            }

            private void UpdateStatus(string strMessage, NotifyType type)
            {
                switch (type)
                {
                    case NotifyType.StatusMessage:
                        StatusBorder.Background = new SolidColorBrush(
                            Windows.UI.Colors.Green);
                        break;
                    case NotifyType.ErrorMessage:
                        StatusBorder.Background = new SolidColorBrush(
                            Windows.UI.Colors.Red);
                        break;
                }
                StatusBlock.Text += string.IsNullOrEmpty(StatusBlock.Text) 
                    ? strMessage : "\n" + strMessage;

                if (!string.IsNullOrEmpty(StatusBlock.Text))
                {
                    StatusBorder.Visibility = Visibility.Visible;
                    StatusPanel.Visibility = Visibility.Visible;
                }
                else
                {
                    StatusBorder.Visibility = Visibility.Collapsed;
                    StatusPanel.Visibility = Visibility.Collapsed;
                }
                // Raise an event if necessary to enable a screen reader 
                // to announce the status update.
                var peer = Windows.UI.Xaml.Automation.Peers.FrameworkElementAutomationPeer.FromElement(StatusBlock);
                if (peer != null)
                {
                    peer.RaiseAutomationEvent(
                        Windows.UI.Xaml.Automation.Peers.AutomationEvents.LiveRegionChanged);
                }
            }

            // Waits for and accumulates all audio associated with a given 
            // PullAudioOutputStream and then plays it to the MediaElement. Long spoken 
            // audio will create extra latency and a streaming playback solution 
            // (that plays audio while it continues to be received) should be used -- 
            // see the samples for examples of this.
            private void SynchronouslyPlayActivityAudio(
                PullAudioOutputStream activityAudio)
            {
                var playbackStreamWithHeader = new MemoryStream();
                playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("RIFF"), 0, 4); // ChunkID
                playbackStreamWithHeader.Write(BitConverter.GetBytes(UInt32.MaxValue), 0, 4); // ChunkSize: max
                playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("WAVE"), 0, 4); // Format
                playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("fmt "), 0, 4); // Subchunk1ID
                playbackStreamWithHeader.Write(BitConverter.GetBytes(16), 0, 4); // Subchunk1Size: PCM
                playbackStreamWithHeader.Write(BitConverter.GetBytes(1), 0, 2); // AudioFormat: PCM
                playbackStreamWithHeader.Write(BitConverter.GetBytes(1), 0, 2); // NumChannels: mono
                playbackStreamWithHeader.Write(BitConverter.GetBytes(16000), 0, 4); // SampleRate: 16kHz
                playbackStreamWithHeader.Write(BitConverter.GetBytes(32000), 0, 4); // ByteRate
                playbackStreamWithHeader.Write(BitConverter.GetBytes(2), 0, 2); // BlockAlign
                playbackStreamWithHeader.Write(BitConverter.GetBytes(16), 0, 2); // BitsPerSample: 16-bit
                playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("data"), 0, 4); // Subchunk2ID
                playbackStreamWithHeader.Write(BitConverter.GetBytes(UInt32.MaxValue), 0, 4); // Subchunk2Size

                byte[] pullBuffer = new byte[2056];

                uint lastRead = 0;
                do
                {
                    lastRead = activityAudio.Read(pullBuffer);
                    playbackStreamWithHeader.Write(pullBuffer, 0, (int)lastRead);
                }
                while (lastRead == pullBuffer.Length);

                var task = Dispatcher.RunAsync(
                    Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
                {
                    mediaElement.SetSource(
                        playbackStreamWithHeader.AsRandomAccessStream(), "audio/wav");
                    mediaElement.Play();
                });
            }

            private void InitializeDialogServiceConnector()
            {
                // New code will go here
            }

            private async void ListenButton_ButtonClicked(
                object sender, RoutedEventArgs e)
            {
                // New code will go here
            }
        }
    }
    ```

1. Voeg het volgende code fragment toe aan de methode hoofdtekst van `InitializeDialogServiceConnector`. Met deze code wordt de `DialogServiceConnector` gemaakt met uw abonnements gegevens.

    ```csharp
    // create a DialogServiceConfig by providing a bot secret key 
    // and Cognitive Services subscription key
    // the RecoLanguage property is optional (default en-US); 
    // note that only en-US is supported in Preview
    const string channelSecret = "YourChannelSecret"; // Your channel secret
    const string speechSubscriptionKey = "YourSpeechSubscriptionKey"; // Your subscription key

    // Your subscription service region. 
    // Note: only a subset of regions are currently supported
    const string region = "YourServiceRegion"; 

    var botConfig = DialogServiceConfig.FromBotSecret(
        channelSecret, speechSubscriptionKey, region);
    botConfig.SetProperty(PropertyId.SpeechServiceConnection_RecoLanguage, "en-US");
    connector = new DialogServiceConnector(botConfig);
    ```

   > [!NOTE]
   > Direct line Speech (preview) is momenteel beschikbaar in een subset van de regio's met spraak Services. Raadpleeg [de lijst met ondersteunde regio's voor de eerste virtuele assistenten voor spraak](regions.md#voice-first-virtual-assistants) en zorg ervoor dat uw resources in een van deze regio's worden geïmplementeerd.

   > [!NOTE]
   > Voor informatie over het configureren van uw bot en het ophalen van een kanaal geheim raadpleegt u de bot Framework-documentatie voor [het directe lijn spraak kanaal](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech).

1. Vervang de teken reeksen `YourChannelSecret`, `YourSpeechSubscriptionKey` en `YourServiceRegion` door uw eigen waarden voor uw bot, spraak abonnement en [regio](regions.md).

1. Voeg het volgende code fragment toe aan het einde van de methode hoofdtekst van `InitializeDialogServiceConnector`. Met deze code worden handlers ingesteld voor gebeurtenissen die zijn vertrouwd door `DialogServiceConnector` om de bot-activiteiten, de resultaten van spraak herkenning en andere informatie te communiceren.

    ```csharp
    // ActivityReceived is the main way your bot will communicate with the client 
    // and uses bot framework activities
    connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
    {
        NotifyUser(
            $"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

        if (activityReceivedEventArgs.HasAudio)
        {
            SynchronouslyPlayActivityAudio(activityReceivedEventArgs.Audio);
        }
    };

    // Canceled will be signaled when a turn is aborted or experiences an error condition
    connector.Canceled += (sender, canceledEventArgs) =>
    {
        NotifyUser($"Canceled, reason={canceledEventArgs.Reason}");
        if (canceledEventArgs.Reason == CancellationReason.Error)
        {
            NotifyUser(
                $"Error: code={canceledEventArgs.ErrorCode}, details={canceledEventArgs.ErrorDetails}");
        }
    };

    // Recognizing (not 'Recognized') will provide the intermediate recognized text 
    // while an audio stream is being processed
    connector.Recognizing += (sender, recognitionEventArgs) =>
    {
        NotifyUser($"Recognizing! in-progress text={recognitionEventArgs.Result.Text}");
    };

    // Recognized (not 'Recognizing') will provide the final recognized text 
    // once audio capture is completed
    connector.Recognized += (sender, recognitionEventArgs) =>
    {
        NotifyUser($"Final speech-to-text result: '{recognitionEventArgs.Result.Text}'");
    };

    // SessionStarted will notify when audio begins flowing to the service for a turn
    connector.SessionStarted += (sender, sessionEventArgs) =>
    {
        NotifyUser($"Now Listening! Session started, id={sessionEventArgs.SessionId}");
    };

    // SessionStopped will notify when a turn is complete and 
    // it's safe to begin listening again
    connector.SessionStopped += (sender, sessionEventArgs) =>
    {
        NotifyUser($"Listening complete. Session ended, id={sessionEventArgs.SessionId}");
    };
    ```

1. Voeg het volgende code fragment toe aan de hoofd tekst van de methode `ListenButton_ButtonClicked` in de klasse `MainPage`. Met deze code wordt `DialogServiceConnector` ingesteld om te Luis teren, omdat u de configuratie al hebt ingesteld en de gebeurtenis-handlers hebt geregistreerd.

    ```csharp
    if (connector == null)
    {
        InitializeDialogServiceConnector();
        // Optional step to speed up first interaction: if not called, 
        // connection happens automatically on first use
        var connectTask = connector.ConnectAsync();
    }

    try
    {
        // Start sending audio to your speech-enabled bot
        var listenTask = connector.ListenOnceAsync();

        // You can also send activities to your bot as JSON strings -- 
        // Microsoft.Bot.Schema can simplify this
        string speakActivity = 
            @"{""type"":""message"",""text"":""Greeting Message"", ""speak"":""Hello there!""}";
        await connector.SendActivityAsync(speakActivity);

    }
    catch (Exception ex)
    {
        NotifyUser($"Exception: {ex.ToString()}", NotifyType.ErrorMessage);
    }
    ```

1. Kies in de menu balk de optie **bestand** > **Alles opslaan** om uw wijzigingen op te slaan.

## <a name="build-and-run-the-application"></a>De toepassing bouwen en uitvoeren.

U bent nu klaar om uw toepassing te bouwen en te testen.

1. Kies in de menu balk **build** > **Build-oplossing** om de toepassing te bouwen. De code moet nu zonder fouten worden gecompileerd.

1. Kies **fouten opsporen** > **fout opsporing starten** (of druk op **F5**) om de toepassing te starten. Het venster **HelloWorld** wordt weer gegeven.

   ![Voor beeld van UWP Virtual assistent C# -toepassing in-Quick Start](media/sdk/qs-virtual-assistant-uwp-helloworld-window.png)

1. Selecteer **microfoon inschakelen**en selecteer wanneer de toegangs machtigings aanvraag wordt weer gegeven, **Ja**.

   ![Verzoek om toegang tot de microfoon](media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Selecteer **praten met uw bot**en spreek een Engelse zin of zin in op de microfoon van uw apparaat. Uw spraak wordt verzonden naar het directe lijn spraak kanaal en naar tekst getranscribeerd, die wordt weer gegeven in het venster.
<!--
    ![Successful bot response](media/voice-first-virtual-assistants/quickstart-cs-uwp-bot-successful-turn.png)
-->
## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een basisbot maken en implementeren](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)

## <a name="see-also"></a>Zie ook

- [Over de spraak-eerste virtuele assistenten](voice-first-virtual-assistants.md)
- [Gratis een abonnements sleutel voor spraak Services aanschaffen](get-started.md)
- [Aangepaste Ontwaak woorden](speech-devices-sdk-create-kws.md)
- [Directe lijn spraak op uw bot aansluiten](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
- [C#-voorbeelden op GitHub bekijken](https://aka.ms/csspeech/samples)
