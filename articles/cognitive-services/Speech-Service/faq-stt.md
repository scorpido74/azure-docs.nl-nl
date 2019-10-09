---
title: Veelgestelde vragen over de spraak-naar-tekst service in azure
titleSuffix: Azure Cognitive Services
description: Krijg antwoorden op de populairste vragen over de spraak-naar-tekst service.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: panosper
ms.openlocfilehash: bde68a70ac047433e86b7e06bc5f4a56bdd28595
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72028506"
---
# <a name="speech-to-text-frequently-asked-questions"></a>Veelgestelde vragen over spraak naar tekst

Als u in deze veelgestelde vragen geen antwoorden op uw vragen kunt vinden, kunt u [Andere ondersteunings opties](support.md)bekijken.

## <a name="general"></a>Algemeen

**V: Wat is het verschil tussen een basislijn model en een aangepaste spraak op tekst model?**

**A**: Een basislijn model is getraind met behulp van gegevens van micro soft en is al geïmplementeerd in de Cloud.  U kunt een aangepast model gebruiken om een model aan te passen aan een specifieke omgeving met specifieke omgevings ruis of-taal. In fabrieks-, auto-of geluids-en geluids eigen straten is een aangepast akoestisch model vereist. Voor onderwerpen als biologie, natuur kunde, radiology, product namen en aangepaste acroniemen zou een aangepast taal model nodig zijn.

**V: Waar kan ik beginnen als ik een basislijn model wil gebruiken?**

**A**: Haal eerst een [abonnements sleutel](get-started.md)op. Als u REST-aanroepen naar de voorgeïmplementeerde basislijn modellen wilt maken, raadpleegt u de [rest api's](rest-apis.md). Als u websockets wilt gebruiken, [downloadt u de SDK](speech-sdk.md).

**V: Moet ik altijd een aangepast spraak model bouwen?**

**A**: Nee. Als uw toepassing gebruikmaakt van een generieke, dagelijkse taal, hoeft u geen model aan te passen. Als uw toepassing wordt gebruikt in een omgeving waar zich weinig of geen achtergrond ruis bevindt, hoeft u geen model aan te passen.

U kunt basis lijnen en aangepaste modellen implementeren in de portal en vervolgens nauw keurige tests uitvoeren. U kunt deze functie gebruiken om de nauw keurigheid van een basislijn model te meten versus een aangepast model.

**V: Hoe weet ik wanneer de verwerking van mijn gegevensset of model is voltooid?**

**A**: Op dit moment is de status van het model of de gegevensset in de tabel de enige manier om te weten. Wanneer de verwerking is voltooid, is de status **geslaagd**.

**V: Kan ik meer dan één model maken?**

**A**: Er is geen limiet voor het aantal modellen dat u kunt hebben in uw verzameling.

**V: Ik heb een fout gemaakt. Hoe kan ik annuleert u het importeren van gegevens of het maken van het model dat wordt uitgevoerd?**

**A**: Op dit moment kunt u een akoestische of taal aanpassings proces niet terugdraaien. U kunt geïmporteerde gegevens en modellen verwijderen wanneer ze zich in een Terminal status bevinden.

**V: Wat is het verschil tussen het zoek-en dicteer model en het gespreks model?**

**A**: U kunt kiezen uit meer dan één basislijn model in de speech-service. Het gespreks model is handig voor het herkennen van spraak die in een gespreks stijl wordt gesp roken. Dit model is ideaal voor het transcriberen van telefoon gesprekken. Het model voor zoeken en dicteren is ideaal voor apps met spraak activering. Het universele model is een nieuw model dat gericht is op beide scenario's. Het universele model bevindt zich momenteel op of boven het kwaliteits niveau van het gespreks model in de meeste landen.

**V: Kan ik mijn bestaande model (model stacking) bijwerken?**

**A**: U kunt een bestaand model niet bijwerken. Als oplossing kunt u de oude gegevensset combi neren met de nieuwe gegevensset en deze opnieuw aanpassen.

De oude gegevensset en de nieuwe gegevensset moeten worden gecombineerd in één ZIP-bestand (voor akoestische gegevens) of in een txt-bestand (voor taal gegevens). Wanneer de aanpassing is voltooid, moet het nieuwe, bijgewerkte model opnieuw worden geïmplementeerd om een nieuw eind punt te verkrijgen

**V: Wanneer een nieuwe versie van een basis lijn beschikbaar is, wordt mijn implementatie automatisch bijgewerkt?**

**A**: Implementaties worden niet automatisch bijgewerkt.

Als u een model hebt aangepast en geïmplementeerd met basis lijn V 1.0, blijft die implementatie ongewijzigd. Klanten kunnen het geïmplementeerde model buiten gebruik stellen, opnieuw aanpassen met de nieuwere versie van de basis lijn en opnieuw implementeren.

**V: Wat moet ik doen als ik een hoger gelijktijdig resultaat heb voor mijn geïmplementeerde model dan in de portal wordt aangeboden?**

**A**: U kunt uw model omhoog schalen in stappen van 20 gelijktijdige aanvragen.

Neem contact op met [spraak ondersteuning](mailto:speechsupport@microsoft.com?subject=Request%20for%20higher%20concurrency%20for%20Speech-to-text) als u een hogere schaal nodig hebt.

**V: Kan ik mijn model downloaden en lokaal uitvoeren?**

**A**: Het is niet mogelijk om modellen te downloaden en lokaal uit te voeren.

**V: Zijn mijn aanvragen vastgelegd?**

**A**: U kunt kiezen wanneer u een implementatie maakt om tracering uit te scha kelen. Op dat moment worden er geen audio-of transcripties vastgelegd. Anders worden aanvragen doorgaans vastgelegd in Azure in beveiligde opslag.

**V: Worden mijn aanvragen beperkt?**

**A**: Het REST API beperkt aanvragen tot 25 per 5 seconden. Meer informatie vindt u op onze pagina's voor [spraak op tekst](speech-to-text.md).

**V: Hoe worden er kosten in rekening gebracht voor Dual Channel audio?**

**A**: Als u elk kanaal afzonderlijk verzendt (elk kanaal in een eigen bestand), wordt u in rekening gebracht volgens de duur van elk bestand. Als u één bestand met elk kanaal hebt verzonden, worden er kosten in rekening gebracht voor de duur van het ene bestand.

> [!IMPORTANT]
> Neem contact op met een van de ondersteunings kanalen als u meer privacy-problemen hebt die verhinderen dat u de aangepaste spraak service kunt gebruiken.

## <a name="importing-data"></a>Gegevens importeren

**V: Wat is de limiet voor de grootte van een gegevensset en waarom is het de limiet?**

**A**: De huidige limiet voor een gegevensset is 2 GB. De limiet wordt veroorzaakt door de beperking van de grootte van een bestand voor HTTP-upload. 

**V: Kan ik mijn tekst bestanden opzip zodat ik een groter tekst bestand kan uploaden?** 

**A**: Nee. Op dit moment zijn alleen niet-gecomprimeerde tekst bestanden toegestaan.

**V: Het gegevens rapport geeft aan dat er mislukte uitingen zijn. Wat is het probleem?**

**A**: Het uploaden van 100 procent van de uitingen in een bestand is geen probleem. Als het meren deel van de uitingen in een akoestische of taal-gegevensset (bijvoorbeeld meer dan 95 procent) is geïmporteerd, kan de gegevensset bruikbaar zijn. We raden u echter aan om te begrijpen waarom de uitingen is mislukt en de problemen op te lossen. De meest voorkomende problemen, zoals opmaak fouten, zijn gemakkelijk te herstellen. 

## <a name="creating-an-acoustic-model"></a>Een akoestische model maken

**V: Hoeveel akoestische gegevens heb ik nodig?**

**A**: We raden u aan om te beginnen met tussen 30 minuten en één uur aan akoestische gegevens.

**V: Welke gegevens moet ik verzamelen?**

**A**: Verzamel gegevens die zich zo dicht mogelijk bij het toepassings scenario behoren en gebruik de situatie. De gegevens verzameling moet overeenkomen met de doel toepassing en gebruikers in termen van apparaten, omgevingen en typen sprekers. Over het algemeen moet u zo veel mogelijk gegevens verzamelen uit een groot aantal luid sprekers. 

**V: Hoe kan ik akoestische gegevens verzamelen?**

**A**: U kunt een zelfstandige toepassing voor het verzamelen van gegevens maken of gebruikmaken van ingebouwde software voor het opnemen van audio. U kunt ook een versie van uw toepassing maken die de audio gegevens registreert en vervolgens de gegevens gebruikt. 

**V: Moet ik de aanpassings gegevens zelf detranscriberen?**

**A**: Ja. U kunt het zelf detranscriberen of een professionele transcriptie-service gebruiken. Sommige gebruikers hebben de voor keur aan professionele transcribers en anderen gebruiken crowdsourcing of de transcripties zelf.

## <a name="accuracy-testing"></a>Nauw keurig testen

**V: Kan ik offline testen van mijn aangepaste akoestische model uitvoeren met behulp van een aangepast taal model?**

**A**: Ja, selecteer het aangepaste taal model in de vervolg keuzelijst bij het instellen van de offline test.

**V: Kan ik offline testen van mijn aangepaste taal model uitvoeren met behulp van een aangepast akoestisch model?**

**A**: Ja, alleen het aangepaste geluids model in de vervolg keuzelijst selecteren wanneer u de offline test instelt.

**V: Wat is een Word-fout snelheid (WER) en hoe wordt deze berekend?**

**A**: WER is de evaluatie-metric voor spraak herkenning. WER wordt geteld als het totale aantal fouten, inclusief invoegingen, verwijderingen en vervangingen, gedeeld door het totale aantal woorden in de verwijzings transcriptie. Zie [Word-fout frequentie](https://en.wikipedia.org/wiki/Word_error_rate)voor meer informatie.

**V: Hoe kan ik bepalen of de resultaten van een nauw keurige test goed zijn?**

**A**: In de resultaten ziet u een vergelijking tussen het basis model en het model dat u hebt aangepast. U moet zich richten op het basis model om aanpassing van de voor keur te maken.

**V: Hoe kan ik de WER van een basis model bepalen zodat ik kan zien of er een verbetering is opgetreden?** 

**A**: De resultaten van de offline test tonen de nauw keurigheid van het aangepaste model en de verbetering ten opzichte van de basis lijn.

## <a name="creating-a-language-model"></a>Een taal model maken

**V: Hoeveel tekst gegevens heb ik nodig om te uploaden?**

**A**: Het hangt af van de manier waarop de woorden lijst en zinsdelen die in uw toepassing worden gebruikt, afkomstig zijn uit de start taal modellen. Voor alle nieuwe woorden is het handig om zo veel mogelijk voor beelden te bieden van het gebruik van deze woorden. Voor veelvoorkomende woord groepen die worden gebruikt in uw toepassing, met inbegrip van zinsdelen in de taal gegevens, is ook nuttig omdat het systeem ook kan Luis teren naar deze voor waarden. Het is gebruikelijk om ten minste 100 en meestal honderden of meer uitingen in de taal-gegevensset te hebben. Als sommige typen query's waarschijnlijk vaker worden gebruikt dan andere, kunt u meerdere exemplaren van de algemene query's in de gegevensset invoegen.

**V: Kan ik gewoon een lijst met woorden uploaden?**

**A**: Als u een lijst met woorden uploadt, worden de woorden toegevoegd aan de vocabulaire, maar wordt het systeem niet leren hoe de woorden doorgaans worden gebruikt. Door volledige of gedeeltelijke uitingen (zinnen of zinsdelen te bieden van dingen die gebruikers waarschijnlijk zeggen), kan het taal model de nieuwe woorden en hoe ze worden gebruikt, leren. Het aangepaste taal model is niet alleen geschikt voor het toevoegen van nieuwe woorden aan het systeem, maar ook voor het aanpassen van de kans op bekende woorden voor uw toepassing. Het bieden van een volledige uitingen helpt het systeem beter te leren. 

## <a name="next-steps"></a>Volgende stappen

* [Problemen oplossen](troubleshooting.md)
* [Releaseopmerkingen](releasenotes.md)
