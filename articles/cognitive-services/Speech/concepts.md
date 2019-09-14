---
title: Bing Speech concepten | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Basis concepten die worden gebruikt in de speech-service van micro soft.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: fba1bbdeaf68bdd45524b336011627a27cd024da
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965715"
---
# <a name="basic-concepts"></a>Basisbegrippen

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Op deze pagina worden enkele basis concepten van micro soft speech recognition service beschreven. U wordt aangeraden deze pagina te lezen voordat u micro soft Speech Recognition API in uw toepassing gebruikt.

## <a name="understanding-speech-recognition"></a>Spraak herkenning

Als dit de eerste keer is dat u een toepassing met spraak herkenning maakt, of als dit de eerste keer is dat u spraak mogelijkheden toevoegt aan een bestaande toepassing, kunt u aan de slag met deze sectie. Als u al enige ervaring met spraak toepassingen hebt, kunt u ervoor kiezen om deze sectie alleen te skim of u kunt deze helemaal overs Laan als u een oude hand bij spraak hebt en u recht wilt krijgen op de details van het protocol.

### <a name="audio-streams"></a>Audio gegevensstromen

De basis concepten van spraak is de *Audio stroom*. In tegens telling tot een toetsaanslag, die op een enkel moment plaatsvindt en één stukje informatie bevat, wordt een gesp roken aanvraag verdeeld over honderden milliseconden en bevat het een groot aantal kB aan informatie. De duur van gesp roken uitingen biedt ontwikkel aars een gestroomlijnde en elegante spraak ervaring voor hun toepassing. Computers en algoritmen van vandaag voeren spraak transcriptie in ongeveer de helft van de duur van de utterance, zodat een utterance van 2 seconden kan worden getranscribeerd in ongeveer 1 seconde, maar elke toepassing die een vertraging van 1 seconde bij de verwerking van de gebruiker heeft, is noch gestroomlijnd noch elegant.

Gelukkig zijn er manieren om de transcriptie-tijd te verbergen door transcriptie uit te voeren op een deel van de utterance terwijl de gebruiker een ander deel spreekt. Bijvoorbeeld: door een utterance van 1 seconde te splitsen in tien delen van 100 milliseconden en door transcriptie op elk segment op een andere manier uit te voeren, kan meer dan 450 van het totale aantal 500 milliseconden dat vereist is voor transcriptie, verborgen zijn, zodat de gebruiker niet op de hoogte is transcriptie wordt uitgevoerd terwijl ze spreken. Houd er rekening mee dat de service transcriptie op de vorige 100 milliseconden audio tijdens het nadenken van dit voor beeld, terwijl de gebruiker de volgende 100 uitspreekt. als de gebruiker niet meer reageert, hoeft de service maar ongeveer 100 te transcriberen de milliseconden van de audio om een resultaat te verkrijgen.

Om deze gebruikers ervaring te verzorgen, worden gesp roken audio gegevens verzameld in segmenten en getranscribeerd als de gebruiker spreekt. Deze audio fragmenten worden collectief van de *Audio stroom*en het proces voor het verzenden van deze audio segmenten naar de service wordt *Audio streaming genoemd.* Audio streaming is een belang rijk onderdeel van elke toepassing met spraak functionaliteit; het afstemmen van de segment grootte en het optimaliseren van de implementatie van de streaming zijn een aantal van de meest consequente manieren om de gebruikers ervaring van uw toepassing te verbeteren.

### <a name="microphones"></a>Microfoon

Mensen verwerken geluid met hun oren, maar hardware gebruiken microfoons. Als u spraak in een toepassing wilt inschakelen, moet u integreren met de microfoon die de audio stroom voor uw toepassing levert.

De Api's voor uw microfoon moeten u in staat stellen om audio bytes van de microfoon te starten en te ontvangen. U moet bepalen op welke gebruikers acties de microfoon wordt geactiveerd om te Luis teren naar spraak. U kunt ervoor kiezen om een knop op het begin van het Luis teren te activeren, of u kunt ervoor kiezen om een *belang rijk woord* te hebben of te laten *ontwaken woord* Spotter altijd Luis teren naar de microfoon en de uitvoer van die module gebruiken om het verzenden van audio naar micro soft speech te activeren Service.

### <a name="end-of-speech"></a>Einde van spraak

*Wanneer* een spreker is *gestopt* , is het niet meer voldoende voor mensen, maar is er een moeilijkere probleem buiten de omstandigheden van het laboratorium. Het is niet voldoende om simpelweg na een utterance te zoeken naar zuivere stilte, omdat er vaak veel ruis in de omgeving is om dingen te bemoeilijken. De micro soft Speech-Service biedt een uitstekende taak om snel te detecteren wanneer een gebruiker niet meer heeft gereageerd en de service kan de toepassing van dit feit op de hoogte stellen, maar deze indeling betekent dat uw toepassing de laatste is om te weten wanneer de gebruiker stopt met het spreken. Dit is niet helemaal hetzelfde als andere vormen van invoer waarbij uw toepassing het *eerst* is om te weten wanneer de invoer van de gebruiker begint *en* eindigt.

### <a name="asynchronous-service-responses"></a>Asynchrone Service Reacties

Het feit dat uw toepassing op de hoogte moet worden gesteld wanneer gebruikers invoer is voltooid, kent geen prestatie straffen of programmeer problemen voor uw toepassing, maar het is wel vereist dat u van de invoer aanvraag een andere vraag hebt over spraak aanvragen/ antwoord patronen waarmee u bekend bent. Omdat uw toepassing niet bekend is als de gebruiker niet meer reageert, moet uw toepassing de audio naar de service blijven streamen tegelijk en asynchroon wachten op een reactie van de service. Dit patroon is in tegens telling tot andere protocollen voor aanvraag/antwoord, zoals HTTP. In deze protocollen moet u een aanvraag volt ooien voordat u een antwoord ontvangt. in het micro soft Speech Service-Protocol ontvangt u antwoorden *terwijl u nog steeds audio streamt voor de aanvraag*.

> [!NOTE]
> Deze functie wordt niet ondersteund bij het gebruik van speech HTTP REST API.

### <a name="turns"></a>Wordt

Speech is een drager van informatie. Wanneer u spreekt, probeert u gegevens over uw eigendom over te brengen naar iemand die op die informatie luistert. Bij het overbrengen van informatie gaat u doorgaans gesp roken en Luis teren. Op dezelfde manier communiceert uw spraak toepassing met gebruikers door te Luis teren en te reageren, hoewel uw toepassing meestal het meeste luistert. De gesp roken invoer van de gebruiker en de service respons op deze invoer wordt een *Turn*genoemd. Er wordt een *Turn* -bewerking gestart wanneer de gebruiker spreekt en eindigt wanneer uw toepassing de verwerking van het antwoord op de spraak service heeft voltooid.

### <a name="telemetry"></a>Telemetrie

Het maken van een apparaat of toepassing met spraak mogelijkheid kan lastig zijn, zelfs voor ervaren ontwikkel aars. Op streams gebaseerde protocollen lijken meestal in eerste oogopslag en belang rijke details, zoals stilte detectie, kunnen volledig nieuw zijn. Omdat er zoveel berichten moeten worden verzonden en ontvangen om één aanvraag/antwoord paar te kunnen volt ooien *, is het* belang rijk om volledige en nauw keurige gegevens over deze berichten te verzamelen. Het micro soft Speech Service-Protocol voorziet in het verzamelen van deze gegevens. U moet ervoor zorgen dat de vereiste gegevens zo nauw keurig mogelijk worden verstrekt. door volledige en nauw keurige gegevens op te geven, kunt u zelf aan de slag met het micro soft Speech Service-team bij het oplossen van problemen met de client implementatie. de kwaliteit van de telemetriegegevens die u hebt verzameld, is essentieel voor het probleem bepaling.

> [!NOTE]
> Deze functie wordt niet ondersteund bij het gebruik van spraak herkenning REST API.

### <a name="speech-application-states"></a>Status van spraak toepassingen

De stappen die u moet nemen om spraak invoer in uw toepassing in te scha kelen, zijn iets anders dan de stappen voor andere invoer vormen, zoals muis klikken of tikken. U moet bijhouden wanneer uw toepassing naar de microfoon luistert en gegevens verzendt naar de speech-service, wanneer deze wacht op een reactie van de service en wanneer deze in een niet-actieve status is. De relatie tussen deze statussen wordt weer gegeven in het onderstaande diagram.

![Status diagram van spraak toepassing](Images/speech-application-state-diagram.png)

Omdat de micro soft Speech-Service deel uitmaakt van een deel van de statussen, definieert het Service-Protocol berichten die de overgang van uw toepassing tussen statussen helpen. Uw toepassing moet deze protocol berichten interpreteren en hierop reageren om de status van de spraak toepassingen bij te houden en te beheren.

## <a name="using-the-speech-recognition-service-from-your-apps"></a>De service voor spraak herkenning gebruiken vanuit uw apps

Micro soft speech recognition service biedt ontwikkel aars twee manieren om spraak toe te voegen aan hun apps.

- [Rest-api's](GetStarted/GetStartedREST.md): Ontwikkel aars kunnen HTTP-aanroepen van hun apps naar de service gebruiken voor spraak herkenning.
- [Client bibliotheken](GetStarted/GetStartedClientLibraries.md): Ontwikkel aars kunnen voor geavanceerde functies micro soft speech client-bibliotheken downloaden en een koppeling maken naar hun apps.  De-client bibliotheken zijn beschikbaar op verschillende platformen (Windows, Android, iOS) met behulp van verschillende talen (C#Java, java script, ObjectiveC).

| Gebruiksvoorbeelden | [REST API's](GetStarted/GetStartedREST.md) | [Client bibliotheken](GetStarted/GetStartedClientLibraries.md) |
|-----|-----|-----|
| Een korte gesp roken audio converteren, bijvoorbeeld opdrachten (audio lengte < 15 s) zonder tussenliggende resultaten | Ja | Ja |
| Een lange audio converteren (> 15 s) | Nee | Ja |
| Stream audio met de gewenste tussentijdse resultaten | Nee | Ja |
| Inzicht in de tekst die is geconverteerd van audio met behulp van LUIS | Nee | Ja |

 Als uw taal of platform nog geen SDK heeft, kunt u uw eigen implementatie maken op basis van de [protocol documentatie](API-Reference-REST/websocketprotocol.md).

## <a name="recognition-modes"></a>Herkennings modi

Er zijn drie modi voor herkenning: `interactive`, `conversation`en `dictation`. De herkennings modus past spraak herkenning aan op basis van hoe de gebruikers waarschijnlijk spreken. Kies de juiste herkennings modus voor uw toepassing.

> [!NOTE]
> Herkennings modi kunnen verschillende gedrag hebben in het REST-protocol dan in het WebSocket-protocol. De REST API biedt bijvoorbeeld geen ondersteuning voor doorlopende herkenning, zelfs in de modus voor conversaties of dicteren.
> [!NOTE]
> Deze modi zijn van toepassing wanneer u direct het REST-of WebSocket-protocol gebruikt. De- [client bibliotheken](GetStarted/GetStartedClientLibraries.md) gebruiken verschillende para meters om de herkennings modus op te geven. Zie de client bibliotheek van uw keuze voor meer informatie.

De micro soft speech-service retourneert slechts één resultaat van een herkennings woordgroep voor alle herkennings modi. Er geldt een limiet van 15 seconden voor één utterance.

### <a name="interactive-mode"></a>Interactieve modus

In `interactive` de modus maakt een gebruiker korte aanvragen en verwacht de toepassing een actie uit te voeren als reactie.

De volgende kenmerken zijn typische toepassingen in de interactieve modus:

- Gebruikers weten dat ze op een machine en niet naar een ander personeel spreken.
- Gebruikers van de toepassing weten wat ze willen zeggen, op basis van wat ze nodig hebben voor de toepassing.
- Uitingen duurt doorgaans ongeveer 2-3 seconden.

### <a name="conversation-mode"></a>Conversatie modus

In `conversation` de modus worden gebruikers gecommuniceerd met een Human-To-Human-conversatie.

De volgende kenmerken zijn een typische toepassing voor de conversatie modus:

- Gebruikers weten dat ze met een andere persoon praten.
- Spraak herkenning verbetert de menselijke gesp rekken door een of beide deel nemers toe te staan om de gesp roken tekst te zien.
- Gebruikers plannen niet altijd wat ze willen zeggen.
- Gebruikers gebruiken vaak slang en andere informele spraak.

### <a name="dictation-mode"></a>Dicteer modus

In `dictation` de modus worden gebruikers langer uitingen naar de toepassing geciteerd voor verdere verwerking.

De volgende kenmerken zijn typische toepassingen van de dicteer modus:

- Gebruikers weten dat ze met een computer praten.
- Gebruikers worden weer gegeven met de tekst resultaten van spraak herkenning.
- Gebruikers plannen vaak wat ze willen zeggen en gebruiken meer formele taal.
- Gebruikers gebruiken volledige zinnen die de afgelopen 5-8 seconden zijn.

> [!NOTE]
> In dicteer modus en conversatie modi retourneert de micro soft speech-service geen gedeeltelijke resultaten. In plaats daarvan retourneert de service stabiele frase resultaten na stilte grenzen in de audio stroom. Micro soft kan het spraak protocol verbeteren om de gebruikers ervaring te verbeteren in deze modi voor continue herkenning.

## <a name="recognition-languages"></a>Herkennings talen

De *herkennings taal* geeft de taal op die de gebruiker van de toepassing spreekt. Geef de *herkennings taal* op met de para meter voor de *taal* -URL-query voor de verbinding. De waarde van de para meter voor de *taal* query maakt gebruik van de IETF-taal code [bcp 47](https://en.wikipedia.org/wiki/IETF_language_tag)en **moet** een van de talen zijn die worden ondersteund door de spraakherkennings-API. De volledige lijst met talen die door de spraak service worden ondersteund, vindt u in de talen die door de pagina worden [ondersteund](API-Reference-REST/supportedlanguages.md).

De micro soft Speech-Service weigert ongeldige verbindings aanvragen door `HTTP 400 Bad Request` een antwoord weer te geven. Een ongeldige aanvraag is een van de volgende:

- Bevat geen waarde voor de para meter voor *taal* query's.
- Bevat een *taal* query parameter die een onjuiste indeling heeft.
- Bevat een *taal* query parameter die niet een van de ondersteunings talen is.

U kunt kiezen voor het bouwen van een toepassing die ondersteuning biedt voor een of alle talen die worden ondersteund door de service.

### <a name="example"></a>Voorbeeld

In het volgende voor beeld maakt een toepassing gebruik van de modus *conversatie* spraak herkenning voor een Amerikaans-Engelse spreker.

```HTTP
https://speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

## <a name="transcription-responses"></a>Transcriptie-antwoorden

De transcriptie-antwoorden retour neren de geconverteerde tekst van audio naar clients. Een transcriptie-antwoord bevat de volgende velden:

- `RecognitionStatus`Hiermee geeft u de status van de herkenning op. De mogelijke waarden worden gegeven in de onderstaande tabel.

| Status | Description |
| ------------- | ---------------- |
| Geslaagd | De herkenning is geslaagd en het veld weergave tekst is aanwezig |
| Geen overeenkomst | Spraak is gedetecteerd in de audiostream, maar er zijn geen woorden uit de doeltaal zijn afgestemd. Zie [herkennings status niet overeenkomen (#nomatch-herkenning-status) voor meer informatie  |
| InitialSilenceTimeout | Het begin van de audio stroom bevat alleen stilte en er is een time-out opgetreden voor de service tijdens het wachten op spraak |
| BabbleTimeout | Het begin van de audio stroom bevat alleen ruis en er is een time-out opgetreden voor de service tijdens het wachten op spraak |
| Fout | De herkennings service heeft een interne fout aangetroffen en kan niet worden voortgezet |

- `DisplayText`de herkende woord groep na het hoofdletter gebruik, interpunctie en inverse-tekst-normalisatie is toegepast en het scheld is gemaskerd met sterretjes. Het veld weergave tekst is *alleen* aanwezig als `RecognitionStatus` het veld de waarde `Success`bevat.

- `Offset`Hiermee geeft u de verschuiving (in 100 nano seconden-eenheden) op waarmee de woord groep is herkend, ten opzichte van het begin van de audio stroom.

- `Duration`Hiermee geeft u de duur (in 100-nano seconden eenheden) van deze spraak woordgroep op.

Een transcriptie-antwoord retourneert indien gewenst meer informatie. Zie [uitvoer indeling](#output-format) voor het retour neren van meer gedetailleerde uitvoer.

Micro soft Speech Service ondersteunt extra transcriptie-processen, zoals het toevoegen van hoofd letters en interpunctie, het maskeren van scheld woorden en het normaliseren van tekst naar algemene formulieren. Als een gebruiker bijvoorbeeld een zin spreekt die wordt weer gegeven met de woorden ' herinner mij aan het kopen van zes iPhones ', wordt door de speech services van micro soft de vertranscribde tekst weer gegeven om 6 iPhones aan te schaffen. Het proces waarmee het woord "zes" naar het getal "6" wordt geconverteerd, wordt *inverse tekst normalisatie* (*ITN* for Short) genoemd.

### <a name="nomatch-recognition-status"></a>Herkennings status niet overeenkomst

Het transcriptie-antwoord `NoMatch` wordt `RecognitionStatus` geretourneerd in wanneer de micro soft Speech-Service spraak detecteert in de audio stroom, maar die spraak niet kan overeenkomen met de taal grammatica die voor de aanvraag wordt gebruikt. Een voor waarde voor geen *overeenkomst* kan bijvoorbeeld optreden als een gebruiker iets in het Duits zegt wanneer de herkenner het Engels als de gesp roken taal verwacht. Het Golf patroon van de utterance geeft aan dat de aanwezigheid van menselijke spraak, maar geen van de woorden die worden gesp roken overeenkomt met het Amerikaans Engels lexicon dat wordt gebruikt door de herkenner.

Een andere voor waarde voor geen *overeenkomst* treedt op wanneer het herkennings algoritme geen nauw keurige overeenkomst kan vinden voor de geluiden die in de audio stroom zijn opgenomen. Als dit probleem zich voordoet, kan de micro soft speech-service *spraak maken. hypo these* -berichten die *hypothetische tekst* bevatten, maar die een *Speech* -bericht produceert waarin de *RecognitionStatus* niet *overeenkomt* . Dit probleem is normaal; u moet geen veronderstellingen doen over de nauw keurigheid of de betrouw baarheid van de tekst in het bericht *Speech. hypo these* . Bovendien moet u niet aannemen dat de micro soft speech-service *spraak maakt. hypo these* berichten die de service kan maken van een *spraak. woordgroepen* bericht met *RecognitionStatus* *geslaagd*.

## <a name="output-format"></a>Uitvoerindeling

Micro soft Speech Service kan diverse Payload-indelingen in transcriptie-antwoorden retour neren. Alle payloads zijn JSON-structuren.

U kunt de indeling van de woordgroepen-resultaten `format` bepalen door de URL-query parameter op te geven. De service retourneert `simple` standaard resultaten.

| Indeling | Description |
|-----|-----|
| `simple` | Een vereenvoudigd woordgroepen resultaat dat de herkennings status en de herkende tekst in een weergave formulier bevat. |
| `detailed` | Een herkennings status en N-beste lijst met frase resultaten waarbij elk woordgroepen resultaat alle vier herkennings formulieren en een betrouwbaarheids Score bevat. |

De `detailed` notatie bevat [N-beste waarden](#n-best-values) `RecognitionStatus`, naast, `Offset`en `duration`, in de reactie.

### <a name="n-best-values"></a>N-beste waarden

Listeners, of mensen of machines, nooit zeker kunnen zijn dat ze *precies* hebben gehoord wat er is gesp roken. Een listener kan een *kans* alleen toewijzen aan een bepaalde interpretatie van een utterance.

In normale omstandigheden hebben mensen een hoge kans op het herkennen van de woorden die ze hebben gesp roken. Spraak-listeners op basis van machines streven ernaar vergelijk bare nauwkeurigheids niveaus te creëren en, onder de juiste voor waarden, [behaalt de pariteit met mensen](https://blogs.microsoft.com/next/2016/10/18/historic-achievement-microsoft-researchers-reach-human-parity-conversational-speech-recognition/#sm.001ykosqs14zte8qyxj2k9o28oz5v).

De algoritmen die worden gebruikt in spraak herkenning, verkennen alternatieve interpretaties van een utterance als onderdeel van de normale verwerking. Normaal gesp roken worden deze alternatieven verwijderd als het bewijs dat het voor deel is van één SIC-interpretatie. In minder dan optimale voor waarden is de spraak herkenner echter voltooid met een lijst met alternatieve mogelijke interpretaties. De eerste *n* alternatieven in deze lijst worden de *N-beste lijst*genoemd. Aan elk alternatief wordt een [betrouwbaarheids Score](#confidence)toegewezen. Betrouwbaarheids scores variëren van 0 tot 1. Een Score van 1 staat voor het hoogste betrouwbaarheids niveau. Een Score van 0 staat voor het laagste vertrouwens niveau.

> [!NOTE]
> Het aantal vermeldingen in de N-beste lijst varieert voor meerdere uitingen. Het aantal items kan variëren per opname van *hetzelfde* utterance. Deze variant is een natuurlijke en verwachte uitkomst van de Probabilistic aard van het algoritme voor spraak herkenning.

Elk item dat wordt geretourneerd in de N-beste lijst bevat

- `Confidence`, waarmee de [betrouwbaarheids scores](#confidence) van dit item worden aangeduid.
- `Lexical`, de [lexicale vorm](#lexical-form) van de herkende tekst.
- `ITN`: dit is de [ITN vorm](#itn-form) van de herkende tekst.
- `MaskedITN`: dit is de [gemaskerde ITN vorm](#masked-itn-form) van de herkende tekst.
- `Display`: dit is de [weergave vorm](#display-form) van de herkende tekst.

### Betrouwbaarheids scores<a id="confidence"></a>

Betrouwbaarheids scores zijn geïntegreerd in systemen voor spraak herkenning. De micro soft Speech-Service verkrijgt betrouwbaarheids scores van een *betrouw bare classificatie*. Micro soft traint de betrouw baarheid van een set functies die zijn ontworpen om maximally onderscheid te maken tussen juiste en onjuiste herkenning. Betrouwbaarheids scores worden geëvalueerd voor afzonderlijke woorden en volledige uitingen.

Als u ervoor kiest om de betrouwbaarheids scores te gebruiken die door de service worden geretourneerd, moet u rekening houden met het volgende gedrag:

- Betrouwbaarheids scores kunnen alleen in dezelfde herkennings modus en taal worden vergeleken. Vergelijkt geen scores tussen verschillende talen of andere herkennings modi. Een betrouwbaarheids Score in de interactieve herkennings modus heeft bijvoorbeeld *geen* correlatie met een betrouwbaarheids Score in de dicteer modus.
- Betrouwbaarheids scores worden het beste gebruikt voor een beperkte set uitingen. Er is natuurlijk een grote mate van variabiliteit in de scores voor een groot aantal uitingen.

Als u een waarde voor een betrouw bare score wilt gebruiken als *drempel* voor uw toepassing, gebruikt u spraak herkenning om de drempel waarden vast te leggen.

- Voer spraak herkenning uit op een representatief voor beeld van uitingen voor uw toepassing.
- Verzamel de betrouwbaarheids scores voor elke opname in de voor beeld-set.
- Baseer uw drempel waarde op een deel van het bereik van vertrouwen voor dat voor beeld.

Er is geen drempel waarde voor alle toepassingen. Een acceptabele betrouwbaarheids score voor een toepassing is mogelijk onaanvaardbaar voor een andere toepassing.

### <a name="lexical-form"></a>lexicale vorm

De lexicale vorm is de herkende tekst, precies de manier waarop deze is opgetreden in de utterance en zonder interpunctie of hoofdletter gebruik. De lexicale vorm van het adres ' 1020 Enter prise Way ' zou bijvoorbeeld 10 20 op de *ondernemings*wijze zijn, ervan uitgaande dat het op die manier werd gesp roken. De lexicale vorm van de zin ' herinner mij om 5 potloden aan te schaffen ' is een *herinnering dat ik vijf potloden wil kopen*.

De lexicale vorm is het meest geschikt voor toepassingen die niet-standaard tekst normalisatie moeten uitvoeren. De lexicale vorm is ook geschikt voor toepassingen waarvoor niet-verwerkte herkennings woorden nodig zijn.

Scheld woorden worden nooit gemaskeerd in de lexicale vorm.

### <a name="itn-form"></a>Formulier ITN

Tekst normalisatie is het proces van het converteren van tekst van een formulier naar een ander "canonieke" formulier. Het telefoon nummer ' 555-1212 ' kan bijvoorbeeld worden geconverteerd naar de canonieke vorm *5 5 5 1 2 1 2*. Met *inverse* tekst normalisatie (ITN) wordt dit proces omgekeerd, waarbij de woorden "5 5 5 1 2 1 2" worden geconverteerd naar de omgekeerde canonieke vorm *555-1212*. De ITN vorm van een herkennings resultaat bevat geen hoofdletter gebruik of interpunctie.

Het formulier ITN is het meest geschikt voor toepassingen die op de herkende tekst reageren. Een toepassing waarmee een gebruiker bijvoorbeeld zoek termen kan spreken en deze voor waarden in een webquery gebruikt, zou het formulier ITN gebruiken. Scheld woorden worden nooit gemaskeerd in het formulier ITN. Gebruik het *gemaskerde ITN-formulier*om grove woorden te maskeren.

### <a name="masked-itn-form"></a>Gemaskerd ITN-formulier

Omdat scheld woorden natuurlijk een deel uitmaakt van een gesp roken taal, herkent de micro soft Speech-Service dergelijke termen en zinsdelen wanneer ze worden gesp roken. Scheld is mogelijk niet geschikt voor alle toepassingen, met name voor de toepassingen met een beperkte, niet-volwassen gebruikers publiek.

Het gemaskerde ITN-formulier past Grove maskering toe op het inverse tekst normalisatie formulier. Stel de waarde van de waarde van de Gods-taal `masked`instelling in om grove woorden te maskeren. Als scheld woorden worden gemaskeerd, worden woorden die worden herkend als onderdeel van het woorden Lexicon van de taal, vervangen door sterretjes. Bijvoorbeeld: mij een herinnering geven om 5 * * * * * *pen aan te schaffen*. De gemaskeerde ITN vorm van een herkennings resultaat bevat geen hoofdletter gebruik of interpunctie.

> [!NOTE]
> Als de query parameter waarde voor scheld woorden is `raw`ingesteld op, is het gemaskerde ITN-formulier hetzelfde als het formulier ITN. Scheld woorden worden *niet* gemaskeerd.

### <a name="display-form"></a>Formulier weergeven

Lees tekens en het hoofdletter gebruik waarbij de nadruk ligt, waar u moet pauzeren, enzovoort, waardoor tekst gemakkelijker te begrijpen is. Het weergave formulier voegt interpunctie en kapitalisatie toe aan herkennings resultaten, waardoor het het meest geschikte formulier is voor toepassingen waarin de gesp roken tekst wordt weer gegeven.

Omdat het weergave formulier het gemaskeerde ITN-formulier uitbreidt, kunt u de waarde van `masked` de `raw`para meter voor de scheld instellen op of. Als de waarde is ingesteld op `raw`, omvatten de herkennings resultaten een ongodsheid die door de gebruiker wordt afgesp roken. Als de waarde is ingesteld op `masked`, worden woorden die zijn herkend als onderdeel van het woorden Lexicon van de taal vervangen door sterretjes.

### <a name="sample-responses"></a>Voorbeeld van reacties

Alle payloads zijn JSON-structuren.

De indeling van het resultaat `simple` van de zin:

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

De indeling van het resultaat `detailed` van de zin:

```json
{
  "RecognitionStatus": "Success",
  "Offset": "1236645672289",
  "Duration": "1236645672289",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "remind me to buy five pencils",
        "ITN" : "remind me to buy 5 pencils",
        "MaskedITN" : "remind me to buy 5 pencils",
        "Display" : "Remind me to buy 5 pencils.",
      },
      {
        "Confidence" : "0.54",
        "Lexical" : "rewind me to buy five pencils",
        "ITN" : "rewind me to buy 5 pencils",
        "MaskedITN" : "rewind me to buy 5 pencils",
        "Display" : "Rewind me to buy 5 pencils.",
      }
  ]
}
```

## <a name="profanity-handling-in-speech-recognition"></a>Scheld woorden: afhandeling in spraak herkenning

De micro soft speech-service herkent alle vormen van menselijke spraak, met inbegrip van woorden en zinsdelen die veel mensen zouden kunnen classificeren als ' scheld woorden '. Met de query parameter voor *scheld woorden* kunt u bepalen hoe de service ongebruikt. De service maskers zijn standaard in de *spraak. woordgroepen* resultaten en retour neren geen *spraak. hypo these* -berichten met scheld woorden.

| *Grove* waarde | Description |
| - | - |
| `masked` | Maskert woorden met sterretjes. Dit is de standaard instelling. |
| `removed` | Verwijdert ongepaste woorden uit alle resultaten. |
| `raw` | Herkent en retourneert Grove woorden in alle resultaten. |

### <a name="profanity-value-masked"></a>Grove waarde`Masked`

Als u grove woorden wilt maskeren, stelt u de query parameter voor *scheld* in op de waarde *gemaskeerd*. Wanneer de query parameter van de *Gods* dienst deze waarde heeft of niet is opgegeven voor een aanvraag, is de ongodsheid van de service *maskers* . De service voert maskering uit door de woorden in de herkennings resultaten te vervangen door sterretjes. Wanneer u de verwerking van ongepaste maskering opgeeft, retourneert de service geen *spraak. hypo these* berichten die woorden bevatten.

### <a name="profanity-value-removed"></a>Grove waarde`Removed`

Wanneer de query parameter van de *Gods taal* de waarde *verwijderd*heeft, verwijdert de service ongepaste woorden uit *spraak. woord groep* en *spraak. hypo these* -berichten. De resultaten zijn hetzelfde als *Wanneer woorden niet zijn gesp roken*.

#### <a name="profanity-only-utterances"></a>Ongepaste uitingen

Een gebruiker kan *alleen* woorden spreken wanneer een toepassing de service heeft geconfigureerd voor het verwijderen van scheld woorden. Als in dit scenario de herkennings modus *dicteert* of *conversatie*, retourneert de service geen *spraak. resultaat*. Als de herkennings modus *interactief*is, retourneert de service een *spraak. resultaat* met de status code niet *overeenkomen*.

### <a name="profanity-value-raw"></a>Grove waarde`Raw`

Wanneer de query-para meter voor *scheld woorden* de waarde *RAW*heeft, verwijdert of maskeert de service niet in de woorden *Speech. woordgroepen* of *Speech. hypo these* -berichten.
