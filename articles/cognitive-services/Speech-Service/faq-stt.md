---
title: Veelgestelde vragen over spraak naar tekst
titleSuffix: Azure Cognitive Services
description: Krijg antwoorden op veelgestelde vragen over de spraak-naar-tekstservice.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/4/2019
ms.author: panosper
ms.openlocfilehash: a279aebdd19ebd3a41ddad0c1c279937e00838c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77168463"
---
# <a name="speech-to-text-frequently-asked-questions"></a>Veelgestelde vragen over spraak naar tekst

Als u geen antwoorden op uw vragen vinden in deze veelgestelde vragen, raadpleegt u [andere ondersteuningsopties.](support.md)

## <a name="general"></a>Algemeen

**V: Wat is het verschil tussen een basislijnmodel en een aangepast speech-to-tekstmodel?**

**A:** Een basislijnmodel is getraind met gegevens die eigendom zijn van Microsoft en is al geïmplementeerd in de cloud. U een aangepast model gebruiken om een model aan te passen aan een specifieke omgeving met specifieke omgevingsgeluid of taal. Fabrieksvloeren, auto's of lawaaierige straten zouden een aangepast akoestisch model vereisen. Onderwerpen als biologie, natuurkunde, radiologie, productnamen en aangepaste afkortingen zouden een aangepast taalmodel vereisen.

**V: Waar moet ik beginnen als ik een basislijnmodel wil gebruiken?**

**A:** Ten eerste, krijg een [abonnementssleutel](get-started.md). Zie de [REST API's](rest-apis.md)als u REST-aanroepen naar de vooraf geïmplementeerde basislijnmodellen. Als u WebSockets wilt gebruiken, [downloadt u de SDK](speech-sdk.md).

**V: Moet ik altijd een aangepast spraakmodel bouwen?**

**A:** Nee. Als uw toepassing algemene taal van dag tot dag gebruikt, hoeft u een model niet aan te passen. Als uw toepassing wordt gebruikt in een omgeving waar weinig of geen achtergrondgeluid is, hoeft u een model niet aan te passen.

U basislijn- en aangepaste modellen implementeren in de portal en vervolgens nauwkeurigheidstests tegen deze modellen uitvoeren. U deze functie gebruiken om de nauwkeurigheid van een basislijnmodel ten opzichte van een aangepast model te meten.

**V: Hoe weet ik wanneer de verwerking voor mijn gegevensset of model is voltooid?**

**A**: Momenteel is de status van het model of de gegevensset in de tabel de enige manier om het te weten. Wanneer de verwerking is voltooid, wordt de status **opgevolgd.**

**V: Kan ik meer dan één model maken?**

**A:** Er is geen limiet aan het aantal modellen dat je in je collectie hebben.

**V: Ik realiseerde me dat ik een fout heb gemaakt. Hoe annuleer ik het importeren van gegevens of het maken van een model dat aan de gang is?**

**A**: Op dit moment u een akoestisch of taalaanpassingsproces niet terugdraaien. U geïmporteerde gegevens en modellen verwijderen wanneer ze in een terminalstatus zijn.

**V: Wat is het verschil tussen het zoek- en dicteermodel en het conversational-model?**

**A:** U kiezen uit meer dan één basislijnmodel in de spraakservice. Het conversationele model is handig voor het herkennen van spraak die wordt gesproken in een conversationele stijl. Dit model is ideaal voor het transcriberen van telefoongesprekken. Het zoek- en dicteermodel is ideaal voor spraakgestuurde apps. Het Universal-model is een nieuw model dat beide scenario's wil aanpakken. Het Universele model bevindt zich momenteel op of boven het kwaliteitsniveau van het Conversational-model in de meeste landlocaties.

**V: Kan ik mijn bestaande model bijwerken (modelstapelen)?**

**A:** U een bestaand model niet bijwerken. Als oplossing combineer je de oude dataset met de nieuwe dataset en pas je je aan.

De oude gegevensset en de nieuwe gegevensset moeten worden gecombineerd in één .zip-bestand (voor akoestische gegevens) of in een .txt-bestand (voor taalgegevens). Wanneer de aanpassing is voltooid, moet het nieuwe, bijgewerkte model opnieuw worden geïmplementeerd om een nieuw eindpunt te verkrijgen

**V: Wanneer een nieuwe versie van een basislijn beschikbaar is, wordt mijn implementatie automatisch bijgewerkt?**

**A**: Implementaties worden NIET automatisch bijgewerkt.

Als u een model met baseline V1.0 hebt aangepast en geïmplementeerd, blijft die implementatie zoals het is. Klanten kunnen het geïmplementeerde model buiten gebruik stellen, aanpassen met de nieuwere versie van de basislijn en opnieuw implementeren.

**V: Kan ik mijn model downloaden en lokaal uitvoeren?**

**A:** Modellen kunnen niet lokaal worden gedownload en uitgevoerd.

**V: Worden mijn verzoeken geregistreerd?**

**A:** U hebt een keuze wanneer u een implementatie maakt om tracering uit te schakelen. Op dat moment worden er geen audio of transcripties geregistreerd. Anders worden aanvragen meestal aangemeld in Azure in beveiligde opslag.

**V: Worden mijn verzoeken beperkt?**

**A:** De REST API beperkt aanvragen tot 25 per 5 seconden. Details zijn te vinden in onze pagina's voor [Spraak naar tekst](speech-to-text.md).

**V: Hoe worden er kosten in rekening gebracht voor dual channel audio?**

**A:** Als u elk kanaal afzonderlijk indient (elk kanaal in zijn eigen bestand), worden er kosten in rekening gebracht voor de duur van elk bestand. Als u één bestand indient waarbij elk kanaal samen wordt gemultiplexed, worden er kosten in rekening gebracht voor de duur van het ene bestand. Zie voor meer informatie over de prijzen de [prijspagina azure cognitive services.](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)

> [!IMPORTANT]
> Als u nog meer privacyproblemen hebt die u verbieden de aangepaste spraakservice te gebruiken, neemt u contact op met een van de ondersteuningskanalen.

## <a name="increasing-concurrency"></a>Toenemende gelijktijdigheid

**V: Wat als ik meer gelijktijdigheid nodig heb voor mijn geïmplementeerde model dan wat er in de portal wordt aangeboden?**

**A:** U uw model opschalen in stappen van 20 gelijktijdige aanvragen.

Maak met de vereiste informatie een ondersteuningsaanvraag in de [Azure-ondersteuningsportal.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) Plaats de informatie niet op een van de openbare kanalen (GitHub, Stackoverflow, ...) vermeld op de [support pagina](support.md).

Om de gelijktijdigheid voor een ***aangepast model te***verhogen, hebben we de volgende informatie nodig:

- De regio waar het model wordt geïmplementeerd,
- de eindpunt-id van het geïmplementeerde model:
  - Ga naar de [Custom Speech Portal,](https://aka.ms/customspeech)
  - inloggen (indien nodig),
  - selecteer uw project en implementatie,
  - selecteer het eindpunt waarvoor u de gelijktijdigheidsverhoging nodig hebt,
  - kopieer `Endpoint ID`de .

Om de gelijktijdigheid voor een ***basismodel***te verhogen, hebben we de volgende informatie nodig:

- De regio van uw dienst,

en ofwel

- een toegangstoken voor uw abonnement (zie [hier),](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-speech-to-text#how-to-get-an-access-token)

of

- de Resource-id voor uw abonnement:
  - Ga naar de [Azure-portal,](https://portal.azure.com)
  - selecteren `Cognitive Services` in het zoekvak,
  - van de weergegeven services kiest u de spraakservice waarvoor u de gelijktijdigheid wilt verhogen,
  - voor `Properties` deze dienst weer te geven,
  - de volledige `Resource ID`kopie .

## <a name="importing-data"></a>Gegevens importeren

**V: Wat is de limiet voor de grootte van een gegevensset en waarom is het de limiet?**

**A:** De huidige limiet voor een gegevensset is 2 GB. De limiet is te wijten aan de beperking van de grootte van een bestand voor HTTP-upload.

**V: Kan ik mijn tekstbestanden zippen zodat ik een groter tekstbestand kan uploaden?**

**A:** Nee. Momenteel zijn alleen niet-gecomprimeerde tekstbestanden toegestaan.

**V: In het gegevensrapport staat dat er geen uitingen zijn. Wat is het probleem?**

**A:** Het niet uploaden van 100 procent van de uitingen in een bestand is geen probleem. Als de overgrote meerderheid van de uitingen in een akoestische of taalgegevensset (bijvoorbeeld meer dan 95 procent) met succes wordt geïmporteerd, kan de gegevensset bruikbaar zijn. We raden u echter aan te proberen te begrijpen waarom de uitingen zijn mislukt en de problemen op te lossen. De meest voorkomende problemen, zoals opmaakfouten, zijn eenvoudig op te lossen.

## <a name="creating-an-acoustic-model"></a>Een akoestisch model maken

**V: Hoeveel akoestische gegevens heb ik nodig?**

**A**: We raden aan om te beginnen met tussen de 30 minuten en een uur aan akoestische gegevens.

**V: Welke gegevens moet ik verzamelen?**

**A**: Verzamel gegevens die zo dicht mogelijk bij het toepassingsscenario en de use case mogelijk zijn. De gegevensverzameling moet overeenkomen met de doeltoepassing en gebruikers in termen van apparaat of apparaten, omgevingen en typen luidsprekers. In het algemeen moet u gegevens verzamelen van een zo breed mogelijk scala aan sprekers.

**V: Hoe moet ik akoestische gegevens verzamelen?**

**A:** U een zelfstandige toepassing voor het verzamelen van gegevens maken of kant-en-klare audio-opnamesoftware gebruiken. U ook een versie van uw toepassing maken die de audiogegevens registreert en vervolgens de gegevens gebruikt.

**V: Moet ik zelf aanpassingsgegevens transcriberen?**

**A:** Ja! U het zelf transcriberen of gebruik maken van een professionele transcriptieservice. Sommige gebruikers geven de voorkeur aan professionele transcribers en anderen gebruiken crowdsourcing of doen de transcripties zelf.

## <a name="accuracy-testing"></a>Nauwkeurigheidstests

**V: Kan ik offline testen van mijn aangepaste akoestische model uitvoeren met behulp van een aangepast taalmodel?**

**A:** Ja, selecteer het aangepaste taalmodel in het vervolgkeuzemenu wanneer u de offlinetest instelt.

**V: Kan ik offline testen van mijn aangepaste taalmodel uitvoeren met behulp van een aangepast akoestisch model?**

**A:** Ja, selecteer gewoon het aangepaste akoestische model in het vervolgkeuzemenu wanneer u de offline test instelt.

**V: Wat is word error rate (WER) en hoe wordt het berekend?**

**A**: WER is de evaluatiestatistiek voor spraakherkenning. WER wordt geteld als het totale aantal fouten, waaronder invoegingen, verwijderingen en vervangingen, gedeeld door het totale aantal woorden in de referentietranscriptie. Zie [woordfoutenpercentage](https://en.wikipedia.org/wiki/Word_error_rate)voor meer informatie .

**V: Hoe bepaal ik of de resultaten van een nauwkeurigheidstest goed zijn?**

**A:** De resultaten tonen een vergelijking tussen het basislijnmodel en het model dat u hebt aangepast. Je moet ernaar streven om het basislijnmodel te verslaan om maatwerk de moeite waard te maken.

**V: Hoe bepaal ik de WER van een basismodel zodat ik kan zien of er een verbetering is opgetreden?**

**A:** De offline testresultaten tonen de basislijnnauwkeurigheid van het aangepaste model en de verbetering ten opzichte van de basislijn.

## <a name="creating-a-language-model"></a>Een taalmodel maken

**V: Hoeveel tekstgegevens moet ik uploaden?**

**A:** Het hangt af van hoe verschillend de woordenschat en zinnen die in uw toepassing worden gebruikt zijn van de beginnende taalmodellen. Voor alle nieuwe woorden is het handig om zoveel mogelijk voorbeelden te geven van het gebruik van die woorden. Voor veelvoorkomende zinnen die in uw toepassing worden gebruikt, is het ook handig om zinnen in de taalgegevens op te nemen, omdat het systeem ook naar deze termen moet luisteren. Het is gebruikelijk om ten minste 100, en meestal enkele honderden of meer uitingen in de taalgegevensset. Als sommige typen query's naar verwachting vaker voorkomen dan andere, u ook meerdere kopieën van de algemene query's in de gegevensset invoegen.

**V: Kan ik gewoon een lijst met woorden uploaden?**

**A:** Als u een lijst met woorden uploadt, worden de woorden toegevoegd aan de woordenschat, maar het zal het systeem niet leren hoe de woorden meestal worden gebruikt. Door volledige of gedeeltelijke uitingen (zinnen of zinnen van dingen die gebruikers waarschijnlijk zullen zeggen), kan het taalmodel de nieuwe woorden leren en hoe ze worden gebruikt. Het aangepaste taalmodel is niet alleen goed voor het toevoegen van nieuwe woorden aan het systeem, maar ook voor het aanpassen van de waarschijnlijkheid van bekende woorden voor uw toepassing. Het verstrekken van volledige uitingen helpt het systeem beter te leren.

## <a name="tenant-model-custom-speech-with-office-365-data"></a>Tenantmodel (aangepaste spraak met Office 365-gegevens)

**V: Welke informatie is opgenomen in het tenantmodel en hoe wordt deze gemaakt?**

**A:** Een tenantmodel is gemaakt met behulp van [openbare groepse-mails](https://support.office.com/article/learn-about-office-365-groups-b565caa1-5c40-40ef-9915-60fdb2d97fa2) en documenten die door iedereen in uw organisatie kunnen worden gezien.

**V: Welke spraakervaringen worden verbeterd door het Tenant-model?**

**A:** Wanneer het tenantmodel is ingeschakeld, gemaakt en gepubliceerd, wordt het gebruikt om de herkenning te verbeteren voor alle bedrijfstoepassingen die zijn gebouwd met behulp van de spraakservice; die ook een aad-token van de gebruiker doorgeeft die het lidmaatschap aan de onderneming aangeeft.

De spraakervaringen die zijn ingebouwd in Office 365, zoals Dicteren en PowerPoint-ondertiteling, worden niet gewijzigd wanneer u een tenantmodel maakt voor uw spraakservicetoepassingen.

## <a name="next-steps"></a>Volgende stappen

- [Probleemoplossing](troubleshooting.md)
- [Opmerkingen vrijgeven](releasenotes.md)
