---
title: Veelgestelde vragen over Video Indexer - Azure
titleSuffix: Azure Media Services
description: In dit artikel vindt u antwoorden op veelgestelde vragen over Azure Media Services Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 03/30/2020
ms.author: juliako
ms.openlocfilehash: dd41596b6631bb63e1625325f8bec065b43881cd
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421386"
---
# <a name="video-indexer-frequently-asked-questions"></a>Video Indexer veelgestelde vragen

In dit artikel worden veelgestelde vragen over Video Indexer beantwoord.

## <a name="general-questions"></a>Algemene vragen

### <a name="what-is-video-indexer"></a>Wat is Video Indexer?

Video Indexer is een kunstmatige intelligentieservice die deel uitmaakt van Microsoft Azure Media Services. Video Indexer biedt een orkestratie van meerdere machine learning-modellen waarmee u eenvoudig diepgaand inzicht uit een video halen. Om geavanceerde en nauwkeurige inzichten te bieden, maakt Video Indexer gebruik van meerdere kanalen van de video: audio, spraak en visueel. De inzichten van Video Indexer kunnen op vele manieren worden gebruikt, zoals het verbeteren van de vindbaarheid en toegankelijkheid van inhoud, het creëren van nieuwe mogelijkheden voor het genereren van inkomsten of het bouwen van nieuwe ervaringen die de inzichten gebruiken. Video Indexer biedt een webgebaseerde interface voor het testen, configureren en aanpassen van modellen in uw account. Ontwikkelaars kunnen een REST-gebaseerde API gebruiken om Video Indexer te integreren in het productiesysteem. 

### <a name="what-can-i-do-with-video-indexer"></a>Wat kan ik doen met Video Indexer?

Enkele bewerkingen die Video Indexer kan uitvoeren op mediabestanden zijn:

* Het identificeren en extraheren van spraak en het identificeren van sprekers.
* Tekst op het scherm in een video identificeren en extraheren.
* Objecten in een videobestand detecteren.
* Identificeer merken (bijvoorbeeld: Microsoft) van audiotracks en tekst op het scherm in een video.
* Het detecteren en herkennen van gezichten uit een database van beroemdheden en een door de gebruiker gedefinieerde database met gezichten.
* Het extraheren van onderwerpen besproken, maar niet noodzakelijkerwijs genoemd in audio-en video-inhoud.
* Het maken van ondertiteling of ondertiteling van het audiospoor.

Zie [Overzicht](video-indexer-overview.md)voor meer informatie en meer functies van video-indexer.

### <a name="how-do-i-get-started-with-video-indexer"></a>Hoe ga ik aan de slag met Video Indexer?

Video Indexer bevat een gratis proefaanbod dat u 600 minuten biedt in de webinterface en 2.400 minuten via de API. U [inloggen op de webinterface video-indexer](https://www.videoindexer.ai/) en deze zelf proberen met behulp van een webidentiteit en zonder dat u een Azure-abonnement hoeft in te stellen. 

Als u video's en audiovliegen op schaal wilt indexeren, u Video Indexer verbinden met een betaald Microsoft Azure-abonnement. Meer informatie over de prijzen vindt u op de [prijspagina.](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/)

Meer informatie over aan de slag vindt u in [Aan de slag.](video-indexer-get-started.md)

### <a name="do-i-need-coding-skills-to-use-video-indexer"></a>Heb ik codeervaardigheden nodig om Video Indexer te gebruiken?

U de webinterface Video Indexer gebruiken om uw account te evalueren, configureren en beheren **zonder dat codering vereist is.**  Wanneer u klaar bent om complexere toepassingen te ontwikkelen, u de [Video Indexer API](https://api-portal.videoindexer.ai/) gebruiken om Video Indexer te integreren in uw eigen toepassingen, websites of [aangepaste werkstromen met behulp van serverloze technologieën zoals Azure Logic Apps](https://azure.microsoft.com/blog/logic-apps-flow-connectors-will-make-automating-video-indexer-simpler-than-ever/) of Azure Functions.

### <a name="do-i-need-machine-learning-skills-to-use-video-indexer"></a>Heb ik machine learning-vaardigheden nodig om Video Indexer te gebruiken?

Nee, Video Indexer biedt de integratie van meerdere machine learning-modellen in één pijplijn. Door een video- of audiobestand te indexeren via Video Indexer, worden een volledige set inzichten opgehaald die op één gedeelde tijdlijn zijn geëxtraheerd zonder dat er machine learning-vaardigheden of kennis zijn over algoritmen die van de klant nodig zijn.

### <a name="what-media-formats-does-video-indexer-support"></a>Welke mediaformaten ondersteunt Video Indexer?

Video Indexer ondersteunt de meest voorkomende mediaformaten. Raadpleeg de [standaardindelingenlijst van Azure Media Encoder](https://docs.microsoft.com/azure/media-services/latest/media-encoder-standard-formats) voor meer informatie.

### <a name="how-to-do-i-upload-a-media-into-video-indexer"></a>Hoe upload ik een media naar Video Indexer?

In de webportal Video Indexer u een mediabestand uploaden via het dialoogvenster bestandsupload of door te wijzen op een URL die rechtstreeks het bronbestand host (zie [voorbeeld).](https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/Ignite-short.mp4) Elke URL die de media-inhoud host met een iFrame- of insluitcode, werkt niet (zie [voorbeeld).](https://www.videoindexer.ai/accounts/7e1282e8-083c-46ab-8c20-84cae3dc289d/videos/5cfa29e152/?t=4.11) De Video Indexer API vereist dat u het invoerbestand opgeeft via een URL of een bytearray. Uploads via een URL met behulp van de API zijn beperkt tot 10 GB, maar hebben geen tijdslimiet. Voor meer informatie, zie deze [how-to gids](https://docs.microsoft.com/azure/media-services/video-indexer/upload-index-videos).

### <a name="how-long-does-it-take-video-indexer-to-extract-insights-from-media"></a>Hoe lang duurt het video-indexer om inzichten uit media te halen?

De hoeveelheid tijd die nodig is om een video- of audiobestand te indexeren, zowel met behulp van de Video Indexer API als de video-indexer webgebaseerde interface, is afhankelijk van meerdere parameters, zoals de bestandslengte en -kwaliteit, het aantal inzichten in het bestand, het aantal [beschikbare gereserveerde eenheden](https://docs.microsoft.com/azure/media-services/previous/media-services-scale-media-processing-overview) en of het [streaming-eindpunt](https://docs.microsoft.com/azure/media-services/previous/media-services-streaming-endpoints-overview) is ingeschakeld of niet. We raden u aan een paar testbestanden met uw eigen inhoud uit te voeren en een gemiddelde te nemen om een beter idee te krijgen.

### <a name="can-i-create-customized-workflows-to-automate-processes-with-video-indexer"></a>Kan ik aangepaste workflows maken om processen te automatiseren met Video Indexer?

Ja, u Video Indexer integreren in serverloze technologieën zoals Logic Apps, Flow en [Azure-functies.](https://azure.microsoft.com/services/functions/) Meer informatie over de [Logic App-](https://azure.microsoft.com/services/logic-apps/) en [Flow-connectors](https://flow.microsoft.com/en-us/) voor video-indexer vindt [u hier.](https://azure.microsoft.com/blog/logic-apps-flow-connectors-will-make-automating-video-indexer-simpler-than-ever/) 

### <a name="in-which-azure-regions-is-video-indexer-available"></a>In welke Azure-regio's is video-indexer beschikbaar?

U zien welke Azure-regio's Video Indexer beschikbaar is op de [regiopagina.](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all)

### <a name="can-i-customize-video-indexer-models-for-my-specific-use-case"></a>Kan ik Video Indexer-modellen aanpassen voor mijn specifieke use case? 

Ja. In Video Indexer u een aantal van de beschikbare modellen aanpassen aan uw behoeften. 

Ons persoonsmodel ondersteunt bijvoorbeeld kant-en-klare 1.000.000 gezichten met herkenning door beroemdheden, maar je het ook trainen om andere gezichten te herkennen die niet in die database staan. 

Zie artikelen over het aanpassen [Person](customize-person-model-overview.md)van persoons-, [merken-](customize-brands-model-overview.md)en [taalmodellen voor](customize-language-model-overview.md) meer informatie. 

### <a name="what-is-the-sla-for-video-indexer"></a>Wat is de SLA voor video-indexer?

De SLA van Azure Media Service heeft betrekking op Video Indexer en is te vinden op de [SLA-pagina.](https://azure.microsoft.com/support/legal/sla/media-services/v1_2/) De SLA is alleen van toepassing op betaalde video-accounts van Video Indexer en is niet van toepassing op de gratis proefperiode.

## <a name="privacy-questions"></a>Privacyvragen

### <a name="are-video-and-audio-files-indexed-by-video-indexer-stored"></a>Worden video- en audiobestanden geïndexeerd door Video Indexer opgeslagen?

Ja, tenzij u het bestand verwijdert uit Video Indexer, via de website of API van Video Indexer, worden uw video- en audiobestanden opgeslagen. Voor de gratis proefperiode worden de video- en audiobestanden die u indexeert opgeslagen in de Azure-regio Oost-VS. Anders worden uw video- en audiobestanden opgeslagen in het opslagaccount van uw Azure-abonnement.

### <a name="can-i-delete-my-files-that-are-stored-in-video-indexer-portal"></a>Kan ik mijn bestanden verwijderen die zijn opgeslagen in Video Indexer Portal?

Ja, u uw video- en audiobestanden altijd verwijderen, evenals alle metagegevens en inzichten die door Video Indexer zijn vertrokken. Zodra u een bestand uit Video Indexer verwijdert, worden het bestand en de metagegevens en -inzichten definitief verwijderd uit Video Indexer. Als u echter uw eigen back-upoplossing in Azure-opslag hebt geïmplementeerd, blijft het bestand in uw Azure-opslag.

### <a name="can-i-control-user-access-to-my-video-indexer-account"></a>Kan ik de gebruiker de toegang tot mijn Video Indexer-account beheren?

Ja, alleen accountbeheerders kunnen mensen uitnodigen en niet uitnodigen voor hun accounts, en toewijzen wie bewerkingsrechten heeft en wie alleen-lezen toegang heeft.

### <a name="who-has-access-to-my-video-and-audio-files-that-have-been-indexed-andor-stored-by-video-indexer-and-the-metadata-and-insights-that-were-extracted"></a>Wie heeft toegang tot mijn video- en audiobestanden die zijn geïndexeerd en/of opgeslagen door Video Indexer en de metadata en inzichten die zijn geëxtraheerd?

Je video- of audio-inhoud die openbaar is als privacyinstelling, kan worden geopend door iedereen die de link heeft naar je video- of audio-inhoud en de inzichten ervan. Uw video- of audio-inhoud die privé is als privacyinstelling, kan alleen worden geopend door gebruikers die zijn uitgenodigd voor het account van de video- of audio-inhoud. De privacyinstelling van uw inhoud is ook van toepassing op de metagegevens en inzichten die Video Indexer uitheert. U wijst de privacyinstelling toe wanneer u uw video- of audiobestand uploadt. U ook de privacyinstelling wijzigen na het indexeren.

### <a name="what-access-does-microsoft-have-to-my-video-or-audio-files-that-have-been-indexed-andor-stored-by-video-indexer-and-the-metadata-and-insights-that-were-extracted"></a>Welke toegang heeft Microsoft tot mijn video- of audiobestanden die zijn geïndexeerd en/of zijn opgeslagen door Video Indexer en de metagegevens en inzichten die zijn geëxtraheerd?

Volgens de [Azure Online Services Terms](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) (OST) bent u volledig eigenaar van uw inhoud en heeft Microsoft alleen toegang tot uw inhoud en de metagegevens en inzichten die Video Indexer uit uw inhoud haalt volgens de OST en de Microsoft-privacyverklaring.

### <a name="are-the-custom-models-that-i-build-in-my-video-indexer-account-available-to-other-accounts"></a>Zijn de aangepaste modellen die ik in mijn Video Indexer-account bouw beschikbaar voor andere accounts?

 Nee, de aangepaste modellen die u in uw account maakt, zijn niet beschikbaar voor een ander account. Met Video Indexer u momenteel aangepaste [merken,](customize-brands-model-overview.md) [taal-](customize-language-model-overview.md)en [persoonsmodellen](customize-person-model-overview.md) in uw account bouwen. Deze modellen zijn alleen beschikbaar in het account waarin u de modellen hebt gemaakt.
  
### <a name="is-the-content-indexed-by-video-indexer-kept-within-the-azure-region-where-i-am-using-video-indexer"></a>Wordt de inhoud die door Video Indexer wordt geïndexeerd, bewaard in het Azure-gebied waar ik Video Indexer gebruik?

Ja, de inhoud en de inzichten ervan worden bewaard binnen de Azure-regio, tenzij u een handmatige configuratie hebt in uw Azure-abonnement dat meerdere Azure-regio's gebruikt. 

### <a name="what-is-the-privacy-policy-for-video-indexer"></a>Wat is het privacybeleid voor Video Indexer?

Video-indexer valt onder de [Privacyverklaring van Microsoft](https://privacy.microsoft.com/privacystatement). In de privacyverklaring worden de persoonsgegevens uitgelegd die Microsoft verwerkt, hoe Microsoft deze verwerkt en voor welke doeleinden Microsoft deze verwerkt. Ga voor meer informatie over privacy naar het [Microsoft Trust Center](https://www.microsoft.com/trustcenter).

### <a name="what-certifications-does-video-indexer-have"></a>Welke certificeringen heeft Video Indexer?

Video Indexer heeft momenteel de SOC-certificering. Als u de certificering van Video Indexer wilt controleren, raadpleegt u het [Microsoft Trust Center](https://www.microsoft.com/trustcenter/compliance/complianceofferings?product=Azure).

### <a name="what-is-the-difference-between-private-and-public-videos"></a>Wat is het verschil tussen privé- en openbare video's? 

Wanneer video's worden geüpload naar Video Indexer, u kiezen uit twee privacyinstellingen: privé en openbaar. Openbare video's zijn toegankelijk voor iedereen, inclusief anonieme en niet-geïdentificeerde gebruikers. Privé zijn uitsluitend beperkt tot de accountleden. 

### <a name="i-tried-to-upload-a-video-as-public-and-it-was-flagged-for-inappropriate-or-offensive-content-what-does-that-mean"></a>Ik heb geprobeerd om een video te uploaden als openbaar en het werd gemarkeerd voor ongepaste of aanstootgevende inhoud, wat betekent dat? 

Bij het uploaden van een video naar Video Indexer wordt een automatische inhoudsanalyse uitgevoerd door algoritmen en modellen om ervoor te zorgen dat er geen ongepaste inhoud openbaar wordt gepresenteerd. Als een video verdacht wordt bevonden als expliciete inhoud, is het niet mogelijk om deze als openbaar in te stellen. De accountleden hebben er echter nog steeds toegang toe als een privévideo (bekijk deze, download de inzichten en geëxtraheerde artefacten en voeren andere bewerkingen uit die beschikbaar zijn voor accountleden).   

Om de video in te stellen voor openbare toegang, u: 

* Bouw je eigen interfacelaag (zoals app of website) en gebruik deze om te communiceren met de Video Indexer-service. Op deze manier blijft de video privé in onze portal en kunnen uw gebruikers ermee communiceren via uw interface. U bijvoorbeeld nog steeds de inzichten krijgen of het bekijken van de video toestaan in uw eigen interface. 
* Vraag een menselijke beoordeling van de inhoud aan, wat zou resulteren in het verwijderen van de beperking ervan uitgaande dat de inhoud niet expliciet is. 

    Deze optie kan worden onderzocht als de video-indexer-website rechtstreeks door uw gebruikers wordt gebruikt als interfacelaag en voor openbare (niet-geverifieerde) weergave. 

## <a name="api-questions"></a>API-vragen

### <a name="what-apis-does-video-indexer-offer"></a>Welke API's biedt Video Indexer?

De API's van Video Indexer maken indexering, het extraheren van metadata, asset management, vertaling, inbedding, aanpassing van modellen en meer mogelijk. Voor meer gedetailleerde informatie over het gebruik van de Video Indexer API verwijzen wij u naar de [Video Indexer Developer Portal](https://api-portal.videoindexer.ai/).

### <a name="what-client-sdks-does-video-indexer-offer"></a>Welke client SDKs biedt Video Indexer?

Er zijn momenteel geen client SDKs aangeboden. De Video Indexer team werkt aan de SDKs en is van plan om ze binnenkort te leveren.

### <a name="how-do-i-get-started-with-video-indexers-api"></a>Hoe ga ik aan de slag met de API van Video Indexer?

Volg [zelfstudie: aan de slag met de Video Indexer API](video-indexer-use-apis.md).

### <a name="what-is-the-difference-between-the-video-indexer-api-and-the-azure-media-service-v3-api"></a>Wat is het verschil tussen de Video Indexer API en de Azure Media Service v3 API?

Momenteel zijn er enkele overlappingen in functies die worden aangeboden door de Video Indexer API en de Azure Media Service v3 API. Meer informatie over het vergelijken van beide services vindt [u hier.](compare-video-indexer-with-media-services-presets.md)

### <a name="what-is-an-api-access-token-and-why-do-i-need-it"></a>Wat is een API-toegangstoken en waarom heb ik het nodig?

De Video Indexer API bevat een Autorisatie-API en een Operations API. De API Autorisaties bevat aanroepen die u toegang geven tot token. Elke aanroep naar de Operations-API moet worden gekoppeld aan een toegangstoken dat overeenkomt met het autorisatiebereik van de aanroep.

Toegangstokens zijn nodig om de API's voor video-indexer te gebruiken voor beveiligingsdoeleinden. Dit zorgt ervoor dat oproepen afkomstig zijn van u of degenen die toegangsmachtigingen hebben voor uw account. 

### <a name="what-is-the-difference-between-account-access-token-user-access-token-and-video-access-token"></a>Wat is het verschil tussen accounttoegangstoken, gebruikerstoegangstoken en videotoegangstoken?

* Accountniveau: met toegangstokens op accountniveau kunt u bewerkingen uitvoeren op het accountniveau of het videoniveau. Upload bijvoorbeeld een video, zet alle video's op een rij, verkrijg video-inzichten.
* Gebruikersniveau - met toegangstokens op gebruikersniveau u bewerkingen uitvoeren op gebruikersniveau. Hiermee kunt u bijvoorbeeld gekoppelde accounts ophalen.
* Videoniveau: met toegangstokens op videoniveau kunt u bewerkingen uitvoeren op een specifieke video. Hiermee kunt u bijvoorbeeld inzichten in een video krijgen, ondertiteling downloaden, widgets ophalen, enzovoort.

### <a name="how-often-do-i-need-to-get-a-new-access-token-when-do-access-tokens-expire"></a>Hoe vaak heb ik een nieuw toegangstoken nodig? Wanneer verlopen toegangstokens?

Toegangstokens verlopen elk uur, dus je moet elk uur een nieuw toegangstoken genereren. 

### <a name="what-are-the-login-options-to-video-indexer-developer-portal"></a>Wat zijn de inlogopties voor video-indexer developer portal?

U inloggen met Azure AD, Microsoft-account, Google-account of Facebook-account. 

Zodra u uw e-mailaccount registreert via een identiteitsprovider, u dit e-mailaccount niet meer gebruiken bij een andere identiteitsprovider.

## <a name="billing-questions"></a>Vragen over facturering

### <a name="how-much-does-video-indexer-cost"></a>Hoeveel kost Video Indexer?

Video Indexer maakt gebruik van een eenvoudig prijsmodel voor betalen per gebruik op basis van de duur van de inhoudsinvoer die u indexeert. Er kunnen extra kosten in rekening worden gebracht voor coderings-, streaming-, opslag-, netwerkgebruik- en mediagereserveerde eenheden. Zie voor meer informatie de [prijspagina.](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/)

### <a name="when-am-i-billed-for-using-video-indexer"></a>Wanneer worden er kosten in rekening gebracht voor het gebruik van Video Indexer?

Bij het verzenden van een te indexeren video definieert de gebruiker de indexering als video-analyse, audio-analyse of beide. Dit bepaalt welke SKU's in rekening worden gebracht. Als er een fout op kritiekniveau optreedt tijdens de verwerking, wordt een foutcode geretourneerd als antwoord. In een dergelijk geval vindt er geen facturering plaats.  Een kritieke fout kan worden veroorzaakt door een fout in onze code of een kritieke fout in een interne afhankelijkheid die de service heeft. Fouten zoals verkeerde identificatie of inzichtextractie worden niet als kritiek beschouwd en een reactie wordt geretourneerd. In ieder geval wanneer een geldig (non-error code) antwoord wordt geretourneerd, facturering vindt plaats.
 
### <a name="does-video-indexer-offer-a-free-trial"></a>Biedt Video Indexer een gratis proefperiode aan?

Ja, Video Indexer biedt een gratis proefversie die volledige service- en API-functionaliteit biedt. Er is een quotum van 600 minuten aan video's voor webgebaseerde interfacegebruikers en 2.400 minuten voor API-gebruikers. 

## <a name="next-steps"></a>Volgende stappen

[Overzicht](video-indexer-overview.md)
