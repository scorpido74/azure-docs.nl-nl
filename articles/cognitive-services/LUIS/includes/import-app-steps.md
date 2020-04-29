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
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80422759"
---
1. Selecteer in [de Luis-Portal](https://preview.luis.ai)van de preview-versie op de pagina **mijn apps** **+ nieuwe app voor CONVERSATIE**en **Importeer vervolgens als JSON**-bestand. Zoek het opgeslagen JSON-bestand uit de vorige stap. U hoeft de naam van de app niet te wijzigen. Selecteer **gereed**

1. Selecteer in de sectie **beheren** op het tabblad **versies** de `0.1` versie, selecteer **klon** om de versie te klonen en geef deze een nieuwe naam van 10 tekens van `ml-entity`en selecteer **gereed** om het kloon proces te volt ooien. Omdat de versienaam wordt gebruikt als onderdeel van de URL-route, kan de naam geen tekens bevatten die niet zijn toegestaan in een URL.

    > [!TIP]
    > Het klonen van een nieuwe versie is een best practice voordat u uw app wijzigt. Wanneer u klaar bent met een wijziging in een versie, exporteert u de versie (als een. json-of. lu-bestand) en controleert u het bestand in uw bron beheersysteem.

1. Selecteer **samen stellen** en **intenties** om de intenties, de belangrijkste bouw stenen van een Luis-app te bekijken.

    ![Ga van de pagina versies naar de pagina intenties.](../media/tutorial-machine-learned-entity/new-version-imported-app.png)