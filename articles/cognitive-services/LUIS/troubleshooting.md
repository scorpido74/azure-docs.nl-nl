---
title: Veelgestelde vragen (FAQ) - LUIS
description: Dit artikel bevat antwoorden op veelgestelde vragen over Taalbegrip (LUIS).
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: diberry
ms.openlocfilehash: 59d9b1f21e1936b7d03293ec3d338677380a7c99
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81530229"
---
# <a name="language-understanding-frequently-asked-questions-faq"></a>Veelgestelde vragen over het begrijpen van veelgestelde vragen (taalverstaan)

Dit artikel bevat antwoorden op veelgestelde vragen over Taalbegrip (LUIS).

## <a name="whats-new"></a>Nieuwe functies

[Meer informatie](whats-new.md) over wat er nieuw is in Language Understanding (LUIS).

<a name="luis-authoring"></a>

## <a name="authoring"></a>Ontwerpen

### <a name="what-are-the-luis-best-practices"></a>Wat zijn de LUIS best practices?
Begin met [de ontwerpcyclus](luis-concept-app-iteration.md)en lees vervolgens de [aanbevolen procedures](luis-concept-best-practices.md).

### <a name="what-is-the-best-way-to-start-building-my-app-in-luis"></a>Wat is de beste manier om mijn app in LUIS te bouwen?

De beste manier om uw app te bouwen is via een [incrementeel proces.](luis-concept-app-iteration.md)

### <a name="what-is-a-good-practice-to-model-the-intents-of-my-app-should-i-create-more-specific-or-more-generic-intents"></a>Wat is een goede gewoonte om de intenties van mijn app te modelleren? Moet ik meer specifieke of meer generieke intenties maken?

Kies intenties die niet zo algemeen zijn als overlappend, maar niet zo specifiek dat het voor LUIS moeilijk is om onderscheid te maken tussen vergelijkbare intenties. Het maken van discriminerende specifieke intents is een van de best practices voor LUIS-modellering.

### <a name="is-it-important-to-train-the-none-intent"></a>Is het belangrijk om de Intentie van Geen te trainen?

Ja, het is goed om uw **Intentie geen** te trainen met meer uitingen terwijl u meer labels toevoegt aan andere intenties. Een goede verhouding is 1 of 2 labels toegevoegd aan **Geen** voor elke 10 labels toegevoegd aan een intentie. Deze verhouding verhoogt de discriminerende kracht van LUIS.

### <a name="how-can-i-correct-spelling-mistakes-in-utterances"></a>Hoe kan ik spelfouten in uitingen corrigeren?

Zie de zelfstudie [van Bing Spell Check API V7.](luis-tutorial-bing-spellcheck.md) LUIS handhaaft limieten opgelegd door Bing Spell Check API V7.

### <a name="how-do-i-edit-my-luis-app-programmatically"></a>Hoe kan ik mijn LUIS-app programmatisch bewerken?
Als u uw LUIS-app programmatisch wilt bewerken, gebruikt u de [auteursrechten-API](https://go.microsoft.com/fwlink/?linkid=2092087). Zie [Call LUIS authoring API](./get-started-get-model-rest-apis.md) en Build a LUIS app programmatisch met Behulp van [Node.js](./luis-tutorial-node-import-utterances-csv.md) voor voorbeelden van hoe u de Authoring API aanroept. De ontwerp-API vereist dat u een [ontwerpsleutel](luis-concept-keys.md#azure-resources-for-luis) gebruikt in plaats van een eindpuntsleutel. Programmatisch ontwerpen maakt maximaal 1.000.000 oproepen per maand en vijf transacties per seconde mogelijk. Zie [Sleutels beheren](./luis-concept-keys.md)voor meer informatie over de sleutels die u met LUIS gebruikt.

### <a name="where-is-the-pattern-feature-that-provided-regular-expression-matching"></a>Waar is de patroonfunctie die regelmatige expressiematching heeft geboden?
De vorige **patroonfunctie** is momenteel afgeschaft en vervangen door **[Patronen](luis-concept-patterns.md)**.

### <a name="how-do-i-use-an-entity-to-pull-out-the-correct-data"></a>Hoe gebruik ik een entiteit om de juiste gegevens te verzamelen?
Zie [entiteiten](luis-concept-entity-types.md) en [gegevensextractie](luis-concept-data-extraction.md).

### <a name="should-variations-of-an-example-utterance-include-punctuation"></a>Moeten variaties van een voorbeelduiting interpunctie bevatten?
Gebruik een van de volgende oplossingen:
* [Interpunctie negeren](luis-reference-application-settings.md#punctuation-normalization)
* De verschillende variaties toevoegen als voorbeelduitingen aan de intentie
* Voeg het patroon van de voorbeeldutterance toe aan de syntaxis om de interpunctie [te negeren.](luis-concept-patterns.md#pattern-syntax)

### <a name="does-luis-currently-support-cortana"></a>Ondersteunt LUIS momenteel Cortana?

Cortana vooraf gebouwde apps werden afgeschaft in 2017. Ze worden niet langer ondersteund.

### <a name="how-do-i-transfer-ownership-of-a-luis-app"></a>Hoe draag ik het eigendom van een LUIS-app over?
Als u een LUIS-app wilt overzetten naar een ander Azure-abonnement, exporteert u de LUIS-app en importeert u deze met een nieuw account. Werk de LUIS-app-id bij in de clienttoepassing die deze aanroept. De nieuwe app kan iets andere LUIS-scores van de oorspronkelijke app retourneren.

### <a name="a-prebuilt-entity-is-tagged-in-an-example-utterance-instead-of-my-custom-entity-how-do-i-fix-this"></a>Een vooraf gebouwde entiteit wordt getagd in een voorbeeldutterance in plaats van mijn aangepaste entiteit. Hoe los ik dit op?

In de LUIS-portal u tekst labelen voor de exacte entiteit die u wilt extraheren. Als de LUIS-portal niet de juiste entiteitsvoorspelling weergeeft, moet u mogelijk meer uitingen toevoegen en de entiteit in de tekst labelen of een beschrijving toevoegen (zoals een functie).

### <a name="i-tried-to-import-an-app-or-version-file-but-i-got-an-error-what-happened"></a>Ik heb geprobeerd om een app of versie bestand importeren, maar ik kreeg een foutmelding, wat is er gebeurd?

Lees meer over [fouten bij het importeren van versies](luis-how-to-manage-versions.md#import-errors).

<a name="luis-collaborating"></a>

## <a name="collaborating-and-contributing"></a>Samenwerken en bijdragen

### <a name="how-do-i-give-collaborators-access-to-luis-with-azure-active-directory-azure-ad-or-role-based-access-control-rbac"></a>Hoe geef ik medewerkers toegang tot LUIS met Azure AD (Azure AD) of RBAC (Role-based access control)?

Zie [Azure Active Directory-resources](luis-how-to-collaborate.md#azure-active-directory-resources) en [Azure Active Directory-tenantgebruiker](luis-how-to-collaborate.md#azure-active-directory-tenant-user) voor meer informatie over het geven van toegang aan bijdragers.

<a name="luis-endpoint"></a>

## <a name="endpoint"></a>Eindpunt

### <a name="i-received-an-http-403-error-status-code-how-do-i-fix-it"></a>Ik heb een HTTP 403-foutstatuscode ontvangen. Hoe kan ik dit probleem oplossen?

U krijgt 403 en 429 foutstatuscodes wanneer u de transacties per seconde of transacties per maand voor uw prijscategorie overschrijdt. Verhoog uw prijscategorie of gebruik [containers](luis-container-howto.md)voor taalbegrip.

Wanneer u al die gratis 1000 eindpuntquery's gebruikt of het maandelijkse transactiequotum van uw prijscategorie overschrijdt, ontvangt u een FOUTstatuscode van HTTP 403.

Als u deze fout wilt oplossen, moet u [uw prijslaag wijzigen](luis-how-to-azure-subscription.md#change-pricing-tier) in een hogere laag of [een nieuwe bron maken](get-started-portal-deploy-app.md#create-the-endpoint-resource) en deze toewijzen aan uw [app.](get-started-portal-deploy-app.md#assign-the-resource-key-to-the-luis-app-in-the-luis-portal)

Oplossingen voor deze fout zijn onder andere:

* Verander uw prijscategorie in de [Azure-portal](https://portal.azure.com)op uw bron voor taalbegrip op de **> prijscategorie Resourcebeheer**, uw prijscategorie in een hogere TPS-laag. U hoeft niets te doen in de portal Taalbegrip als uw bron al is toegewezen aan uw app Taalbegrip.
*  Als uw gebruik de hoogste prijscategorie overschrijdt, voegt u meer bronnen voor taalbegrip toe met een load balancer ervoor. De [taalbegrijpende container](luis-container-howto.md) met Kubernetes of Docker Compose kan hierbij helpen.

### <a name="i-received-an-http-429-error-status-code-how-do-i-fix-it"></a>Ik heb een HTTP 429-foutstatuscode ontvangen. Hoe kan ik dit probleem oplossen?

U krijgt 403 en 429 foutstatuscodes wanneer u de transacties per seconde of transacties per maand voor uw prijscategorie overschrijdt. Verhoog uw prijscategorie of gebruik [containers](luis-container-howto.md)voor taalbegrip.

Deze statuscode wordt geretourneerd wanneer uw transacties per seconde uw prijsniveau overschrijden.

Oplossingen zijn onder andere:

* U [uw prijscategorie verhogen](luis-how-to-azure-subscription.md#change-pricing-tier)als u niet op het hoogste niveau bent.
* Als uw gebruik de hoogste prijscategorie overschrijdt, voegt u meer bronnen voor taalbegrip toe met een load balancer ervoor. De [taalbegrijpende container](luis-container-howto.md) met Kubernetes of Docker Compose kan hierbij helpen.
* U uw aanvraagaanvragen voor klanten gateen met een [hernieuwingsbeleid dat](https://docs.microsoft.com/azure/architecture/best-practices/transient-faults#general-guidelines) u zelf implementeert wanneer u deze statuscode krijgt.

### <a name="my-endpoint-query-returned-unexpected-results-what-should-i-do"></a>Mijn eindpuntquery heeft onverwachte resultaten opgeleverd. Wat moet ik doen?

Onverwachte queryvoorspellingsresultaten zijn gebaseerd op de status van het gepubliceerde model. Als u het model wilt corrigeren, moet u mogelijk het model wijzigen, trainen en opnieuw publiceren.

Het corrigeren van het model begint met [actief leren.](luis-how-to-review-endpoint-utterances.md)

U niet-deterministische training verwijderen door de [API voor toepassingsversie-instellingen](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) bij te werken om alle trainingsgegevens te gebruiken.

Bekijk de [aanbevolen procedures](luis-concept-best-practices.md) voor andere tips.

### <a name="why-does-luis-add-spaces-to-the-query-around-or-in-the-middle-of-words"></a>Waarom voegt LUIS spaties toe aan de query rond of in het midden van woorden?
LUIS [tokenizes](luis-glossary.md#token) de uiting op basis van de [cultuur](luis-language-support.md#tokenization). Zowel de oorspronkelijke waarde als de tokenized waarde zijn beschikbaar voor [gegevensextractie.](luis-concept-data-extraction.md#tokenized-entity-returned)

### <a name="how-do-i-create-and-assign-a-luis-endpoint-key"></a>Hoe maak en wijs ik een LUIS-eindpuntsleutel toe?
[Maak de eindpuntsleutel](luis-how-to-azure-subscription.md) in Azure voor uw [serviceniveau.](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) [Wijs de sleutel toe](luis-how-to-azure-subscription.md) op de pagina **[Azure Resources.](luis-how-to-azure-subscription.md)** Er is geen bijbehorende API voor deze actie. Vervolgens moet u de HTTP-aanvraag wijzigen in het eindpunt om [de nieuwe eindpunttoets](luis-concept-keys.md)te gebruiken.

### <a name="how-do-i-interpret-luis-scores"></a>Hoe interpreteer ik LUIS-scores?
Uw systeem moet de hoogste score intentie gebruiken, ongeacht de waarde ervan. Bijvoorbeeld een score onder 0,5 (minder dan 50%) betekent niet noodzakelijkerwijs dat LUIS weinig vertrouwen heeft. Het verstrekken van meer trainingsgegevens kan helpen de [score](luis-concept-prediction-score.md) van de meest waarschijnlijke intentie te verhogen.

### <a name="why-dont-i-see-my-endpoint-hits-in-my-apps-dashboard"></a>Waarom zie ik mijn eindpunthits niet in het dashboard van mijn app?
De totale eindpunttreffers in het dashboard van uw app worden periodiek bijgewerkt, maar de statistieken die zijn gekoppeld aan uw LUIS-eindpuntsleutel in de Azure-portal worden vaker bijgewerkt.

Als u geen bijgewerkte eindpunttreffers in het dashboard ziet, meldt u zich aan bij de Azure-portal en zoekt u de resource die is gekoppeld aan uw LUIS-eindpuntsleutel en opent **u Metrische gegevens** om de statistiek Totale **aanrijgesprekken** te selecteren. Als de eindpuntsleutel voor meer dan één LUIS-app wordt gebruikt, wordt in de statistiek in de Azure-portal het totale aantal oproepen weergegeven van alle LUIS-apps die deze gebruiken.

### <a name="is-there-a-powershell-command-get-to-the-endpoint-quota"></a>Is er een PowerShell-opdracht om het eindpuntquotum te bereiken?

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

U een PowerShell-opdracht gebruiken om het eindpuntquotum te bekijken:

```powershell
Get-AzCognitiveServicesAccountUsage -ResourceGroupName <your-resource-group> -Name <your-resource-name>
```

### <a name="my-luis-app-was-working-yesterday-but-today-im-getting-403-errors-i-didnt-change-the-app-how-do-i-fix-it"></a>Mijn LUIS app werkte gisteren, maar vandaag krijg ik 403 fouten. Ik heb de app niet gewijzigd. Hoe kan ik dit probleem oplossen?
Volg deze [instructies](#how-do-i-create-and-assign-a-luis-endpoint-key) om een LUIS-eindpuntsleutel te maken en deze aan de app toe te wijzen. Vervolgens moet u het HTTP-verzoek van de clienttoepassing wijzigen in het eindpunt om [de nieuwe eindpuntsleutel](luis-concept-keys.md)te gebruiken. Als u een nieuwe resource in een andere regio hebt gemaakt, wijzigt u ook de regio van de HTTP-clientaanvraag.

### <a name="how-do-i-secure-my-luis-endpoint"></a>Hoe beveilig ik mijn LUIS-eindpunt?
Zie [Het eindpunt beveiligen](luis-concept-keys.md#securing-the-endpoint).

## <a name="working-within-luis-limits"></a>Werken binnen LUIS-limieten

### <a name="what-is-the-maximum-number-of-intents-and-entities-that-a-luis-app-can-support"></a>Wat is het maximum aantal intenties en entiteiten dat een LUIS-app kan ondersteunen?
Zie de [grenzen](luis-boundaries.md) referentie.

### <a name="i-want-to-build-a-luis-app-with-more-than-the-maximum-number-of-intents-what-should-i-do"></a>Ik wil een LUIS-app bouwen met meer dan het maximum aantal intenties. Wat moet ik doen?

Zie [Aanbevolen procedures voor intents](luis-concept-intent.md#if-you-need-more-than-the-maximum-number-of-intents).

### <a name="i-want-to-build-an-app-in-luis-with-more-than-the-maximum-number-of-entities-what-should-i-do"></a>Ik wil een app bouwen in LUIS met meer dan het maximum aantal entiteiten. Wat moet ik doen?

Aanbevolen [procedures voor entiteiten bekijken](luis-concept-entity-types.md#if-you-need-more-than-the-maximum-number-of-entities)

### <a name="what-are-the-limits-on-the-number-and-size-of-phrase-lists"></a>Wat zijn de beperkingen op het aantal en de grootte van woordlijsten?
Zie de verwijzing naar de [grenzen](luis-boundaries.md) voor de maximale lengte van een [woordgroeplijst.](./luis-concept-feature.md)

### <a name="what-are-the-limits-on-example-utterances"></a>Wat zijn de limieten voor voorbeelduitingen?
Zie de [grenzen](luis-boundaries.md) referentie.

## <a name="testing-and-training"></a>Testen en trainen

### <a name="i-see-some-errors-in-the-batch-testing-pane-for-some-of-the-models-in-my-app-how-can-i-address-this-problem"></a>Ik zie een aantal fouten in de batch testen venster voor een aantal van de modellen in mijn app. Hoe kan ik dit probleem aanpakken?

De fouten geven aan dat er een discrepantie is tussen uw labels en de voorspellingen van uw modellen. Als u het probleem wilt oplossen, doet u een of beide van de volgende taken:
* Voeg meer labels toe om LUIS te helpen discriminatie tussen intenties te verbeteren.
* Als u LUIS sneller wilt leren, voegt u termenlijstfuncties toe die domeinspecifieke woordenschat introduceren.

Zie de [zelfstudie voor batchtesten.](luis-tutorial-batch-testing.md)

### <a name="when-an-app-is-exported-then-reimported-into-a-new-app-with-a-new-app-id-the-luis-prediction-scores-are-different-why-does-this-happen"></a>Wanneer een app wordt geëxporteerd en vervolgens opnieuw wordt geïmporteerd in een nieuwe app (met een nieuwe app-id), zijn de LUIS-voorspellingsscores anders. Waarom gebeurt dit?

Zie [Voorspellingsverschillen tussen kopieën van dezelfde app](luis-concept-prediction-score.md#review-intents-with-similar-scores).

### <a name="some-utterances-go-to-the-wrong-intent-after-i-made-changes-to-my-app-the-issue-seems-to-disappear-at-random-how-do-i-fix-it"></a>Sommige uitingen gaan naar de verkeerde intentie nadat ik wijzigingen heb aangebracht in mijn app. Het probleem lijkt willekeurig te verdwijnen. Hoe kan ik dit probleem oplossen?

Zie [Trainen met alle gegevens](luis-how-to-train.md#train-with-all-data).

## <a name="app-publishing"></a>App-publicatie

### <a name="what-is-the-tenant-id-in-the-add-a-key-to-your-app-window"></a>Wat is de tenant-id in het venster 'Een sleutel toevoegen aan uw app'?
In Azure vertegenwoordigt een tenant de client of organisatie die is gekoppeld aan een service. Zoek uw tenant-id in de Azure-portal in het vak **Directory-id** door **Azure Active Directory** > **Manage** > **Properties te**selecteren.

![Tenant-id in de Azure-portal](./media/luis-manage-keys/luis-assign-key-tenant-id.png)

<a name="why-are-there-more-subscription-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>
<a name="why-are-there-more-endpoint-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>


### <a name="why-are-there-more-endpoint-keys-assigned-to-my-app-than-i-assigned"></a>Waarom zijn er meer eindpuntsleutels toegewezen aan mijn app dan ik heb toegewezen?
Elke LUIS-app heeft de ontwerp-/startersleutel in de eindpuntlijst als een gemak. Deze toets maakt slechts een paar endpoint hits mogelijk, zodat u LUIS uitproberen.

Als uw app bestond voordat LUIS algemeen beschikbaar was (GA), worden LUIS-eindpuntsleutels in uw abonnement automatisch toegewezen. Dit werd gedaan om GA-migratie gemakkelijker te maken. Nieuwe LUIS-eindpuntsleutels in de Azure-portal worden _niet_ automatisch aan LUIS toegewezen.

## <a name="key-management"></a>Sleutelbeheer

### <a name="how-do-i-know-what-key-i-need-where-i-get-it-and-what-i-do-with-it"></a>Hoe weet ik welke sleutel ik nodig heb, waar ik het vandaan haal en wat ik ermee doe?

Zie [Eindpuntsleutels voor authoring en queryvoorspelling in LUIS](luis-concept-keys.md) voor meer informatie over de verschillen tussen de ontwerpsleutel en de uitvoeringstijdsleutel voor voorspellingen.

### <a name="i-got-an-error-about-being-out-of-quota-how-do-i-fix-it"></a>Ik heb een fout over het feit dat uit het quotum. Hoe kan ik dit probleem oplossen?

Zie HTTP-statuscode [403](#i-received-an-http-403-error-status-code-how-do-i-fix-it) en [429](#i-received-an-http-429-error-status-code-how-do-i-fix-it) voor meer informatie.

### <a name="i-need-to-handle-more-endpoint-queries-how-do-i-do-that"></a>Ik moet meer eindpuntquery's afhandelen. Hoe doe ik dat?

Zie HTTP-statuscode [403](#i-received-an-http-403-error-status-code-how-do-i-fix-it) en [429](#i-received-an-http-429-error-status-code-how-do-i-fix-it) voor meer informatie.

### <a name="i-created-an-authoring-key-but-it-isnt-showing-in-the-luis-portal-what-happened"></a>Ik heb een ontwerpsleutel gemaakt, maar deze wordt niet weergegeven in de LUIS-portal. Wat is er gebeurd?

Ontwerpsleutels zijn beschikbaar in de LUIS-portal nadat u [naar de ontwerpsleutelervaring is gemigreerd.](luis-migration-authoring.md)

## <a name="app-management"></a>Appbeheer

### <a name="how-do-i-download-a-log-of-user-utterances"></a>Hoe download ik een logboek met gebruikersuitingen?
Standaard registreert uw LUIS-app uitingen van gebruikers. Als u een logboek wilt downloaden met uitingen die gebruikers naar uw LUIS-app verzenden, gaat u naar **Mijn apps**en selecteert u de app. Selecteer op de contextuele werkbalk **Endpoint-logboeken exporteren**. Het logboek is opgemaakt als een CSV-bestand (comma-separated value).

### <a name="how-can-i-disable-the-logging-of-utterances"></a>Hoe kan ik het loggen van uitingen uitschakelen?
U de logboekregistratie van gebruikersuitingen uitschakelen door in te stellen `log=false` in de URL van eindpunt die uw clienttoepassing gebruikt om LUIS op te vragen. Als u logboekregistratie uitschakelt, wordt de mogelijkheid van uw LUIS-app om uitingen voor te stellen uitgeschakeld of de prestaties te verbeteren die zijn gebaseerd op [actief leren.](luis-concept-review-endpoint-utterances.md#what-is-active-learning) Als u `log=false` vanwege problemen met de privacy van gegevens instelt, u geen record van die uitingen van gebruikers van LUIS downloaden of deze uitingen gebruiken om uw app te verbeteren.

Logging is de enige opslag van uitingen.

### <a name="why-dont-i-want-all-my-endpoint-utterances-logged"></a>Waarom wil ik niet dat al mijn eindpuntuitingen worden geregistreerd?
Als u uw logboek gebruikt voor voorspellingsanalyse, legt u geen testuitingen vast in uw logboek.

## <a name="data-management"></a>Gegevensbeheer

### <a name="can-i-delete-data-from-luis"></a>Kan ik gegevens uit LUIS verwijderen?

* U voorbeelduitingen die worden gebruikt voor het trainen van LUIS altijd verwijderen. Als u een voorbeelduiting uit uw LUIS-app verwijdert, wordt deze verwijderd uit de LUIS-webservice en is deze niet beschikbaar voor export.
* U uitingen verwijderen uit de lijst met gebruikersuitingen die LUIS voorstelt op de pagina **Eindpuntuitingen controleren.** Als u uitingen uit deze lijst verwijdert, wordt deze niet voorgesteld, maar worden ze niet uit logboeken verwijderd.
* Als u een account verwijdert, worden alle apps verwijderd, samen met hun voorbeelduitingen en logboeken. De gegevens worden 60 dagen op de servers bewaard voordat ze permanent worden verwijderd.

### <a name="how-does-microsoft-manage-data-i-send-to-luis"></a>Hoe beheert Microsoft gegevens die ik naar LUIS stuur?

Het [vertrouwenscentrum](https://www.microsoft.com/trustcenter) legt onze toezeggingen en uw opties voor gegevensbeheer en -toegang in Azure Services uit.

## <a name="language-and-translation-support"></a>Ondersteuning voor taal en vertaling

### <a name="i-have-an-app-in-one-language-and-want-to-create-a-parallel-app-in-another-language-what-is-the-easiest-way-to-do-so"></a>Ik heb een app in één taal en wil een parallelle app maken in een andere taal. Wat is de makkelijkste manier om dit te doen?
1. Exporteer uw app.
2. Vertaal de gelabelde uitingen in het JSON-bestand van de geëxporteerde app naar de doeltaal.
3. Mogelijk moet u de namen van de intenties en entiteiten wijzigen of ze laten zoals ze zijn.
4. Importeer ten slotte de app om een LUIS-app in de doeltaal te hebben.

## <a name="app-notification"></a>App-melding

### <a name="why-did-i-get-an-email-saying-im-almost-out-of-quota"></a>Waarom kreeg ik een e-mail waarin staat dat ik bijna geen quota meer heb?
Uw ontwerp-/startsleutel is slechts 1000 eindpuntquery's per maand toegestaan. Maak een LUIS-eindpuntsleutel (gratis of betaald) en gebruik die sleutel bij het maken van eindpuntquery's. Als u eindpuntquery's maakt vanuit een bot of een andere clienttoepassing, moet u daar de LUIS-eindpuntsleutel wijzigen.

## <a name="bots"></a>Bots

### <a name="my-luis-bot-isnt-working-what-do-i-do"></a>Mijn LUIS-bot werkt niet. Wat moet ik doen?

Het eerste probleem is te isoleren als het probleem is gerelateerd aan LUIS of gebeurt buiten de LUIS middleware.

#### <a name="resolve-issue-in-luis"></a>Probleem oplossen in LUIS
Geef dezelfde utterance door aan LUIS vanaf het [LUIS-eindpunt](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint). Als u een fout ontvangt, lost u het probleem op in LUIS totdat de fout niet meer is geretourneerd. Veelvoorkomende fouten zijn:

* `Out of call volume quota. Quota will be replenished in <time>.`- Dit probleem geeft aan dat u moet overschakelen van een ontwerpsleutel naar een [eindpuntsleutel](luis-how-to-azure-subscription.md) of dat u [servicelagen](luis-how-to-azure-subscription.md#change-pricing-tier)moet wijzigen.

#### <a name="resolve-issue-in-azure-bot-service"></a>Probleem oplossen in Azure Bot-service

Als u de Azure Bot-service gebruikt en het probleem `Sorry, my bot code is having an issue`is dat de test in **webchat** terugkeert, controleert u uw logboeken:

1. Selecteer in de Azure-portal voor uw bot in de sectie **Botbeheer** de optie **Bouwen**.
1. Open de online codeeditor.
1. Selecteer in de bovenste, blauwe navigatiebalk de botnaam (het tweede item rechts).
1. Selecteer **Kudu-console openen**in de afsluitende vervolgkeuzelijst .
1. Selecteer **LogFiles**en selecteer **Vervolgens Toepassing**. Bekijk alle logbestanden. Als u de fout niet ziet in de toepassingsmap, controleert u alle logboekbestanden onder **LogFiles**.
1. Vergeet niet om uw project opnieuw op te bouwen als u een gecompileerde taal zoals C#gebruikt.

> [!Tip]
> De console kan ook pakketten installeren.

#### <a name="resolve-issue-while-debugging-on-local-machine-with-bot-framework"></a>Probleem oplossen tijdens het debuggen op lokale machines met Bot Framework.

Zie [Een bot debuggen voor](https://docs.microsoft.com/azure/bot-service/bot-service-debug-bot?view=azure-bot-service-4.0)meer informatie over lokale foutopsporing van een bot.

## <a name="integrating-luis"></a>Luis integreren

### <a name="where-is-my-luis-app-created-during-the-azure-web-app-bot-subscription-process"></a>Waar wordt mijn LUIS-app gemaakt tijdens het azure web app bot-abonnementsproces?
Als u een LUIS-sjabloon selecteert en de knop **Selecteren** selecteert in het sjabloonvenster, wordt het linkerdeelvenster gewijzigd om het sjabloontype op te nemen en wordt in welke regio de LUIS-sjabloon gemaakt. Het botproces voor web-apps maakt echter geen LUIS-abonnement.

![LUIS-sjabloonweb-app-botregio](./media/luis-faq/web-app-bot-location.png)

### <a name="what-luis-regions-support-bot-framework-speech-priming"></a>Welke LUIS-regio's ondersteunen Bot Framework-spraakpriming?
[Spraakpriming](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming) wordt alleen ondersteund voor LUIS-apps in het centrale (VS)-exemplaar.

## <a name="api-programming-strategies"></a>API-programmeerstrategieën

### <a name="how-do-i-programmatically-get-the-luis-region-of-a-resource"></a>Hoe krijg ik programmatisch het LUIS-gebied van een resource?

Gebruik het LUIS-voorbeeld om regio programmatisch te [vinden](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/find-region) met Behulp van C# of Node.Js.

## <a name="luis-service"></a>LUIS-service

### <a name="is-language-understanding-luis-available-on-premises-or-in-private-cloud"></a>Is Language Understanding (LUIS) on-premises of in private cloud beschikbaar?

Ja, u de [LUIS-container](luis-container-howto.md) voor deze scenario's gebruiken als u over de nodige connectiviteit met het gebruik van de meter beschikt.

## <a name="migrating-to-the-next-version"></a>Migreren naar de volgende versie

### <a name="how-do-i-migrate-to-preview-v3-api"></a>Hoe migreer ik naar een voorbeeld van V3 API?

Zie [API v2 naar v3 Migratiegids voor LUIS-apps](luis-migration-api-v3.md)

## <a name="build-2019-conference-announcements"></a>Aankondigingen van de conferentie voor 2019

De volgende functies zijn uitgebracht op de Build 2019-conferentie:

* [Voorbeeld van V3 API-migratiehandleiding](luis-migration-api-v3.md)
* [Verbeterd analysedashboard](luis-how-to-use-dashboard.md)
* [Verbeterde vooraf gebouwde domeinen](luis-reference-prebuilt-domains.md)
* [Dynamische lijstentiteiten](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [Externe entiteiten](luis-migration-api-v3.md#external-entities-passed-in-at-prediction-time)

Video's:

* [Azure Conversational AI gebruiken om uw bedrijf te schalen voor de volgende generatie](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="next-steps"></a>Volgende stappen

Zie de volgende bronnen voor meer informatie over LUIS:
* [Stapel overloopvragen die zijn getagd met LUIS](https://stackoverflow.com/questions/tagged/luis)
* [MSDN Language Understanding Intelligent Services (LUIS) Forum](https://social.msdn.microsoft.com/forums/azure/home?forum=LUIS)
