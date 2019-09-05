---
title: 'Quickstart: Een nieuwe app maken in de LUIS-Portal'
titleSuffix: Azure Cognitive Services
description: In deze Quick Start maakt u een nieuwe app in de LUIS-Portal. Maak de basis onderdelen van een app, intenties en entiteiten. Test de app met een voor beeld van een gebruiker utterance in het interactieve test paneel om de voorspelde intentie te verkrijgen. Het bouwen van een app is gratis; hiervoor is geen Azure-abonnement vereist.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 09/04/2019
ms.author: diberry
ms.openlocfilehash: ff666437790a1e32dde83f9e3be90b4c62637181
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307733"
---
# <a name="quickstart-create-a-new-app-in-the-luis-portal"></a>Quickstart: Een nieuwe app maken in de LUIS-Portal

In deze Quick Start maakt u een nieuwe app in de [Luis-Portal](https://www.luis.ai). Eerst maakt u de basis onderdelen van een app, **intenties**en **entiteiten**. Vervolgens test u de app door een voor beeld van een gebruiker utterance in het interactieve test paneel op te geven om de voorspelde intentie te verkrijgen.

Het bouwen van een app is gratis en er is geen Azure-abonnement vereist. Wanneer u klaar bent om uw app te implementeren, raadpleegt u de [Snelstartgids voor het implementeren van een app](get-started-portal-deploy-app.md). U ziet hoe u een Azure cognitieve service-resource maakt en deze aan de app toewijst.

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-an-app"></a>Een app maken

1. Selecteer **nieuwe app maken** in de context werkbalk.

   [![Nieuwe app maken in de LUIS-Portal](./media/get-started-portal-build-app/create-app-in-portal.png)](./media/get-started-portal-build-app/create-app-in-portal.png#lightbox)

1. Configureer in het pop-upvenster de app met de volgende instellingen en selecteer vervolgens **gereed**.

   |Naam van instelling| Value | Doel|
   |--|--|--|
   |Name|`myEnglishApp`|Unieke naam van de LUIS-app<br>vereist|
   |Culture|**Engels**|Taal van uitingen van gebruikers, **en-US**<br>vereist|
   |Description|`App made with LUIS Portal`|Beschrijving van de app<br>optioneel|
   | | | |

   ![Nieuwe app-instellingen invoeren](./media/get-started-portal-build-app/create-new-app-settings.png)

## <a name="create-intents"></a>Intents maken

Nadat de LUIS-app is gemaakt, moet u intenties maken. Met bedingen kunt u tekst van gebruikers categoriseren. Een Human resources-app kan bijvoorbeeld twee functies hebben. Om mensen te helpen:

 1. Taken zoeken en Toep assen
 1. Formulieren zoeken die moeten worden toegepast op taken

De twee verschillende _bedoelingen_ van de app worden uitgelijnd op de volgende intenties:

|Intentie|Voorbeeld tekst van gebruiker<br>ook wel bekend als een _utterance_|
|--|--|
|ApplyForJob|`I want to apply for the new software engineering position in Cairo.`|
|FindForm|`Where is the job transfer form hrf-123456?`|

Voer de volgende stappen uit om intenties te maken:

1. Nadat de app is gemaakt, bevindt u zich op de pagina **intenties** van de sectie **bouwen** . Selecteer **Create new intent**.

   [![Knop nieuwe intentie maken selecteren](./media/get-started-portal-build-app/create-new-intent-button.png)](./media/get-started-portal-build-app/create-new-intent-button.png#lightbox)

1. Voer de naam van de doel `FindForm`groep in en selecteer vervolgens **gereed**.

   ![De naam van de doel groep van FindForm invoeren](./media/get-started-portal-build-app/create-new-intent-dialog.png)

## <a name="add-an-example-utterance"></a>Een voorbeeld-utterance toevoegen

U kunt bijvoorbeeld uitingen toevoegen nadat u intenties hebt gemaakt. Voor beelden van uitingen zijn tekst die een gebruiker invoert in een chat-bot of een andere client toepassing. Ze wijzen de bedoeling van de tekst van de gebruiker toe aan een LUIS intentie.

Voor het doel van `FindForm` deze voorbeeld toepassing bevat bijvoorbeeld uitingen het formulier nummer. De client toepassing heeft het formulier nummer nodig om te voldoen aan de aanvraag van de gebruiker. het is dus belang rijk dat u deze opneemt in de utterance.

[![Voer een voor beeld-uitingen in voor de FindForm intentie](./media/get-started-portal-build-app/add-example-utterance.png)](./media/get-started-portal-build-app/add-example-utterance.png#lightbox)

Voeg het volgende 15-voor beeld uitingen `FindForm` toe aan de bedoeling.

|#|Voorbeelden van utterances|
|--|--|
|1|Zoeken naar HRF-123456|
|2|Waar bevindt het Human Resources-formulier zich HRF-234591?|
|3|HRF-345623, waar is het|
|4|Is het mogelijk me HRF-345794 te sturen|
|5|Heb ik HRF-234695 nodig om een interne taak uit te voeren?|
|6|Moet mijn manager weten wat ik toepas voor een taak met HRF-234091|
|7|Waar kan ik HRF-234918 verzenden? Ontvang ik een e-mail antwoord dat is ontvangen?|
|8|hrf-234555|
|9|Wanneer is HRF-234987 bijgewerkt?|
|10|Gebruik Form HRF-876345 om te worden toegepast op technische posities|
|11|Was er een nieuwe versie van HRF-765234 ingediend voor mijn open-aanvraag?|
|12|Moet ik HRF-234234 gebruiken voor internationale taken?|
|13|HRF-234598-spel fout|
|14|zullen HRF-234567 worden bewerkt voor nieuwe vereisten|
|15|HRF-123456, HRF-123123, HRF-234567|

Het ontwerp van dit voor beeld kan er als volgt uitzien:

* lengte van utterance
* interpunctie
* woorden keuze
* werkwoordsvormen (is, was, is)
* woord volgorde



## <a name="create-a-regular-expression-entity"></a>Een reguliere expressie-entiteit maken

Het formulier moet worden gemarkeerd als een entiteit om het formulier nummer te retour neren in het runtime-Voorspellings antwoord. Omdat de tekst van het formulier nummer zeer gestructureerd is, kunt u deze markeren met behulp van een reguliere expressie-entiteit. Maak de entiteit met de volgende stappen:

1. Selecteer **entiteiten** in het menu aan de linkerkant.

1. Selecteer **nieuwe entiteit maken** op de pagina **entiteiten** .

1. Voer de naam `Human Resources Form Number`in, selecteer het type **regex** -entiteit en voer `hrf-[0-9]{6}`de reguliere expressie in. Deze vermelding komt overeen met de letterlijke tekens, `hrf-`en staat precies zes cijfers toe.

   ![Entiteits gegevens invoeren voor de reguliere expressie-entiteit](./media/get-started-portal-build-app/create-regular-expression-entity.png)

1. Selecteer **Done**.

## <a name="add-example-utterances-to-the-none-intent"></a>Voorbeelduitingen toevoegen aan de intentie None

**Geen** intentie is de terugval intentie en mag niet leeg blijven. Deze intentie moet één utterance bevatten voor elke 10 voorbeeld uitingen die u hebt toegevoegd voor de andere intentie van de app.

De voor beeld-uitingen van **geen** intentie moet zich buiten uw client toepassings domein bevinden.

1. Selecteer **intenties** in het menu links en selecteer vervolgens **geen** in de lijst intenties.

1. Voeg het volgende voor beeld uitingen toe aan de bedoeling:

   |Voor beeld van geen intentie-uitingen|
   |--|
   |Blaffende honden zijn irritant|
   |Bestel een pizza voor me|
   |Pinguïns in de oceaan|

   Voor deze human resources-app bevinden dit voor beeld uitingen zich buiten het domein. Als uw Human Resources-domein dieren, levens middelen of de oceaan bevat, moet u verschillende voor beeld-uitingen gebruiken voor de **geen** intentie.

## <a name="train-the-app"></a>De app trainen

Selecteer in het menu in de rechter bovenhoek de optie **Train** om het doel van de intentie en het entiteits model toe te passen op de huidige versie van de app.

## <a name="look-at-the-regular-expression-entity-in-the-example-utterances"></a>Bekijk de entiteit reguliere expressie in het voor beeld uitingen

1. Controleer of de entiteit is gevonden in de **FindForm** -intentie door **intenties** te selecteren in het menu links. Selecteer vervolgens **FindForm** intentie.

   De entiteit is gemarkeerd waar deze wordt weer gegeven in het voor beeld uitingen. Als u de oorspronkelijke tekst wilt zien in plaats van de naam van de entiteit, schakelt u in de werk balk **weer gave entiteiten** in.

   [![Alle voor beelden uitingen gemarkeerd met entiteiten](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png)](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png#lightbox)

## <a name="test-your-new-app-with-the-interactive-test-pane"></a>Uw nieuwe app testen met het interactieve test venster

Gebruik het interactieve **test** venster in de Luis-Portal om te controleren of de entiteit is geëxtraheerd uit nieuwe uitingen de app nog niet is gedetecteerd.

1. Selecteer **testen** in het menu in de rechter bovenhoek.

1. Voeg een nieuwe utterance toe en druk vervolgens op ENTER:

   ```Is there a form named hrf-234098```

   ![Nieuwe utterance testen in het test venster](./media/get-started-portal-build-app/test-new-utterance.png)

   De eerste voorspelde intentie is op de juiste manier **FindForm** met meer dan 90% betrouw baarheid (0,977). Het **faxnummer van het formulier Human Resources** wordt geëxtraheerd met de waarde hrf-234098.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met deze Snelstartgids en niet naar de volgende Snelstartgids gaat, selecteert u **mijn apps** in het bovenste navigatie menu. Schakel vervolgens het selectie vakje van de app naar links in de lijst in en selecteer **verwijderen** in de context werkbalk boven de lijst.

[![App verwijderen uit de lijst met apps](./media/get-started-portal-build-app/delete-app.png)](./media/get-started-portal-build-app/delete-app.png#lightbox)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [2. Een app implementeren](get-started-portal-deploy-app.md)
