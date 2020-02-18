---
title: Aanbevolen procedures - QnA Maker
description: Gebruik deze aanbevolen procedures te verbeteren van uw knowledge base en biedt betere resultaten van uw toepassing/chat bot-eindgebruikers.
ms.topic: conceptual
ms.date: 02/15/2020
ms.openlocfilehash: fb935aeed7b492a3a0c213d6d7166bd5d80144c1
ms.sourcegitcommit: f255f869c1dc451fd71e0cab340af629a1b5fb6b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/16/2020
ms.locfileid: "77370095"
---
# <a name="best-practices-of-a-qna-maker-knowledge-base"></a>Best practices van een kennisdatabase QnA Maker

De [Knowledge Base-ontwikkelings levenscyclus](../Concepts/development-lifecycle-knowledge-base.md) helpt u bij het beheren van uw KB van begin tot eind. Gebruik deze aanbevolen procedures om uw Knowledge Base te verbeteren en betere resultaten te bieden aan de eind gebruikers van uw client toepassing of chat-bot.

## <a name="extraction"></a>Extractie

De QnA Maker-service wordt voortdurend verbeteren van de algoritmen die vragen en antwoorden supereenvoudig uitpakken van inhoud en de lijst met ondersteunde bestands- en HTML-indelingen uit te vouwen. Volg de [richt lijnen](../Concepts/content-types.md) voor gegevens extractie op basis van uw document type.

Veelgestelde vragen over pagina's moeten in het algemeen zijn zelfstandige en niet gecombineerd met andere informatie. Producthandleidingen moeten wissen koppen en bij voorkeur een indexpagina hebben.

### <a name="configuring-multi-turn"></a>Multi-turn configureren

[Maak uw Knowledge Base](../how-to/multiturn-conversation.md#create-a-multi-turn-conversation-from-a-documents-structure) met meerdere Schakel extractie ingeschakeld. Als uw Knowledge Base wel of geen vraag hiërarchie ondersteunt, kan deze hiërarchie worden geëxtraheerd uit het document of worden gemaakt nadat het document is geëxtraheerd.

## <a name="creating-good-questions-and-answers"></a>Goede vragen en antwoorden maken

### <a name="good-questions"></a>Goede vragen

De beste vragen zijn eenvoudig. Bekijk het sleutel woord of de woord groep voor elke vraag en maak een eenvoudige vraag voor die sleutel woord of woord groep.

Voeg zoveel alternatieve vragen toe als u nodig hebt, maar behoud de aanpassingen eenvoudig. Het toevoegen van meer woorden of frasen die geen deel uitmaken van het belangrijkste doel van de vraag, helpt niet bij het QnA Maker vinden van een overeenkomst.


### <a name="add-relevant-alternative-questions"></a>Relevante alternatieve vragen toevoegen

Uw gebruiker kan vragen invoeren met een spreek stijl van tekst, `How do I add a toner cartridge to my printer?` of een zoek opdracht op tref woorden zoals `toner cartridge`. De Knowledge Base moet beide stijlen van vragen hebben om het beste antwoord correct te kunnen retour neren. Als u niet zeker weet welke tref woorden een klant invoert, gebruikt u Application Insights gegevens voor het analyseren van query's.

### <a name="good-answers"></a>Goede antwoorden

De beste antwoorden zijn eenvoudige antwoorden, maar niet te eenvoudig. Gebruik geen antwoorden zoals `yes` en `no`. Als uw antwoord moet worden gekoppeld aan andere bronnen of als u een uitgebreide ervaring met media en koppelingen wilt bieden, gebruikt u [labels voor meta gegevens](../how-to/edit-knowledge-base.md#add-metadata) om onderscheid te maken tussen antwoorden. vervolgens [verzendt u de query](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) met de tags voor meta gegevens in de eigenschap `strictFilters` om de juiste antwoord versie te verkrijgen.

|Antwoord|Follup vragen|
|--|--|
|Schakel de Surface-laptop uit met de knop aan/uit op het toetsen bord.|* Toetsen combinaties die in de slaap stand worden gezet, afgesloten en opnieuw starten.<br>* Een oppervlakte laptop hard opstarten<br>* Het BIOS voor een Surface-laptop wijzigen<br>* Verschillen tussen de slaap stand, afsluiten en opnieuw opstarten|
|Klanten service is 24 uur per dag beschikbaar via telefoon-, Skype-en SMS-berichten.|* Neem contact op met de verkoop gegevens.<br> * Kantoor-en opslag locaties en-uren voor een persoonlijk bezoek.<br> * Accessoires voor een Surface-laptop.|

## <a name="chit-chat"></a>Chit Chat
Chit chat toevoegen aan uw bot, maak uw bot aantrekkelijke, en meer conversatie met kleine hoeveelheid werk. U kunt eenvoudig Chit toevoegen van vooraf gedefinieerde persoonlijke gegevens wanneer u uw KB maakt en deze op elk gewenst moment wijzigen. Meer informatie over het [toevoegen van Chit-Chat aan uw KB](../How-To/chit-chat-knowledge-base.md).

Chit: chatten wordt ondersteund in [veel talen](../how-to/chit-chat-knowledge-base.md#language-support).

### <a name="choosing-a-personality"></a>Een persoonlijkheid kiezen
Chit-Chat wordt ondersteund voor verschillende vooraf gedefinieerde persoonlijke voors:

|Persoonlijkheid |QnA Maker Gegevenssetbestand |
|---------|-----|
|Professional |[qna_chitchat_professional. TSV](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_professional.tsv) |
|Weergave |[qna_chitchat_friendly. TSV](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_friendly.tsv) |
|Witty |[qna_chitchat_witty. TSV](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_witty.tsv) |
|Caring |[qna_chitchat_caring. TSV](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_caring.tsv) |
|Enthousiast |[qna_chitchat_enthusiastic. TSV](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_enthusiastic.tsv) |

De antwoorden variëren van formele tot informele en vast. U kunt de persoonlijkheid die dichtst is afgestemd op de toon die u wilt gebruiken voor uw bot moet selecteren. U kunt de [gegevens sets](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets)bekijken en er een selecteren die als basis voor uw bot fungeert en vervolgens de antwoorden aanpassen.

### <a name="edit-bot-specific-questions"></a>Bot-specifieke vragen bewerken
Er zijn enkele bot-specifieke vragen die deel uitmaken van de gegevensset chit chat en algemene antwoorden zijn is ingevuld. Wijzig deze vragen te beantwoorden om de details van uw bot beste weer te geven.

U wordt aangeraden de volgende chit chat vragen en antwoorden supereenvoudig specifieker maken:

* Wie bent u?
* Wat kunt u doen?
* Hoe oud bent u?
* Die u hebt gemaakt?
* Hello

### <a name="adding-custom-chit-chat-with-a-metadata-tag"></a>Aangepaste Chit-Chat toevoegen met een meta gegevens code

Als u uw eigen Chit-Chat QnA-paren toevoegt, moet u ervoor zorgen dat u meta gegevens toevoegt zodat deze antwoorden worden geretourneerd. De naam/waarde-paar van de meta gegevens is `editorial:chitchat`.

## <a name="searching-for-answers"></a>Antwoorden zoeken

GenerateAnswer-API maakt gebruik van beide vragen en het antwoord op zoek naar de beste antwoorden op de query van een gebruiker.

### <a name="searching-questions-only-when-answer-is-not-relevant"></a>Vragen alleen zoeken wanneer antwoord niet relevant is

Gebruik de [`RankerType=QuestionOnly`](#choosing-ranker-type) als u geen antwoorden wilt zoeken.

Een voor beeld hiervan is wanneer de Knowledge Base een catalogus van acroniemen is als vragen met hun volledige formulier als antwoord. De waarde van het antwoord kan niet helpen bij het zoeken naar het juiste antwoord.

## <a name="rankingscoring"></a>Rangschikking/scoren
Zorg ervoor dat u het beste gebruik van de classificatie-functies die biedt ondersteuning voor QnA Maker wilt maken. In dat geval zal de verbeteren de kans dat een bepaalde gebruiker-query wordt beantwoord met een juiste reactie.

### <a name="choosing-a-threshold"></a>Het kiezen van een drempelwaarde

De standaard [betrouwbaarheids Score](confidence-score.md) die wordt gebruikt als drempel waarde is 0, maar u kunt [de drempel waarde](confidence-score.md#set-threshold) voor uw KB wijzigen op basis van uw behoeften. Omdat elke KB anders is, moet u testen en kies de drempelwaarde die is het beste geschikt is voor uw KB.

### <a name="choosing-ranker-type"></a>Type rangorde kiezen
QnA Maker zoekt standaard naar vragen en antwoorden. Als u alleen vragen wilt doorzoeken, kunt u een antwoord genereren met behulp van de `RankerType=QuestionOnly` in de hoofd tekst van de GenerateAnswer-aanvraag.

### <a name="add-alternate-questions"></a>Alternatieve vragen toevoegen
[Alternatieve vragen](../How-To/edit-knowledge-base.md) verbeteren de kans op een overeenkomst met een gebruikers query. Andere vragen zijn nuttig wanneer er zijn meerdere manieren waarop deze vraag kan worden gevraagd. Deze kunt wijzigingen in de zinsstructuur en word-stijl opnemen.

|Oorspronkelijke query|Alternatieve query 's|Wijzigen|
|--|--|--|
|Is de vervangende domeinpagina beschikbaar?|Hebt u Auto-park?|zinsstructuur|
 |Hallo|Yo<br>Hallo er!|Word-stijl en taalgebruik|

<a name="use-metadata-filters"></a>

### <a name="use-metadata-tags-to-filter-questions-and-answers"></a>Tags voor meta gegevens gebruiken voor het filteren van vragen en antwoorden

[Meta gegevens](../How-To/edit-knowledge-base.md) voegen de mogelijkheid toe om een client toepassing te laten weten dat deze niet alle antwoorden mag hebben, maar in plaats daarvan de resultaten van een gebruikers query te beperken op basis van meta gegevenslabels. De knowledge base-antwoord kan verschillen op basis van de metadata-tag, zelfs als de query hetzelfde is. Bijvoorbeeld *' waar bevindt zich parkeren '* kan een ander antwoord hebben als de locatie van de restaurant vertakking anders is, dat wil zeggen, de meta gegevens zijn *Locatie: Seattle* versus *Locatie: Redmond*.

### <a name="use-synonyms"></a>Synoniemen gebruiken
Hoewel er enige ondersteuning is voor synoniemen in de Engelse taal, gebruikt u hoofdletter gevoelige woord wijzigingen via de [aanpassings-API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace) om synoniemen toe te voegen aan tref woorden die een andere vorm hebben. Synoniemen worden toegevoegd op het QnA Maker service niveau en worden gedeeld door alle kennis grondslagen in de service.

|Oorspronkelijke word|Synoniemen|
|--|--|
|Kopen|kopen<br>net-bankieren<br>NET bankieren|

### <a name="use-distinct-words-to-differentiate-questions"></a>Gebruik verschillende woorden te onderscheiden van vragen
Het classificatie algoritme van QnA Maker, dat overeenkomt met een gebruikers query met een vraag in de Knowledge Base, werkt het beste als elke vraag een andere behoefte behandelt. Herhaling van hetzelfde woord in te stellen tussen vragen vermindert de kans dat het juiste antwoord is gekozen voor een bepaalde gebruikersquery met deze woorden.

Bijvoorbeeld, mogelijk hebt u twee afzonderlijke vragen en antwoorden supereenvoudig met de volgende vragen:

|Vragen en antwoorden supereenvoudig|
|--|
|waar bevindt zich de parkeer *locatie*|
|waar is de ATM- *locatie*|

Omdat deze twee QnAs worden aangeduid met zeer vergelijk bare woorden, kan deze soort gelijke scores worden veroorzaakt door veel gebruikers query's die zijn aangeduid als *' waar is de `<x>` locatie '* . Probeer in plaats daarvan duidelijk te onderscheiden van query's zoals *' waar is de parkeer partij '* en *' waar is de ATM '* , door woorden als ' locatie ' te vermijden, zodat u veel vragen in uw KB kunt vinden.

## <a name="collaborate"></a>Samenwerken
Met QnA Maker kunnen gebruikers [samen werken](../How-to/collaborate-knowledge-base.md) aan een Knowledge Base. Gebruikers moeten toegang hebben tot de resourcegroep Azure QnA Maker voor toegang tot knowledge bases. Sommige organisaties willen uitbesteden de kennisdatabase bewerken en het onderhoud, en nog steeds mogelijk om toegang tot hun Azure-resources te beveiligen. Dit model voor redacteur-goed keuring wordt uitgevoerd door twee identieke [QnA Maker Services](../How-to/set-up-qnamaker-service-azure.md) in verschillende abonnementen in te stellen en er een te selecteren voor de bewerking voor het bewerken van de test cyclus. Zodra het testen is voltooid, wordt de inhoud van de Knowledge Base met een [import-export](../Tutorials/migrate-knowledge-base.md) proces overgebracht naar de QnA Maker-service van de goed keurder die de Knowledge Base uiteindelijk gaat publiceren en het eind punt bijwerkt.



## <a name="active-learning"></a>Actief leren

Met het [actieve leer proces](../How-to/improve-knowledge-base.md) wordt geadviseerd om alternatieve vragen te stellen wanneer het een breed scala van kwaliteit en aantal op gebruikers gebaseerde query's heeft. Het is belang rijk om gebruikers query's van client toepassingen toe te staan om deel te nemen aan de feedback-lus van het actieve Learning zonder dat de censoren. Zodra vragen worden voorgesteld in de QnA Maker Portal, kunt u **[filteren op suggesties](../How-To/improve-knowledge-base.md#accept-an-active-learning-suggestion-in-the-knowledge-base)** en deze suggesties bekijken en accepteren of afwijzen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een kennis database bewerken](../How-to/edit-knowledge-base.md)
