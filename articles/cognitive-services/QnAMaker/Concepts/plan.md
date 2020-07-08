---
title: Uw app plannen-QnA Maker
description: Als u uw QnA Maker-app wilt plannen, moet u weten hoe QnA Maker werkt en samenwerkt met andere Azure-Services, evenals een aantal Knowledge Base-concepten.
ms.topic: conceptual
ms.date: 07/2/2020
ms.openlocfilehash: d19ec51aec7e71b6f040a03543f72af3aed09556
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85875707"
---
# <a name="plan-your-qna-maker-app"></a>Uw QnA Maker-app plannen

Als u uw QnA Maker-app wilt plannen, moet u weten hoe QnA Maker werkt en samenwerkt met andere Azure-Services, evenals een aantal Knowledge Base-concepten.

## <a name="azure-resources"></a>Azure-resources

Elke [Azure-resource](azure-resources.md#resource-purposes) die is gemaakt met QnA Maker heeft een specifiek doel. Omdat elke resource een eigen doel, limiet en [prijs categorie](azure-resources.md#pricing-tier-considerations)heeft, is het belang rijk om te begrijpen wat deze resources doen als onderdeel van het plannings proces.

|Resource|Functie|
|--|--|
| [QnA Maker](azure-resources.md#qna-maker-resource) resource|Voor spelling en query's ontwerpen|
| [Cognitive Search](azure-resources.md#cognitive-search-resource) resource|Gegevens opslag en zoeken|
| [Resource en app-plan service resource app service](azure-resources.md#app-service-and-app-service-plan)|Eindpunt queryvoorspelling|
| [Application Insights](azure-resources.md#application-insights) resource|Telemetrie voor spelling query|

### <a name="resource-planning"></a>Resource planning

Tijdens het leren van het ontwerpen en opvragen van query's kunt u gebruikmaken van de gratis laag, `F0` van elke resource en kunt u zowel de ontwerp-als query Voorspellings ervaring opgeven. Wanneer u naar een productie, Live, scenario gaat, moet u de resource selectie opnieuw evalueren.

#### <a name="qna-maker-resource"></a>QnA Maker resource

Eén QnA Maker resource kan meerdere Knowledge bases hosten. Het aantal kennis grondslagen wordt bepaald door het aantal ondersteunde indexen van de Cognitive Search prijs categorie. Meer informatie over de [relatie tussen indexen en kennis bases](azure-resources.md#index-usage).

#### <a name="knowledge-base-size-and-throughput"></a>Grootte van kennis basis en door Voer

Wanneer u van plan bent om een echte app te bouwen, plant u uw resources voor de grootte van uw Knowledge Base en de aanvragen voor de Voorspellings aanvraag die u verwacht.

Een Knowledge Base-grootte wordt bepaald door:
* Limieten voor de prijs categorie voor [Cognitive Search resources](../../../search/search-limits-quotas-capacity.md)
* [QnA Maker limieten](../limits.md)

De aanvraag voor de voor spelling van de Knowledge Base-query wordt bepaald door het web-app-abonnement en de web-app. Raadpleeg de [Aanbevolen instellingen](azure-resources.md#recommended-settings) om uw prijs categorie te plannen.

### <a name="resource-sharing"></a>Resources delen

Als u al een aantal van deze resources in gebruik hebt, kunt u overwegen resources te delen. Hoewel sommige resources [kunnen worden gedeeld](azure-resources.md#share-services-with-qna-maker), is dit een geavanceerd scenario.

Alle Knowledge bases die zijn gemaakt in dezelfde QnA Maker resource delen hetzelfde **test** query Voorspellings eindpunt.

### <a name="understanding-impact-of-resource-selection"></a>Wat is de impact van het selecteren van resources?

De juiste resource selectie betekent dat uw Knowledge Base antwoorden op query voorspellingen heeft beantwoord.

Als uw Knowledge Base niet goed werkt, is het probleem doorgaans onjuist voor het bron beheer.

Voor een onjuiste resource selectie moet worden onderzocht om te bepalen welke [resource moet worden gewijzigd](azure-resources.md#when-to-change-a-pricing-tier).

## <a name="knowledge-bases"></a>Kennis bases

Een kennis database is rechtstreeks verbonden met de QnA Maker resource en bevat de paren vraag en antwoord (QnA) die worden gebruikt voor het beantwoorden van aanvragen voor de voor spelling van query's.

### <a name="language-considerations"></a>Taal overwegingen

De eerste Knowledge Base die is gemaakt op uw QnA Maker resource stelt de taal voor de resource in. U kunt slechts één taal voor een QnA Maker bron hebben.

Structureer uw QnA Maker resources op taal of gebruik [Translator](../../translator/translator-info-overview.md) om een query van een andere taal te wijzigen in de taal van de Knowledge Base voordat u de query naar het eind punt voor query voorspelling verzendt.

### <a name="ingesting-data-sources"></a>Gegevens bronnen opnemen

Opgenomen [gegevens bronnen](knowledge-base.md), die worden gebruikt voor het maken van een Knowledge Base, kunnen een van de volgende zijn:

* Open bare URL
* Persoonlijke share point-URL
* Bestand

Met het opname proces worden [ondersteunde inhouds typen](content-types.md) geconverteerd om te worden geprijsd. Alle verdere bewerking van het *antwoord* geschiedt met prijs verlaging. Nadat u de Knowledge Base hebt gemaakt, kunt u [QnA-paren](question-answer-set.md) in de QnA Maker Portal bewerken met [Rich Text-ontwerp](../how-to/edit-knowledge-base.md#rich-text-editing-for-answer).

### <a name="data-format-considerations"></a>Overwegingen voor gegevens indeling

Omdat de uiteindelijke indeling van een QnA-paar wordt geprijsd, is de ondersteuning van de [prijs](../reference-markdown-format.md) informatie

Gekoppelde installatie kopieën moeten beschikbaar zijn vanaf een open bare URL om weer te geven in het test deel venster van de QnA Maker Portal, maar ook op elke client toepassing, omdat QnA Maker geen verificatie biedt voor inhoud, inclusief installatie kopieën.

### <a name="bot-personality"></a>Bot-persoonlijkheid

Voeg een bot-persoonlijkheid toe aan uw Knowledge Base met [Chit-Chat](../how-to/chit-chat-knowledge-base.md). Deze persoonlijkheid bevindt zich in de antwoorden die zijn opgenomen in een bepaalde gespreks Toon, zoals *professioneel* en *vriendelijk*. Deze Chit-Chat is een set met de volgende stem, die u kunt toevoegen, bewerken en verwijderen.

Een bot-persoonlijkheid wordt aanbevolen als uw bot verbinding maakt met uw Knowledge Base. Als u verbinding maakt met verschillende services, een van de is uw kennis database, kunt u ervoor kiezen om chit te blijven gebruiken in uw Knowledge Base, maar u moet nagaan hoe de bot-service op de hoogte is van het juiste architectuur ontwerp voor uw gebruik.

### <a name="conversation-flow-with-a-knowledge-base"></a>Conversatie stroom met een Knowledge Base

De conversatie stroom begint meestal met een aanhef van een gebruiker, zoals `Hi` of `Hello` . Uw Knowledge Base kan antwoorden op een algemeen antwoord, zoals `Hi, how can I help you` , en het kan ook een selectie van vervolg vragen bieden om door te gaan met de conversatie.

Ontwerp uw gesprek stroom met een lus, zodat een gebruiker weet hoe uw bot moet worden gebruikt en niet wordt afgebroken door de bot in de conversatie. [Opvolgings prompts](../how-to/multiturn-conversation.md) bieden een koppeling tussen QnA-paren, die de conversatie stroom toestaan.

### <a name="authoring-with-collaborators"></a>Ontwerpen met samen werkers

Mede werkers kunnen andere ontwikkel aars zijn die de volledige ontwikkelings stack van de Knowledge Base-toepassing delen of kunnen worden beperkt tot alleen het ontwerpen van de kennis database.

Knowledge Base-ontwerp ondersteunt diverse op [rollen gebaseerde toegangs machtigingen](../how-to/collaborate-knowledge-base.md) die u in de Azure Portal toepast om het bereik van de mogelijkheden van een samen werker te beperken.

## <a name="integration-with-client-applications"></a>Integratie met client toepassingen

Integratie met [client toepassingen](integration-with-other-applications.md) houdt in het verzenden van een query naar het runtime-eind punt voor voor spellingen. Er wordt een query naar uw specifieke Knowledge Base verzonden met een SDK of op REST gebaseerde aanvraag naar het eind punt van de Web-App van uw QnA Maker.

Als u een client aanvraag correct wilt verifiëren, moet de client toepassing de juiste referenties en de Knowledge Base-ID verzenden. Als u een Azure Bot Service gebruikt, configureert u de instelling als onderdeel van de bot-configuratie in de Azure Portal.

### <a name="conversation-flow-in-a-client-application"></a>Conversatie stroom in een client toepassing

De conversatie stroom in een [client toepassing](integration-with-other-applications.md), zoals een Azure-bot, vereist mogelijk functionaliteit voor en na interactie met de Knowledge Base.

Als uw client toepassing conversatie stroom ondersteunt, kunt u een alternatieve methode bieden voor het verwerken van opvolgings prompts of Chit-Chit, deze vroegtijdig ontwerpen en ervoor zorgen dat de query die in de client toepassing wordt gebruikt, correct wordt verwerkt, hetzij door een andere service, hetzij naar uw Knowledge Base.

### <a name="dispatching-between-qna-maker-and-language-understanding-luis"></a>De verzen ding tussen QnA Maker en Language Understanding (LUIS)

Een client toepassing kan verschillende functies bieden, waarvan er slechts één wordt beantwoord door een Knowledge Base. Andere functies moeten nog steeds inzicht krijgen in de tekst van de conversatie en betekenis hiervan uitpakken.

Een gemeen schappelijke client toepassings architectuur is om zowel QnA Maker als [Language Understanding (Luis)](../../LUIS/what-is-luis.md) samen te gebruiken. LUIS biedt de tekst classificatie en-extractie voor elke query, met inbegrip van andere services, terwijl QnA Maker antwoorden van uw Knowledge Base bevat.

In een [gedeelde architectuur](../choose-natural-language-processing-service.md)wordt de [verzen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch) ding tussen de twee services uitgevoerd met het verzend programma van bot Framework.

### <a name="active-learning-from-a-client-application"></a>Actief leren vanuit een client toepassing

QnA Maker maakt gebruik van _actief leren_ om uw Knowledge Base te verbeteren door alternatieve vragen te stellen aan een antwoord. De client toepassing is verantwoordelijk voor een deel van dit [actief onderwijs](active-learning-suggestions.md). Via gespreks prompts van de client toepassing kan worden vastgesteld dat het antwoord dat wordt geretourneerd door de Knowledge Base niet het antwoord is dat de gebruiker op zoek was en het betere antwoord kan vaststellen. De client toepassing moet [die gegevens naar de Knowledge Base verzenden](active-learning-suggestions.md#how-you-give-explicit-feedback-with-the-train-api) om de kwaliteit van de voor spelling te verbeteren.

### <a name="providing-a-default-answer"></a>Een standaard antwoord opgeven

Als uw Knowledge Base geen antwoord heeft gevonden, wordt het _standaard antwoord_geretourneerd. Dit antwoord kan worden geconfigureerd via de QnA Maker Portal, op de pagina **instellingen** of de [api's](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#request-body).

Dit standaard antwoord wijkt af van het standaard antwoord van Azure bot. Het standaard antwoord van Azure bot wordt geconfigureerd in de Azure Portal voor uw bot als onderdeel van de configuratie-instellingen en wordt geretourneerd als er niet aan de Score drempel wordt voldaan.

## <a name="prediction"></a>Voorspelling

De voor spelling is het antwoord van uw Knowledge Base en bevat meer informatie dan alleen het antwoord. Als u een antwoord op een query wilt ontvangen, gebruikt u de [GeneateAnswer-API](query-knowledge-base.md).

### <a name="prediction-score-fluctuations"></a>Schommelingen van de Voorspellings Score

Een score kan worden gewijzigd op basis van verschillende factoren:

* Aantal antwoorden dat u hebt aangevraagd als reactie op [GenerateAnswer](query-knowledge-base.md) met `top` eigenschap
* Diverse beschik bare alternatieve vragen
* Filteren op meta gegevens
* De query is verzonden naar `test` of de `production` Knowledge Base

Er is een [positie in twee fasen](query-knowledge-base.md#how-qna-maker-processes-a-user-query-to-select-the-best-answer):
* Cognitive Search-eerste rang schikking: als u een antwoord wilt ontvangen van Cognitive Search, moet het aantal _antwoorden_ dat is toegestaan hoog genoeg zijn dat de beste antwoorden worden geretourneerd door Cognitive Search zodat deze kunnen worden door gegeven aan de QnA Maker-classificatieer
* QnA Maker-tweede positie-parametrisatie Toep assen en machine learning om het beste antwoord te bepalen.

### <a name="service-updates"></a>Service-updates

Service-updates worden automatisch beheerd door de [meest recente runtime-updates](../how-to/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates)toe te passen.

### <a name="scaling-throughput-and-resiliency"></a>Schalen, door Voer en tolerantie

Schalen, door Voer en tolerantie worden bepaald door de [Azure-resources](../how-to/set-up-qnamaker-service-azure.md), hun prijs categorieën en de bijbehorende architectuur, zoals [Traffic Manager](../how-to/set-up-qnamaker-service-azure.md#business-continuity-with-traffic-manager).

### <a name="analytics-with-application-insights"></a>Analytics met Application Insights

Alle query's voor uw Knowledge Base worden opgeslagen in Application Insights. Gebruik onze [meest voorkomende query's](../how-to/get-analytics-knowledge-base.md) om inzicht te krijgen in uw metrische gegevens.

## <a name="development-lifecycle"></a>Levenscyclus van ontwikkeling

De [ontwikkelings levenscyclus](development-lifecycle-knowledge-base.md) van een Knowledge Base is actief: het bewerken, testen en publiceren van uw kennis database.

### <a name="knowledge-base-development-of-qna-maker-pairs"></a>Knowledge Base-ontwikkeling van QnA Maker paren

Uw [QnA-paren](question-answer-set.md) moeten zijn ontworpen en ontwikkeld op basis van het gebruik van uw client toepassing.

Elk paar kan het volgende bevatten:
* Meta gegevens die worden gefilterd tijdens het uitvoeren van query's. Zo kunt u uw QnA-paren labelen met aanvullende informatie over de bron, de inhoud, de indeling en het doel van uw gegevens.
* Vervolg vragen: Stel een pad op door uw kennis database, zodat de gebruiker bij het juiste antwoord komt.
* Alternatieve vragen: alternatieve vragen zijn belang rijk om ervoor te zorgen dat de zoek opdracht in verschillende vormen van de vraag kan worden afgestemd op uw antwoord. [Actieve Learning suggesties](active-learning-suggestions.md) veranderen in alternatieve vragen.

### <a name="devops-development"></a>DevOps-ontwikkeling

Voor het ontwikkelen van een Knowledge Base voor het invoegen van een DevOps-pijp lijn moet de Knowledge Base tijdens [batch tests](../quickstarts/batch-testing.md)worden geïsoleerd.

Een Knowledge Base deelt de Cognitive Search index met alle andere kennis grondslagen op de QnA Maker resource. Hoewel de Knowledge Base is geïsoleerd per partitie, kan het delen van de index een verschil in de Score veroorzaken in vergelijking met de gepubliceerde kennis database.

Als u _dezelfde score_ wilt hebben op de `test` en `production` kennis bases, moet u een QnA Maker resource isoleren in één kennis database. In deze architectuur hoeft de resource alleen live te zijn voor de lengte van de geïsoleerde batch test.

## <a name="next-step"></a>Volgende stap

* [Azure-resources](../how-to/set-up-qnamaker-service-azure.md)
* [Vraag-en-antwoord-paren](question-answer-set.md)