---
title: Een nieuwe app maken-LUIS
titleSuffix: Azure Cognitive Services
description: Maken en beheren van uw toepassingen op de webpagina van de Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: diberry
ms.openlocfilehash: 5939886f68a877c4cfad752fe93c4f0eca8f80cf
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70258511"
---
# <a name="create-a-new-luis-app-in-the-luis-portal"></a>Een nieuwe LUIS-app maken in de LUIS-Portal
Er zijn een aantal manieren om een LUIS-app te maken. U kunt een LUIS-app in de [LUIS](https://www.luis.ai) portal of via het ontwerpen van LUIS [API's](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f).

## <a name="using-the-luis-portal"></a>Met behulp van de portal LUIS

U kunt een nieuwe app maken in de portal LUIS op verschillende manieren:

* Beginnen met een lege app en intenties, uitingen en entiteiten te maken.
* Beginnen met een lege app en voeg een [vooraf gemaakte domein](luis-how-to-use-prebuilt-domains.md).
* Importeer een LUIS-app vanuit een JSON-bestand dat al intents, uitingen en entiteiten bevat.

## <a name="using-the-authoring-apis"></a>Met behulp van de API's voor ontwerpen
U kunt een nieuwe app maken met de authoring API's in een aantal manieren:

* [Start](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) met een lege app en intenties, uitingen en entiteiten te maken.
* [Start](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/59104e515aca2f0b48c76be5) met een vooraf gedefinieerde domein.  


<a name="export-app"></a>
<a name="import-new-app"></a>
<a name="delete-app"></a>
 

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-new-app-in-luis"></a>Nieuwe app maken in LUIS

1. Op **mijn Apps** weergeeft, schakelt **nieuwe app maken**.

    ![Lijst met apps van LUIS](./media/luis-create-new-app/apps-list.png)


2. Geef uw toepassing 'TravelAgent' in het dialoogvenster.

    ![Dialoogvenster voor een nieuwe app maken](./media/luis-create-new-app/create-app.png)

3. Kies de cultuur van uw toepassing (voor TravelAgent app, kiest u Engels), en selecteer vervolgens **gedaan**. 

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

Uw eerste taak in de app bestaat uit het [intents toevoegen](luis-how-to-add-intents.md).
