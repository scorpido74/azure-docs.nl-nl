---
title: Versies beheren - LUIS
titleSuffix: Azure Cognitive Services
description: Met versies u verschillende modellen bouwen en publiceren. Een goede gewoonte is om het huidige actieve model te klonen naar een andere versie van de app voordat u wijzigingen aanbrengt in het model.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: 138b84a9b7f54782fd6254304a3fdcf4dba83182
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220895"
---
# <a name="use-versions-to-edit-and-test-without-impacting-staging-or-production-apps"></a>Versies gebruiken om te bewerken en te testen zonder dat dit gevolgen heeft voor faserings- of productie-apps

Met versies u verschillende modellen bouwen en publiceren. Een goede gewoonte is om het huidige actieve model te klonen naar een andere [versie](luis-concept-version.md) van de app voordat u wijzigingen aanbrengt in het model. 

Als u met versies wilt werken, opent u uw app door de naam op de pagina **Mijn apps te** selecteren en selecteert u **Beheren** in de bovenste balk en selecteert u **Versies** in de linkernavigatie. 

De lijst met versies geeft aan welke versies worden gepubliceerd, waar ze worden gepubliceerd en welke versie momenteel actief is. 

> [!div class="mx-imgBorder"]
> [![Sectie beheren, pagina versies](./media/luis-how-to-manage-versions/versions-import.png "Sectie beheren, pagina versies")](./media/luis-how-to-manage-versions/versions-import.png#lightbox)

## <a name="clone-a-version"></a>Een versie klonen

1. Selecteer de versie die u wilt klonen en selecteer **Klonen** op de werkbalk. 

2. Typ in het dialoogvenster **Kloonversie** een naam voor de nieuwe versie, zoals '0.2'.

   ![Dialoogvenster Kloonversie](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)
 
     > [!NOTE]
     > Versie-ID kan alleen bestaan uit tekens, cijfers of '.' en mag niet langer zijn dan 10 tekens.
 
   Er wordt een nieuwe versie met de opgegeven naam gemaakt en ingesteld als de actieve versie.

## <a name="set-active-version"></a>Actieve versie instellen

Selecteer een versie in de lijst en selecteer **Activeren** op de werkbalk. 

> [!div class="mx-imgBorder"]
> [![Sectie beheren, pagina versies, een versieactie uitvoeren](./media/luis-how-to-manage-versions/versions-other.png "Sectie beheren, pagina versies, een versieactie uitvoeren")](./media/luis-how-to-manage-versions/versions-other.png#lightbox)

## <a name="import-version"></a>Versie importeren

U `.json` een `.lu` of een versie van uw toepassing importeren.

1. Selecteer **Importeren vanaf** de werkbalk en selecteer de indeling. 

2. Voer in het pop-upvenster **Nieuwe versie importeren** de nieuwe naam van de versie met tien tekens in. U hoeft alleen een versie-id in te stellen als de versie in het bestand al in de app bestaat.

    ![Sectie beheren, pagina versies, nieuwe versie importeren](./media/luis-how-to-manage-versions/versions-import-pop-up.png)

    Zodra u een versie importeert, wordt de nieuwe versie de actieve versie.

### <a name="import-errors"></a>Importfouten

* Tokenizerfouten: Als u een **tokenizerfout** krijgt bij het importeren, probeert u een versie te importeren die een andere [tokenizer](luis-language-support.md#custom-tokenizer-versions) gebruikt dan de app momenteel gebruikt. Zie [Migreren tussen tokenizerversies](luis-language-support.md#migrating-between-tokenizer-versions)om dit op te lossen.

<a name = "export-version"></a>

## <a name="other-actions"></a>Overige acties

* Als u een versie **wilt verwijderen,** selecteert u een versie in de lijst en selecteert u **Verwijderen op** de werkbalk. Selecteer **Ok**. 
* Als u de naam van een versie **wilt wijzigen,** selecteert u een versie in de lijst en selecteert u **Naam wijzigen** op de werkbalk. Voer een nieuwe naam in en selecteer **Gereed**. 
* Als u een versie **wilt exporteren,** selecteert u een versie in de lijst en selecteert u **App exporteren** op de werkbalk. Kies JSON om te exporteren voor back-up, kies **Exporteren voor container** om deze app te gebruiken in een [LUIS-container.](luis-container-howto.md)  

