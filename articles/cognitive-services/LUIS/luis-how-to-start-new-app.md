---
title: Een nieuwe app maken-LUIS
titleSuffix: Azure Cognitive Services
description: Maak en beheer uw toepassingen op de webpagina Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 227efcdbcb7d8e776dd77b38c5d1dedd54d71b6b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73500319"
---
# <a name="create-a-new-luis-app-in-the-luis-portal"></a>Een nieuwe LUIS-app maken in de LUIS-Portal
Er zijn een aantal manieren om een LUIS-app te maken. U kunt een LUIS-app maken in de [Luis](https://www.luis.ai) -portal of via de Luis-ontwerp- [api's](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f).

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="using-the-luis-portal"></a>De LUIS-Portal gebruiken

U kunt op verschillende manieren een nieuwe app maken in de LUIS-portal:

* Begin met een lege app en maak intents, uitingen en entiteiten.
* Begin met een lege app en voeg een [vooraf gebouwd domein](luis-how-to-use-prebuilt-domains.md)toe.
* Importeer een LUIS-app uit een JSON-bestand dat al de intenties, uitingen en entiteiten bevat.

## <a name="using-the-authoring-apis"></a>De ontwerp-Api's gebruiken
U kunt op een aantal manieren een nieuwe app maken met behulp van de ontwerp-Api's:

* [Begin](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) met een lege app en maak intents, uitingen en entiteiten.
* [Begin](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/59104e515aca2f0b48c76be5) met een vooraf gebouwd domein.  


<a name="export-app"></a>
<a name="import-new-app"></a>
<a name="delete-app"></a>
 

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-new-app-in-luis"></a>Nieuwe app maken in LUIS

1. Selecteer op **de pagina mijn apps** **+ maken**.

    ![Lijst met apps van LUIS](./media/luis-create-new-app/apps-list.png)


2. Geef in het dialoog venster de naam van de toepassing "TravelAgent".

    ![Dialoog venster nieuwe app maken](./media/luis-create-new-app/create-app.png)

3. Kies uw toepassings cultuur (voor de TravelAgent-app, Kies Engels) en selecteer vervolgens **gereed**. 

    > [!NOTE]
    > De cultuur kan niet worden gewijzigd nadat de toepassing is gemaakt. 

## <a name="import-an-app-from-file"></a>Een app importeren uit een bestand

1. Selecteer **nieuwe app importeren**op **de pagina mijn apps** .
1. Selecteer in het pop-updialoogvenster een geldig JSON-bestand voor de app en selecteer vervolgens **gereed**.

### <a name="import-errors"></a>Fouten importeren

Mogelijke fouten zijn: 

* Er bestaat al een app met die naam. U kunt dit probleem verhelpen door de app opnieuw te importeren en de **optionele naam** in te stellen op een nieuwe naam. 

## <a name="export-app-for-backup"></a>App voor back-up exporteren

1. Selecteer op **de pagina mijn apps** **exporteren**.
1. Selecteer **exporteren als JSON**. Uw browser downloadt de actieve versie van de app.
1. Voeg dit bestand toe aan het back-upsysteem om het model te archiveren.

## <a name="export-app-for-containers"></a>App voor containers exporteren

1. Selecteer op **de pagina mijn apps** **exporteren**.
1. Selecteer **exporteren als container** en selecteer vervolgens welke gepubliceerde sleuf (productie of fase) u wilt exporteren.
1. Gebruik dit bestand met uw [Luis-container](luis-container-howto.md). 

    Als u geïnteresseerd bent in het exporteren van een getraind maar nog niet gepubliceerd model dat u wilt gebruiken met de LUIS-container, gaat u naar de pagina **versies** en exporteert u daar naartoe. 

## <a name="delete-app"></a>App verwijderen

1. Selecteer op de pagina **mijn apps** de drie puntjes (...) aan het einde van de rij van de app.
1. Selecteer **verwijderen** in het menu.
1. Selecteer **OK** in het bevestigings venster.

## <a name="next-steps"></a>Volgende stappen

Uw eerste taak in de app is om [intenties toe te voegen](luis-how-to-add-intents.md).
