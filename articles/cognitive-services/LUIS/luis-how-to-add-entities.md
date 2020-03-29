---
title: Entiteiten toevoegen - LUIS
titleSuffix: Azure Cognitive Services
description: Maak entiteiten om belangrijke gegevens uit gebruikersuitingen te extraheren in LUIS-apps (Language Understanding). Geëxtraheerde entiteitsgegevens worden door de clienttoepassing gebruikt om klantverzoeken van Fullfil te verwerken.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: diberry
ms.openlocfilehash: 1f2b293acdc77e25e6b932c47d466cc28a04a2b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220906"
---
# <a name="add-entities-to-extract-data"></a>Entiteiten toevoegen om gegevens te extraheren 

Maak entiteiten om belangrijke gegevens uit gebruikersuitingen te extraheren in LUIS-apps (Language Understanding). Geëxtraheerde entiteitsgegevens worden door uw clienttoepassing gebruikt om klantverzoeken volledig te verwerken.

De entiteit vertegenwoordigt een woord of woordgroep in de utterance die u wilt extraheren. Entiteiten beschrijven informatie die relevant is voor de intentie en soms zijn ze essentieel voor uw app om zijn taak uit te voeren. U entiteiten maken wanneer u een voorbeeldutterance toevoegt aan een intentie of afgezien van (voor of na) het toevoegen van een voorbeeldutterance aan een intentie.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="plan-entities-then-create-and-label"></a>Entiteiten plannen en vervolgens maken en labelen

Door machines geleerde entiteiten kunnen worden gemaakt op basis van de voorbeelduitingen of die zijn gemaakt op de pagina **Entiteiten.** 

In het algemeen is het een goede gewoonte om tijd te besteden aan het plannen van de entiteiten voordat u een door machines geleerde entiteit in de portal maakt. Maak vervolgens de door de machine geleerde entiteit uit de voorbeeldutterance met evenveel details in de subcomponenten en beschrijvingen en beperkingen zoals u dat op dat moment weet. De [zelfstudie van de ontleedbare entiteit](tutorial-machine-learned-entity.md) laat zien hoe u deze methode gebruiken. 

Als onderdeel van het plannen van de entiteiten weet u mogelijk dat u tekstovereenkomende entiteiten nodig hebt (zoals vooraf gebouwde entiteiten, entiteiten met reguliere expressie of lijstentiteiten). U deze maken op de pagina **Entiteiten** voordat ze worden gelabeld in voorbeelduitingen. 

Wanneer u labelt, u afzonderlijke entiteiten labelen en vervolgens opbouwen tot een bovenliggende, door de machine geleerde entiteit. U ook beginnen met een bovenliggende, door de machine geleerde entiteit en ontleden in onderliggende entiteiten. 

> [!TIP] 
>Label alle woorden die op een entiteit kunnen wijzen, zelfs als de woorden niet worden gebruikt wanneer ze worden geëxtraheerd in de clienttoepassing. 

## <a name="creating-an-entity-before-or-with-labeling"></a>Een entiteit maken voor of met labeling

Gebruik de volgende tabel om te begrijpen welke entiteiten elke entiteit moeten maken of toevoegen aan de app. 

|Entiteitstype|Waar entiteit maken in de LUIS-portal|
|--|--|
|Entiteit op basis van machine learning|Details van entiteiten of Intentie|
|Lijstentiteit|Details van entiteiten of Intentie|
|Een entiteit in de vorm van een reguliere expressie|Entiteiten|
|Pattern.any-entiteit|Entiteiten|
|Vooraf gebouwde entiteit|Entiteiten|
|Vooraf gebouwde domeinentiteit|Entiteiten|

U alle entiteiten maken op de pagina **Entiteiten** of u een paar entiteiten maken als onderdeel van het labelen van de entiteit in de voorbeeldutterance op de pagina **Intent-detail.** U een entiteit alleen _labelen_ in een voorbeeldutterance van de pagina **Intent-detail.** 

## <a name="create-a-machine-learned-entity"></a>Een door machines geleerde entiteit maken

[!INCLUDE [Create and label entities in machine-learned tutorial](includes/decomposable-tutorial-links.md)]

## <a name="create-a-text-matching-entity"></a>Een entiteit met tekstmatching maken

Gebruik entiteiten voor het afstemmen van tekst en bieden verschillende manieren om gegevens te extraheren:

|Entiteiten die tekst overeenkomen|Doel|
|--|--|
|[Lijstentiteit](#add-list-entities-for-exact-matches)|lijst van canonieke namen samen met synoniemen als alternatieve vormen|
|Een entiteit in de vorm van een reguliere expressie|tekst overeenkomen met een entiteit met reguliere expressie|
|[Vooraf gebouwde entiteit](tutorial-machine-learned-entity.md#add-prebuilt-number-to-help-extract-data)|overeenkomen met veelvoorkomende gegevenstypen zoals nummer, e-mail, datum|
|Vooraf gebouwde domeinentiteit|overeenkomen met geselecteerde onderwerpdomeinen|
|[Patroon.elk](#add-a-patternany-entity)| om entiteiten te matchen die gemakkelijk kunnen worden verward met de omringende tekst|  

Vooraf gebouwde entiteiten werken zonder aangepaste trainingsgegevens. De andere entiteiten hebben u nodig om klanttrainingsgegevens (zoals items van de entiteit Lijst) of een expressie (zoals een reguliere expressie of patroon.any) op te geven.

<a name="add-list-entities"></a>

### <a name="how-to-create-a-new-custom-entity"></a>Een nieuwe aangepaste entiteit maken

1. Ga in de LUIS-portal naar de sectie **Beheren** en vervolgens naar de pagina **Entiteiten.** 
1. Selecteer **+ Maken**en selecteer vervolgens het entiteitstype. 
1. Ga door met het configureren van de entiteit en selecteer **Maken** wanneer u klaar bent. 

### <a name="add-list-entities-for-exact-matches"></a>Lijstentiteiten toevoegen voor exacte overeenkomsten

Lijstentiteiten vertegenwoordigen een vaste, gesloten reeks verwante woorden. Terwijl u als auteur de lijst wijzigen, wordt de lijst niet groter of verkleinen. U ook importeren naar een bestaande lijstentiteit met behulp van een [list entiteit .json-indeling(referentie-entiteit-lijst.md#example-json-to-import-into-list-entity). 

De volgende lijst toont de canonieke naam en de synoniemen. 

|Kleur - naam van lijstitem|Kleur - synoniemen|
|--|--|
|Rood|karmozijnrood, bloed, appel, brandweerauto|
|Blauw|hemel, azuurblauw, kobalt|
|Groen|kelly, limoen|

Gebruik de procedure om een lijstentiteit te maken. Zodra de lijstentiteit is gemaakt, hoeft u voorbeelduitingen niet meer in een intentie te labelen. Lijstitems en synoniemen worden gekoppeld aan exacte tekst. 

1. Selecteer in de sectie **Bouwen** de optie **Entiteiten** in het linkerdeelvenster en selecteer **+ Maken**.

1. Voer in het dialoogvenster **Een entiteitstype maken** de `Colors` naam van de entiteit in, zoals en selecteer **Lijst**.
1. Voer in het dialoogvenster **Een lijstentiteit maken** in de **sublijst Toevoegen....** `Green`de naam van het lijstitem in, zoals vervolgens synoniemen toevoegen.

    > [!div class="mx-imgBorder"]
    > ![Maak een lijst met kleuren als lijstentiteit op de pagina Entiteitsdetail.](media/how-to-add-entities/create-list-entity-of-colors.png) 

1. Wanneer u klaar bent met het toevoegen van lijstitems en synoniemen, selecteert u **Maken**.

    Wanneer u klaar bent met een groep wijzigingen in de app, vergeet dan niet om de app te **trainen.** Train de app niet na één wijziging. 

    > [!NOTE]
    > Deze procedure toont het maken en labelen van een lijstentiteit uit een voorbeeldutterance op de pagina **Intent-detail.** U dezelfde entiteit ook maken op de pagina **Entiteiten.**

## <a name="add-a-role-for-an-entity"></a>Een rol voor een entiteit toevoegen

Een rol is een benoemd subtype van een entiteit, gebaseerd op context. 

### <a name="add-a-role-to-distinguish-different-contexts"></a>Een rol toevoegen om verschillende contexten te onderscheiden

In de volgende uiting, zijn er twee plaatsen, en elk wordt `to` `from`semantisch gespecificeerd door de woorden eromheen, zoals en : 

`Pick up the package from Seattle and deliver to New York City.`

Voeg in deze `origin` `destination` procedure toe en rol toe aan een vooraf gebouwde entiteit V2.

1. Selecteer **entiteiten** in het linkerdeelvenster in de sectie **Bouwen.**

1. Selecteer **+ Prebuilt entiteit toevoegen**. Selecteer **aardrijkskundeV2** en selecteer **Gereed**. Hiermee wordt een vooraf gebouwde entiteit aan de app toegevoegd.
    
    Als u merkt dat het patroon, wanneer dit een Pattern.any bevat, entiteiten onjuist extraheert, gebruikt u een [expliciete lijst](reference-pattern-syntax.md#explicit-lists) om dit probleem te verhelpen. 

1. Selecteer de nieuw gebouwde voorgebouwde entiteit V2 in de lijst **met** entiteiten. 
1. Als u een nieuwe **+** rol wilt toevoegen, selecteert u naast **Geen toegevoegde rollen**.
1. Voer **in het tekstvak Teksttype Tekst...** tekst de naam van de rol `Origin` in. Voeg een tweede `Destination` rolnaam toe van enter. 

    > [!div class="mx-imgBorder"]
    > ![Schermafbeelding van het toevoegen van origin-rol aan de entiteit Locatie](media/how-to-add-entities//add-role-to-prebuilt-geographyv2-entity.png)

    De rol wordt toegevoegd aan de vooraf gebouwde entiteit, maar wordt niet toegevoegd aan uitingen met die entiteit. 

### <a name="label-text-with-a-role-in-an-example-utterance"></a>Tekst labelen met een rol in een voorbeeldutterance

1. Ga naar de pagina Intentiedetails, met voorbeelduitingen die de rol gebruiken. 
1. Als u wilt labelen met de rol, selecteert u het entiteitslabel (vaste regel onder tekst) in de voorbeeldutterance en selecteert u **Weergave in entiteitspalet in** de vervolgkeuzelijst. 

    > [!div class="mx-imgBorder"]
    > ![Schermafbeelding van het selecteren van Weergave in entiteitspalet](media/how-to-add-entities/select-text-label-with-entity-palette-for-role.png)   

    Het entiteitspalet wordt naar rechts geopend. 

1. Selecteer de entiteit, ga naar de onderkant van het palet en selecteer de rol. 

    > [!div class="mx-imgBorder"]
    > ![Schermafbeelding van het selecteren van Weergave in entiteitspalet](media/how-to-add-entities/select-role-from-entity-palette-entity-inspector.png)

<a name="add-pattern-any-entities"></a>

## <a name="add-a-patternany-entity"></a>Een patroon toevoegen.een entiteit

[Pattern.any](luis-concept-entity-types.md) entiteiten zijn alleen geldig in [patronen,](luis-how-to-model-intent-pattern.md)niet in voorbeelduitingen van intenties. Dit type entiteit helpt LUIS het einde te vinden van entiteiten met verschillende lengte en woordkeuze. Omdat deze entiteit in een patroon wordt gebruikt, weet LUIS waar het einde van de entiteit zich bevindt in de utterancesjabloon.

### <a name="steps-to-create-a-patternany-entity"></a>Stappen om een patroon te maken.een entiteit

1. Selecteer in de sectie **Bouwen** de optie **Entiteiten** in het linkerdeelvenster en selecteer **+ Maken**.

1. Voer in het dialoogvenster **Een entiteitstype kiezen** de entiteitsnaam in het vak **Naam** in en selecteer **Patroon.Any** terwijl het **type** vervolgens **Maken**selecteert.

    Zodra u [een patroonutterance](luis-how-to-model-intent-pattern.md) maakt met behulp van deze entiteit, wordt de entiteit geëxtraheerd met een gecombineerd door de machine geleerde en tekstmatchingsalgoritme. 

### <a name="create-a-pattern-template-utterance-to-use-patternany-entity"></a>Een patroonsjabloonutterance maken om pattern.any-entiteit te gebruiken

Als u de entiteit pattern.any wilt gebruiken, voegt u een patroon toe op de pagina `Where is **{HumanResourcesFormTitle}** on the server?` **Patronen** in de sectie Prestaties van de app **verbeteren,** met de juiste syntaxis van de krullende brace, zoals .

Als u merkt dat het patroon, wanneer dit een Pattern.any bevat, entiteiten onjuist extraheert, gebruikt u een [expliciete lijst](reference-pattern-syntax.md#explicit-lists) om dit probleem te verhelpen. 

## <a name="do-not-change-entity-type"></a>Entiteitstype niet wijzigen

Luis staat u niet toe het type entiteit te wijzigen omdat het niet weet wat u moet toevoegen of verwijderen om die entiteit te construeren. Om het type te wijzigen, is het beter om een nieuwe entiteit van het juiste type te maken met een iets andere naam. Zodra de entiteit is gemaakt, verwijdert u in elke utterance de oude naam van de gelabelde entiteit en voegt u de nieuwe entiteitsnaam toe. Zodra alle uitingen opnieuw zijn gelabeld, verwijdert u de oude entiteit. 

<a name="create-a-pattern-from-an-utterance"></a>

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"] 
> [Vooraf gemaakte modellen gebruiken](howto-add-prebuilt-models.md) 

Meer informatie over:
* Hoe te [trainen](luis-how-to-train.md)
* Testen [test](luis-interactive-test.md)
* Publiceren [publish](luis-how-to-publish-app.md)
* Patronen:
    * [Concepten](luis-concept-patterns.md)
    * [Syntaxis](reference-pattern-syntax.md)
* [Vooraf gebouwde entiteiten GitHub-repository](https://github.com/Microsoft/Recognizers-Text)
* [Concepten voor gegevensextractie](luis-concept-data-extraction.md)


 
