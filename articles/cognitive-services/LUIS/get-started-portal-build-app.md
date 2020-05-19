---
title: 'Snelstartgids: een nieuwe app maken in de LUIS-Portal'
description: In deze Quick Start maakt u de basis onderdelen van een app, intenties en entiteiten, evenals de test met voorbeeld utterance in de LUIS-Portal.
ms.topic: quickstart
ms.date: 05/05/2020
ms.openlocfilehash: b2ab2ec0fa5398ae8a69906aee25ef44ad5db3c9
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83585195"
---
# <a name="quickstart-create-a-new-app-in-the-luis-portal"></a>Snelstartgids: een nieuwe app maken in de LUIS-Portal

In deze Quick Start maakt u een nieuwe app in de LUIS-Portal. Maak eerst de basis onderdelen van een app, **intenties**en **entiteiten**. Test vervolgens de app door een voor beeld van een gebruiker utterance in het interactieve test paneel op te geven om de voorspelde intentie te verkrijgen.

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-an-app"></a>Een app maken

1. Selecteer **+ nieuwe app voor gesprek** in de context werkbalk en selecteer vervolgens **+ nieuwe app voor gesprek** opnieuw.

    > [!div class="mx-imgBorder"]
    > [![Scherm afbeelding van het maken van een nieuwe app in de LUIS-Portal](./media/create-app-in-portal.png)](./media/create-app-in-portal.png#lightbox)

1. Configureer in het pop-upvenster de app met de volgende instellingen en selecteer vervolgens **gereed**.

   |Naam van de instelling| Waarde | Doel|
   |--|--|--|
   |Name|`myEnglishApp`|Unieke naam van de LUIS-app<br>vereist|
   |Cultuur|**Engels**|Taal van uitingen van gebruikers, **en-US**<br>vereist|
   |Beschrijving (optioneel)|`App made with LUIS Portal`|Beschrijving van de app<br>optioneel|
   |Voorspellings bron (optioneel) |-  |Niet selecteren. LUIS biedt u een begin sleutel die gratis kan worden gebruikt voor het maken van bewerkings eindpunt aanvragen voor ontwerpen en 1.000. |

   ![Scherm opname van het invoeren van nieuwe app-instellingen](./media/get-started-portal-build-app/create-new-app-settings.png)

## <a name="create-intents"></a>Intents maken

Nadat de LUIS-app is gemaakt, moet u intenties maken. Met bedingen kunt u tekst van gebruikers classificeren. Een Human resources-app kan bijvoorbeeld twee functies hebben. Om mensen te helpen:

 1. Taken zoeken en Toep assen
 1. Formulieren zoeken die moeten worden toegepast op taken

De twee verschillende _bedoelingen_ van de app worden uitgelijnd op de volgende intenties:

|Intentie|Voorbeeld tekst van gebruiker<br>ook wel bekend als een _utterance_|
|--|--|
|ApplyForJob|`I want to apply for the new software engineering position in Cairo.`|
|FindForm|`Where is the job transfer form hrf-123456?`|

Voer de volgende stappen uit om intenties te maken:

1. Nadat de app is gemaakt, bevindt u zich op de pagina **intenties** van de sectie **bouwen** . Selecteer **Maken**.

   [![Scherm opname van het selecteren van ' maken ' om een nieuwe intentie te maken](./media/get-started-portal-build-app/create-new-intent-button.png)](./media/get-started-portal-build-app/create-new-intent-button.png#lightbox)

1. Voer de naam van de doel groep in `FindForm` en selecteer vervolgens **gereed**.

## <a name="add-an-example-utterance"></a>Een voor beeld-utterance toevoegen

U kunt bijvoorbeeld uitingen toevoegen nadat u intenties hebt gemaakt. Voor beelden van uitingen zijn tekst die een gebruiker invoert in een chat-bot of een andere client toepassing. Ze wijzen de bedoeling van de tekst van de gebruiker toe aan een LUIS intentie.

Voor het doel van deze voorbeeld toepassing `FindForm` bevat bijvoorbeeld uitingen het formulier nummer. De client toepassing heeft het formulier nummer nodig om te voldoen aan de aanvraag van de gebruiker. het is dus belang rijk dat u deze opneemt in de utterance.

> [!div class="mx-imgBorder"]
> [![Scherm afbeelding van het invoeren van voor beeld-uitingen voor de FindForm intentie](./media/get-started-portal-build-app/add-example-utterance.png)](./media/get-started-portal-build-app/add-example-utterance.png#lightbox)

Voeg het volgende 15-voor beeld uitingen toe aan de `FindForm` bedoeling.

|#|Voorbeelden van utterances|
|--|--|
|1|`Looking for hrf-123456`|
|2|`Where is the human resources form hrf-234591?`|
|3|`hrf-345623, where is it`|
|4|`Is it possible to send me hrf-345794`|
|5|`Do I need hrf-234695 to apply for an internal job?`|
|6|`Does my manager need to know I'm applying for a job with hrf-234091`|
|7|`Where do I send hrf-234918? Do I get an email response it was received?`|
|8|`hrf-234555`|
|9|`When was hrf-234987 updated?`|
|10|`Do I use form hrf-876345 to apply for engineering positions`|
|11|`Was a new version of hrf-765234 submitted for my open req?`|
|12|`Do I use hrf-234234 for international jobs?`|
|13|`hrf-234598 spelling mistake`|
|14|`will hrf-234567 be edited for new requirements`|
|15|`hrf-123456, hrf-123123, hrf-234567`|

Het ontwerp van dit voor beeld kan er als volgt uitzien:

* lengte van utterance
* [interpunctie](luis-reference-application-settings.md#punctuation-normalization)
* woorden keuze
* werkwoordsvormen (is, was, is)
* woord volgorde


## <a name="create-a-regular-expression-entity"></a>Een reguliere expressie-entiteit maken

Om het formulier nummer te retour neren in het runtime-Voorspellings antwoord, moet het formulier nummer worden geëxtraheerd als een entiteit. Omdat de tekst van het formulier nummer zeer gestructureerd is, kunt u een reguliere expressie-entiteit gebruiken. Maak de reguliere expressie-entiteit met de volgende stappen:

1. Selecteer **entiteiten** in het menu aan de linkerkant.

1. Selecteer **maken** op de pagina **entiteiten** .

1. Voer de naam in `FormNumber` , selecteer het type **regex** -entiteit.

1. Voer de reguliere expressie `hrf-[0-9]{6}` in het veld **regex** in. Deze vermelding komt overeen met de letterlijke tekens, `hrf-` en staat precies zes cijfers toe, en selecteer vervolgens **maken**.

    > [!div class="mx-imgBorder"]
    > ![Scherm opname van entiteit voor reguliere expressie maken](./media/get-started-portal-build-app/create-regular-expression-entity.png)


    Deze entiteit extraheert alle tekst die overeenkomt met de reguliere expressie in een van de intenties.

## <a name="add-example-utterances-to-the-none-intent"></a>Voorbeelduitingen toevoegen aan de intentie None

**Geen** intentie is de terugval intentie en mag niet leeg blijven. Deze intentie moet één utterance bevatten voor elke 10 voorbeeld uitingen die u hebt toegevoegd voor de andere intentie van de app.

De voor beeld-uitingen van **geen** intentie moet zich buiten uw client toepassings domein bevinden.

1. Selecteer **intenties** in het menu links en selecteer vervolgens **geen** in de lijst intenties.

1. Voeg het volgende voor beeld uitingen toe aan de bedoeling:

   |Voor beeld van geen intentie-uitingen|
   |--|
   |`Barking dogs are annoying`|
   |`Penguins in the ocean`|

   Voor deze app zijn dit voor beeld-uitingen buiten het domein. Als uw domein dieren bevat of de Oceaan, moet u een ander voor beeld uitingen gebruiken voor de **geen** intentie.

## <a name="train-the-app"></a>De app trainen

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="look-at-the-regular-expression-entity-in-the-example-utterances"></a>Bekijk de entiteit reguliere expressie in het voor beeld uitingen

1. Controleer of de entiteit is gevonden in de **FindForm** -intentie door **intenties** te selecteren in het menu links. Selecteer vervolgens **FindForm** intentie.

   De entiteit is gemarkeerd waar deze wordt weer gegeven in het voor beeld uitingen. Als u de oorspronkelijke tekst wilt zien in plaats van de naam van de entiteit, schakelt u in de werk balk **weer gave entiteiten** in.

   > [!div class="mx-imgBorder"]
   > [![Scherm afbeelding van alle voor beelden uitingen dat is gemarkeerd met entiteiten](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png)](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png#lightbox)

## <a name="test-your-new-app-with-the-interactive-test-pane"></a>Uw nieuwe app testen met het interactieve test venster

Gebruik het interactieve **test** venster in de Luis-Portal om te controleren of de entiteit is geëxtraheerd uit nieuwe uitingen de app nog niet is gedetecteerd.

1. Selecteer **testen** in het menu in de rechter bovenhoek.

1. Voeg een nieuwe utterance toe en druk vervolgens op ENTER:

   ```Is there a form named hrf-234098```

    Selecteer **controleren** om de voor spellingen van de entiteit te bekijken.

   > [!div class="mx-imgBorder"]
   > ![Scherm opname van het testen van nieuwe utterance in het test venster](./media/get-started-portal-build-app/test-new-utterance.png)

   De eerste voorspelde intentie is op de juiste manier **FindForm** met meer dan 90% betrouw baarheid (0,977). De **FormNumber** -entiteit wordt geëxtraheerd met de waarde hrf-234098.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met deze Snelstartgids en niet naar de volgende Snelstartgids gaat, selecteert u **mijn apps** in het bovenste navigatie menu. Schakel vervolgens het selectie vakje van de app naar links in de lijst in en selecteer **verwijderen** in de context werkbalk boven de lijst.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [2. een app implementeren](get-started-portal-deploy-app.md)
