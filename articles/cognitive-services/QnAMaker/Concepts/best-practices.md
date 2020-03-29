---
title: Best practices - QnA Maker
description: Gebruik deze aanbevolen procedures om uw kennisbank te verbeteren en betere resultaten te bieden aan de eindgebruikers van uw applicatie/chatbot.
ms.topic: conceptual
ms.date: 02/15/2020
ms.openlocfilehash: 9a6f7f7d6edc4544942476050a1ed3c2011af7fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80053134"
---
# <a name="best-practices-of-a-qna-maker-knowledge-base"></a>Best practices van een Kennisbank QnA Maker

De [knowledge base development lifecycle](../Concepts/development-lifecycle-knowledge-base.md) begeleidt u bij het beheren van uw KB van begin tot eind. Gebruik deze best practices om uw kennisbank te verbeteren en betere resultaten te bieden aan de eindgebruikers van uw clienttoepassing of chatbot.

## <a name="extraction"></a>Extractie

De QnA Maker-service verbetert voortdurend de algoritmen die QnA's uit inhoud halen en breidt de lijst met ondersteunde bestands- en HTML-indelingen uit. Volg de [richtlijnen](../Concepts/content-types.md) voor gegevensextractie op basis van uw documenttype.

In het algemeen moeten FAQ-pagina's op zichzelf staan en niet worden gecombineerd met andere informatie. Producthandleidingen moeten duidelijke koppen en bij voorkeur een indexpagina hebben.

### <a name="configuring-multi-turn"></a>Multi-turn configureren

[Creëer je kennisbank](../how-to/multiturn-conversation.md#create-a-multi-turn-conversation-from-a-documents-structure) met multi-turn extractie ingeschakeld. Als uw kennisbank de vraaghiërarchie wel of juist ondersteunt, kan deze hiërarchie uit het document worden gehaald of worden gemaakt nadat het document is geëxtraheerd.

## <a name="creating-good-questions-and-answers"></a>Goede vragen en antwoorden creëren

### <a name="good-questions"></a>Goede vragen

De beste vragen zijn eenvoudig. Overweeg het sleutelwoord of de zin voor elke vraag en maak vervolgens een eenvoudige vraag voor dat sleutelwoord of die zin.

Voeg zoveel alternatieve vragen als je nodig hebt, maar houd de wijzigingen eenvoudig. Het toevoegen van meer woorden of formuleringen die geen deel uitmaken van het belangrijkste doel van de vraag helpt QnA Maker niet om een overeenkomst te vinden.


### <a name="add-relevant-alternative-questions"></a>Relevante alternatieve vragen toevoegen

Uw gebruiker kan vragen invoeren met een `How do I add a toner cartridge to my printer?` gespreksstijl van `toner cartridge`tekst of een trefwoordzoekopdracht zoals . De kennisbank moet beide stijlen van vragen hebben om het beste antwoord correct terug te geven. Als u niet zeker weet welke zoekwoorden een klant invoert, gebruikt u Toepassingsstatistiekengegevens om query's te analyseren.

### <a name="good-answers"></a>Goede antwoorden

De beste antwoorden zijn eenvoudige antwoorden, maar niet te eenvoudig. Gebruik geen antwoorden `yes` zoals `no`en . Als uw antwoord moet linken naar andere bronnen of een rijke ervaring met media en links biedt, `strictFilters` gebruikt u [metagegevens om](../how-to/edit-knowledge-base.md#add-metadata) onderscheid te maken tussen antwoorden en verzendt u de [query](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) met metagegevenstags in de eigenschap om de juiste antwoordversie te krijgen.

|Antwoord|Follup-up prompts|
|--|--|
|Sluit de Surface-laptop uit met de aan/uit-knop op het toetsenbord.|* Sleutelcombinaties om te slapen, af te sluiten en opnieuw op te starten.<br>* Hoe maak je hard-boot een Surface laptop<br>* Hoe verander je het BIOS voor een Surface-laptop<br>* Verschillen tussen slaap, afsluiten en opnieuw opstarten|
|De klantenservice is 24 uur per dag bereikbaar via telefoon, Skype en sms.|* Contactgegevens voor verkoop.<br> * Kantoor- en winkellocaties en uren voor een persoonlijk bezoek.<br> * Accessoires voor een Surface-laptop.|

## <a name="chit-chat"></a>Chit-Chat Chit-Chat
Voeg chit-chat toe aan je bot, om je bot meer conversatie en boeiendheid te maken, met een lage inspanning. U eenvoudig chit-chat-gegevenssets toevoegen van vooraf gedefinieerde persoonlijkheden bij het maken van uw KB en deze op elk gewenst moment wijzigen. Meer informatie over het [toevoegen van chit-chat aan uw KB.](../How-To/chit-chat-knowledge-base.md)

Chit-chat wordt ondersteund in [vele talen.](../how-to/chit-chat-knowledge-base.md#language-support)

### <a name="choosing-a-personality"></a>Het kiezen van een persoonlijkheid
Chit-chat wordt ondersteund voor verschillende vooraf gedefinieerde persoonlijkheden:

|Persoonlijkheid |QnA Maker-gegevenssetbestand |
|---------|-----|
|Professional |[qna_chitchat_professional.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_professional.tsv) |
|Vriendelijke |[qna_chitchat_friendly.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_friendly.tsv) |
|Witty |[qna_chitchat_witty.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_witty.tsv) |
|Zorg |[qna_chitchat_caring.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_caring.tsv) |
|Enthousiast |[qna_chitchat_enthusiastic.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_enthusiastic.tsv) |

De antwoorden variëren van formeel tot informeel en oneerbiedig. U moet de persoonlijkheid selecteren die het dichtst is afgestemd op de toon die u wilt voor uw bot. U de [gegevenssets](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets)bekijken en een set kiezen die als basis voor uw bot dient en vervolgens de antwoorden aanpassen.

### <a name="edit-bot-specific-questions"></a>Botspecifieke vragen bewerken
Er zijn een aantal bot-specifieke vragen die deel uitmaken van de chit-chat data set, en zijn ingevuld met generieke antwoorden. Wijzig deze antwoorden om uw botgegevens het beste weer te geven.

We raden u aan de volgende chit-chat QnAs specifieker te maken:

* Wie ben jij?
* Wat kun je doen?
* How old are you?
* Wie heeft jou geschapen?
* Hello

### <a name="adding-custom-chit-chat-with-a-metadata-tag"></a>Aangepaste chit-chat toevoegen met een metadatatag

Als u uw eigen chit-chat QnA paren toevoegt, zorg ervoor dat metadata toe te voegen, zodat deze antwoorden worden geretourneerd. Het metagegevensnaam/waardepaar is `editorial:chitchat`.

## <a name="searching-for-answers"></a>Zoeken naar antwoorden

GenerateAnswer API gebruikt zowel vragen als het antwoord om te zoeken naar de beste antwoorden op de zoekopdracht van een gebruiker.

### <a name="searching-questions-only-when-answer-is-not-relevant"></a>Alleen vragen zoeken als antwoord niet relevant is

Gebruik [`RankerType=QuestionOnly`](#choosing-ranker-type) de als je niet wilt antwoorden zoeken.

Een voorbeeld hiervan is wanneer de kennisbank is een catalogus van afkortingen als vragen met hun volledige vorm als het antwoord. De waarde van het antwoord zal niet helpen om te zoeken naar het juiste antwoord.

## <a name="rankingscoring"></a>Ranking/scoren
Zorg ervoor dat u optimaal gebruik maakt van de classificatiefuncties die QnA Maker ondersteunt. Hierdoor wordt de kans verbeterd dat een bepaalde gebruikersquery wordt beantwoord met een passend antwoord.

### <a name="choosing-a-threshold"></a>Een drempel kiezen

De [standaardbetrouwbaarheidsscore](confidence-score.md) die als drempelwaarde wordt gebruikt, is 0, maar u [de drempelwaarde](confidence-score.md#set-threshold) voor uw KB wijzigen op basis van uw behoeften. Aangezien elke KB anders is, moet u de drempel testen en kiezen die het meest geschikt is voor uw KB.

### <a name="choosing-ranker-type"></a>Ranker-type kiezen
QnA Maker doorzoekt standaard vragen en antwoorden. Als u alleen door vragen wilt zoeken om `RankerType=QuestionOnly` een antwoord te genereren, gebruikt u de post-hoofd van het verzoek generateanswer.

### <a name="add-alternate-questions"></a>Alternatieve vragen toevoegen
[Alternatieve vragen](../How-To/edit-knowledge-base.md) verbeteren de kans op een overeenkomst met een gebruikersquery. Alternatieve vragen zijn handig wanneer er meerdere manieren zijn waarop dezelfde vraag kan worden gesteld. Dit kunnen wijzigingen in de zinsstructuur en woordstijl omvatten.

|Oorspronkelijke query|Alternatieve query's|Wijzigen|
|--|--|--|
|Is parkeren mogelijk?|Heeft u een parkeerplaats?|zinsstructuur|
 |Hallo|Yo<br>Hey daar!|woordstijl of slang|

<a name="use-metadata-filters"></a>

### <a name="use-metadata-tags-to-filter-questions-and-answers"></a>Metagegevenstags gebruiken om vragen en antwoorden te filteren

[Metagegevens](../How-To/edit-knowledge-base.md) voegt de mogelijkheid voor een clienttoepassing om te weten dat het niet alle antwoorden moet nemen, maar in plaats daarvan om de resultaten van een gebruikersquery te beperken op basis van metadatatags. Het antwoord van de kennisbank kan verschillen op basis van de metadatatag, zelfs als de query hetzelfde is. Bijvoorbeeld, *"waar is parkeren gelegen"* kan een ander antwoord hebben als de locatie van het restaurant tak is anders - dat wil zeggen, de metadata is *Locatie: Seattle* versus *Locatie: Redmond*.

### <a name="use-synonyms"></a>Synoniemen gebruiken
Hoewel er enige ondersteuning is voor synoniemen in het Engels, gebruikt u hoofdletter-ongevoelige woordwijzigingen via de [Api Wijzigingen](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace) om synoniemen toe te voegen aan zoekwoorden die een andere vorm aannemen. Synoniemen worden toegevoegd op QnA Maker service-niveau en gedeeld door alle kennisbanken in de dienst.

|Origineel woord|Synoniemen|
|--|--|
|Kopen|kopen<br>net-banking<br>netto bankieren|

### <a name="use-distinct-words-to-differentiate-questions"></a>Afzonderlijke woorden gebruiken om vragen te differentiëren
QnA Maker's ranking algoritme, dat een gebruikersquery matcht met een vraag in de kennisbank, werkt het beste als elke vraag een andere behoefte aangaat. Herhaling van hetzelfde woord dat tussen vragen is ingesteld, vermindert de kans dat het juiste antwoord wordt gekozen voor een bepaalde gebruikersquery met die woorden.

U bijvoorbeeld twee afzonderlijke QnA's hebben met de volgende vragen:

|QnA's|
|--|
|waar is de *parkeerlocatie*|
|waar is de *ATM-locatie*|

Aangezien deze twee QnA's zijn geformuleerd met zeer vergelijkbare woorden, deze gelijkenis kan leiden tot zeer vergelijkbare scores voor veel gebruikersquery's die worden geformuleerd als *"waar is `<x>` de locatie"*. In plaats daarvan proberen om duidelijk te onderscheiden met vragen als *"waar is de parkeerplaats"* en *"waar is de atm"*, door het vermijden van woorden als "locatie" dat zou kunnen worden in veel vragen in uw KB.

## <a name="collaborate"></a>Samenwerken
QnA Maker stelt gebruikers in staat om samen te [werken](../How-to/collaborate-knowledge-base.md) aan een kennisbank. Gebruikers hebben toegang nodig tot de Azure QnA Maker-brongroep om toegang te krijgen tot de kennisbases. Sommige organisaties willen de bewerken en het onderhoud van de kennisbasis mogelijk uitbesteden en kunnen nog steeds de toegang tot hun Azure-bronnen beveiligen. Dit editor-approver-model wordt gedaan door twee identieke [QnA Maker-services](../How-to/set-up-qnamaker-service-azure.md) in verschillende abonnementen in te stellen en er een te selecteren voor de bewerkingscyclus. Zodra het testen is voltooid, wordt de inhoud van de knowledge base overgedragen met een [import-exportproces](../Tutorials/migrate-knowledge-base.md) naar de QnA Maker-service van de goedkeurder die uiteindelijk de kennisbank publiceert en het eindpunt bijwerkt.



## <a name="active-learning"></a>Actief leren

[Actief leren](../How-to/use-active-learning.md) doet het beste werk van het suggereren van alternatieve vragen wanneer het een breed scala van kwaliteit en kwantiteit van de gebruiker op basis van query's heeft. Het is belangrijk om gebruikersquery's van client-applicaties zonder censuur toe te staan aan de actieve leerfeedbacklus. Zodra vragen worden voorgesteld in de QnA Maker-portal, u **[filteren op suggesties](../How-To/improve-knowledge-base.md#accept-an-active-learning-suggestion-in-the-knowledge-base)** en deze suggesties vervolgens bekijken en accepteren of afwijzen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een knowledge base bewerken](../How-to/edit-knowledge-base.md)
