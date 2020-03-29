---
title: Inhoudswerkstromen definiëren en gebruiken via het hulpprogramma Controleren - Inhoudsmoderator
titleSuffix: Azure Cognitive Services
description: U de azure content moderator-werkstroomontwerper gebruiken om aangepaste werkstromen en drempelwaarden te definiëren op basis van uw inhoudsbeleid.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: pafarley
ms.openlocfilehash: 0be77dc1ac249c37f9b59078451d8fbe35f17458
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "72754244"
---
# <a name="define-and-use-moderation-workflows"></a>Moderatiewerkstromen definiëren en gebruiken

In deze handleiding leert u hoe u [werkstromen](../review-api.md#workflows) instelt en gebruikt op de website van het [hulpprogramma Controleren.](https://contentmoderator.cognitive.microsoft.com) Werkstromen zijn op de cloud gebaseerde aangepaste filters die u gebruiken om inhoud efficiënter te verwerken. Werkstromen kunnen verbinding maken met verschillende services om inhoud op verschillende manieren te filteren en vervolgens de juiste actie te ondernemen. In deze handleiding ziet u hoe u de inhoudsmoderatorconnector (die standaard is opgenomen) gebruiken om inhoud te filteren en menselijke beoordelingen in te stellen in een typisch moderatiescenario.

## <a name="create-a-new-workflow"></a>Een nieuwe werkstroom maken

Ga naar het [hulpprogramma Inhoudsmoderator en](https://contentmoderator.cognitive.microsoft.com/) meld u aan. Selecteer **werkstromen** op **Workflows**het tabblad Instellingen .

![Instelling voor werkstromen](images/2-workflows-0.png)

Selecteer **Werkstroom toevoegen**in het volgende scherm .

![Een werkstroom toevoegen](images/2-workflows-1.png)

### <a name="assign-a-name-and-description"></a>Een naam en beschrijving toewijzen

Geef uw werkstroom een naam, voer een beschrijving in en kies of de werkstroom afbeeldingen of tekst verwerkt.

![Werkstroomnaam en -beschrijving](images/image-workflow-create.PNG)

### <a name="define-evaluation-criteria"></a>Evaluatiecriteria definiëren

Ga op het volgende scherm naar de sectie **Als.** Kies **Voorwaarde**in het bovenste vervolgkeuzemenu . Hiermee u de voorwaarde configureren waarop de werkstroom actie onderneemt. Als u meerdere voorwaarden wilt gebruiken, kiest u In plaats daarvan **Combinatie.** 

Selecteer vervolgens een connector. In dit voorbeeld wordt **inhoudsmoderator gebruikt**. Afhankelijk van de connector die u kiest, krijgt u verschillende opties voor gegevensuitvoer. Zie het gedeelte [Connectors](./configure.md#connectors) van de instellingengids voor het hulpprogramma Controleren voor meer informatie over het instellen van andere connectors.

![Werkstroomconnector selecteren](images/image-workflow-connect-to.PNG)

Kies de gewenste uitvoer om te gebruiken en stel de voorwaarden in om deze te controleren.

![Werkstroomvoorwaarde definiëren](images/image-workflow-condition.PNG)

### <a name="define-the-action"></a>De actie definiëren

Ga naar de sectie **Toen,** waar u een actie selecteert. In het volgende voorbeeld wordt een afbeeldingsbeoordeling gemaakt en wordt een tag toewijst. Optioneel u een alternatief (Else) pad toevoegen en daar ook een actie voor instellen.

![Werkstroomactie definiëren](images/image-workflow-action.PNG)

### <a name="save-the-workflow"></a>De werkstroom opslaan

Let op de werkstroomnaam; u hebt de naam nodig om een moderatietaak te starten met de Workflow-API (zie hieronder). Sla ten slotte de werkstroom op met de knop **Opslaan** boven aan de pagina.

## <a name="test-the-workflow"></a>De werkstroom testen

Nu u een aangepaste werkstroom hebt gedefinieerd, test u deze met voorbeeldinhoud. Ga naar **Werkstromen** en selecteer de bijbehorende **knop Werkstroom uitvoeren.**

![Werkstroomtest](images/image-workflow-execute.PNG)

Sla deze [voorbeeldafbeelding op](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg) in uw lokale schijf. Selecteer vervolgens **Bestand(en) kiezen** en upload de afbeelding naar de werkstroom.

![Een agent met een citaat bovenop het beeld](images/sample-text.jpg)

### <a name="track-progress"></a>Voortgang bijhouden

U de voortgang van de werkstroom bekijken in het volgende pop-upvenster.

![Werkstroomuitvoering bijhouden](images/image-workflow-job.PNG)

### <a name="verify-workflow-action"></a>Werkstroomactie verifiëren

Ga naar het tabblad **Afbeelding** onder **Controleren** en controleer of er een nieuw gemaakte afbeeldingscontrole is uitgevoerd.

![Afbeeldingen beoordelen](images/image-workflow-review.PNG)

## <a name="next-steps"></a>Volgende stappen

In deze handleiding heb je geleerd hoe je moderatieworkflows instellen en gebruiken via het [hulpprogramma](https://contentmoderator.cognitive.microsoft.com)Inhoudsmoderator. Zie vervolgens de [API-handleiding voor rust](../try-review-api-workflow.md) om te leren hoe u workflows programmatisch maken.
