---
title: 'Snelstart: een nieuwe app maken in de LUIS-portal'
description: In deze quickstart maakt u de basisonderdelen van een app, intenties en entiteiten en test u met voorbeeldutterance in de LUIS-portal.
ms.topic: quickstart
ms.date: 04/14/2020
ms.openlocfilehash: 2d601646c43c0f0d99dc6934cf1f1c960e0b0f79
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382569"
---
# <a name="quickstart-create-a-new-app-in-the-luis-portal"></a>Snelstart: een nieuwe app maken in de LUIS-portal

In deze quickstart bouwt u een nieuwe app in de LUIS-portal. Maak eerst de basisonderdelen van een app, **intenties**en **entiteiten**. Test vervolgens de app door een voorbeeldvan gebruiker uiting in het interactieve testpaneel om de voorspelde intentie te krijgen.

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-an-app"></a>Een app maken

1. Selecteer **+ Nieuwe app voor een gesprek** op de contextwerkbalk en selecteer Vervolgens Nieuwe app voor een **gesprek**.

    > [!div class="mx-imgBorder"]
    > [![Nieuwe app maken in LUIS-portal](./media/create-app-in-portal.png)](./media/create-app-in-portal.png#lightbox)

1. Configureer de app in het pop-upvenster met de volgende instellingen en selecteer **Gereed**.

   |Naam van instelling| Waarde | Doel|
   |--|--|--|
   |Name|`myEnglishApp`|Unieke luis-app-naam<br>vereist|
   |Cultuur|**Engels**|Taal van uitingen van gebruikers, **en-us**<br>vereist|
   |Beschrijving (optioneel)|`App made with LUIS Portal`|Beschrijving van de app<br>optioneel|
   |Voorspellingsbron (optioneel) |-  |Niet selecteren. LUIS geeft u een Starter-sleutel om gratis te gebruiken voor het maken van auteur en 1.000 voorspellingseindpuntaanvragen. |

   ![Nieuwe app-instellingen invoeren](./media/get-started-portal-build-app/create-new-app-settings.png)

## <a name="create-intents"></a>Intents maken

Nadat de LUIS-app is gemaakt, moet u intenties maken. Intents zijn een manier om tekst van gebruikers te categoriseren. Een personeelsapp kan bijvoorbeeld twee functies hebben. Om mensen te helpen:

 1. Vacatures zoeken en solliciteren
 1. Formulieren zoeken om te solliciteren naar vacatures

De twee verschillende _intenties_ van de app sluiten aan bij de volgende intenties:

|Intentie|Voorbeeldtekst van de gebruiker<br>bekend als een _utterance_|
|--|--|
|ApplyForJob|`I want to apply for the new software engineering position in Cairo.`|
|Zoekformulier|`Where is the job transfer form hrf-123456?`|

Voer de volgende stappen uit om intents te maken:

1. Nadat de app is gemaakt, bevindt u zich op de pagina **Intents** van de sectie **Bouwen.** Selecteer **Maken**.

   [![Selecteer Maken om nieuwe intenties te maken](./media/get-started-portal-build-app/create-new-intent-button.png)](./media/get-started-portal-build-app/create-new-intent-button.png#lightbox)

1. Voer de intentienaam `FindForm`in en selecteer **Gereed**.

## <a name="add-an-example-utterance"></a>Een voorbeeldutterance toevoegen

U voegt voorbeelduitingen toe nadat u intents hebt gemaakt. Voorbeelduitingen zijn tekst die een gebruiker invoert in een chatbot of andere clienttoepassing. Ze brengen de intentie van de tekst van de gebruiker in kaart met een LUIS-intentie.

Voor de intentie `FindForm` van deze voorbeeldtoepassing bevatten voorbeelduitingen het formuliernummer. De clienttoepassing heeft het formuliernummer nodig om aan het verzoek van de gebruiker te voldoen, dus het is belangrijk om het in de utterance op te nemen.

> [!div class="mx-imgBorder"]
> [![Voorbeelduitingen invoeren voor de intentie FindForm](./media/get-started-portal-build-app/add-example-utterance.png)](./media/get-started-portal-build-app/add-example-utterance.png#lightbox)

Voeg de volgende 15 voorbeelduitingen toe aan de `FindForm` intentie.

|#|Voorbeelden van utterances|
|--|--|
|1|Op zoek naar hrf-123456|
|2|Waar is de human resources vorm hrf-234591?|
|3|hrf-345623, waar is het|
|4|Is het mogelijk om mij hrf-345794 te sturen|
|5|Heb ik hrf-234695 nodig om te solliciteren naar een interne baan?|
|6|Moet mijn manager weten dat ik solliciteer bij hrf-234091|
|7|Waar stuur ik hrf-234918? Krijg ik een e-mailantwoord dat is ontvangen?|
|8|hrf-234555|
|9|Wanneer is hrf-234987 bijgewerkt?|
|10|Gebruik ik form hrf-876345 om te solliciteren op technische functies|
|11|Was een nieuwe versie van hrf-765234 ingediend voor mijn open req?|
|12|Gebruik ik hrf-234234 voor internationale banen?|
|13|hrf-234598 spelfout|
|14|zal hrf-234567 worden bewerkt voor nieuwe eisen|
|15|hrf-123456, hrf-123123, hrf-234567|

Volgens het ontwerp variëren deze voorbeelduitingen op de volgende manieren:

* utterancelengte
* [Interpunctie](luis-reference-application-settings.md#punctuation-normalization)
* woordkeuze
* werkwoord gespannen (is, was, zal zijn)
* woordvolgorde


## <a name="create-a-regular-expression-entity"></a>Een entiteit met reguliere expressiemaken

Als u het formuliernummer wilt retourneren in het antwoord op de voorspelling van runtime, moet het formulier als een entiteit worden gemarkeerd. Omdat de tekst van het formuliernummer sterk gestructureerd is, u deze markeren met behulp van een entiteit met reguliere expressie. Maak de entiteit met de volgende stappen:

1. Selecteer **Entiteiten in** het menu aan de linkerkant.

1. Selecteer **Maken** op de pagina **Entiteiten.**

1. Voer de `Human Resources Form Number`naam in, selecteer het **entiteitstype Regex** en selecteer **Volgende**.

   ![Entiteit met reguliere expressie maken](./media/get-started-portal-build-app/create-regular-expression-entity.png)

1. Voer de expressie reguliere expressie `hrf-[0-9]{6}`**RegEx**in . Dit item komt overeen met de letterlijke tekens `hrf-`en biedt precies 6 cijfers de optie **Maken**.

   ![Reguliere expressie voor entiteit invoeren](./media/get-started-portal-build-app/create-regular-expression-entity-with-expression.png)


## <a name="add-example-utterances-to-the-none-intent"></a>Voorbeelduitingen toevoegen aan de intentie None

De **intentie Geen** is de terugvalintentie en mag niet leeg blijven. Deze intentie moet één utterance bevatten voor elke 10 voorbeelduitingen die u hebt toegevoegd voor de andere intents van de app.

De voorbeelduitingen van de intentie **geen** moeten zich buiten het domein van uw clienttoepassings bevinden.

1. Selecteer **Intents** in het linkermenu en selecteer **Geen** in de lijst met intenties.

1. Voeg de volgende voorbeelduitingen toe aan de intentie:

   |None|
   |--|
   |Blaffende honden zijn irritant|
   |Bestel een pizza voor me|
   |Pinguïns in de oceaan|

   Voor deze app bevinden deze voorbeelduitingen zich buiten het domein. Als uw domein dieren, voedsel of de oceaan bevat, moet u verschillende voorbeelduitingen gebruiken voor de **intentie Geen.**

## <a name="train-the-app"></a>De app trainen

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="look-at-the-regular-expression-entity-in-the-example-utterances"></a>Bekijk de entiteit met reguliere expressie in de voorbeelduitingen

1. Controleer of de entiteit wordt gevonden in de intentie **FindForm** door **Intents** te selecteren in het linkermenu. Selecteer vervolgens De intentie **Zoekvorm.**

   De entiteit wordt gemarkeerd wanneer deze wordt weergegeven in de voorbeelduitingen. Als u de oorspronkelijke tekst wilt zien in plaats van de entiteitsnaam, schakelt u **de weergave Entiteiten** in vanaf de werkbalk.

   > [!div class="mx-imgBorder"]
   > [![Alle voorbeelduitingen die zijn gemarkeerd met entiteiten](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png)](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png#lightbox)

## <a name="test-your-new-app-with-the-interactive-test-pane"></a>Uw nieuwe app testen met het interactieve testvenster

Gebruik het interactieve **testvenster** in de LUIS-portal om te valideren dat de entiteit is geëxtraheerd uit nieuwe uitingen die de app nog niet heeft gezien.

1. Selecteer **Testen** in het menu rechtsboven.

1. Voeg een nieuwe utterance toe en druk op Enter:

   ```Is there a form named hrf-234098```

    Selecteer **Inspecteren** om entiteitsvoorspellingen te bekijken.

   > [!div class="mx-imgBorder"]
   > ![Nieuwe utterance testen in testvenster](./media/get-started-portal-build-app/test-new-utterance.png)

   De top voorspelde intentie is correct **FindForm** met meer dan 90% vertrouwen (0.977). De entiteit **Human Resources Form Number** wordt geëxtraheerd met een waarde van hrf-234098.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met deze quickstart en niet naar de volgende quickstart gaat, selecteert u **Mijn apps** in het bovenste navigatiemenu. Schakel vervolgens het linkerselectievakje van de app in de lijst in en selecteer **Verwijderen op** de contextwerkbalk boven de lijst.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [2. Een app implementeren](get-started-portal-deploy-app.md)
