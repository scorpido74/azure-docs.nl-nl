---
title: Entiteiten toevoegen-LUIS
titleSuffix: Azure Cognitive Services
description: Maak entiteiten voor het extra heren van belang rijke gegevens van gebruikers uitingen in apps van Language Understanding (LUIS). Geëxtraheerde entiteits gegevens worden door de client toepassing gebruikt voor fullfile-aanvragen van klanten.
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
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383687"
---
# <a name="add-entities-to-extract-data"></a>Entiteiten toevoegen om gegevens op te halen 

Maak entiteiten voor het extra heren van belang rijke gegevens van gebruikers uitingen in apps van Language Understanding (LUIS). Geëxtraheerde entiteits gegevens worden door uw client toepassing gebruikt voor fullfile-aanvragen van klanten.

De entiteit vertegenwoordigt een woord of zinsdeel in de utterance die u wilt dat opgehaald. Entiteiten informatie die relevant zijn voor de bedoeling beschrijven en soms ze zijn essentieel voor uw app de taak uit te voeren. U kunt entiteiten maken wanneer u een voor beeld-utterance toevoegt aan een intentie of van (vóór of na) het toevoegen van een voor beeld-utterance aan een intentie.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="plan-entities-then-create-and-label"></a>Entiteiten plannen, vervolgens maken en labelen

Door machines geleerde entiteiten kunnen worden gemaakt op basis van het voor beeld uitingen of gemaakt op basis van de pagina **entiteiten** . 

Over het algemeen is een best practice tijd om de entiteiten te plannen voordat een door de machine geleerde entiteit in de portal wordt gemaakt. Maak vervolgens de door de machine geleerde entiteit uit het voor beeld-utterance met zo veel details in de subonderdelen en descriptors en beperkingen die u op dat moment kent. Met de [zelf studie voor het samen stellen](tutorial-machine-learned-entity.md) van de entiteit wordt gedemonstreerd hoe u deze methode gebruikt. 

Als onderdeel van het plannen van de entiteiten weet u misschien dat u tekst-overeenkomende entiteiten (zoals vooraf gemaakte entiteiten, reguliere expressie-entiteiten of lijst entiteiten) nodig hebt. U kunt deze maken op de pagina **entiteiten** voordat ze worden gelabeld in voor beeld uitingen. 

Wanneer u labels aanmaakt, kunt u afzonderlijke entiteiten labelen en vervolgens een entiteit maken die door een bovenliggende machine is geleerd. U kunt ook beginnen met een entiteit die door een bovenliggende machine is geleerd en de onderliggende entiteiten afbreken. 

> [!TIP] 
>Voorzie alle woorden die een entiteit kunnen aanduiden, zelfs als de woorden niet worden gebruikt als ze worden geëxtraheerd in de client toepassing. 

## <a name="creating-an-entity-before-or-with-labeling"></a>Een entiteit maken voor of met een label

Gebruik de volgende tabel om te begrijpen welke entiteiten voor het maken of toevoegen van elke entiteit aan de app. 

|Entiteits type|Locatie voor het maken van een entiteit in de LUIS-Portal|
|--|--|
|Entiteit op basis van machine learning|Details van entiteiten of intentie|
|Lijstentiteit|Details van entiteiten of intentie|
|Een entiteit in de vorm van een reguliere expressie|Entiteiten|
|Pattern.any-entiteit|Entiteiten|
|Vooraf gemaakte entiteiten|Entiteiten|
|Vooraf ontwikkelde domein entiteit|Entiteiten|

U kunt alle entiteiten maken op de pagina **entiteiten** , maar u kunt ook een aantal entiteiten maken als onderdeel van het labelen van de entiteit in het voor beeld utterance op de detail pagina van het **doel** . U kunt een entiteit alleen _labelen_ in een voor beeld-utterance van de detail pagina van het **doel** . 

## <a name="create-a-machine-learned-entity"></a>Een door een machine geleerde entiteit maken

[!INCLUDE [Create and label entities in machine-learned tutorial](includes/decomposable-tutorial-links.md)]

## <a name="create-a-text-matching-entity"></a>Een tekst-overeenkomende entiteit maken

Het gebruik van tekst-overeenkomende entiteiten biedt verschillende manieren om gegevens te extra heren:

|Tekst-overeenkomende entiteiten|Doel|
|--|--|
|[Entiteit weer geven](#add-list-entities-for-exact-matches)|lijst met canonieke namen en synoniemen als alternatieve formulieren|
|Een entiteit in de vorm van een reguliere expressie|tekst overeenkomen met een reguliere expressie-entiteit|
|[Vooraf gebouwde entiteit](tutorial-machine-learned-entity.md#add-prebuilt-number-to-help-extract-data)|overeenkomende gegevens typen zoals nummer, e-mail adres, datum|
|Vooraf ontwikkelde domein entiteit|overeenkomst met geselecteerde onderwerps domeinen|
|[Patroon. alle](#add-a-patternany-entity)| entiteiten afstemmen die gemakkelijk kunnen worden verward met de omringende tekst|  

Preconstrueerde entiteiten werken zonder aangepaste trainings gegevens. De andere entiteiten moeten gegevens over de training van de klant (zoals items van de lijst entiteit) of een expressie (zoals een reguliere expressie of patroon) opgeven.

<a name="add-list-entities"></a>

### <a name="how-to-create-a-new-custom-entity"></a>Een nieuwe aangepaste entiteit maken

1. Ga in de LUIS-Portal naar de sectie **Manage** en vervolgens op de pagina **entities** . 
1. Selecteer **+ maken**en selecteer vervolgens het entiteits type. 
1. Ga verder met het configureren van de entiteit en selecteer **maken** wanneer u klaar bent. 

### <a name="add-list-entities-for-exact-matches"></a>Lijst entiteiten toevoegen voor exacte overeenkomsten

Lijst met entiteiten vertegenwoordigen een vaste en gesloten set verwante woorden. Als de auteur de lijst kan wijzigen, kan LUIS de lijst niet verg Roten of verkleinen. U kunt ook importeren naar een bestaande entiteit van een lijst met behulp van een [lijst entiteit. json-indeling (Reference-Entity-List. MD # voor beeld-JSON-to-import-into-List-entity-entiteit). 

In de volgende lijst ziet u de canonieke naam en de synoniemen. 

|Kleur-lijst item naam|Kleur-synoniemen|
|--|--|
|Rood|Crimson, bloed, Apple, brand engine|
|Blauw|lucht, azure, kobalt|
|Groen|Kelly, licht groen|

Gebruik de procedure om een lijst entiteit te maken. Zodra de lijst entiteit is gemaakt, hoeft u geen label voor beeld-uitingen in te richten. Lijst items en synoniemen worden vergeleken met de exacte tekst. 

1. Selecteer in de sectie **Build** de optie **entiteiten** in het linkerdeel venster en selecteer **+ maken**.

1. Voer in het dialoog venster **een entiteits type maken** de naam van de entiteit in, bijvoorbeeld `Colors` en selecteer **lijst**.
1. Voer in het dialoog venster **een lijst entiteit maken** in de lijst **nieuwe sublijst toevoegen....** de naam van het item in, bijvoorbeeld `Green`, en voeg synoniemen toe.

    > [!div class="mx-imgBorder"]
    > ![een lijst met kleuren als een lijst entiteit te maken op de pagina Details van entiteit.](media/how-to-add-entities/create-list-entity-of-colors.png) 

1. Wanneer u klaar bent met het toevoegen van lijst items en synoniemen, selecteert u **maken**.

    Wanneer u klaar bent met een groep wijzigingen in de app, vergeet dan niet om de app te **trainen** . Train de app niet na één wijziging. 

    > [!NOTE]
    > Deze procedure illustreert het maken en labelen van een lijst entiteit van een voorbeeld utterance op de detail pagina van het **doel** . U kunt ook dezelfde entiteit maken op de pagina **entiteiten** .

## <a name="add-a-role-for-an-entity"></a>Een rol voor een entiteit toevoegen

Een rol is een benoemd subtype van een entiteit, op basis van de context. 

### <a name="add-a-role-to-distinguish-different-contexts"></a>Een rol toevoegen om verschillende contexten te onderscheiden

In de volgende utterance zijn er twee locaties en elke naam wordt semantisch opgegeven door de woorden eromheen, zoals `to` en `from`: 

`Pick up the package from Seattle and deliver to New York City.`

In deze procedure voegt u `origin` en `destination` rollen toe aan een vooraf gemaakte geographyV2-entiteit.

1. Selecteer in de sectie **Build** de optie **entiteiten** in het linkerdeel venster.

1. Selecteer **+ vooraf samengestelde entiteit toevoegen**. Selecteer **geographyV2** en selecteer vervolgens **gereed**. Hiermee voegt u een vooraf samengestelde entiteit toe aan de app.
    
    Als u merkt dat het patroon, wanneer dit een Pattern.any bevat, entiteiten onjuist extraheert, gebruikt u een [expliciete lijst](reference-pattern-syntax.md#explicit-lists) om dit probleem te verhelpen. 

1. Selecteer de zojuist toegevoegde, vooraf gemaakte geographyV2-entiteit uit de pagina lijst **entiteiten** van entiteiten. 
1. Als u een nieuwe rol wilt toevoegen, selecteert u **+** naast **geen rollen toegevoegd**.
1. Voer in het tekstvak **type Role...** de naam van de rol in `Origin` vervolgens op ENTER. Voeg een tweede Rolnaam van `Destination` in en voer vervolgens ENTER. 

    > [!div class="mx-imgBorder"]
    > ![scherm opname van de functie origin toevoegen aan de locatie-entiteit](media/how-to-add-entities//add-role-to-prebuilt-geographyv2-entity.png)

    De rol wordt toegevoegd aan de vooraf samengestelde entiteit, maar wordt niet aan uitingen toegevoegd met behulp van die entiteit. 

### <a name="label-text-with-a-role-in-an-example-utterance"></a>Label tekst met een rol in een voor beeld van een utterance

1. Ga naar de pagina Details van intentie, met bijvoorbeeld uitingen die gebruikmaken van de rol. 
1. Als u wilt labelen met de rol, selecteert u het label entiteit (effen lijn onder tekst) in het voor beeld utterance en selecteert u vervolgens **weer gave in entiteits palet** in de vervolg keuzelijst. 

    > [!div class="mx-imgBorder"]
    > ![scherm opname van het selecteren van weer gave in het entiteits palet](media/how-to-add-entities/select-text-label-with-entity-palette-for-role.png)   

    Het palet entiteit wordt geopend aan de rechter kant. 

1. Selecteer de entiteit, ga naar de onderkant van het palet en selecteer de rol. 

    > [!div class="mx-imgBorder"]
    > ![scherm opname van het selecteren van weer gave in het entiteits palet](media/how-to-add-entities/select-role-from-entity-palette-entity-inspector.png)

<a name="add-pattern-any-entities"></a>

## <a name="add-a-patternany-entity"></a>Een patroon toevoegen. elke entiteit

[Patroon. alle](luis-concept-entity-types.md) entiteiten zijn alleen geldig in [patronen](luis-how-to-model-intent-pattern.md), niet in de uitingen van voor beelden. Dit type entiteit kunt LUIS vinden het einde van de entiteiten met verschillende lengte en word keuze. Omdat deze entiteit wordt gebruikt in een patroon, LUIS u weet waar het einde van de entiteit zich bevindt in de sjabloon utterance.

### <a name="steps-to-create-a-patternany-entity"></a>Stappen voor het maken van een patroon. elke entiteit

1. Selecteer in de sectie **Build** de optie **entiteiten** in het linkerdeel venster en selecteer **+ maken**.

1. Voer in het dialoog venster **een entiteits type kiezen** de naam van de entiteit in het vak **naam** in en selecteer vervolgens **patroon. elk** **type** en selecteer vervolgens **maken**.

    Wanneer u [een patroon utterance maakt](luis-how-to-model-intent-pattern.md) met behulp van deze entiteit, wordt de entiteit geëxtraheerd met een gecombineerd door de machine geleerd en tekst-overeenkomend algoritme. 

### <a name="create-a-pattern-template-utterance-to-use-patternany-entity"></a>Maak een patroon sjabloon utterance om gebruik te maken van een patroon. elke entiteit

Als u het patroon wilt gebruiken. een wille keurige entiteit, een patroon toevoegen op de pagina **patronen** , in de sectie de prestaties van de **app verbeteren** , met de juiste accolade syntaxis, zoals `Where is **{HumanResourcesFormTitle}** on the server?`.

Als u merkt dat het patroon, wanneer dit een Pattern.any bevat, entiteiten onjuist extraheert, gebruikt u een [expliciete lijst](reference-pattern-syntax.md#explicit-lists) om dit probleem te verhelpen. 

## <a name="do-not-change-entity-type"></a>Entiteits type niet wijzigen

LUIS staat niet toe dat u het type van de entiteit niet wijzigen omdat het niet weet wat u moet toevoegen of verwijderen om te bouwen die entiteit. Als u wilt wijzigen, is het beter om een nieuwe entiteit van het juiste type maken met een iets andere naam. Nadat de entiteit is gemaakt, in elke utterance, verwijdert u de oude naam van de gelabelde entiteit en de naam van de nieuwe entiteit toevoegen. Zodra alle uitingen hebben is relabeled, de oude entiteit niet verwijderen. 

<a name="create-a-pattern-from-an-utterance"></a>

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"] 
> [Vooraf samengestelde modellen gebruiken](howto-add-prebuilt-models.md) 

Meer informatie over:
* [Train](luis-how-to-train.md)
* [Testen](luis-interactive-test.md)
* [Publiceren](luis-how-to-publish-app.md)
* Daarin
    * [Concepten](luis-concept-patterns.md)
    * [Syntaxis](reference-pattern-syntax.md)
* [Vooraf gemaakte entiteiten GitHub-opslag plaats](https://github.com/Microsoft/Recognizers-Text)
* [Concepten voor gegevens extractie](luis-concept-data-extraction.md)


 
