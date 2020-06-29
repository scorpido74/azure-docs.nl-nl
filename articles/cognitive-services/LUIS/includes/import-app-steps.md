---
title: App-stappen importeren
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/22/2020
ms.author: diberry
ms.openlocfilehash: 2cf588ed120b353958cc708189c86481cd247d8e
ms.sourcegitcommit: 74ba70139781ed854d3ad898a9c65ef70c0ba99b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85445917"
---
1. Ga in [de Luis-Portal](https://www.luis.ai)naar de pagina **mijn apps** en selecteer **+ nieuwe app voor gesprek**en **Importeer vervolgens als JSON**-bestand. Zoek het opgeslagen JSON-bestand uit de vorige stap. U hoeft de naam van de app niet te wijzigen. Selecteer **gereed**

1. Selecteer in de sectie **beheren** op het tabblad **versies** de `sentiment` versie, selecteer **klon** om de versie te klonen en geef deze een nieuwe naam, en `ml-entity` Selecteer vervolgens **gereed** om het kloon proces te volt ooien. Omdat de versienaam wordt gebruikt als onderdeel van de URL-route, kan de naam geen tekens bevatten die niet zijn toegestaan in een URL.

    > [!TIP]
    > Het klonen van een nieuwe versie is een best practice voordat u uw app wijzigt. Wanneer u klaar bent met een wijziging in een versie, exporteert u de versie (als een. json-of. lu-bestand) en controleert u het bestand in uw bron beheersysteem.

1. Selecteer **samen stellen** en **intenties** om de intenties, de belangrijkste bouw stenen van een Luis-app te bekijken.

    ![Ga van de pagina versies naar de pagina intenties.](../media/tutorial-machine-learned-entity/new-version-imported-app.png)