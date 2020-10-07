---
title: Wat is de Speech-service?
titleSuffix: Azure Cognitive Services
description: De spraakservice combineert spraak-naar-tekst, tekst-naar-spraak en spraakomzetting in één Azure-abonnement. U kunt spraak eenvoudig aan uw toepassingen, hulpprogramma's en apparaten toevoegen met de Speech SDK, Speech Devices SDK of REST API's.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 08/20/2020
ms.author: trbye
ms.openlocfilehash: 2277c054be3e0649d48350b21eb9add2a1e52e8b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91282864"
---
# <a name="what-is-the-speech-service"></a>Wat is de Speech-service?

De spraakservice combineert spraak-naar-tekst, tekst-naar-spraak en spraakomzetting in één Azure-abonnement. U kunt spraak eenvoudig inschakelen voor uw toepassingen, hulpprogramma's en apparaten met de [Speech CLI](spx-overview.md), [Speech SDK](speech-sdk-reference.md), [Speech Devices SDK](https://aka.ms/sdsdk-quickstart), [Speech Studio](https://speech.microsoft.com/) of [REST API's](rest-apis.md).

> [!IMPORTANT]
> De spraakservice heeft de Bing Speech API en Translator Speech vervangen. Zie _Handleidingen > Migratie_ voor migratie-instructies.

De volgende onderdelen maken deel uit van de spraakservice. Gebruik de koppelingen in deze tabel voor meer informatie over veelvoorkomende gebruikssituaties voor elke functie of blader door de API-naslaginformatie.

| Service | Functie | Beschrijving | SDK | REST |
|---------|---------|-------------|-----|------|
| [Spraak-naar-tekst](speech-to-text.md) | Spraak-naar-tekst in realtime | Bij spraak-naar-tekst worden audiostromen of lokale bestanden in realtime naar tekst getranscribeerd of vertaald, zodat uw toepassingen, hulpprogramma's of apparaten deze kunnen gebruiken of weergeven. Gebruik spraak-naar-tekst met [Language Understanding (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/) om gebruikersintentie af te leiden uit getranscribeerde spraak en om te reageren op spraakopdrachten. | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Spraak-naar-tekst batchgewijs verwerken](batch-transcription.md) | Met Spraak-naar-tekst batchgewijs verwerken kunt u asynchrone spraak-naar-tekst-transcriptie doorvoeren van grote volumes spraakgegevens die zijn opgeslagen in Azure Blob Storage. Met Spraak-naar-tekst batchgewijs verwerken kunt u niet alleen spraak omzetten naar tekst, maar ook sprekersindexering en sentimentanalyses uitvoeren. | Nee | [Ja](https://westus.cris.ai/swagger/ui/index) |
| | [Gesprek via meerdere apparaten](multi-device-conversation.md) | Meerdere apparaten of clients in een gesprek verbinden om spraak- of tekstberichten te verzenden, met eenvoudige ondersteuning voor transcriptie en vertaling| Ja | Nee |
| | [Gesprekstranscriptie](conversation-transcription-service.md) | Hiermee schakelt u realtime spraakherkenning, sprekeridentificatie en sprekersindexering in. Het is ideaal voor het transcriberen van persoonlijke vergaderingen met de mogelijkheid om de sprekers te onderscheiden. | Ja | Nee |
| | [Aangepaste spraakmodellen maken](#customize-your-speech-experience) | Als u spraak-naar-tekst gebruikt voor herkenning en transcriptie in een unieke omgeving, kunt u aangepaste akoestische, taal- en uitspraakmodellen maken en trainen om te kunnen omgaan met omgevingslawaai of branchespecifieke woordenlijsten. | Nee | [Ja](https://westus.cris.ai/swagger/ui/index) |
| [Tekst naar spraak](text-to-speech.md) | Tekst naar spraak | Bij tekst-naar-spraak wordt invoertekst omgezet in menselijke spraak die is samengesteld met behulp van [SSML (Speech Synthesis Markup Language)](speech-synthesis-markup.md). Kies uit standaardstemmen en neurale stemmen (zie [Taalondersteuning](language-support.md)). | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Aangepaste stemmen maken](#customize-your-speech-experience) | Maak aangepaste spraakstijlen die uniek zijn voor uw merk of product. | Nee | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Speech Translation](speech-translation.md) | Spraakomzetting | Spraakomzetting maakt realtime omzetting van spraak in meerdere talen mogelijk voor uw toepassingen, hulpprogramma's en apparaten. Gebruik deze service voor het omzetten van spraak-naar-spraak en spraak-naar-tekst. | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | Nee |
| [Spraakassistenten](voice-assistants.md) | Spraakassistenten | Spraakassistenten die gebruikmaken van de spraakservice stellen ontwikkelaars in staat om natuurlijke, menselijke gespreksinterfaces te maken voor hun toepassingen en gebruikstoepassingen. De spraakassistentservice biedt een snelle, betrouwbare interactie tussen een apparaat en een assistentimplementatie die gebruikmaakt van het Direct Line Speech-kanaal van het Bot Framework of de geïntegreerde service voor het uitvoeren van aangepaste opdrachten (preview) voor het uitvoeren van taken. | [Ja](voice-assistants.md) | Nee |
| [Speaker Recognition](speaker-recognition-overview.md) | Sprekercontrole en -identificatie | De Speaker Recognition-service biedt algoritmen die sprekers controleren en identificeren aan de hand van hun unieke stemkenmerken. Speaker Recognition wordt gebruikt voor het beantwoorden van de vraag 'Wie spreekt er?'. | Ja | [Ja](https://docs.microsoft.com/rest/api/speakerrecognition/) |


[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

## <a name="try-the-speech-service-for-free"></a>Speech Service gratis uitproberen

Voor de volgende stappen hebt u zowel een Microsoft-account als een Azure-account nodig. Als u geen Microsoft-account hebt, kunt u zich gratis registreren bij het [Microsoft-accountportal](https://account.microsoft.com/account). Selecteer **Aanmelden met Microsoft** en klik als u wordt gevraagd om u aan te melden op **Een Microsoft-account maken**. Volg de stappen om uw nieuwe Microsoft-account te maken en te verifiëren.

Zodra u een Microsoft-account hebt, gaat u naar de [Azure-aanmeldingspagina](https://azure.microsoft.com/free/ai/), selecteert u **Gratis starten** en maakt u een nieuw Azure-account met behulp van een Microsoft-account.

> [!NOTE]
> De spraakservice heeft twee servicecategorieën: gratis en abonnement, die verschillende beperkingen en voordelen hebben. Wanneer u zich aanmeldt voor een gratis Azure-account, krijgt u dit met $ 200 aan servicetegoed dat u kunt toepassen op een betaald abonnement op de spraakservice, dat maximaal 30 dagen geldig is.
>
> Als u de gratis prijscategorie van de spraakservice met een laag volume kiest, kunt u dit gratis abonnement ook houden nadat uw gratis proefperiode of servicetegoed is verlopen.
>
> Zie [Prijzen van Cognitive Services - spraakservice](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) voor meer informatie.

### <a name="create-the-azure-resource"></a>De Azure-resource maken

Ga als volgt te werk om een spraakserviceresource (prijscategorie gratis of betaald) toe te voegen aan uw Azure-account:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/) met behulp van uw Microsoft-account.

1. Selecteer **Een resource maken** in de linkerbovenhoek van de portal. Als u **Een resource maken** niet ziet, kunt u dit altijd vinden door in de linkerbovenhoek van het scherm het samengevouwen menu te selecteren.

1. Typ 'spraak' in het zoekvak in het venster **Nieuw** en druk op Enter.

1. Selecteer **spraak** in de zoekresultaten.

   ![zoekresultaten voor spraak](media/index/speech-search.png)

1. Selecteer **Maken** en voer vervolgens de volgende handelingen uit:

   - Geef een unieke naam op voor uw nieuwe resource. De naam helpt u om onderscheid te maken tussen meerdere abonnementen die aan dezelfde service zijn gekoppeld.
   - Kies het Azure-abonnement waaraan de nieuwe resource wordt gekoppeld om te bepalen hoe de kosten worden gefactureerd.
   - Kies de [regio](regions.md) waarin de resource wordt gebruikt.
   - Kies gratis (F0) of betaald (S0) als prijscategorie. Selecteer **Volledige prijsgegevens weergeven** voor volledige informatie over prijzen en quota voor gebruik voor elke prijscategorie. Zie [limieten voor Azure Cognitive Services](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cognitive-services-limits) voor meer informatie over de resources die u kunt maken voor elk abonnement.
   - Maak een nieuwe resourcegroep voor dit spraakabonnement of wijs het abonnement toe aan een bestaande resourcegroep. Met resourcegroepen kunt u de verschillende Azure-abonnementen ordenen.
   - Selecteer **Maken**. Hiermee gaat u naar het implementatieoverzicht en worden berichten over de voortgang van de implementatie weergegeven.
<!--
> [!NOTE]
> You can create an unlimited number of standard-tier subscriptions in one or multiple regions. However, you can create only one free-tier subscription. Model deployments on the free tier that remain unused for 7 days will be decommissioned automatically.
-->
Het kan even duren voordat u uw nieuwe spraakresource is geïmplementeerd. Zodra de implementatie is voltooid, selecteert u **Ga naar resource** en selecteert u in het navigatievenster links **Sleutels** om uw abonnementssleutels voor de spraakservice weer te geven. Elk abonnement heeft twee sleutels. U kunt beide sleutels gebruiken in uw toepassing. Als u snel een sleutel naar de code-editor of een andere locatie wilt kopiëren/plakken, selecteert u de kopieerknop naast elke sleutel en schakelt u over naar Windows om de inhoud van het klembord op de gewenste locatie te plakken.

> [!IMPORTANT]
> Deze abonnementssleutels worden gebruikt om toegang te krijgen tot de API van Cognitive Services. Deel uw sleutels niet. Sla ze veilig op, bijvoorbeeld met behulp van Azure Key Vault. We raden u ook aan om deze sleutels regelmatig opnieuw te genereren. Er is slechts één sleutel nodig om een API-aanroep te maken. Wanneer u de eerste sleutel opnieuw genereert, kunt u de tweede sleutel gebruiken voor verdere toegang tot de service.

## <a name="complete-a-quickstart"></a>Een quickstart volgen

We bieden quickstarts in de populairste programmeertalen, die zijn ontworpen om u de basisontwerppatronen te leren en waarmee u in minder dan tien minuten code kunt uitvoeren. Zie de volgende lijst voor de quickstart voor elke functie.

* [Quickstart voor spraak-naar-tekst](get-started-speech-to-text.md)
* [Quickstart voor tekst-naar-spraak](get-started-text-to-speech.md)
* [Quickstart over spraakomzetting](speech-translation-basics.md)
* [Quickstart over intentieherkenning](quickstarts/intent-recognition.md)
* [Quickstart over sprekerherkenning](speaker-recognition-basics.md)

Nu u een begin hebt gemaakt met de spraakservice, kunt u onze zelfstudies proberen waarin u leert om verschillende scenario's op te lossen.

- [Zelfstudie: Intenties van gesproken inhoud herkennen met de Speech SDK en LUIS, C#](how-to-recognize-intents-from-speech-csharp.md)
- [Zelfstudie: Spraak inschakelen voor uw bot met de Speech SDK, C#](tutorial-voice-enable-your-bot-speech-sdk.md)
- [Zelfstudie: Een Flask-app maken om tekst te vertalen, sentiment te analyseren en vertaalde tekst in spraak om te zetten](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json&bc=%2fazure%2fcognitive-services%2fspeech-service%2fbreadcrumb%2ftoc.json&toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fspeech-service%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)

## <a name="get-sample-code"></a>Voorbeeldcode ophalen

Voorbeeldcode is beschikbaar op GitHub voor de spraakservice. Deze voorbeelden hebben betrekking op veelvoorkomende scenario's, zoals het lezen van audio van een bestand of stream, continue en eenmalige herkenning en het werken met aangepaste modellen. Gebruik de volgende koppelingen om SDK- en REST-voorbeelden te bekijken:

- [Voorbeelden van spraak-naar-tekst, tekst-naar-spraak en spraakomzetting (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Voorbeelden van batchtranscriptie (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [Voorbeelden van tekst-naar-spraak (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)
- [Voorbeelden van spraakassistenten (SDK)](https://aka.ms/csspeech/samples)

## <a name="customize-your-speech-experience"></a>Uw spraaktoepassing aanpassen

De spraakservice kan goed worden gebruikt met ingebouwde modellen, maar mogelijk wilt u de toepassing verder aanpassen en afstemmen op uw product of omgeving. Aanpassingsopties variëren van het afstemmen van akoestische modellen tot unieke spraakstijlen voor uw merk.

Andere producten bieden spraakmodellen die zijn afgestemd op specifieke doeleinden als gezondheidszorg of verzekeringen, maar zijn ook voor iedereen beschikbaar. Aanpassing in Azure Speech wordt onderdeel van *uw unieke* concurrentievoordeel dat niet beschikbaar is voor een andere gebruiker of klant. Met andere woorden, uw modellen zijn privé en aangepast, uitsluitend afgestemd op uw gebruikstoepassing.

| Speech Service | Platform | Beschrijving |
| -------------- | -------- | ----------- |
| Spraak naar tekst | [Aangepaste spraak](https://aka.ms/customspeech) | Pas de modellen voor spraakherkenning aan uw behoeften en beschikbare gegevens aan. Neem belemmeringen voor spraakherkenning weg, zoals spreekstijl, vocabulaire en achtergrondgeluiden. |
| Tekst naar spraak | [Aangepaste stem](https://aka.ms/customvoice) | Bouw een herkenbare, eenduidige stem voor uw tekst-naar-spraak-apps met uw beschikbare gesproken gegevens. U kunt de stemuitvoer verder verfijnen door een aantal stemparameters aan te passen. |

## <a name="reference-docs"></a>Naslagdocumentatie

- [Speech-SDK](speech-sdk-reference.md)
- [Speech Devices SDK](speech-devices-sdk.md)
- [REST API: Spraak-naar-tekst](rest-speech-to-text.md)
- [REST API: Tekst-naar-spraak](rest-text-to-speech.md)
- [REST API: Batchtranscriptie en aanpassing](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Aan de slag met spraak-naar-tekst](speech-to-text-basics.md)
> [Aan de slag met tekst-naar-spraak](get-started-text-to-speech.md)
