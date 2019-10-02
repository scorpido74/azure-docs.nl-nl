---
title: 'Quickstart: Aangepaste spraak-eerste virtuele assistent (preview), java (Windows, Linux)-spraak service'
titleSuffix: Azure Cognitive Services
description: In deze Quick Start leert u hoe u de Cognitive Services speech Software Development Kit (SDK) gebruikt in een Java-Console toepassing. U leert hoe u uw client toepassing kunt verbinden met een eerder gemaakte bot-Framework-bot die is geconfigureerd om het directe lijn spraak kanaal te gebruiken en de ervaring van de virtuele assistent voor het eerst te activeren.
services: cognitive-services
author: bidishac
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: bidishac
ms.openlocfilehash: c5a6042e4b181190849b3759325e4aab0c22413b
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71800042"
---
# <a name="quickstart-create-a-voice-first-virtual-assistant-with-the-speech-sdk-java"></a>Quickstart: Maak een virtuele assistent met spraak-eerste met de spraak-SDK, java

Quick starts zijn ook beschikbaar voor [spraak naar tekst](quickstart-java-jre.md), [tekst naar spraak](quickstart-text-to-speech-java-jre.md)en [spraak omzetting](quickstart-translate-speech-java-jre.md).

In dit artikel maakt u een Java-Console toepassing met behulp van de [Cognitive Services Speech SDK](speech-sdk.md). De toepassing maakt verbinding met een eerder geontwerpde bot die is geconfigureerd voor gebruik van het directe lijn spraak kanaal, verzendt een spraak aanvraag en retourneert een activiteit van een spraak reactie (indien geconfigureerd). De toepassing is gebouwd met het Speech SDK maven-pakket en de eclips Java IDE in Windows, Ubuntu Linux of op macOS. De toepassing wordt uitgevoerd op een 64-bit Java 8 runtime-omgeving (JRE).

## <a name="prerequisites"></a>Vereisten

Voor deze snelstart zijn de volgende zaken vereist:

* Besturingssysteem: Windows (64-bits), Ubuntu Linux 16.04/18.04 (64-bits) of macOS 10,13 of hoger
* [Eclipse Java IDE](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) of [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Een Azure-abonnements sleutel voor spraak Services. [Ontvang een gratis versie](get-started.md) of maak deze op de [Azure Portal](https://portal.azure.com).
* Een vooraf geconfigureerde bot die is gemaakt met behulp van bot Framework versie 4,2 of hoger. De bot moet worden geabonneerd op het nieuwe kanaal ' directe lijn spraak ' om gesp roken invoer te ontvangen.

    > [!NOTE]
    > Direct line Speech (preview) is momenteel beschikbaar in een subset van de regio's met spraak Services. Raadpleeg [de lijst met ondersteunde regio's voor de eerste virtuele assistenten voor spraak](regions.md#Voice-first virtual assistants) en zorg ervoor dat uw resources in een van deze regio's worden geïmplementeerd.

Als u Ubuntu 16.04/18.04 uitvoert, moet u ervoor zorgen dat deze afhankelijkheden zijn geïnstalleerd voordat u de eclips start:

```console
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2 wget
```

Als u Windows (64-bits) gebruikt, moet u ervoor zorgen dat u de micro C++ Soft Visual Redistributable voor uw platform hebt geïnstalleerd:
* [Download Microsoft Visual C++ Redistributable voor Visual Studio 2017](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

## <a name="optional-get-started-fast"></a>Optioneel: Snel aan de slag

In deze Snelstartgids wordt stapsgewijs beschreven hoe u een eenvoudige client toepassing kunt maken om verbinding te maken met uw op spraak ingeschakelde bot. Als u de voor keur geeft aan de volledige, kant-en-klare bron code die in deze Quick Start wordt gebruikt, is beschikbaar in de [Speech SDK](https://aka.ms/csspeech/samples) -voor beelden in de map `quickstart`.

## <a name="create-and-configure-project"></a>Project maken en configureren

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

Als u logboek registratie wilt inschakelen, moet u ook het bestand **pom. XML** bijwerken met de volgende afhankelijkheden.

   ```xml
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-simple</artifactId>
        <version>1.7.5</version>
    </dependency>
   ```

## <a name="add-sample-code"></a>Voorbeeldcode toevoegen

1. Om een nieuwe lege klasse aan uw Java-project toe te voegen, selecteert u **Bestand** > **Nieuw** > **Klasse**.

1. Voer in het venster **Nieuwe Java-klasse**, in het veld **Pakket**, **speechsdk.quickstart** in en voer in het veld **Naam** **Main** in.

   ![Schermopname van het venster Nieuwe Java-klasse](media/sdk/qs-java-jre-06-create-main-java.png)

1. Open de zojuist gemaakte **hoofd** klasse en vervang de inhoud van het bestand `Main.java` door de volgende begin code.

    ```java
    package speechsdk.quickstart;

    import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
    import com.microsoft.cognitiveservices.speech.audio.PullAudioOutputStream;
    import com.microsoft.cognitiveservices.speech.dialog.DialogServiceConfig;
    import com.microsoft.cognitiveservices.speech.dialog.DialogServiceConnector;
    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;

    import javax.sound.sampled.AudioFormat;
    import javax.sound.sampled.AudioSystem;
    import javax.sound.sampled.DataLine;
    import javax.sound.sampled.SourceDataLine;
    import java.io.InputStream;

    public class Main {
        final Logger log = LoggerFactory.getLogger(Main.class);

        public static void main(String[] args) {
            // New code will go here
        }

        private void playAudioStream(PullAudioOutputStream audio) {
            ActivityAudioStream stream = new ActivityAudioStream(audio);
            final ActivityAudioStream.ActivityAudioFormat audioFormat = stream.getActivityAudioFormat();
            final AudioFormat format = new AudioFormat(
                    AudioFormat.Encoding.PCM_SIGNED,
                    audioFormat.getSamplesPerSecond(),
                    audioFormat.getBitsPerSample(),
                    audioFormat.getChannels(),
                    audioFormat.getFrameSize(),
                    audioFormat.getSamplesPerSecond(),
                    false);
            try {
                int bufferSize = format.getFrameSize();
                final byte[] data = new byte[bufferSize];

                SourceDataLine.Info info = new DataLine.Info(SourceDataLine.class, format);
                SourceDataLine line = (SourceDataLine) AudioSystem.getLine(info);
                line.open(format);

                if (line != null) {
                    line.start();
                    int nBytesRead = 0;
                    while (nBytesRead != -1) {
                        nBytesRead = stream.read(data);
                        if (nBytesRead != -1) {
                            line.write(data, 0, nBytesRead);
                        }
                    }
                    line.drain();
                    line.stop();
                    line.close();
                }
                stream.close();

            } catch (Exception e) {
                e.printStackTrace();
            }
        }

    }
    ```

1. In de methode **Main** kunt u uw `DialogServiceConfig` eerst configureren en gebruiken om een `DialogServiceConnector`-exemplaar te maken. Hiermee wordt verbinding gemaakt met het directe lijn spraak kanaal om te communiceren met uw bot. Er wordt ook een `AudioConfig`-exemplaar gebruikt voor het opgeven van de bron voor audio-invoer. In dit voor beeld wordt de standaard microfoon gebruikt met `AudioConfig.fromDefaultMicrophoneInput()`.

    * Vervang de teken reeks `YourSubscriptionKey` door uw abonnements sleutel, die u [hier](get-started.md)kunt bereiken.
    * Vervang de teken reeks `YourServiceRegion` door de [regio](regions.md) die aan uw abonnement is gekoppeld.
    * Vervang de teken reeks `YourChannelSecret` door het geheim van het directe lijn spraak kanaal.

    > [!NOTE]
    > Direct line Speech (preview) is momenteel beschikbaar in een subset van de regio's met spraak Services. Raadpleeg [de lijst met ondersteunde regio's voor de eerste virtuele assistenten voor spraak](regions.md#voice-first-virtual-assistants) en zorg ervoor dat uw resources in een van deze regio's worden geïmplementeerd.

    ```java
    final String channelSecret = "YourChannelSecret"; // Your channel secret
    final String subscriptionKey = "YourSubscriptionKey"; // Your subscription key
    final String region = "YourServiceRegion"; // Your speech subscription service region. Note: only a subset of regions are currently supported
    final DialogServiceConfig botConfig = DialogServiceConfig.fromBotSecret(channelSecret, subscriptionKey, region);

    // Configure audio input from microphone.
    final AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();

    // Create a DialogServiceConnector instance
    final DialogServiceConnector connector = new DialogServiceConnector(botConfig, audioConfig);
    ```

1. `DialogServiceConnector` is afhankelijk van verschillende gebeurtenissen om de bot-activiteiten, de resultaten van spraak herkenning en andere informatie te communiceren. Voeg deze gebeurtenislisteners toe volgende.

    ```java
    // Recognizing will provide the intermediate recognized text while an audio stream is being processed
    connector.recognizing.addEventListener((o, speechRecognitionResultEventArgs) -> {
        log.info("Recognizing speech event text: {}", speechRecognitionResultEventArgs.getResult().getText());
    });

    // Recognized will provide the final recognized text once audio capture is completed
    connector.recognized.addEventListener((o, speechRecognitionResultEventArgs) -> {
        log.info("Recognized speech event reason text: {}", speechRecognitionResultEventArgs.getResult().getText());
    });

    // SessionStarted will notify when audio begins flowing to the service for a turn
    connector.sessionStarted.addEventListener((o, sessionEventArgs) -> {
        log.info("Session Started event id: {} ", sessionEventArgs.getSessionId());
    });

    // SessionStopped will notify when a turn is complete and it's safe to begin listening again
    connector.sessionStopped.addEventListener((o, sessionEventArgs) -> {
        log.info("Session stopped event id: {}", sessionEventArgs.getSessionId());
    });

    // Canceled will be signaled when a turn is aborted or experiences an error condition
    connector.canceled.addEventListener((o, canceledEventArgs) -> {
        log.info("Canceled event details: {}", canceledEventArgs.getErrorDetails());
        connector.disconnectAsync();
    });

    // ActivityReceived is the main way your bot will communicate with the client and uses bot framework activities.
    connector.activityReceived.addEventListener((o, activityEventArgs) -> {
        final String act = activityEventArgs.getActivity().serialize();
            log.info("Received activity {} audio", activityEventArgs.hasAudio() ? "with" : "without");
            if (activityEventArgs.hasAudio()) {
                playAudioStream(activityEventArgs.getAudio());
            }
        });
    ```

1. Verbind de `DialogServiceConnector` om een regel spraak te maken door de `connectAsync()`-methode aan te roepen. Als u uw bot wilt testen, kunt u de methode `listenOnceAsync` aanroepen om audio-invoer van uw microfoon te verzenden. Daarnaast kunt u ook de methode `sendActivityAsync` gebruiken om een aangepaste activiteit te verzenden als een geserialiseerde teken reeks. Deze aangepaste activiteiten kunnen extra gegevens leveren die uw bot in het gesprek gebruikt.

    ```java
    connector.connectAsync();
    // Start listening.
    System.out.println("Say something ...");
    connector.listenOnceAsync();

    // connector.sendActivityAsync(...)
    ```

1. Sla de wijzigingen in het bestand `Main` op.

1. Als u het afspelen van antwoorden wilt ondersteunen, voegt u een extra klasse toe die het PullAudioOutputStream-object dat wordt geretourneerd door de getAudio-API naar een Java-InputStream voor gemakkelijke verwerking kunt transformeren. Deze ActivityAudioStream is een gespecialiseerde klasse waarmee geluids reacties van het "directe-lijn spraak kanaal" wordt verwerkt. Het biedt accesss de mogelijkheid om audio-indelings gegevens op te halen die nodig zijn voor het afhandelen van afspelen Selecteer voor die **bestands** > **nieuwe** > -**klasse**.

1. Voer in het venster **nieuwe Java** -klasse **speechsdk. Quick** start in het veld **pakket** in en **ActivityAudioStream** in het veld **naam** .

1. Open de zojuist gemaakte **ActivityAudioStream** -klasse en vervang door de hieronder weer gegeven code.

    ```java
    package com.speechsdk.quickstart;

    import com.microsoft.cognitiveservices.speech.audio.PullAudioOutputStream;

    import java.io.IOException;
    import java.io.InputStream;


    public final class ActivityAudioStream extends InputStream {
        /**
         * The number of samples played per second. (16 kHz)
         */
        public static final long SAMPLE_RATE = 16000;
        /**
         * The number of bits in each sample of a sound that has this format. (16 bits)
         */
        public static final int BITS_PER_SECOND = 16;
        /**
         * The number of audio channels in this format (1 for mono).
         */
        public static final int CHANNELS = 1;
        /**
         * The number of bytes in each frame of a sound that has this format (2).
         */
        public static final int FRAME_SIZE = 2;

        /**
         * Reads up to a specified maximum number of bytes of data from the audio
         * stream, putting them into the given byte array.
         *
         * @param b   the buffer into which the data is read
         * @param off the offset, from the beginning of array <code>b</code>, at which
         *            the data will be written
         * @param len the maximum number of bytes to read
         * @return the total number of bytes read into the buffer, or -1 if there
         * is no more data because the end of the stream has been reached
         */
        @Override
        public int read(byte[] b, int off, int len) {
            byte[] tempBuffer = new byte[len];
            int n = (int) this.pullStreamImpl.read(tempBuffer);
            for (int i = 0; i < n; i++) {
                if (off + i > b.length) {
                    throw new ArrayIndexOutOfBoundsException(b.length);
                }
                b[off + i] = tempBuffer[i];
            }
            if (n == 0) {
                return -1;
            }
            return n;
        }

        /**
         * Reads the next byte of data from the activity audio stream if available.
         *
         * @return the next byte of data, or -1 if the end of the stream is reached
         * @see #read(byte[], int, int)
         * @see #read(byte[])
         * @see #available
         * <p>
         */
        @Override
        public int read() {
            byte[] data = new byte[1];
            int temp = read(data);
            if (temp <= 0) {
                // we have a weird situation if read(byte[]) returns 0!
                return -1;
            }
            return data[0] & 0xFF;
        }

        /**
         * Reads up to a specified maximum number of bytes of data from the activity audio stream
         * putting them into the given byte array.
         *
         * @param b the buffer into which the data is read
         * @return the total number of bytes read into the buffer, or -1 if there
         * is no more data because the end of the stream has been reached
         */
        @Override
        public int read(byte[] b) {
            int n = (int) pullStreamImpl.read(b);
            if (n == 0) {
                return -1;
            }
            return n;
        }

        /**
         * Skips over and discards a specified number of bytes from this
         * audio input stream.
         *
         * @param n the requested number of bytes to be skipped
         * @return the actual number of bytes skipped
         * @throws IOException if an input or output error occurs
         * @see #read
         * @see #available
         */
        @Override
        public long skip(long n) {
            if (n <= 0) {
                return 0;
            }
            if (n <= Integer.MAX_VALUE) {
                byte[] tempBuffer = new byte[(int) n];
                return read(tempBuffer);
            }
            long count = 0;
            for (long i = n; i > 0; i -= Integer.MAX_VALUE) {
                int size = (int) Math.min(Integer.MAX_VALUE, i);
                byte[] tempBuffer = new byte[size];
                count += read(tempBuffer);
            }
            return count;
        }

        /**
         * Closes this audio input stream and releases any system resources associated
         * with the stream.
         */
        @Override
        public void close() {
            this.pullStreamImpl.close();
        }

        /**
         * Fetch the audio format for the ActivityAudioStream. The ActivityAudioFormat defines the sample rate, bits per sample and the # channels
         *
         * @return instance of the ActivityAudioFormat associated with the stream
         */
        public ActivityAudioStream.ActivityAudioFormat getActivityAudioFormat() {
            return activityAudioFormat;
        }

        /**
         * Returns the maximum number of bytes that can be read (or skipped over) from this
         * audio input stream without blocking.
         *
         * @return the number of bytes that can be read from this audio input stream without blocking.
         * As this implementation does not buffer this will be defaulted to 0
         */
        @Override
        public int available() {
            return 0;
        }

        public ActivityAudioStream(final PullAudioOutputStream stream) {
            pullStreamImpl = stream;
            this.activityAudioFormat = new ActivityAudioStream.ActivityAudioFormat(SAMPLE_RATE, BITS_PER_SECOND, CHANNELS, FRAME_SIZE, AudioEncoding.PCM_SIGNED);
        }

        private PullAudioOutputStream pullStreamImpl;

        private ActivityAudioFormat activityAudioFormat;

        /**
         * ActivityAudioFormat is an internal format which contains metadata regarding the type of arrangement of
         * audio bits in this activity audio stream.
         */
        static class ActivityAudioFormat {

            private long samplesPerSecond;
            private int bitsPerSample;
            private int channels;
            private int frameSize;
            private AudioEncoding encoding;

            public ActivityAudioFormat(long samplesPerSecond, int bitsPerSample, int channels, int frameSize, AudioEncoding encoding) {
                this.samplesPerSecond = samplesPerSecond;
                this.bitsPerSample = bitsPerSample;
                this.channels = channels;
                this.encoding = encoding;
                this.frameSize = frameSize;
            }

            /**
             * Fetch the number of samples played per second for the associated audio stream format.
             *
             * @return the number of samples played per second
             */
            public long getSamplesPerSecond() {
                return samplesPerSecond;
            }

            /**
             * Fetch the number of bits in each sample of a sound that has this audio stream format.
             *
             * @return the number of bits per sample
             */
            public int getBitsPerSample() {
                return bitsPerSample;
            }

            /**
             * Fetch the number of audio channels used by this audio stream format.
             *
             * @return the number of channels
             */
            public int getChannels() {
                return channels;
            }

            /**
             * Fetch the default number of bytes in a frame required by this audio stream format.
             *
             * @return the number of bytes
             */
            public int getFrameSize() {
                return frameSize;
            }

            /**
             * Fetch the audio encoding type associated with this audio stream format.
             *
             * @return the encoding associated
             */
            public AudioEncoding getEncoding() {
                return encoding;
            }
        }

        /**
         * Enum defining the types of audio encoding supported by this stream
         */
        public enum AudioEncoding {
            PCM_SIGNED("PCM_SIGNED");

            String value;

            AudioEncoding(String value) {
                this.value = value;
            }
        }
    }

    ```

1. Sla de wijzigingen in het bestand `ActivityAudioStream` op.

## <a name="build-and-run-the-app"></a>De app bouwen en uitvoeren

Druk op F11 of selecteer **Uitvoeren** > **Fouten opsporen**.
In de-console wordt een bericht weer gegeven met de tekst ' zeg iets ' op dit moment kunt u een Engelse zin of zin spreken die uw bot begrijpt. Uw spraak wordt via het directe lijn spraak kanaal naar uw bot verzonden, waar deze wordt herkend, verwerkt door uw bot en het antwoord wordt geretourneerd als een activiteit. Als uw bot spraak retourneert als reactie, wordt de audio afgespeeld met behulp van de klasse `AudioPlayer`.

![Schermafbeelding van console-uitvoer na geslaagde herkenning](media/sdk/qs-java-jre-08-console-output.png)

## <a name="next-steps"></a>Volgende stappen

Op GitHub vindt u aanvullende voorbeelden, zoals hoe u spraak kunt lezen vanuit een audiobestand.

> [!div class="nextstepaction"]
> [Een basisbot maken en implementeren](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)

## <a name="see-also"></a>Zie ook

- [Over de spraak-eerste virtuele assistenten](voice-first-virtual-assistants.md)
- [Gratis een abonnements sleutel voor spraak Services aanschaffen](get-started.md)
- [Aangepaste Ontwaak woorden](speech-devices-sdk-create-kws.md)
- [Directe lijn spraak op uw bot aansluiten](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
- [Bekijk Java-voorbeelden op GitHub](https://aka.ms/csspeech/samples)
