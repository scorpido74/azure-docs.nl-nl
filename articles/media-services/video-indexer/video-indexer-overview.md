---
title: Wat is Azure Media Services Video Indexer?
titleSuffix: Azure Media Services
description: In dit onderwerp vindt u een overzicht van de Azure Media Services Video Indexer-service.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/17/2019
ms.author: juliako
ms.openlocfilehash: d346f68534a9fdbc286cd5521c00d81c2a5fec78
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/23/2019
ms.locfileid: "71203557"
---
# <a name="what-is-video-indexer"></a>Wat is Video Indexer?

Azure Media Services Video Indexer is een AI-oplossing die is gebouwd op Azure Cognitive Services. Video Indexer biedt de mogelijkheid om diepere inzichten te verkrijgen (zonder gegevens analyse of codeer vaardig heden) met behulp van machine learning modellen op basis van meerdere kanalen (spraak, vocals, Visual). U kunt de modellen verder aanpassen en trainen. Met de service kunt u uitgebreide zoek opdrachten, de operationele kosten reduceren, nieuwe verdiensten maximaliseren-mogelijkheden, nieuwe gebruikers ervaring op grote archieven van Video's (met lage toetredings drempels). 

Als u inzichten wilt gaan extra heren met Video Indexer, moet u een account maken en Video's uploaden. Wanneer u uw Video's uploadt naar Video Indexer, analyseert deze zowel visuele elementen als audio door verschillende AI-modellen uit te voeren. Als Video Indexer analyseert u uw video, de inzichten die worden geëxtraheerd door de modellen.

![Stroomdiagram](./media/video-indexer-overview/model-chart.png)

## <a name="what-can-i-do-with-video-indexer"></a>Wat kan ik doen met Video Indexer?

De inzichten van Video Indexer kunnen worden toegepast op verschillende scenario's, waaronder:

* *Uitgebreide zoek opdracht* : gebruik de inzichten die zijn geëxtraheerd uit de video om de zoek ervaring in een video bibliotheek te verbeteren. Als u bijvoorbeeld gesp roken woorden en gezichten indexeert, kan de zoek ervaring van het vinden van momenten in een video waarbij een persoon op bepaalde woorden is gespokeeerd of wanneer twee personen samen worden weer gegeven. Zoekopdrachten op basis van dergelijke inzichten uit video's zijn handig voor persbureaus, onderwijsinstellingen, omroepen, eigenaren van inhoud voor de entertainmentindustrie, LOB-apps voor ondernemingen, kortom voor elke bedrijfstak waarin videobibliotheken worden onderhouden die door gebruikers worden doorzocht.
* *Maken van inhoud*: Maak Trailers, Markeer trommels, inhoud van sociale media of Nieuws clips op basis van de inzichten video indexer extracten van uw inhoud. Met keyframes, scènes en tijds tempels voor de vormgeving van personen en labels kan het maken van het proces veel vloeiender en eenvoudiger worden, en kunt u meteen profiteren van de interessante onderdelen van de video die u nodig hebt voor de inhoud die u maakt.
* *Toegankelijkheid*: of u uw inhoud beschikbaar wilt maken voor mensen met een handicap of als u wilt dat uw inhoud naar verschillende regio's wordt gedistribueerd met behulp van verschillende talen, kunt u de transcriptie en de vertaling van de video gebruiken Indexeer functie in meerdere talen.
* *Verdiensten maximaliseren*: Video Indexer kunt u helpen de waarde van Video's te verhogen. Zo kunnen branches die gebruikmaken van de inkomsten van de advertentie (bijvoorbeeld nieuws media, sociale media enz.), relevante advertenties leveren door gebruik te maken van de uitgepakte inzichten als extra signalen voor de ad-server.
* *Toezicht op inhoud*: gebruik tekstuele en visuele toezicht modellen voor inhoud om uw gebruikers veilig te houden van ongepaste inhoud en te controleren of de inhoud die u publiceert, overeenkomt met de waarden van uw organisatie. U kunt bepaalde Video's automatisch blok keren of uw gebruikers een waarschuwing geven over de inhoud. 
* *Aanbevelingen*: Video Insights kan worden gebruikt om de gebruikers betrokkenheid te verbeteren door de relevante video momenten voor gebruikers te markeren. Door elke video met aanvullende meta gegevens te labelen, kunt u gebruikers aanbevelen de meest relevante Video's en het deel van de video markeren die overeenkomen met hun behoeften. 

## <a name="features"></a>Functies

Hieronder ziet u de lijst met inzichten die u kunt ophalen uit uw Video's met Video Indexer video-en audio modellen:

### <a name="video-insights"></a>Video inzichten

* **Gezichts detectie**: Detecteert en groepeert gezichten die in de video worden weer gegeven.
* **Beroemdheden-id**: Video Indexer identificeert automatisch meer dan 1.000.000 beroemdheden, zoals wereld leiders, actoren, Actresses, atleten, onderzoekers, zakelijke en technologische leiders over de hele wereld. De gegevens van deze beroemdheden zijn ook beschikbaar op verschillende beroemde websites, zoals IMDB en Wikipedia.
* **Op account gebaseerde gezichts identificatie**: Video Indexer treinen een model voor een specifiek account. Vervolgens worden de gezichten herkend in de video op basis van het getrainde model. Zie [een persoons model aanpassen van de video indexer website](customize-person-model-with-website.md) en [een persoonlijk model aanpassen met de video indexer-API](customize-person-model-with-api.md)voor meer informatie.
* **Miniatuur extractie voor gezichten** ("beste gezicht"): Identificeert automatisch het beste vastgelegde gezicht in elke groep gezichten (op basis van kwaliteit, grootte en front-positie) en extraheer dit als een afbeeldings Asset.
* **Visuele tekst herkenning** (OCR): Extraheert tekst die visueel wordt weer gegeven in de video.
* **Visuele toezicht op inhoud**: Detecteert de visuele elementen volwassene en/of ongepaste.
* **Identificatie van labels**: Hiermee worden visuele objecten en weer gegeven acties geïdentificeerd.
* **Scène segmentering**: Hiermee wordt bepaald wanneer een scène in video wordt gewijzigd op basis van visuele hints. In een scène wordt één gebeurtenis weer gegeven. deze is samengesteld op basis van een reeks opeenvolgende opnamen, die semantisch verwant zijn.
* **Opname detectie**: bepaalt wanneer een foto in video wordt gewijzigd op basis van visuele hints. Een foto is een reeks frames uit dezelfde camera voor animatie. Zie scènes, afbeeldingen en keyframes voor meer informatie.
* **Black frame-detectie**: Identificeert zwarte frames die in de video worden weer gegeven.
* **Extractie van keyframe**: Detecteert stabiele keyframes in een video.
* **Rollend tegoed**: Identificeer het begin en het einde van de roulerende tegoeden aan het einde van TV-Program ma's en films.
* **Detectie van animatie tekens** (preview): detectie, groepering en herkenning van tekens in inhoud met animatie via integratie met [Cognitive Services aangepast gezichts vermogen](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/). Zie voor meer informatie [tekst detectie met animatie](animated-characters-recognition.md).
* **Redactionele afbeeldings type detectie**: Tags maken op basis van het type (zoals grote opname, middel grote opnamen, close-up, bijna sluiten, twee Foto's, meerdere personen, buiten en binnen, enzovoort). Zie [redactionele shot type Detection](scenes-shots-keyframes.md#editorial-shot-type-detection)(Engelstalig) voor meer informatie.

### <a name="audio-insights"></a>Audio inzichten

* **Automatische taal detectie**: Identificeert automatisch de taal voor de dominante spraak. Ondersteunde talen zijn Engels, Spaans, Frans, Duits, Italiaans, Chinees (vereenvoudigd), Japans, Russisch en Portugees (Brazilië). Als de taal niet met vertrouwen kan worden geïdentificeerd Video Indexer, wordt ervan uitgegaan dat de gesp roken taal Engels is. Zie [Language Identification model (Engelstalig)](language-identification-model.md)voor meer informatie.
* **Meertalige spraak-identificatie en transcriptie** (preview-versie): Identificeert automatisch de gesp roken taal in verschillende segmenten van de audio, waarbij elk segment van het Media bestand dat moet worden getranscribeerd, wordt verzonden en de transcriptie opnieuw kan worden gecombineerd tot één Unified transcriptie. Zie voor meer informatie [automatisch inhoud identificeren en transcriberen in meerdere talen](multi-language-identification-transcription.md).
* **Audio-transcriptie**: Hiermee converteert u spraak naar tekst in 12 talen en kunt u uitbrei dingen toestaan. Ondersteunde talen zijn Engels, Spaans, Frans, Duits, Italiaans, Chinees (vereenvoudigd), Japans, Arabisch, Russisch, Braziliaans Portugees, Hindi en Koreaans.
* **Ondertiteling**: Hiermee maakt u ondertiteling in drie indelingen: VTT, TTML, SRT.
* **Verwerking van twee kanalen**: Automatische detectie, afzonderlijke transcripten en samen voegingen op één tijd lijn.
* **Ruis reductie**: Hiermee wist u audio-of ruis opnamen van telefonie (op basis van Skype-filters).
* **Transcript aanpassing** (CRI'S): Traint aangepaste spraak naar tekst modellen om branchespecifieke transcripten te maken. Zie [een taal model aanpassen van de video indexer website](customize-language-model-with-website.md) en [een taal model aanpassen met de video indexer-api's](customize-language-model-with-api.md)voor meer informatie.
* **Opsomming**van de spreker: Hiermee wordt de spreker en het tijdstip waarop wordt gezocht, genoteerd en begrepen.
* **Sprekers statistieken**: Biedt statistieken voor de spraak verhoudingen van de luid sprekers.
* **Toezicht op tekstuele inhoud**: Detecteert expliciete tekst in de transcripten audio.
* **Audio-effecten**: Identificeert audio-effecten, zoals hand claps, spraak en stilte.
* **Detectie van Emotion**: Identificeert emoties op basis van de spraak (wat er wordt genoemd) en de spreek kleur (hoe deze wordt genoemd). De EMOTION kan Joy, verdriet, boosheid of vrezen zijn.
* **Vertaling**: Maakt vertalingen van de Audio-Transcript op 54 verschillende talen.

### <a name="audio-and-video-insights-multi-channels"></a>Audio-en video inzichten (multi kanalen)

Wanneer het indexeren per kanaal gedeeltelijk resultaat voor deze modellen beschikbaar is

* **Extractie van tref woorden**: Extraheert tref woorden uit spraak en visuele tekst.
* **Extractie van benoemde entiteiten**: Pakt merken, locaties en mensen uit vanuit spraak en visuele tekst via natuurlijke taal verwerking (NLP).
* **Onderwerp**voor demijnren: Hiermee maakt u de belangrijkste onderwerpen van transcripten onduidelijker. De IPTC-taxonomie op 1e niveau is opgenomen.
* **Artefacten**: Extraheert een uitgebreide set met artefacten van "volgende niveau Details" voor elk model.
* **Sentiment analyse**: Geeft positieve, negatieve en neutrale gevoel aan vanuit spraak-en visuele tekst.

## <a name="how-can-i-get-started-with-video-indexer"></a>Hoe kan ik aan de slag met Video Indexer?

U kunt Video Indexer mogelijkheden op drie verschillende manieren gebruiken: 

* Video Indexer-portal: een eenvoudig te gebruiken oplossing waarmee u het product kunt evalueren, het account beheert en modellen aanpast. 

    Zie [aan de slag met de video indexer-website](video-indexer-get-started.md)voor meer informatie over de portal.  
* API-integratie-alle mogelijkheden van Video Indexer zijn beschikbaar via een REST API zodat u de oplossing kunt integreren in uw toepassingen en infra structuur. 

    Zie [Video Indexer rest API gebruiken](video-indexer-use-apis.md)om aan de slag te gaan als ontwikkelaar. 
* Emendable-widget: Hiermee kunt u de video indexer Insights-, speler-en redacteur-ervaringen insluiten in uw toepassing. 

    Zie [visuele objecten insluiten in uw toepassing](video-indexer-embed-widgets.md)voor meer informatie. 

Als u de website gebruikt, worden de inzichten toegevoegd als meta gegevens en zichtbaar in de portal. Als u Api's gebruikt, zijn de inzichten beschikbaar als JSON-bestand. 

## <a name="next-steps"></a>Volgende stappen

U kunt aan de slag met Video Indexer. Raadpleeg voor meer informatie de volgende artikelen:

- [Aan de slag met de Video Indexer-website](video-indexer-get-started.md)
- [REST-API van Video Indexer gebruiken](video-indexer-use-apis.md)
- [Video Indexer-widgets insluiten in uw toepassingen](video-indexer-embed-widgets.md)
