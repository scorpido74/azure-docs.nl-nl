---
title: 'Zelfstudie: Voices schakelen uw bot in met spraak-SDK - Spraakservice'
titleSuffix: Azure Cognitive Services
description: In deze zelfstudie maakt u een Echo Bot met Microsoft Bot-Framework, implementeert u deze naar Azure en registreert u deze met het Bot-Framework Direct Line Speech-kanaal. Vervolgens configureert u een voorbeeldclient-app voor Windows waarmee u met uw bot spreken en deze op u horen reageren.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: dapine
ms.openlocfilehash: 96d2c2e5e3772575e681d2db079ab0122b7014e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80348542"
---
# <a name="tutorial-voice-enable-your-bot-using-the-speech-sdk"></a>Zelfstudie: Maak uw bot in schakelen met de Spraak-SDK

U nu de kracht van de Spraakservice gebruiken om een chatbot eenvoudig te bedienen.

In deze zelfstudie maakt u een Echo Bot met Microsoft Bot-Framework, implementeert u deze naar Azure en registreert u deze met het Bot-Framework Direct Line Speech-kanaal. Vervolgens configureert u een voorbeeldclient-app voor Windows waarmee u met uw bot spreken en deze op u horen reageren.

Deze zelfstudie is ontworpen voor ontwikkelaars die net aan hun reis beginnen met Azure, Bot-Framework-bots, Direct Line Speech of de Speech SDK, en die snel een werkend systeem willen bouwen met beperkte codering. Er is geen ervaring of vertrouwdheid met deze diensten nodig.

Aan het einde van deze oefening, heb je het opzetten van een systeem dat zal werken als volgt:

1. De voorbeeldclienttoepassing is geconfigureerd om verbinding te maken met direct line spraakkanaal en de Echo Bot
1. Audio wordt opgenomen via de standaardmicrofoon op de knop druk (of continu opgenomen als aangepast trefwoord is geactiveerd)
1. Optioneel vindt aangepaste trefwoorddetectie plaats, waarbij audiostreaming naar de cloud wordt afgesloten
1. Met SpraakSDK maakt de app verbinding met direct line spraakkanaal en streamt audio
1. Optioneel vindt een hogere nauwkeurigheidstrefwoordverificatie plaats op de service
1. De audio wordt doorgegeven aan de spraakherkenningsservice en getranscribeerd naar tekst
1. De herkende tekst wordt doorgegeven aan de Echo-Bot als een Bot Framework Activity 
1. De antwoordtekst wordt omgezet in audio door de TTS-service (Text-to-Speech) en wordt teruggestreamd naar de clienttoepassing voor afspelen

![diagram-tag](media/tutorial-voice-enable-your-bot-speech-sdk/diagram.png "De spraakkanaalstroom")

> [!NOTE]
> De stappen in deze zelfstudie vereisen geen betaalde service. Als nieuwe Azure-gebruiker u credits van uw gratis Azure-proefabonnement en de gratis laag van de Spraakservice gebruiken om deze zelfstudie te voltooien.

Hier is wat deze tutorial heeft betrekking op:
> [!div class="checklist"]
> * Nieuwe Azure-bronnen maken
> * Het voorbeeld van Echo Bot bouwen, testen en implementeren in een Azure App-service
> * Registreer je bot met Direct Line Speech-kanaal
> * De Windows Voice Assistant-client bouwen en uitvoeren om te communiceren met uw Echo Bot
> * Aangepaste trefwoordactivering toevoegen
> * Leer de taal van de erkende en gesproken spraak te wijzigen

## <a name="prerequisites"></a>Vereisten

Dit is wat je nodig hebt om deze zelfstudie te voltooien:

- Een Windows 10-pc met een werkende microfoon en luidsprekers (of hoofdtelefoon)
- [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) of hoger
- [.NET Core SDK](https://dotnet.microsoft.com/download) versie 2.1 of hoger
- Een Azure-account. [Schrijf je hier gratis in.](https://azure.microsoft.com/free/ai/)
- Een [GitHub-account](https://github.com/)
- [Git voor Windows](https://git-scm.com/download/win)

## <a name="create-a-resource-group"></a>Een resourcegroep maken

De client-app die u in deze zelfstudie maakt, maakt gebruik van een handvol Azure-services. Als u de retourtijd voor reacties van uw bot wilt verkorten, wilt u ervoor zorgen dat deze services zich in dezelfde Azure-regio bevinden. In deze sectie maakt u een resourcegroep in de **regio West-VS.** Deze resourcegroep wordt gebruikt bij het maken van afzonderlijke bronnen voor het Bot-Framework, het direct line-spraakkanaal en de spraakservice.

1. <a href="https://ms.portal.azure.com/#create/Microsoft.ResourceGroup" target="_blank">Een resourcegroep maken<span class="docon docon-navigate-external x-hidden-focus"></span></a>
1. U wordt gevraagd om informatie te verstrekken:
   * **Abonnement instellen op** gratis **proefperiode** (u ook een bestaand abonnement gebruiken).
   * Voer een naam in voor uw **resourcegroep**. Wij raden **SpeechEchoBotTutorial-ResourceGroup**aan.
   * Selecteer **west-VS**in de **vervolgkeuzelijst Regio** .
1. Klik op **Controleren en maken**. U ziet een banner waarop **Validatie is geslaagd .**
1. Klik **op Maken**. Het kan enkele minuten duren voordat de resourcegroep is gemaakt.
1. Net als bij de bronnen die u later in deze zelfstudie maakt, is het een goed idee om deze brongroep vast te maken aan uw dashboard voor eenvoudige toegang. Als u deze brongroep wilt vastmaken, klikt u op het pictogram van de pin in de rechterbovenhoek van het dashboard.

### <a name="choosing-an-azure-region"></a>Een Azure-regio kiezen

Als u een andere regio wilt gebruiken voor deze zelfstudie, kunnen deze factoren uw keuzes beperken:

* Zorg ervoor dat u een [ondersteunde Azure-regio gebruikt.](regions.md#voice-assistants)
* Het direct line speech kanaal maakt gebruik van de text-to-speech service, die standaard en neurale stemmen heeft. Neurale stemmen zijn [beperkt tot specifieke Azure-regio's.](regions.md#standard-and-neural-voices)
* Gratis proefsleutels kunnen worden beperkt tot een specifieke regio.

Zie [Azure-locaties](https://azure.microsoft.com/global-infrastructure/locations/)voor meer informatie over regio's.

## <a name="create-resources"></a>Resources maken

Nu u een resourcegroep in een ondersteund gebied hebt, is de volgende stap het maken van afzonderlijke bronnen voor elke service die u in deze zelfstudie gebruikt.

### <a name="create-a-speech-service-resource"></a>Een spraakservicebron maken

Volg deze instructies om een spraakbron te maken:

1. <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Een spraakservicebron maken<span class="docon docon-navigate-external x-hidden-focus"></span></a>
4. U wordt gevraagd om informatie te verstrekken:
   * Geef uw resource een **naam.** Wij raden **SpeechEchoBotTutorial-Speech**
   * Controleer **bij Abonnement**of Gratis **proefversie** is geselecteerd.
   * Selecteer West **US**voor **Locatie**.
   * Selecteer **F0** **voor de prijsniveau**. Dit is de gratis laag.
   * Selecteer **SpeechEchoBotTutorial-ResourceGroup**voor **resourcegroep**.
5. Nadat u alle vereiste gegevens hebt ingevoerd, klikt u op **Maken**. Het kan enkele minuten duren voordat uw resource is gemaakt.
6. Later in deze zelfstudie heb je abonnementssleutels voor deze service nodig. U deze sleutels op elk gewenst moment openen via het **overzicht** van uw bron (Sleutels beheren) of **Sleutels.**

Controleer op dit punt of uw resourcegroep (**SpeechEchoBotTutorial-ResourceGroup**) een spraakbron heeft:

| Name | Type  | Locatie |
|------|-------|----------|
| SpeechechoBotTutorial-Speech | Cognitive Services | VS - west |

### <a name="create-an-azure-app-service-plan"></a>Een Azure App Service-plan maken

De volgende stap is het maken van een App Service Plan. Een App Service-plan definieert een set rekenresources waarmee een web-app kan worden uitgevoerd.

1. <a href="https://ms.portal.azure.com/#create/Microsoft.AppServicePlanCreate" target="_blank">Een Azure App Service-abonnement maken<span class="docon docon-navigate-external x-hidden-focus"></span></a>
4. U wordt gevraagd om informatie te verstrekken:
   * **Abonnement instellen op** gratis **proefperiode** (u ook een bestaand abonnement gebruiken).
   * Selecteer **SpeechEchoBotTutorial-ResourceGroup**voor **resourcegroep**.
   * Geef uw resource een **naam.** Wij raden **SpeechEchoBotTutorial-AppServicePlan**
   * Selecteer **Windows**voor **het besturingssysteem**.
   * Selecteer West **US**voor **Regio**.
   * Controleer **bij prijscategorie**of **standaard S1** is geselecteerd. Dit moet de standaardwaarde zijn. Als dit niet het is, moet u ervoor zorgen dat u het **besturingssysteem** instelt op **Windows** zoals hierboven beschreven.
5. Klik op **Controleren en maken**. U ziet een banner waarop **Validatie is geslaagd .**
6. Klik **op Maken**. Het kan enkele minuten duren voordat de resourcegroep is gemaakt.

Controleer op dit punt of uw resourcegroep (**SpeechEchoBotTutorial-ResourceGroup**) twee bronnen heeft:

| Name | Type  | Locatie |
|------|-------|----------|
| SpeechechoBotTutorial-AppServicePlan | App Service-plan | VS - west |
| SpeechechoBotTutorial-Speech | Cognitive Services | VS - west |

## <a name="build-an-echo-bot"></a>Een Echo Bot bouwen

Nu u een aantal bronnen hebt gemaakt, laten we een bot bouwen. We beginnen met het Echo Bot-voorbeeld, dat zoals de naam al aangeeft, de tekst weerkaatst die je hebt ingevoerd als reactie. Maak je geen zorgen, de voorbeeldcode is klaar voor gebruik zonder wijzigingen. Het is geconfigureerd om te werken met het Direct Line Speech-kanaal, waarmee we verbinding maken nadat we de bot hebben geïmplementeerd op Azure.

> [!NOTE]
> De instructies die volgen, evenals aanvullende informatie over de Echo Bot zijn beschikbaar in de README van het [voorbeeld op GitHub.](https://github.com/microsoft/BotBuilder-Samples/blob/master/samples/csharp_dotnetcore/02.echo-bot/README.md)

### <a name="run-the-bot-sample-on-your-machine"></a>Het botmonster op uw machine uitvoeren

1. Kloon de monsters repository:

   ```bash
   git clone https://github.com/Microsoft/botbuilder-samples.git
   ```

2. Start Visual Studio.
3. Selecteer op de werkbalk**Project/oplossing** **bestand** > **openen** > en open de Projectoplossing Echo Bot:

   ```
   samples\csharp_dotnetcore\02.echo-bot\EchoBot.sln
   ```

4. Nadat het project is geladen, drukt u op <kbd>F5</kbd> om het project te bouwen en uit te voeren.
5. Een browser moet starten en je ziet een scherm op zoek naar soortgelijke.
    > [!div class="mx-imgBorder"]
    > [![echobot-running-on-localhost](media/tutorial-voice-enable-your-bot-speech-sdk/echobot-running-on-localhost.png "EchoBot draait op localhost")](media/tutorial-voice-enable-your-bot-speech-sdk/echobot-running-on-localhost.png#lightbox)

### <a name="test-the-bot-sample-with-the-bot-framework-emulator"></a>Test het botmonster met de Bot Framework Emulator

De [Bot Framework Emulator](https://github.com/microsoft/botframework-emulator) is een desktop app waarmee bot ontwikkelaars om te testen en debuggen hun bots lokaal of op afstand door een tunnel. De Emulator ondersteunt getypte tekst als de invoer (geen stem). De bot zal reageren met tekst. Volg deze stappen om de Bot Framework Emulator te gebruiken om uw Echo Bot lokaal te testen, met tekstinvoer en tekstuitvoer. Nadat we de bot hebben geïmplementeerd in Azure, testen we deze met spraakinvoer en spraakuitvoer.

1. Installeer de [Bot Framework Emulator](https://github.com/Microsoft/BotFramework-Emulator/releases/latest) versie 4.3.0 of hoger
2. Start de Bot Framework Emulator en open je bot:
   * **Bestand** -> **Open Bot**.
3. Voer de URL voor uw bot in. Bijvoorbeeld:

   ```
   http://localhost:3978/api/messages
   ```
   en druk op "Connect".
4. De bot moet onmiddellijk begroeten u met "Hallo en welkom!" . Typ een sms-bericht en bevestig dat u een antwoord krijgt van de bot.
5. Dit is hoe een uitwisseling van communicatie met een Echo Bot-instantie eruit zou kunnen zien: [ ![bot-framework-emulator](media/tutorial-voice-enable-your-bot-speech-sdk/bot-framework-emulator.png "Bot Framework emulator")](media/tutorial-voice-enable-your-bot-speech-sdk/bot-framework-emulator.png#lightbox)

## <a name="deploy-your-bot-to-an-azure-app-service"></a>Uw bot implementeren in een Azure App-service

De volgende stap is het implementeren van de Echo Bot naar Azure. Er zijn een paar manieren om een bot te implementeren, maar in deze zelfstudie richten we ons op het publiceren rechtstreeks vanuit Visual Studio.

> [!NOTE]
> U ook een bot implementeren met behulp van de [Azure CLI-](https://docs.microsoft.com/azure/bot-service/bot-builder-deploy-az-cli) en [implementatiesjablonen.](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/directline-speech/csharp_dotnetcore/02.echo-bot/DeploymentTemplates)

1. Open vanuit Visual Studio de Echo-bot die is geconfigureerd voor gebruik met direct line-spraakkanaal:

   ```
   samples\csharp_dotnetcore\02.echo-bot\EchoBot.sln
   ```

1. Klik in de **Solution Explorer**met de rechtermuisknop op het **EchoBot-project** en selecteer **Publiceren...**
1. Er wordt een nieuw venster geopend met de titel **Een publicatiedoel kiezen.**
1. Selecteer **App Service** in de Azure **services** navigatie, selecteer Nieuw **maken**en klik op Profiel **maken**.
1. Wanneer het venster **App-service maken** wordt weergegeven:
   * Klik **op Een account toevoegen**en meld u aan met uw Azure-accountreferenties. Als u al bent aangemeld, selecteert u het gewenste account in de vervolgkeuzelijst.
   * Voor de **appnaam**moet u een wereldwijd unieke naam voor uw Bot invoeren. Deze naam wordt gebruikt om een unieke bot-URL te maken. Er wordt een standaardwaarde ingevuld, inclusief datum en tijd (Bijvoorbeeld: 'EchoBot20190805125647'). U de standaardnaam voor deze zelfstudie gebruiken.
   * Voor **abonnement,** stel deze in op **Gratis proefversie**
   * Selecteer **SpeechEchoBotTutorial-ResourceGroup** voor **resourcegroep**
   * Selecteer **SpeechEchoBotTutorial-AppServicePlan** voor **hostingplan**
   * Voor **Application Insights**, laat als **Geen**
1. Klik **op Maken**
1. U ziet een succesbericht in Visual Studio dat er als volgt uitziet:

   ```
   Publish Succeeded.
   Web App was published successfully https://EchoBot20190805125647.azurewebsites.net/
   ```

1. Uw standaardbrowser moet een pagina openen en weergeven met de tekst: "Uw bot is klaar!".
1. Controleer op dit punt uw Resourcegroep **SpeechEchoBotTutorial-ResourceGroup** in de Azure-portal en controleer of er drie bronnen zijn:

| Name | Type  | Locatie |
|------|-------|----------|
| EchoBot20190805125647 | App Service | VS - west |
| SpeechechoBotTutorial-AppServicePlan | App Service-plan | VS - west |
| SpeechechoBotTutorial-Speech | Cognitive Services | VS - west |

## <a name="enable-web-sockets"></a>Websockets inschakelen

U moet een kleine configuratiewijziging aanbrengen, zodat uw bot met het Direct Line Speech-kanaal kan communiceren via websockets. Volg de volgende stappen om websockets in te schakelen:

1. Navigeer naar de [Azure-portal](https://portal.azure.com)en zoek uw App-service. De bron moet een naam krijgen die vergelijkbaar is met **EchoBot20190805125647** (uw unieke app-naam).
2. Klik in de navigatie **van Azure-services** onder **Instellingen**op **Configuratie**.
3. Selecteer het tabblad **Algemene instellingen.**
4. Zoek de schakelaar voor **websockets** en stel deze in **op Aan**.
5. Klik op **Opslaan**.

> [!TIP]
> U de besturingselementen boven aan de pagina Azure App Service gebruiken om de service te stoppen of opnieuw te starten. Dit kan handig zijn bij het oplossen van problemen.

## <a name="create-a-channel-registration"></a>Een kanaalregistratie maken

Nu u een Azure App-service hebt gemaakt om uw bot te hosten, is de volgende stap het maken van een **bot-kanaalregistratie.** Het maken van een kanaalregistratie is een voorwaarde voor het registreren van uw bot met Bot-Framework-kanalen, waaronder Direct Line Speech-kanaal.

> [!NOTE]
> Als je meer wilt weten over hoe bots kanalen benutten, zie [Een bot verbinden met kanalen.](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels?view=azure-bot-service-4.0)


1. <a href="https://ms.portal.azure.com/#create/Microsoft.BotServiceConnectivityGalleryPackage" target="_blank">Een azure bot-kanalenregistratie maken<span class="docon docon-navigate-external x-hidden-focus"></span></a>
2. U wordt gevraagd om informatie te verstrekken:
   * Voer voor **de handgreep van Bot** **SpraakechoBotTutorial-BotRegistration**in .
   * Selecteer Gratis **proefversie** **voor abonnement.**
   * Selecteer **SpeechEchoBotTutorial-ResourceGroup**voor **resourcegroep**.
   * Selecteer West **US**voor **Locatie**.
     * Selecteer **F0** **voor de prijsniveau**.
     * Voer voor **eindpunt Messaging**de URL voor `/api/messages` uw web-app in met het pad dat aan het einde is toegevoegd. Bijvoorbeeld: als uw wereldwijd unieke app-naam **EchoBot20190805125647**was, `https://EchoBot20190805125647.azurewebsites.net/api/messages/`zou uw eindpunt voor berichten zijn: .
     * Voor **toepassingsinzichten**u dit instellen op **Uit.** Zie [Botanalytics](https://docs.microsoft.com/azure/bot-service/bot-service-manage-analytics?view=azure-bot-service-4.0)voor meer informatie.
     * Automatisch **maken app-id en wachtwoord negeren**.
5. Klik onder aan het **formulier Voor registratie van botkanalen** op **Maken**.

Controleer op dit moment uw Resourcegroep **SpeechEchoBotTutorial-ResourceGroup** in de Azure-portal. Het moet nu vier bronnen laten zien:

| Name | Type  | Locatie |
|------|-------|----------|
| EchoBot20190805125647 | App Service | VS - west |
| SpeechechoBotTutorial-AppServicePlan | App Service-plan | VS - west |
| SpeechEchoBotTutorial-BotRegistratie | Registratie van botkanalen | Globale |
| SpeechechoBotTutorial-Speech | Cognitive Services | VS - west |

> [!IMPORTANT]
> De resource Registratie botkanalen toont de regio Globaal, ook al hebt u West US geselecteerd. Dit is normaal gedrag.

## <a name="register-the-direct-line-speech-channel"></a>Het kanaal Direct Line Speech registreren

Nu is het tijd om je bot te registreren met het Direct Line Speech kanaal. Dit kanaal wordt gebruikt om een verbinding te maken tussen uw echobot en een client-app die is samengesteld met de Speech SDK.

1. Zoek en open uw **SpeechEchoBotTutorial-BotRegistration-bron** in de [Azure-portal.](https://portal.azure.com)
1. Selecteer **Kanalen**in de navigatie **van Azure-services** .
   * Zoek naar **meer kanalen,** zoek en klik op **Direct Line Speech**.
   * Controleer de tekst op de pagina met de titel **Directe lijnspraak configureren**en vouw vervolgens het vervolgkeuzemenu uit met het label 'Cognitief serviceaccount'.
   * Selecteer de spraakbron die u eerder hebt gemaakt (bijvoorbeeld **SpeechEchoBotTutorial-Speech)** in het menu om uw bot te koppelen aan uw spraakabonnementssleutel.
   * Klik op **Opslaan**.

1. Klik in de **navigatie voor botbeheer** op **Instellingen**.
   * Schakel het selectievakje met het label **Streaming Endpoint inschakelen in**. Dit is nodig om een communicatieprotocol in te schakelen dat is gebouwd op websockets tussen uw bot en het Direct Line Speech-kanaal.
   * Klik op **Opslaan**.

> [!TIP]
> Zie Een bot verbinden met [Direct Line Speech](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech?view=azure-bot-service-4.0)als u meer wilt weten. Deze pagina bevat aanvullende informatie en bekende problemen.

## <a name="build-the-windows-voice-assistant-client"></a>De Windows Voice Assistant-client bouwen

In deze stap gaat u de Windows Voice Assistant-client bouwen. De client is een WpF-app (Windows Presentation Foundation) in C# die de [Spraak-SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk) gebruikt om de communicatie met uw bot te beheren met behulp van het Direct Line Speech-kanaal. Gebruik het om te communiceren met en test uw bot voordat u een aangepaste client-app schrijft.

De Windows Voice Assistant-client heeft een eenvoudige gebruikersinterface waarmee u de verbinding met uw bot configureren, het tekstgesprek bekijken, Bot-Framework-activiteiten in JSON-indeling bekijken en adaptieve kaarten weergeven. Het ondersteunt ook het gebruik van aangepaste zoekwoorden. U gebruikt deze client om met uw bot te spreken en een spraakreactie te ontvangen.

Voordat we verder gaan, moet u ervoor zorgen dat uw microfoon en luidsprekers zijn ingeschakeld en werken.

1. Navigeer naar de GitHub-opslagplaats voor de [Windows Voice Assistant-client](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/README.md).
2. Volg de instructies die zijn gegeven om de repository te klonen, het project te bouwen, de client te configureren en de client te starten.
3. Klik **op Opnieuw verbinding maken** en zorg ervoor dat u het bericht ziet Druk op de **microfoonknop of typ om met uw bot te gaan praten.**
4. Laten we het testen. Klik op de microfoonknop en spreek een paar woorden in het Engels. De herkende tekst wordt op dit moment weergegeven. Als je klaar bent met spreken, zal de bot antwoorden in zijn eigen stem, zeggen "echo" gevolgd door de erkende woorden.
5. U ook tekst gebruiken om met de bot te communiceren. Typ gewoon de tekst op de onderste balk. 

### <a name="troubleshooting-errors-in-windows-voice-assistant-client"></a>Fouten oplossen in Windows Voice Assistant-client

Als u een foutbericht ontvangt in het venster van de hoofdapp, gebruikt u deze tabel om de fout te identificeren en op te lossen:

| Fout | Wat moet u doen? |
|-------|----------------------|
|FoutverificatieMislukt: websocket-upgrade is mislukt met een verificatiefout (401). Controleren op de juiste abonnementssleutel (of autorisatietoken) en regionaam| Controleer op de pagina Instellingen van de app of u de toets Spraakabonnement en de regio correct hebt ingevoerd.<br>Zorg ervoor dat uw spraaksleutel en sleutelgebied correct zijn ingevoerd. |
|FoutverbindingMislukt: de verbinding is gesloten door de externe host. Foutcode: 1011. Foutgegevens: we konden geen verbinding maken met de bot voordat we een bericht verzenden | Controleer of u het selectievakje [Streaming eindpunt inschakelen](#register-the-direct-line-speech-channel) en/of [ingeschakelde **websockets** ](#enable-web-sockets) hebt ingeschakeld om aan te gaan.<br>Controleer of uw Azure App Service wordt uitgevoerd. Als dit het wel is, probeert u de App-service opnieuw op te starten.|
|FoutverbindingMislukt: de verbinding is gesloten door de externe host. Foutcode: 1011. Foutgegevens: statuscode van de respons geeft geen succes aan: 500 (InternalServerError)| Uw bot heeft een neurale stem opgegeven in het veld [Activiteitsspreek,](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) maar de Azure-regio die is gekoppeld aan uw spraakabonnementssleutel ondersteunt geen neurale stemmen. Zie [Standaard- en neurale stemmen.](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices)|
|FoutverbindingMislukt: de verbinding is gesloten door de externe host. Foutcode: 1000. Foutgegevens: maximale idleduur van de websocketverbinding (> 300000 ms)| Dit is een verwachte fout wanneer een verbinding met het kanaal langer dan vijf minuten open en inactief blijft. |

Zie [Spraakassistenten: veelgestelde vragen](faq-voice-assistants.md)als uw probleem niet in de tabel wordt opgelost.

### <a name="view-bot-activities"></a>Botactiviteiten weergeven

Elke bot verzendt **Activity** en ontvangt activiteitsberichten. In het **venster Activiteitslogboek** van Windows Voice Assistant-client ziet u tijdstempellogboeken bij elke activiteit die de client van de bot heeft ontvangen. U ook de activiteiten zien die de [`DialogServiceConnector.SendActivityAsync`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.sendactivityasync) client met de methode naar de bot heeft verzonden. Wanneer u een logboekitem selecteert, worden de details van de bijbehorende activiteit weergegeven als JSON.

Hier is een voorbeeld van een activiteit die de client heeft ontvangen:

```json
{
    "attachments":[],
    "channelData":{
        "conversationalAiData":{
             "requestInfo":{
                 "interactionId":"8d5cb416-73c3-476b-95fd-9358cbfaebfa",
                 "version":"0.2"
             }
         }
    },
    "channelId":"directlinespeech",
    "conversation":{
        "id":"129ebffe-772b-47f0-9812-7c5bfd4aca79",
        "isGroup":false
    },
    "entities":[],
    "from":{
        "id":"SpeechEchoBotTutorial-BotRegistration"
    },
    "id":"89841b4d-46ce-42de-9960-4fe4070c70cc",
    "inputHint":"acceptingInput",
    "recipient":{
        "id":"129ebffe-772b-47f0-9812-7c5bfd4aca79|0000"
    },
    "replyToId":"67c823b4-4c7a-4828-9d6e-0b84fd052869",
    "serviceUrl":"urn:botframework:websocket:directlinespeech",
    "speak":"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='en-US'><voice name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>Echo: Hello and welcome.</voice></speak>",
    "text":"Echo: Hello and welcome.",
    "timestamp":"2019-07-19T20:03:51.1939097Z",
    "type":"message"
}
```

Zie [velden in de activiteit](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md)voor meer informatie over wat er is geretourneerd in de JSON-uitvoer. Voor deze zelfstudie u zich richten op de velden [Tekst](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#text) en [Spreek.](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak)

### <a name="view-client-source-code-for-calls-to-the-speech-sdk"></a>Clientbroncode weergeven voor gesprekken met de Speech SDK

De Windows Voice Assistant Client maakt gebruik van het NuGet-pakket [Microsoft.CognitiveServices.Speech](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/), dat de Speech SDK bevat. Een goede plek om te beginnen met het controleren van de [`VoiceAssistantClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/MainWindow.xaml.cs)voorbeeldcode is de methode InitSpeechConnector() in het bestand, die deze twee Speech SDK-objecten maakt:
- [`DialogServiceConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconfig)- Voor configuratie-instellingen (bijvoorbeeld spraakabonnementssleutel, sleutelgebied)
- [`DialogServiceConnector`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.-ctor)- Het beheren van de kanaalverbinding en clientabonnementsgebeurtenissen voor het verwerken van herkende spraak- en botreacties.

## <a name="add-custom-keyword-activation"></a>Aangepaste trefwoordactivering toevoegen

De Speech SDK ondersteunt aangepaste trefwoordactivering. Net als bij 'Hey Cortana' voor de Assistent van Microsoft, u een app schrijven die continu luistert naar een zoekwoord naar keuze. Houd er rekening mee dat een trefwoord één woord of een woordzin van meerdere woorden kan zijn.

> [!NOTE]
> Het term *trefwoord* wordt vaak door elkaar gebruikt met de term *wake word,* en u beide gebruikt in Microsoft-documentatie.

Trefwoorddetectie gebeurt in de client-app. Als u een trefwoord gebruikt, wordt audio alleen gestreamd naar het kanaal Direct Line Speech als het zoekwoord wordt gedetecteerd. Het Direct Line Speech-kanaal bevat een component genaamd *keyword verification (KWV),* die complexere verwerking en verwerking in de cloud doet om te controleren of het gekozen zoekwoord zich aan het begin van de audiostream bevindt. Als de verificatie van het sleutelwoord slaagt, communiceert het kanaal met de bot.

Volg deze stappen om een zoekwoordmodel te maken, configureer de Windows Voice Assistant-client om dit model te gebruiken en test het ten slotte met uw bot.

1. Volg deze instructies om een aangepast zoekwoord te [maken met behulp van de spraakservice](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws).
2. Rits het modelbestand dat u in de vorige stap hebt gedownload, uit. Het moet worden genoemd naar uw zoekwoord. Je bent op zoek `kws.table`naar een bestand met de naam.
3. Zoek in de Windows Voice Assistant-client het menu **Instellingen** (zoek naar het tandwielpictogram rechtsboven). Zoek **het bestandspad model** en voer `kws.table` vanaf stap 2 de volledige padnaam voor het bestand in.
4. Schakel het selectievakje met het label **Ingeschakeld in.** U ziet dit bericht naast het selectievakje :"Luistert naar het zoekwoord bij de volgende verbinding". Als u het verkeerde bestand of een ongeldig pad hebt opgegeven, ziet u een foutbericht.
5. Voer de toets van uw **spraakabonnement** **in**en klik op **OK** om het menu **Instellingen** te sluiten.
6. Klik **op Opnieuw verbinden**. U ziet een bericht met de tekst: "Nieuw gesprek gestart - typ, druk op de microfoonknop of zeg het trefwoord". De app luistert nu continu.
7. Spreek elke zin die begint met uw trefwoord. Bijvoorbeeld:**{uw trefwoord}**, hoe laat is het?". U hoeft niet te pauzeren na het uitspreken van het trefwoord. Als je klaar bent, gebeuren er twee dingen:
   * U ziet een transcriptie van wat u sprak
   * Kort daarna hoor je de reactie van de bot
8. Blijf experimenteren met de drie invoertypen die uw bot ondersteunt:
   * Getypte tekst op de onderste balk
   * Druk op het microfoonpictogram en spreekt
   * Een zin zeggen die begint met uw trefwoord

### <a name="view-the-source-code-that-enables-keyword"></a>De broncode weergeven waarmee trefwoord wordt instaat

Bekijk in de broncode van de Windows Voice Assistant Client deze bestanden om de code te bekijken die wordt gebruikt om trefwoorddetectie in te schakelen:

1. [`VoiceAssistantClient\Models.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/Models.cs)bevat een aanroep naar [`KeywordRecognitionModel.fromFile()`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/keywordrecognitionmodel?view=azure-node-latest#fromfile-string-)de Speech SDK-methode, die wordt gebruikt om het model te instantiëren vanuit een lokaal bestand op schijf.
1. [`VoiceAssistantClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/MainWindow.xaml.cs)bevat een call-to-Speech SDK-methode, [`DialogServiceConnector.StartKeywordRecognitionAsync()`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.startkeywordrecognitionasync)die continue trefwoorddetectie activeert.

## <a name="optional-change-the-language-and-bot-voice"></a>(Optioneel) De taal- en botstem wijzigen

De bot die u hebt gemaakt, luistert naar en reageert in het Engels, met een standaard Tekst-naar-spraakstem in Het Engels. U bent echter niet beperkt tot het gebruik van Engels of een standaardstem. In deze sectie leert u hoe u de taal wijzigen waaruw bot naar luistert en waarop u reageert. Je leert ook hoe je een andere stem voor die taal selecteren.

### <a name="change-the-language"></a>De taal wijzigen

U kiezen uit een van de talen die in de [spraak-naar-teksttabel](language-support.md#speech-to-text) worden genoemd. In het onderstaande voorbeeld veranderen we de taal in het Duits.

1. Open de Windows Voice Assistant Client-app, klik op de instellingenknop (pictogram rechtsboven en voer `de-de` het veld Taal in (dit is de landinstellingswaarde die wordt genoemd in de tabel [spraak-naar-tekst).](language-support.md#speech-to-text) Hiermee wordt de gesproken taal herkend, met `en-us`als gevolg dat de standaardwaarde wordt overschrijven. Dit instrueert ook Direct Line Speech kanaal om een standaard Duitse stem te gebruiken voor de Bot antwoord.
2. Sluit de pagina Instellingen en klik op de knop Opnieuw verbinden om een nieuwe verbinding met uw echobot tot stand te brengen.
3. Klik op de microfoonknop en zeg een zin in het Duits. U ziet de herkende tekst en de echobot die met de standaard Duitse stem antwoordt.

### <a name="change-the-default-bot-voice"></a>De standaardbotstem wijzigen

Het selecteren van de tekst-naar-spraak stem en het regelen van de uitspraak kan worden gedaan als de Bot geeft het antwoord in de vorm van een [Spraaksynthese Markup Language](speech-synthesis-markup.md) (SSML) in plaats van eenvoudige tekst. De echo bot maakt geen gebruik van SSML, maar we kunnen gemakkelijk wijzigen van de code om dat te doen. In het onderstaande voorbeeld voegen we SSML toe aan het echobot-antwoord, zodat de Duitse stem Stefan Apollo (een mannenstem) wordt gebruikt in plaats van de standaard vrouwenstem. Zie de lijst met [standaardstemmen](language-support.md#standard-voices) en [neurale stemmen](language-support.md#neural-voices) die worden ondersteund voor uw taal.

1. Laten we beginnen `samples\csharp_dotnetcore\02.echo-bot\echo-bot.cs`met het openen.
2. Zoek deze twee regels:
    ```csharp
    var replyText = $"Echo: {turnContext.Activity.Text}";
    await turnContext.SendActivityAsync(MessageFactory.Text(replyText, replyText), cancellationToken);
    ```
3. Vervang ze door:
    ```csharp
    var replyText = $"Echo: {turnContext.Activity.Text}";
    var replySpeak = @"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='de-DE'>
                    <voice name='Microsoft Server Speech Text to Speech Voice (de-DE, Stefan, Apollo)'>" +
                    $"{replyText}" + "</voice></speak>";
    await turnContext.SendActivityAsync(MessageFactory.Text(replyText, replySpeak), cancellationToken);
    ```
4. Bouw uw oplossing in Visual Studio en los eventuele buildfouten op.

In het tweede argument in de methode 'MessageFactory.Text' wordt het [veld Activiteitsspreekveld](https://github.com/Microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) in het botantwoord ingesteld. Met de bovenstaande wijziging is deze vervangen van eenvoudige tekst naar SSML om een niet-standaard Duitse stem op te geven.

### <a name="redeploy-your-bot"></a>Uw bot opnieuw implementeren

Nu u de benodigde wijziging in de bot hebt aangebracht, is de volgende stap om deze opnieuw te publiceren naar uw Azure App Service en deze uit te proberen:

1. Klik in het venster Solution Explorer met de rechtermuisknop op het **EchoBot-project** en selecteer **Publiceren**.
2. Uw vorige implementatieconfiguratie is al als standaardgeladen. Klik op **Publiceren** naast **EchoBot20190805125647 - Webdeploy**.
3. Het bericht **Dat is gestart met publiceren** wordt weergegeven in het uitvoervenster van Visual Studio en er wordt een webpagina gestart met het bericht 'Uw bot is gereed!'.
4. Open de Windows Voice Assistant Client-app, klik op de instellingenknop (pictogram `de-de` rechtsboven) en zorg ervoor dat u nog steeds in het veld Taal hebt.
5. Volg de instructies in [Build the Windows Voice Assistant Client](#build-the-windows-voice-assistant-client) om opnieuw verbinding te maken met uw nieuw geïmplementeerde bot, spreek in de nieuwe taal en hoor u in die taal antwoorden met de nieuwe stem.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de echo-bot die in deze zelfstudie is geïmplementeerd, niet verder wilt gebruiken, u deze en alle bijbehorende Azure-resources verwijderen door simpelweg de Azure Resource-groep **SpeechEchoBotTutorial-ResourceGroup**te verwijderen.

1. Klik in de [Azure-portal](https://portal.azure.com)op **Resourcegroepen** uit de **Azure-services-navigatie.**
2. Zoek de brongroep met de naam **SpeechEchoBotTutorial-ResourceGroup**. Klik op de drie puntjes (...).
3. Selecteer **Resourcegroep verwijderen**.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Bouw je eigen client-app met de Speech SDK](quickstart-voice-assistant-csharp-uwp.md)

## <a name="see-also"></a>Zie ook

* Implementeren in een [Azure-regio bij u in de buurt](https://azure.microsoft.com/global-infrastructure/locations/) om de verbetering van de reactietijd van de bot te zien
* Implementeren in een [Azure-regio die neurale TTS-stemmen van hoge kwaliteit ondersteunt](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices)
* Prijzen in verband met Direct Line Speech-kanaal:
  * [Bot Service prijzen](https://azure.microsoft.com/pricing/details/bot-service/)
  * [Speech Service](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)
* Het bouwen en implementeren van uw eigen voice-enabled bot:
  * Bouw een [Bot-Framework bot.](https://dev.botframework.com/) Registreer het met [Direct Line Speech-kanaal](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech?view=azure-bot-service-4.0) en [pas je bot aan voor spraak](https://docs.microsoft.com/azure/bot-service/directline-speech-bot?view=azure-bot-service-4.0)
  * Ontdek bestaande [Bot-Framework-oplossingen:](https://microsoft.github.io/botframework-solutions/index)Bouw een [virtuele assistent](https://microsoft.github.io/botframework-solutions/overview/virtual-assistant-solution/) en [breid deze uit naar Direct Line Speech](https://microsoft.github.io/botframework-solutions/clients-and-channels/tutorials/enable-speech/1-intro/)
