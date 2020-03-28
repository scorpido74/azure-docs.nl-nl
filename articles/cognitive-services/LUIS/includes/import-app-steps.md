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
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74840813"
---
1. Selecteer in de [preview LUIS-portal](https://preview.luis.ai)op de pagina **Mijn apps** de optie **Importeren**en vervolgens importeren **als JSON**. Zoek het opgeslagen JSON-bestand uit de vorige stap. U hoeft de naam van de app niet te wijzigen. Gereed **doen selecteren**

1. Selecteer in de sectie Beheren op het tabblad **Versies** de versie, selecteer **Vervolgens Kloon** om de versie te klonen en geef deze een nieuwe naam **met** 10 tekens en selecteer **Vervolgens Gereed** om het kloonproces te voltooien. Omdat de versienaam wordt gebruikt als onderdeel van de URL-route, kan de naam geen tekens bevatten die niet zijn toegestaan in een URL.

    > [!TIP]
    > Klonen in een nieuwe versie is een aanbevolen manier voordat u uw app wijzigt. Wanneer u een versie voltooit, exporteert u de versie (als een .json- of .lu-bestand) en controleert u het bestand in uw bronbesturingssysteem.

1. Selecteer **Vervolgens** **Intenties bouwen** om de intenties te zien, de belangrijkste bouwstenen van een LUIS-app.

    ![Wijzigen van de pagina Versies naar de pagina Intents.](../media/tutorial-machine-learned-entity/new-version-imported-app.png)