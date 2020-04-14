---
title: Wat is Video Indexer?
titleSuffix: Azure Media Services
description: In dit artikel vindt u een overzicht van de Azure Media Services Video Indexer-service.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/12/2020
ms.author: juliako
ms.openlocfilehash: 9bea9e375e00d6fc0f6c33aa1bcc766773db2059
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81269938"
---
# <a name="what-is-video-indexer"></a>Wat is Video Indexer?

Video Indexer (VI) is de Azure Media Services AI-oplossing en onderdeel van het merk Azure Cognitive Services. Video Indexer biedt de mogelijkheid om diepgaande inzichten te extraheren (zonder gegevensanalyse of coderingsvaardigheden) met behulp van machine learning-modellen op basis van meerdere kanalen (stem, zang, visueel). U de modellen verder aanpassen en trainen. De service maakt diep zoeken mogelijk, verlaagt de operationele kosten, maakt nieuwe mogelijkheden voor het genereren van inkomsten mogelijk en creëert nieuwe gebruikerservaringen op grote archieven van video's (met lage toegangsdrempels).

Als je inzichten wilt verzamelen met Video Indexer, moet je een account maken en video's uploaden. Wanneer u uw video's uploadt naar Video Indexer, worden zowel visuals als audio geanalyseerd door verschillende AI-modellen uit te voeren. Terwijl Video Indexer uw video analyseert, worden de inzichten die door de AI-modellen worden geëxtraheerd.

Het volgende diagram is een illustratie en geen technische uitleg van hoe Video Indexer werkt in de backend.

![Azure Media Services-videoindexerstroomdiagram](./media/video-indexer-overview/model-chart.png)


## <a name="compliance-privacy-and-security"></a>Compliance, privacy en beveiliging

Als een belangrijke herinnering moet u voldoen aan alle toepasselijke wetten bij uw gebruik van Video Indexer en mag u Video Indexer of een Azure-service niet gebruiken op een manier die de rechten van anderen schendt of die schadelijk kan zijn voor anderen.

Voordat u een video/afbeelding uploadt naar Video Indexer, moet u over alle juiste rechten beschikken om de video/afbeelding te gebruiken, inclusief, indien vereist door de wet, alle benodigde toestemmingen van personen (indien aanwezig) in de video/afbeelding, voor het gebruik, de verwerking en de opslag van hun gegevens in Video Indexer en Azure. Sommige rechtsgebieden kunnen speciale wettelijke vereisten opleggen voor het verzamelen, online verwerken en opslaan van bepaalde categorieën gegevens, zoals biometrische gegevens. Voordat u Video Indexer en Azure gebruikt voor de verwerking en opslag van gegevens die aan speciale wettelijke vereisten voldoen, moet u ervoor zorgen dat aan dergelijke wettelijke vereisten wordt voldaan die op u van toepassing kunnen zijn.

Ga naar het Microsoft [Trust Center](https://www.microsoft.com/TrustCenter/CloudServices/Azure/default.aspx)voor meer informatie over compliance, privacy en beveiliging in Video Indexer. Voor de privacyverplichtingen van Microsoft, gegevensverwerking en bewaarpraktijken, waaronder het verwijderen van uw gegevens, raadpleegt u de [privacyverklaring](https://privacy.microsoft.com/PrivacyStatement)van Microsoft, de [voorwaarden voor onlineservices](https://www.microsoft.com/licensing/product-licensing/products?rtc=1) (OST) en [het addendum voor gegevensverwerking](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) (DPA"). Door Video Indexer te gebruiken, stemt u ermee in gebonden te zijn door de OST, DPA en de Privacy Verklaring.

## <a name="what-can-i-do-with-video-indexer"></a>Wat kan ik doen met Video Indexer?

De inzichten van Video Indexer kunnen worden toegepast op vele scenario's, waaronder:

* *Diep zoeken:* gebruik de inzichten uit de video om de zoekervaring in een videobibliotheek te verbeteren. Als u bijvoorbeeld gesproken woorden en gezichten indexeert, kan het zoeken mogelijk zijn om momenten te vinden in een video waarin een persoon bepaalde woorden sprak of wanneer twee personen samen werden gezien. Zoeken op basis van dergelijke inzichten uit video's is van toepassing op persbureaus, onderwijsinstellingen, omroepen, eigenaren van entertainmentinhoud, LOB-apps voor bedrijven en in het algemeen op elke branche die een videobibliotheek heeft waargebruikers tegen moeten zoeken.
* *Content creatie*: Maak trailers, markeer rollen, social media content, of nieuws clips op basis van de inzichten Video Indexer uittreksels uit uw inhoud. Hoofdframes, scènesmarkeringen en tijdstempels voor de mensen en labeloptredens maken het creatieproces veel soepeler en gemakkelijker en stelt u in staat om de delen van de video te krijgen die u nodig hebt voor de inhoud die u maakt.
* *Toegankelijkheid:* of u uw inhoud beschikbaar wilt maken voor mensen met een handicap of als u wilt dat uw inhoud wordt gedistribueerd naar verschillende regio's met verschillende talen, u de transcriptie en vertaling gebruiken die door video-indexeerder in meerdere talen wordt geleverd.
* *Inkomsten genereren:* Video-indexer kan helpen de waarde van video's te verhogen. Bedrijven die afhankelijk zijn van advertentie-inkomsten (nieuwsmedia, sociale media, enzovoort) kunnen bijvoorbeeld relevante advertenties weergeven door de geëxtraheerde inzichten te gebruiken als extra signalen naar de advertentieserver.
* *Inhoudsmatiging:* gebruik tekstuele en visuele inhoudsmoderatiemodellen om uw gebruikers te beschermen tegen ongepaste inhoud en te valideren dat de inhoud die u publiceert overeenkomt met de waarden van uw organisatie. U bepaalde video's automatisch blokkeren of uw gebruikers waarschuwen voor de inhoud.
* *Aanbevelingen:* Video-inzichten kunnen worden gebruikt om de betrokkenheid van gebruikers te verbeteren door de relevante videomomenten aan gebruikers te benadrukken. Door elke video te taggen met extra metadata, kun je gebruikers de meest relevante video's aanbevelen en de delen van de video markeren die aan hun behoeften voldoen.

## <a name="features"></a>Functies

In de volgende lijst worden de inzichten weergegeven die u uit uw video's ophalen met video-indexervideo- en audiomodellen:

### <a name="video-insights"></a>Video-inzichten

* **Gezichtsdetectie**: detecteert en groepeert gezichten die worden weergegeven in de video.
* **Celebrity identificatie**: Video Indexer identificeert automatisch meer dan 1 miljoen beroemdheden- zoals wereldleiders, acteurs, actrices, atleten, onderzoekers, het bedrijfsleven en tech leiders over de hele wereld. De gegevens over deze beroemdheden zijn ook te vinden op verschillende websites (IMDB, Wikipedia, enzovoort).
* **Gezichtsidentificatie op basis van account**: Video Indexer traint een model voor een specifiek account. Het herkent dan gezichten in de video op basis van het getrainde model. Zie [Een persoonsmodel aanpassen op de website van video-indexer](customize-person-model-with-website.md) en [Een persoonsmodel aanpassen met de API voor video-indexeren](customize-person-model-with-api.md)voor meer informatie.
* **Miniatuurextractie voor gezichten** ('beste gezicht'): identificeert automatisch het best vastgelegde gezicht in elke groep gezichten (op basis van kwaliteit, grootte en frontale positie) en haalt het uit als een afbeeldingselement.
* **Visuele tekstherkenning** (OCR): hiermee wordt tekst geëxtraheerd die visueel wordt weergegeven in de video.
* **Visueel inhoudstoezicht**: detecteert inhoud voor volwassenen en/of ongepaste visuele elementen.
* **Identificatie van labels**: identificeert visuele objecten en acties die worden weergegeven.
* **Scènesegmentatie:** bepaalt wanneer een scène verandert in video op basis van visuele aanwijzingen. Een scène toont een enkele gebeurtenis en het is samengesteld door een reeks opeenvolgende opnamen, die semantisch gerelateerd zijn.
* **Shotdetectie:** bepaalt wanneer een opname in video verandert op basis van visuele aanwijzingen. Een opname is een reeks frames genomen van dezelfde motion-picture camera. Zie [Scènes, opnamen en hoofdframes](scenes-shots-keyframes.md)voor meer informatie.
* **Detectie van zwarte frames**: identificeert zwarte frames in de video.
* **Extractie van sleutelframes**: detecteert stabiele sleutelframes in een video.
* **Rolling credits**: Identificeert het begin en einde van de rollende credits aan het einde van tv-programma's en films.
* **Detectie van geanimeerde tekens** (voorbeeld): detectie, groepering en herkenning van tekens in geanimeerde inhoud via integratie met [de aangepaste visie van Cognitive Services](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/). Zie [Detectie van tekens geanimeerd](animated-characters-recognition.md)voor meer informatie.
* **Editorial shot type detectie:** Tagging shots op basis van hun type (zoals wide shot, medium shot, close-up, extreme close-up, twee schot, meerdere mensen, outdoor en binnen, enzovoort). Zie [Typedetectie van redactionele schoten](scenes-shots-keyframes.md#editorial-shot-type-detection)voor meer informatie .

### <a name="audio-insights"></a>Audio-inzichten

* **Audiotranscriptie**: converteert spraak naar tekst in 12 talen en maakt extensies mogelijk. Ondersteunde talen zijn Engels, Spaans, Frans, Duits, Italiaans, Mandarijn Chines, Japans, Arabisch, Russisch, Braziliaans Portugees, Hindi en Koreaans.
* **Automatische taaldetectie**: identificeert automatisch de meest gesproken taal. Ondersteunde talen zijn Engels, Spaans, Frans, Duits, Italiaans, Mandarijn Chines, Japans, Russisch en Braziliaans Portugees. Als de taal niet met vertrouwen kan worden geïdentificeerd, gaat Video Indexer ervan uit dat de gesproken taal Engels is. Zie [Taalidentificatiemodel](language-identification-model.md)voor meer informatie .
* **Meertalige spraakidentificatie en transcriptie** (voorbeeld): identificeert automatisch de gesproken taal in verschillende segmenten van audio. Het stuurt elk segment van het mediabestand dat moet worden getranscribeerd en combineert vervolgens de transcriptie terug naar één uniforme transcriptie. Zie [Meertalige inhoud automatisch identificeren en transcriberen](multi-language-identification-transcription.md)voor meer informatie.
* **Ondertiteling**: hiermee maakt u ondertiteling in drie indelingen: VTT, TTML, SRT.
* **Verwerking met twee kanalen:** Automatisch detecteert afzonderlijke transcripties en voegt samen met één tijdlijn.
* **Ruisonderdrukking**: Ruimt audio of luidruchtige opnames op (op basis van Skype-filters).
* **Transcript customization** (CRIS): Traint aangepaste spraak naar tekstmodellen om branchespecifieke transcripties te maken. Zie [Een taalmodel aanpassen op de website van video-indexer](customize-language-model-with-website.md) en [Een taalmodel aanpassen met de VIDEO-indexer-API's](customize-language-model-with-api.md)voor meer informatie.
* **Spreker opsomming**: Kaarten en begrijpt welke spreker sprak welke woorden en wanneer.
* **Sprekersstatistieken**: Biedt statistieken voor de spraakverhoudingen van sprekers.
* **Tekstueel inhoudsbeheer**: detecteert expliciete tekst in het audiotranscript.
* **Audio-effecten:** identificeert audio-effecten zoals handklappen, spraak en stilte.
* **Emotie detectie**: Identificeert emoties op basis van spraak (wat er gezegd wordt) en stem tonaliteit (hoe het wordt gezegd). De emotie kan vreugde, verdriet, woede of angst zijn.
* **Vertaling**: maakt vertalingen van het audiotranscript in 54 verschillende talen.

### <a name="audio-and-video-insights-multi-channels"></a>Audio- en video-inzichten (meerdere kanalen)

Bij indexering per kanaal is een gedeeltelijk resultaat voor deze modellen beschikbaar.

* **Trefwoordenextractie**: Haalt trefwoorden uit spraak en visuele tekst.
* **Benoemde entiteiten extractie**: Haalt merken, locaties en mensen uit spraak en visuele tekst via natural language processing (NLP).
* **Onderwerpdeductie**: maakt een deductie van de belangrijkste onderwerpen uit de transcripten. De 2e-level IPTC taxonomie is inbegrepen.
* **Artefacten**: extraheert een grote verscheidenheid aan 'extra gedetailleerde' artefacten voor elk van de modellen.
* **Gevoelsanalyse**: identificeert positieve, negatieve en neutrale gevoelens uit visuele tekst en gesproken woorden.

## <a name="how-can-i-get-started-with-video-indexer"></a>Hoe kan ik aan de slag met Video Indexer?

U op drie manieren toegang krijgen tot de mogelijkheden van Video Indexer:

* Video Indexer-portal: een gebruiksvriendelijke oplossing waarmee u het product evalueren, het account beheren en modellen aanpassen.

    Zie Aan de slag [met de website Video Indexer](video-indexer-get-started.md)voor meer informatie over de portal.  

* API-integratie: Alle mogelijkheden van Video Indexer zijn beschikbaar via een REST API, waarmee u de oplossing integreren in uw apps en infrastructuur.

    Zie REST API voor [video-indexer gebruiken](video-indexer-use-apis.md)om als ontwikkelaar aan de slag te gaan.

* Insluitbare widget: hiermee u de ervaringen met video-indexer, speler en editor insluiten in uw app.

    Zie [Visuele widgets insluiten in uw toepassing](video-indexer-embed-widgets.md)voor meer informatie.

Als u de website gebruikt, worden de inzichten toegevoegd als metagegevens en zijn ze zichtbaar in de portal. Als u API's gebruikt, zijn de inzichten beschikbaar als JSON-bestand.

## <a name="next-steps"></a>Volgende stappen

U kunt aan de slag met Video Indexer. Raadpleeg voor meer informatie de volgende artikelen:

- [Ga aan de slag met de website Video Indexer](video-indexer-get-started.md).
- [Inhoud verwerken met Video Indexer REST API](video-indexer-use-apis.md).
- [Sluit visuele widgets in uw toepassing in.](video-indexer-embed-widgets.md)
