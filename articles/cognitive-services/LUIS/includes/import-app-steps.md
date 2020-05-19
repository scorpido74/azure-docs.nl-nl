---
title: App-stappen importeren
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/07/2020
ms.author: diberry
ms.openlocfilehash: 4c87ea9ebb2dd7aec36025e4b45f74e186ca1cd4
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588773"
---
1. Ga in [de Luis-Portal](https://www.luis.ai)naar de pagina **mijn apps** en selecteer **+ nieuwe app voor gesprek**en **Importeer vervolgens als JSON**-bestand. Zoek het opgeslagen JSON-bestand uit de vorige stap. U hoeft de naam van de app niet te wijzigen. Selecteer **gereed**

1. Selecteer in de sectie **beheren** op het tabblad **versies** de `0.1` versie, selecteer **klon** om de versie te klonen en geef deze een nieuwe naam, en `ml-entity` Selecteer vervolgens **gereed** om het kloon proces te volt ooien. Omdat de versienaam wordt gebruikt als onderdeel van de URL-route, kan de naam geen tekens bevatten die niet zijn toegestaan in een URL.

    > [!TIP]
    > Het klonen van een nieuwe versie is een best practice voordat u uw app wijzigt. Wanneer u klaar bent met een wijziging in een versie, exporteert u de versie (als een. json-of. lu-bestand) en controleert u het bestand in uw bron beheersysteem.

1. Selecteer **samen stellen** en **intenties** om de intenties, de belangrijkste bouw stenen van een Luis-app te bekijken.

    ![Ga van de pagina versies naar de pagina intenties.](../media/tutorial-machine-learned-entity/new-version-imported-app.png)