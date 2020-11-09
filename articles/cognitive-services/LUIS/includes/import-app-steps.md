---
title: Stappen voor het importeren van apps
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 10/16/2020
ms.openlocfilehash: b70a22e502de5441b5c91a8f33357375834f54e1
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130734"
---
1. Selecteer **+ Nieuwe app voor conversatie** en vervolgens **Importeren als JSON** in de [LUIS-portal](https://www.luis.ai) op de pagina **Mijn app**. Zoek het opgeslagen JSON-bestand uit de vorige stap. U hoeft de naam van de app niet te wijzigen. Selecteer **Gereed**

1. Selecteer in de sectie **Beheren** op het tabblad **Versies** versie `0.1` en selecteer **Klonen** om de versie te klonen. Geef deze de nieuwe naam `ml-entity` en selecteer **Gereed** om het klonen te voltooien. Omdat de versienaam wordt gebruikt als onderdeel van de URL-route, kan de naam geen tekens bevatten die niet zijn toegestaan in een URL.

    > [!TIP]
    > Het klonen naar een nieuwe versie is een best practice voordat u uw app wijzigt. Wanneer u klaar bent met een wijziging in een versie, exporteert u de versie (als een .json- of . lu-bestand) en checkt u het bestand in in uw broncodebeheersysteem.

1. Selecteer **Bouwen** boven aan het scherm en klik vervolgens op **Intenties** in het navigatiemenu aan de linkerkant. U ziet de intenties van de app, die de belangrijkste bouwstenen van een LUIS-app vormen.
