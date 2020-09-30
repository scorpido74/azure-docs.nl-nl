---
title: Stappen voor het importeren van apps
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 06/22/2020
ms.openlocfilehash: aa0ba1de63cbcf1d9485c71b6bec7a29afb5140f
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91545739"
---
1. Selecteer **+ Nieuwe app voor conversatie** en vervolgens **Importeren als JSON** in de [LUIS-portal](https://www.luis.ai) op de pagina **Mijn app**. Zoek het opgeslagen JSON-bestand uit de vorige stap. U hoeft de naam van de app niet te wijzigen. Selecteer **Gereed**

1. Selecteer in de sectie **Beheren** op het tabblad **Versies** versie `0.1` en selecteer **Klonen** om de versie te klonen. Geef deze de nieuwe naam `ml-entity` en selecteer **Gereed** om het klonen te voltooien. Omdat de versienaam wordt gebruikt als onderdeel van de URL-route, kan de naam geen tekens bevatten die niet zijn toegestaan in een URL.

    > [!TIP]
    > Het klonen naar een nieuwe versie is een best practice voordat u uw app wijzigt. Wanneer u klaar bent met een wijziging in een versie, exporteert u de versie (als een .json- of . lu-bestand) en checkt u het bestand in in uw broncodebeheersysteem.

1. Selecteer **Build** en vervolgens **Intenties** om de intenties te bekijken, de belangrijkste bouwstenen van een LUIS-app.

    ![Ga van de pagina Versies naar de pagina Intenties.](../media/tutorial-machine-learned-entity/new-version-imported-app.png)
