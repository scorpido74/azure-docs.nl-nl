---
title: App-stappen importeren
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/30/2020
ms.author: diberry
ms.openlocfilehash: b7b8befa0f5871b65b9b5621bfb99c659bf07235
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422759"
---
1. Selecteer in de [preview LUIS-portal](https://preview.luis.ai)op de pagina **Mijn apps** de optie + Nieuwe app voor **een gesprek**en vervolgens Importeren **als JSON**. Zoek het opgeslagen JSON-bestand uit de vorige stap. U hoeft de naam van de app niet te wijzigen. Gereed **doen selecteren**

1. Selecteer in de sectie **Beheren** op `0.1` het tabblad **Versies** de versie, selecteer **Vervolgens Kloon** om de `ml-entity`versie te klonen en geef deze een nieuwe naam van 10 tekens van , selecteer **Gereed** om het kloonproces te voltooien. Omdat de versienaam wordt gebruikt als onderdeel van de URL-route, kan de naam geen tekens bevatten die niet zijn toegestaan in een URL.

    > [!TIP]
    > Klonen in een nieuwe versie is een aanbevolen manier voordat u uw app wijzigt. Wanneer u klaar bent met een wijziging in een versie, exporteert u de versie (als een .json- of .lu-bestand) en controleert u het bestand in uw bronbeheersysteem.

1. Selecteer **Vervolgens** **Intenties bouwen** om de intenties te zien, de belangrijkste bouwstenen van een LUIS-app.

    ![Wijzigen van de pagina Versies naar de pagina Intents.](../media/tutorial-machine-learned-entity/new-version-imported-app.png)