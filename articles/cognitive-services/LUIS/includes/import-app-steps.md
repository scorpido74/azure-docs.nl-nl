---
title: App-stappen importeren
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 12/03/2019
ms.author: diberry
ms.openlocfilehash: c0253360c66ef6fd995f65e8a83ba5adcdf19543
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806407"
---
1. Selecteer in de [Preview Luis-Portal](https://preview.luis.ai)op de pagina **mijn apps** **importeren**en vervolgens **importeren als JSON**. Zoek het opgeslagen JSON-bestand uit de vorige stap. U hoeft de naam van de app niet te wijzigen. Selecteer **gereed**

1. Selecteer in de sectie **beheren** op het tabblad **versies** de versie, selecteer **klon** om de versie te klonen en geef deze een nieuwe naam van tien tekens en selecteer **gereed** om het kloon proces te volt ooien. Omdat de versienaam wordt gebruikt als onderdeel van de URL-route, kan de naam geen tekens bevatten die niet zijn toegestaan in een URL.

    > [!TIP]
    > Het klonen van een versie naar een nieuwe versie is een best practice voordat u uw app wijzigt. Wanneer u klaar bent met het volt ooien van een versie, exporteert u de versie (als een. json-of. lu-bestand) en controleert u het bestand in uw bron beheersysteem.

1. Selecteer **samen stellen** en **intenties** om de intenties, de belangrijkste bouw stenen van een Luis-app te bekijken.

    ![Ga van de pagina versies naar de pagina intenties.](../media/tutorial-machine-learned-entity/new-version-imported-app.png)