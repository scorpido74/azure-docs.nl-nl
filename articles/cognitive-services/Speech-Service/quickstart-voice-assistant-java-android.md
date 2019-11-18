---
title: 'Snelstartgids: Custom Voice Assistant, java (Android)-Speech Service'
titleSuffix: Azure Cognitive Services
description: Meer informatie over het maken van een toepassing voor spraak assistenten in Java op Android met behulp van de Speech SDK
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: travisw
ms.openlocfilehash: 58bc33c54a7bd878d717b2c6e44d9ac8657c245f
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74111787"
---
# <a name="quickstart-create-a-voice-assistant-in-java-on-android-by-using-the-speech-sdk"></a>Snelstartgids: een spraak assistent maken in Java op Android met behulp van de Speech SDK

Er is ook een Snelstartgids beschikbaar voor [spraak naar tekst](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=android) en [tekst naar spraak](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-java&tabs=android).

In dit artikel maakt u een spraak assistent met Java voor Android met behulp van de [spraak-SDK](speech-sdk.md). Met deze toepassing wordt verbinding gemaakt met een bot die u al hebt gemaakt en geconfigureerd met het [directe lijn spraak kanaal](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech). Er wordt dan een spraak aanvraag verzonden naar de bot en er wordt een reactie activiteit met spraak ingeschakeld.

Deze toepassing is gebouwd met het Speech SDK maven-pakket en Android Studio 3,3. De Speech SDK is op dit moment compatibel met Android-apparaten met 32/64-bits ARM- en Intel x86/x64-compatibele processors.

> [!NOTE]
> Zie [Speech Devices SDK](speech-devices-sdk.md) voor de Speech Devices SDK en het Roobo-apparaat.

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnements sleutel voor spraak Services. [Ontvang een gratis versie](get-started.md) of maak deze op de [Azure Portal](https://portal.azure.com).
- Een eerder gemaakte bot die is geconfigureerd met het [directe lijn spraak kanaal](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
- [Android Studio](https://developer.android.com/studio/) v 3.3 of hoger

    > [!NOTE]
    > Raadpleeg [de lijst met ondersteunde regio's voor spraak assistenten](regions.md#voice-assistants) om ervoor te zorgen dat uw resources in een van deze regio's worden geïmplementeerd.

## <a name="create-and-configure-a-project"></a>Een project maken en configureren

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-android-create-proj.md)]

## <a name="create-user-interface"></a>Gebruikersinterface maken

In deze sectie maken we een Basic-gebruikers interface (UI) voor de toepassing. Laten we beginnen met het openen van de hoofd activiteit: `activity_main.xml`. De basis sjabloon bevat een titel balk met de naam van de toepassing en een `TextView` met het bericht Hello World!.

Vervang vervolgens de inhoud van de `activity_main.xml` door de volgende code:

   ```xml
   <?xml version="1.0" encoding="utf-8"?>
   <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:onClick="onBotButtonClicked"
        android:text="Talk to your bot" />

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Recognition Data"
        android:textSize="18dp"
        android:textStyle="bold" />

    <TextView
        android:id="@+id/recoText"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="  \n(Recognition goes here)\n" />

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Activity Data"
        android:textSize="18dp"
        android:textStyle="bold" />

    <TextView
        android:id="@+id/activityText"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:scrollbars="vertical"
        android:text="  \n(Activities go here)\n" />

   </LinearLayout>
   ```

Deze XML definieert een eenvoudige gebruikers interface voor interactie met uw bot.

- Het `button`-element initieert een interactie en roept de methode `onBotButtonClicked` aan wanneer erop wordt geklikt.
- Met het element `recoText` worden de resultaten van de spraak naar tekst weer gegeven wanneer u met uw bot praat.
- In het element `activityText` wordt de JSON-nettolading weer gegeven voor de nieuwste bot-Framework activiteit vanuit uw bot.

De tekst en de grafische weergave van uw gebruikersinterface moeten er nu ongeveer als volgt uitzien:

![](media/sdk/qs-java-android-assistant-designer-ui.png)

## <a name="add-sample-code"></a>Voorbeeldcode toevoegen

1. Open `MainActivity.java`en vervang de inhoud door de volgende code:

   ```java
    package samples.speech.cognitiveservices.microsoft.com;

    import android.media.AudioFormat;
    import android.media.AudioManager;
    import android.media.AudioTrack;
    import android.support.v4.app.ActivityCompat;
    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    import android.text.method.ScrollingMovementMethod;
    import android.view.View;
    import android.widget.TextView;

    import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
    import com.microsoft.cognitiveservices.speech.audio.PullAudioOutputStream;
    import com.microsoft.cognitiveservices.speech.dialog.DialogServiceConfig;
    import com.microsoft.cognitiveservices.speech.dialog.DialogServiceConnector;

    import org.json.JSONException;
    import org.json.JSONObject;

    import static android.Manifest.permission.*;

    public class MainActivity extends AppCompatActivity {
        // Replace below with your own speech subscription key
        private static String speechSubscriptionKey = "YourSpeechSubscriptionKey";
        // Replace below with your own speech service region
        private static String serviceRegion = "YourSpeechServiceRegion";

        private DialogServiceConnector connector;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            TextView recoText = (TextView) this.findViewById(R.id.recoText);
            TextView activityText = (TextView) this.findViewById(R.id.activityText);
            recoText.setMovementMethod(new ScrollingMovementMethod());
            activityText.setMovementMethod(new ScrollingMovementMethod());

            // Note: we need to request permissions for audio input and network access
            int requestCode = 5; // unique code for the permission request
            ActivityCompat.requestPermissions(MainActivity.this, new String[]{RECORD_AUDIO, INTERNET}, requestCode);
        }

        public void onBotButtonClicked(View v) {
            // Recreate the DialogServiceConnector on each button press, ensuring that the existing one is closed
            if (connector != null) {
                connector.close();
                connector = null;
            }

            // Create the DialogServiceConnector from speech subscription information
            BotFrameworkConfig config = BotFrameworkConfig.fromSubscription(speechSubscriptionKey, serviceRegion);
            connector = new DialogServiceConnector(config, AudioConfig.fromDefaultMicrophoneInput());

            // Optional step: preemptively connect to reduce first interaction latency
            connector.connectAsync();

            // Register the DialogServiceConnector's event listeners
            registerEventListeners();

            // Begin sending audio to your bot
            connector.listenOnceAsync();
        }

        private void registerEventListeners() {
            TextView recoText = (TextView) this.findViewById(R.id.recoText); // 'recoText' is the ID of your text view
            TextView activityText = (TextView) this.findViewById(R.id.activityText); // 'activityText' is the ID of your text view

            // Recognizing will provide the intermediate recognized text while an audio stream is being processed
            connector.recognizing.addEventListener((o, recoArgs) -> {
                recoText.setText("  Recognizing: " + recoArgs.getResult().getText());
            });

            // Recognized will provide the final recognized text once audio capture is completed
            connector.recognized.addEventListener((o, recoArgs) -> {
                recoText.setText("  Recognized: " + recoArgs.getResult().getText());
            });

            // SessionStarted will notify when audio begins flowing to the service for a turn
            connector.sessionStarted.addEventListener((o, sessionArgs) -> {
                recoText.setText("Listening...");
            });

            // SessionStopped will notify when a turn is complete and it's safe to begin listening again
            connector.sessionStopped.addEventListener((o, sessionArgs) -> {
            });

            // Canceled will be signaled when a turn is aborted or experiences an error condition
            connector.canceled.addEventListener((o, canceledArgs) -> {
                recoText.setText("Canceled (" + canceledArgs.getReason().toString() + ") error details: {}" + canceledArgs.getErrorDetails());
                connector.disconnectAsync();
            });

            // ActivityReceived is the main way your bot will communicate with the client and uses bot framework activities.
            connector.activityReceived.addEventListener((o, activityArgs) -> {
                try {
                    // Here we use JSONObject only to "pretty print" the condensed Activity JSON
                    String rawActivity = activityArgs.getActivity().serialize();
                    String formattedActivity = new JSONObject(rawActivity).toString(2);
                    activityText.setText(formattedActivity);
                } catch (JSONException e) {
                    activityText.setText("Couldn't format activity text: " + e.getMessage());
                }

                if (activityArgs.hasAudio()) {
                    // Text-to-speech audio associated with the activity is 16 kHz 16-bit mono PCM data
                    final int sampleRate = 16000;
                    int bufferSize = AudioTrack.getMinBufferSize(sampleRate, AudioFormat.CHANNEL_OUT_MONO, AudioFormat.ENCODING_PCM_16BIT);

                    AudioTrack track = new AudioTrack(
                            AudioManager.STREAM_MUSIC,
                            sampleRate,
                            AudioFormat.CHANNEL_OUT_MONO,
                            AudioFormat.ENCODING_PCM_16BIT,
                            bufferSize,
                            AudioTrack.MODE_STREAM);

                    track.play();

                    PullAudioOutputStream stream = activityArgs.getAudio();

                    // Audio is streamed as it becomes available. Play it as it arrives.
                    byte[] buffer = new byte[bufferSize];
                    long bytesRead = 0;

                    do {
                        bytesRead = stream.read(buffer);
                        track.write(buffer, 0, (int) bytesRead);
                    } while (bytesRead == bufferSize);

                    track.release();
                }
            });
        }
    }
   ```

   * De methode `onCreate` bevat code die microfoon-en Internet machtigingen aanvraagt.

   * De methode `onBotButtonClicked` is, zoals eerder gezegd, de methode voor het afhandelen van het klikken op de knop. Een knop indrukt een enkele interactie (' turn ') wordt geactiveerd met uw bot.

   * Met de methode `registerEventListeners` worden de gebeurtenissen gedemonstreerd die worden gebruikt door de `DialogServiceConnector` en basis verwerking van binnenkomende activiteiten.

1. Vervang in hetzelfde bestand de configuratie teken reeksen zodat deze overeenkomen met uw resources:

    * Vervang `YourChannelSecret` door het geheim van het directe lijn spraak kanaal voor uw bot.

    * Vervang `YourSpeechSubscriptionKey` door uw abonnementssleutel.

    * `YourServiceRegion` vervangen door de [regio](regions.md) die aan uw abonnement is gekoppeld alleen een subset van spraak Services regio's wordt momenteel ondersteund met direct line speech. Zie [regio's](regions.md#voice-assistants)voor meer informatie.

## <a name="build-and-run-the-app"></a>De app bouwen en uitvoeren

1. Sluit uw Android-apparaat aan op de ontwikkelmachine. Zorg ervoor dat u de [ontwikkelmodus en USB-foutopsporing](https://developer.android.com/studio/debug/dev-options) hebt ingeschakeld op het apparaat.

1. Druk op Ctrl + F9 om de toepassing te compileren, of kies **Build** > **Make Project** in de menubalk.

1. Als u de toepassing wilt starten, drukt u op Shift + F10 of kiest u **Run** > **Run 'app'** .

1. Kies uw Android-apparaat in het venster Select Deployment Target dat wordt weergegeven.

   ![Schermafbeelding van het venster Select Deployment Target](media/sdk/qs-java-android-12-deploy.png)

Zodra de toepassing en de bijbehorende activiteiten zijn gestart, klikt u op de knop om te beginnen met het praten met uw bot. Getranscribeerde tekst wordt weer gegeven terwijl u praat en de laatste activiteit die u hebt ontvangen van uw bot wordt weer gegeven wanneer deze wordt ontvangen. Als uw bot is geconfigureerd om gesp roken reacties te geven, wordt de spraak-naar-tekst automatisch afgespeeld.

![Schermafbeelding van de Android-toepassing](media/sdk/qs-java-android-assistant-completed-turn.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een basisbot maken en implementeren](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)

## <a name="see-also"></a>Zie ook
- [Over spraak assistenten](voice-assistants.md)
- [Gratis een abonnements sleutel voor spraak Services aanschaffen](get-started.md)
- [Aangepaste tref woorden](speech-devices-sdk-create-kws.md)
- [Directe lijn spraak op uw bot aansluiten](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
- [Bekijk Java-voorbeelden op GitHub](https://aka.ms/csspeech/samples)
