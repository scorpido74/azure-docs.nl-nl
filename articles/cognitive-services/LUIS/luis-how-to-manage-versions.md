---
title: Versies beheren-LUIS
titleSuffix: Azure Cognitive Services
description: Versies kunnen u bouwt en publiceert verschillende modellen. Er is een goede gewoonte om te klonen van de huidige actieve model naar een andere versie van de app voordat u wijzigingen in het model.
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
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74221894"
---
# <a name="use-versions-to-edit-and-test-without-impacting-staging-or-production-apps"></a>Versies gebruiken om te bewerken en te testen zonder dat dit van invloed is op staging-of productie-apps

Versies kunnen u bouwt en publiceert verschillende modellen. Het is een goed idee om het huidige actieve model te klonen naar een andere [versie](luis-concept-version.md) van de app voordat u wijzigingen aanbrengt in het model. 

Als u met versies wilt werken, opent u uw app door de naam ervan te selecteren op **mijn apps** pagina en vervolgens **beheren** te selecteren in de bovenste balk en vervolgens **versies** in de linkernavigatiebalk te selecteren. 

In de lijst met versies ziet u welke versies worden gepubliceerd, waar ze worden gepubliceerd en welke versie momenteel actief is. 

> [!div class="mx-imgBorder"]
> [![Sectie beheren, pagina versies](./media/luis-how-to-manage-versions/versions-import.png "Sectie, pagina-versies beheren")](./media/luis-how-to-manage-versions/versions-import.png#lightbox)

## <a name="clone-a-version"></a>Een versie klonen

1. Selecteer de versie die u wilt klonen en selecteer vervolgens **klonen** in de werk balk. 

2. Typ in het dialoog venster **versie klonen** een naam voor de nieuwe versie, bijvoorbeeld ' 0,2 '.

   ![Dialoogvenster van de kloon-versie](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)
 
     > [!NOTE]
     > Versie-ID kan bestaan alleen uit tekens, cijfers of '.' en mag niet langer zijn dan 10 tekens.
 
   Een nieuwe versie met de opgegeven naam gemaakt en ingesteld als de actieve versie.

## <a name="set-active-version"></a>Actieve versie instellen

Selecteer een versie in de lijst en selecteer vervolgens **activeren** in de werk balk. 

> [!div class="mx-imgBorder"]
> [![Sectie beheren, pagina versies, een versie actie maken](./media/luis-how-to-manage-versions/versions-other.png "Sectie beheren, pagina versies, een versie actie maken")](./media/luis-how-to-manage-versions/versions-other.png#lightbox)

## <a name="import-version"></a>Importversie

U kunt een `.json` of een `.lu` versie van uw toepassing importeren.

1. Selecteer **importeren** in de werk balk en selecteer vervolgens de indeling. 

2. Voer in het pop-upvenster **nieuwe versie importeren** de nieuwe versie naam van tien tekens in. U hoeft alleen een versie-ID in te stellen als de versie in het bestand al aanwezig is in de app.

    ![Sectie beheren, versies pagina, nieuwe versie importeren](./media/luis-how-to-manage-versions/versions-import-pop-up.png)

    Nadat u een versie importeert, wordt de nieuwe versie de actieve versie.

### <a name="import-errors"></a>Fouten importeren

* Tokenizer-fouten: als er een **tokenizer-fout optreedt** tijdens het importeren, probeert u een versie te importeren die gebruikmaakt van een andere [tokenizer](luis-language-support.md#custom-tokenizer-versions) dan die momenteel wordt gebruikt door de app. Zie voor het oplossen van dit probleem [migreren tussen tokenizer-versies](luis-language-support.md#migrating-between-tokenizer-versions).

<a name = "export-version"></a>

## <a name="other-actions"></a>Andere acties

* Als u een versie wilt **verwijderen** , selecteert u een versie in de lijst en selecteert u vervolgens **verwijderen** op de werk balk. Selecteer **OK**. 
* Als u de naam van een versie wilt **wijzigen** , selecteert u een versie in de lijst en selecteert u vervolgens **naam wijzigen** in de werk balk. Voer een nieuwe naam in en selecteer **gereed**. 
* Als u een versie wilt **exporteren** , selecteert u een versie in de lijst en selecteert u vervolgens **app exporteren** in de werk balk. Kies JSON om te exporteren voor back-up. Kies **exporteren voor container** om [deze app te gebruiken in een Luis-container](luis-container-howto.md).  

