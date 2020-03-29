---
title: 'Snelstart: verbinding maken met een app Aangepaste opdrachten met Spraak-SDK - Spraakservice'
titleSuffix: Azure Cognitive Services
description: In dit artikel maakt u een Speech SDK-clienttoepassing met aangepaste opdrachten.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: donkim
ms.openlocfilehash: 9e324af0b90f595b5b7af2a417a562efb193d854
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76156774"
---
# <a name="quickstart-connect-to-a-custom-commands-application-with-the-speech-sdk-preview"></a>Snelstart: verbinding maken met een toepassing aangepaste opdrachten met de Spraak-SDK (Voorbeeld)

Nadat u een gehoste toepassing voor aangepaste opdrachten hebt gemaakt, u vanaf een clientapparaat met deze toepassing beginnen te praten.

In dit artikel:

- Een toepassing met aangepaste opdrachten publiceren en een toepassings-id (App-id)
- Een client-app maken met de Spraak-SDK waarmee u met uw toepassing Aangepaste opdrachten praten

## <a name="prerequisites"></a>Vereisten

Een toepassing Met aangepaste opdrachten is vereist om dit artikel te voltooien. Als u nog geen toepassing voor aangepaste opdrachten hebt gemaakt, u dit doen in deze vorige quickstarts:

- [Snelstart: een aangepaste opdracht maken (voorbeeld)](./quickstart-custom-speech-commands-create-new.md)
- [Snelstart: een aangepaste opdracht maken met parameters (voorbeeld)](./quickstart-custom-speech-commands-create-parameters.md)

Je hebt ook nodig:

- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
- Een Azure-abonnementssleutel voor Spraakservices. [Gratis een gratis of](get-started.md) maak het op de [Azure-portal](https://portal.azure.com)

## <a name="optional-get-started-fast"></a>Optioneel: snel aan de slag

In deze snelle start wordt stap voor stap beschreven hoe u een clienttoepassing maken om verbinding te maken met uw app Aangepaste opdrachten. Als u er liever direct in duikt, is de volledige, kant-en-klare broncode die `quickstart` in deze quickstart wordt gebruikt, beschikbaar in de [Spraak-SDK-voorbeelden](https://aka.ms/csspeech/samples) onder de map.

## <a name="step-1-publish-custom-commands-application"></a>Stap 1: Toepassing Aangepaste opdrachten publiceren

1. De [eerder gemaakte toepassing Aangepaste opdrachten openen](./quickstart-custom-speech-commands-create-new.md) en de optie Publiceren **selecteren**

   > [!div class="mx-imgBorder"]
   > ![Uw toepassing publiceren](media/custom-speech-commands/fulfill-sdk-publish-application.png)

1. De app-id kopiëren van de publicatiemelding voor later gebruik

## <a name="step-2-create-a-visual-studio-project"></a>Stap 2: Een Visual Studio-project maken

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="step-3-add-sample-code"></a>Stap 3: Voorbeeldcode toevoegen

In deze stap voegen we de XAML-code toe die de gebruikersinterface van de toepassing definieert en voegen we de C#-code-behind-implementatie toe.

### <a name="xaml-code"></a>XAML-code

Maak de gebruikersinterface van de toepassing door de XAML-code toe te voegen.

1. Open **in Solution Explorer**`MainPage.xaml`

1. Vervang in de XAML-weergave van de ontwerper de volledige inhoud door het volgende codefragment:

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
               <Button x:Name="ListenButton" Content="Talk"
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

De ontwerpweergave wordt bijgewerkt om de gebruikersinterface van de toepassing weer te geven.

### <a name="c-code-behind-source"></a>C# code-behind bron

Voeg de code-behind bron toe, zodat de toepassing werkt zoals verwacht. De code-behind bron omvat:

- Vereiste `using` instructies `Speech` voor `Speech.Dialog` de en naamruimten
- Een eenvoudige implementatie om toegang tot de microfoon te garanderen, verbonden met een knophandler
- Basisgebruikersvan gebruikersinterface om berichten en fouten in de toepassing te presenteren
- Een landingspunt voor het initialisatiecodepad dat later wordt ingevuld
- Een helper om tekst-naar-spraak af te spelen (zonder streamingondersteuning)
- Een lege knophandler om te beginnen met luisteren die later wordt ingevuld

Voeg de code-behind bron als volgt toe:

1. Open in **Solution Explorer**het broncodebestand `MainPage.xaml.cs` met `MainPage.xaml`code achter (gegroepeerd onder)

1. Vervang de inhoud van het bestand door de volgende code:

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

1. Voeg de volgende code toe aan de methodebody van`InitializeDialogServiceConnector`

   ```csharp
   // This code creates the `DialogServiceConnector` with your subscription information.
   // create a DialogServiceConfig by providing a Custom Commands application id and Cognitive Services subscription key
   // the RecoLanguage property is optional (default en-US); note that only en-US is supported in Preview
   const string speechCommandsApplicationId = "YourApplicationId"; // Your application id
   const string speechSubscriptionKey = "YourSpeechSubscriptionKey"; // Your subscription key
   const string region = "YourServiceRegion"; // The subscription service region. Note: only 'westus2' is currently supported

   var speechCommandsConfig = CustomCommandsConfig.FromSubscription(speechCommandsApplicationId, speechSubscriptionKey, region);
   speechCommandsConfig.SetProperty(PropertyId.SpeechServiceConnection_RecoLanguage, "en-us");
   connector = new DialogServiceConnector(speechCommandsConfig);
   ```

1. De `YourApplicationId`tekenreeksen `YourSpeechSubscriptionKey`en `YourServiceRegion` uw eigen waarden voor uw app, spraakabonnement en [regio vervangen](regions.md)

1. Het volgende codefragment toevoegen aan het einde van de methodebody van`InitializeDialogServiceConnector`

   ```csharp
   //
   // This code sets up handlers for events relied on by `DialogServiceConnector` to communicate its activities,
   // speech recognition results, and other information.
   //
   // ActivityReceived is the main way your client will receive messages, audio, and events
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

1. Het volgende codefragment toevoegen aan `ListenButton_ButtonClicked` de `MainPage` hoofdtekst van de methode in de klasse

   ```csharp
   // This code sets up `DialogServiceConnector` to listen, since you already established the configuration and
   // registered the event handlers.
   if (connector == null)
   {
       InitializeDialogServiceConnector();
       // Optional step to speed up first interaction: if not called,
       // connection happens automatically on first use
       var connectTask = connector.ConnectAsync();
   }

   try
   {
       // Start sending audio
       await connector.ListenOnceAsync();
   }
   catch (Exception ex)
   {
       NotifyUser($"Exception: {ex.ToString()}", NotifyType.ErrorMessage);
   }
   ```

1. Kies Alles**opslaan** **op** > de menubalk om de wijzigingen op te slaan

## <a name="build-and-run-the-application"></a>De toepassing bouwen en uitvoeren.

1. Kies**build-oplossing** **bouwen** > om de toepassing te bouwen op de menubalk. De code moet zonder fouten worden gecompileerd.

1. Kies **Foutopsporing debuggen** > **debuggen** (of druk op **F5)** om de toepassing te starten. Het **helloworld** venster verschijnt.

   ![Voorbeeld van uwp-toepassing voor virtuele assistent in C# - snel starten](media/sdk/qs-voice-assistant-uwp-helloworld-window.png)

1. Selecteer **Microfoon inschakelen**. Als de aanvraag voor toegangsmachtigingen wordt opduikt, selecteert u **Ja**.

   ![Machtigingsaanvraag voor toegang voor microfoon](media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Selecteer **Praten**en spreek een Engelse zin of zin in de microfoon van uw apparaat. Uw toespraak wordt verzonden naar het kanaal Direct Line Speech en getranscribeerd naar tekst, die in het venster wordt weergegeven.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [How to: Opdrachten uitvoeren op de client met de Speech SDK (preview)](./how-to-custom-speech-commands-fulfill-sdk.md)
> [How To: Validaties toevoegen aan aangepaste opdrachtparameters (Preview)](./how-to-custom-speech-commands-validations.md)
