---
title: App-stappen importeren
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 12/03/2019
ms.author: diberry
ms.openlocfilehash: b0d1735df7a3ac36e20af771939aec6b8013f2df
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74840813"
---
1. Selecteer in de [Preview Luis-Portal](https://preview.luis.ai)op de pagina **mijn apps** **importeren**en vervolgens **importeren als JSON**. Zoek het opgeslagen JSON-bestand uit de vorige stap. U hoeft de naam van de app niet te wijzigen. Selecteer **gereed**

1. Selecteer in de sectie **beheren** op het tabblad **versies** de versie, selecteer **klon** om de versie te klonen en geef deze een nieuwe naam van tien tekens en selecteer **gereed** om het kloon proces te volt ooien. Omdat de versienaam wordt gebruikt als onderdeel van de URL-route, kan de naam geen tekens bevatten die niet zijn toegestaan in een URL.

    > [!TIP]
    > Het klonen van een nieuwe versie is een best practice voordat u uw app wijzigt. Wanneer u klaar bent met het volt ooien van een versie, exporteert u de versie (als een. json-of. lu-bestand) en controleert u het bestand in uw bron beheersysteem.

1. Selecteer **samen stellen** en **intenties** om de intenties, de belangrijkste bouw stenen van een Luis-app te bekijken.

    ![Ga van de pagina versies naar de pagina intenties.](../media/tutorial-machine-learned-entity/new-version-imported-app.png)