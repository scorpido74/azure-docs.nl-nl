---
title: 'Snelstart: Maak een aangepaste spraakassistent, Java (Android) - Spraakservice'
titleSuffix: Azure Cognitive Services
description: Meer informatie over het maken van een aangepaste spraakassistent in Java op Android met behulp van de Speech SDK.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 03/20/2020
ms.author: travisw
ms.openlocfilehash: 24030a568dd4df7f3e0888e2bda8f1335e52ba38
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80241169"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat, moet u:

> [!div class="checklist"]
> * [Een Azure-spraakbron maken](~/articles/cognitive-services/speech-service/get-started.md)
> * [Stel uw ontwikkelomgeving in en maak een leeg project](~/articles/cognitive-services/speech-service/quickstarts/setup-platform.md?tabs=android)
> * Een bot maken die is verbonden met het [kanaal Direct Line Spraak](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
> * Zorg ervoor dat u toegang hebt tot een microfoon voor audio-opname

  > [!NOTE]
  > Raadpleeg [de lijst met ondersteunde regio's voor spraakassistenten](~/articles/cognitive-services/speech-service/regions.md#voice-assistants) en zorg ervoor dat uw resources in een van deze regio's worden geïmplementeerd.

## <a name="create-and-configure-a-project"></a>Een project maken en configureren

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-java-android-create-proj.md)]

## <a name="create-user-interface"></a>Gebruikersinterface maken

In deze sectie maken we een basisgebruikersinterface (UI) voor de toepassing. Laten we beginnen met het `activity_main.xml`openen van de hoofdactiviteit: . De basissjabloon bevat een titelbalk met de `TextView` naam van de toepassing en een met het bericht 'Hallo wereld!'.

Vervang vervolgens de inhoud `activity_main.xml` van de inhoud door de volgende code:

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

Deze XML definieert een eenvoudige gebruikersinterface om met uw bot te communiceren.

- Het `button` element initieert een `onBotButtonClicked` interactie en roept de methode aan wanneer erop wordt geklikt.
- Het `recoText` element geeft de spraak-naar-tekstresultaten weer terwijl u met uw bot praat.
- Het `activityText` element geeft de JSON-payload weer voor de nieuwste Bot Framework-activiteit van uw bot.

De tekst en de grafische weergave van uw gebruikersinterface moeten er nu ongeveer als volgt uitzien:

![](~/articles/cognitive-services/speech-service/media/sdk/qs-java-android-assistant-designer-ui.png)

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

   * De `onCreate` methode bevat code die microfoon- en internetmachtigingen opvraagt.

   * De methode `onBotButtonClicked` is, zoals eerder gezegd, de methode voor het afhandelen van het klikken op de knop. Een druk op de knop activeert één interactie ("turn") met uw bot.

   * De `registerEventListeners` methode toont de gebeurtenissen `DialogServiceConnector` die worden gebruikt door de en de basisbehandeling van inkomende activiteiten.

1. Vervang in hetzelfde bestand de configuratietekenreeksen die overeenkomen met uw bronnen:

    * Vervang `YourSpeechSubscriptionKey` door uw abonnementssleutel.

    * Vervang `YourServiceRegion` door het [gebied](~/articles/cognitive-services/speech-service/regions.md) dat is gekoppeld aan uw abonnement Alleen een subset van spraakserviceregio's wordt momenteel ondersteund met Direct Line Speech. Zie [regio's](~/articles/cognitive-services/speech-service/regions.md#voice-assistants)voor meer informatie .

## <a name="build-and-run-the-app"></a>De app bouwen en uitvoeren

1. Sluit uw Android-apparaat aan op de ontwikkelmachine. Zorg ervoor dat u de [ontwikkelmodus en USB-foutopsporing](https://developer.android.com/studio/debug/dev-options) hebt ingeschakeld op het apparaat.

1. Als u de toepassing wilt maken, drukt u op Ctrl+F9 of kiest **u** > **Project maken** maken op de menubalk.

1. Als u de toepassing wilt starten, drukt u op Shift+F10 of kiest u **'app'** **uitvoeren.** > 

1. Kies uw Android-apparaat in het venster Select Deployment Target dat wordt weergegeven.

   ![Schermafbeelding van het venster Select Deployment Target](~/articles/cognitive-services/speech-service/media/sdk/qs-java-android-12-deploy.png)

Zodra de toepassing en de activiteit zijn gestart, klikt u op de knop om te beginnen met praten met uw bot. Getranscribeerde tekst wordt op dit moment weergegeven en de laatste activiteit die u van uw bot hebt ontvangen, wordt weergegeven wanneer deze wordt ontvangen. Als uw bot is geconfigureerd om gesproken antwoorden te geven, wordt de spraak-naar-tekst automatisch afgespeeld.

![Schermafbeelding van de Android-toepassing](~/articles/cognitive-services/speech-service/media/sdk/qs-java-android-assistant-completed-turn.png)

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](./footer.md)]

