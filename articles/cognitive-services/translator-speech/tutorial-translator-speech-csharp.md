---
title: 'Zelfstudie: Translator Speech-API C#'
titleSuffix: Azure Cognitive Services
description: Gebruik de Translator Speech-API om tekst in realtime te vertalen.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: tutorial
ms.date: 3/5/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 669322aefb152729cc120abc0d7a52ce9d1151da
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965287"
---
# <a name="tutorial-translator-speech-application-in-c"></a>Zelfstudie: Translator Speech-toepassing in C#

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

Deze zelfstudie is een rondleiding in een interactief hulpprogramma voor spraakomzetting dat gebruikmaakt van de Translator Speech-API van Azure Cognitive Services. U leert het volgende:

> [!div class="checklist"]
> * Een lijst met talen aanvragen die worden ondersteund in de service voor spraakomzetting
> * Audio opnemen en verzenden naar de service
> * Vertalingen van spraak ontvangen en weergeven als tekst
> * Een gesproken versie (tekst-naar-spraak) van de vertaling afspelen (optioneel)

Een Visual Studio-oplossingsbestand voor deze toepassing is [beschikbaar in GitHub](https://github.com/MicrosoftTranslator/SpeechTranslator).

## <a name="prerequisites"></a>Vereisten

Voor deze zelf studie hebt u een editie van Visual Studio 2019, inclusief de Community Edition, nodig.

Met de Visual Studio-oplossing wordt ook een installatieprogramma voor de toepassing gebouwd. U hebt de [WiX Toolset](http://wixtoolset.org/) en de [WiX Toolset Visual Studio-extensie](https://marketplace.visualstudio.com/items?itemName=RobMensching.WixToolsetVisualStudio2017Extension) nodig om deze functionaliteit te ondersteunen.

U hebt ook een abonnementssleutel nodig voor de Translator Speech-service. Deze kunt u verkrijgen via het Microsoft Azure-dashboard. Er is een gratis prijslaag beschikbaar waarmee u gratis maximaal 10 uur spraak per maand kunt vertalen. Deze laag is voldoende voor deze zelfstudie.

De [JSON.net-bibliotheek](https://www.newtonsoft.com/json) van derden (van Newton Soft) is ook vereist. Deze assembly wordt automatisch geïnstalleerd met NuGet als beide selectievakjes voor Pakket herstellen zijn ingeschakeld in de Visual Studio-opties.

## <a name="trying-the-translation-app"></a>De vertaal-app uitproberen

Nadat u de Speech Translator-oplossing (`SpeechTranslator.sln`) hebt geopend in Visual Studio, drukt u op F5 om de toepassing te bouwen en te starten.  Het hoofdvenster van het programma wordt weergegeven.

![[Hoofdvenster van Speech Translator]](media/speech-translator-main-window.png)

Kies bij de eerste uitvoering in het menu **Instellingen** de optie **Accountinstellingen** om het venster te openen dat hier wordt weergegeven.

![[Hoofdvenster van Speech Translator]](media/speech-translator-settings-window.png)

Plak de Translator Speech-abonnementssleutel in dit venster en klik vervolgens op **Opslaan**. De sleutel wordt opgeslagen tijden uitvoerbewerkingen.

Als u weer in het hoofdvenster bent, kiest u de geluidsinvoer- en uitvoerapparaten die u wilt gebruiken, en de bron- en doeltaal. Als u audio van de vertaling wilt horen, moet u ervoor zorgen dat de optie **TTS** (tekst-naar-spraak) is ingeschakeld. Als u speculatieve gedeeltelijke vertalingen wilt zien terwijl u spreekt, schakelt u de optie **Gedeeltelijke resultaten** in.

Klik ten slotte op **Start** om de vertaling te starten. Zeg iets wat u wilt laten vertalen, en kijk hoe de herkende tekst en de vertaling in het venster verschijnen. Als u de optie TTS hebt ingeschakeld, hoort u de vertaling ook.

## <a name="obtaining-supported-languages"></a>Ondersteunde talen ophalen

Ten tijde van dit schrijven biedt de Translator Speech-service ondersteuning voor tekstvertaling in meer dan zestig talen. Een kleiner aantal talen wordt ondersteund voor spraakomzetting. Voor dergelijke talen is ondersteuning vereist voor zowel transcriptie (spraakherkenning) als voor synthese (tekst-naar-spraak-uitvoer).

Met andere woorden: voor spraakomzetting moet de brontaal zijn ondersteund voor transcriptie. De doeltaal kan elke taal zijn die wordt ondersteund voor tekstvertaling, aangenomen dat u een tekstresultaat wilt zien. Als u spraakuitvoer wilt, kunt u alleen vertalen in een taal die wordt ondersteund voor tekst-naar-spraak.

Zo nu en dan wordt in Microsoft ondersteuning toegevoegd voor nieuwe talen. Het is dus raadzaam om kennis van ondersteunde talen niet vast te leggen in uw toepassing. In plaats hiervan biedt de Translator Speech-API een eindpunt Talen waarmee u de ondersteunde talen tijdens de uitvoering kunt ophalen. U kunt ervoor kiezen om een of meer lijsten met talen te ontvangen:

| | |
|-|-|
|`speech`|De ondersteunde talen voor spraaktranscriptie. Kunnen brontalen voor spraakomzetting zijn.|
|`text`|De ondersteunde talen voor tekst-naar-tekst-vertaling. Kunnen doeltalen voor spraakomzetting zijn wanneer tekstuitvoer wordt gebruikt.|
|`tts`|De stemmen die worden ondersteund voor spraaksynthese, elk gekoppeld aan een bepaalde taal. Kunnen doeltalen voor spraakomzetting zijn wanneer tekst-naar-spraak wordt gebruikt. Een bepaalde taal kan ondersteuning hebben voor meer dan één stem.|

Voor het eindpunt Talen is geen abonnementssleutel vereist, en het gebruik van het eindpunt telt niet mee in uw quotum. De bijbehorende URI is `https://dev.microsofttranslator.com/languages` en de resultaten worden geretourneerd in de JSON-indeling.

Met de methode `UpdateLanguageSettingsAsync()` in `MainWindow.xaml.cs`, die hier wordt weergegeven, wordt het eindpunt Talen aangeroepen om de lijst met ondersteunde talen op te halen.

```csharp
private async Task UpdateLanguageSettingsAsync()
{
    Uri baseUri = new Uri("https://" + baseUrl);
    string fullUriString = "/Languages?api-version=1.0&scope=text,speech,tts";
    if (MenuItem_Experimental.IsChecked) fullUriString += "&flight=experimental";            
    Uri fullUri = new Uri(baseUri, fullUriString);

    using (HttpClient client = new HttpClient()) //'client' is the var - using statement ensures the dispose method is used even after an exception.
    {
        HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, fullUri);

        // get language names for current UI culture:
        request.Headers.Add("Accept-Language", CultureInfo.CurrentUICulture.TwoLetterISOLanguageName);

        // add a client-side trace Id. In case of issues, one can contact support and provide this:
        //string traceId = "SpeechTranslator" + Guid.NewGuid().ToString();
        //request.Headers.Add("X-ClientTraceId", traceId);
        //Debug.Print("TraceId: {0}", traceId);

        client.Timeout = TimeSpan.FromMilliseconds(10000);
        HttpResponseMessage response = await client.SendAsync(request); //make the async call to the web using the client var and passing the built up URI
        response.EnsureSuccessStatusCode(); //causes exception if the return is false

        Debug.Print("Request Id returned: {0}", GetRequestId(response));

        //create dictionaries to hold the language specific data
        spokenLanguages = new Dictionary<string, string>();
        fromLanguages = new Dictionary<string, string>();
        textLanguages = new Dictionary<string, string>();
        isLTR = new Dictionary<string, bool>();
        voices = new Dictionary<string, List<TTsDetail>>();

        JObject jResponse = JObject.Parse(await response.Content.ReadAsStringAsync()); //get the json from the async call with the response var created above, parse it and put it in a var called jResponse - JObject is a newton class

        //Gather the set of TTS voices
        foreach (JProperty jTts in jResponse["tts"])
        {
            JObject ttsDetails = (JObject)jTts.Value;

            string code = jTts.Name;
            string language = ttsDetails["language"].ToString();
            string displayName = ttsDetails["displayName"].ToString();
            string gender = ttsDetails["gender"].ToString();

            if (!voices.ContainsKey(language)) //check dictionary for a specific key value
            {
                voices.Add(language, new List<TTsDetail>()); //add to the dictionary the locale key and a ttsDetail object
            }

            voices[language].Add(new TTsDetail() { Code = code, DisplayName = string.Format("{0} ({1})", displayName, gender) });
        }

        // Gather the set of speech translation languages
        foreach (JProperty jSpeech in jResponse["speech"])
        {
            JObject languageDetails = (JObject)jSpeech.Value;
            string code = jSpeech.Name;
            string simplecode = languageDetails["language"].ToString();
            string displayName = languageDetails["name"].ToString();
            spokenLanguages.Add(code, displayName);
            fromLanguages.Add(code,simplecode);
        }

        spokenLanguages = spokenLanguages.OrderBy(x => x.Value).ToDictionary(x => x.Key, x => x.Value);
        FromLanguage.Items.Clear();
        foreach (var language in spokenLanguages)
        {
            FromLanguage.Items.Add(new ComboBoxItem() { Content = language.Value, Tag = language.Key});
        }

        // Gather the set of text translation languages
        foreach (JProperty jText in jResponse["text"])
        {
            JObject languageDetails = (JObject)jText.Value;
            string code = jText.Name;
            string displayName = languageDetails["name"].ToString();
            textLanguages.Add(code, displayName);

            string direction = languageDetails["dir"].ToString().ToLowerInvariant();
            bool LTR = true;
            if (direction.ToLowerInvariant() == "rtl") LTR = false;
            isLTR.Add(code, LTR);
        }

        textLanguages = textLanguages.OrderBy(x => x.Value).ToDictionary(x => x.Key, x => x.Value);
        ToLanguage.Items.Clear();
        foreach (var language in textLanguages)
        {
            ToLanguage.Items.Add(new ComboBoxItem() { Content = language.Value, Tag = language.Key });
        }

        if (Properties.Settings.Default.FromLanguageIndex >= 0) FromLanguage.SelectedIndex = Properties.Settings.Default.FromLanguageIndex;
        else
        {
            for(int i=0; i < FromLanguage.Items.Count; ++i)
            {
                ComboBoxItem item = (ComboBoxItem)FromLanguage.Items[i];
                if(CultureInfo.CurrentUICulture.Name.Equals((string)item.Tag, StringComparison.OrdinalIgnoreCase))
                {
                    FromLanguage.SelectedIndex = i;
                }
            }
        }
        if (Properties.Settings.Default.ToLanguageIndex >= 0) ToLanguage.SelectedIndex = Properties.Settings.Default.ToLanguageIndex;
        else
        {
            Random rnd = new Random(DateTime.Now.Millisecond);
            ToLanguage.SelectedIndex = (rnd.Next() % textLanguages.Count);
        }
    }
}
```

Met deze methode wordt eerst een HTTP-aanvraag naar het eindpunt Talen gemaakt, waarbij alle drie de lijsten met talen worden aangevraagd (`text`, `speech` en `tts`).

Het eindpunt Talen maakt gebruik van de `Accept-Languages`-header van de aanvraag om de taal te bepalen waarin de namen van de talen worden weergegeven. De taal die in het Engels bijvoorbeeld ‘German’ wordt genoemd, heet ‘Deutsch’ in het Duits en ‘Alemán’ in het Spaans, en in de lijst met talen worden deze verschillen weergegeven. Voor deze header wordt de standaardtaal van het systeem gebruikt.

Nadat de aanvraag is verzonden en het JSON-antwoord is ontvangen, wordt het antwoord geparseerd in interne gegevensstructuren. Deze structuren worden vervolgens gebruikt om de menu’s voor de brontaal en doeltaal samen te stellen.

Omdat de beschikbare stemmen afhankelijk zijn van de door de gebruiker gekozen doeltaal, kan het menu Stem nog niet worden ingesteld. In plaats hiervan worden de beschikbare stemmen voor elke taal opgeslagen voor later gebruik. Het menu Stem wordt later bijgewerkt met behulp van de `ToLanguage_SelectionChanged`-handler (in hetzelfde bronbestand) door `UpdateVoiceComboBox()` aan te roepen wanneer de gebruiker een doeltaal kiest.

Als de gebruiker de toepassing nog nooit eerder heeft uitgevoerd, wordt een willekeurige doeltaal geselecteerd. (De menu-instellingen worden tussen sessies in opgeslagen.)

## <a name="authenticating-requests"></a>Aanvragen verifiëren

U moet ter verificatie bij de Microsoft Translator Speech-service uw Azure-abonnementssleutel in de header verzenden als de waarde voor `Ocp-Apim-Subscription-Key` in de verbindingsaanvraag.

## <a name="translation-overview"></a>Vertalingsoverzicht

De Vertalen-API (WebSockets-eindpunt `wss://dev.microsofttranslator.com/speech/translate`) staat toe dat audio wordt vertaald in een mono, 16 kHz, 16-bit ondertekende WAVE-indeling. De service retourneert een of meer JSON-antwoorden met zowel de herkende als de vertaalde tekst. Als tekst-naar-spraak is aangevraagd, wordt een audiobestand verzonden.

De gebruiker kiest de audiobron met behulp van het menu Microphone/File Input. De audio kan afkomstig zijn van een audio-apparaat (zoals een microfoon) of van een `.WAV`-bestand.

De methode `StartListening_Click` wordt aangeroepen wanneer de gebruiker op de knop Start klikt. Met deze gebeurtenis-handler wordt vervolgens `Connect()` aangeroepen om te beginnen met het verzenden van audio naar het service-API-eindpunt. Met de methode `Connect()` worden de volgende taken uitgevoerd:


> [!div class="checklist"]
> * Gebruikersinstellingen ophalen uit het hoofdvenster en ze valideren
> * De invoer- en uitvoerstroom voor audio initialiseren
> * `ConnectAsync()` aanroepen om de rest van het werk af te handelen

Met `ConnectAsync()` worden vervolgens de volgende taken afgehandeld:

> [!div class="checklist"]
> * Verifiëren met de Azure-abonnementssleutel in header `Ocp-Apim-Subscription-Key`
> * Een `SpeechClient`-exemplaar maken (te vinden in `SpeechClient.cs`) om met de service te communiceren
> * `TextMessageDecoder`- en `BinaryMessageDecoder`-exemplaren initialiseren (zie `SpeechResponseDecoder.cs`) om antwoorden te verwerken
> * De audio via het `SpeechClient`-exemplaar verzenden naar de Translator Speech-service
> * De resultaten van de vertaling ontvangen en verwerken

`SpeechClient` heeft minder verantwoordelijkheden:

> [!div class="checklist"]
> * Een WebSocket-verbinding met de Translator Speech-service tot stand brengen
> * Audiogegevens verzenden en antwoorden ontvangen via de socket

## <a name="a-closer-look"></a>Nadere beschouwing

Als het goed is, is het nu duidelijk hoe delen van de toepassing samenwerken om een vertaalaanvraag uit te voeren. Laten we wat code onder de loep nemen en daarbij focussen op de relevante onderdelen.

Hier is een gedeeltelijke versie van `Connect()` waarin het instellen van audiostreams wordt getoond:

```csharp
private void Connect()
{
    if (this.currentState != UiState.ReadyToConnect) return;

    Stopwatch watch = Stopwatch.StartNew();
    UpdateUiState(UiState.Connecting);

    // Omitted: code to validate UI settings

    string tag = ((ComboBoxItem)Mic.SelectedItem).Tag as string;
    string audioFileInputPath = null;
    if (tag == "File")
    {
        audioFileInputPath = this.AudioFileInput.Text;
        foreach (string currFile in audioFileInputPath.Split('|'))
        {
            if (!File.Exists(currFile))
            {
                SetMessage(String.Format($"Invalid audio source: selected file {currFile} does not exist."), "", MessageKind.Error);
                UpdateUiState(UiState.ReadyToConnect);
                return;
            }
        }
    }
    bool shouldSuspendInputAudioDuringTTS = this.CutInputAudioCheckBox.IsChecked.HasValue ? this.CutInputAudioCheckBox.IsChecked.Value : false;

    correlationId = Guid.NewGuid().ToString("D").Split('-')[0].ToUpperInvariant();

    // Setup speech translation client options
    SpeechClientOptions options;

    string voicename = "";
    if (this.Voice.SelectedItem != null)
    {
        voicename = ((ComboBoxItem)this.Voice.SelectedItem).Tag.ToString();
    }
    options = new SpeechTranslateClientOptions()
    {
        TranslateFrom = ((ComboBoxItem)this.FromLanguage.SelectedItem).Tag.ToString(),
        TranslateTo = ((ComboBoxItem)this.ToLanguage.SelectedItem).Tag.ToString(),
        Voice = voicename,
    };

    options.Hostname = baseUrl;
    options.AuthHeaderKey = "Authorization";
    options.AuthHeaderValue = ""; // set later in ConnectAsync.
    options.ClientAppId = new Guid("EA66703D-90A8-436B-9BD6-7A2707A2AD99");
    options.CorrelationId = this.correlationId;
    options.Features = GetFeatures().ToString().Replace(" ", "");
    options.Profanity = ((SpeechClient.ProfanityFilter)Enum.Parse(typeof(SpeechClient.ProfanityFilter), GetProfanityLevel(), true)).ToString();
    options.Experimental = MenuItem_Experimental.IsChecked;

    // Setup player and recorder but don't start them yet.
    WaveFormat waveFormat = new WaveFormat(16000, 16, 1);

    // WaveProvider for incoming TTS
    // We use a rather large BufferDuration because we need to be able to hold an entire utterance.
    // TTS audio is received in bursts (faster than real-time).
    textToSpeechBytes = 0;
    playerTextToSpeechWaveProvider = new BufferedWaveProvider(waveFormat);
    playerTextToSpeechWaveProvider.BufferDuration = TimeSpan.FromMinutes(5);

    ISampleProvider sampleProvider = null;
    if (audioFileInputPath != null)
    {
        // Setup mixing of audio from input file and from TTS
        playerAudioInputWaveProvider = new BufferedWaveProvider(waveFormat);
        var srce1 = new Pcm16BitToSampleProvider(playerTextToSpeechWaveProvider);
        var srce2 = new Pcm16BitToSampleProvider(playerAudioInputWaveProvider);
        var mixer = new MixingSampleProvider(srce1.WaveFormat);
        mixer.AddMixerInput(srce1);
        mixer.AddMixerInput(srce2);
        sampleProvider = mixer;
    }
    else
    {
        recorder = new WaveIn();
        recorder.DeviceNumber = (int)((ComboBoxItem)Mic.SelectedItem).Tag;
        recorder.WaveFormat = waveFormat;
        recorder.DataAvailable += OnRecorderDataAvailable;
        sampleProvider = playerTextToSpeechWaveProvider.ToSampleProvider();
    }

    if (!batchMode)
    {
        player = new WaveOut();
        player.DeviceNumber = (int)((ComboBoxItem)Speaker.SelectedItem).Tag;
        player.Init(sampleProvider);
    }

    this.audioBytesSent = 0;

    string logAudioFileName = null;
    if (LogSentAudio.IsChecked|| LogReceivedAudio.IsChecked)
    {
        string logAudioPath = System.IO.Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.ApplicationData), Properties.Settings.Default.OutputDirectory);
        try
        {
            Directory.CreateDirectory(logAudioPath);
        }
        catch
        {
            this.AddItemToLog(string.Format("Could not create folder {0}", logAudioPath));
        }

        if (LogSentAudio.IsChecked)
        {
            logAudioFileName = System.IO.Path.Combine(logAudioPath, string.Format("audiosent_{0}.wav", this.correlationId));
        }

        if (LogReceivedAudio.IsChecked)
        {
            string fmt = System.IO.Path.Combine(logAudioPath, string.Format("audiotts_{0}_{{0}}.wav", this.correlationId));
            this.audioReceived = new BinaryMessageDecoder(fmt);
        }
    }
}
```

Een aanzienlijk deel van `Connect()` omvat het maken van een `SpeechClientOptions`-exemplaar (zie `SpeechClientOptions.cs`) om de opties voor vertalen op te slaan. Opties zijn onder andere de benodigde informatie om verbinding te maken met de service (zoals verificatiesleutel en hostnaam) en de functies die worden gebruikt voor de vertaling. Deze velden worden toegewezen aan de header-velden en HTTP-parameters die beschikbaar zijn gemaakt met [de Translator Speech-API](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference).

Met `Connect()` wordt ook het geluidsinvoerapparaat gemaakt en geïnitialiseerd (variabele `sampleProvider`) dat dient als bron van de spraak die moet worden vertaald. Dit apparaat is een hardware-invoerapparaat, zoals een microfoon, of een bestand met WAVE-audiogegevens.

Hier is de methode `ConnectAsync()` waarmee de klasse `speechClient` wordt geïnstantieerd en anonieme functies worden gekoppeld voor het verwerken van tekstantwoorden en binaire antwoorden van de service.

```csharp
private async Task ConnectAsync(SpeechClientOptions options, bool suspendInputAudioDuringTTS)
{
    await ADMAuthenticate(options);

    TextMessageDecoder textDecoder;

    s2smtClient = new SpeechClient((SpeechTranslateClientOptions)options, CancellationToken.None);

    s2smtClient.OnBinaryData += (c, a) => { AddSamplesToPlay(a, suspendInputAudioDuringTTS); };
    s2smtClient.OnEndOfBinaryData += (c, a) => { AddSamplesToPlay(a, suspendInputAudioDuringTTS); };
    s2smtClient.OnTextData += (c, a) => { textDecoder.AppendData(a); lastReceivedPacketTick = DateTime.Now.Ticks; };
    s2smtClient.OnEndOfTextData += (c, a) =>
    {
        textDecoder.AppendData(a);
        lastReceivedPacketTick = DateTime.Now.Ticks;
        textDecoder
            .Decode()
            .ContinueWith(t =>
            {
                if (t.IsFaulted)
                {
                    Log(t.Exception, "E: Failed to decode incoming text message.");
                }
                else
                {
                    object msg = t.Result;
                    if (msg.GetType() == typeof(FinalResultMessage))
                    {
                        // omitted: code to process final binary result
                    }
                    if (msg.GetType() == typeof(PartialResultMessage))
                    {
                        // omitted: code to process partial binary result
                    }
                }
            });
    };
    s2smtClient.Failed += (c, ex) =>
    {
        Log(ex, "E: SpeechTranslation client reported an error.");
    };
    s2smtClient.Disconnected += (c, ea) =>
    {
        SafeInvoke(() =>
        {
            // We only care to react to server disconnect when our state is Connected.
            if (currentState == UiState.Connected)
            {
                Log("E: Connection has been lost.");
                Log($"E: Errors (if any): \n{string.Join("\n", s2smtClient.Errors)}");
                Disconnect();
            }
        });
    };
    await s2smtClient.Connect();
}
```

Na de verificatie wordt via de methode het `SpeechClient`-exemplaar gemaakt. Met de klasse `SpeechClient` (in `SpeechClient.cs`) worden gebeurtenis-handlers aangeroepen bij de ontvangst van binaire gegevens en tekstgegevens. Aanvullende handlers worden aangeroepen wanneer de verbinding mislukt of wordt verbroken.

Binaire gegevens zijn audiogegevens (tekst-naar-spraak-uitvoer) die via de service worden verzonden wanneer TTS is ingeschakeld. Tekstgegevens zijn een gedeeltelijke of volledige vertaling van de gesproken tekst. Na het instantiëren worden met de methode functies gekoppeld om deze berichten te verwerken: audio wordt opgeslagen zodat deze later kan worden afgespeeld, en tekst wordt weergegeven in het venster.

## <a name="next-steps"></a>Volgende stappen

Dit codevoorbeeld is een toepassing met veel functies waarmee het gebruik van de Translator Speech-API wordt gedemonstreerd. Daarom is er een behoorlijk aantal bewegende delen waarvan u kennis moet nemen. U hebt nu de belangrijkste gedeelten bekeken. Verder kan het nuttig zijn om een paar breekpunten in Visual Studio te bepalen en het vertaalproces een keer te doorlopen. Als u de werking van de voorbeeldtoepassing onder de knie hebt, bent u er klaar voor om de Translator Speech-service te gebruiken in uw eigen toepassingen.

> [!div class="nextstepaction"]
> [ Naslaginformatie Microsoft Translator Speech-API](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference)
